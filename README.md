# GitHub Actions: Reusable Workflows

## Overview
As a best practice in Continuous Integration and Continuous Deployment (CI/CD), reusable workflows in GitHub Actions are a powerful feature that enhances code reuse, promotes best practices, boosts productivity, and improves maintainability and collaboration. This ultimately leads to more efficient and effective CI/CD processes.

## Benefits of Reusable Workflows
- Reusable workflows facilitate the creation of modular workflows, reducing code duplication across multiple locations. This centralization simplifies maintenance and updates, as changes can be made in one place.
- By promoting best practices, reusable workflows allow teams to develop a library of well-designed, tested, and effective workflows, which can be utilized across different repositories.
- Enhances flexibility, maintainability, and productivity by enabling teams to build upon existing workflows rather than starting from scratch.

## Testing Reusable Workflows

### Problem Statement
While reusable workflows offer significant advantages, testing these workflows can be challenging, especially since they may be utilized across multiple repositories. A single change in a reusable workflow can impact various repositories if not thoroughly tested.

### Solution
GitHub Actions provides two key features, `workflow_dispatch` and `repository_dispatch`, that allow users to trigger workflows either manually or from external systems. 
- The `workflow_dispatch` event enables users to manually initiate a workflow through the GitHub API or UI, allowing for user-driven actions instead of automatic triggers.
- In contrast, the `repository_dispatch` event allows external systems to programmatically trigger workflows via API calls. Custom event types can be defined, enabling workflows to respond to specific events and providing granular control over their execution.

## Implementation
Create a centralized workflow repository named `gh-actions-wf`, which contains multiple reusable workflows:

<img width="783" alt="image" src="https://github.com/user-attachments/assets/771f2155-947c-4fae-8e99-69e86b5a4f29">

- **get-branch-name.yml**: Determines the branch name based on the event type (push, pull request, or repository dispatch).
- **get-short-sha.yml**: Retrieves the 7-digit short SHA to append to the Docker image.
- **nodejs-build.yml**: Builds a Node.js Docker image, taking service names, Git SHA, and branch name as inputs.
- **ci-unittest.yml**: Configured with `workflow_dispatch` to trigger workflows on the `hw-ui-service` repository. Any changes made to the files in the `gh-actions-wf` workflow repository will trigger a unit test on the `hw-ui-service` repository, ensuring that changes are tested. GitHub Actions is set to listen for push events on the develop branch. The `hw-ui-service` repository utilizes the reusable workflows in its `precommit.yml` file, which is configured to listen for `repository_dispatch` events of the `ci-unittest` type.

## Workflow Execution
Whenever a change is made to the reusable workflow repository, it triggers a GitHub API call to initiate the unit tests on the invoked `hw-ui-service` repository.

### Branch Structure:
- **Develop Branch**: The default branch for development changes to reusable workflow templates.
- **Main Branch**: The production branch that contains only promoted changes that have been tested.

<img width="225" alt="image" src="https://github.com/user-attachments/assets/011bbb69-1486-4da9-ab35-921bfd9f222f">

Changes to `gh-actions-wf` trigger an API call to initiate unit tests on other repositories - [GitHub Actions for gh-actions-wf](https://github.com/thunderbirdgit/gh-actions-wf/actions)

<img width="649" alt="image" src="https://github.com/user-attachments/assets/c96260de-202f-477f-9a8c-b9d16d78c8c6">

## Testing Reusable Workflows on Invoked Repositories
Changes made to the workflow repository will trigger unit tests on the `hw-ui-service` repository to verify that the changes are functioning as expected - [GitHub Actions for hw-ui-service](https://github.com/thunderbirdgit/hw-ui-service/actions)

<img width="722" alt="image" src="https://github.com/user-attachments/assets/066cd5e4-5370-4f14-af0e-b65d6489b3d0">

By implementing unit testing for reusable workflows, we can ensure that changes are thoroughly validated before being applied to dependent repositories. This practice not only enhances the reliability of our CI/CD processes but also fosters a culture of quality and accountability within development teams.

## References
- [GitHub Documentation on workflow_dispatch](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows#workflow_dispatch)
- [GitHub Documentation on repository_dispatch](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows#repository_dispatch)
