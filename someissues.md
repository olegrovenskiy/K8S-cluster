
https://serverfault.com/questions/877136/debug-kubelet-not-starting

sudo -i
swapoff -a
kubeadm reset
kubeadm init


сборка из собственных образов
Нужно включать registry и загружать туда образ
Кстати registry можно поднять так же в докере. (https://hub.docker.com/_/registry)
