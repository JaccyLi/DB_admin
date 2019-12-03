Percona XtraDB Cluster Configuration
====================================

Configure
---------

Host list::

    172.20.1.69 ---> node1
    172.20.1.68 ---> node2
    172.20.1.67 ---> node3
    172.20.1.79 ---> node4

Important options::

    wsrep_provider 指定Galera库路径
    wsrep_cluster_name  Galera集群的名称
    wsrep_cluster_address=gcomm://  Galera集群中各节点的地址。组通讯协议(Group communication)
    wsrep_node_name  本节点在Galera集群中的名称
    wsrep_node_address  本节点在Galera集群中的通信地址
    wsrep_sst_method  state_snapshot_transfer(SST)使用的传输方法，可用方法有
                      mysqldump、rsync和xtrabackup，前两者在传输时都需要对
                      Donor加全局只读锁(FLUSH TABLES WITH READ LOCK)，
                      xtrabackup则不需要(它使用percona自己提供的backup lock)。
                      强烈建议采用xtrabackup
    wsrep_sst_auth  在SST传输时需要用到的认证凭据，格式为："用户：密码"
    pxc_strict_mode  是否限制PXC启用正在试用阶段的功能，ENFORCING是默认值，表示不启用
    binlog_format  二进制日志的格式。Galera只支持row格式的二进制日志
    default_storage_engine  指定默认存储引擎。Galera的复制功能只支持InnoDB
    innodb_autoinc_lock_mode  只能设置为2，设置为0或1时会无法正确处理死锁问题

First node(node1)::

    /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    Configure content:
    [mysqld]
    # Path to Galera library
    wsrep_provider=/usr/lib64/galera3/libgalera_smm.so

    # Cluster connection URL contains IPs of nodes
    #If no IP is found, this implies that a new cluster needs to be created,
    #in order to do that you need to bootstrap this node
    wsrep_cluster_address=gcomm://172.20.1.69,172.20.1.68,172.20.1.67,172.20.1.79

    # In order for Galera to work correctly binlog format should be ROW
    binlog_format=ROW

    # MyISAM storage engine has only experimental support
    default_storage_engine=InnoDB

    # Slave thread to use
    wsrep_slave_threads= 8

    wsrep_log_conflicts

    # This changes how InnoDB autoincrement locks are managed and is a requirement for Galera
    innodb_autoinc_lock_mode=2

    # Node IP address
    wsrep_node_address=172.20.1.69
    # Cluster name
    wsrep_cluster_name=pxc-cluster

    #If wsrep_node_name is not specified,  then system hostname will be used
    wsrep_node_name=pxc-cluster-node-1

    #pxc_strict_mode allowed values: DISABLED,PERMISSIVE,ENFORCING,MASTER
    pxc_strict_mode=ENFORCING

    # SST method
    wsrep_sst_method=xtrabackup-v2

    #Authentication for SST method
    wsrep_sst_auth="sstuser:s3cretPass"


Second node(node2)::

    /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    Configure content:
    [mysqld]
    # Path to Galera library
    wsrep_provider=/usr/lib64/galera3/libgalera_smm.so

    # Cluster connection URL contains IPs of nodes
    #If no IP is found, this implies that a new cluster needs to be created,
    #in order to do that you need to bootstrap this node
    wsrep_cluster_address=gcomm://172.20.1.69,172.20.1.68,172.20.1.67,172.20.1.79

    # In order for Galera to work correctly binlog format should be ROW
    binlog_format=ROW

    # MyISAM storage engine has only experimental support
    default_storage_engine=InnoDB

    # Slave thread to use
    wsrep_slave_threads= 8

    wsrep_log_conflicts

    # This changes how InnoDB autoincrement locks are managed and is a requirement for Galera
    innodb_autoinc_lock_mode=2

    # Node IP address
    wsrep_node_address=172.20.1.68
    # Cluster name
    wsrep_cluster_name=pxc-cluster

    #If wsrep_node_name is not specified,  then system hostname will be used
    wsrep_node_name=pxc-cluster-node-2

    #pxc_strict_mode allowed values: DISABLED,PERMISSIVE,ENFORCING,MASTER
    pxc_strict_mode=ENFORCING

    # SST method
    wsrep_sst_method=xtrabackup-v2

    #Authentication for SST method
    wsrep_sst_auth="sstuser:s3cretPass"


Third node(node3)::

    /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    Configure content:
    [mysqld]
    # Path to Galera library
    wsrep_provider=/usr/lib64/galera3/libgalera_smm.so

    # Cluster connection URL contains IPs of nodes
    #If no IP is found, this implies that a new cluster needs to be created,
    #in order to do that you need to bootstrap this node
    wsrep_cluster_address=gcomm://172.20.1.69,172.20.1.68,172.20.1.67,172.20.1.79

    # In order for Galera to work correctly binlog format should be ROW
    binlog_format=ROW

    # MyISAM storage engine has only experimental support
    default_storage_engine=InnoDB

    # Slave thread to use
    wsrep_slave_threads= 8

    wsrep_log_conflicts

    # This changes how InnoDB autoincrement locks are managed and is a requirement for Galera
    innodb_autoinc_lock_mode=2

    # Node IP address
    wsrep_node_address=172.20.1.67
    # Cluster name
    wsrep_cluster_name=pxc-cluster

    #If wsrep_node_name is not specified,  then system hostname will be used
    wsrep_node_name=pxc-cluster-node-3

    #pxc_strict_mode allowed values: DISABLED,PERMISSIVE,ENFORCING,MASTER
    pxc_strict_mode=ENFORCING

    # SST method
    wsrep_sst_method=xtrabackup-v2

    #Authentication for SST method
    wsrep_sst_auth="sstuser:s3cretPass"


