1.
```commandline

C:\Users\red_hat\learn-terraform-iam-policy>terraform init

Initializing the backend...

Initializing provider plugins...
- Reusing previous version of hashicorp/aws from the dependency lock file
- Reusing previous version of hashicorp/random from the dependency lock file
- Using previously-installed hashicorp/aws v4.15.0
- Using previously-installed hashicorp/random v3.3.2

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.

C:\Users\red_hat\learn-terraform-iam-policy>terraform validate
Success! The configuration is valid.

C:\Users\red_hat\learn-terraform-iam-policy>aws configure list
      Name                    Value             Type    Location
      ----                    -----             ----    --------
   profile                  default              env    ['AWS_DEFAULT_PROFILE', 'AWS_PROFILE']
access_key     ****************RX4K shared-credentials-file
secret_key     ****************vc76 shared-credentials-file
    region                us-west-2      config-file    ~/.aws/config
```
2.
<details><summary>Terraform plan</summary>

```
C:\Users\red_hat\learn-terraform-iam-policy>terraform plan
data.aws_caller_identity.current: Reading...
data.aws_ami.ubuntu: Reading...
data.aws_ami.ubuntu: Read complete after 1s [id=ami-017fecd1353bcc96e]
data.aws_caller_identity.current: Read complete after 1s [id=951728628080]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

  # data.aws_iam_policy_document.example will be read during apply
  # (config refers to values not yet known)
 <= data "aws_iam_policy_document" "example" {
      + id   = (known after apply)
      + json = (known after apply)

      + statement {
          + actions   = [
              + "s3:ListAllMyBuckets",
            ]
          + effect    = "Allow"
          + resources = [
              + "arn:aws:s3:::*",
            ]
        }
      + statement {
          + actions   = [
              + "s3:*",
            ]
          + effect    = "Allow"
          + resources = [
              + (known after apply),
            ]
        }
    }

  # aws_iam_policy.policy will be created
  + resource "aws_iam_policy" "policy" {
      + arn         = (known after apply)
      + description = "My test policy"
      + id          = (known after apply)
      + name        = (known after apply)
      + path        = "/"
      + policy      = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "s3:ListAllMyBuckets",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                  + {
                      + Action   = [
                          + "ec2:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                  + {
                      + Action   = [
                          + "dynamodb:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "arn:aws:dynamodb:us-west-2:951728628080:table/Books"
                    },
                  + {
                      + Action   = [
                          + "cloudwatch:*",
                          + "iam:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                  + {
                      + Action    = "iam:CreateServiceLinkedRole"
                      + Condition = {
                          + StringLike = {
                              + "iam:AWSServiceName" = "events.amazonaws.com"
                            }
                        }
                      + Effect    = "Allow"
                      + Resource  = "arn:aws:iam::*:role/aws-service-role/events.amazonaws.com/AWSServiceRoleForCloudWatchEvents*"
                    },
                  + {
                      + Action   = [
                          + "s3:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + policy_id   = (known after apply)
      + tags_all    = {
          + "Hashicorp-Learn" = "aws-iam-policy"
        }
    }

  # aws_iam_user.red_hat37 will be created
  + resource "aws_iam_user" "red_hat37" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "red_hat37"
      + path          = "/"
      + tags_all      = {
          + "Hashicorp-Learn" = "aws-iam-policy"
        }
      + unique_id     = (known after apply)
    }

  # aws_iam_user_policy_attachment.attachment will be created
  + resource "aws_iam_user_policy_attachment" "attachment" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + user       = "red_hat37"
    }

  # aws_instance.web will be created
  + resource "aws_instance" "web" {
      + ami                                  = "ami-017fecd1353bcc96e"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "HelloNetology"
        }
      + tags_all                             = {
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "HelloNetology"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification {
          + capacity_reservation_preference = (known after apply)

          + capacity_reservation_target {
              + capacity_reservation_id                 = (known after apply)
              + capacity_reservation_resource_group_arn = (known after apply)
            }
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + maintenance_options {
          + auto_recovery = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
          + instance_metadata_tags      = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_card_index    = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

  # aws_network_interface.foo will be created
  + resource "aws_network_interface" "foo" {
      + arn                       = (known after apply)
      + id                        = (known after apply)
      + interface_type            = (known after apply)
      + ipv4_prefix_count         = (known after apply)
      + ipv4_prefixes             = (known after apply)
      + ipv6_address_count        = (known after apply)
      + ipv6_address_list         = (known after apply)
      + ipv6_address_list_enabled = false
      + ipv6_addresses            = (known after apply)
      + ipv6_prefix_count         = (known after apply)
      + ipv6_prefixes             = (known after apply)
      + mac_address               = (known after apply)
      + outpost_arn               = (known after apply)
      + owner_id                  = (known after apply)
      + private_dns_name          = (known after apply)
      + private_ip                = (known after apply)
      + private_ip_list           = (known after apply)
      + private_ip_list_enabled   = false
      + private_ips               = [
          + "172.31.10.100",
        ]
      + private_ips_count         = (known after apply)
      + security_groups           = (known after apply)
      + source_dest_check         = true
      + subnet_id                 = (known after apply)
      + tags                      = {
          + "Name" = "primary_network_interface"
        }
      + tags_all                  = {
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "primary_network_interface"
        }

      + attachment {
          + attachment_id = (known after apply)
          + device_index  = (known after apply)
          + instance      = (known after apply)
        }
    }

  # aws_s3_bucket.bucket will be created
  + resource "aws_s3_bucket" "bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = (known after apply)
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = "tf-bucket"
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags                        = {
          + "Environment" = "Dev"
          + "Name"        = "My bucket"
        }
      + tags_all                    = {
          + "Environment"     = "Dev"
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "My bucket"
        }
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule {
          + allowed_headers = (known after apply)
          + allowed_methods = (known after apply)
          + allowed_origins = (known after apply)
          + expose_headers  = (known after apply)
          + max_age_seconds = (known after apply)
        }

      + grant {
          + id          = (known after apply)
          + permissions = (known after apply)
          + type        = (known after apply)
          + uri         = (known after apply)
        }

      + lifecycle_rule {
          + abort_incomplete_multipart_upload_days = (known after apply)
          + enabled                                = (known after apply)
          + id                                     = (known after apply)
          + prefix                                 = (known after apply)
          + tags                                   = (known after apply)

          + expiration {
              + date                         = (known after apply)
              + days                         = (known after apply)
              + expired_object_delete_marker = (known after apply)
            }

          + noncurrent_version_expiration {
              + days = (known after apply)
            }

          + noncurrent_version_transition {
              + days          = (known after apply)
              + storage_class = (known after apply)
            }

          + transition {
              + date          = (known after apply)
              + days          = (known after apply)
              + storage_class = (known after apply)
            }
        }

      + logging {
          + target_bucket = (known after apply)
          + target_prefix = (known after apply)
        }

      + object_lock_configuration {
          + object_lock_enabled = (known after apply)

          + rule {
              + default_retention {
                  + days  = (known after apply)
                  + mode  = (known after apply)
                  + years = (known after apply)
                }
            }
        }

      + replication_configuration {
          + role = (known after apply)

          + rules {
              + delete_marker_replication_status = (known after apply)
              + id                               = (known after apply)
              + prefix                           = (known after apply)
              + priority                         = (known after apply)
              + status                           = (known after apply)

              + destination {
                  + account_id         = (known after apply)
                  + bucket             = (known after apply)
                  + replica_kms_key_id = (known after apply)
                  + storage_class      = (known after apply)

                  + access_control_translation {
                      + owner = (known after apply)
                    }

                  + metrics {
                      + minutes = (known after apply)
                      + status  = (known after apply)
                    }

                  + replication_time {
                      + minutes = (known after apply)
                      + status  = (known after apply)
                    }
                }

              + filter {
                  + prefix = (known after apply)
                  + tags   = (known after apply)
                }

              + source_selection_criteria {
                  + sse_kms_encrypted_objects {
                      + enabled = (known after apply)
                    }
                }
            }
        }

      + server_side_encryption_configuration {
          + rule {
              + bucket_key_enabled = (known after apply)

              + apply_server_side_encryption_by_default {
                  + kms_master_key_id = (known after apply)
                  + sse_algorithm     = (known after apply)
                }
            }
        }

      + versioning {
          + enabled    = (known after apply)
          + mfa_delete = (known after apply)
        }

      + website {
          + error_document           = (known after apply)
          + index_document           = (known after apply)
          + redirect_all_requests_to = (known after apply)
          + routing_rules            = (known after apply)
        }
    }

  # aws_s3_bucket_acl.bucket will be created
  + resource "aws_s3_bucket_acl" "bucket" {
      + acl    = "private"
      + bucket = (known after apply)
      + id     = (known after apply)

      + access_control_policy {
          + grant {
              + permission = (known after apply)

              + grantee {
                  + display_name  = (known after apply)
                  + email_address = (known after apply)
                  + id            = (known after apply)
                  + type          = (known after apply)
                  + uri           = (known after apply)
                }
            }

          + owner {
              + display_name = (known after apply)
              + id           = (known after apply)
            }
        }
    }

  # aws_subnet.my_subnet will be created
  + resource "aws_subnet" "my_subnet" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-west-2a"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "172.31.10.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Name" = "tf-example"
        }
      + tags_all                                       = {
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "tf-example"
        }
      + vpc_id                                         = (known after apply)
    }

  # aws_vpc.my_vpc will be created
  + resource "aws_vpc" "my_vpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "172.31.0.0/16"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_classiclink                   = (known after apply)
      + enable_classiclink_dns_support       = (known after apply)
      + enable_dns_hostnames                 = (known after apply)
      + enable_dns_support                   = true
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags_all                             = {
          + "Hashicorp-Learn" = "aws-iam-policy"
        }
    }

  # random_pet.red_hat will be created
  + resource "random_pet" "red_hat" {
      + id        = (known after apply)
      + length    = 3
      + separator = "-"
    }

Plan: 10 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + account_id         = "951728628080"
  + caller_arn         = "arn:aws:iam::951728628080:root"
  + caller_user        = "951728628080"
  + instance_id        = (known after apply)
  + instance_ip_addr   = (known after apply)
  + instance_public_ip = (known after apply)
  + my_vpc             = (known after apply)
  + region             = "us-west-2"
  + s3_bucket_name     = (known after apply)

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if
you run "terraform apply" now.
```
</details>

