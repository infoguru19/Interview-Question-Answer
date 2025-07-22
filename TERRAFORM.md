## How to create multiple storage account through terraform but i will pass storage account names from variable.tf ?
**variable.tf**
```
variable "storage_account_names" {
  description = "List of storage account names to create"
  type        = list(string)
  default     = ["stacct01demo", "stacct02demo", "stacct03demo"]  # you can override this via tfvars
}

variable "resource_group_name" {
  description = "Resource group to deploy storage accounts"
  type        = string
}

variable "location" {
  description = "Azure region"
  type        = string
}
```

**main.tf**
```
provider "azurerm" {
  features {}
}

resource "azurerm_storage_account" "this" {
  for_each                     = toset(var.storage_account_names)
  name                         = each.value
  resource_group_name          = var.resource_group_name
  location                     = var.location
  account_tier                 = "Standard"
  account_replication_type     = "LRS"
  allow_blob_public_access     = false
  min_tls_version              = "TLS1_2"

  tags = {
    environment = "test"
    created_by  = "terraform"
  }
}
```

**terraform.tfvars**
resource_group_name      = "rg-demo"
location                 = "eastus"
storage_account_names    = ["stacteast01", "stacteast02"]



