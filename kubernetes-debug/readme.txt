kubectl debug -it nginx-distroless --image=busybox:latest --target=nginx --share-processes
/ # ls -la /etc/nginx
ls: /etc/nginx: No such file or directory
/ # 

ls -la /proc/1/cwd/etc/nginx/
total 48
drwxr-xr-x    3 root     root          4096 Oct  5  2020 .
drwxr-xr-x    1 root     root          4096 Jul 15 17:20 ..
drwxr-xr-x    2 root     root          4096 Oct  5  2020 conf.d
-rw-r--r--    1 root     root          1007 Apr 21  2020 fastcgi_params
-rw-r--r--    1 root     root          2837 Apr 21  2020 koi-utf
-rw-r--r--    1 root     root          2223 Apr 21  2020 koi-win
-rw-r--r--    1 root     root          5231 Apr 21  2020 mime.types
lrwxrwxrwx    1 root     root            22 Apr 21  2020 modules -> /usr/lib/nginx/modules
-rw-r--r--    1 root     root           643 Apr 21  2020 nginx.conf
-rw-r--r--    1 root     root           636 Apr 21  2020 scgi_params
-rw-r--r--    1 root     root           664 Apr 21  2020 uwsgi_params
-rw-r--r--    1 root     root          3610 Apr 21  2020 win-utf

nginx-distroless  ~  tcpdump -nn -i any -e port 80
tcpdump: WARNING: any: That device doesn't support promiscuous mode
(Promiscuous mode not supported on the "any" device)
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on any, link-type LINUX_SLL2 (Linux cooked v2), snapshot length 262144 bytes
18:29:29.968839 lo    In  ifindex 1 00:00:00:00:00:00 ethertype IPv4 (0x0800), length 80: 127.0.0.1.41248 > 127.0.0.1.80: Flags [S], seq 844794463, win 65495, options [mss 65495,sackOK,TS val 2323488367 ecr 0,nop,wscale 7], length 0
18:29:29.968852 lo    In  ifindex 1 00:00:00:00:00:00 ethertype IPv4 (0x0800), length 80: 127.0.0.1.80 > 127.0.0.1.41248: Flags [S.], seq 2568408097, ack 844794464, win 65483, options [mss 65495,sackOK,TS val 2323488368 ecr 2323488367,nop,wscale 7], length 0
18:29:29.968864 lo    In  ifindex 1 00:00:00:00:00:00 ethertype IPv4 (0x0800), length 72: 127.0.0.1.41248 > 127.0.0.1.80: Flags [.], ack 1, win 512, options [nop,nop,TS val 2323488368 ecr 2323488368], length 0
18:29:29.968938 lo    In  ifindex 1 00:00:00:00:00:00 ethertype IPv4 (0x0800), length 150: 127.0.0.1.41248 > 127.0.0.1.80: Flags [P.], seq 1:79, ack 1, win 512, options [nop,nop,TS val 2323488368 ecr 2323488368], length 78: HTTP: GET / HTTP/1.1
18:29:29.968944 lo    In  ifindex 1 00:00:00:00:00:00 ethertype IPv4 (0x0800), length 72: 127.0.0.1.80 > 127.0.0.1.41248: Flags [.], ack 79, win 511, options [nop,nop,TS val 2323488368 ecr 2323488368], length 0
18:29:29.969321 lo    In  ifindex 1 00:00:00:00:00:00 ethertype IPv4 (0x0800), length 310: 127.0.0.1.80 > 127.0.0.1.41248: Flags [P.], seq 1:239, ack 79, win 512, options [nop,nop,TS val 2323488368 ecr 2323488368], length 238: HTTP: HTTP/1.1 200 OK
18:29:29.969335 lo    In  ifindex 1 00:00:00:00:00:00 ethertype IPv4 (0x0800), length 72: 127.0.0.1.41248 > 127.0.0.1.80: Flags [.], ack 239, win 511, options [nop,nop,TS val 2323488368 ecr 2323488368], length 0

kubectl apply -f nginx-debug-3.yaml

kubectl exec -it nginx-debug-3 -c debugger -- bash

ps -ef | grep nginx

root@nginx-debug-3:/# ls -la /proc/7/root/var/log/nginx/
total 8
drwxr-xr-x 2 root root 4096 Oct  5  2020 .
drwxr-xr-x 1 root root 4096 Oct  5  2020 ..
lrwxrwxrwx 1 root root   11 Oct  5  2020 access.log -> /dev/stdout
lrwxrwxrwx 1 root root   11 Oct  5  2020 error.log -> /dev/stderr
root@nginx-debug-3:/# 

Т.е. контейнер не пишет логи внутри контейнера в файлы, поэтому посмотреть логи можно только через
kubectl logs nginx-debug-3 -c nginx


