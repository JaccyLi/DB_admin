From official documentation
===========================

General Configure::

    skip_name_resolve=0|1    # If set to 1 (0 is the default), only IP addresses are used for connections.
        Host names are not resolved.All host values in the GRANT tables must be IP addresses (or localhost).
    innodb_flush_log_at_trx_commit=0|1|2|3   # Set to 1, along with sync_binlog=1 for the greatest level of fault tolerance.

For the greatest possible durability and consistency in a replication setup that uses InnoDB with transactions,
in /etc/my.cnf.d/use these settings::

    sync_binlog=1
    innodb_flush_log_at_trx_commit=1

Specify the error log file path and specify the verbosity of error_log::

    log_error=/PATH/TO/LOG_ERROR_FILE
    log_warnings=1|2    # don't recommand to 0

Whether the general query log is enabled::

    general_log=0|1     # 0 for off, 1 for on
    general_log_file=/PATH/TO/CUSTPATH      # default is host_name.log eg:centos8.log

Whether the slow query log is enabled, 'Slow' is determined by the value of the ``long_query_time`` variable::

    slow_query_log=ON|OFF
    slow_query_log_file=/PATH/TO/CUSTPATH   # default is host_name.log eg:centos8-slow.log
    long_query_time=10   # default 10,  resolution can be microseconds.
        The minimum and default values of long_query_time are 0 and 10
        If a query takes longer than this many seconds, the server increments
        the Slow_queries status variable. If the slow query log is enabled,
        the query is logged to the slow query log file.
    long_query_time=N   #慢查询的阀值，单位秒
    slow_query_log_file=HOSTNAME-slow.log #慢查询日志文件
    log_slow_filter = admin,filesort,filesort_on_disk,full_join,full_scan,
    query_cache,query_cache_miss,tmp_table,tmp_table_on_disk
    #上述查询类型且查询时长超过long_query_time，则记录日志
    log_queries_not_using_indexes=ON #不使用索引或使用全索引扫描，不论是否达到慢查询阀值的
    语句是否记录日志，默认OFF，即不记录
    log_slow_rate_limit = 1 #多少次查询才记录，mariadb特有
    log_slow_verbosity= Query_plan,explain #记录内容
    log_slow_queries = OFF 同slow_query_log #新版已废弃

Profiling the query::

    set profiling = ON  # 查看SQL语句的底层资源使用情况
    show profiles;      # 查看语句,注意结果中的query_id值
    Show profile for query #;     # 显示语句的详细执行步骤和时长
    Show profile cpu for query #; # 显示cpu使用情况

Sets the binary logging format::

    binlog_format=STATEMENT|ROW|MIXED

    related variables:
    sql_log_bin=ON|OFF：#是否记录二进制日志，默认ON
    log_bin=/PATH/BIN_LOG_FILE：#指定文件位置；默认OFF，表示不启用二进制日志功能，上述两项都开启才可
    binlog_format=STATEMENT|ROW|MIXED：#二进制日志记录的格式，默认STATEMENT
    max_binlog_size=1073741824：#单个二进制日志文件的最大体积，到达最大值会自动滚动，默认为1G
    #说明：文件达到上限时的大小未必为指定的精确值
    sync_binlog=1|0：#设定是否启动二进制日志即时同步磁盘功能，默认0，由操作系统负责同步日志到磁盘
    expire_logs_days=N：#二进制日志可以自动删除的天数。 默认为0，即不自动删除

Transaction log configure::

    innodb_log_file_size   50331648     每个日志文件大小
    innodb_log_files_in_group  2        日志组成员个数
    innodb_log_group_home_dir  ./       事务文件路径
    innodb_flush_log_at_trx_commit      默认为1

