```
[client]
# password=88888888
# socket=/data/var/mysql/mysql.sock

[mysqld_safe]
pid-file=/data/var/mysql/mysqld.pid
log-error=/data/local/mysql-5.7.19/log/mysql-error.log

[mysql]
socket=/data/var/mysql/mysql.sock

[mysqld]
user = mysql
port = 31306
datadir = /data/var/mysql
socket=/data/var/mysql/mysql.sock
symbolic-links=0
########basic settings########
server-id = 11
#bind_address = 10.166.224.32
autocommit = 1
character_set_server=utf8mb4
skip_name_resolve = 1
max_connections = 800
max_connect_errors = 100
transaction_isolation = READ-COMMITTED
explicit_defaults_for_timestamp = 1
join_buffer_size = 128M
tmp_table_size = 128M
tmpdir = /dev/shm
max_allowed_packet = 16M
sql_mode = "STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER"
interactive_timeout = 60
wait_timeout = 60
read_buffer_size = 16M
read_rnd_buffer_size = 32M
sort_buffer_size = 32M
########log settings########
#log_error = /data/local/mysql-5.7.19/log/mysql-error.log
slow_query_log = 1
slow_query_log_file = /data/local/mysql-5.7.19/log/mysql-slow.log
log_queries_not_using_indexes = 1
log_slow_admin_statements = 1
log_slow_slave_statements = 1
log_throttle_queries_not_using_indexes = 10
expire_logs_days = 90
long_query_time = 1
min_examined_row_limit = 100
########replication settings########
#master_info_repository = TABLE
#relay_log_info_repository = TABLE
log_bin = /data/local/mysql-5.7.19/log/mysql-bin
#sync_binlog = 4
gtid_mode = on
enforce_gtid_consistency = 1
#log_slave_updates
binlog_format = row
#relay_log = /data/local/mysql-5.7.19/log/mysql-relay.log
#relay_log_recovery = 1
#binlog_gtid_simple_recovery = 1
#slave_skip_errors = ddl_exist_errors
########innodb settings########
innodb_page_size = 16K
innodb_buffer_pool_size = 4G
#innodb_buffer_pool_instances = 8
#innodb_buffer_pool_load_at_startup = 1
#innodb_buffer_pool_dump_at_shutdown = 1
#innodb_lru_scan_depth = 2000
innodb_lock_wait_timeout = 5
#innodb_io_capacity = 4000
#innodb_io_capacity_max = 8000
#innodb_flush_method = O_DIRECT
#innodb_log_group_home_dir = /data/local/mysql-5.7.19/log/redolog/
#innodb_undo_directory = /data/local/mysql-5.7.19/log/undolog/
#innodb_undo_logs = 128
#innodb_undo_tablespaces = 0
#innodb_flush_neighbors = 1
#innodb_log_file_size = 4G
#innodb_log_buffer_size = 16M
#innodb_purge_threads = 4
innodb_large_prefix = 1
innodb_thread_concurrency = 64
#innodb_print_all_deadlocks = 1
#innodb_strict_mode = 1
innodb_sort_buffer_size = 64M
########semi sync replication settings########
#plugin_dir=/data/local/mysql-5.7.19/lib/plugin
#plugin_load = "rpl_semi_sync_master=semisync_master.so;rpl_semi_sync_slave=semisync_slave.so"
#loose_rpl_semi_sync_master_enabled = 1
#loose_rpl_semi_sync_slave_enabled = 1
#loose_rpl_semi_sync_master_timeout = 5000

[mysqld-5.7]
#innodb_buffer_pool_dump_pct = 40
innodb_page_cleaners = 4
#innodb_undo_log_truncate = 1
#innodb_max_undo_log_size = 2G
#innodb_purge_rseg_truncate_frequency = 128
#binlog_gtid_simple_recovery=1
log_timestamps=system
#transaction_write_set_extraction=MURMUR32
#show_compatibility_56=on
```

## 200服务器配置
```

[client]
default-character-set=utf8

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
symbolic-links=0
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
lower_case_table_names=1
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
default-storage-engine=INNODB
character-set-server=utf8
collation-server=utf8_general_ci
max_connections=1000

[mysql]
no-auto-rehash
default-character-set=utf8
```
