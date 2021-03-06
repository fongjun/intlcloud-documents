You can view the status of the cluster both in the cluster list page and the basic information section of the cluster details page.
![Cluster list page](https://main.qcloudimg.com/raw/fc9882d5985397c260d7441117fe8d60.jpg)
![Cluster details page](https://main.qcloudimg.com/raw/279b5c35b2bcc5a1ada5118aa61f3459.jpg)
Cluster status indicates whether the cluster is changing or in use, including OK, processing, etc. The meaning of a specific status is as follows:

| Status | Meaning | 
|---------|---------|
| Normal | Cluster creation is complete and there are no configuration changes, cluster restart, etc., and the cluster can be accessed and used normally. |
| Processing | Operations such as cluster creation, cluster configuration change, and cluster restart are in progress, during which access to some services (such as Kibana, data storage and query) will be affected. |

Among which, health status is one of the most important indicators used to monitoring the ES cluster, which is used to indicate whether the cluster is working normally overall. The categories of health status are as follows:

| Color | Health Status |
|-------|-------------|
| Green | Everything is good (cluster is fully functional). |
| Yellow | All data is available but some replicas are not yet allocated (cluster is fully functional). |
| Red | Some data is not available for whatever reason (cluster is partially functional) |

For more information, see [ES Cluster Health](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/_cluster_health.html).

