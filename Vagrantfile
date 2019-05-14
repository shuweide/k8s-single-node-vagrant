# -*- mode: ruby -*-
# vi: set ft=ruby :
#
Vagrant.require_version ">= 2.0.2"

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.


boxes = [
    {
        :name => "k8s-test-node-master",
        :eth1 => "192.168.205.10",
        :mem => "4096",
        :cpu => "2"
    }
]

Vagrant.configure(2) do |config|

  config.vm.box = "centos/7"

  boxes.each do |opts|
      config.vm.define opts[:name] do |config|
        config.vm.hostname = opts[:name]

        config.vm.provider "vmware_fusion" do |v|
          v.vmx["memsize"] = opts[:mem]
          v.vmx["numvcpus"] = opts[:cpu]
        end

        config.vm.provider "virtualbox" do |v|
          v.customize ["modifyvm", :id, "--memory", opts[:mem]]
          v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
        end

        config.vm.network :private_network, ip: opts[:eth1]
      end
  end

  config.ssh.insert_key = false
  
  if Vagrant.has_plugin?("vagrant-vbguest")
      config.vbguest.auto_update = false  
  end
  
  config.vm.define "k8s-test-node-master" do |master|
  
      # vagrant plugin install vagrant-vbguest
      master.vm.synced_folder "./simples", "/home/vagrant/simples"
	  master.vm.provision "shell", inline: <<-SHELL
		echo "install docker ce begin"
		sudo yum install -y yum-utils device-mapper-persistent-data lvm2
		sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
		sudo yum makecache fast
		sudo yum install -y docker-ce
		sudo docker version
		sudo systemctl enable docker
		sudo systemctl start docker
		sudo usermod -aG docker vagrant
		echo "install docker ce done"
		
		echo "install kubectl begin"
		curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
		chmod +x ./kubectl
		sudo mv ./kubectl /usr/bin/kubectl
		sudo kubectl version
		echo "install kubectl done"
		
		echo "install minikube begin"
		curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && sudo install minikube-linux-amd64 /usr/bin/minikube
		sudo minikube config set vm-driver none
		sudo minikube version
		echo "install minikube done"
		
		echo "install tools"
		sudo yum install -y socat
		sudo yum install -y vim
		sudo yum install -y epel-release
		sudo yum install -y python-pip
		sudo yum upgrade -y python*
		sudo yum install -y bash-completion
		echo "install tools done"
		
		echo "install docker-compose"
		sudo pip install docker-compose
		echo "instal docker-compose done"
	  SHELL
  end
end
