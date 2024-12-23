---
title: 'Quickstart: Create a mesh network topology with Azure Virtual Network Manager - Azure portal'
description: Learn to a mesh virtual network topology with Azure Virtual Network Manager by using the Azure portal.
author: mbender-ms
ms.author: mbender
ms.service: azure-virtual-network-manager
ms.topic: quickstart
ms.date: 12/11/2024
ms.custom: template-quickstart, mode-ui, engagement-fy23
---

# Quickstart: Create a mesh network topology with Azure Virtual Network Manager - Azure portal

Get started with Azure Virtual Network Manager by using the Azure portal to manage connectivity for all your virtual networks.

In this quickstart, you deploy three virtual networks and use Azure Virtual Network Manager to create a mesh network topology. Then you verify that the connectivity configuration was applied.

:::image type="content" source="media/create-virtual-network-manager-portal/virtual-network-manager-resources-diagram.png" alt-text="Diagram of resources deployed for a mesh virtual network topology with Azure virtual network manager." lightbox="media/create-virtual-network-manager-portal/virtual-network-manager-resources-diagram.png":::

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- To modify dynamic network groups, you must be [granted access via Azure RBAC role](concept-network-groups.md#network-groups-and-azure-policy) assignment only. Classic Admin/legacy authorization isn't supported.

[!INCLUDE [virtual-network-manager-create-instance](../../includes/virtual-network-manager-create-instance.md)]

## Create virtual networks

Create three virtual networks by using the portal. Each virtual network has a `networkType` tag that's used for dynamic membership. If you have existing virtual networks for your mesh configuration, add the tags listed in the table to your virtual networks and skip to the next section.

1. From the **Home** screen, select **+ Create a resource** and search for **Virtual networks**. Then select **Create** to begin configuring a virtual network.

1. On the **Basics** tab, enter or select the following information.

    | Setting | Value |
    | ------- | ----- |
    | **Subscription** | Select the subscription where you want to deploy this virtual network. |
    | **Resource group** | Select **rg-learn-eastus-001**.
    | **Virtual network name** | Enter **vnet-learn-prod-eastus-001**. |
    | **Region** | Select **(US) East US**. |

1. Select **Next** or the **IP addresses** tab, configure the following network address spaces, and then select **Review + create**.

    | Setting | Value |
    | -------- | ----- |
    | **IPv4 address space** | 10.0.0.0/16 |
    | **Subnet name** | default |
    | **Subnet address space** | 10.0.0.0/24 |

1. After your configuration passes validation, select **Create** to deploy the virtual network.

1. Repeat the preceding steps to create more virtual networks with the following information:

    | Setting | Value |
    | ------- | ----- |
    | **Subscription** | Select the same subscription that you selected in step 2. |
    | **Resource group** | Select **rg-learn-eastus-001**. |
    | **Name** | Enter **vnet-learn-prod-eastus-002** and **vnet-learn-test-eastus-003** for the other virtual networks. |
    | **Region** | Select **(US) East US**. |
    | **vnet-learn-prod-eastus-002 IP addresses** | IPv4 address space: **10.1.0.0/16** </br> Subnet name: **default** </br> Subnet address space: **10.1.0.0/24**|
    | **vnet-learn-test-eastus-003 IP addresses** | IPv4 address space: **10.2.0.0/16** </br> Subnet name: **default** </br> Subnet address space: **10.2.0.0/24**|

## Create a network group

Virtual Network Manager applies configurations to groups of virtual networks by placing them in network groups. To create a network group:

[!INCLUDE [virtual-network-manager-create-network-group](../../includes/virtual-network-manager-create-network-group.md)]

## Define membership for a connectivity configuration

After you create your network group, you add virtual networks as members. Choose one of the following options for your mesh membership configuration.

# [Manual membership](#tab/manualmembership)

### Add a membership manually

In this task, you manually add two virtual networks for your mesh configuration to your network group:

1. From the list of network groups, select **ng-learn-prod-eastus-001**. On the **ng-learn-prod-eastus-001** pane, under **Manually add members**, select **Add virtual networks**.


1. On the **Manually add members** pane, select **vnet-learn-prod-eastus-001** and **vnet-learn-prod-eastus-002**, and then select **Add**.

1. On the **Network Group** pane, under **Settings**, select **Group Members**. Confirm the membership of the group that you manually selected.

    :::image type="content" source="media/create-virtual-network-manager-portal/group-members-list.png" alt-text="Screenshot that shows a list of group members." lightbox="media/create-virtual-network-manager-portal/group-members-list.png":::

# [Azure Policy](#tab/azurepolicy)

### Create a policy definition for dynamic membership

By using [Azure Policy](concept-azure-policy-integration.md), you define a condition to dynamically add two virtual networks to your network group when the name of the virtual network includes *prod*:

1. From the list of network groups, select **ng-learn-prod-eastus-001**. Under **Create policy to dynamically add members**, select **Create Azure policy**.

1. On the **Create Azure policy** pane, select or enter the following information, and then select **Preview resources**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-group-conditional.png" alt-text="Screenshot of the pane for creating an Azure policy, including criteria for definitions.":::

    | Setting | Value |
    | ------- | ----- |
    | **Policy name** | Enter **azpol-learn-prod-eastus-001**. |
    | **Scope** | Choose **Select scopes** and then select your current subscription. |
    | **Parameter** | Select **Name** from the dropdown list.|
    | **Operator** | Select **Contains** from the dropdown list.|
    | **Condition** | Enter **-prod**. |

2. The **Effective virtual networks** pane shows the virtual networks for addition to the network group based on the defined conditions in Azure Policy. When you're ready, select **Close**.

    :::image type="content" source="media/create-virtual-network-manager-portal/effective-virtual-networks.png" alt-text="Screenshot of the pane for effective virtual networks.":::

3. Select **Save** to deploy the group membership. It can take up to one minute for the policy to take effect and be added to your network group.

4. On the **Network Group** pane, under **Settings**, select **Group members** to view the membership of the group based on the conditions that you defined in Azure Policy. Confirm that **Source** is listed as **azpol-learn-prod-eastus-001 - subscriptions/subscription_id**.

    :::image type="content" source="media/create-virtual-network-manager-portal/group-members-list.png" alt-text="Screenshot of listed group members with a configured source." lightbox="media/create-virtual-network-manager-portal/group-members-list.png":::

---

## Create a configuration

Now that you created the network group and updated its membership with virtual networks, you create a mesh network topology configuration. Replace `<subscription_id>` with your subscription.

1. Under **Settings**, select **Configurations**. Then select **Create**.

1. Select **Connectivity configuration** from the dropdown menu to begin creating a connectivity configuration.

1. On the **Basics** tab, enter the following information, and then select **Next: Topology**.

    | Setting | Value |
    | ------- | ----- |
    | **Name** | Enter **cc-learn-prod-eastus-001**. |
    | **Description** | *(Optional)* Provide a description about this connectivity configuration. |

1. On the **Topology** tab, select the **Mesh** topology, and leave the **Enable mesh connectivity across regions** checkbox cleared. Cross-region connectivity isn't required for this setup, because all the virtual networks are in the same region. When you're ready, select **Add** > **Add network group**.

1. Under **Network groups**, select **ng-learn-prod-eastus-001**. Then choose **Select** to add the network group to the configuration.

1. Select the **Visualization** tab to view the topology of the configuration. This tab shows a visual representation of the network group that you added to the configuration.

    :::image type="content" source="./media/create-virtual-network-manager-portal/preview-topology.png" alt-text="Screenshot of previewing a topology for network group connectivity configuration.":::

1. Select **Next: Review + Create** > **Create** to create the configuration.

1. After the deployment finishes, select **Refresh**. The new connectivity configuration appears on the **Configurations** pane.

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration-list.png" alt-text="Screenshot of a connectivity configuration list.":::

## Deploy the connectivity configuration

To apply your configurations to your environment, you need to commit the configuration by deployment. Deploy the configuration to the East US region where the virtual networks are deployed:

1. Under **Settings**, select **Deployments**. Then select **Deploy configurations**.

1. Select the following settings, and then select **Next**.

    | Setting | Value |
    | ------- | ----- |
    | **Configurations** | Select **Include connectivity configurations in your goal state**. |
    | **Connectivity configurations** | Select **cc-learn-prod-eastus-001**. |
    | **Target regions** | Select **East US** as the deployment region. |

1. Select **Deploy** to complete the deployment.

1. Confirm that the deployment appears in the list for the selected region. The deployment of the configuration can take a few minutes to finish.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-in-progress.png" alt-text="Screenshot of a configuration deployment that shows a status of succeeded.":::

## Verify configuration deployment

Use the **Network Manager** section for each virtual network to verify that you deployed your configuration:

1. Go to the **vnet-learn-prod-eastus-001** virtual network.
1. Under **Settings**, select **Network Manager**.
1. On the **Connectivity Configurations** tab, verify that **cc-learn-prod-eastus-001** appears in the list.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-configuration-association.png" alt-text="Screenshot of a connectivity configuration listed for a virtual network." lightbox="./media/create-virtual-network-manager-portal/vnet-configuration-association.png":::

1. Repeat the previous steps on **vnet-learn-prod-eastus-002**.

## Clean up resources

If you no longer need Azure Virtual Network Manager, you can remove it after you remove all configurations, deployments, and network groups:

1. To remove all configurations from a region, start in Virtual Network Manager and select **Deploy configurations**. Select the following settings, and then select **Next**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/none-configuration.png" alt-text="Screenshot of the tab for configuring a goal state for network resources, with the option for removing existing connectivity configurations selected.":::

    | Setting | Value |
    | ------- | ----- |
    | **Configurations** | Select **Include connectivity configurations in your goal state**. |
    | **Connectivity configurations** | Select **None - Remove existing connectivity configurations**. |
    | **Target regions** | Select **East US** as the deployed region. |

1. Select **Deploy** to complete the deployment removal.

1. To delete a configuration, go to the left pane of Virtual Network Manager. Under **Settings**, select **Configurations**. Select the checkbox next to the configuration that you want to remove, and then select **Delete** at the top of the resource pane.

1. On the **Delete a configuration** pane, select the following options, and then select **Delete**.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-delete-options.png" alt-text="Screenshot of the pane for deleting a configuration.":::

    | Setting | Value |
    | ------- | ----- |
    | **Delete option** | Select **Force delete the resource and all dependent resources**. |
    | **Confirm deletion** | Enter the name of the configuration. In this example, it's **cc-learn-prod-eastus-001**. |

1. To delete a network group, go to the left pane of Virtual Network Manager. Under **Settings**, select **Network groups**. Select the checkbox next to the network group that you want to remove, and then select **Delete** at the top of the resource pane.

1. On the **Delete a network group** pane, select the following options, and then select **Delete**.

    | Setting | Value |
    | ------- | ----- |
    | **Delete option** | Select **Force delete the resource and all dependent resources**. |
    | **Confirm deletion** | Enter the name of the network group. In this example, it's **ng-learn-prod-eastus-001**. |

1. Select **Yes** to confirm the network group deletion.

1. After you remove all network groups, go to the left pane of Virtual Network Manager. Select **Overview**, and then select **Delete**.

1. On the **Delete a network manager** pane, select the following options, and then select **Delete**.

    | Setting | Value |
    | ------- | ----- |
    | **Delete option** | Select **Force delete the resource and all dependent resources**. |
    | **Confirm deletion** | Enter the name of the Virtual Network Manager instance. In this example, it's **vnm-learn-eastus-001**. |

1. Select **Yes** to confirm the deletion.

1. To delete the resource group and virtual networks, locate **rg-learn-eastus-001** and select **Delete resource group**. Confirm that you want to delete by entering **rg-learn-eastus-001** in the text box, and then select **Delete**.

## Next steps

> [!div class="nextstepaction"]
> [Block network traffic with Azure Virtual Network Manager](how-to-block-network-traffic-portal.md)
