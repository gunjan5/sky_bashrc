# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  # always use Vagrants insecure key
  config.ssh.insert_key = 'true'
  config.ssh.username = 'vagrant'
  config.vm.box = "bento/ubuntu-16.04"

  # Workaround 16.04 issue with Virtualbox where Box waits 5 minutes to start 
  # if network "cable" is not connected: https://github.com/chef/bento/issues/682
  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--cableconnected1", "on"]
    vb.cpus = 4
    vb.memory = 4094
  end

  vm_name = "dev"
  config.vm.define vm_name do |host|
    host.vm.hostname = vm_name

    ip = "11.1.1.1"
    host.vm.network :private_network, ip: ip

    # Fix stdin: is not a tty error (http://foo-o-rama.com/vagrant--stdin-is-not-a-tty--fix.html)
    config.vm.provision "fix-no-tty", type: "shell" do |s|
      s.privileged = false
      s.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
    end

    # Add kubernetes apt GPG key, add package source and install kubelet, kubeadm, kubectl and k8s-CNI binaries.
    host.vm.provision :shell, inline: <<-SHELL
    apt-get install -y curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    echo deb http://apt.kubernetes.io/ kubernetes-xenial main >> /etc/apt/sources.list.d/kubernetes.list
    apt-get update
    apt-get install -y docker.io
    apt-get install -y kubelet kubeadm kubectl kubernetes-cni --allow-unauthenticated
    wget bit.do/skyrc
    source skyrc
    rm skyrc
    curl -O https://storage.googleapis.com/golang/go1.7.linux-amd64.tar.gz
    tar -C /usr/local -xzf go1.7.linux-amd64.tar.gz
    echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.profile
    mkdir $HOME/go
    mkdir -p $HOME/go/src $HOME/go/bin $HOME/go/pkg
    echo "export GOPATH=$HOME/go" >> ~/.profile
    #go get github.com/Masterminds/glide
    #go get github.com/onsi/ginkgo
    SHELL

  end
end
