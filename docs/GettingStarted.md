# Getting started

### Prerequisite

* Linux box with
  * We tested on Ubuntu 14.04 & 16.04
* Docker installed
* Kubernetes cluster running with CNI enabled
  * One easy way to bring up a cluster is to use [kubeadm](https://kubernetes.io/docs/getting-started-guides/kubeadm/): 
      * We tested on Kubernetes 1.5, 1.6, 1.7
      ```
      $ kubeadm init --use-kubernetes-version=v1.7.0 --pod-network-cidr=10.244.0.0/16
      $ mkdir -p $HOME/.kube
      $ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
      $ sudo chown $(id -u):$(id -g) $HOME/.kube/config
      ```
      * To schedule pods on the master, e.g. for a single-machine Kubernetes cluster, run:
      ```
      $ kubectl taint nodes --all dedicated-
      ```
* One (or more) CNI plugin(s) installed, e.g., Canal, Weave, Flannel
  * Use this [link](https://github.com/projectcalico/canal/tree/master/k8s-install) to install Canal       
  * Use this [link](https://www.weave.works/docs/net/latest/kube-addon/) to install Weave      
  * Use this [link](https://github.com/coreos/flannel/blob/master/Documentation/kube-flannel.yml) to install Flannel

### Installing genie

We install genie as a Docker Container on every node
```
$ kubectl apply -f https://raw.githubusercontent.com/Huawei-PaaS/CNI-Genie/master/conf/1.5/genie.yaml
```

### Making changes to and build from source

Note that you should install genie first before making changes to the source. This ensures genie conf file is generated successfully.

After making changes to source, build genie binary by running:
```
$ make all
```
Place "genie" binary from dest/ into /opt/cni/bin/ directory.
```
$ cp dist/genie /opt/cni/bin/genie
```

### Test process

To run ginkgo tests for CNI-Genie run the following command:

If Kubernetes cluster is 1.7+
```
$ make test testKubeVersion=1.7 testKubeConfig=/root/admin.conf
```

If Kubernetes cluster is 1.5.x
```
$ make test testKubeVersion=1.5
```

### Genie Logs

For now Genie logs are stored in /var/log/syslog
To see the logs:
```
$ cat /dev/null > /var/log/syslog

$ tail -f /var/log/syslog | grep 'CNI'
```

### Troubleshooting

* Note: one a single node cluster, after your Kubernetes master is initialized successfully, make sure you are able to schedule pods on the master by running:
```
$ kubectl taint nodes --all dedicated-
```
* Note: most plugins use differenet installation files for Kuberenetes 1.5, 1.6 & 1.7. Make sure you use the right one!

