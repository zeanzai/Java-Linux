
# 集群的安装

- 创建文件夹

```
mkdir /opt/package
mkdir -p /usr/setup/redis-cluster/confs /usr/setup/redis-cluster/logs /usr/setup/redis-cluster/pids
```

- 安装gcc

```
yum install -y gcc
```

- 解压

```
tar zxf /opt/package/redis-5.0.3.tar.gz -C /opt/package/unziped
```

- make

```
make
```

- make install

```
make install PREFIX=/usr/setup/redis-cluster

在/usr/setup/redis-cluster目录下生成bin文件夹
```

- 拷贝配置文件

```
cp /opt/package/unziped/redis-5.0.3/redis.conf /usr/setup/redis-cluster/confs/6379.conf
```

- 修改配置文件

```
vi /usr/setup/redis-cluster/confs/6379.conf

bind 192.168.100.201
protected-mode no #非保护模式
port 6379
daemonize yes
pidfile /usr/setup/redis-cluster/pids/6379.pid
logfile "/usr/setup/redis-cluster/logs/6379.log"
appendonly yes
cluster-enabled yes #启用集群模式
cluster-config-file /usr/setup/redis-cluster/confs/nodes-6379.conf
cluster-node-timeout 5000 #超时时间
```

- 复制集群

```
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6380.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6381.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6382.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6383.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6384.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6385.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6386.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6387.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6388.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6389.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6390.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6391.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6392.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6393.conf
cp -r /usr/setup/redis-cluster/confs/6379.conf /usr/setup/redis-cluster/confs/6394.conf
```

- 修改各节点配置文件

```

port 6380
pidfile /usr/setup/redis-cluster/pids/6380.pid
logfile "/usr/setup/redis-cluster/logs/6380.log"
cluster-config-file /usr/setup/redis-cluster/confs/nodes-6380.conf

find . -name '63*' | cat 63* | grep -a0b0 'port 63*'
find . -name '63*' | cat 63* | grep -a0b0 'pidfile*'
find . -name '63*' | cat 63* | grep -a0b0 'logfile*'
find . -name '63*' | cat 63* | grep -a0b0 'cluster-config-file*'
```

- 创建集群启动脚本

```
vi /usr/setup/redis-cluster/startall.sh

cd /usr/setup/redis-cluster/bin
./redis-server /usr/setup/redis-cluster/confs/6379.conf
./redis-server /usr/setup/redis-cluster/confs/6380.conf
./redis-server /usr/setup/redis-cluster/confs/6381.conf
./redis-server /usr/setup/redis-cluster/confs/6382.conf
./redis-server /usr/setup/redis-cluster/confs/6383.conf
./redis-server /usr/setup/redis-cluster/confs/6384.conf
./redis-server /usr/setup/redis-cluster/confs/6385.conf
./redis-server /usr/setup/redis-cluster/confs/6386.conf
./redis-server /usr/setup/redis-cluster/confs/6387.conf
./redis-server /usr/setup/redis-cluster/confs/6388.conf
./redis-server /usr/setup/redis-cluster/confs/6389.conf
./redis-server /usr/setup/redis-cluster/confs/6390.conf
./redis-server /usr/setup/redis-cluster/confs/6391.conf
./redis-server /usr/setup/redis-cluster/confs/6392.conf
./redis-server /usr/setup/redis-cluster/confs/6393.conf
./redis-server /usr/setup/redis-cluster/confs/6394.conf

chmod +x /usr/setup/redis-cluster/startall.sh

```

- 启动所有节点

```
sh /usr/setup/redis-cluster/startall.sh
ps aux | grep redis
```

- 创建集群

