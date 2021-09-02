# gpu-operator

# 1. Install Curl
```
$ sudo apt-get install curl
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following package was automatically installed and is no longer required:
  libllvm11
Use 'sudo apt autoremove' to remove it.
The following NEW packages will be installed:
  curl
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 161 kB of archives.
After this operation, 411 kB of additional disk space will be used.
Get:1 http://jp.archive.ubuntu.com/ubuntu focal-updates/main amd64 curl amd64 7.68.0-1ubuntu2.6 [161 kB]
Fetched 161 kB in 0s (344 kB/s)
Selecting previously unselected package curl.
(Reading database ... 194787 files and directories currently installed.)
Preparing to unpack .../curl_7.68.0-1ubuntu2.6_amd64.deb ...
Unpacking curl (7.68.0-1ubuntu2.6) ...
Setting up curl (7.68.0-1ubuntu2.6) ...
Processing triggers for man-db (2.9.1-1) ...
```

# 2. Install Docker-CE
```
$ curl https://get.docker.com | sh \
>   && sudo systemctl --now enable docker
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 18617  100 18617    0     0   101k      0 --:--:-- --:--:-- --:--:--  101k
# Executing docker install script, commit: 93d2499759296ac1f9c510605fef85052a2c32be
+ sudo -E sh -c apt-get update -qq >/dev/null
+ sudo -E sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq apt-transport-https ca-certificates curl >/dev/null
+ sudo -E sh -c curl -fsSL "https://download.docker.com/linux/ubuntu/gpg" | gpg --dearmor --yes -o /usr/share/keyrings/docker-archive-keyring.gpg
gpg: WARNING: unsafe ownership on homedir '/home/xxx/.gnupg'
+ sudo -E sh -c echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu focal stable" > /etc/apt/sources.list.d/docker.list
+ sudo -E sh -c apt-get update -qq >/dev/null
+ sudo -E sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq --no-install-recommends  docker-ce-cli docker-scan-plugin docker-ce >/dev/null
+ version_gte 20.10
+ [ -z  ]
+ return 0
+ sudo -E sh -c DEBIAN_FRONTEND=noninteractive apt-get install -y -qq docker-ce-rootless-extras >/dev/null
+ sudo -E sh -c docker version
Client: Docker Engine - Community
 Version:           20.10.8
 API version:       1.41
 Go version:        go1.16.6
 Git commit:        3967b7d
 Built:             Fri Jul 30 19:54:27 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.8
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.16.6
  Git commit:       75249d8
  Built:            Fri Jul 30 19:52:33 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.9
  GitCommit:        e25210fe30a0a703442421b0f60afac609f950a3
 runc:
  Version:          1.0.1
  GitCommit:        v1.0.1-0-g4144b63
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0

================================================================================

To run Docker as a non-privileged user, consider setting up the
Docker daemon in rootless mode for your user:

    dockerd-rootless-setuptool.sh install

Visit https://docs.docker.com/go/rootless/ to learn about rootless mode.


To run the Docker daemon as a fully privileged service, but granting non-root
users access, refer to https://docs.docker.com/go/daemon-access/

WARNING: Access to the remote API on a privileged Docker daemon is equivalent
         to root access on the host. Refer to the 'Docker daemon attack surface'
         documentation for details: https://docs.docker.com/go/attack-surface/

================================================================================

Synchronizing state of docker.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable docker

$ sudo docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

# 3. Install Nvidia Docker2
```
$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
>    && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \
>    && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
OK
deb https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/$(ARCH) /
#deb https://nvidia.github.io/libnvidia-container/experimental/ubuntu18.04/$(ARCH) /
deb https://nvidia.github.io/nvidia-container-runtime/stable/ubuntu18.04/$(ARCH) /
#deb https://nvidia.github.io/nvidia-container-runtime/experimental/ubuntu18.04/$(ARCH) /
deb https://nvidia.github.io/nvidia-docker/ubuntu18.04/$(ARCH) /

