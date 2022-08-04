# vagrant-prometheus-docker-image-sandbox

~~~~


vagrant up vg-prom-04

vagrant ssh vg-prom-04

vagrant@vg-prom-04:~$ docker container ls
CONTAINER ID   IMAGE             COMMAND                  CREATED         STATUS         PORTS                                       NAMES
e151ebb87fab   prom/prometheus   "/bin/prometheus --c…"   3 minutes ago   Up 3 minutes   0.0.0.0:9090->9090/tcp, :::9090->9090/tcp   prometheus-server

Prometheus GUI 
http://192.168.55.11:9090/

vagrant destroy -f  vg-prom-04 does not work
Virtual Box GUI - Power Off - Delete files

vagrant global-status (still listing VM)
vagrant global-status --prune 

~~~~
