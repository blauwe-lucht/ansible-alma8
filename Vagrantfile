Vagrant.configure("2") do |config|

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
                
                # The proxy stuff is needed because this repo is used in a corporate setting
                # where Zscaler is used. Leave it out or change it when you're situation
                # is different.

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

                yum install -y epel-release
                yum install -y ansible
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
