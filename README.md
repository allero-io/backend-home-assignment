# Backend home assignment
## Background
As a company, we'd like to monitor our GitHub activity, specifically our CI/CD pipelines.
Our CI/CD pipelines are implemented as [GitHub Actions Workflows](https://github.com/features/actions). Our company has several [GitHub Organizations](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/about-organizations), and we'd like to monitor all of their CI/CD pipelines.

GitHub provides developers with [GitHub Apps](https://docs.github.com/en/developers/apps/building-github-apps/creating-a-github-app) to allow them to interact with the GitHub API in an [authenticated manner](https://docs.github.com/en/developers/apps/building-github-apps/authenticating-with-github-apps). Organizations can install a GitHub App within the organization to allow it to access the organization's data, and each GitHub App has access to all of the organizations which installed it (see the [GitHub API](https://docs.github.com/en/rest/reference/apps#list-installations-for-the-authenticated-app) on how to access this information).

For your convenience, we've created a GitHub App and organizations which already have it installed, and credentials are provided with the assignment.

We provide the following organizations and repos:
* allero-test1/test-repo1
* allero-test1/test-repo2
* allero-test2/test-repo1
* allero-test2/test-repo2

Our already-set-up organizations have public and private repositories, which have pipelines that run periodically (once a day at midnight), so that the output should be consistent (other than dates and runtimes). Example outputs for organization `allero-test1` is provided in the endpoints.
## Overview
Create a multi-container docker application consisting of a web server that exposes a  Tasks
* Create a basic web server in Node.js + express.js, located at `localhost:6123`. On **server startup**, the service should query GitHub for all the information it needs to provide the below endpoints successfully (meaning, no calls to the GitHub API should be made from the endpoints, apart for the explicit refresh endpoint).
All data should be stored in a DB, and queried and served via the API when needed.
* Implement the endpoint `GET localhost:6123/org/<org-name>/daily-runs/<workflow-id?>`
This endpoint is required to return a list of all of the workflow runs in the organization org-name from 00:00 same day (server time) onward. Workflow-id is optional, and if provided - then only runs of that workflow-id are to be provided.
Here’s the GitHub API on workflows, for reference: [Actions - GitHub Docs](https://docs.github.com/en/rest/reference/actions#workflows).

  <u>Note:</u> This endpoint should work for any organization that the GitHub App is installed in.

  The endpoint should return a JSON object of the following format. The example provided is based on org `allero-test1` and relies on the crendentials provided with the assignment. `http://localhost:6123/org/allero-test1/daily-runs`:
  ```
  [
    {
      "_id":"6242b7b61f86621cf36bacf3",
      "workflowId":22803291,
      "workflowName":"Pipeline2",
      "workflowPath":".github/workflows/pipeline2.yml",
      "workflowRunId":2055800427,
      "status":"completed",
      "conclusion":"success",
      "runAt":1648516373000,
      "repoId":475198994,
      "repoName":"test-repo1",
      "ownerId":102559014,
      "ownerName":"allero-test1",
      "branch":"main",
      "commitSHA":"593ddcf11650629feb02bdb217966882cff9d916"
    },
    {
      "_id":"6242b7b61f86621cf36bacf4",
      "workflowId":22803290,
      "workflowName":"Pipeline1",
      "workflowPath":".github/workflows/pipeline1.yml",
      "workflowRunId":2055760127,
      "status":"completed",
      "conclusion":"success",
      "runAt":1648515564000,
      "repoId":475198994,
      "repoName":"test-repo1",
      "ownerId":102559014,
      "ownerName":"allero-test1",
      "branch":"main",
      "commitSHA":"593ddcf11650629feb02bdb217966882cff9d916"
    },
    {
      "_id":"6242b7b61f86621cf36bacf7",
      "workflowId":22804077,
      "workflowName":"Pipeline2",
      "workflowPath":".github/workflows/pipeline2.yml",
      "workflowRunId":2055917321,
      "status":"completed",
      "conclusion":"success",
      "runAt":1648518780000,
      "repoId":475203626,
      "repoName":"test-repo2",
      "ownerId":102559014,
      "ownerName":"allero-test1",
      "branch":"main",
      "commitSHA":"593ddcf11650629feb02bdb217966882cff9d916"
    },
    {
      "_id":"6242b7b61f86621cf36bacf8",
      "workflowId":22804076,
      "workflowName":"Pipeline1",
      "workflowPath":".github/workflows/pipeline1.yml",
      "workflowRunId":2055784848,
      "status":"completed",
      "conclusion":"success",
      "runAt":1648516030000,
      "repoId":475203626,
      "repoName":"test-repo2",
      "ownerId":102559014,
      "ownerName":"allero-test1",
      "branch":"main",
      "commitSHA":"593ddcf11650629feb02bdb217966882cff9d916"
    }
  ]
  ```
* Implement the endpoint `POST localhost:6123/refresh-data` with an empty body.
This endpoint should refresh td support the user’s own GitHub App credentials. Make sure these are configurable by the user using the application.
* The DB must be persistent between runs of the docker environment, however for convenience, you may clear the DB on each ingestion of the GitHub data (at startup and when using the refresh endpoint).
* Package the app and its dependencies in a docker-compatible, easy-to-launch solution.
