# Git Flows

| **Strategy**                | **Key Branches**                                     | **Release Branch**  | **Branch Purpose**                                                                                                                                                                                                                                                                            | **Advantages**                                                                                                                 | **Disadvantages**                                                                                     | **Use Case**                                                                        |
|-----------------------------|------------------------------------------------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
| **Git Flow**                | `main`, `develop`, `feature`, `release`, `hotfix`    | `release`           | - **`main`**: Stable production-ready code. <br/> - **`develop`**: Integration branch for all changes. <br/> - **`feature`**: Individual features or tasks. <br/> - **`release`**: Final testing and bug fixes for an upcoming release. <br/> - **`hotfix`**: Emergency fixes for production. | - Structured and organized. <br/> - Isolates feature, release, and hotfix development. <br/> - Ideal for teams with formal QA. | - Overhead with long-lived branches. <br/> - Complex workflows not ideal for small teams or projects. | Complex projects with well-defined release cycles.                                  |
| **GitHub Flow**             | `main`, `feature`                                    | `main`              | - **`main`**: Single branch with production-ready code. <br/> - **`feature`**: Short-lived branches for changes or tasks.                                                                                                                                                                     | - Simple and lightweight. <br/> - Focuses on rapid development. <br/> - Easy for smaller teams.                                | - Limited formal structure for releases. <br/> - Not suitable for managing parallel releases.         | Continuous integration and delivery in small-to-medium projects.                    |
| **GitLab Flow**             | `main`, `staging`, `production`, `feature`           | `production`        | - **`main`**: General branch for integration and changes. <br/> - **`staging`**: Code awaiting final testing before production. <br/> - **`production`**: Live production code. <br/> - **`feature`**: Individual changes or features.                                                        | - Aligns with DevOps workflows. <br/> - Supports environment-specific deployments.                                             | - Environment-based branches may require custom workflows for complex systems.                        | Projects requiring environment-based workflows and CI/CD integration.               |
| **Trunk-Based Development** | `main` or `trunk`, short-lived branches              | `main`              | - **`main`/`trunk`**: Single branch for active development and production-ready code. <br/> - **Short-lived branches**: Temporary branches for small tasks.                                                                                                                                   | - Simplifies branching. <br/> - Encourages rapid delivery and tight feedback loops. <br/> - Fewer merge conflicts.             | - Requires robust CI/CD pipelines and strict developer discipline.                                    | Fast-moving projects prioritizing frequent small deployments.                       |
| **Feature Branching**       | `main` or `develop`, `feature` branches              | `main` or `develop` | - **`main`/`develop`**: Integration branches for finalized or ongoing development. <br/> - **`feature`**: Task-specific or feature-specific branches.                                                                                                                                         | - Clear separation of work by feature. <br/> - Individual branches for specific tasks.                                         | - Long-lived feature branches may lead to merge conflicts and integration delays.                     | Teams working on independent, modular tasks or features.                            |
| **Release Branching**       | `release-vX.Y`, `feature`, `hotfix`                  | `release-vX.Y`      | - **`release-vX.Y`**: Stable release branch for a specific version. <br/> - **`feature`**: Individual task-specific branches. <br/> - **`hotfix`**: Emergency patches for production versions.                                                                                                | - Simplifies management of multiple releases. <br/> - Clear isolation of release-specific changes.                             | - Higher branch management overhead. <br/> - Repetitive work for fixes across multiple branches.      | Projects with versioned deliverables or multiple supported versions.                |
| **Sequential Release**      | `release-X`, `feature-issue`, new branch per release | `release-X`         | - **`release-X`**: The main branch for development and eventual deployment of the release. <br/> - **`feature-issue`**: Short-lived branches tied to individual tasks (e.g., JIRA items).                                                                                                     | - Predictable workflow. <br/> - Isolated development per release. <br/> - Natural continuity across releases.                  | - Merge conflicts for fixes applied across releases. <br/> - Propagation of technical debt.           | Projects with linear, sequential releases needing clear isolation between versions. |

## Git Flow
```mermaid
gitGraph
   commit id: "Start of Development"
   branch develop
   commit id: "Feature A"
   branch featureA
   checkout featureA
   commit id: "Work on Feature A"
   checkout develop
   merge featureA tag: "Feature A Complete"
   commit id: "Feature B"
   branch featureB
   checkout featureB
   commit id: "Work on Feature B"
   checkout develop
   merge featureB tag: "Feature B Complete"
   commit id: "Prepare Release"
   branch release-1.0
   checkout release-1.0
   commit id: "Fix Bugs in Release"
   checkout main
   merge release-1.0 tag: "Release 1.0 Deployed"
   checkout develop
   merge release-1.0 tag: "Sync Develop with Main"
   commit id: "Start Next Development Cycle"
```

## GitHub Flow

```mermaid
gitGraph
    commit id: "Start of Development"
    checkout main
    commit id: "Feature A Started"
    checkout main
    branch featureA
    checkout featureA
    commit id: "Work on Feature A"
    checkout main
    merge featureA tag: "Feature A Merged"
    branch featureB
    checkout featureB
    commit id: "Work on Feature B"
    checkout main
    merge featureB tag: "Feature B Merged"
    commit id: "Deploy Release"

```