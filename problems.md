# DNS

    root@mck-t-k8s-cp1:/etc# cat /etc/netplan/00-installer-config.yaml
    # This is the network config written by 'subiquity'
    network:
      ethernets:
        ens160:
          addresses:
          - 10.102.8.80/24
          gateway4: 10.102.8.1
          nameservers:
            addresses:
            - 10.102.5.101
            - 10.102.5.102
            search:
            - 10.102.5.101
            - 10.102.5.102
      version: 2
    root@mck-t-k8s-cp1:/etc#

но

    root@mck-t-k8s-cp1:~# nslookup mck-t3-mariadb.mgc.local
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    ** server can't find mck-t3-mariadb.mgc.local: SERVFAIL

    root@mck-t-k8s-cp1:~# ^C


В файле

    root@mck-t-k8s-cp1:/etc# cat resolv.conf
    # This file is managed by man:systemd-resolved(8). Do not edit.
    #
    # This is a dynamic resolv.conf file for connecting local clients to the
    # internal DNS stub resolver of systemd-resolved. This file lists all
    # configured search domains.
    #
    # Run "resolvectl status" to see details about the uplink DNS servers
    # currently in use.
    #
    # Third party programs must not access this file directly, but only through the
    # symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
    # replace this symlink by a static file or a different symlink.
    #
    # See man:systemd-resolved.service(8) for details about the supported modes of
    # operation for /etc/resolv.conf.

    nameserver 127.0.0.53
    options edns0 trust-ad
    search default.svc.mgc.local svc.mgc.local 10.102.5.101 10.102.5.102

руками прописал сервера DNS

    root@mck-t-k8s-cp1:/etc# cat resolv.conf
    # This file is managed by man:systemd-resolved(8). Do not edit.
    #
    # This is a dynamic resolv.conf file for connecting local clients to the
    # internal DNS stub resolver of systemd-resolved. This file lists all
    # configured search domains.
    #
    # Run "resolvectl status" to see details about the uplink DNS servers
    # currently in use.
    #
    # Third party programs must not access this file directly, but only through the
    # symlink at /etc/resolv.conf. To manage man:resolv.conf(5) in a different way,
    # replace this symlink by a static file or a different symlink.
    #
    # See man:systemd-resolved.service(8) for details about the supported modes of
    # operation for /etc/resolv.conf.

    nameserver 127.0.0.53
    nameserver 10.102.5.101
    nameserver 10.102.5.102
    options edns0 trust-ad
    search default.svc.mgc.local svc.mgc.local 10.102.5.101 10.102.5.102
    root@mck-t-k8s-cp1:/etc#

После чего всё ок

    root@mck-t-k8s-cp1:/etc# nslookup mck-t3-mariadb.mgc.local
    ;; Got SERVFAIL reply from 127.0.0.53, trying next server
    Server:         10.102.5.101
    Address:        10.102.5.101#53

    Name:   mck-t3-mariadb.mgc.local
    Address: 10.102.27.45
    ;; Got SERVFAIL reply from 127.0.0.53, trying next server

    root@mck-t-k8s-cp1:/etc#


прописал в файлу all.yml

        ## Upstream dns servers
        # upstream_dns_servers:
        #   - 8.8.8.8
        #   - 8.8.4.4

наши DNS сервера
