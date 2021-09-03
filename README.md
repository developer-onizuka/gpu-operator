# GPU Operator with preinstalled driver in Host

# 0. Disable Swapping and Blacklisting Nouveau driver
This is very important step. If you skip this step, the kubelet will never run.

See https://github.com/developer-onizuka/swapoff .

The nouveau driver for NVIDIA GPUs must be blacklisted before starting the GPU Operator. 
Create a file at /etc/modprobe.d/blacklist-nouveau.conf with the following contents:
```
$ sudo vi /etc/modprobe.d/blacklist-nouveau.conf
blacklist nouveau
options nouveau modeset=0

$ sudo update-initramfs -u
```

# 1. Install Nvidia Driver on Host Machine
```
$ sudo apt-get update
$ sudo apt-get install nvidia-driver-470
```

# 2. Install Curl
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

# 3. Install Docker-CE
```
$ curl https://get.docker.com | sh \
&& sudo systemctl --now enable docker
-----
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
-----
$ sudo docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

# 4. Install Nvidia Docker2
You can skip this because GPU operator will introduce the daemonSet of nvidia-container-toolkit-daemonset in later.

# 5. Configuring about using systemd instead of cgroups.
This step is very important. If you skip this step then the kubelet does not run. 
Configure the Docker daemon, in particular to use systemd for the management of the container’s cgroups. 

See also https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker .
```
$ sudo mkdir /etc/docker
mkdir: cannot create directory ‘/etc/docker’: File exists
-----
$ cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
-----
$ sudo systemctl enable docker
Synchronizing state of docker.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable docker
-----
$ sudo systemctl daemon-reload
-----
$ sudo systemctl restart docker
-----
$ sudo docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

# 6. Install kubernetes
See also https://docs.nvidia.com/datacenter/cloud-native/kubernetes/install-k8s.html .
```
$ sudo apt-get update \
&& sudo apt-get install -y apt-transport-https curl
-----
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
-----
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
OK
-----
$ cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
-----
$ sudo apt-get update \
&& sudo apt-get install -y -q kubelet kubectl kubeadm \
&& sudo kubeadm init --pod-network-cidr=192.168.0.0/16
-----
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
-----
$ mkdir -p $HOME/.kube \
&& sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config \
&& sudo chown $(id -u):$(id -g) $HOME/.kube/config
-----
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
-----
$ kubectl taint nodes --all node-role.kubernetes.io/master-
node/gpu-operator untainted
```

# 7. Check the status of kubelet
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

# 8. Check the images
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
-----
$ kubectl get nodes
NAME           STATUS   ROLES                  AGE     VERSION
gpu-operator   Ready    control-plane,master   2m49s   v1.22.1
```

# 9. Install helm and GPU operator
```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 \
&& chmod 700 get_helm.sh \
&& ./get_helm.sh
-----
Downloading https://get.helm.sh/helm-v3.6.3-linux-amd64.tar.gz
Verifying checksum... Done.
Preparing to install helm into /usr/local/bin
helm installed into /usr/local/bin/helm

