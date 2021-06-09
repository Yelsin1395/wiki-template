# Tokens
To run GitHub Actions, we always need a set of tokens so the actions can run different tasks: read repo, write packages, etc.

Although GitHub now has the GITHUB_TOKEN, it cannot be used when you want to run certain actions that are across repositories (really GitHub was created with the repository in mind, therefore it has some limitations when used as an organization that is usually what you do when you are running your entire organization dev stack in github).

Therefore we need to create a Personal Access Token that comes from the org owner (katlimruiz).

We need the following tokens:
1. One to read repositories, and to publish a package in GitHub Packages (either being a node package, or a Docker image) = `katlimruiz.org_build_deploy`.
2. One to pull the Docker images from GitHub Packages = `katlimruiz.org_pull_image`.

And these tokens need to be registered as Organization Secrets, so they can be used in any GitHub Action workflow.
1. ORG_BUILD_DEPLOY_TOKEN = katlimruiz.org_build_deploy
2. ORG_PULL_IMAGE = katlimruiz.org_pull_image

And in GHA, you can access these secrets by using:

```
{{ secrets.ORG_BUILD_DEPLOY_TOKEN }}
```
