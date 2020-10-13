# tf-azure-functionapp

This repo creates a Azure Function app and deploys a Zip file containing a Python application. The Zip file has to be created upfront and stored locally.

## Install tools

* Azure CLI
* Terraform
* 7z

## Login to Azure

Execute ```az login``` and follow the instructions in your browser or ```az login -h``` to see other ways to login.

## Clone this repo

```git clone git@github.com:hc-showcase/tf-azure-functionapp.git

cd tf-azure-functionapp

```
### Build the Zip

The Zip file must contain the functions and the host.json file in the root. It must not container a root folder.

The Zip must look like that:
```
mkaesz@arch ~/w/tf-azure-funcapp (main)> unzip -l dist.zip
Archive:  dist.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2020-10-13 11:42   HttpExample/
      751  2020-10-13 11:41   HttpExample/__init__.py
        0  2020-10-13 11:42   HttpExample/__pycache__/
      865  2020-10-13 11:42   HttpExample/__pycache__/__init__.cpython-38.pyc
      314  2020-10-13 11:41   HttpExample/function.json
      288  2020-10-13 11:41   host.json
      117  2020-10-13 11:41   local.settings.json
      109  2020-10-13 11:41   requirements.txt
---------                     -------
     2444                     8 files

```

To build the Zip I used 7z:
```
mkaesz@arch ~/w/tf-azure-functionapp (main)> 
7z a dist.zip ./functions/*

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=en_US.UTF-8,Utf16=on,HugeFiles=on,64 bits,24 CPUs AMD Ryzen 9 3900X 12-Core Processor             (870F10),ASM,AES-NI)

Open archive: dist.zip
--
Path = dist.zip
Type = zip
Physical Size = 2717

Scanning the drive:
2 folders, 6 files, 2444 bytes (3 KiB)

Updating archive: dist.zip

Items to compress: 8

    
Files read from disk: 6
Archive size: 2717 bytes (3 KiB)
Everything is Ok
```

The repository already contains a dist.zip that can be immediately be used without the build step.

### Set your Subscription ID

In main.tf you have to specify the subscription ID you want to use.

### Execute Terraform

