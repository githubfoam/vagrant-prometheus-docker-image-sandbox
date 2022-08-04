# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

# Minikube
KUBERNETES_VERSION = ENV['KUBERNETES_VERSION'] || "1.16.3" # OK
# X Exiting due to GUEST_MISSING_CONNTRACK: Sorry, Kubernetes 1.24.3 requires conntrack to be installed in root's path
# KUBERNETES_VERSION = ENV['KUBERNETES_VERSION'] || "1.24.3" # NOT OK conntrack missing






$ubuntu_docker_script = <<-SCRIPT

echo "current user is $(whoami)"
echo "current directory is $(pwd)"

# vg-prom01: Package 'docker.io' is not installed, so not removed
# vg-prom01: E: Unable to locate package docker
# vg-prom01: E: Unable to locate package docker-engine
# The SSH command responded with a non-zero exit status. Vagrant
# assumes that this means the command failed. The output for this command
# should be in the log above. Please read the output to determine what
# went wrong.
# Uninstall old versions
# apt-get remove docker docker-engine docker.io containerd runc -y

# Set up the repository
apt-get update -y
apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release -y

# Add Docker’s official GPG key    
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# set up the stable repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


# Install Docker Engine
apt-get update -y
apt-get install \
    docker-ce \
    docker-ce-cli \
    containerd.io -y


docker --version

# Verify that Docker Engine is installed correctly     
docker run hello-world

# Post-installation steps for Linux
# Manage Docker as a non-root user

# Create the docker group
groupadd docker
# Add your user to the docker group
# usermod -aG docker $USER # by default run by root
usermod -aG docker vagrant

#docker compose
apt-get install docker-compose -y

SCRIPT



$Prometheus_From_Docker_Image_script = <<-SCRIPT

cd /tmp 

cat | sudo tee /tmp/prometheus.yml << EOF
global:
  scrape_interval:     5s
  scrape_timeout:      3s
  evaluation_interval: 5s

# Our alerting rule files
rule_files:
  - "alert.rules"

# Scrape endpoints
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']      
EOF

cat /tmp/prometheus.yml #verify

docker run \
     --name prometheus-server \
     -p 9090:9090 \
     -v /tmp/prometheus.yml:/etc/prometheus/prometheus.yml \
     prom/prometheus

docker container ls

SCRIPT



# script on vg-prom-05 (192.168.53.12)
$prometheus_remote_script = <<-SCRIPT

#Exposing Docker Metrics to remote Prometheus server
#https://docs.docker.com/config/daemon/prometheus/
cat | sudo tee /etc/docker/daemon.json << EOF
{
  "experimental": true,
  "metrics-addr": "192.168.55.12:9323"
}
EOF
cat /etc/docker/daemon.json

systemctl restart docker
systemctl status docker

docker network create observability_network_remote
docker network ls


echo "============== Deploy apache test server =============="

docker run -d \
--name apache-server-remote \
--network observability_network_remote \
-p 80:80 \
httpd

SCRIPT

Vagrant.configure("2") do |config|

  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "1024"
    vb.cpus = 2

  end


    config.vm.define "vg-prom-04" do |kalicluster|
      # https://wiki.ubuntu.com/Releases
      # https://app.vagrantup.com/ubuntu/boxes/jammy64
      kalicluster.vm.box = "ubuntu/jammy64" #22.04
      # https://app.vagrantup.com/ubuntu/boxes/focal64
      # kalicluster.vm.box = "ubuntu/focal64" #20.04 LTS (Focal Fossa) builds      
      # https://app.vagrantup.com/ubuntu/boxes/xenial64
      # kalicluster.vm.box = "ubuntu/bionic64" #18.04      
      # https://app.vagrantup.com/ubuntu/boxes/xenial64
      # kalicluster.vm.box = "ubuntu/xenial64" #16.04
      kalicluster.vm.hostname = "vg-prom-04"
      #bridged network,DHCP disabled, manual IP assignment                  
      # kalicluster.vm.network "public_network", ip: "10.10.8.67"
      #bridged network,DHCP enabled,auto IP assignment
      # kalicluster.vm.network "public_network"
      kalicluster.vm.network "private_network", ip: "192.168.55.11"
      # kalicluster.vm.network "forwarded_port", guest: 80, host: 81
      #Disabling the default /vagrant share can be done as follows:
      # kalicluster.vm.synced_folder ".", "/vagrant", disabled: true
      kalicluster.vm.provider "virtualbox" do |vb|
          vb.name = "vbox-prom-04"
          vb.cpus = 2
          vb.memory = 1024          
          vb.gui = false       
      end
      kalicluster.vm.provision "shell", inline: $ubuntu_docker_script
      # kalicluster.vm.provision "shell", inline: $prometheus_script
      kalicluster.vm.provision "shell", inline: $Prometheus_From_Docker_Image_script #OK

    end

    config.vm.define "vg-prom-05" do |kalicluster|
      # https://wiki.ubuntu.com/Releases
      # https://app.vagrantup.com/ubuntu/boxes/jammy64
      kalicluster.vm.box = "ubuntu/jammy64" #22.04
      # https://app.vagrantup.com/ubuntu/boxes/focal64
      # kalicluster.vm.box = "ubuntu/focal64" #Official Ubuntu 20.04 LTS (Focal Fossa) builds      
      # https://app.vagrantup.com/ubuntu/boxes/xenial64
      # kalicluster.vm.box = "ubuntu/bionic64" #18.04      
      # https://app.vagrantup.com/ubuntu/boxes/xenial64
      # kalicluster.vm.box = "ubuntu/xenial64" #16.04
      kalicluster.vm.hostname = "vg-prom-05"
      #bridged network,DHCP disabled, manual IP assignment                  
      # kalicluster.vm.network "public_network", ip: "10.10.8.67"
      #bridged network,DHCP enabled,auto IP assignment
      # kalicluster.vm.network "public_network"
      kalicluster.vm.network "private_network", ip: "192.168.55.12"
      # kalicluster.vm.network "forwarded_port", guest: 80, host: 81
      #Disabling the default /vagrant share can be done as follows:
      # kalicluster.vm.synced_folder ".", "/vagrant", disabled: true
      kalicluster.vm.provider "virtualbox" do |vb|
          vb.name = "vbox-prom-05"
          vb.cpus = 2
          vb.memory = 1024          
          vb.gui = false       
      end
      kalicluster.vm.provision "shell", inline: $ubuntu_docker_script
      kalicluster.vm.provision "shell", inline: $prometheus_remote_script
      
    end

end

