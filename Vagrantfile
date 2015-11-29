VAGRANTFILE_API_VERSION = "2"

$script = <<'EOF'
    # http://mechanicalcat.net/richard/log/OpenStack/Easy_installation_of_a_new_stack_with_OpenStack_Ansible_Deployment__OSAD/Horizon_development_with_openstack_ansible

    # Update repository information and install git
    apt-get update && apt-get install -y git

    # Clone the OSA repository
    test -e /opt/openstack-ansible || git clone https://github.com/openstack/openstack-ansible /opt/openstack-ansible

    # Bootstrap the env
    cd /opt/openstack-ansible
    ./scripts/bootstrap-aio.sh

    # Bootstrap ansible
    ./scripts/bootstrap-ansible.sh

    # Set the internal address to the external
    sed -i "s/internal_lb_vip_address:.*/internal_lb_vip_address: \"{{ external_lb_vip_address }}\"/" \
      /etc/openstack_deploy/openstack_user_config.yml​

    # disable unnecessary services

    sed -i "s/horizon_container: 2/horizon_container: 1/" /etc/openstack_deploy/openstack_user_config.yml
    sed -i "s/galera_container: 3/galera_container: 1/" /etc/openstack_deploy/openstack_user_config.yml
    sed -i "s/rabbit_mq_container: 3/rabbit_mq_container: 1/" /etc/openstack_deploy/openstack_user_config.yml

    scripts/run-playbooks.sh

    pushd playbooks
    openstack-ansible os-tempest-install.yml
    popd

    grep admin_pass /etc/openstack_deploy/user_secrets.yml
EOF

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu/trusty64"

  config.vm.provider "virtualbox" do |vb|
     vb.customize ["modifyvm", :id, "--memory", "8192"]
     vb.customize ["modifyvm", :id, "--ioapic", "on"]
     vb.cpus = "4"
  end

  config.vm.provision "shell", inline => $script
end