```
Changes to Outputs:
  + account_id         = "951728628080"
  + caller_arn         = "arn:aws:iam::951728628080:root"
  + caller_user        = "951728628080"
  + instance_id        = (known after apply)
  + instance_ip_addr   = (known after apply)
  + instance_public_ip = (known after apply)
  + my_vpc             = (known after apply)
  + region             = "us-west-2"
  + s3_bucket_name     = (known after apply)

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if
you run "terraform apply" now.
```

<details><summary>Terraform apply</summary>

```

C:\Users\red_hat\learn-terraform-iam-policy>terraform apply -auto-approve
data.aws_caller_identity.current: Reading...
data.aws_ami.ubuntu: Reading...
data.aws_ami.ubuntu: Read complete after 1s [id=ami-017fecd1353bcc96e]
data.aws_caller_identity.current: Read complete after 1s [id=951728628080]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

  # data.aws_iam_policy_document.example will be read during apply
  # (config refers to values not yet known)
 <= data "aws_iam_policy_document" "example" {
      + id   = (known after apply)
      + json = (known after apply)

      + statement {
          + actions   = [
              + "s3:ListAllMyBuckets",
            ]
          + effect    = "Allow"
          + resources = [
              + "arn:aws:s3:::*",
            ]
        }
      + statement {
          + actions   = [
              + "s3:*",
            ]
          + effect    = "Allow"
          + resources = [
              + (known after apply),
            ]
        }
    }

  # aws_iam_policy.policy will be created
  + resource "aws_iam_policy" "policy" {
      + arn         = (known after apply)
      + description = "My test policy"
      + id          = (known after apply)
      + name        = (known after apply)
      + path        = "/"
      + policy      = jsonencode(
            {
              + Statement = [
                  + {
                      + Action   = [
                          + "s3:ListAllMyBuckets",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                  + {
                      + Action   = [
                          + "ec2:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                  + {
                      + Action   = [
                          + "dynamodb:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "arn:aws:dynamodb:us-west-2:951728628080:table/Books"
                    },
                  + {
                      + Action   = [
                          + "cloudwatch:*",
                          + "iam:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                  + {
                      + Action    = "iam:CreateServiceLinkedRole"
                      + Condition = {
                          + StringLike = {
                              + "iam:AWSServiceName" = "events.amazonaws.com"
                            }
                        }
                      + Effect    = "Allow"
                      + Resource  = "arn:aws:iam::*:role/aws-service-role/events.amazonaws.com/AWSServiceRoleForCloudWatchEvents*"
                    },
                  + {
                      + Action   = [
                          + "s3:*",
                        ]
                      + Effect   = "Allow"
                      + Resource = "*"
                    },
                ]
              + Version   = "2012-10-17"
            }
        )
      + policy_id   = (known after apply)
      + tags_all    = {
          + "Hashicorp-Learn" = "aws-iam-policy"
        }
    }

  # aws_iam_user.red_hat37 will be created
  + resource "aws_iam_user" "red_hat37" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "red_hat37"
      + path          = "/"
      + tags_all      = {
          + "Hashicorp-Learn" = "aws-iam-policy"
        }
      + unique_id     = (known after apply)
    }

  # aws_iam_user_policy_attachment.attachment will be created
  + resource "aws_iam_user_policy_attachment" "attachment" {
      + id         = (known after apply)
      + policy_arn = (known after apply)
      + user       = "red_hat37"
    }

  # aws_instance.web will be created
  + resource "aws_instance" "web" {
      + ami                                  = "ami-017fecd1353bcc96e"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "HelloNetology"
        }
      + tags_all                             = {
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "HelloNetology"
        }
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification {
          + capacity_reservation_preference = (known after apply)

          + capacity_reservation_target {
              + capacity_reservation_id                 = (known after apply)
              + capacity_reservation_resource_group_arn = (known after apply)
            }
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + maintenance_options {
          + auto_recovery = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
          + instance_metadata_tags      = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_card_index    = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

  # aws_network_interface.foo will be created
  + resource "aws_network_interface" "foo" {
      + arn                       = (known after apply)
      + id                        = (known after apply)
      + interface_type            = (known after apply)
      + ipv4_prefix_count         = (known after apply)
      + ipv4_prefixes             = (known after apply)
      + ipv6_address_count        = (known after apply)
      + ipv6_address_list         = (known after apply)
      + ipv6_address_list_enabled = false
      + ipv6_addresses            = (known after apply)
      + ipv6_prefix_count         = (known after apply)
      + ipv6_prefixes             = (known after apply)
      + mac_address               = (known after apply)
      + outpost_arn               = (known after apply)
      + owner_id                  = (known after apply)
      + private_dns_name          = (known after apply)
      + private_ip                = (known after apply)
      + private_ip_list           = (known after apply)
      + private_ip_list_enabled   = false
      + private_ips               = [
          + "172.31.10.100",
        ]
      + private_ips_count         = (known after apply)
      + security_groups           = (known after apply)
      + source_dest_check         = true
      + subnet_id                 = (known after apply)
      + tags                      = {
          + "Name" = "primary_network_interface"
        }
      + tags_all                  = {
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "primary_network_interface"
        }

      + attachment {
          + attachment_id = (known after apply)
          + device_index  = (known after apply)
          + instance      = (known after apply)
        }
    }

  # aws_s3_bucket.bucket will be created
  + resource "aws_s3_bucket" "bucket" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = (known after apply)
      + bucket_domain_name          = (known after apply)
      + bucket_prefix               = "tf-bucket"
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = (known after apply)
      + request_payer               = (known after apply)
      + tags                        = {
          + "Environment" = "Dev"
          + "Name"        = "My bucket"
        }
      + tags_all                    = {
          + "Environment"     = "Dev"
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "My bucket"
        }
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)

      + cors_rule {
          + allowed_headers = (known after apply)
          + allowed_methods = (known after apply)
          + allowed_origins = (known after apply)
          + expose_headers  = (known after apply)
          + max_age_seconds = (known after apply)
        }

      + grant {
          + id          = (known after apply)
          + permissions = (known after apply)
          + type        = (known after apply)
          + uri         = (known after apply)
        }

      + lifecycle_rule {
          + abort_incomplete_multipart_upload_days = (known after apply)
          + enabled                                = (known after apply)
          + id                                     = (known after apply)
          + prefix                                 = (known after apply)
          + tags                                   = (known after apply)

          + expiration {
              + date                         = (known after apply)
              + days                         = (known after apply)
              + expired_object_delete_marker = (known after apply)
            }

          + noncurrent_version_expiration {
              + days = (known after apply)
            }

          + noncurrent_version_transition {
              + days          = (known after apply)
              + storage_class = (known after apply)
            }

          + transition {
              + date          = (known after apply)
              + days          = (known after apply)
              + storage_class = (known after apply)
            }
        }

      + logging {
          + target_bucket = (known after apply)
          + target_prefix = (known after apply)
        }

      + object_lock_configuration {
          + object_lock_enabled = (known after apply)

          + rule {
              + default_retention {
                  + days  = (known after apply)
                  + mode  = (known after apply)
                  + years = (known after apply)
                }
            }
        }

      + replication_configuration {
          + role = (known after apply)

          + rules {
              + delete_marker_replication_status = (known after apply)
              + id                               = (known after apply)
              + prefix                           = (known after apply)
              + priority                         = (known after apply)
              + status                           = (known after apply)

              + destination {
                  + account_id         = (known after apply)
                  + bucket             = (known after apply)
                  + replica_kms_key_id = (known after apply)
                  + storage_class      = (known after apply)

                  + access_control_translation {
                      + owner = (known after apply)
                    }

                  + metrics {
                      + minutes = (known after apply)
                      + status  = (known after apply)
                    }

                  + replication_time {
                      + minutes = (known after apply)
                      + status  = (known after apply)
                    }
                }

              + filter {
                  + prefix = (known after apply)
                  + tags   = (known after apply)
                }

              + source_selection_criteria {
                  + sse_kms_encrypted_objects {
                      + enabled = (known after apply)
                    }
                }
            }
        }

      + server_side_encryption_configuration {
          + rule {
              + bucket_key_enabled = (known after apply)

              + apply_server_side_encryption_by_default {
                  + kms_master_key_id = (known after apply)
                  + sse_algorithm     = (known after apply)
                }
            }
        }

      + versioning {
          + enabled    = (known after apply)
          + mfa_delete = (known after apply)
        }

      + website {
          + error_document           = (known after apply)
          + index_document           = (known after apply)
          + redirect_all_requests_to = (known after apply)
          + routing_rules            = (known after apply)
        }
    }

  # aws_s3_bucket_acl.bucket will be created
  + resource "aws_s3_bucket_acl" "bucket" {
      + acl    = "private"
      + bucket = (known after apply)
      + id     = (known after apply)

      + access_control_policy {
          + grant {
              + permission = (known after apply)

              + grantee {
                  + display_name  = (known after apply)
                  + email_address = (known after apply)
                  + id            = (known after apply)
                  + type          = (known after apply)
                  + uri           = (known after apply)
                }
            }

          + owner {
              + display_name = (known after apply)
              + id           = (known after apply)
            }
        }
    }

  # aws_subnet.my_subnet will be created
  + resource "aws_subnet" "my_subnet" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-west-2a"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "172.31.10.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = false
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Name" = "tf-example"
        }
      + tags_all                                       = {
          + "Hashicorp-Learn" = "aws-iam-policy"
          + "Name"            = "tf-example"
        }
      + vpc_id                                         = (known after apply)
    }

  # aws_vpc.my_vpc will be created
  + resource "aws_vpc" "my_vpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "172.31.0.0/16"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_classiclink                   = (known after apply)
      + enable_classiclink_dns_support       = (known after apply)
      + enable_dns_hostnames                 = (known after apply)
      + enable_dns_support                   = true
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags_all                             = {
          + "Hashicorp-Learn" = "aws-iam-policy"
        }
    }

  # random_pet.red_hat will be created
  + resource "random_pet" "red_hat" {
      + id        = (known after apply)
      + length    = 3
      + separator = "-"
    }

Plan: 10 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + account_id         = "951728628080"
  + caller_arn         = "arn:aws:iam::951728628080:root"
  + caller_user        = "951728628080"
  + instance_id        = (known after apply)
  + instance_ip_addr   = (known after apply)
  + instance_public_ip = (known after apply)
  + my_vpc             = (known after apply)
  + region             = "us-west-2"
  + s3_bucket_name     = (known after apply)
random_pet.red_hat: Creating...
random_pet.red_hat: Creation complete after 0s [id=wildly-elegant-termite]
aws_iam_user.red_hat37: Creating...
aws_vpc.my_vpc: Creating...
aws_iam_policy.policy: Creating...
aws_s3_bucket.bucket: Creating...
aws_instance.web: Creating...
aws_iam_user.red_hat37: Creation complete after 1s [id=red_hat37]
aws_iam_policy.policy: Creation complete after 1s [id=arn:aws:iam::951728628080:policy/wildly-elegant-termite-policy]
aws_iam_user_policy_attachment.attachment: Creating...
aws_iam_user_policy_attachment.attachment: Creation complete after 0s [id=red_hat37-20220918091626001800000002]
aws_vpc.my_vpc: Creation complete after 3s [id=vpc-0dfb56e2dd19c29a4]
aws_subnet.my_subnet: Creating...
aws_subnet.my_subnet: Creation complete after 1s [id=subnet-08b6f1b66be8f251b]
aws_network_interface.foo: Creating...
aws_s3_bucket.bucket: Creation complete after 5s [id=tf-bucket20220918091624839400000001]
data.aws_iam_policy_document.example: Reading...
aws_s3_bucket_acl.bucket: Creating...
data.aws_iam_policy_document.example: Read complete after 0s [id=1237476978]
aws_network_interface.foo: Creation complete after 1s [id=eni-0e6a03bad70941205]
aws_s3_bucket_acl.bucket: Creation complete after 1s [id=tf-bucket20220918091624839400000001,private]
aws_instance.web: Still creating... [10s elapsed]
aws_instance.web: Creation complete after 15s [id=i-048724e877b3acb23]

Apply complete! Resources: 10 added, 0 changed, 0 destroyed.

Outputs:

account_id = "951728628080"
caller_arn = "arn:aws:iam::951728628080:root"
caller_user = "951728628080"
instance_id = "i-048724e877b3acb23"
instance_ip_addr = "172.31.61.176"
instance_public_ip = "35.86.220.113"
my_vpc = "vpc-0dfb56e2dd19c29a4"
region = "us-west-2"
s3_bucket_name = "tf-bucket20220918091624839400000001"
```
</details>

```
Outputs:

account_id = "951728628080"
caller_arn = "arn:aws:iam::951728628080:root"
caller_user = "951728628080"
instance_id = "i-048724e877b3acb23"
instance_ip_addr = "172.31.61.176"
instance_public_ip = "35.86.220.113"
my_vpc = "vpc-0dfb56e2dd19c29a4"
region = "us-west-2"
s3_bucket_name = "tf-bucket20220918091624839400000001"
```

1. CloudFormation
2. https://github.com/rodionovmv/terraform
