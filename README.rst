========
DB_admin
========

MySQL_admin
-----------

Configurations::

    ./mysql/server.cnf    # usually in /etc/my.cnf.d/
    ./mysql/client.cnf    # usually in /etc/my.cnf.d/
    ./mysql/my.cnf        # usually in /etc/
    ./percona-xtradb-cluster-conf/wsrep.cnf_node1   # usually as /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    ./percona-xtradb-cluster-conf/wsrep.cnf_node2   # usually as /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    ./percona-xtradb-cluster-conf/wsrep.cnf_node3   # usually as /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    ./percona-xtradb-cluster-conf/wsrep.cnf_node4   # usually as /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
    ./percona-xtradb-cluster-conf/percona.repo      # for install PXC  (yum install Percona-XtraDB-Cluster-57 -y)
Readme for configurations::

    ./server.cnf.readme.rst
    ./percona-xtradb-cluster-conf/PXC_README.rst

Bash Shell Scripts::

    ./auto_install_mysql_v03.sh

