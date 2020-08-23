##Config Elasticsearch
The Elasticsearch configuration files are in the /etc/elasticsearch directory. There are two files:

elasticsearch.yml — Configures the Elasticsearch server settings. This is where all options, except those for logging, are stored, which is why we are mostly interested in this file.
logging.yml — Provides configuration for logging. In the beginning, you don’t have to edit this file. You can leave all default logging options. You can find the resulting logs in /var/log/elasticsearch by default.
The first variables to customize on any Elasticsearch server are node.name and cluster.name in elasticsearch.yml. As their names suggest, node.name specifies the name of the server (node) and the cluster to which the latter is associated.

If you don’t customize these variable, a node.name will be assigned automatically in respect to the Droplet hostname. The cluster.name will be automatically set to the name of the default cluster.

The cluster.name value is used by the auto-discovery feature of Elasticsearch to automatically discover and associate Elasticsearch nodes to a cluster. Thus, if you don’t change the default value, you might have unwanted nodes, found on the same network, in your cluster.

To start editing the main elasticsearch.yml configuration file:

sudo nano /etc/elasticsearch/elasticsearch.yml
Remove the # character at the beginning of the lines for node.name and cluster.name to uncomment them, and then change their values. Your first configuration changes in the /etc/elasticsearch/elasticsearch.yml file should look like this:
...
node.name: "My First Node"
cluster.name: mycluster1
...

Another important setting is the role of the server, which could be either “master” or “slave”. “Masters” are responsible for the cluster health and stability. In large deployments with a lot of cluster nodes, it’s recommended to have more than one dedicated “master.” Typically, a dedicated “master” will not store data or create indexes. Thus, there should be no chance of being overloaded, by which the cluster health could be endangered.

“Slaves” are used as “workhorses” which can be loaded with data tasks. Even if a “slave” node is overloaded, the cluster health shouldn’t be affected seriously, provided there are other nodes to take additional load.

The setting which determines the role of the server is called node.master. If you have only one Elasticsearch node, you should leave this option commented out so that it keeps its default value of true — i.e. the sole node should be also a master. Alternatively, if you wish to configure the node as a slave, remove the # character at the beginning of the node.master line, and change the value to false:

...
node.master: false
...

Another important configuration option is node.data, which determines whether a node will store data or not. In most cases this option should be left to its default value (true), but there are two cases in which you might wish not to store data on a node. One is when the node is a dedicated “master,” as we have already mentioned. The other is when a node is used only for fetching data from nodes and aggregating results. In the latter case the node will act up as a “search load balancer”.

Again, if you have only one Elasticsearch node, you should leave this setting commented out so that it keeps the default true value. Otherwise, to disable storing data locally, uncomment the following line and change the value to false:

...
node.data: false
...

Two other important options are index.number_of_shards and index.number_of_replicas. The first determines into how many pieces (shards) the index will be split into. The second defines the number of replicas which will be distributed across the cluster. Having more shards improves the indexing performance, while having more replicas makes searching faster.

Assuming that you are still exploring and testing Elasticsearch on a single node, it’s better to start with only one shard and no replicas. Thus, their values should be set to the following (make sure to remove the # at the beginning of the lines):

...
index.number_of_shards: 1
index.number_of_replicas: 0
...

One final setting which you might be interested in changing is path.data, which determines the path where data is stored. The default path is /var/lib/elasticsearch. In a production environment it’s recommended that you use a dedicated partition and mount point for storing Elasticsearch data. In the best case, this dedicated partition will be a separate storage media which will provide better performance and data isolation. You can specify a different path.data path by uncommenting the path.data line and changing its value:

...
path.data: /media/different_media
...

Once you make all the changes, please save and exit the file. Now you can start Elasticsearch for the first time with the command:

sudo service elasticsearch start
Please allow at least 10 seconds for Elasticsearch to fully start before you are able to use it. Otherwise, you may get errors about not being able to connect.