$ sudo apt-get update
Get:1 https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/amd64  InRelease [1,484 B]
Get:2 https://nvidia.github.io/nvidia-container-runtime/stable/ubuntu18.04/amd64  InRelease [1,481 B]
Get:3 https://nvidia.github.io/nvidia-docker/ubuntu18.04/amd64  InRelease [1,474 B]
Get:4 https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/amd64  Packages [11.6 kB]          
Hit:5 https://download.docker.com/linux/ubuntu focal InRelease                                           
Hit:6 http://jp.archive.ubuntu.com/ubuntu focal InRelease                                                
Hit:7 http://jp.archive.ubuntu.com/ubuntu focal-updates InRelease                                        
Hit:8 http://jp.archive.ubuntu.com/ubuntu focal-backports InRelease                              
Get:9 https://nvidia.github.io/nvidia-container-runtime/stable/ubuntu18.04/amd64  Packages [7,416 B]
Get:10 https://nvidia.github.io/nvidia-docker/ubuntu18.04/amd64  Packages [4,488 B]
Hit:11 http://security.ubuntu.com/ubuntu focal-security InRelease
Fetched 28.0 kB in 1s (24.8 kB/s)
Reading package lists... Done

$ sudo apt-get install -y nvidia-docker2
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following package was automatically installed and is no longer required:
  libllvm11
Use 'sudo apt autoremove' to remove it.
The following additional packages will be installed:
  libnvidia-container-tools libnvidia-container1 nvidia-container-runtime nvidia-container-toolkit
The following NEW packages will be installed:
  libnvidia-container-tools libnvidia-container1 nvidia-container-runtime nvidia-container-toolkit
  nvidia-docker2
0 upgraded, 5 newly installed, 0 to remove and 0 not upgraded.
Need to get 1,588 kB of archives.
After this operation, 4,853 kB of additional disk space will be used.
Get:1 https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/amd64  libnvidia-container1 1.4.0-1 [68.0 kB]
Get:2 https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/amd64  libnvidia-container-tools 1.4.0-1 [20.9 kB]
Get:3 https://nvidia.github.io/nvidia-container-runtime/stable/ubuntu18.04/amd64  nvidia-container-toolkit 1.5.1-1 [716 kB]
Get:4 https://nvidia.github.io/nvidia-container-runtime/stable/ubuntu18.04/amd64  nvidia-container-runtime 3.5.0-1 [777 kB]
Get:5 https://nvidia.github.io/nvidia-docker/ubuntu18.04/amd64  nvidia-docker2 2.6.0-1 [5,960 B]
Fetched 1,588 kB in 1s (2,411 kB/s)  
Selecting previously unselected package libnvidia-container1:amd64.
(Reading database ... 195039 files and directories currently installed.)
Preparing to unpack .../libnvidia-container1_1.4.0-1_amd64.deb ...
Unpacking libnvidia-container1:amd64 (1.4.0-1) ...
Selecting previously unselected package libnvidia-container-tools.
Preparing to unpack .../libnvidia-container-tools_1.4.0-1_amd64.deb ...
Unpacking libnvidia-container-tools (1.4.0-1) ...
Selecting previously unselected package nvidia-container-toolkit.
Preparing to unpack .../nvidia-container-toolkit_1.5.1-1_amd64.deb ...
Unpacking nvidia-container-toolkit (1.5.1-1) ...
Selecting previously unselected package nvidia-container-runtime.
Preparing to unpack .../nvidia-container-runtime_3.5.0-1_amd64.deb ...
Unpacking nvidia-container-runtime (3.5.0-1) ...
Selecting previously unselected package nvidia-docker2.
Preparing to unpack .../nvidia-docker2_2.6.0-1_all.deb ...
Unpacking nvidia-docker2 (2.6.0-1) ...
Setting up libnvidia-container1:amd64 (1.4.0-1) ...
Setting up libnvidia-container-tools (1.4.0-1) ...
Setting up nvidia-container-toolkit (1.5.1-1) ...
Setting up nvidia-container-runtime (3.5.0-1) ...
Setting up nvidia-docker2 (2.6.0-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...

$ sudo systemctl restart docker
```

# 4. Configuring about using systemd instead of cgroups.
This step is very important. If you skip this step then the kubelet does not run. 
Configure the Docker daemon, in particular to use systemd for the management of the container’s cgroups. 
See also https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker .
```
$ sudo mkdir /etc/docker
mkdir: cannot create directory ‘/etc/docker’: File exists
$ cat <<EOF | sudo tee /etc/docker/daemon.json
> {
>   "exec-opts": ["native.cgroupdriver=systemd"],
>   "log-driver": "json-file",
>   "log-opts": {
>     "max-size": "100m"
>   },
>   "storage-driver": "overlay2"
> }
> EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}