```
cd /usr/setup/redis-cluster/bin/
./redis-cli --cluster create --cluster-replicas 3 \
192.168.100.201:6379 \
192.168.100.201:6380 \
192.168.100.201:6381 \
192.168.100.201:6382 \
192.168.100.201:6383 \
192.168.100.201:6384 \
192.168.100.201:6385 \
192.168.100.201:6386 \
192.168.100.201:6387 \
192.168.100.201:6388 \
192.168.100.201:6389 \
192.168.100.201:6390 \
192.168.100.201:6391 \
192.168.100.201:6392 \
192.168.100.201:6393 \
192.168.100.201:6394

>>> Performing hash slots allocation on 16 nodes...
Master[0] -> Slots 0 - 4095
Master[1] -> Slots 4096 - 8191
Master[2] -> Slots 8192 - 12287
Master[3] -> Slots 12288 - 16383
Adding replica 192.168.100.201:6383 to 192.168.100.201:6379
Adding replica 192.168.100.201:6384 to 192.168.100.201:6379
Adding replica 192.168.100.201:6385 to 192.168.100.201:6379
Adding replica 192.168.100.201:6386 to 192.168.100.201:6380
Adding replica 192.168.100.201:6387 to 192.168.100.201:6380
Adding replica 192.168.100.201:6388 to 192.168.100.201:6380
Adding replica 192.168.100.201:6389 to 192.168.100.201:6381
Adding replica 192.168.100.201:6390 to 192.168.100.201:6381
Adding replica 192.168.100.201:6391 to 192.168.100.201:6381
Adding replica 192.168.100.201:6392 to 192.168.100.201:6382
Adding replica 192.168.100.201:6393 to 192.168.100.201:6382
Adding replica 192.168.100.201:6394 to 192.168.100.201:6382
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: c7e78c93dedb1284003a0709c87a8ba4d9b6679b 192.168.100.201:6379
   slots:[0-4095] (4096 slots) master
M: 17633c5abd228d07bf9a606d2a5c9add63a2af95 192.168.100.201:6380
   slots:[4096-8191] (4096 slots) master
M: 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1 192.168.100.201:6381
   slots:[8192-12287] (4096 slots) master
M: cbbdbc7515f91fe9cf937f3564ed2df71d87c960 192.168.100.201:6382
   slots:[12288-16383] (4096 slots) master
S: 8cffa3aad27a22f188f6806e869b282861c83bbe 192.168.100.201:6383
   replicates 17633c5abd228d07bf9a606d2a5c9add63a2af95
S: 8c7e2c830962f6137e55640769d9c8b2d81c37db 192.168.100.201:6384
   replicates 17633c5abd228d07bf9a606d2a5c9add63a2af95
S: fb62ed7f6b9b1f9146b3ddaaf4d576142b92bfbe 192.168.100.201:6385
   replicates 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1
S: 225fe75247ca4e8a24c7ca4eeaa06dad24a96ab3 192.168.100.201:6386
   replicates c7e78c93dedb1284003a0709c87a8ba4d9b6679b
S: ec1fce90e082d070d9ecb99ca5ea7ce2cef64320 192.168.100.201:6387
   replicates 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1
S: 403f0341c92dfd033281ad413592e9b714eb0991 192.168.100.201:6388
   replicates cbbdbc7515f91fe9cf937f3564ed2df71d87c960
S: 5943d2b52106cf5ad9b219d485abfc429c2d8221 192.168.100.201:6389
   replicates c7e78c93dedb1284003a0709c87a8ba4d9b6679b
S: 64bb41b59180150abfff06db4d02fd7eeb13d87e 192.168.100.201:6390
   replicates 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1
S: cf7bad137d214bc0258f0766af28f4b75ad11387 192.168.100.201:6391
   replicates cbbdbc7515f91fe9cf937f3564ed2df71d87c960
S: 4c230dc3566baf84939a907e9c245a1346340194 192.168.100.201:6392
   replicates c7e78c93dedb1284003a0709c87a8ba4d9b6679b
S: 6bb555c3282fed3688164c9f4ed576c285e1349b 192.168.100.201:6393
   replicates 17633c5abd228d07bf9a606d2a5c9add63a2af95
S: a859cdbebe053ce34856971cb80952e9714d4f46 192.168.100.201:6394
   replicates cbbdbc7515f91fe9cf937f3564ed2df71d87c960
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
.....
>>> Performing Cluster Check (using node 192.168.100.201:6379)
M: c7e78c93dedb1284003a0709c87a8ba4d9b6679b 192.168.100.201:6379
   slots:[0-4095] (4096 slots) master
   3 additional replica(s)
S: 403f0341c92dfd033281ad413592e9b714eb0991 192.168.100.201:6388
   slots: (0 slots) slave
   replicates cbbdbc7515f91fe9cf937f3564ed2df71d87c960
S: 5943d2b52106cf5ad9b219d485abfc429c2d8221 192.168.100.201:6389
   slots: (0 slots) slave
   replicates c7e78c93dedb1284003a0709c87a8ba4d9b6679b
M: 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1 192.168.100.201:6381
   slots:[8192-12287] (4096 slots) master
   3 additional replica(s)
S: cf7bad137d214bc0258f0766af28f4b75ad11387 192.168.100.201:6391
   slots: (0 slots) slave
   replicates cbbdbc7515f91fe9cf937f3564ed2df71d87c960
S: 6bb555c3282fed3688164c9f4ed576c285e1349b 192.168.100.201:6393
   slots: (0 slots) slave
   replicates 17633c5abd228d07bf9a606d2a5c9add63a2af95
S: ec1fce90e082d070d9ecb99ca5ea7ce2cef64320 192.168.100.201:6387
   slots: (0 slots) slave
   replicates 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1
S: 8c7e2c830962f6137e55640769d9c8b2d81c37db 192.168.100.201:6384
   slots: (0 slots) slave
   replicates 17633c5abd228d07bf9a606d2a5c9add63a2af95
S: 8cffa3aad27a22f188f6806e869b282861c83bbe 192.168.100.201:6383
   slots: (0 slots) slave
   replicates 17633c5abd228d07bf9a606d2a5c9add63a2af95
M: cbbdbc7515f91fe9cf937f3564ed2df71d87c960 192.168.100.201:6382
   slots:[12288-16383] (4096 slots) master
   3 additional replica(s)
M: 17633c5abd228d07bf9a606d2a5c9add63a2af95 192.168.100.201:6380
   slots:[4096-8191] (4096 slots) master
   3 additional replica(s)
S: fb62ed7f6b9b1f9146b3ddaaf4d576142b92bfbe 192.168.100.201:6385
   slots: (0 slots) slave
   replicates 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1
S: 4c230dc3566baf84939a907e9c245a1346340194 192.168.100.201:6392
   slots: (0 slots) slave
   replicates c7e78c93dedb1284003a0709c87a8ba4d9b6679b
S: 64bb41b59180150abfff06db4d02fd7eeb13d87e 192.168.100.201:6390
   slots: (0 slots) slave
   replicates 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1
S: a859cdbebe053ce34856971cb80952e9714d4f46 192.168.100.201:6394
   slots: (0 slots) slave
   replicates cbbdbc7515f91fe9cf937f3564ed2df71d87c960
S: 225fe75247ca4e8a24c7ca4eeaa06dad24a96ab3 192.168.100.201:6386
   slots: (0 slots) slave
   replicates c7e78c93dedb1284003a0709c87a8ba4d9b6679b
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```


