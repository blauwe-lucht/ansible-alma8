Vagrant.configure("2") do |config|

    config.vm.define "acs" do |acs|
        acs.vm.box = "generic/alma8"
        acs.vm.hostname = "acs"
        acs.vm.network "private_network", ip: "192.168.15.56"
        
        # Make sure all sensitive info is only readable by user.
        acs.vm.synced_folder ".", "/vagrant", mount_options: ["dmode=700,fmode=600"]

        # Do the initial setup of the ACS with a script.
        acs.vm.provision "shell" do |shell|
            shell.inline = <<-SHELL
                set -euxo pipefail
                
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
    end

end