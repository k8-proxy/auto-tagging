# Auto tagging, release and labelling

# Description

This action can be used to automate tag and release creation, adding label to the PR and issues on merge to main and develop branch.

# Inputs
main_branch_name      - main branch of repository 
develop_branch_name   - develop branch of repositoty
GUTHUBPAT             - Github PAT which has access repo admin access

# Usage

For this action to work as expected,
- The trigger should be On pull request merge/close

  ```
    on:
    # Triggers the workflow on push or pull request events but only for the main branch
    pull_request:
      branches:
        - master
        - develop
      types: [closed]
  ```
- You can use the action as below
  ```
    jobs:

    build:

      runs-on: ubuntu-latest

      steps:
        - uses: actions/checkout@v2
        - id: auto_tagging

          uses: k8-proxy/auto-tagging@v0.0.1
          with:
             GITHUBPAT: ${{secrets.GITHUBPAT}}
             main_branch_name: "master"
             develop_branch_name: "develop"
  ```
- This action addes label to issues linked to PR automatically. For this to work, you have to mention issue in following manner
    - Use `Changes` keyword in body of PR
    - if issue is in same repo, mention {issue_number} with `#` prefix
        ```
          Changes #11
        ```
    - if issue is in different repo, mention {repo_owner}/{repo_name}#{issue_number in body of PR
        ```
          Changes k8-proxy/cs-k8s-api#17
        ```

# Functionality

- The standard for tag naming here defined in format `v1.2.3` where

  ```
          1 - version
          2 - major
          3 - minor     
  ```

- The action is valid when github actions trigger will be `PR merge` to `main` and `develop` branch

- On merge to `main` branch
  - a major will be incremented and a `tag_name` is formed
      - Example : v0.1.2 --> v0.2.0  ( pre-release )
  - It will be pushed as a `pre-release`
  - A new label with newly created `tag_name`  
  - The above `tag_name` label will be added in label section of merged `PR` 
  - The action will pickup all issues which is prefixed with `Changes` keyword in `PR` body and label all of them with `tag_name`

- On merge to `develop` branch
  - a major will be incremented and a `tag_name` is formed
      - Example : v0.1.2 --> v0.1.3 ( just a tag )
  - It will be pushed as a `tag`
  - A new label with newly created `tag_name`  
  - The above `tag_name` label will be added in label section of merged `PR` 
  - The action will pickup all issues which is prefixed with `Changes` keyword in `PR` body and label all of them with `tag_name`

