### Provision Redis Cluster using Ansible and Vagrant

If you want to provision a test redis cluster for yourself, I recently created an automated solution using [Vagrant](https://www.vagrantup.com/) and [Ansible](https://www.ansible.com/)

Git clone the repository [madhur/redis-cluster-vagrant](https://github.com/madhur/redis-cluster-vagrant) and use `vagrant up` command to bring up the six Centos 7 nodes.

Once the nodes are up, the ansible playbook `setup-redis.yml` can be executed which will install redis on all the five nodes.

```
ansible-playbook playbooks/setup-redis.yml
```

The ansible playbook provisions single redis process on each node.

Once the redis is setup, the execute the below command by sshing into any of the nodes (ex `vagrant ssh redis1`) to form the cluster

```
redis-cli --cluster create 192.168.56.10:6379 192.168.56.11:6379 192.168.56.12:6379 192.168.56.13:6379 192.168.56.14:6379 192.168.56.15:6379  --cluster-replicas 1
```

Cluster info and nodes output

```
[vagrant@redis1 ~]$ redis-cli
127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:288
cluster_stats_messages_pong_sent:289
cluster_stats_messages_sent:577
cluster_stats_messages_ping_received:284
cluster_stats_messages_pong_received:288
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:577
total_cluster_links_buffer_limit_exceeded:0

127.0.0.1:6379> cluster nodes
c9df2f9d7ad73f3b1ffde9a04f23c667c278624e 192.168.56.10:6379@16379 myself,master - 0 1669573945000 1 connected 0-5460
d56ed3bf5826ccd6f1c9d8ead3065e1d03f86956 192.168.56.13:6379@16379 slave 034e598487d2e8fd820db70566ab0f3b6eae287c 0 1669573946998 3 connected
9296809ef66c4610333a01b468e5f84b2624a8c2 192.168.56.11:6379@16379 master - 0 1669573947000 2 connected 5461-10922
36499eb6b0643de87d41b2efbb96ea53ece0f8e2 192.168.56.15:6379@16379 slave 9296809ef66c4610333a01b468e5f84b2624a8c2 0 1669573946000 2 connected
034e598487d2e8fd820db70566ab0f3b6eae287c 192.168.56.12:6379@16379 master - 0 1669573945995 3 connected 10923-16383
8c65509a5376b188f6dc5ce2b617d4f9a21e14a1 192.168.56.14:6379@16379 slave c9df2f9d7ad73f3b1ffde9a04f23c667c278624e 0 1669573946596 1 connected
```