$ sudo systemctl enable docker
Synchronizing state of docker.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable docker

$ sudo systemctl daemon-reload

$ sudo systemctl restart docker

$ sudo docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

# 5. Install kubernetes
See als https://docs.nvidia.com/datacenter/cloud-native/kubernetes/install-k8s.html .

```
$ sudo apt-get update \
>    && sudo apt-get install -y apt-transport-https curl
Hit:1 https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/amd64  InRelease
Hit:2 https://nvidia.github.io/nvidia-container-runtime/stable/ubuntu18.04/amd64  InRelease
Hit:3 https://nvidia.github.io/nvidia-docker/ubuntu18.04/amd64  InRelease
Hit:4 https://download.docker.com/linux/ubuntu focal InRelease                                           
Hit:5 http://jp.archive.ubuntu.com/ubuntu focal InRelease                                         
Hit:6 http://jp.archive.ubuntu.com/ubuntu focal-updates InRelease          
Hit:7 http://jp.archive.ubuntu.com/ubuntu focal-backports InRelease        
Hit:8 http://security.ubuntu.com/ubuntu focal-security InRelease           
Reading package lists... Done
Reading package lists... Done
Building dependency tree       
Reading state information... Done
curl is already the newest version (7.68.0-1ubuntu2.6).
apt-transport-https is already the newest version (2.0.6).
The following package was automatically installed and is no longer required:
  libllvm11
Use 'sudo apt autoremove' to remove it.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.

$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
OK

$ cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
> deb https://apt.kubernetes.io/ kubernetes-xenial main
> EOF
deb https://apt.kubernetes.io/ kubernetes-xenial main

$ sudo apt-get update \
>    && sudo apt-get install -y -q kubelet kubectl kubeadm \
>    && sudo kubeadm init --pod-network-cidr=192.168.0.0/16^C


$ sudo apt-get update \
>    && sudo apt-get install -y -q kubelet kubectl kubeadm \
>    && sudo kubeadm init --pod-network-cidr=192.168.0.0/16
Hit:1 https://nvidia.github.io/libnvidia-container/stable/ubuntu18.04/amd64  InRelease
Hit:2 https://nvidia.github.io/nvidia-container-runtime/stable/ubuntu18.04/amd64  InRelease
Hit:3 https://nvidia.github.io/nvidia-docker/ubuntu18.04/amd64  InRelease
Hit:4 https://download.docker.com/linux/ubuntu focal InRelease
Hit:5 http://jp.archive.ubuntu.com/ubuntu focal InRelease                                                
Hit:6 http://jp.archive.ubuntu.com/ubuntu focal-updates InRelease                                        
Hit:7 http://jp.archive.ubuntu.com/ubuntu focal-backports InRelease                              
Hit:9 http://security.ubuntu.com/ubuntu focal-security InRelease                                 
Get:8 https://packages.cloud.google.com/apt kubernetes-xenial InRelease [9,383 B]
Get:10 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 Packages [49.4 kB]
Fetched 58.8 kB in 2s (33.6 kB/s) 
Reading package lists... Done
Reading package lists...
Building dependency tree...
Reading state information...
The following package was automatically installed and is no longer required:
  libllvm11
Use 'sudo apt autoremove' to remove it.
The following additional packages will be installed:
  conntrack cri-tools ebtables ethtool kubernetes-cni socat
Suggested packages:
  nftables
The following NEW packages will be installed:
  conntrack cri-tools ebtables ethtool kubeadm kubectl kubelet kubernetes-cni socat
0 upgraded, 9 newly installed, 0 to remove and 0 not upgraded.
Need to get 73.9 MB of archives.
After this operation, 347 MB of additional disk space will be used.
Get:1 http://jp.archive.ubuntu.com/ubuntu focal/main amd64 conntrack amd64 1:1.4.5-2 [30.3 kB]
Get:2 http://jp.archive.ubuntu.com/ubuntu focal/main amd64 ebtables amd64 2.0.11-3build1 [80.3 kB]
Get:3 http://jp.archive.ubuntu.com/ubuntu focal/main amd64 ethtool amd64 1:5.4-1 [134 kB]
Get:4 http://jp.archive.ubuntu.com/ubuntu focal/main amd64 socat amd64 1.7.3.3-2 [323 kB]
Get:5 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 cri-tools amd64 1.13.0-01 [8,775 kB]
Get:6 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubernetes-cni amd64 0.8.7-00 [25.0 MB]
Get:7 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubelet amd64 1.22.1-00 [21.8 MB]
Get:8 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubectl amd64 1.22.1-00 [9,036 kB]
Get:9 https://packages.cloud.google.com/apt kubernetes-xenial/main amd64 kubeadm amd64 1.22.1-00 [8,717 kB]
Fetched 73.9 MB in 22s (3,304 kB/s)
Selecting previously unselected package conntrack.
(Reading database ... 195069 files and directories currently installed.)
Preparing to unpack .../0-conntrack_1%3a1.4.5-2_amd64.deb ...
Unpacking conntrack (1:1.4.5-2) ...
Selecting previously unselected package cri-tools.
Preparing to unpack .../1-cri-tools_1.13.0-01_amd64.deb ...
Unpacking cri-tools (1.13.0-01) ...
Selecting previously unselected package ebtables.
Preparing to unpack .../2-ebtables_2.0.11-3build1_amd64.deb ...
Unpacking ebtables (2.0.11-3build1) ...
Selecting previously unselected package ethtool.
Preparing to unpack .../3-ethtool_1%3a5.4-1_amd64.deb ...
Unpacking ethtool (1:5.4-1) ...
Selecting previously unselected package kubernetes-cni.
Preparing to unpack .../4-kubernetes-cni_0.8.7-00_amd64.deb ...
Unpacking kubernetes-cni (0.8.7-00) ...
Selecting previously unselected package socat.
Preparing to unpack .../5-socat_1.7.3.3-2_amd64.deb ...
Unpacking socat (1.7.3.3-2) ...
Selecting previously unselected package kubelet.
Preparing to unpack .../6-kubelet_1.22.1-00_amd64.deb ...
Unpacking kubelet (1.22.1-00) ...
Selecting previously unselected package kubectl.
Preparing to unpack .../7-kubectl_1.22.1-00_amd64.deb ...
Unpacking kubectl (1.22.1-00) ...
Selecting previously unselected package kubeadm.
Preparing to unpack .../8-kubeadm_1.22.1-00_amd64.deb ...
Unpacking kubeadm (1.22.1-00) ...
Setting up conntrack (1:1.4.5-2) ...
Setting up kubectl (1.22.1-00) ...
Setting up ebtables (2.0.11-3build1) ...
Setting up socat (1.7.3.3-2) ...
Setting up cri-tools (1.13.0-01) ...
Setting up kubernetes-cni (0.8.7-00) ...
Setting up ethtool (1:5.4-1) ...
Setting up kubelet (1.22.1-00) ...
Created symlink /etc/systemd/system/multi-user.target.wants/kubelet.service → /lib/systemd/system/kubelet.service.
Setting up kubeadm (1.22.1-00) ...
Processing triggers for man-db (2.9.1-1) ...
[init] Using Kubernetes version: v1.22.1
[preflight] Running pre-flight checks
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [gpu-operator kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.122.244]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [gpu-operator localhost] and IPs [192.168.122.244 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [gpu-operator localhost] and IPs [192.168.122.244 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 9.505131 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.22" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node gpu-operator as control-plane by adding the labels: [node-role.kubernetes.io/master(deprecated) node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node gpu-operator as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: 6zhg2i.phaheb96vw8utouy
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.122.244:6443 --token 6zhg2i.phaheb96vw8utouy \
	--discovery-token-ca-cert-hash sha256:2c1658332ad2f9dda9cc9f62157b9700861e311f02d68d0d18baa80290e80139 

$ mkdir -p $HOME/.kube \
>    && sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config \
>    && sudo chown $(id -u):$(id -g) $HOME/.kube/config

$ kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
configmap/calico-config created
customresourcedefinition.apiextensions.k8s.io/bgpconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/bgppeers.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/blockaffinities.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/clusterinformations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/felixconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/globalnetworksets.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/hostendpoints.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamblocks.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamconfigs.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ipamhandles.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/ippools.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/kubecontrollersconfigurations.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networkpolicies.crd.projectcalico.org created
customresourcedefinition.apiextensions.k8s.io/networksets.crd.projectcalico.org created
clusterrole.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrolebinding.rbac.authorization.k8s.io/calico-kube-controllers created
clusterrole.rbac.authorization.k8s.io/calico-node created
clusterrolebinding.rbac.authorization.k8s.io/calico-node created
daemonset.apps/calico-node created
serviceaccount/calico-node created
deployment.apps/calico-kube-controllers created
serviceaccount/calico-kube-controllers created
Warning: policy/v1beta1 PodDisruptionBudget is deprecated in v1.21+, unavailable in v1.25+; use policy/v1 PodDisruptionBudget
poddisruptionbudget.policy/calico-kube-controllers created

$ kubectl taint nodes --all node-role.kubernetes.io/master-
node/gpu-operator untainted
```

