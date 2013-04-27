# -*- mode: ruby -*-
# vi: set ft=ruby :

#module Vagrant
#  module Guest
#    class FreeBSD < Base
#      def configure_networks(networks)
#        vm.channel.sudo("sed -i '' -e '/^#VAGRANT-BEGIN/,/^#VAGRANT-END/ d' /etc/rc.conf")
#
#        networks.each do |network|
#          entry = TemplateRenderer.render("guests/freebsd/network_#{network[:type]}",
#                                          :options => network)
#          temp = Tempfile.new("vagrant")
#          temp.binmode
#          temp.write(entry)
#          temp.close
#          vm.channel.upload(temp.path, "/tmp/vagrant-network-entry")
#          vm.channel.sudo("su -m root -c 'cat /tmp/vagrant-network-entry >> /etc/rc.conf'")
#          if network[:type].to_sym == :static
#            vm.channel.sudo("ifconfig em#{network[:interface]} inet #{network[:ip]} netmask #{network[:netmask]}")
#          elsif network[:type].to_sym == :dhcp
#            vm.channel.sudo("dhclient em#{network[:interface]}")
#          end
#        end
#      end
#    end
#  end
#end

require "vagrant"

if Vagrant::VERSION < "1.2.1"
    raise "The Omnibus Build Lab is only compatible with Vagrant 1.2.1+"
end

@host_project_path = File.expand_path("..", __FILE__)
@guest_project_path = "/home/vagrant/#{File.basename(@host_project_path)}"
@project_name = "chef"

Vagrant.configure("2") do |config|

  config.vm.hostname = "#{@project_name}-omnibus-build-lab"

  host_ip_counter = 100

  config.vm.define 'ubuntu-10.04' do |c|
    c.vm.box     = "opscode-ubuntu-10.04"
    c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/boxes/opscode-ubuntu-10.04.box"
    configure_vm c
  end

  config.vm.define 'ubuntu-11.04' do |c|
    c.vm.box = "opscode-ubuntu-11.04"
    c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/boxes/opscode-ubuntu-11.04.box"
    configure_vm c
  end

  config.vm.define 'ubuntu-12.04' do |c|
    c.vm.box = "canonical-ubuntu-12.04"
    c.vm.box_url = "http://cloud-images.ubuntu.com/vagrant/precise/current/precise-server-cloudimg-amd64-vagrant-disk1.box"
    configure_vm c
  end

  config.vm.define 'centos-5' do |c|
    c.vm.box = "opscode-centos-5.8"
    c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/opscode_centos-5.8_chef-11.2.0.box"
    configure_vm c
  end

  config.vm.define 'centos-6' do |c|
    c.vm.box = "opscode-centos-6.3"
    c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/opscode_centos-6.3_chef-11.2.0.box"
    configure_vm c
  end

  config.vm.define 'freebsd-9.1-i386' do |c|
    c.vm.box     = "taximagic-freebsd-9.1-i386"
    c.vm.guest   = :freebsd
    #c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/boxes/opscode-centos-6.2.box"
    c.vm.network   :private_network, ip: "172.30.30.#{host_ip_counter+=1}"
    c.vm.provision :shell, :inline => "sed -i '' -E 's%^([^#].*):setenv=%\1:setenv=PACKAGESITE=ftp://ftp.freebsd.org/pub/FreeBSD/ports/amd64/packages-9-stable/Latest,%' /etc/login.conf"
    configure_vm c, :use_nfs => true
  end

  config.vm.define 'freebsd-9.1' do |c|
    c.vm.box     = "taximagic-freebsd-9.1"
    c.vm.guest   = :freebsd
    #c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/boxes/opscode-centos-6.2.box"
    c.vm.network   :private_network, ip: "172.30.30.#{host_ip_counter+=1}"
    c.vm.provision :shell, :inline => "sed -i '' -E 's%^([^#].*):setenv=%\1:setenv=PACKAGESITE=ftp://ftp.freebsd.org/pub/FreeBSD/ports/amd64/packages-9-stable/Latest,%' /etc/login.conf"
    configure_vm c, :use_nfs => true
  end

  config.vm.define 'freebsd-8.3' do |c|
    c.vm.box     = "taximagic-freebsd-8.3"
    c.vm.guest   = :freebsd
    #c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/boxes/opscode-centos-6.2.box"
    c.vm.network   :private_network, ip: "172.30.30.#{host_ip_counter+=1}"
    c.vm.provision :shell, :inline => "sed -i '' -E 's%^([^#].*):setenv=%\1:setenv=PACKAGESITE=ftp://ftp.freebsd.org/pub/FreeBSD/ports/amd64/packages-8-stable/Latest,%' /etc/login.conf"
    configure_vm c, :use_nfs => true
  end

  config.vm.define 'freebsd-8.3-i386' do |c|
    c.vm.box     = "taximagic-freebsd-8.3-i386"
    c.vm.guest   = :freebsd
    #c.vm.box_url = "http://opscode-vm.s3.amazonaws.com/vagrant/boxes/opscode-centos-6.2.box"
    c.vm.network   :private_network, ip: "172.30.30.#{host_ip_counter+=1}"
    c.vm.provision :shell, :inline => "sed -i '' -E 's%^([^#].*):setenv=%\1:setenv=PACKAGESITE=ftp://ftp.freebsd.org/pub/FreeBSD/ports/i386/packages-8-stable/Latest,%' /etc/login.conf"
    configure_vm c, :use_nfs => true
  end

