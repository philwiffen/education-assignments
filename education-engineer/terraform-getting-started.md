# Getting Started with Terraform

Terraform is the most popular language for defining and provisioning Infrastructure as Code (IaC).

With Infrastructure as Code you can build, change, and manage your infrastructure in a safe, consistent, and repeatable way by defining resource configurations that you can version, reuse, and share.

In this guide you will learn to:

1. Install Terraform
1. Create a terraform configuration file
1. Provision some infrastructure
1. Destroy the infrastructure you created

## Prerequisities

To complete this guide you will need:

- Access to a Linux Operating System, or Windows Subsystem for Linux
- Internet access

## Install Terraform

To install Terraform, find the [appropriate package](https://www.terraform.io/downloads.html) for your system and download it as a zip archive.

Extract the terraform binary file from the zip archive you downloaded and put it in a suitable location.

With Terraform installed, let's start creating some infrastructure.

## Create a Terraform configuration file

Most people find it easiest to create a new directory on their local machine and create Terraform configuration code inside the new directory.

```shell
$ mkdir terraform-demo
$ cd terraform-demo
```

Create a file for your Terraform configuration code.

```shell
$ touch main.tf
```

Paste the following lines into the file.

```hcl
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.15.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "tutorial"
  ports {
    internal = 80
    external = 8000
  }
}
```

The configuration above will tell Terraform how to create some infrastructure resources. In this example: a docker container running an NGINX server.

## Create the infrastructure

Initialize Terraform with the `init` command.

```shell
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding kreuzwerker/docker versions matching "~> 2.15.0"...
- Installing kreuzwerker/docker v2.15.0...
- Installed kreuzwerker/docker v2.15.0 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
<https://www.terraform.io/docs/cli/plugins/signing.html>

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

You should check for any errors.

If `terraform init` ran successfully, provision the infrastructure with the `terraform apply` command, and say `yes` when prompted.

```shell
$ terraform apply
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = "json-file"
      + logs             = false
      + must_run         = true
      + name             = "tutorial"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 8000
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id           = (known after apply)
      + keep_locally = false
      + latest       = (known after apply)
      + name         = "nginx:latest"
      + output       = (known after apply)
      + repo_digest  = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 9s [id=sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=7999862efaf5c3003458232a2261994b8521296e7949050067c05c4e2a342738]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

The `terraform init` command will take up to a few minutes to run and will display a message indicating that the resource was created.

## Destroy the infrastructure

Finally, destroy the infrastructure by running `terraform destroy`, and say yes when prompted.

```shell
$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest]
docker_container.nginx: Refreshing state... [id=7999862efaf5c3003458232a2261994b8521296e7949050067c05c4e2a342738]

Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":

  # docker_container.nginx has been changed
  ~ resource "docker_container" "nginx" {
      + dns               = []
      + dns_opts          = []
      + dns_search        = []
      + group_add         = []
        id                = "7999862efaf5c3003458232a2261994b8521296e7949050067c05c4e2a342738"
      + links             = []
      + log_opts          = {}
        name              = "tutorial"
      + storage_opts      = {}
      + sysctls           = {}
      + tmpfs             = {}
        # (31 unchanged attributes hidden)

        # (1 unchanged block hidden)
    }

Unless you have made equivalent changes to your configuration, or ignored the relevant attributes using ignore_changes, the
following plan may include actions to undo or respond to these changes.

────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "7999862efaf5" -> null
      - id                = "7999862efaf5c3003458232a2261994b8521296e7949050067c05c4e2a342738" -> null
      - image             = "sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85" -> null
      - init              = false -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "tutorial" -> null
      - network_data      = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null
      - storage_opts      = {} -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 8000 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id           = "sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest" -> null
      - keep_locally = false -> null
      - latest       = "sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85" -> null
      - name         = "nginx:latest" -> null
      - repo_digest  = "nginx@sha256:0d17b565c37bcbd895e9d92315a05c1c3c9a29f762b011a10c54a66cd53c9b31" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=7999862efaf5c3003458232a2261994b8521296e7949050067c05c4e2a342738]
docker_container.nginx: Destruction complete after 1s
docker_image.nginx: Destroying... [id=sha256:605c77e624ddb75e6110f997c58876baa13f8754486b461117934b24a9dc3a85nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

Terraform will destroy the infrastructure resources it created earlier.

## Next steps

You learned the core building blocks of Infrastructure as Code by creating a configuration file, and provisioning and destroying infrastructure resources with Terraform. Next, learn how to [create your own configuration](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started).
