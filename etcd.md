# etcd 
## 配置
```
[Unit]
Description=etcd key-value store
Documentation=https://github.com/etcd-io/etcd
After=network-online.target local-fs.target remote-fs.target time-sync.target
Wants=network-online.target local-fs.target remote-fs.target time-sync.target

[Service]
User=etcd
Type=notify
ExecStart=/usr/bin/etcd \
  --name infra3 \
  --initial-advertise-peer-urls http://114.132.228.135:2380 \
  --listen-peer-urls http://10.0.20.17:2380 \
  --listen-client-urls http://10.0.20.17:2379,http://127.0.0.1:2379 \
  --advertise-client-urls http://114.132.228.135:2379 \
  --initial-cluster-token etcd-cluster \
  --initial-cluster infra1=http://118.195.204.214:2380,infra2=http://159.75.15.177:2380,infra3=http://114.132.228.135:2380  \
  --initial-cluster-state existing \
  --data-dir=/var/lib/etcd
Restart=always
RestartSec=10s
LimitNOFILE=40000

[Install]
WantedBy=multi-user.target
```
* 如果etcdctl member list 找不到节点，要删除掉data-dir的内容。
* 腾讯云中 --listen-peer-urls --listen-client-urls ip要配置内网ip。