end

def configure_vm(config, options={})

  use_nfs = options[:use_nfs] || false

  config.vm.provider :virtualbox do |vb|
    # Give enough horsepower to build without taking all day.
    vb.customize [
      "modifyvm", :id,
      "--memory", "1536",
      "--cpus", "2"
    ]
  end

  # Ensure a recent version of the Chef Omnibus packages are installed
  config.omnibus.chef_version = "11.4.0"

  # Enable the berkshelf-vagrant plugin
  config.berkshelf.enabled = true
  # The path to the Berksfile to use with Vagrant Berkshelf
  config.berkshelf.berksfile_path = "./Berksfile"

  config.ssh.max_tries = 40
  config.ssh.timeout   = 120
  config.ssh.forward_agent = true

  @host_project_path = File.expand_path("..", __FILE__)
  @guest_project_path = "/home/vagrant/#{File.basename(@host_project_path)}"

  vagrant_home = "#{ENV['HOME']}/.vagrant"
  begin Dir.mkdir(vagrant_home) rescue Exception end
  config.vm.synced_folder vagrant_home, "/vagrant", :nfs => use_nfs, id: "vagrant-root"
  config.vm.synced_folder @host_project_path, @guest_project_path, :nfs => use_nfs

  # prepare VM to be an Omnibus builder
  config.vm.provision :chef_solo do |chef|
    chef.json = {
      "omnibus" => {
        "build_user" => "vagrant",
        "build_dir" => @guest_project_path,
        "install_dir" => "/opt/#{@project_name}"
      }
    }
    chef.nfs = use_nfs

    chef.run_list = [
      "recipe[omnibus::default]"
    ]
  end

  # We have to nuke any chef omnibus packages (used during provisioning) before
  # we build new chef omnibus packages!
  config.vm.provision :shell, :inline => <<-REMOVE_OMNIBUS
    if command -v dpkg &>/dev/null;
    then
      sudo dpkg -P #{@project_name} || true
    else
      sudo rpm -ev #{@project_name} || true
    fi
  REMOVE_OMNIBUS

  config.vm.provision :shell, :inline => <<-OMNIBUS_BUILD
    sudo mkdir -p /opt/#{@project_name}
    sudo chown vagrant /opt/#{@project_name}
    export PATH=/usr/local/bin:$PATH
    cd #{@guest_project_path}
    su vagrant -c "bundle install --binstubs"
    su vagrant -c "bin/omnibus build project #{@project_name}"
  OMNIBUS_BUILD
end
