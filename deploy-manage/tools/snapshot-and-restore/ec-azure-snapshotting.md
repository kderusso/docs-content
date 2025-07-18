---
mapped_pages:
  - https://www.elastic.co/guide/en/cloud/current/ec-azure-snapshotting.html
  - https://www.elastic.co/guide/en/cloud-heroku/current/ech-azure-snapshotting.html
applies_to:
  deployment:
    ess:
products:
  - id: cloud-hosted
---

# Configure a snapshot repository using Azure Blob storage [ec-azure-snapshotting]

Configure a custom snapshot repository using your Azure Blob storage account.


## Prepare a container [ec-prepare-azure-container]

Follow the Microsoft documentation to [set up an Azure storage account](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create) with an access key, and then [create a container](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal).


## Enable the `repository-azure` plugin in {{stack}} 7.17 and earlier [ec-enable-azure-plugin]

For deployments with **{{stack}} version 7.17 and earlier**, you’ll need to enable the `repository-azure` plugin to use the Azure repository type. On the Azure platform, the plugin is enabled by default. If your deployment is on AWS or GCP, follow these steps to enable the `repository-azure` plugin:

1. Refer to [Azure Repository Plugin](https://www.elastic.co/guide/en/elasticsearch/plugins/7.17/repository-azure.html) to download the version of the plugin that matches your {{stack}} version.
2. Upload the plugin to your deployment:

    1. Log in to the [{{ecloud}} Console](https://cloud.elastic.co?page=docs&placement=docs-body).
    2. Go to your deployment.
    3. From the lower navigation menu, select **Extensions** and then select **Upload extension**.
    4. Specify the plugin name (`repository-azure`) and version.
    5. Select **An installable plugin (compiled, no source code)**.
    6. Select **Create extension**.
    7. Navigate back to the **Extensions** page.
    8. Select the extension name.
    9. Drag and drop to upload the `repository-azure` plugin zip file.



### Configure the keystore [ec-configure-azure-keystore]

Create an entry for the Azure client in the {{es}} keystore:

1. Log in to the [{{ecloud}} Console](https://cloud.elastic.co?page=docs&placement=docs-body).
2. Navigate to the **Security** page of the deployment you wish to configure.
3. Locate **{{es}} keystore** and select **Add settings**.
4. With **Type** set to **Single string**, add the following keys and their values:

    * `azure.client.secondary.account`
    * `azure.client.secondary.key`

5. Select **Save**.


### Create the repository [ec-create-azure-repository]

1. Open {{kib}} and go to **Management** > **Snapshot and Restore**.
2. On the **Repositories** tab, select **Register a repository**.
3. Provide a name for your repository and select type **Azure**.
4. Provide the following settings:

    * Client: `secondary`

        * You can also use `default`, but we recommend using `secondary` to ensure that your secure settings are mapped to the correct repository definition.

    * Container: The name of your Azure container
    * base_path: A directory to contain the snapshots

        * This setting is optional. Include a `base_path` if you have multiple clusters writing to the same Azure repository. This ensures that a snapshot won’t overwrite the snapshot metadata for another cluster.

5. Add any other settings that you wish to configure.
6. Select Register.
7. Select **Verify** to confirm that your settings are correct and the deployment can connect to your repository.

Your snapshot repository is now set up using Azure Blob storage! You can use {{kib}} to manage your snapshots and begin sending {{es}} snapshots to your own container. For details, check the [Snapshot and Restore](create-snapshots.md) documentation.

