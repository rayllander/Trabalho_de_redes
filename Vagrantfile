Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 2
  end

  config.vm.define "dns" do |dns|
    dns.vm.provider "virtualbox" do |vb|
        vb.name = "DNS"
    end
    dns.vm.network "public_network", ip: "192.168.56.51"
    dns.vm.synced_folder "./keys", "/keys"
    dns.vm.synced_folder ".", "/vagrant", disabled: true
    dns.vm.provision "shell",
      inline: "cat /keys/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
  end

  config.vm.define "web_server" do |web_server|
    web_server.vm.provider "virtualbox" do |vb|
        vb.name = "Web_Server"
    end
    web_server.vm.network "public_network", ip: "192.168.56.52"
    web_server.vm.network "forwarded_port", guest: 81, host: 3000
    web_server.vm.synced_folder "./keys", "/keys"
    web_server.vm.synced_folder ".", "/vagrant", disabled: true
    web_server.vm.provision "shell",
      inline: "cat /keys/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    web_server.vm.provision "shell", inline: <<-SHELL
      echo "nameserver 192.168.56.51" | tee /etc/resolv.conf
    SHELL
  end

  config.vm.define "nfs" do |nfs|
    nfs.vm.provider "virtualbox" do |vb|
        vb.name = "NFS"
    end
    nfs.vm.network "public_network", ip: "192.168.56.53"
    nfs.vm.synced_folder "./keys", "/keys"
    nfs.vm.synced_folder ".", "/vagrant", disabled: true
    nfs.vm.provision "shell",
      inline: "cat /keys/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    nfs.vm.provision "shell", inline: <<-SHELL
      echo "nameserver 192.168.56.51" | tee /etc/resolv.conf
    SHELL
  end

  config.vm.define "proxy_squid" do |proxy_squid|
    proxy_squid.vm.provider "virtualbox" do |vb|
        vb.name = "Proxy_Squid"
    end
    proxy_squid.vm.network "public_network", ip: "192.168.56.54"
    proxy_squid.vm.synced_folder "./keys", "/keys"
    proxy_squid.vm.synced_folder ".", "/vagrant", disabled: true
    proxy_squid.vm.provision "shell",
      inline: "cat /keys/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
    proxy_squid.vm.provision "shell", inline: <<-SHELL
      echo "nameserver 192.168.56.51" | tee /etc/resolv.conf
    SHELL
  end

  config.vm.define "ansible" do |ansible|
    ansible.vm.provider "virtualbox" do |vb|
        vb.name = "Ansible"
    end
    ansible.vm.network "public_network", ip: "192.168.56.50"
    ansible.vm.synced_folder "./keys", "/keys"
    ansible.vm.synced_folder "./ansible", "/ansible"
    ansible.vm.synced_folder ".", "/vagrant", disabled: true
    ansible.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y software-properties-common
      apt-add-repository --yes --update ppa:ansible/ansible
      apt-get install -y ansible
    SHELL
    ansible.vm.provision "shell",
    inline: "ansible-playbook -i /ansible/hosts /ansible/playbook.yml --ssh-extra-args='-o StrictHostKeyChecking=no'"
  end
end
