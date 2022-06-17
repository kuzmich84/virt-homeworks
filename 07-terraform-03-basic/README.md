# Домашнее задание к занятию "7.3. Основы и принцип работы Терраформ"

## Задача 1. Создадим бэкэнд в S3 (необязательно, но крайне желательно).

Если в рамках предыдущего задания у вас уже есть аккаунт AWS, то давайте продолжим знакомство со взаимодействием
терраформа и aws. 

1. Создайте s3 бакет, iam роль и пользователя от которого будет работать терраформ. Можно создать отдельного пользователя,
а можно использовать созданного в рамках предыдущего задания, просто добавьте ему необходимы права, как описано 
[здесь](https://www.terraform.io/docs/backends/types/s3.html).
1. Зарегистрируйте бэкэнд в терраформ проекте как описано по ссылке выше. 


## Задача 2. Инициализируем проект и создаем воркспейсы. 

1. Выполните `terraform init`:
    * если был создан бэкэнд в S3, то терраформ создат файл стейтов в S3 и запись в таблице 
dynamodb.
    * иначе будет создан локальный файл со стейтами.  
2. Создайте два воркспейса `stage` и `prod`.
```shell
dmitry@dmitry-N56VZ:~/yandex-cloud-terraform$ terraform workspace new stage
Created and switched to workspace "stage"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
dmitry@dmitry-N56VZ:~/yandex-cloud-terraform$ terraform workspace new prod
Created and switched to workspace "prod"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
```
3. В уже созданный `aws_instance` добавьте зависимость типа инстанса от вокспейса, что бы в разных ворскспейсах 
использовались разные `instance_type`.
4. Добавим `count`. Для `stage` должен создаться один экземпляр `ec2`, а для `prod` два. 
5. Создайте рядом еще один `aws_instance`, но теперь определите их количество при помощи `for_each`, а не `count`.
6. Что бы при изменении типа инстанса не возникло ситуации, когда не будет ни одного инстанса добавьте параметр
жизненного цикла `create_before_destroy = true` в один из рессурсов `aws_instance`.
7. При желании поэкспериментируйте с другими параметрами и рессурсами.

В виде результата работы пришлите:
* Вывод команды `terraform workspace list`.:
```shell
dmitry@dmitry-N56VZ:~/yandex-cloud-terraform/hometask$ terraform workspace list
  default
* prod
  stage

```

* Вывод команды `terraform plan` для воркспейса `prod`.  :

```shell
dmitry@dmitry-N56VZ:~/yandex-cloud-terraform/hometask$ terraform plan
module.vpc.data.yandex_compute_image.nat_instance: Reading...
module.vpc.data.yandex_compute_image.nat_instance: Read complete after 1s [id=fd8uj8cu5v8u8kfnosb4]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

  # module.count.data.yandex_compute_image.image will be read during apply
  # (depends on a resource or a module with changes pending)
 <= data "yandex_compute_image" "image" {
      + created_at    = (known after apply)
      + description   = (known after apply)
      + family        = "centos-7"
      + folder_id     = (known after apply)
      + id            = (known after apply)
      + image_id      = (known after apply)
      + labels        = (known after apply)
      + min_disk_size = (known after apply)
      + name          = (known after apply)
      + os_type       = (known after apply)
      + product_ids   = (known after apply)
      + size          = (known after apply)
      + status        = (known after apply)
    }

  # module.count.yandex_compute_instance.instance[0] will be created
  + resource "yandex_compute_instance" "instance" {
      + created_at                = (known after apply)
      + description               = "News App Demo"
      + folder_id                 = "b1g2mc1mgba7si9sl1ss"
      + fqdn                      = (known after apply)
      + hostname                  = "count-1"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDcH0vY43QB4y0ApzyW3HtfhzWP3fNyEVdRqJt6IiR1tZlYE/OC2GxTLaHvmt97oF9Kw5IuUWNvpJqn/i2JQitWsJaqIrZNifFns4/cFMF5ZxR2leM9fewWRiPto+8bnKWNSsrgiW08QE8S9HMZI3qn8Rvvj1r/Fcjnv4xlyXJE8gKci1ezhwXuuqWyrYSAk2SfCYCxPLSMjwS+vPkpDwrEgmB5zCsY/OTZ6jp0k1ukovhCLVpE98XelRni65HzjeOxCq++a8ktcYEw4pTPmwddiyxXmB+ikdakDZ+zBv/eDs5OCBvzsgI7rdHtORIjrXURcH1EHSjhkTPc+ZtSnuL4kk7EwQWX5oZ9j+Zk8ESUpjtYJIk/m9KJWcZ0pLrQibw8aDkGpsOYPvqUGbUejMTth7GSjTNXHXuSzo2eiQDuG3iIFtfpKO32UglkfbmxuLUmtwr7cWysR10l5xACAN+Nn5DYVXGqPFYlG14yOrFWd5mnGrw+3HXTYvjMQQrEfqcqaJ8jb52jX27EQoqZNSslstTRWnlr0Ob8lVzsodk6P27ZOCM580c/4UQFCXzzF/c4rLn0M/RFna0qdam/Zp97fB5m4t+n87LeaqJOpYJhFmHHhnsavU4N/a6QmYoaCU0c7kzLxv6== kudim@inbox.ru
            EOT
        }
      + name                      = "count-1"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v2"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + description = (known after apply)
              + image_id    = (known after apply)
              + name        = (known after apply)
              + size        = 40
              + snapshot_id = (known after apply)
              + type        = "network-ssd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + placement_group_id = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 2
          + memory        = 2
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # module.count.yandex_compute_instance.instance[1] will be created
  + resource "yandex_compute_instance" "instance" {
      + created_at                = (known after apply)
      + description               = "News App Demo"
      + folder_id                 = "b1g2mc1mgba7si9sl1ss"
      + fqdn                      = (known after apply)
      + hostname                  = "count-2"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDcH0vY43QB4y0ApzyW3HtfhzWP3fNyEVdRqJt6IiR1tZlYE/OC2GxTLaHvmt97oF9Kw5IuUWNvpJqn/i2JQitWsJaqIrZNifFns4/cFMF5ZxR2leM9fewWRiPto+8bnKWNSsrgiW08QE8S9HMZI3qn8Rvvj1r/Fcjnv4xlyXJE8gKci1ezhwXuuqWyrYSAk2SfCYCxPLSMjwS+vPkpDwrEgmB5zCsY/OTZ6jp0k1ukovhCLVpE98XelRni65HzjeOxCq++a8ktcYEw4pTPmwddiyxXmB+ikdakDZ+zBv/eDs5OCBvzsgI7rdHtORIjrXURcH1EHSjhkTPc+ZtSnuL4kk7EwQWX5oZ9j+Zk8ESUpjtYJIk/m9KJWcZ0pLrQibw8aDkGpsOYPvqUGbUejMTth7GSjTNXHXuSzo2eiQDuG3iIFtfpKO32UglkfbmxuLUmtwr7cWysR10l5xACAN+Nn5DYVXGqPFYlG14yOrFWd5mnGrw+3HXTYvjMQQrEfqcqaJ8jb52jX27EQoqZNSslstTRWnlr0Ob8lVzsodk6P27ZOCM580c/4UQFCXzzF/c4rLn0M/RFna0qdam/Zp97fB5m4t+n87LeaqJOpYJhFmHHhnsavU4N/a6QmYoaCU0c7kzLxv6UaqHHIi/V5Q== kudim@inbox.ru
            EOT
        }
      + name                      = "count-2"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v2"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + description = (known after apply)
              + image_id    = (known after apply)
              + name        = (known after apply)
              + size        = 40
              + snapshot_id = (known after apply)
              + type        = "network-ssd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + placement_group_id = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 2
          + memory        = 2
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # module.for_each["foreach1"].data.yandex_compute_image.image will be read during apply
  # (depends on a resource or a module with changes pending)
 <= data "yandex_compute_image" "image" {
      + created_at    = (known after apply)
      + description   = (known after apply)
      + family        = "centos-7"
      + folder_id     = (known after apply)
      + id            = (known after apply)
      + image_id      = (known after apply)
      + labels        = (known after apply)
      + min_disk_size = (known after apply)
      + name          = (known after apply)
      + os_type       = (known after apply)
      + product_ids   = (known after apply)
      + size          = (known after apply)
      + status        = (known after apply)
    }

  # module.for_each["foreach1"].yandex_compute_instance.instance2 will be created
  + resource "yandex_compute_instance" "instance2" {
      + created_at                = (known after apply)
      + description               = "News App Demo"
      + folder_id                 = "b1g2mc1mgba7si9sl1ss"
      + fqdn                      = (known after apply)
      + hostname                  = "foreach1"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDcH0vY43QB4y0ApzyW3HtfhzWP3fNyEVdRqJt6IiR1tZlYE/OC2GxTLaHvmt97oF9Kw5IuUWNvpJqn/i2JQitWsJaqIrZNifFns4/cFMF5ZxR2leM9fewWRiPto+8bnKWNSsrgiW08QE8S9HMZI3qn8Rvvj1r/Fcjnv4xlyXJE8gKci1ezhwXuuqWyrYSAk2SfCYCxPLSMjwS+vPkpDwrEgmB5zCsY/OTZ6jp0k1ukovhCLVpE98XelRni65HzjeOxCq++a8ktcYEw4pTPmwddiyxXmB+ikdakDZ+zBv/eDs5OCBvzsgI7rdHtORIjrXURcH1EHSjhkTPc+ZtSnuL4kk7EwQWX5oZ9j+Zk8ESUpjtYJIk/m9KJWcZ0pLrQibw8aDkGpsOYPvqUGbUejMTth7GSjTNXHXuSzo2eiQDuG3iIFtfpKO32UglkfbmxuLUmtwr7cWysR10l5xACAN+Nn5DYVXGqPFYlG14yOrFWd5mnGrw+3HXTYvjMQQrEfqcqaJ8jb52jX27EQoqZNSslstTRWnlr0Ob8lVzsodk6P27ZOCM580c/4UQFCXzzF/c4rLn0M/RFna0qdam/Zp97fB5m4t+n87LeaqJOpYJhFmHHhnsavU4N/a6QmYoaCU0c7kzLxv6UaqHHIi/V52n6yMKZT== kudim@inbox.ru
            EOT
        }
      + name                      = "foreach1"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v2"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + description = (known after apply)
              + image_id    = (known after apply)
              + name        = (known after apply)
              + size        = 40
              + snapshot_id = (known after apply)
              + type        = "network-ssd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + placement_group_id = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 2
          + memory        = 2
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # module.for_each["foreach2"].data.yandex_compute_image.image will be read during apply
  # (depends on a resource or a module with changes pending)
 <= data "yandex_compute_image" "image" {
      + created_at    = (known after apply)
      + description   = (known after apply)
      + family        = "centos-7"
      + folder_id     = (known after apply)
      + id            = (known after apply)
      + image_id      = (known after apply)
      + labels        = (known after apply)
      + min_disk_size = (known after apply)
      + name          = (known after apply)
      + os_type       = (known after apply)
      + product_ids   = (known after apply)
      + size          = (known after apply)
      + status        = (known after apply)
    }

  # module.for_each["foreach2"].yandex_compute_instance.instance2 will be created
  + resource "yandex_compute_instance" "instance2" {
      + created_at                = (known after apply)
      + description               = "News App Demo"
      + folder_id                 = "b1g2mc1mgba7si9sl1ss"
      + fqdn                      = (known after apply)
      + hostname                  = "foreach2"
      + id                        = (known after apply)
      + metadata                  = {
          + "ssh-keys" = <<-EOT
                centos:ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDcH0vY43QB4y0ApzyW3HtfhzWP3fNyEVdRqJt6IiR1tZlYE/OC2GxTLaHvmt97oF9Kw5IuUWNvpJqn/i2JQitWsJaqIrZNifFns4/cFMF5ZxR2leM9fewWRiPto+8bnKWNSsrgiW08QE8S9HMZI3qn8Rvvj1r/Fcjnv4xlyXJE8gKci1ezhwXuuqWyrYSAk2SfCYCxPLSMjwS+vPkpDwrEgmB5zCsY/OTZ6jp0k1ukovhCLVpE98XelRni65HzjeOxCq++a8ktcYEw4pTPmwddiyxXmB+ikdakDZ+zBv/eDs5OCBvzsgI7rdHtORIjrXURcH1EHSjhkTPc+ZtSnuL4kk7EwQWX5oZ9j+Zk8ESUpjtYJIk/m9KJWcZ0pLrQibw8aDkGpsOYPvqUGbUejMTth7GSjTNXHXuSzo2eiQDuG3iIFtfpKO32UglkfbmxuLUmtwr7cWysR10l5xACAN+Nn5DYVXGqPFYlG14yOrFWd5mnGrw+3HXTYvjMQQrEfqcqaJ8jb52jX27EQoqZNSslstTRWnlr0Ob8lVzsodk6P27ZOCM580c/4UQFCXzzF/c4rLn0M/RFna0qdam/Zp97fB5m4t+n87LeaqJOpYJhFmHHhnsavU4N/a6QmYoaCU0c7kzLxv6UaqHHIi/V5== kudim@inbox.ru
            EOT
        }
      + name                      = "foreach2"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v2"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = "ru-central1-a"

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + description = (known after apply)
              + image_id    = (known after apply)
              + name        = (known after apply)
              + size        = 40
              + snapshot_id = (known after apply)
              + type        = "network-ssd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + placement_group_id = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 2
          + memory        = 2
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # module.vpc.yandex_vpc_network.this will be created
  + resource "yandex_vpc_network" "this" {
      + created_at                = (known after apply)
      + default_security_group_id = (known after apply)
      + description               = "managed by terraform prod network"
      + folder_id                 = "b1g2mc1mgba7si9sl1ss"
      + id                        = (known after apply)
      + name                      = "prod"
      + subnet_ids                = (known after apply)
    }

  # module.vpc.yandex_vpc_subnet.this["ru-central1-a"] will be created
  + resource "yandex_vpc_subnet" "this" {
      + created_at     = (known after apply)
      + description    = "managed by terraform prod subnet for zone ru-central1-a"
      + folder_id      = "b1g2mc1mgba7si9sl1ss"
      + id             = (known after apply)
      + name           = "prod-ru-central1-a"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "10.128.0.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-a"
    }

  # module.vpc.yandex_vpc_subnet.this["ru-central1-b"] will be created
  + resource "yandex_vpc_subnet" "this" {
      + created_at     = (known after apply)
      + description    = "managed by terraform prod subnet for zone ru-central1-b"
      + folder_id      = "b1g2mc1mgba7si9sl1ss"
      + id             = (known after apply)
      + name           = "prod-ru-central1-b"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "10.129.0.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-b"
    }

  # module.vpc.yandex_vpc_subnet.this["ru-central1-c"] will be created
  + resource "yandex_vpc_subnet" "this" {
      + created_at     = (known after apply)
      + description    = "managed by terraform prod subnet for zone ru-central1-c"
      + folder_id      = "b1g2mc1mgba7si9sl1ss"
      + id             = (known after apply)
      + name           = "prod-ru-central1-c"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "10.130.0.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-c"
    }

Plan: 8 to add, 0 to change, 0 to destroy.

```

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
