require 'yaml'
vconf_file = File.dirname(File.expand_path(__FILE__)) + "/config.yml"
if !File.exist?(vconf_file); raise 'Configuration file not found! Please copy "' + vconf_file + '.example" to "' +  vconf_file + '" and try again.' end
vconf = YAML::load_file(vconf_file)

Vagrant.configure(2) do |config|
  config.vm.box = vconf['vagrant_box']
  config.vm.network :private_network, ip: vconf['vagrant_ip']
  for fport in vconf['vagrant_forwarded_ports'];
    config.vm.network "forwarded_port", guest: fport['guest'], host: fport['host']
  end
  config.ssh.insert_key = false
  config.ssh.forward_agent = true
  config.vm.hostname = vconf['vagrant_hostname']
  config.vm.provider :virtualbox do |v|
    v.name = config.vm.hostname
    v.memory = vconf['vagrant_memory']
    v.cpus = vconf['vagrant_cpus']
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--ioapic", "on"]
  end
  config.vm.synced_folder ".", "/vagrant", disabled: true
  for sfolder in vconf['vagrant_synced_folders'];
    config.vm.synced_folder sfolder['local'], sfolder['remote'], type: "nfs", mount_options: ['vers=4']
  end
  config.vm.provision "ansible" do |ansible|
    ansible.host_key_checking = false
    ansible.playbook = "ansible/main.yml"
    ansible.extra_vars = vconf
    ansible.verbose = 'vv'
  end
end
