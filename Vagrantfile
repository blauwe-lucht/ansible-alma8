Vagrant.configure("2") do |config|

    host_userdomain = ENV['USERDOMAIN']

    config.vm.define "acs" do |acs|
        acs.vm.box = "generic/alma8"
        acs.vm.hostname = "acs"

        # We're using a private network which will result in a 'host-only' adapter,
        # which will make the VM accessible directly from the host machine.
        # We specify the IP-address so we can easily refer to each machine in Ansible.
        acs.vm.network "private_network", ip: "192.168.15.56"

        # To prevent clashes, we explicitly specify the local SSH port for forwarding.
        # Notice that it is derived from the last two digits of the IP address.
        # See https://realguess.net/2015/10/06/overriding-the-default-forwarded-ssh-port-in-vagrant/
        acs.vm.network "forwarded_port", id: "ssh", guest: 22, host: 21556
        
        # Make sure all sensitive info is only readable by user (SSH requires this
        # when using private keys to connect to a VM).
        acs.vm.synced_folder ".", "/vagrant", mount_options: ["dmode=700,fmode=600"]

        # Do the initial setup of the ACS (Ansible Control Server) with a script.
        acs.vm.provision "shell" do |shell|
            shell.inline = <<-SHELL
                set -euxo pipefail
                
                if [ "#{host_userdomain}" = "CORP" ]; then
                    # The proxy stuff is needed because this repo is used in a corporate setting
                    # where Zscaler is used (= http proxy on host port 9000).

                    # netstat -rn shows something like:
                    # Kernel IP routing table
                    # Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
                    # 0.0.0.0         10.0.2.2        0.0.0.0         UG        0 0          0 eth0
                    # 10.0.2.0        0.0.0.0         255.255.255.0   U         0 0          0 eth0
                    # 192.168.15.0    0.0.0.0         255.255.255.0   U         0 0          0 eth1
                    # We retrieve the Gateway from this output:
                    host_ip=$(netstat -rn | grep '^0.0.0.0' | awk '{print $2}')
                    echo "http_proxy=http://$host_ip:9000" >> /etc/environment
                    echo "https_proxy=http://$host_ip:9000" >> /etc/environment

                    export http_proxy=http://$host_ip:9000
                    export https_proxy=http://$host_ip:9000
                fi

                # By default python 3.6 is installed which results in errors when trying to install ansible-lint.
                # So we'll explicitly install python 3.11.
                # Pip prefers to run in a virtual environment, so we create one for the vagrant user
                # and use that pip to install ansible and ansible-lint.
                dnf install -y epel-release python3.11 python3.11-pip
                sudo -u vagrant bash -c '
                    set -euxo pipefail

                    python3.11 -m venv /home/vagrant/venv-ansible
                    . /home/vagrant/venv-ansible/bin/activate
                    pip install --upgrade pip
                    pip install ansible ansible-lint

                    # Make sure we always use ansible from the virtualenv.
                    echo ". ~/venv-ansible/bin/activate" >> ~/.bashrc
                '
            SHELL
        end
    end

    config.vm.define "node1" do |node1|
        node1.vm.box = "centos/7"
        node1.vm.hostname = "node1"
        node1.vm.network "private_network", ip: "192.168.15.57"
        node1.vm.network "forwarded_port", id: "ssh", guest: 22, host: 21557
    end

    config.vm.define "node2" do |node2|
        node2.vm.box = "generic/alma8"
        node2.vm.hostname = "node2"
        node2.vm.network "private_network", ip: "192.168.15.58"
        node2.vm.network "forwarded_port", id: "ssh", guest: 22, host: 21558
    end
end
