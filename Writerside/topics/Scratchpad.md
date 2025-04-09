# Scratchpad

```yaml
version: 2.1

orbs:
  ggshield: gitguardian/ggshield@volatile
  sonarscanner: psl/sonarscanner@x.y.z  # Replace with actual version

executors:
  ci-executor:
    docker:
      - image: your-dockerhub-org/ci-pipeline:latest
    working_directory: ~/repo

jobs:
  validate_dev_branch:
    executor: ci-executor
    steps:
      - checkout
      - run:
          name: Code Formatting Check
          command: black --check my_package
      - ggshield/scan
      - sonarscanner/scan:
          project_key: your_project_key
      - run:
          name: Run Unit Tests with Coverage
          command: pytest --cov=my_package --cov-report=xml --junitxml=test-report.xml

  deploy_environment:
    executor: ci-executor
    parameters:
      env:
        type: string
    steps:
      - checkout
      - setup_remote_docker
      - run:
          name: Authenticate with Artifactory
          command: |
            echo "$ARTIFACTORY_PASSWORD" | docker login your-artifactory-domain --username $ARTIFACTORY_USERNAME --password-stdin
      - run:
          name: Build and Push Docker Image
          command: |
            docker build --build-arg APP_VER=$CIRCLE_BUILD_NUM -t your-artifactory-domain/myapp:$CIRCLE_BUILD_NUM .
            docker push your-artifactory-domain/myapp:$CIRCLE_BUILD_NUM
      - run:
          name: Deploy Infrastructure
          command: |
            cd infrastructure
            terraform init -backend-config="env/<< parameters.env >>/backend.tf"
            terraform apply -var-file="env/<< parameters.env >>/terraform.tfvars" -auto-approve
      - run:
          name: Save Deploy Timestamp
          command: date +%s > deploy_timestamp.txt
      - persist_to_workspace:
          root: .
          paths:
            - deploy_timestamp.txt

  run_tests:
    executor: ci-executor
    parameters:
      env:
        type: string
    steps:
      - checkout
      - run:
          name: Run Integration Tests
          command: |
            robot -x junit.xml -o output.xml login_tests
            echo $? > test_status.txt
      - run:
          name: Run DAST (Veracode)
          command: |
            veracode_scan --appname "myapp" --createprofile true --filepath ./build_artifacts --vid $VERACODE_API_ID --vkey $VERACODE_API_KEY
      - persist_to_workspace:
          root: .
          paths:
            - test_status.txt

  conditional_destroy:
    executor: ci-executor
    parameters:
      env:
        type: string
    steps:
      - checkout
      - attach_workspace:
          at: ~/workspace
      - run:
          name: Conditional Destroy Logic (48h TTL)
          command: |
            TEST_STATUS=$(cat ~/workspace/test_status.txt)
            DEPLOY_TS=$(cat ~/workspace/deploy_timestamp.txt)
            NOW=$(date +%s)
            TTL_SECONDS=$((48 * 60 * 60))
            AGE=$((NOW - DEPLOY_TS))

            echo "⏱️ Deployment age: $AGE seconds"
            echo "✅ TTL threshold: $TTL_SECONDS seconds"

            if [ "$TEST_STATUS" -eq 0 ]; then
              echo "✅ Tests passed. Destroying environment now."
            elif [ "$AGE" -ge "$TTL_SECONDS" ]; then
              echo "🧹 Tests failed, but deployment is over 48h old. Destroying..."
            else
              echo "⏳ Tests failed. Deployment is still within 48h. Skipping teardown."
              exit 0
            fi

            cd infrastructure
            terraform init -backend-config="env/<< parameters.env >>/backend.tf"
            terraform destroy -var-file="env/<< parameters.env >>/terraform.tfvars" -auto-approve

  release_tests:
    executor: ci-executor
    steps:
      - checkout
      - run:
          name: Smoke Tests
          command: robot -x junit.xml -o output.xml smoke_tests
      - run:
          name: Regression Tests
          command: robot -x junit.xml -o output.xml regression_tests
      - run:
          name: Integration Tests
          command: robot -x junit.xml -o output.xml integration_tests

workflows:
  version: 2

  dev_validation:
    jobs:
      - validate_dev_branch:
          filters:
            branches:
              only: /^dev\/.+$/

  test_deploy:
    jobs:
      - deploy_environment:
          env: test
      - run_tests:
          env: test
          requires:
            - deploy_environment
      - conditional_destroy:
          env: test
          requires:
            - run_tests

  release_candidate:
    jobs:
      - deploy_environment:
          env: release
      - run_tests:
          env: release
          requires:
            - deploy_environment
      - release_tests:
          requires:
            - run_tests
      - conditional_destroy:
          env: release
          requires:
            - run_tests

  production:
    jobs:
      - deploy_environment:
          env: prod
      - run_tests:
          env: prod
          requires:
            - deploy_environment
      - conditional_destroy:
          env: prod
          requires:
            - run_tests

```