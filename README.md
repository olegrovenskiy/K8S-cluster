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





