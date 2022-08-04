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

>vagrant destroy -f  vg-prom-04
An action 'destroy' was attempted on the machine 'vg-prom-04',
but another process is already executing an action on the machine.
Vagrant locks each machine for access by only one process at a time.
Please wait until the other Vagrant process finishes modifying this
machine, then try again.

If you believe this message is in error, please check the process
listing for any "ruby" or "vagrant" processes and kill them. Then
try again.

Virtual Box GUI - VM - Right Click - Close - Power Off
Virtual Box GUI - VM - Right Click - Remove - Delete all files

(still listing VM)
>vagrant global-status
id       name       provider   state   directory
---------------------------------------------------------------------------------------------
1a50c84  vg-prom-04 virtualbox running C:/tmp/github/vagrant-prometheus-docker-image-sandbox

>vagrant global-status --prune
id       name   provider state  directory
--------------------------------------------------------------------
There are no active Vagrant environments on this computer! Or,
you haven't destroyed and recreated Vagrant environments that were
started with an older version of Vagrant.
~~~~