Fouth node(node4)::

    /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    Configure content:
    [mysqld]
    # Path to Galera library
    wsrep_provider=/usr/lib64/galera3/libgalera_smm.so

    # Cluster connection URL contains IPs of nodes
    #If no IP is found, this implies that a new cluster needs to be created,
    #in order to do that you need to bootstrap this node
    wsrep_cluster_address=gcomm://172.20.1.69,172.20.1.68,172.20.1.67,172.20.1.79

    # In order for Galera to work correctly binlog format should be ROW
    binlog_format=ROW

    # MyISAM storage engine has only experimental support
    default_storage_engine=InnoDB

    # Slave thread to use
    wsrep_slave_threads= 8

    wsrep_log_conflicts

    # This changes how InnoDB autoincrement locks are managed and is a requirement for Galera
    innodb_autoinc_lock_mode=2

    # Node IP address
    #wsrep_node_address=192.168.70.63
    # Cluster name
    wsrep_cluster_name=pxc-cluster

    #If wsrep_node_name is not specified,  then system hostname will be used
    wsrep_node_name=pxc-cluster-node-4

    #pxc_strict_mode allowed values: DISABLED,PERMISSIVE,ENFORCING,MASTER
    pxc_strict_mode=ENFORCING

    # SST method
    wsrep_sst_method=xtrabackup-v2

    #Authentication for SST method
    wsrep_sst_auth="sstuser:s3cretPass"



Related Command
---------------

Start No1 node(node1)::

    step1:
    [root@node1 ~]# grep "temporary password" /var/log/mysqld.log
    2019-12-02T14:20:36.077034Z 1 [Note] A temporary password is generated for root@localhost: hjPM1k.n%bij

    step2:
    ss -ntul
    systemctl start mysql@bootstrap.service
    ss -ntul

    step3:
    mysql -uroot -p'hjPM1k.n%bij'
    ALTER USER 'root'@'localhost' INDENTIFIED BY 'stevenux';
    CREATE USER 'sstuser'@'172.20.54.15' IDENTIFIED BY 's3cretPass';
    GRANT RELOADE, LOCK TABLES, PROCESS, REPLICATION CLIENT ON *.* TO 'sstuser'@'localhost';
    SHOW VARIABLES LIKE 'wsrep%'\G
    SHOW STATUS  LIKE 'wsrep%'\G
    wsrep_cluster_size  表示，该Galera集群中只有一个节点
    wsrep_local_state_comment  状态为Synced，表示数据已同步完成(新加入集群的主机)
                               如果状态是Joiner, 意味着 SST 没有完成. 只有所有节点状态是Synced，才可以加新节点
    wsrep_cluster_status | Primary 表示已经完全连接并准备好
        eg:
        mysql> SHOW STATUS LIKE '%wsrep_cluster%';
        +--------------------------+--------------------------------------+
        | Variable_name            | Value                                |
        +--------------------------+--------------------------------------+
        | wsrep_cluster_weight     | 4                                    |
        | wsrep_cluster_conf_id    | 4                                    |
        | wsrep_cluster_size       | 4                                    |
        | wsrep_cluster_state_uuid | bb07538d-1570-11ea-a0fd-b3f3ba857a8d |
        | wsrep_cluster_status     | Primary                              |
        +--------------------------+--------------------------------------+
        5 rows in set (0.00 sec)

Start the remaining node::

    ss -ntul
    systemctl start mysql
    ss -ntul


SST related::

    一个节点加入到Galera集群有两种情况：新节点加入集群、暂时离组的成员再次加入集群

    1）新节点加入Galera集群

    新节点加入集群时，需要从当前集群中选择一个Donor节点来同步数据，也就是所谓的
    state_snapshot_tranfer(SST)过程。SST同步数据的方式由选项wsrep_sst_method决定，
    一般选择的是xtrabackup。必须注意，新节点加入Galera时，会删除新节点上所有已有数据，
    再通过xtrabackup(假设使用的是该方式)从Donor处完整备份所有数据进行恢复。所以，如果
    数据量很大，新节点加入过程会很慢。而且，在一个新节点成为Synced状态之前，不要同时加入其它
    新节点，否则很容易将集群压垮。如果是这种情况，可以考虑使用wsrep_sst_method=rsync来做
    增量同步，既然是增量同步，最好保证新节点上已经有一部分数据基础，否则和全量同步没什么区别，
    且这样会对Donor节点加上全局read only锁。

    2）旧节点加入Galera集群

    如果旧节点加入Galera集群，说明这个节点在之前已经在Galera集群中呆过，有一部分数据基础，缺少
    的只是它离开集群时的数据。这时加入集群时，会采用IST(incremental snapshot transfer)
    传输机制，即使用增量传输。
    但注意，这部分增量传输的数据源是Donor上缓存在GCache文件中的，这个文件有大小限制，如果缺
    失的数据范围超过已缓存的内容，则自动转为SST传输。如果旧节点上的数据和Donor上的数据不匹配
    (例如这个节点离组后人为修改了一点数据)，则自动转为SST传输。