```
mkaesz@arch ~/w/tf-azure-funcapp (main)> terraform plan

Error: Could not load plugin


Plugin reinitialization required. Please run "terraform init".

Plugins are external binaries that Terraform uses to access and manipulate
resources. The configuration provided requires plugins which can't be located,
don't satisfy the version constraints, or are otherwise incompatible.

Terraform automatically discovers provider requirements from your
configuration, including providers used in child modules. To see the
requirements and constraints, run "terraform providers".

2 problems:

- Failed to instantiate provider "registry.terraform.io/hashicorp/azurerm" to
obtain schema: unknown provider "registry.terraform.io/hashicorp/azurerm"
- Failed to instantiate provider "registry.terraform.io/hashicorp/random" to
obtain schema: unknown provider "registry.terraform.io/hashicorp/random"


mkaesz@arch ~/w/tf-azure-funcapp (main) [1]> terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/azurerm...
- Finding latest version of hashicorp/random...
- Installing hashicorp/azurerm v2.31.1...
- Installed hashicorp/azurerm v2.31.1 (signed by HashiCorp)
- Installing hashicorp/random v3.0.0...
- Installed hashicorp/random v3.0.0 (signed by HashiCorp)

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, we recommend adding version constraints in a required_providers block
in your configuration, with the constraint strings suggested below.

* hashicorp/azurerm: version = "~> 2.31.1"
* hashicorp/random: version = "~> 3.0.0"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
mkaesz@arch ~/w/tf-azure-funcapp (main)> terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

  # data.azurerm_storage_account_sas.storage_sas will be read during apply
  # (config refers to values not yet known)
 <= data "azurerm_storage_account_sas" "storage_sas"  {
      + connection_string = (sensitive value)
      + expiry            = "2028-03-21"
      + https_only        = false
      + id                = (known after apply)
      + sas               = (sensitive value)
      + start             = "2018-03-21"

      + permissions {
          + add     = false
          + create  = false
          + delete  = false
          + list    = false
          + process = false
          + read    = true
          + update  = false
          + write   = false
        }

      + resource_types {
          + container = false
          + object    = true
          + service   = false
        }

      + services {
          + blob  = true
          + file  = false
          + queue = false
          + table = false
        }

      + timeouts {
          + read = (known after apply)
        }
    }

  # azurerm_app_service_plan.plan will be created
  + resource "azurerm_app_service_plan" "plan" {
      + id                           = (known after apply)
      + kind                         = "FunctionApp"
      + location                     = "westeurope"
      + maximum_elastic_worker_count = (known after apply)
      + maximum_number_of_workers    = (known after apply)
      + name                         = (known after apply)
      + resource_group_name          = "AzureFunction"

      + sku {
          + capacity = (known after apply)
          + size     = "Y1"
          + tier     = "Dynamic"
        }
    }

  # azurerm_function_app.function will be created
  + resource "azurerm_function_app" "function" {
      + app_service_plan_id            = (known after apply)
      + app_settings                   = (known after apply)
      + client_affinity_enabled        = (known after apply)
      + default_hostname               = (known after apply)
      + enable_builtin_logging         = true
      + enabled                        = true
      + https_only                     = false
      + id                             = (known after apply)
      + kind                           = (known after apply)
      + location                       = "westeurope"
      + name                           = (known after apply)
      + outbound_ip_addresses          = (known after apply)
      + possible_outbound_ip_addresses = (known after apply)
      + resource_group_name            = "AzureFunction"
      + site_credential                = (known after apply)
      + storage_account_access_key     = (sensitive value)
      + storage_account_name           = (known after apply)
      + storage_connection_string      = (sensitive value)
      + version                        = "~1"

      + auth_settings {
          + additional_login_params        = (known after apply)
          + allowed_external_redirect_urls = (known after apply)
          + default_provider               = (known after apply)
          + enabled                        = (known after apply)
          + issuer                         = (known after apply)
          + runtime_version                = (known after apply)
          + token_refresh_extension_hours  = (known after apply)
          + token_store_enabled            = (known after apply)
          + unauthenticated_client_action  = (known after apply)

          + active_directory {
              + allowed_audiences = (known after apply)
              + client_id         = (known after apply)
              + client_secret     = (sensitive value)
            }

          + facebook {
              + app_id       = (known after apply)
              + app_secret   = (sensitive value)
              + oauth_scopes = (known after apply)
            }

          + google {
              + client_id     = (known after apply)
              + client_secret = (sensitive value)
              + oauth_scopes  = (known after apply)
            }

          + microsoft {
              + client_id     = (known after apply)
              + client_secret = (sensitive value)
              + oauth_scopes  = (known after apply)
            }

          + twitter {
              + consumer_key    = (known after apply)
              + consumer_secret = (sensitive value)
            }
        }

      + connection_string {
          + name  = (known after apply)
          + type  = (known after apply)
          + value = (sensitive value)
        }

      + identity {
          + identity_ids = (known after apply)
          + principal_id = (known after apply)
          + tenant_id    = (known after apply)
          + type         = (known after apply)
        }

      + site_config {
          + always_on                   = (known after apply)
          + auto_swap_slot_name         = (known after apply)
          + ftps_state                  = (known after apply)
          + http2_enabled               = (known after apply)
          + ip_restriction              = (known after apply)
          + linux_fx_version            = (known after apply)
          + min_tls_version             = (known after apply)
          + pre_warmed_instance_count   = (known after apply)
          + scm_ip_restriction          = (known after apply)
          + scm_type                    = (known after apply)
          + scm_use_main_ip_restriction = (known after apply)
          + use_32_bit_worker_process   = (known after apply)
          + websockets_enabled          = (known after apply)

          + cors {
              + allowed_origins     = (known after apply)
              + support_credentials = (known after apply)
            }
        }

      + source_control {
          + branch             = (known after apply)
          + manual_integration = (known after apply)
          + repo_url           = (known after apply)
          + rollback_enabled   = (known after apply)
          + use_mercurial      = (known after apply)
        }
    }

  # azurerm_resource_group.rg will be created
  + resource "azurerm_resource_group" "rg" {
      + id       = (known after apply)
      + location = "westeurope"
      + name     = "AzureFunction"
    }

 # azurerm_storage_account.storage will be created
  + resource "azurerm_storage_account" "storage" {
      + access_tier                      = (known after apply)
      + account_kind                     = "StorageV2"
      + account_replication_type         = "LRS"
      + account_tier                     = "Standard"
      + allow_blob_public_access         = false
      + enable_https_traffic_only        = true
      + id                               = (known after apply)
      + is_hns_enabled                   = false
      + large_file_share_enabled         = (known after apply)
      + location                         = "westeurope"
      + min_tls_version                  = "TLS1_0"
      + name                             = (known after apply)
      + primary_access_key               = (sensitive value)
      + primary_blob_connection_string   = (sensitive value)
      + primary_blob_endpoint            = (known after apply)
      + primary_blob_host                = (known after apply)
      + primary_connection_string        = (sensitive value)
      + primary_dfs_endpoint             = (known after apply)
      + primary_dfs_host                 = (known after apply)
      + primary_file_endpoint            = (known after apply)
      + primary_file_host                = (known after apply)
      + primary_location                 = (known after apply)
      + primary_queue_endpoint           = (known after apply)
      + primary_queue_host               = (known after apply)
      + primary_table_endpoint           = (known after apply)
      + primary_table_host               = (known after apply)
      + primary_web_endpoint             = (known after apply)
      + primary_web_host                 = (known after apply)
      + resource_group_name              = "AzureFunction"
      + secondary_access_key             = (sensitive value)
      + secondary_blob_connection_string = (sensitive value)
      + secondary_blob_endpoint          = (known after apply)
      + secondary_blob_host              = (known after apply)
      + secondary_connection_string      = (sensitive value)
      + secondary_dfs_endpoint           = (known after apply)
      + secondary_dfs_host               = (known after apply)
      + secondary_file_endpoint          = (known after apply)
      + secondary_file_host              = (known after apply)
      + secondary_location               = (known after apply)
      + secondary_queue_endpoint         = (known after apply)
      + secondary_queue_host             = (known after apply)
      + secondary_table_endpoint         = (known after apply)
      + secondary_table_host             = (known after apply)
      + secondary_web_endpoint           = (known after apply)
      + secondary_web_host               = (known after apply)

      + blob_properties {
          + cors_rule {
              + allowed_headers    = (known after apply)
              + allowed_methods    = (known after apply)
              + allowed_origins    = (known after apply)
              + exposed_headers    = (known after apply)
              + max_age_in_seconds = (known after apply)
            }

          + delete_retention_policy {
              + days = (known after apply)
            }
        }

      + identity {
          + principal_id = (known after apply)
          + tenant_id    = (known after apply)
          + type         = (known after apply)
        }

      + network_rules {
          + bypass                     = (known after apply)
          + default_action             = (known after apply)
          + ip_rules                   = (known after apply)
          + virtual_network_subnet_ids = (known after apply)
        }

      + queue_properties {
          + cors_rule {
              + allowed_headers    = (known after apply)
              + allowed_methods    = (known after apply)
              + allowed_origins    = (known after apply)
              + exposed_headers    = (known after apply)
              + max_age_in_seconds = (known after apply)
            }

          + hour_metrics {
              + enabled               = (known after apply)
              + include_apis          = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
            }

          + logging {
              + delete                = (known after apply)
              + read                  = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
              + write                 = (known after apply)
            }

          + minute_metrics {
              + enabled               = (known after apply)
              + include_apis          = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
            }
        }
    }

  # azurerm_storage_blob.storage_blob will be created
  + resource "azurerm_storage_blob" "storage_blob" {
      + access_tier            = (known after apply)
      + content_type           = "application/octet-stream"
      + id                     = (known after apply)
      + metadata               = (known after apply)
      + name                   = "dist.zip"
      + parallelism            = 8
      + size                   = 0
      + source                 = "./dist.zip"
      + storage_account_name   = (known after apply)
      + storage_container_name = "func"
      + type                   = "Block"
      + url                    = (known after apply)
    }

  # azurerm_storage_container.storage_container will be created
  + resource "azurerm_storage_container" "storage_container" {
      + container_access_type   = "private"
      + has_immutability_policy = (known after apply)
      + has_legal_hold          = (known after apply)
      + id                      = (known after apply)
      + metadata                = (known after apply)
      + name                    = "func"
      + resource_manager_id     = (known after apply)
      + storage_account_name    = (known after apply)
    }

  # random_string.app_service_plan_name will be created
  + resource "random_string" "app_service_plan_name" {
      + id          = (known after apply)
      + length      = 16
      + lower       = true
      + min_lower   = 0
      + min_numeric = 0
      + min_special = 0
      + min_upper   = 0
      + number      = true
      + result      = (known after apply)
      + special     = false
      + upper       = true
    }

  # random_string.function_name will be created
  + resource "random_string" "function_name" {
      + id          = (known after apply)
      + length      = 16
      + lower       = true
      + min_lower   = 0
      + min_numeric = 0
      + min_special = 0
      + min_upper   = 0
      + number      = true
      + result      = (known after apply)
      + special     = false
      + upper       = false
    }

  # random_string.storage_name will be created
  + resource "random_string" "storage_name" {
      + id          = (known after apply)
      + length      = 16
      + lower       = true
      + min_lower   = 0
      + min_numeric = 0
      + min_special = 0
      + min_upper   = 0
      + number      = true
      + result      = (known after apply)
      + special     = false
      + upper       = false
    }

Plan: 9 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

mkaesz@arch ~/w/tf-azure-funcapp (main)> terraform apply

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create
 <= read (data resources)

Terraform will perform the following actions:

  # data.azurerm_storage_account_sas.storage_sas will be read during apply
  # (config refers to values not yet known)
 <= data "azurerm_storage_account_sas" "storage_sas"  {
      + connection_string = (sensitive value)
      + expiry            = "2028-03-21"
      + https_only        = false
      + id                = (known after apply)
      + sas               = (sensitive value)
      + start             = "2018-03-21"

      + permissions {
          + add     = false
          + create  = false
          + delete  = false
          + list    = false
          + process = false
          + read    = true
          + update  = false
          + write   = false
        }

      + resource_types {
          + container = false
          + object    = true
          + service   = false
        }

      + services {
          + blob  = true
          + file  = false
          + queue = false
          + table = false
        }

      + timeouts {
          + read = (known after apply)
        }
    }

  # azurerm_app_service_plan.plan will be created
  + resource "azurerm_app_service_plan" "plan" {
      + id                           = (known after apply)
      + kind                         = "FunctionApp"
      + location                     = "westeurope"
      + maximum_elastic_worker_count = (known after apply)
      + maximum_number_of_workers    = (known after apply)
      + name                         = (known after apply)
      + resource_group_name          = "AzureFunction"

      + sku {
          + capacity = (known after apply)
          + size     = "Y1"
          + tier     = "Dynamic"
        }
    }

  # azurerm_function_app.function will be created
  + resource "azurerm_function_app" "function" {
      + app_service_plan_id            = (known after apply)
      + app_settings                   = (known after apply)
      + client_affinity_enabled        = (known after apply)
      + default_hostname               = (known after apply)
      + enable_builtin_logging         = true
      + enabled                        = true
      + https_only                     = false
      + id                             = (known after apply)
      + kind                           = (known after apply)
      + location                       = "westeurope"
      + name                           = (known after apply)
      + outbound_ip_addresses          = (known after apply)
      + possible_outbound_ip_addresses = (known after apply)
      + resource_group_name            = "AzureFunction"
      + site_credential                = (known after apply)
      + storage_account_access_key     = (sensitive value)
      + storage_account_name           = (known after apply)
      + storage_connection_string      = (sensitive value)
      + version                        = "~1"

      + auth_settings {
          + additional_login_params        = (known after apply)
          + allowed_external_redirect_urls = (known after apply)
          + default_provider               = (known after apply)
          + enabled                        = (known after apply)
          + issuer                         = (known after apply)
          + runtime_version                = (known after apply)
          + token_refresh_extension_hours  = (known after apply)
          + token_store_enabled            = (known after apply)
          + unauthenticated_client_action  = (known after apply)

          + active_directory {
              + allowed_audiences = (known after apply)
              + client_id         = (known after apply)
              + client_secret     = (sensitive value)
            }

          + facebook {
              + app_id       = (known after apply)
              + app_secret   = (sensitive value)
              + oauth_scopes = (known after apply)
            }

          + google {
              + client_id     = (known after apply)
              + client_secret = (sensitive value)
              + oauth_scopes  = (known after apply)
            }

          + microsoft {
              + client_id     = (known after apply)
              + client_secret = (sensitive value)
              + oauth_scopes  = (known after apply)
            }

          + twitter {
              + consumer_key    = (known after apply)
              + consumer_secret = (sensitive value)
            }
        }

      + connection_string {
          + name  = (known after apply)
          + type  = (known after apply)
          + value = (sensitive value)
        }

      + identity {
          + identity_ids = (known after apply)
          + principal_id = (known after apply)
          + tenant_id    = (known after apply)
          + type         = (known after apply)
        }

      + site_config {
          + always_on                   = (known after apply)
          + auto_swap_slot_name         = (known after apply)
          + ftps_state                  = (known after apply)
          + http2_enabled               = (known after apply)
          + ip_restriction              = (known after apply)
          + linux_fx_version            = (known after apply)
          + min_tls_version             = (known after apply)
          + pre_warmed_instance_count   = (known after apply)
          + scm_ip_restriction          = (known after apply)
          + scm_type                    = (known after apply)
          + scm_use_main_ip_restriction = (known after apply)
          + use_32_bit_worker_process   = (known after apply)
          + websockets_enabled          = (known after apply)

          + cors {
              + allowed_origins     = (known after apply)
              + support_credentials = (known after apply)
            }
        }

      + source_control {
          + branch             = (known after apply)
          + manual_integration = (known after apply)
          + repo_url           = (known after apply)
          + rollback_enabled   = (known after apply)
          + use_mercurial      = (known after apply)
        }
    }

  # azurerm_resource_group.rg will be created
  + resource "azurerm_resource_group" "rg" {
      + id       = (known after apply)
      + location = "westeurope"
      + name     = "AzureFunction"
    }

  # azurerm_storage_account.storage will be created
  + resource "azurerm_storage_account" "storage" {
      + access_tier                      = (known after apply)
      + account_kind                     = "StorageV2"
      + account_replication_type         = "LRS"
      + account_tier                     = "Standard"
      + allow_blob_public_access         = false
      + enable_https_traffic_only        = true
      + id                               = (known after apply)
      + is_hns_enabled                   = false
      + large_file_share_enabled         = (known after apply)
      + location                         = "westeurope"
      + min_tls_version                  = "TLS1_0"
      + name                             = (known after apply)
      + primary_access_key               = (sensitive value)
      + primary_blob_connection_string   = (sensitive value)
      + primary_blob_endpoint            = (known after apply)
      + primary_blob_host                = (known after apply)
      + primary_connection_string        = (sensitive value)
      + primary_dfs_endpoint             = (known after apply)
      + primary_dfs_host                 = (known after apply)
      + primary_file_endpoint            = (known after apply)
      + primary_file_host                = (known after apply)
      + primary_location                 = (known after apply)
      + primary_queue_endpoint           = (known after apply)
      + primary_queue_host               = (known after apply)
      + primary_table_endpoint           = (known after apply)
      + primary_table_host               = (known after apply)
      + primary_web_endpoint             = (known after apply)
      + primary_web_host                 = (known after apply)
      + resource_group_name              = "AzureFunction"
      + secondary_access_key             = (sensitive value)
      + secondary_blob_connection_string = (sensitive value)
      + secondary_blob_endpoint          = (known after apply)
      + secondary_blob_host              = (known after apply)
      + secondary_connection_string      = (sensitive value)
      + secondary_dfs_endpoint           = (known after apply)
      + secondary_dfs_host               = (known after apply)
      + secondary_file_endpoint          = (known after apply)
      + secondary_file_host              = (known after apply)
      + secondary_location               = (known after apply)
      + secondary_queue_endpoint         = (known after apply)
      + secondary_queue_host             = (known after apply)
      + secondary_table_endpoint         = (known after apply)
      + secondary_table_host             = (known after apply)
      + secondary_web_endpoint           = (known after apply)
      + secondary_web_host               = (known after apply)

      + blob_properties {
          + cors_rule {
              + allowed_headers    = (known after apply)
              + allowed_methods    = (known after apply)
              + allowed_origins    = (known after apply)
              + exposed_headers    = (known after apply)
              + max_age_in_seconds = (known after apply)
            }

          + delete_retention_policy {
              + days = (known after apply)
            }
        }

      + identity {
          + principal_id = (known after apply)
          + tenant_id    = (known after apply)
          + type         = (known after apply)
        }

      + network_rules {
          + bypass                     = (known after apply)
          + default_action             = (known after apply)
          + ip_rules                   = (known after apply)
          + virtual_network_subnet_ids = (known after apply)
        }

      + queue_properties {
          + cors_rule {
              + allowed_headers    = (known after apply)
              + allowed_methods    = (known after apply)
              + allowed_origins    = (known after apply)
              + exposed_headers    = (known after apply)
              + max_age_in_seconds = (known after apply)
            }

          + hour_metrics {
              + enabled               = (known after apply)
              + include_apis          = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
            }

          + logging {
              + delete                = (known after apply)
              + read                  = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
              + write                 = (known after apply)
            }

          + minute_metrics {
              + enabled               = (known after apply)
              + include_apis          = (known after apply)
              + retention_policy_days = (known after apply)
              + version               = (known after apply)
            }
        }
    }

  # azurerm_storage_blob.storage_blob will be created
  + resource "azurerm_storage_blob" "storage_blob" {
      + access_tier            = (known after apply)
      + content_type           = "application/octet-stream"
      + id                     = (known after apply)
      + metadata               = (known after apply)
      + name                   = "dist.zip"
      + parallelism            = 8
      + size                   = 0
      + source                 = "./dist.zip"
      + storage_account_name   = (known after apply)
      + storage_container_name = "func"
      + type                   = "Block"
      + url                    = (known after apply)
    }

  # azurerm_storage_container.storage_container will be created
  + resource "azurerm_storage_container" "storage_container" {
      + container_access_type   = "private"
      + has_immutability_policy = (known after apply)
      + has_legal_hold          = (known after apply)
      + id                      = (known after apply)
      + metadata                = (known after apply)
      + name                    = "func"
      + resource_manager_id     = (known after apply)
      + storage_account_name    = (known after apply)
    }

  # random_string.app_service_plan_name will be created
  + resource "random_string" "app_service_plan_name" {
      + id          = (known after apply)
      + length      = 16
      + lower       = true
      + min_lower   = 0
      + min_numeric = 0
      + min_special = 0
      + min_upper   = 0
      + number      = true
      + result      = (known after apply)
      + special     = false
      + upper       = true
    }

  # random_string.function_name will be created
  + resource "random_string" "function_name" {
      + id          = (known after apply)
      + length      = 16
      + lower       = true
      + min_lower   = 0
      + min_numeric = 0
      + min_special = 0
      + min_upper   = 0
      + number      = true
      + result      = (known after apply)
      + special     = false
      + upper       = false
    }

  # random_string.storage_name will be created
  + resource "random_string" "storage_name" {
      + id          = (known after apply)
      + length      = 16
      + lower       = true
      + min_lower   = 0
      + min_numeric = 0
      + min_special = 0
      + min_upper   = 0
      + number      = true
      + result      = (known after apply)
      + special     = false
      + upper       = false
    }

Plan: 9 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

random_string.storage_name: Creating...
random_string.app_service_plan_name: Creating...
random_string.function_name: Creating...
random_string.app_service_plan_name: Creation complete after 0s [id=2R3Z7KHYQRIcY64K]
random_string.storage_name: Creation complete after 0s [id=zsskq9eyggqyimle]
random_string.function_name: Creation complete after 0s [id=qi2fam3952tkdkha]
azurerm_resource_group.rg: Creating...
azurerm_resource_group.rg: Creation complete after 1s [id=/subscriptions/02d0e06b-ed9d-4ca5-bb9f-0a0243a9c9f2/resourceGroups/AzureFunction]
azurerm_app_service_plan.plan: Creating...
azurerm_storage_account.storage: Creating...
azurerm_app_service_plan.plan: Creation complete after 6s [id=/subscriptions/02d0e06b-ed9d-4ca5-bb9f-0a0243a9c9f2/resourceGroups/AzureFunction/providers/Microsoft.Web/serverfarms/2R3Z7KHYQRIcY64K]
azurerm_storage_account.storage: Still creating... [10s elapsed]
azurerm_storage_account.storage: Still creating... [20s elapsed]
azurerm_storage_account.storage: Creation complete after 22s [id=/subscriptions/02d0e06b-ed9d-4ca5-bb9f-0a0243a9c9f2/resourceGroups/AzureFunction/providers/Microsoft.Storage/storageAccounts/zsskq9eyggqyimle]
data.azurerm_storage_account_sas.storage_sas: Reading...
azurerm_storage_container.storage_container: Creating...
data.azurerm_storage_account_sas.storage_sas: Read complete after 0s [id=8f1e254b53e26fbc7288f45999847f558665f12a0ec1208ab4a83b90ea5959b6]
azurerm_storage_container.storage_container: Creation complete after 0s [id=https://zsskq9eyggqyimle.blob.core.windows.net/func]
azurerm_storage_blob.storage_blob: Creating...
azurerm_storage_blob.storage_blob: Creation complete after 0s [id=https://zsskq9eyggqyimle.blob.core.windows.net/func/dist.zip]
azurerm_function_app.function: Creating...
azurerm_function_app.function: Still creating... [10s elapsed]
azurerm_function_app.function: Still creating... [20s elapsed]
azurerm_function_app.function: Still creating... [30s elapsed]
azurerm_function_app.function: Still creating... [40s elapsed]
azurerm_function_app.function: Still creating... [50s elapsed]
azurerm_function_app.function: Still creating... [1m0s elapsed]
azurerm_function_app.function: Creation complete after 1m2s [id=/subscriptions/02d0e06b-ed9d-4ca5-bb9f-0a0243a9c9f2/resourceGroups/AzureFunction/providers/Microsoft.Web/sites/zsskq9eyggqyimle]

Apply complete! Resources: 9 added, 0 changed, 0 destroyed.
```