# 6. Check the status of kubelet
```
$ sudo systemctl status kubelet
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Thu 2021-09-02 14:28:27 JST; 39s ago
       Docs: https://kubernetes.io/docs/home/
   Main PID: 19702 (kubelet)
      Tasks: 14 (limit: 9481)
     Memory: 60.2M
     CGroup: /system.slice/kubelet.service
             └─19702 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kub>

 9月 02 14:28:43 gpu-operator kubelet[19702]: I0902 14:28:43.278797   19702 cni.go:239] "Unable to update>
 9月 02 14:28:43 gpu-operator kubelet[19702]: E0902 14:28:43.702043   19702 kubelet.go:2332] "Container r>
 9月 02 14:28:48 gpu-operator kubelet[19702]: I0902 14:28:48.279715   19702 cni.go:239] "Unable to update>
 9月 02 14:28:48 gpu-operator kubelet[19702]: E0902 14:28:48.737390   19702 kubelet.go:2332] "Container r>
 9月 02 14:28:53 gpu-operator kubelet[19702]: I0902 14:28:53.280858   19702 cni.go:239] "Unable to update>
 9月 02 14:28:53 gpu-operator kubelet[19702]: E0902 14:28:53.765374   19702 kubelet.go:2332] "Container r>
 9月 02 14:28:58 gpu-operator kubelet[19702]: I0902 14:28:58.282149   19702 cni.go:239] "Unable to update>
 9月 02 14:28:58 gpu-operator kubelet[19702]: E0902 14:28:58.792693   19702 kubelet.go:2332] "Container r>
 9月 02 14:29:03 gpu-operator kubelet[19702]: I0902 14:29:03.283766   19702 cni.go:239] "Unable to update>
 9月 02 14:29:03 gpu-operator kubelet[19702]: E0902 14:29:03.816969   19702 kubelet.go:2332] "Container r>
```

# 7. Check the images
```
$ sudo docker images
REPOSITORY                           TAG       IMAGE ID       CREATED        SIZE
k8s.gcr.io/kube-apiserver            v1.22.1   f30469a2491a   13 days ago    128MB
k8s.gcr.io/kube-proxy                v1.22.1   36c4ebbc9d97   13 days ago    104MB
k8s.gcr.io/kube-scheduler            v1.22.1   aca5ededae9c   13 days ago    52.7MB
k8s.gcr.io/kube-controller-manager   v1.22.1   6e002eb89a88   13 days ago    122MB
calico/cni                           v3.20.0   4945b742b8e6   4 weeks ago    146MB
k8s.gcr.io/etcd                      3.5.0-0   004811815584   2 months ago   295MB
k8s.gcr.io/coredns/coredns           v1.8.4    8d147537fb7d   3 months ago   47.6MB
k8s.gcr.io/pause                     3.5       ed210e3e4a5b   5 months ago   683kB

$ kubectl get nodes
NAME           STATUS   ROLES                  AGE     VERSION
gpu-operator   Ready    control-plane,master   2m49s   v1.22.1
```
