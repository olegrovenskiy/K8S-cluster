# K8S-cluster

## Домашнее задание к занятию "12.4 Развертывание кластера на собственных серверах, лекция 2"

Разворачивал кластер немного с другими параметрами, чем в задании, т.к. планирую его дальше использовать для работы.

Файл инвентори:


    [kube_control_plane]
    node1
    node2
    node3

    [etcd]
    node1
    node2
    node3

    [kube_node]
    node4
    node5
    node6

    [calico_rr]

    [k8s_cluster:children]
    kube_control_plane
    kube_node
    calico_rr

То есть, три ноды control plane+etcd, и три ноды worker

Подготовка нод:

1. ВМ подготовлены на ВМваре, Ubuntu 20.04
2. На каждой ВМ отключен своп swapoff -a
3. В файле /etc/hosts прописал все 6 хостов
4. Открыл доступ в интернет для всех нод

Подготовка файлов конфигурации:

1. Инвентори, прописал все хосты
2. Контейнер менеджер - изменил на docker, в sample установлен containerd, для этого:
   - в файле etcd.yml прописал etcd_deployment_type: docker
   - в файле k8s-kluster.yml прописал container_manager: docker
3. Прописал имя кластера cluster_name: k8s.mgs.local


Запуск плейбука

    ansible-playbook -i inventory/mycluster/inventory.ini cluster.yml --become --become-user=root -v --extra-vars "ansible_user=ПОЛЬЗОВАТЕЛЬ    ansible_password=ПАРОЛЬ ansible_sudo_pass=ПАРОЛЬ"


Инсталяция прошла успешно:

        PLAY RECAP ********************************************************************************************************
        localhost                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
        node1                      : ok=647  changed=122  unreachable=0    failed=0    skipped=1219 rescued=0    ignored=4 
        node2                      : ok=577  changed=107  unreachable=0    failed=0    skipped=1076 rescued=0    ignored=2 
        node3                      : ok=579  changed=108  unreachable=0    failed=0    skipped=1074 rescued=0    ignored=2 
        node4                      : ok=423  changed=71   unreachable=0    failed=0    skipped=710  rescued=0    ignored=1 
        node5                      : ok=423  changed=71   unreachable=0    failed=0    skipped=709  rescued=0    ignored=1 
        node6                      : ok=423  changed=71   unreachable=0    failed=0    skipped=709  rescued=0    ignored=1 

        Monday 28 March 2022  19:42:35 +0000 (0:00:00.139)       0:17:37.964 **********
        ===============================================================================
        container-engine/docker : ensure docker packages are installed -------------------------------------------- 75.22s
        kubernetes/preinstall : Preinstall | wait for the apiserver to be running --------------------------------- 35.11s
        kubernetes/preinstall : Install packages requirements ----------------------------------------------------- 31.32s
        kubernetes/preinstall : Update package management cache (APT) --------------------------------------------- 29.82s
        kubernetes/control-plane : Joining control plane node to the cluster. ------------------------------------- 28.85s
        kubernetes/kubeadm : Join to cluster ---------------------------------------------------------------------- 25.73s
        network_plugin/calico : Wait for calico kubeconfig to be created ------------------------------------------ 25.70s
        download : download_container | Download image if required ------------------------------------------------ 22.89s
        download : download_container | Download image if required ------------------------------------------------ 21.92s
        download : download_container | Download image if required ------------------------------------------------ 21.32s
        download : download_container | Download image if required ------------------------------------------------ 18.31s
        container-engine/docker : ensure docker-ce repository is enabled ------------------------------------------ 17.57s
        kubernetes/control-plane : kubeadm | Initialize first master ---------------------------------------------- 15.25s
        download : download_file | Download item ------------------------------------------------------------------ 14.77s
        download : download_container | Download image if required ------------------------------------------------ 13.17s
        download : download_file | Download item ------------------------------------------------------------------ 13.00s
        download : download_container | Download image if required ------------------------------------------------ 12.33s
        etcd : reload etcd ---------------------------------------------------------------------------------------- 10.97s
        download : download_container | Download image if required ------------------------------------------------ 10.45s
        download : download_container | Download image if required ------------------------------------------------ 10.15s
        oleg@mck-devops-tools:~/kubespray$
        oleg@mck-devops-tools:~/kubespray$
        
 
 На ноде control-plane
 
         root@mck-k8s-cp1:/etc# kubectl version
        Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:58:47Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
        Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:52:18Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}


        root@mck-k8s-cp1:/etc# kubectl get nodes
        NAME    STATUS   ROLES                  AGE   VERSION
        node1   Ready    control-plane,master   36m   v1.23.5
        node2   Ready    control-plane,master   35m   v1.23.5
        node3   Ready    control-plane,master   35m   v1.23.5
        node4   Ready    <none>                 34m   v1.23.5
        node5   Ready    <none>                 34m   v1.23.5
        node6   Ready    <none>                 34m   v1.23.5
        root@mck-k8s-cp1:/etc#

        root@mck-k8s-cp1:/etc# kubeadm version
        kubeadm version: &version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:57:37Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
        root@mck-k8s-cp1:/etc#

           root@mck-k8s-cp1:/etc# kubectl cluster-info
        Kubernetes control plane is running at https://127.0.0.1:6443

        To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
        root@mck-k8s-cp1:/etc#
        
        
        root@mck-k8s-cp1:/etc# kubectl get cs -A
        Warning: v1 ComponentStatus is deprecated in v1.19+
        NAME                 STATUS    MESSAGE                         ERROR
        controller-manager   Healthy   ok
        scheduler            Healthy   ok
        etcd-0               Healthy   {"health":"true","reason":""}
        etcd-1               Healthy   {"health":"true","reason":""}
        etcd-2               Healthy   {"health":"true","reason":""}
        root@mck-k8s-cp1:/etc#

    

Ingress, Addons, API сервер не прописывал


