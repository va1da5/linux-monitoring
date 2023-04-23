# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

    config.ssh.insert_key = false

    config.vm.synced_folder ".", "/vagrant", disabled: true

    config.vm.provider :libvirt do |v|
      # v.memory = 256
      # v.linked_clone = true
    end

    config.vm.define "ubuntu" do |guest|
        guest.vm.provider "libvirt" do |vm|
            vm.memory = 1024
            vm.cpus = 1
        end

        guest.vm.box = "generic/ubuntu2204"
        guest.vm.hostname = "ubuntu"
        guest.vm.network "private_network", ip: "192.168.122.20"
    end

    # config.vm.define "alma" do |guest|
    #     guest.vm.provider "libvirt" do |vm|
    #         vm.memory = 1024
    #         vm.cpus = 1
    #     end

    #     guest.vm.box = "generic/alma9"
    #     guest.vm.hostname = "alma"
    #     guest.vm.network "private_network", ip: "192.168.122.30"
    # end

    config.vm.define "elk" do |guest|
      guest.vm.provider "libvirt" do |vm|
          vm.memory = 4096
          vm.cpus = 2
      end

      guest.vm.box = "generic/ubuntu2204"
      guest.vm.hostname = "elk"
      guest.vm.network "private_network", ip: "192.168.122.10"
  end
  end
