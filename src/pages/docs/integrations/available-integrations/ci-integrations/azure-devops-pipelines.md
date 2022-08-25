---
title: "Azure DevOps Pipelines"
page_id: "azure-devops-pipelines"
updated: 2022-08-11
warning: false
contextual_links:
  - type: section
    name: "Prerequisites"
  - type: link
    name: "CI Integrations"
    url: "/docs/integrations/ci-integrations/"
---

[Azure Pipelines](https://azure.microsoft.com/en-us/services/devops/pipelines/) is a continuous integration (CI) and continuous delivery (CD) service that's integrated with Microsoft Azure DevOps. Software development teams can use Azure Pipelines to automatically build, test, and deploy code in Azure DevOps.

To set up an Azure DevOps Pipelines integration for your API, first create a pipeline in the version control system of your choice, and then configure your API in Postman. After you set up the integration, you can view the status of builds from within Postman.

> Azure DevOps Pipelines integration isn't available on Azure DevOps Server (hosted on-premises).

## Contents

* [Creating a pipeline in Azure DevOps](#creating-a-pipeline-in-azure-devops)
* [Configuring an Azure DevOps Pipelines integration](#configuring-an-azure-devops-pipelines-integration)
* [Viewing build status](#viewing-build-status)
* [Viewing collection run details](#viewing-collection-run-details)
* [Configuring Newman for Azure DevOps Pipelines](#configuring-newman-for-azure-devops-pipelines)

## Creating a pipeline in Azure DevOps

If you haven't already, create a pipeline in the repository you use for your API. To create a pipeline, you add an `azure-pipelines.yml` file to your repository. You define your pipeline in this YAML file. To learn more, see [the Azure Pipelines documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/get-started/pipelines-get-started).

## Configuring an Azure DevOps Pipelines integration

1. Open your API by selecting **APIs** in the sidebar, and then selecting an API and a version. *Each API version can be linked to one CI project*.
1. Select the **Test** tab.
1. Under **Connect to CI/CD Builds**, select **Azure DevOps**.
1. You'll be prompted to allow Postman to access your Azure DevOps account. After you grant access, you can close the browser tab and return to Postman.
1. Enter a **Nickname** to help you recognize the integration later.
1. Enter the Azure DevOps **Organization Name** where you created the project pipeline.
1. Select the **CI project** used for your API.
1. Select **Connect**.

<img alt="Connect to Azure DevOps Pipelines" src="https://assets.postman.com/postman-docs/bitbucket-pipelines-connect-project-v9-15.jpg" width="518px">

## Viewing build status

After you set up an Azure DevOps Pipelines integration, information for build jobs is available in Postman. For each build you can view the branch, start time, duration, and status (`Succeeded` or `Failed`). You can also view the results of collection runs that are [configured in your pipeline using Newman](#viewing-collection-run-details).

To view build jobs, open an API version and select the **Test** tab. The most recent jobs are listed under **CI/CD Builds**.

> You can't start Azure Pipelines builds directly in Postman. To start a new build, go to Azure DevOps.

Select **View All Builds** to view the full list of build jobs. From here you can take the following actions:

* Use the dropdown lists to filter jobs by branch or build status.
* To view a build in Azure DevOps, select the build name.
* To get the latest build status information, select <img alt="Refresh icon" src="https://assets.postman.com/postman-docs/icon-refresh-v9-5.jpg#icon" width="14px"> **Refresh**.
* To edit or delete the integration, select the more actions icon <img alt="More actions icon" src="https://assets.postman.com/postman-docs/icon-more-actions-v9.jpg#icon" width="16px">.
* To view more details for a build, use the arrows to expand a build and expand **Build Steps**. For each build step you can view the name, duration, and status.

<img alt="View all Azure DevOps Pipelines builds" src="https://assets.postman.com/postman-docs/bitbucket-pipelines-builds-v9-19.jpg">

## Viewing collection run details

Using Newman, you can run Postman collections with your API tests as part of an Azure DevOps pipeline. The Postman cloud reporter can send details about the collection runs back to Postman.

To view details for collections that were run as part of a build, first [configure Newman for Azure DevOps Pipelines](#configuring-newman-for-azure-devops-pipelines) and then start a new build in Azure DevOps. To learn more about starting builds, see [the Azure Pipelines documentation](https://docs.microsoft.com/en-us/azure/devops/pipelines/process/runs). After the build is complete, use the arrows to expand a build and expand **Collection Runs**. Then expand a collection to view details about a collection run.

<img alt="View Azure DevOps Pipelines collection runs" src="https://assets.postman.com/postman-docs/bitbucket-pipelines-collection-runs-v9-19.jpg">

> Select **View Report** to view a collection run report in the Postman **History**. Learn more about using the [Collection Runner](/docs/running-collections/intro-to-collection-runs/).

## Configuring Newman for Azure DevOps Pipelines

With the help of Newman and the Postman API, you can run API tests created in Postman as part of your Azure DevOps pipeline. First generate the Newman configuration code in Postman. Then add the configuration code to the `azure-pipelines.yml` file in your repository.

Each time the pipeline runs, Newman runs the collections that contain your tests. You can view the results of your tests in Postman. You an also configure the [Postman cloud reporter](https://www.npmjs.com/package/newman-reporter-postman-cloud) to send detailed collection run information back to Postman.

> Before you begin, make sure you’ve already [set up an integration](#configuring-an-azure-devops-pipelines-integration) between your API version and Azure DevOps Pipelines.

To generate configuration code for Newman:

1. Open your API version and select the **Test** tab.
1. Under **CI/CD Builds**, select **View All Builds**.
1. Select **Configure Newman**.
1. Select a **Collection** to run during pipeline builds. To be available in the dropdown list, you must first [add the collection as a test suite](/docs/designing-and-developing-your-api/testing-an-api/#adding-tests) to your API. You can also select an **Environment** to use.

    > If needed, select **+ Add More** to select other collections to run.

1. (Optional) Select the check box to use the Postman cloud reporter to send collection run information back to Postman. You can view the collection run details in the Postman **History** and on the API version **Test** tab.
1. Select **Copy** to copy the Newman configuration, and then select **Finish**.

<img alt="Generate Newman configuration" src="https://assets.postman.com/postman-docs/bitbucket-pipelines-generate-newman-v9-19.jpg" width="548px">

To add the Newman configuration to your Azure DevOps pipeline:

1. Edit the `azure-pipelines.yml` file in your repository.
1. Add the Newman configuration you copied from Postman to the `azure-pipelines.yml` file:
    * Replace all instances of `$POSTMAN_API_KEY` with a valid [Postman API Key](/docs/developer/intro-api/#generating-a-postman-api-key).
1. Commit and push the changes to your remote repository. This will automatically start a build in Azure DevOps Pipelines.
1. To view the test results in Postman, open your API and select the **Test** tab. Learn more about [Viewing collection run details](#viewing-collection-run-details).

### Example azure-pipelines.yml file

```yaml
trigger:
- master

pool:
  vmImage: ubuntu-latest

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '16.x'
  displayName: 'Install Node.js'

- script: |
    npm install -g newman newman-reporter-postman-cloud
  displayName: 'Install Newman and Postman Cloud Reporter'

- task: CmdLine@2
  displayName: 'Run Postman collection'
  env:
    POSTMAN_API_KEY: $(POSTMAN_API_KEY)
  inputs:
    script: newman run "https://api.getpostman.com/collections/4946945-3673316a-9a35-4b0d-a148-3566b490798d?apikey=${POSTMAN_API_KEY}" -r postman-cloud --reporter-apiKey "${POSTMAN_API_KEY}" --reporter-workspaceId "34f3a42c-18a7-4ad6-83fb-2c05767d63a7" --reporter-integrationIdentifier "46689-$(Build.BuildId)"
```