-----
$ helm repo add nvidia https://nvidia.github.io/gpu-operator \
&& helm repo update
-----
"nvidia" has been added to your repositories
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "nvidia" chart repository
Update Complete. ⎈Happy Helming!⎈
-----
$ helm install --wait --generate-name \
nvidia/gpu-operator \
--set driver.enabled=false
-----
NAME: gpu-operator-1630573103
LAST DEPLOYED: Thu Sep  2 17:58:26 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
-----
$ kubectl get pod -A
NAMESPACE                NAME                                                              READY   STATUS      RESTARTS      AGE
default                  gpu-operator-1630662996-node-feature-discovery-master-8477x4t7m   1/1     Running     0             30m
default                  gpu-operator-1630662996-node-feature-discovery-worker-2gkph       1/1     Running     0             30m
default                  gpu-operator-74dcf6544d-xlgcz                                     1/1     Running     0             30m
gpu-operator-resources   gpu-feature-discovery-gpx48                                       1/1     Running     0             29m
gpu-operator-resources   nvidia-container-toolkit-daemonset-cqbd6                          1/1     Running     0             29m
gpu-operator-resources   nvidia-cuda-validator-cpjvz                                       0/1     Completed   0             29m
gpu-operator-resources   nvidia-dcgm-485gt                                                 1/1     Running     0             29m
gpu-operator-resources   nvidia-dcgm-exporter-rgxj7                                        1/1     Running     4 (26m ago)   29m
gpu-operator-resources   nvidia-device-plugin-daemonset-fq45d                              1/1     Running     0             29m
gpu-operator-resources   nvidia-device-plugin-validator-557rb                              0/1     Completed   0             25m
gpu-operator-resources   nvidia-operator-validator-22sxz                                   1/1     Running     0             29m
kube-system              calico-kube-controllers-58497c65d5-dnld7                          1/1     Running     4 (33m ago)   72m
kube-system              calico-node-6q8xj                                                 1/1     Running     4 (33m ago)   72m
kube-system              coredns-78fcd69978-4sdx2                                          1/1     Running     4 (33m ago)   75m
kube-system              coredns-78fcd69978-cxs29                                          1/1     Running     4 (33m ago)   75m
kube-system              etcd-gpu-operator                                                 1/1     Running     5 (32m ago)   76m
kube-system              kube-apiserver-gpu-operator                                       1/1     Running     6 (32m ago)   76m
kube-system              kube-controller-manager-gpu-operator                              1/1     Running     5 (32m ago)   76m
kube-system              kube-proxy-tgslp                                                  1/1     Running     4 (33m ago)   75m
kube-system              kube-scheduler-gpu-operator                                       1/1     Running     5 (32m ago)   76m
```
# 10. Start Ubuntu container without Nvidia Driver
You can see the container of ubuntu can do nvidia-smi even though it could not install nvidia-driver inside.

```
$ cat ubuntu-gpu.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu 
  labels:
    name: ubuntu
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
    - sleep
    - "3600"
    resources:
      limits:
         nvidia.com/gpu: 1
-----
$ kubectl apply -f ubuntu-gpu.yaml
-----
$ kubectl get pods
NAME                                                              READY   STATUS    RESTARTS   AGE
gpu-operator-1630662996-node-feature-discovery-master-8477x4t7m   1/1     Running   0          57m
gpu-operator-1630662996-node-feature-discovery-worker-2gkph       1/1     Running   0          57m
gpu-operator-74dcf6544d-xlgcz                                     1/1     Running   0          57m
ubuntu                                                            1/1     Running   0          30m
-----
$ kubectl exec -it ubuntu -- /bin/bash
-----
root@ubuntu:/# dpkg -l |grep -i nvidia
--> No result, this means container has no driver. But you can do nvidia-smi, even though no driver inside of container.
-----
root@ubuntu:/# nvidia-smi
Fri Sep  3 10:29:53 2021       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.57.02    Driver Version: 470.57.02    CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Quadro P1000        Off  | 00000000:04:00.0 Off |                  N/A |
| 34%   39C    P8    N/A /  N/A |     11MiB /  4040MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```

The following is the yaml file without "nvidia.com/gpu: 1". As you can see below, nvidia-smi is not available.
```
$ cat ubuntu.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu 
  labels:
    name: ubuntu
spec:
  containers:
  - name: ubuntu
    image: ubuntu
    command:
    - sleep
    - "3600"
-----
$ kubectl apply -f ubuntu.yaml
-----
$ kubectl get pod
NAME                                                              READY   STATUS    RESTARTS   AGE
gpu-operator-1630662996-node-feature-discovery-master-8477x4t7m   1/1     Running   0          63m
gpu-operator-1630662996-node-feature-discovery-worker-2gkph       1/1     Running   0          63m
gpu-operator-74dcf6544d-xlgcz                                     1/1     Running   0          63m
ubuntu                                                            1/1     Running   0          12s
-----
$ kubectl exec -it ubuntu -- /bin/bash
-----
root@ubuntu:/# nvidia-smi
bash: nvidia-smi: command not found
```

# 11. Uninstall GPU operator

```
$ helm ls -n default | awk '/gpu-operator/{print $1}'
$ helm delete gpu-operator-1630661158 -n default 
```
