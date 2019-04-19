# -*- mode: ruby -*-
# vim: set ft=ruby :
# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
   :inetRouter => {
        :box_name => "centos/7",
        #:public => {:ip => '10.10.10.1', :adapter => 1},
        :net => [
                   { adapter: 2, virtualbox__intnet: "router-net" },
                   { adapter: 3, virtualbox__intnet: "router-net" },
                ],
        bonding: {
                 conName: "mybond0",
                 address: "192.168.255.1/30",
                 devices: "eth1 eth2",
        },
  },
  :centralRouter => {
        :box_name => "centos/7",
        :net => [
                   { adapter: 2, virtualbox__intnet: "router-net" },
                   { adapter: 3, virtualbox__intnet: "router-net" },
                   {ip: '192.168.255.2', adapter: 4, netmask: "255.255.255.252", virtualbox__intnet: "router-net"},
                   {ip: '192.168.0.1', adapter: 5, netmask: "255.255.255.240", virtualbox__intnet: "dircentral-net"},
                   {ip: '192.168.1.1', adapter: 6, netmask: "255.255.255.128", virtualbox__intnet: "office2router-net"},
                   {ip: '192.168.2.1', adapter: 7, netmask: "255.255.255.192", virtualbox__intnet: "office1router-net"},
                ],
        bonding: {
        conName: "mybond0",
        address: "192.168.255.2/30",
        devices: "eth1 eth2",
        },
  },
  :office1Router => {
        :box_name => "centos/7",
        :net => [  
                   #office1
                   {ip: '192.168.2.1', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "devof1-net"},
                   {ip: '192.168.2.65', adapter: 3, netmask: "255.255.255.192", virtualbox__intnet: "testseroff1-net"},
                   {ip: '192.168.2.129', adapter: 4, netmask: "255.255.255.192", virtualbox__intnet: "managoff1-net"},
                   {ip: '192.168.2.193', adapter: 5, netmask: "255.255.255.192", virtualbox__intnet: "hardwareoff1-net"},
                   #office2
                   {ip: '192.168.1.1', adapter: 6, netmask: "255.255.255.128", virtualbox__intnet: "off2server-net"},
                   #central
                   {ip: '192.168.0.1', adapter: 7, netmask: "255.255.255.240", virtualbox__intnet: "centralserver-net"},
                ]
  },
  :office2Router => {
        :box_name => "centos/7",
        :net => [
                   #office2
                   {ip: '192.168.1.1', adapter: 2, netmask: "255.255.255.128", virtualbox__intnet: "devof2-net"},
                   {ip: '192.168.1.129', adapter: 3, netmask: "255.255.255.192", virtualbox__intnet: "testseroff2-net"},
                   {ip: '192.168.1.193', adapter: 4, netmask: "255.255.255.192", virtualbox__intnet: "hardwareoff2-net"},
                   #office1
                   {ip: '192.168.2.1', adapter: 5, netmask: "255.255.255.192", virtualbox__intnet: "off1server-net"},
                   #central
                   {ip: '192.168.0.1', adapter: 6, netmask: "255.255.255.240", virtualbox__intnet: "centralserver-net"},
        ]
  },
  :centralServer => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.0.2', adapter: 2, netmask: "255.255.255.240", virtualbox__intnet: "centralserver-net"},
        ]
  },
  :office1Server => {
        :box_name => "centos/7",
        :net => [
                   { ip: '192.168.2.2', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "off1server-net" },
        ]
  },
  :office2Server => {
        :box_name => "centos/7",
        :net => [
                   { ip: '192.168.1.2', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "office2server-net" },
        ]
   },
   :testServer1 => {
        :box_name => "centos/7",
        :net => [
                   { ip: '192.168.2.66', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "test-net" },
        ],
        vlans: [
            { vlanID: 2, interface: "eth1", ip: "10.10.10.1/24"},
        ],
  },
  :testServer2 => {
        :box_name => "centos/7",
        :net => [
                  { ip: '192.168.2.67', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "test-net" },
        ],
        vlans: [
            { vlanID: 3, interface: "eth1", ip: "10.10.10.1/24"},           ]
  },
  :testClient1 => {
        :box_name => "centos/7",
        :net => [
                  { ip: '192.168.2.3', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "test-net" },
        ],
        vlans: [
            { vlanID: 2, interface: "eth1", ip: "10.10.10.254/24"},
        ],
  },
  :testClient2 => {
        :box_name => "centos/7",
        :net => [
                  { ip: '192.168.2.4', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "test-net" },
        ],
        vlans: [
                  { vlanID: 3, interface: "eth1", ip: "10.10.10.254/24"},
        ],
   },
  
}

 Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|
        config.vm.define boxname do |box|

        box.vm.box = boxconfig[:box_name]
      
        box.vm.host_name = boxname.to_s

        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
        
        if boxconfig.key?(:public)
          box.vm.network "public_network", boxconfig[:public]
        end

        box.vm.provision "shell", inline: <<-SHELL
          mkdir -p ~root/.ssh
                cp ~vagrant/.ssh/auth* ~root/.ssh
        SHELL
        
        case boxname.to_s
        when "inetRouter"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            eco "net.ipv4.ip_forward=1" >> /etc/sysctl.conf
            sysctl -p /etc/sysctl.conf
            # start bonding
            echo -e 'NM_CONTROLED=no\nDEVICE=bond0\nONBOOT=yes\nTYPE=Bond\nBONDING_MASTER=yes\nIPADDR=192.168.255.1\nPREFIX=30\nBOOTPROTO=static\nBONDING_OPTS="mode=1 miimon=100 fail_over_mac=1"' > /etc/sysconfig/network-scripts/ifcfg-bond0
            echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nONBOOT=yes\nDEVICE=eth1\nMASTER=bond0\nSLAVE=yes" > /etc/sysconfig/network-scripts/ifcfg-eth1
            echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nONBOOT=yes\nDEVICE=eth2\nMASTER=bond0\nSLAVE=yes" > /etc/sysconfig/network-scripts/ifcfg-eth2
            systemctl restart network
            iptables -t nat -A POSTROUTING ! -d 192.168.0.0/16 -o eth0 -j MASQUERADE
        ip route add 192.168.0.0/16 via 192.168.255.2
        # echo "192.168.0.0/16 via 192.168.255.2 dev eth2" >> /etc/sysconfig/network-scripts/route-eth2
        SHELL
        when "centralRouter"
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          echo "net.ipv4.conf.all.forwarding=1" >> /etc/sysctl.conf
                      echo -e 'NM_CONTROLED=no\nDEVICE=bond0\nONBOOT=yes\nTYPE=Bond\nBONDING_MASTER=yes\nIPADDR=192.168.255.2\nPREFIX=30\nGATEWAY=192.168.255.1\nBOOTPROTO=static\nBONDING_OPTS="mode=1 miimon=100 fail_over_mac=1"' > /etc/sysconfig/network-scripts/ifcfg-bond0
          echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nONBOOT=yes\nDEVICE=eth1\nMASTER=bond0\nSLAVE=yes" > /etc/sysconfig/network-scripts/ifcfg-eth1
          echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nONBOOT=yes\nDEVICE=eth5\nMASTER=bond0\nSLAVE=yes" > /etc/sysconfig/network-scripts/ifcfg-eth5
        systemctl restart network
        SHELL
        when "office1Router"
        box.vm.provision "shell", run: "always", inline: <<-SHELL
            #echo -e 'NM_CONTROLED=no\nDEVICE=bond0\nONBOOT=yes\nTYPE=Bond\nBONDING_MASTER=yes\nIPADDR=192.168.255.2\nPREFIX=30\nGATEWAY=192.168.255.1\nBOOTPROTO=static\nBONDING_OPTS="mode=1 miimon=100 fail_over_mac=1"' > /etc/sysconfig/network-scripts/ifcfg-bond0
            #echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nONBOOT=yes\nDEVICE=eth1\nMASTER=bond0\nSLAVE=yes" > /etc/sysconfig/network-scripts/ifcfg-eth1
            #echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nONBOOT=yes\nDEVICE=eth5\nMASTER=bond0\nSLAVE=yes" > /etc/sysconfig/network-scripts/ifcfg-eth5
            #echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0 
            # echo "GATEWAY=192.168.255.1" >> /etc/sysconfig/network-scripts/ifcfg-eth1
            # ip ro add default via 192.168.255.1 dev eth1 metric 1
            # Central office
            #echo -e "BOOTPROTO=static\nONBOOT=yes\nIPADDR=192.168.0.33\nNETMASK=255.255.255.240\nDEVICE=eth2:0" > /etc/sysconfig/network-scripts/ifcfg-eth2:0
            #echo -e "BOOTPROTO=static\nONBOOT=yes\nIPADDR=192.168.0.65\nNETMASK=255.255.255.192\nDEVICE=eth2:1" > /etc/sysconfig/network-scripts/ifcfg-eth2:1
            # Office 2      
            #echo -e "BOOTPROTO=static\nONBOOT=yes\nIPADDR=192.168.1.129\nNETMASK=255.255.255.192\nDEVICE=eth3:0" > /etc/sysconfig/network-scripts/ifcfg-eth3:0
            #echo -e "BOOTPROTO=static\nONBOOT=yes\nIPADDR=192.168.1.193\nNETMASK=255.255.255.192\nDEVICE=eth3:1" > /etc/sysconfig/network-scripts/ifcfg-eth3:1
            # Office 1
            #echo -e "BOOTPROTO=static\nONBOOT=yes\nIPADDR=192.168.2.65\nNETMASK=255.255.255.192\nDEVICE=eth4:0" > /etc/sysconfig/network-scripts/ifcfg-eth4:0
            #echo -e "BOOTPROTO=static\nONBOOT=yes\nIPADDR=192.168.2.129\nNETMASK=255.255.255.192\nDEVICE=eth4:1" > /etc/sysconfig/network-scripts/ifcfg-eth4:1
            #echo -e "BOOTPROTO=static\nONBOOT=yes\nIPADDR=192.168.2.193\nNETMASK=255.255.255.192\nDEVICE=eth4:2" > /etc/sysconfig/network-scripts/ifcfg-eth4:2
             ip ro add default via 192.168.2.1 dev eth1 metric 1
      systemctl restart network
      SHELL
      when "office2Router"
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
          #echo -e "BOOTPROTO=none\nONBOOT=yes\nIPADDR=192.168.1.1\nNETMASK=255.255.255.128\nDEVICE=eth1:0" > /etc/sysconfig/network-scripts/ifcfg-eth1:0
          #echo -e "BOOTPROTO=none\nONBOOT=yes\nIPADDR=192.168.1.129\nNETMASK=255.255.255.192\nDEVICE=eth1:1" > /etc/sysconfig/network-scripts/ifcfg-eth1:1
          #echo -e "BOOTPROTO=none\nONBOOT=yes\nIPADDR=192.168.1.193\nNETMASK=255.255.255.192\nDEVICE=eth1:2" > /etc/sysconfig/network-scripts/ifcfg-eth1:2
          #echo -e "BOOTPROTO=none\nONBOOT=yes\nIPADDR=192.168.0.1\nNETMASK=255.255.255.240\nDEVICE=eth2" > /etc/sysconfig/network-scripts/ifcfg-eth2
          #echo -e "BOOTPROTO=none\nONBOOT=yes\nIPADDR=192.168.2.1\nNETMASK=255.255.255.192\nDEVICE=eth3" > /etc/sysconfig/network-scripts/ifcfg-eth3           echo "GATEWAY=192.168.1.129" >> /etc/sysconfig/network-scripts/ifcfg-eth1
          systemctl stop NetworkManager
          systemctl disable NetworkManager
          sleep 5
          systemctl restart network
      SHELL
      when "centralServer"
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          ip ro add default via 192.168.0.1 dev eth1 metric 1
          #echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
          #echo "GATEWAY=192.168.0.1" >> /etc/sysconfig/network-scripts/ifcfg-eth1
          #systemctl restart network
      SHELL
      when "office1Server"    
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          ip ro add default via 192.168.2.1 dev eth1 metric 1
          #echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0 
          #echo "GATEWAY=192.168.2.1" >> /etc/sysconfig/network-scripts/ifcfg-eth1
          #systemctl restart network
      SHELL
      when "office2Server"    
        box.vm.provision "shell", run: "always", inline: <<-SHELL
          echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0 
          echo "GATEWAY=192.168.1.1" >> /etc/sysconfig/network-scripts/ifcfg-eth1
          systemctl stop NetworkManager
          systemctl disable NetworkManager
          sleep 5
          systemctl restart network
            SHELL
      when "testServer1"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
          echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nVLAN=yes\nONBOOT=yes\nIPADDR=10.10.10.1\nNETMASK=255.255.255.0\nDEVICE=eth1.1" > /etc/sysconfig/network-scripts/ifcfg-eth1.1
          #systemctl restart network
            SHELL
        when "testServer2"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
          echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nVLAN=yes\nONBOOT=yes\nIPADDR=10.10.10.1\nNETMASK=255.255.255.0\nDEVICE=eth1.2" > /etc/sysconfig/network-scripts/ifcfg-eth1.2
        #systemctl restart network
            SHELL
        when "testClient1"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
          echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nVLAN=yes\nONBOOT=yes\nIPADDR=10.10.10.254\nNETMASK=255.255.255.0\nDEVICE=eth1.1" > /etc/sysconfig/network-scripts/ifcfg-eth1.1
            # cp -a ./some_path/id_rsa /home/vagrant/.ssh/
            # sudo systemctl restart network
            SHELL
        when "testClient2"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
          echo -e "NM_CONTROLED=no\nBOOTPROTO=static\nVLAN=yes\nONBOOT=yes\nIPADDR=10.10.10.254\nNETMASK=255.255.255.0\nDEVICE=eth1.2" > /etc/sysconfig/network-scripts/ifcfg-eth1.2
          # sudo systemctl restart network 
          sed -i '65s/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
          systemctl restart sshd
         SHELL
    end
  end
end
end
