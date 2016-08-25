# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.define "rtalur-dev-single-f24" do |testvm|
      testvm.vm.box = "fedora/24-cloud-base"
      testvm.vm.hostname = "rtalur-dev-single-f24"
      testvm.vm.synced_folder ".", "/vagrant", disabled: true
      testvm.vm.synced_folder "/home/rtalur/Code", "/syncdir/Code", type: "nfs", mount_options: ['vers=3', 'udp', 'nolock', 'noatime']
      #testvm.bindfs.bind_folder "/syncdir/Code", "/home/rtalur/Code",  user: "rtalur", group: "rtalur", after: :provision


      if Vagrant.has_plugin?("vagrant-cachier")
        config.cache.scope = :box
        config.cache.auto_detect = false
        config.cache.enable :dnf
        config.cache.synced_folder_opts = {
          type: :nfs,
          mount_options: ['rw', 'vers=3', 'tcp', 'nolock'],
          export_options: ['async', 'insecure', 'no_subtree_check', 'no_acl', 'no_root_squash']
        }
      end

      # Define basic config for VM, memory, cpu, storage pool
      testvm.vm.provider "libvirt" do |lv,override|
        lv.storage_pool_name = "default"
        lv.memory = 4096
        lv.cpus = 2
        lv.default_prefix = ''

        override.vm.network "private_network",
        auto_config: true,
        ip: "192.168.21.2",
        libvirt__network_name: "rtalur_vagrant_single_dev",
        libvirt__netmask: "255.255.255.0",
        libvirt__dhcp_enabled: false,
        libvirt__forward_mode: "none"

        # We need a brick partition, lets have a 5G disk for that.
        # If you need more bricks, just add more letters to the
        # string below.
        "bcdefghijklm".split("").each do |i|
          lv.storage :file,
          #:path           => "",
          #:allow_existing => "",
          :device         => "vd#{i}",
          :size           => "1G",
          :type           => "qcow2",
          :bus            => "virtio",
          :cache          => "default"
        end
      end

      testvm.vm.provision "shell", preserve_order: true, inline: <<-SHELL
          dnf install -y python2 python2-dnf libselinux-python libsemanage-python
      SHELL
      # Let's provision
      testvm.vm.provision "ansible", preserve_order: true  do |setup|
        #setup.verbose = "v"
        setup.playbook = "setup.yml"
      end
#Things that I do manually
#Create symlink ls -s /syncdir/Code /home/rtalur/Code 


    end
end
