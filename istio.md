https://istio.io/latest/docs/setup/getting-started/

    root@mck-t-k8s-cp1:~#
    root@mck-t-k8s-cp1:~# ls -l
    total 56
    -rw-r--r-- 1 root root  1944 May 16 14:56 cert.crt
    -rw------- 1 root root  3243 May 16 14:54 cert.key
    -rw-r--r-- 1 root root  7167 May 16 15:04 domain-cert.yml
    drwxr-xr-x 3 root root  4096 Mar 28 19:40 kube-manifests
    -rw-r--r-- 1 root root 25818 May 16 15:03 secrets.json
    drwxr-xr-x 3 root root  4096 Mar 25 12:34 snap
    drwxr-xr-x 4 root root  4096 Apr  7 07:29 test-k8s-oleg
    root@mck-t-k8s-cp1:~# curl -L https://istio.io/downloadIstio | sh -
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   101  100   101    0     0     12      0  0:00:08  0:00:07  0:00:01    21
    100  4926  100  4926    0     0    597      0  0:00:08  0:00:08 --:--:-- 79451

    Downloading istio-1.14.0 from https://github.com/istio/istio/releases/download/1.14.0/istio-1.14.0-linux-amd64.tar.gz ...

    Istio 1.14.0 Download Complete!

    Istio has been successfully downloaded into the istio-1.14.0 folder on your system.

    Next Steps:
    See https://istio.io/latest/docs/setup/install/ to add Istio to your Kubernetes cluster.

    To configure the istioctl client tool for your workstation,
    add the /root/istio-1.14.0/bin directory to your environment path variable with:
             export PATH="$PATH:/root/istio-1.14.0/bin"

    Begin the Istio pre-installation check by running:
             istioctl x precheck

    Need more information? Visit https://istio.io/latest/docs/setup/install/
    root@mck-t-k8s-cp1:~#
    root@mck-t-k8s-cp1:~#
    root@mck-t-k8s-cp1:~# ls -l
    total 60
    -rw-r--r-- 1 root root  1944 May 16 14:56 cert.crt
    -rw------- 1 root root  3243 May 16 14:54 cert.key
    -rw-r--r-- 1 root root  7167 May 16 15:04 domain-cert.yml
    drwxr-x--- 6 root root  4096 May 27 16:26 istio-1.14.0
    drwxr-xr-x 3 root root  4096 Mar 28 19:40 kube-manifests
    -rw-r--r-- 1 root root 25818 May 16 15:03 secrets.json
    drwxr-xr-x 3 root root  4096 Mar 25 12:34 snap
    drwxr-xr-x 4 root root  4096 Apr  7 07:29 test-k8s-oleg
    root@mck-t-k8s-cp1:~# cd ./istio-1.14.0/
    root@mck-t-k8s-cp1:~/istio-1.14.0# ls -l
    total 40
    drwxr-x---  2 root root  4096 May 27 16:26 bin
    -rw-r--r--  1 root root 11348 May 27 16:26 LICENSE
    drwxr-xr-x  5 root root  4096 May 27 16:26 manifests
    -rw-r-----  1 root root   882 May 27 16:26 manifest.yaml
    -rw-r--r--  1 root root  6016 May 27 16:26 README.md
    drwxr-xr-x 23 root root  4096 May 27 16:26 samples
    drwxr-xr-x  3 root root  4096 May 27 16:26 tools
    root@mck-t-k8s-cp1:~/istio-1.14.0# istioctl x precheck
    istioctl: command not found
    root@mck-t-k8s-cp1:~/istio-1.14.0#
    root@mck-t-k8s-cp1:~/istio-1.14.0#
    root@mck-t-k8s-cp1:~/istio-1.14.0#  export PATH=$PWD/bin:$PATH
    root@mck-t-k8s-cp1:~/istio-1.14.0#
    root@mck-t-k8s-cp1:~/istio-1.14.0#
    root@mck-t-k8s-cp1:~/istio-1.14.0# istioctl install --set profile=demo -y
    WARNING: Istio control planes installed: 1.13.2.
    WARNING: A newer installed version of Istio has been detected. Running this command will overwrite it.
    ✔ Istio core installed
    ✔ Istiod installed
    ✔ Ingress gateways installed
    ✔ Egress gateways installed
    ✔ Installation complete                                                                              Making this installation the default for injection and validation.
    root@mck-t-k8s-cp1:~/istio-1.14.0#




