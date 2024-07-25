Azure Resource Graph Explorer
========================================================================


Get information about AKS and logical network usage from HCI Clusters.

.. code-block:: kql

  resources
  | where type =~ "microsoft.kubernetes/connectedclusters" and kind in~ ("ProvisionedCluster")
  | project clusterId = tolower(id), name, properties
  | join (
      ExtensibilityResources
      | where type =~ "microsoft.hybridcontainerservice/provisionedclusterinstances"
      | parse kind=regex flags=i id with clusterId "/providers/Microsoft.HybridContainerService/provisionedClusterInstances/default"
      | project customLocation = tostring(extendedLocation["name"]), clusterId = tolower(clusterId), networkID = tolower(properties.cloudProviderProfile.infraNetworkProfile.vnetSubnetIds[0]), clusterProperties=properties
      | where customLocation =~ "/subscriptions/[xxx-xxx-xxx-xxx]/resourcegroups/rg-hci-poc/providers/microsoft.extendedlocation/customlocations/jumpstart" // this is optional
      | join (
          resources
          | where type =~ "microsoft.azurestackhci/logicalnetworks"
          | project networkID = tolower(id), networkTags = tags, networkProperties = properties
      ) on networkID
  ) on clusterId
  | project clusterId, name, vlan=networkProperties.subnets[0].properties.vlan, addressPrefix=networkProperties.subnets[0].properties.addressPrefix, connectedClusterProperties=properties, customLocation, networkID, networkTags, networkProperties, clusterProperties
