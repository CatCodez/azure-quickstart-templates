---
description: Deploys a Virtual Machine to by a Hyper-V Host and all dependent resources including virtual network, public IP address and route tables.
page_type: sample
products:
- azure
- azure-resource-manager
urlFragment: nested-vms-in-virtual-network
languages:
- bicep
- json
---
# Hyper-V Host Virtual Machine with nested VMs


This template creates the following resources by default:

+    Virtual Network with four Subnets
+    Virtual Machine to be the Hyper-V Host
+    Public IP Address for remote access to Hyper-V Host
+    Network Security Groups with Default Rules
+    Route Table for Azure Virtual Machines to communicate with nested Virtual Machines
+    DSC Extension to install Windows Features
+    Custom Script Extension to configure Hyper-V Server

Click the button below to deploy from the portal:

[![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FCatCodez%2Fazure-quickstart-templates%2Fmaster%2Fdemos%2Fnested-vms-in-virtual-network%2Fazuredeploy.json)


## Final Configuration

The environment in this guide has the below configurations. This section is intended to be used as a reference.

1. Azure Virtual Network Information.
    + VNet High Level Configuration.
        + Name: Nested-Fun
        + Address Space: 10.0.0.0/22
        + Note: This will be made up of four Subnets. Also, these ranges are not set in stone. Feel free to address your environment however you want.

    + First Subnet High Level Configuration.
        + Name: NAT
        + Address Space: 10.0.0.0/24
        + Note: This is where our Hyper-V hosts primary NIC resides. This will be used to handle outbound NAT for the nested VMs. It will be the gateway to the internet for your nested VMs.

    + Second Subnet High Level Configuration.
        + Name: Hyper-V-LAN
        + Address Space: 10.0.1.0/24
        + Note:  Our Hyper-V host will have a second NIC that will be used to handle the routing between the nested VMs and non-internet resources external to the Hyper-V host.

    + Third Subnet High Level Configuration.
        + Name: Ghosted
        + Address Space: 10.0.2.0/24
        + Note:  This will be a “floating” subnet. The address space will be consumed by our nested VMs and exists to handle route advertisements back to on-premises. No VMs will actually be deployed into this subnet.

    + Fourth Subnet High Level Configuration.
        + Name: Azure-VMs
        + Address Space: 10.0.3.0/24
        + Note: Subnet containing Azure VMs.

2. Our Hyper-V host has the below NIC configurations.
    + Primary NIC
        + IP Address: 10.0.0.4
        + Subnet Mask: 255.255.255.0
        + Default Gateway: 10.0.0.1
        + DNS: Configured for DHCP
        + IP Forwarding Enabled: No

    + Secondary NIC
        + IP Address: 10.0.1.4
        + Subnet Mask: 255.255.255.0
        + Default Gateway: Empty
        + DNS: Configured for DHCP
        + IP Forwarding Enabled: Yes

    + Hyper-V Created NIC for Internal Virtual Switch
        + IP Address: 10.0.2.1
        + Subnet Mask: 255.255.255.0
        + Default Gateway: Empty

3. Our Route Table will have a single rule.
    + Rule 1
        + Name: Nested-VMs
        + Destination: 10.0.2.0/24
        + Next Hop: Virtual Appliance - 10.0.1.4

## Post Deployment Steps

Once the deployment is complete to access your Hyper-V Host an inbound security rule will need to be created on your NAT Subnet NSG.

Beyond this the solution does support network communication between on-premises resources and the nested virtual machines. To achieve this route tables will need to be created on the GatewaySubnet and additional routes created in RRAS on the Hyper-V Host

## Notes

+ If you are going to modify and use existing Virtual Networks, NSGs or any NVAs outbound internet access is required for Hyper-V Host Virtual Machines for setup to complete.

Tags: ``nested, hyper-v, windows server 2016, ws2016``

`Tags: Microsoft.Network/publicIPAddresses, Microsoft.Network/networkSecurityGroups, Microsoft.Network/virtualNetworks, Microsoft.Compute/virtualMachines, Microsoft.Compute/virtualMachines/extensions, DSC, CustomScriptExtension, Microsoft.Resources/deployments, Microsoft.Network/networkInterfaces, Microsoft.Network/routeTables`