Binary log configration::

    sql_log_bin=ON|OFF：        #是否记录二进制日志，默认ON
    log_bin=/PATH/BIN_LOG_FILE：#指定文件位置；默认OFF，表示不启用二进制日志功能，上述两项都开启才可
    binlog_format=STATEMENT|ROW|MIXED：#二进制日志记录的格式，默认STATEMENT
    max_binlog_size=1073741824：       #单个二进制日志文件的最大体积，到达最大值会自动滚动，默认为1G
                                       #说明：文件达到上限时的大小未必为指定的精确值
    sync_binlog=1|0：           #设定是否启动二进制日志即时同步磁盘功能，默认0，由操作系统负责同步日志到磁盘
    expire_logs_days=N：        #二进制日志可以自动删除的天数。 默认为0，即不自动删除

Profiling the binary log::

    SHOW {BINARY | MASTER} LOGS  
    SHOW MASTER STATUS
    SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos] [LIMIT [offset,] row_count]
    eg:show binlog events in 'mysql-bin.000001' from 6516 limit 2,3

Purge binlog::

    PURGE { BINARY | MASTER } LOGS  { TO 'log_name' | BEFORE datetime_expr }
    eg:
        PURGE BINARY LOGS TO 'mariadb-bin.000003'; # 删除mariadb-bin.000003之前的日志
        PURGE BINARY LOGS BEFORE '2017-01-23';
        PURGE BINARY LOGS BEFORE '2017-03-22 09:25:30';
    RESET MASTER [TO #]; # 删除所有二进制日志文件，并重新生成日志文件，文件名从#开始记数，默认从1开始，一般是master主机第一次启动时执行，MariaDB10.1.6开始支持TO #
    FLUSH LOGS;          # 切换二进制日志

TOOL:mysqlbinlog::

    mysqlbinlog --start-position=678 --stop-position=752 /var/lib/mysql/mariadb-bin.000003 -v
    mysqlbinlog  --start-datetime="2018-01-30 20:30:10"   --stop-datetime="2018-01-30 20:35:22" mariadb-bin.000003 -vvv


MySQL-replication
-----------------

Server::

    bin-log
    server-id=#
        1 to 4294967295 (>= MariaDB 10.2.2)，默认值为1
        0 to 4294967295 (<= MariaDB 10.2.1)，默认值为0，如果从节点为0，所有master都将拒绝此slave的连接
    sync_binlog=1 每次写后立即同步二进制日志到磁盘，性能差
    # 如果用到的为InnoDB存储引擎：建议如下
        innodb_flush_log_at_trx_commit=1    #每次事务提交立即同步日志写磁盘
        innodb_support_xa=ON        #分布式事务MariaDB10.3.0废除
        sync_master_info=#          #次事件后master.info同步到磁盘

    master<---->master
        auto_increment_offset=1         #开始点
        auto_increment_increment=2      #增长幅度

    semi-replication
        #主服务器配置:
        INSTALL PLUGIN rpl_semi_sync_master SONAME  'semisync_master.so';
        UNINSTALL PLUGIN rpl_semi_sync_master ;
        SHOW PLUGINS; #查看插件
        SET GLOBAL rpl_semi_sync_master_enabled=1;
        SET GLOBAL rpl_semi_sync_master_timeout = 1000;  #超时长1s,默认值为10s
        SHOW GLOBAL VARIABLES LIKE '%semi%';
        SHOW GLOBAL STATUS LIKE '%semi%';

    semi-replication----mariadb-10.3版以后
        #主服务器配置:
        [mysqld]
        plugin_load_add = semisync_master
        #从服务器配置:
        [mysqld]
        plugin_load_add = semisync_slave

    replication filter
        binlog-do-db =  #数据库白名单列表，多个数据库需多行实现
        binlog-ignore-db =  #数据库黑名单列表

    GTID
        gtid_mode=ON                   #gtid模式
        enforce_gtid_consistency    #保证GTID安全的参数

Relay server::

    log_bin
    log_slave_updates

Slave::

    server-id=#
    read_only=ON
    relay_log=relay-log
    relay_log_file=relay-log.index
    sync_relay_log=#        #次写后同步relay log到磁盘
    sync_relay_log_info=#   #次事务后同步relay-log.info到磁盘

    master<---->master
        auto_increment_offset=2         #开始点
        auto_increment_increment=2      #增长幅度

    semi-replica
        #从服务器配置:
        INSTALL PLUGIN rpl_semi_sync_slave SONAME 'semisync_slave.so';
        SET GLOBAL rpl_semi_sync_slave_enabled=1;

    replication filter
        replicate_do_db=                #指定复制库的白名单
        replicate_ignore_db=            #指定复制库黑名单
        replicate_do_table=             #指定复制表的白名单
        replicate_ignore_table=         #指定复制表的黑名单
        replicate_wild_do_table= foo%.bar%    #支持通配符
        replicate_wild_ignore_table=

    GTID
        gtid_mode=ON                   #gtid模式
        enforce_gtid_consistency    #保证GTID安全的参数

        mysql>CHANGE MASTER TO  MASTER_HOST='192.168.110.112',
          MASTER_USER='repluser',
          MASTER_PASSWORD='passwd',
          MASTER_PORT=3306,
          MASTER_AUTO_POSITION=1;  ## Enable GTID

Slave command::

    CHANGE MASTER TO MASTER_HOST='host',
    MASTER_USER='repluser',
    MASTER_PASSWORD='replpass',
    MASTER_LOG_FILE='mariadb-bin.xxxxxx',
    MASTER_LOG_POS=#;

    START SLAVE [IO_THREAD|SQL_THREAD];
    SHOW SLAVE STATUS;

    RESET SLAVE #从服务器清除master.info ，relay-log.info, relay log ，开始新的relay log
    RESET SLAVE  ALL #清除所有从服务器上设置的主服务器同步信息，如PORT, HOST, USER和PASSWORD 等

Slave replica error handle::

    MariaDB [(none)]> stop slave;
    MariaDB [(none)]> set global sql_slave_skip_counter=1;
    MariaDB [(none)]> start slave;


Monitoring replication::

    SHOW MASTER STATUS
    SHOW BINARY LOGS
    SHOW BINLOG EVENTS
    SHOW SLAVE STATUS
    SHOW PROCESSLIST

MySQL replication with SSL
--------------------------

Server::

    [mysqld]
    log-bin
    server_id=1
    ssl
    ssl-ca=/etc/my.cnf.d/ssl/cacert.pem
    ssl-cert=/etc/my.cnf.d/ssl/master.crt
    ssl-key=/etc/my.cnf.d/ssl/master.key
    [root@centos8 ~]#chown -R mysql.mysql  /etc/my.cnf.d/ssl/

    GRANT REPLICATION SLAVE ON *.* TO  'repluser'@'192.168.110.%' IDENTIFIED BY 'passwd' REQUIRE SSL;

Slave::

    [root@centos8 ~]#mysql -urepluser -pmagedu -h192.168.110.112
                           --ssl-ca=/etc/my.cnf.d/ssl/cacert.pem
                           --ssl-cert=/etc/my.cnf.d/ssl/slave.crt
                           --ssl-key=/etc/my.cnf.d/ssl/slave.key
    MariaDB [(none)]>

    [root@centos8 ~]#chown -R mysql.mysql  /etc/my.cnf.d/ssl/
    mysql>
    CHANGE MASTER TO
    MASTER_HOST='MASTERIP',
    MASTER_USER='repluser',
    MASTER_PASSWORD='paswd',
    MASTER_LOG_FILE='mariadb-bin.000001',
    MASTER_LOG_POS=122,
    MASTER_SSL=1,
    MASTER_SSL_CA = '/etc/my.cnf.d/ssl/cacert.pem',
    MASTER_SSL_CERT = '/etc/my.cnf.d/ssl/slave.crt',
    MASTER_SSL_KEY = '/etc/my.cnf.d/ssl/slave.key';