- 创建集群关闭脚本

```
vi /usr/setup/redis-cluster/shutdownall.sh

cd /usr/setup/redis-cluster/bin
./redis-cli -c -h 192.168.100.201 -p 6379 shutdown
./redis-cli -c -h 192.168.100.201 -p 6380 shutdown
./redis-cli -c -h 192.168.100.201 -p 6381 shutdown
./redis-cli -c -h 192.168.100.201 -p 6382 shutdown
./redis-cli -c -h 192.168.100.201 -p 6383 shutdown
./redis-cli -c -h 192.168.100.201 -p 6384 shutdown
./redis-cli -c -h 192.168.100.201 -p 6385 shutdown
./redis-cli -c -h 192.168.100.201 -p 6386 shutdown
./redis-cli -c -h 192.168.100.201 -p 6387 shutdown
./redis-cli -c -h 192.168.100.201 -p 6388 shutdown
./redis-cli -c -h 192.168.100.201 -p 6389 shutdown
./redis-cli -c -h 192.168.100.201 -p 6390 shutdown
./redis-cli -c -h 192.168.100.201 -p 6391 shutdown
./redis-cli -c -h 192.168.100.201 -p 6392 shutdown
./redis-cli -c -h 192.168.100.201 -p 6393 shutdown
./redis-cli -c -h 192.168.100.201 -p 6394 shutdown

chmod +x shutdownall.sh
```

- 测试

