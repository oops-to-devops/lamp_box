lamp-box
========

Underlying ansible roles: `sa-apache`, `sa-php-fpm`.  Check roles documentation for whole set of parameters to override.

Supported tags:

`sa_apache`, `sa_php_fpm`

Supported parameters overrides:


```
  box_php_pool_properties:
    - {regexp: "^[;]?php_admin_value*memory_limit*", line: "php_admin_value[memory_limit] = 98M"}
    - {regexp: "^[;]?php_admin_value*post_max_size*", line: "php_admin_value[post_max_size] = 30M"}
    - {regexp: "^[;]?php_admin_value*upload_max_filesize*", line: "php_admin_value[upload_max_filesize] = 10M"}

  box_option_install_xdebug: false
  box_php_family: "7.0"

  box_additional_php_extensions: []

  box_project_phars: []

```


Using with vagrant boilerplate (https://github.com/Voronenko/devops-vagrant-ansible-boilerplate)

```

#    https://www.vagrantup.com/docs/provisioning/ansible_common.html
      config.vm.provision "ansible" do |ansible|
          ansible.playbook = "deployment/provisioners/lamp-box/box_lamp.yml"
          ansible.galaxy_role_file = "deployment/provisioners/lamp-box/requirements.yml"
          ansible.galaxy_roles_path = "deployment/provisioners/lamp-box/roles"
          ansible.tags = ["sa_apache", "sa_php_fpm"]
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
