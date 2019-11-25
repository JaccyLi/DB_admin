From official documentation
===========================

For the greatest possible durability and consistency in a replication setup that uses InnoDB with transactions,
in /etc/my.cnf.d/use these settings::

    sync_binlog=1.
    innodb_flush_log_at_trx_commit=1.

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
