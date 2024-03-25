Terraform Loops example using Azure Resource Locks
========================================================================

Enable WCF tracing

.. code-block:: terraform

  variable "resource_locks" {
    type = map(object({
      resource_group_name       = string
      resource_type             = string
      delete_protection_enabled = bool
    }))
    default = {
      example_lock1 = {
        resource_group_name       = "example_resource_group"
        resource_type             = "example_resource_type"
        delete_protection_enabled = true
      }
      example_lock2 = {
        resource_group_name       = "another_resource_group"
        resource_type             = "another_resource_type"
        delete_protection_enabled = false
      }
    }
  }

  data "azurerm_postgresql_flexible_server" "servers" {
    for_each = { for key, value in var.resource_locks : key => value
    if value.resource_type == "azurerm_postgresql_flexible_server" && value.delete_protection_enabled }

    provider            = azurerm.automation-provider
    name                = each.key
    resource_group_name = each.value.resource_group_name
  }

  resource "azurerm_management_lock" "postgresql_flexible_server" {
    for_each = data.azurerm_postgresql_flexible_server.servers

    provider   = azurerm.automation-provider
    name       = each.value.name
    scope      = each.value.id
    lock_level = "CanNotDelete"
    notes      = "Locked because it's locked by the azurerm_management_lock module"
  }