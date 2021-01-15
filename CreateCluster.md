# Create a Cluster

## Evaluation Environment
- etcd 3.4.14
  - https://github.com/etcd-io/etcd/releases/tag/v3.4.14
- Oracle Linux 8.3 (4.18.0-240.10.1.el8_3.x86_64)
  ```
      +----------------------------+
  +---+ ol8-171 (192.168.1.171/24) |
  |   +----------------------------+
  |
  |   +----------------------------+
  +---+ ol8-172 (192.168.1.172/24) |
  |   +----------------------------+
  |
  |   +----------------------------+
  +---+ ol8-173 (192.168.1.173/24) |
      +----------------------------+
  ```
## Create a 3-node Cluster
- Run the following command on each server to create a cluster.
  - ol8-171
    ```sh
    TOKEN=token-01
    CLUSTER_STATE=new
    NAME_1=ol8-171
    NAME_2=ol8-172
    NAME_3=ol8-173
    HOST_1=192.168.1.171
    HOST_2=192.168.1.172
    HOST_3=192.168.1.173
    CLUSTER=${NAME_1}=http://${HOST_1}:2380,${NAME_2}=http://${HOST_2}:2380,${NAME_3}=http://${HOST_3}:2380
    ```
    ```sh
    THIS_NAME=${NAME_1}
    THIS_IP=${HOST_1}
    etcd --data-dir=data.etcd --name ${THIS_NAME} \
    --initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://${THIS_IP}:2380 \
    --advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://${THIS_IP}:2379 \
    --initial-cluster ${CLUSTER} \
    --initial-cluster-state ${CLUSTER_STATE} --initial-cluster-token ${TOKEN}
    ```
  - ol8-172
    ```sh
    TOKEN=token-01
    CLUSTER_STATE=new
    NAME_1=ol8-171
    NAME_2=ol8-172
    NAME_3=ol8-173
    HOST_1=192.168.1.171
    HOST_2=192.168.1.172
    HOST_3=192.168.1.173
    CLUSTER=${NAME_1}=http://${HOST_1}:2380,${NAME_2}=http://${HOST_2}:2380,${NAME_3}=http://${HOST_3}:2380
    ```
    ```sh
    THIS_NAME=${NAME_2}
    THIS_IP=${HOST_2}
    etcd --data-dir=data.etcd --name ${THIS_NAME} \
    --initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://${THIS_IP}:2380 \
    --advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://${THIS_IP}:2379 \
    --initial-cluster ${CLUSTER} \
    --initial-cluster-state ${CLUSTER_STATE} --initial-cluster-token ${TOKEN}
    ```
  - ol8-173
    ```sh
    TOKEN=token-01
    CLUSTER_STATE=new
    NAME_1=ol8-171
    NAME_2=ol8-172
    NAME_3=ol8-173
    HOST_1=192.168.1.171
    HOST_2=192.168.1.172
    HOST_3=192.168.1.173
    CLUSTER=${NAME_1}=http://${HOST_1}:2380,${NAME_2}=http://${HOST_2}:2380,${NAME_3}=http://${HOST_3}:2380
    ```
    ```sh
    THIS_NAME=${NAME_3}
    THIS_IP=${HOST_3}
    etcd --data-dir=data.etcd --name ${THIS_NAME} \
    --initial-advertise-peer-urls http://${THIS_IP}:2380 --listen-peer-urls http://${THIS_IP}:2380 \
    --advertise-client-urls http://${THIS_IP}:2379 --listen-client-urls http://${THIS_IP}:2379 \
    --initial-cluster ${CLUSTER} \
    --initial-cluster-state ${CLUSTER_STATE} --initial-cluster-token ${TOKEN}
    ```
- For more detail, refer to the official documentation.
  - https://etcd.io/docs/v3.4.0/demo/#set-up-a-cluster

## Check the Behaviour
- Check the node status
  ```sh
  export ETCDCTL_API=3
  HOST_1=192.168.1.171
  HOST_2=192.168.1.172
  HOST_3=192.168.1.173
  ENDPOINTS=$HOST_1:2379,$HOST_2:2379,$HOST_3:2379
  
  etcdctl --endpoints=$ENDPOINTS member list -w table
  +------------------+---------+---------+---------------------------+---------------------------+------------+
  |        ID        | STATUS  |  NAME   |        PEER ADDRS         |       CLIENT ADDRS        | IS LEARNER |
  +------------------+---------+---------+---------------------------+---------------------------+------------+
  | 7e260f3c606e264d | started | ol8-172 | http://192.168.1.172:2380 | http://192.168.1.172:2379 |      false |
  | d3a0a388abdb5c59 | started | ol8-171 | http://192.168.1.171:2380 | http://192.168.1.171:2379 |      false |
  | f79d55788095f3d1 | started | ol8-173 | http://192.168.1.173:2380 | http://192.168.1.173:2379 |      false |
  +------------------+---------+---------+---------------------------+---------------------------+------------+  
  ```
- Put the key and get it
  ```sh
  export ETCDCTL_API=3
  HOST_1=192.168.1.171
  HOST_2=192.168.1.172
  HOST_3=192.168.1.173
  ENDPOINTS=$HOST_1:2379,$HOST_2:2379,$HOST_3:2379
  
  etcdctl --endpoints=$ENDPOINTS put foo "Hello, etcd"
  etcdctl --endpoints=$ENDPOINTS get foo  
  OK
  foo
  Hello, etcd
  ```