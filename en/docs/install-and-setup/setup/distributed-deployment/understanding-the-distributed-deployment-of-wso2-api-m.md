# Understanding the Distributed Deployment of WSO2 API-M

Before understanding how to deploy WSO2 API Manager (WSO2 API-M), let's understand the WSO2 API-M distributed deployment better.

## Understanding the WSO2 API-M architecture

WSO2 API Manager uses the following main components:

|                     |                                                                                                                                                       |
|---------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Publisher**       | Enables API providers to easily publish their APIs, share documentation, provision API keys, and gather feedback on API features, quality, and usage. |
| **Developer Portal**           | Enables consumers to self-register, discover API functionality, subscribe to APIs, evaluate them, and interact with API publishers.                   |
| **Key Manager**     | Responsible for all security and key-related operations.                                                                                              |
| **Gateway**         | Responsible for securing, protecting, managing, and scaling API calls.                                                                                |
| **Traffic Manager** | Used to make a decision on throttling. It also works as an event hub for broadcasting controller events such as throttling events, block conditions, revoke token retrieval events, API events, API policy events, application events, application policy events, application keys events, subscription events, and subscription policy events.                                                                                                               |

For more information on the above, see the main components of a distributed system.

API Manager uses the following internal datastores to store temporary internal system data required by the local instance of the API Manager.

-   **Carbon database** - Stores general internal data related to the product.

-   **Message Broker database** - Stores the product's message broker related data.

!!! note
    Product users should not configure the above datastores to use an external RDBMS. These datastores will be managed locally by the respective product instance.

Additionally, API Manager uses the following databases, which are shared among the server nodes. These are responsible for storing user-specific application data.

-   **API Manager database** - Stores information related to the APIs along with the API subscription details. The Key Manager Server uses this database to store user access tokens that are used for verification of API calls. The API Manager database is also referred to as **WSO2\_AM\_DB** and **apimgtdb**.
-   **Shared database** - Stores information related to users and API metadata. In previous versions of API Manager a distinction was made between the user management database(used to store users and user roles) and the registry database(used to store API metadata). But now the Shared database will unify these requirements under one single database instance. This helps to reduce the complexity of the deployment. The Shared database user related information is accessed by the Key Manager Server, Developer Portal, and Publisher. The Shared database API metadata is accessed by the Publisher and Developer Portal. Optionally if you are planning to create this setup for a multi-tenanted environment (create and work with tenants), the Gateway and Key Manager components will also need to access API metadata in this database. The Shared database is also referred to as  **WSO2\_SHARED\_DB** and **shareddb**.


WSO2 API Manager components use the databases as follows:

<table>
<thead>
<tr class="header">
<th><br />
</th>
<th><p><strong>API Manager<br />
database</strong></p>
<p><code>              apimgtdb             </code></p>
<p><code>              WSO2_AM_DB             </code></p></th>
<th><p><strong>Shared Database</strong></p>
<p><code>                                            shareddb                           </code></p>
<p><code>              WSO2_SHARED_DB             </code></p></th>

</tr>
</thead>
<tbody>
<tr class="odd">
<td><p><strong>Publisher</strong></p></td>
<td><p>Used</p></td>
<td><p>Used</p></td>

</tr>
<tr class="even">
<td><p><strong>Developer Portal</strong></p></td>
<td><p>Used</p></td>
<td><p>Used</p></td>

</tr>
<tr class="odd">
<td><p><strong>Key Manager</strong></p></td>
<td><p>Used</p></td>
<td><p>Used</p></td>
</tr>
<tr class="even">
<td><p><strong>Gateway</strong></p></td>
<td><p>Not used</p></td>
<td><p>Used (in multi-tenancy mode/ in multiple gateway mode when Google Analytics is used)</p></td>

</tr>
<tr class="odd">
<td><strong>Traffic Manager</strong></td>
<td>Used</td>
<td>Used</td>

</tr>
</tbody>
</table>

When we consider a distributed deployment of WSO2 API Manager, we have the option of separating the five components and clustering each component as needed. Let's look more closely at how the API Manager components are deployed separately.

## Understanding the distributed deployment

In the following diagram, the five components are set up in a distributed deployment, and the five databases are connected to the relevant components respectively. The entire setup is also fronted by a load balancer.


[![Understanding the distributed deployment]({{base_path}}/assets/img/setup-and-install/db-connections-distributed-deployment.png)]({{base_path}}/assets/img/setup-and-install/db-connections-distributed-deployment.png)


