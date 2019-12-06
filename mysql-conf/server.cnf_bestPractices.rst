Best Practices for mysql
========================

Configure spec::

    #打开独立表空间
    innodb_file_per_table = 1

    #MySQL 服务所允许的同时会话数的上限，经常出现Too Many Connections的错误提示，则需要增大此值
    max_connections = 8000

    #所有线程所打开表的数量
    open_files_limit = 10240

    #back_log 是操作系统在监听队列中所能保持的连接数
    back_log = 300

    #每个客户端连接最大的错误允许数量，当超过该次数，MYSQL服务器将禁止此主机的连接请求，直到MYSQL
    服务器重启或通过flush hosts命令清空此主机的相关信息
    max_connect_errors = 1000

    #每个连接传输数据大小.最大1G，须是1024的倍数，一般设为最大的BLOB的值
    max_allowed_packet = 32M

    #指定一个请求的最大连接时间
    wait_timeout = 10

    # 排序缓冲被用来处理类似ORDER BY以及GROUP BY队列所引起的排序
    sort_buffer_size = 16M

    #不带索引的全表扫描.使用的buffer的最小值
    join_buffer_size = 16M

    #查询缓冲大小
    query_cache_size = 128M

    #指定单个查询能够使用的缓冲区大小，缺省为1M
    query_cache_limit = 4M    

    # 设定默认的事务隔离级别
    transaction_isolation = REPEATABLE-READ

    # 线程使用的堆大小. 此值限制内存中能处理的存储过程的递归深度和SQL语句复杂性，此容量的内存在每次连接时被预留.
    thread_stack = 512K

    # 二进制日志功能
    log-bin=/data/mysqlbinlogs/

    #二进制日志格式
    binlog_format=row

    #InnoDB使用一个缓冲池来保存索引和原始数据, 可设置这个变量到物理内存大小的80%
    innodb_buffer_pool_size = 24G

    #用来同步IO操作的IO线程的数量
    innodb_file_io_threads = 4

    #在InnoDb核心内的允许线程数量，建议的设置是CPU数量加上磁盘数量的两倍
    innodb_thread_concurrency = 16

    # 用来缓冲日志数据的缓冲区的大小
    innodb_log_buffer_size = 16M

    在日志组中每个日志文件的大小
    innodb_log_file_size = 512M

    # 在日志组中的文件总数
    innodb_log_files_in_group = 3

    # SQL语句在被回滚前,InnoDB事务等待InnoDB行锁的时间
    innodb_lock_wait_timeout = 120

    #慢查询时长
    long_query_time = 2

    #将没有使用索引的查询也记录下来
    log-queries-not-using-indexes
