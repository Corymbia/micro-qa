# -*- mode: ruby -*-
# vi: set ft=ruby :
options = {
  :cores => 2,
  :memory => 3000,
}
Vagrant.configure('2') do |config|
    config.vm.box = 'chef/centos-6.6'
    config.vm.hostname = 'micro-qa'
    config.vm.synced_folder '.', '/vagrant', owner: 'root', group: 'root', rsync__args: ["--verbose", "--archive", "--delete", "-z"]
    config.vm.provider :aws do |aws, override|
        override.vm.box_url = 'https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box'
        override.ssh.private_key_path = '~/.ssh/id_rsa'
        aws.access_key_id = 'AKI......'
        aws.secret_access_key = 'JrqLXw.....'
        aws.instance_type = 'm1.xlarge'
        aws.ami = 'emi-1fbfc4ed'
        aws.security_groups = ['default']
        aws.instance_ready_timeout = 600
        aws.endpoint = 'http://my-clc-ip:8773/services/compute'
        aws.keypair_name = 'my-keypair'
        override.ssh.username ='root'
        aws.user_data = File.read('user_data.txt')
        aws.block_device_mapping = [
        {
            :DeviceName => '/dev/sda',
            'Ebs.VolumeSize' => 10
        }]
        aws.tags = {
                Name: 'Micro QA',
        }
    end
    config.vm.network :forwarded_port, guest: 8080, host: 8080
    config.vm.provider :vmware_fusion do |v, override|
        override.vm.network 'public_network'
        v.vmx['memsize'] = options[:memory].to_i
        v.vmx['numvcpus'] = options[:cores].to_i
        v.vmx['vhv.enable'] = 'true'
    end
    config.vm.provider :virtualbox do |v, override|
        override.vm.network 'public_network'
        v.customize [ 'modifyvm', :id, '--memory', options[:memory].to_i, '--cpus', options[:cores].to_i]
    end
    config.omnibus.chef_version = :latest
    config.berkshelf.enabled = true
    config.vm.provision :chef_solo do |chef|
      chef.add_recipe 'micro-qa::jenkins'
      chef.add_recipe 'micro-qa::eutester'
      chef.add_recipe 'micro-qa::console-tests'
      chef.add_recipe 'micro-qa::deploy'
      chef.json = {}
    end
    if Vagrant.has_plugin?('vagrant-cachier')
      config.cache.scope = :box
    end
end
