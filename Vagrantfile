# vim: tabstop=4 expandtab shiftwidth=4 softtabstop=4
#
# Vagrantfile

# This guide is optimized for Vagrant 1.7 and above.
# Although versions 1.6.x should behave very similarly, it is recommended
# to upgrade instead of disabling the requirement below.
#[
#  { :name => "vagrant-vbguest", :version => ">= 0.15.0" },
#  { :name => "vagrant-share", :version => ">= 1.1.9" },
#].each do |plugin|
#
#  if not Vagrant.has_plugin?(plugin[:name], plugin[:version])
#    raise "#{plugin[:name]} #{plugin[:version]} is required. Please run `vagrant plugin install #{plugin[:name]}`"
#  end
#end

Vagrant.require_version ">= 2.0.0"
Vagrant.configure(2) do |config|
  # xCAT master (OpenHPC) (xCAT)
  # Setup network
  subnet_prefix="172.16"
  #mvlan_prefix="#{subnet_prefix}.99"
  cvlan_prefix="#{subnet_prefix}.1"
  cvlan_netmask="255.255.0.0"

  config.vm.define "xc-master" do |xcmaster|
    xcmaster.vm.box = "centos/7"
    xcmaster.vm.hostname = 'xc-master.vagrant'
    xcmaster.vm.network "private_network",
                        ip: "172.16.0.254",
                        netmask: "255.255.0.0",
                        adapter: 2,
                        virtualbox__intnet: "cvlan-xc"
    xcmaster.vm.network "private_network",
                        ip: "172.17.0.3",
                        netmask: "255.255.255.0",
                        adapter: 3
    xcmaster.vm.provider "virtualbox" do |v|
        v.name = "xc-master"
        v.memory = 4096
        v.cpus = 2
        v.customize [
               'modifyvm', :id,
               '--nic1', 'nat',
               '--nic2', 'intnet',
               '--intnet1', 'cvlan-xc',
               '--nic3', 'hostonly',
               '--hostonlyadapter1', 'vboxnet3'
        ]
    end
  end

  # Warewulf master (OpenHPC) (Warewulf)
  # Setup network
  subnet_prefix="172.16"
  #mvlan_prefix="#{subnet_prefix}.99"
  cvlan_prefix="#{subnet_prefix}.1"
  cvlan_netmask="255.255.0.0"

  config.vm.define "ww-master" do |wwmaster|
    wwmaster.vm.box = "centos/7"
    wwmaster.vm.hostname = 'ww-master.vagrant'
    wwmaster.vm.network "private_network",
                        ip: "172.16.0.254",
                        netmask: "255.255.0.0",
                        adapter: 2,
                        virtualbox__intnet: "cvlan-ww"
    wwmaster.vm.network "private_network",
                        ip: "172.17.0.2",
                        netmask: "255.255.255.0",
                        adapter: 3
    wwmaster.vm.provider "virtualbox" do |v|
        v.name = "ww-master"
        v.memory = 4096
        v.cpus = 2
        v.customize [
               'modifyvm', :id,
               '--nic1', 'nat',
               '--nic2', 'intnet',
               '--intnet1', 'cvlan-ww',
               '--nic3', 'hostonly',
               '--hostonlyadapter1', 'vboxnet3'
        ]
    end
  end
  (1..8).each do |ww_idx|
      config.vm.define "ww#{ww_idx}", autostart: (ww_idx>1 ? false : true) do |ww|
          ww.vm.box = "centos/7"
          ww.vm.host_name = "ww#{ww_idx}.localdomain"
          # Compute / application network
          ww.vm.network 'private_network',
              type: 'dhcp',
              adapter: 1,
              mac: "000AAAAAAA0#{ww_idx}",
              virtualbox__intnet: "cvlan-ww"

#          ww.vm.network "forwarded_port",
#              id: "ssh",
#              adapter: 2,
#              guest: 22,
#              host: "223#{ww_idx}",
#              host_ip: "127.0.0.1",
#              auto_correct: false

          ww.vm.provider "virtualbox" do |v|
              v.name = "ww#{ww_idx}"
              v.memory = 2048
              v.cpus = 1
              v.customize [
                'modifyvm', :id,
                '--nic1', 'intnet',
                '--intnet1', 'cvlan-ww',
                '--nic2', 'nat',
                #'--natpf2', "ssh,tcp,127.0.0.1,223#{ww_idx},,22",
                '--boot1', 'net',
                '--boot2', 'none',
                '--boot3', 'none',
                '--boot4', 'none'
              ]

          end
      end
  end

  # Virtual Box Settings
  config.vbguest.auto_update = true

  config.ssh.insert_key = true

  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"
    ansible.playbook = "vagrant_playbook.yaml"
  end
end