```
 cd /usr/setup/redis-cluster/bin && ./redis-cli -c -h 192.168.100.201
 192.168.100.201:6379> set a 123
-> Redirected to slot [15495] located at 192.168.100.201:6382
OK
192.168.100.201:6382> set b 456
-> Redirected to slot [3300] located at 192.168.100.201:6379
OK
192.168.100.201:6379> set c 789
-> Redirected to slot [7365] located at 192.168.100.201:6380
OK
192.168.100.201:6380> set d 987
-> Redirected to slot [11298] located at 192.168.100.201:6381
OK
192.168.100.201:6381> keys *
1) "d"
192.168.100.201:6381> get a
-> Redirected to slot [15495] located at 192.168.100.201:6382
"123"
192.168.100.201:6382> get b
-> Redirected to slot [3300] located at 192.168.100.201:6379
"456"
192.168.100.201:6379> get c
-> Redirected to slot [7365] located at 192.168.100.201:6380
"789"
192.168.100.201:6380> get d
-> Redirected to slot [11298] located at 192.168.100.201:6381
"987"
192.168.100.201:6381>

cd /usr/setup/redis-cluster/bin && ./redis-cli -c -h 192.168.100.201 cluster nodes
403f0341c92dfd033281ad413592e9b714eb0991 192.168.100.201:6388@16388 slave cbbdbc7515f91fe9cf937f3564ed2df71d87c960 0 1569499890635 10 connected
225fe75247ca4e8a24c7ca4eeaa06dad24a96ab3 192.168.100.201:6386@16386 slave c7e78c93dedb1284003a0709c87a8ba4d9b6679b 0 1569499891039 8 connected
cbbdbc7515f91fe9cf937f3564ed2df71d87c960 192.168.100.201:6382@16382 master - 0 1569499891543 4 connected 12288-16383
fb62ed7f6b9b1f9146b3ddaaf4d576142b92bfbe 192.168.100.201:6385@16385 slave 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1 0 1569499890000 7 connected
c7e78c93dedb1284003a0709c87a8ba4d9b6679b 192.168.100.201:6379@16379 myself,master - 0 1569499891000 1 connected 0-4095
6bb555c3282fed3688164c9f4ed576c285e1349b 192.168.100.201:6393@16393 slave 17633c5abd228d07bf9a606d2a5c9add63a2af95 0 1569499890331 15 connected
64bb41b59180150abfff06db4d02fd7eeb13d87e 192.168.100.201:6390@16390 slave 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1 0 1569499891543 12 connected
7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1 192.168.100.201:6381@16381 master - 0 1569499891000 3 connected 8192-12287
a859cdbebe053ce34856971cb80952e9714d4f46 192.168.100.201:6394@16394 slave cbbdbc7515f91fe9cf937f3564ed2df71d87c960 0 1569499890635 16 connected
cf7bad137d214bc0258f0766af28f4b75ad11387 192.168.100.201:6391@16391 slave cbbdbc7515f91fe9cf937f3564ed2df71d87c960 0 1569499890535 13 connected
5943d2b52106cf5ad9b219d485abfc429c2d8221 192.168.100.201:6389@16389 slave c7e78c93dedb1284003a0709c87a8ba4d9b6679b 0 1569499890000 11 connected
ec1fce90e082d070d9ecb99ca5ea7ce2cef64320 192.168.100.201:6387@16387 slave 7dbfd5fd5fa26e32701aa4b1ef69f405cc20b5f1 0 1569499890000 9 connected
4c230dc3566baf84939a907e9c245a1346340194 192.168.100.201:6392@16392 slave c7e78c93dedb1284003a0709c87a8ba4d9b6679b 0 1569499890129 14 connected
8cffa3aad27a22f188f6806e869b282861c83bbe 192.168.100.201:6383@16383 slave 17633c5abd228d07bf9a606d2a5c9add63a2af95 0 1569499891340 5 connected
8c7e2c830962f6137e55640769d9c8b2d81c37db 192.168.100.201:6384@16384 slave 17633c5abd228d07bf9a606d2a5c9add63a2af95 0 1569499891000 6 connected
17633c5abd228d07bf9a606d2a5c9add63a2af95 192.168.100.201:6380@16380 master - 0 1569499890000 2 connected 4096-8191
```
