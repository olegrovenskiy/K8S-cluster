#  Домашнее задание к занятию "12.5 Сетевые решения CNI"

##  Задание 1: установить в кластер CNI плагин Calico

Для выполнения использовал рекомендации с вебинара и темплейты 

https://github.com/aak74/kubernetes-for-beginners/tree/master/16-networking/20-network-policy

Развёртывание объектов

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl apply -f ./templates/main/
    deployment.apps/frontend created
    service/frontend created
    deployment.apps/backend created
    service/backend created
    deployment.apps/cache created
    service/cache created
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy#

Проверка:

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl get po
    NAME                        READY   STATUS    RESTARTS   AGE
    backend-f785447b9-nmczl     1/1     Running   0          86s
    cache-b4f65b647-tzt27       1/1     Running   0          86s
    frontend-8645d9cb9c-pnkjs   1/1     Running   0          86s
    nginx-7c658794b9-2x6vq      1/1     Running   0          5d11h
    nginx-7c658794b9-5qgzm      1/1     Running   0          5d11h
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy#


Все три объекта для тестирования network-policy (frontend, backend, cache) готовы

Проверка доступности

    kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 frontend
    kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 cache
    kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 backend


    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 frontend
    Praqma Network MultiTool (with NGINX) - frontend-8645d9cb9c-pnkjs - 10.233.105.2
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 cache
    Praqma Network MultiTool (with NGINX) - cache-b4f65b647-tzt27 - 10.233.70.1
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 backend
    Praqma Network MultiTool (with NGINX) - backend-f785447b9-nmczl - 10.233.108.2
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy#


С backend все поды доступны.

Применяю политику 00-default, которая всем запрещает ingress
На класткрк CNI Calico

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 frontend
    command terminated with exit code 28
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 cache
    command terminated with exit code 28
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec backend-f785447b9-nmczl -- curl -s -m 1 backend
    command terminated with exit code 28
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# 


политика работает, доступ по ингрес закрыт


Применяем все политики

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl apply -f ./templates/network-policy
    networkpolicy.networking.k8s.io/default-deny-ingress unchanged
    networkpolicy.networking.k8s.io/frontend created
    networkpolicy.networking.k8s.io/backend created
    networkpolicy.networking.k8s.io/cache created
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl get networkpolicies
    NAME                   POD-SELECTOR   AGE
    backend                app=backend    12s
    cache                  app=cache      12s
    default-deny-ingress   <none>         4m34s
    frontend               app=frontend   12s
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy#


Из бекенда доступен только кеш, что соответствует полиси

Проверяем из фронтенда

    kubectl exec frontend-8645d9cb9c-pnkjs -- curl -s -m 1 backend

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec frontend-8645d9cb9c-pnkjs -- curl -s -m 1 backend
    Praqma Network MultiTool (with NGINX) - backend-f785447b9-nmczl - 10.233.108.2
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# kubectl exec frontend-8645d9cb9c-pnkjs -- curl -s -m 1 cache
    command terminated with exit code 28
    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy#


доступен бекенд и недоступен кеш, что соответствует network-policy

##  Задание 2: изучить, что запущено по умолчанию

Список нод, ipPool и profile.

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# calicoctl get nodes
    NAME
    node1
    node2
    node3
    node4
    node5
    node6


    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# calicoctl get ippool
    NAME           CIDR             SELECTOR
    default-pool   10.233.64.0/18   all()

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# ^C


    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy# calicoctl get profile
    NAME
    projectcalico-default-allow
    kns.default
    kns.kube-node-lease
    kns.kube-public
    kns.kube-system
    ksa.default.default
    ksa.kube-node-lease.default
    ksa.kube-public.default
    ksa.kube-system.attachdetach-controller
    ksa.kube-system.bootstrap-signer
    ksa.kube-system.calico-kube-controllers
    ksa.kube-system.calico-node
    ksa.kube-system.certificate-controller
    ksa.kube-system.clusterrole-aggregation-controller
    ksa.kube-system.coredns
    ksa.kube-system.cronjob-controller
    ksa.kube-system.daemon-set-controller
    ksa.kube-system.default
    ksa.kube-system.deployment-controller
    ksa.kube-system.disruption-controller
    ksa.kube-system.dns-autoscaler
    ksa.kube-system.endpoint-controller
    ksa.kube-system.endpointslice-controller
    ksa.kube-system.endpointslicemirroring-controller
    ksa.kube-system.ephemeral-volume-controller
    ksa.kube-system.expand-controller
    ksa.kube-system.generic-garbage-collector
    ksa.kube-system.horizontal-pod-autoscaler
    ksa.kube-system.job-controller
    ksa.kube-system.kube-proxy
    ksa.kube-system.namespace-controller
    ksa.kube-system.node-controller
    ksa.kube-system.nodelocaldns
    ksa.kube-system.persistent-volume-binder
    ksa.kube-system.pod-garbage-collector
    ksa.kube-system.pv-protection-controller
    ksa.kube-system.pvc-protection-controller
    ksa.kube-system.replicaset-controller
    ksa.kube-system.replication-controller
    ksa.kube-system.resourcequota-controller
    ksa.kube-system.root-ca-cert-publisher
    ksa.kube-system.service-account-controller
    ksa.kube-system.service-controller
    ksa.kube-system.statefulset-controller
    ksa.kube-system.token-cleaner
    ksa.kube-system.ttl-after-finished-controller
    ksa.kube-system.ttl-controller

    root@mck-k8s-cp1:~/test-k8s-oleg/network-policy#





