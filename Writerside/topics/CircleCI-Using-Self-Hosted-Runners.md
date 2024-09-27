# CircleCI Using Self-Hosted Runners

## Create a resource class and get a token
Self-Hosted runners authenticate to CircleCI using a *Resource Class Token*.

A resource class is a name for a pool of self-hosted runners.

In CircleCI navigate to
<ui-path>Self-hosted Runners → Create Resource Class</ui-path>.

Fill in the `Resource Class Label` field and click `Save and Continue`.

You will be presented with a `Resource Class Token`. Save the token in secure storage, *it will NOT be displayed again*.

## Install and Configure a Self-Hosted Runner
We're going to do this on `Ubuntu 24.04` machine with `docker` already installed.<br/>
CircleCI Self-Hosted runners come in two flavors—a container and OS executable. We will be using the OS executable runner in this example.

* install CircleCI CLI using snap
```shell
sudo snap install circleci
```
* install the runner executable from circleci package repository
```shell
curl -s https://packagecloud.io/install/repositories/circleci/runner/script.deb.sh\?any\=true | sudo bash
```
```shell
sudo apt-get install -y circleci-runner
```
* edit `/etc/circleci-runner/circleci-runner-config.yaml`
* set `auth_token` to the value obtained in [](#create-a-resource-class-and-get-a-token)
```yaml
api:
  auth_token: cfa89e104d9f98b4ea6c6fbb5ce30115659b20977108ca5e574aacae98b3de168235824dc782a475
  # On server, set url to the hostname of your server installation. For example,
  # url: https://circleci.example.com
runner:
  name: linux-host
  working_directory: "/var/lib/circleci-runner/workdir"
  cleanup_working_directory: true
logging:
  level: "info"
  format: "text"
  file: "-"
```
* enable and start the runner service
```shell
sudo systemctl enable circleci-runner && sudo systemctl start circleci-runner
```
* check service status
```shell
sudo systemctl status circleci-runner
```
If everything went well, you should now see your runner on `app.circleci.com`

![circleci-using-self-hosted-runners-01.png](circleci-using-self-hosted-runners-01.png){thumbnail="true"}

## Configure and run a test build using the runner
* in the root of your project, create a `.circleci` directory and a file named `config.yml` under it.
* place the following content into the `config.yml` file and commit
```yaml
version: 2.1

workflows:
  build-workflow:
    jobs:
      - runner
jobs:
  runner:
    machine: true
    resource_class: <namespace>/<resource-class>
    steps:
      - run: echo "Hi I'm on Runners!"
```
> Take care to replace `<namespace>/<resource-class>` value with the `Resource Class` value shown on your "Self-Hosted Runners" page.
> 
{style="note"}
* in the `app.circleci.com` navigate to `Projects` and click `Set Up Project`
* Select `Fastest: Use the .circleci/config.yml in my repo` option, select branch and click `Set Up Project`
* navigate to `Pipelines` and you should see your project building
* Expand the pipeline, click `runner`
* if everything went well, you should see the following last build step
* 
![circleci-using-self-hosted-runners-02.png](circleci-using-self-hosted-runners-02.png) {thumbnail="true"}