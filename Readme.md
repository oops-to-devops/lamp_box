lamp-box
========

Using with vagrant boilerplate (https://github.com/Voronenko/devops-vagrant-ansible-boilerplate)

```

#    https://www.vagrantup.com/docs/provisioning/ansible_common.html
      config.vm.provision "ansible" do |ansible|
          ansible.playbook = "deployment/provisioners/lamp-box/box_lamp.yml"
          ansible.galaxy_role_file = "deployment/provisioners/lamp-box/requirements.yml"
          ansible.galaxy_roles_path = "deployment/provisioners/lamp-box/roles"
          ansible.verbose = true
          ansible.groups = {
              "lamp_box" => [vconfig['vagrant_machine_name']]
          }
      end

```


Using with terraform boilerplate (https://github.com/Voronenko/devops-terraform-ansible-boilerplate)

```

resource "null_resource" "lamped_servers" {
  # Changes to any instance of the cluster requires re-provisioning
  triggers {
    cluster_instance_ids = "${join(",", digitalocean_droplet.web.*.id)}"
  }


  provisioner "local-exec" {

    command = "$INFRASTRUCTURE_ROOT_DIR/provisioners/lamp-box/provision_box.sh"

    environment {
      REMOTE_HOST = "${digitalocean_droplet.web.ipv4_address}"
      BOX_DEPLOY_USER = "ubuntu"
      BOX_DEPLOY_PASS = ""
      BOX_PROVIDER = "digitalocean"
    }

  }

  depends_on = ["null_resource.letsencrypted_servers"]
}

```
