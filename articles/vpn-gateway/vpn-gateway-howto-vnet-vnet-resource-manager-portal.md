---
title: "Connecter un réseau virtuel Azure à un autre réseau virtuel : Portail | Microsoft Docs"
description: "Créer une connexion de passerelle VPN entre des réseaux virtuels à l’aide de Resource Manager et du portail Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: c614efa25e2e7ddbb9b6c90094cf84fa8b6b4243
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017


---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Configurer une connexion de passerelle VPN de réseau virtuel à réseau virtuel à l’aide du portail Azure

Cet article vous explique comment créer une connexion de passerelle VPN entre des réseaux virtuels. Les réseaux virtuels peuvent être situés dans des régions identiques ou différentes et appartenir à des abonnements identiques ou différents. Les étapes mentionnées dans cet article s’appliquent au modèle de déploiement Resource Manager et au portail Azure. Vous pouvez également créer cette configuration à l’aide d’un autre outil ou modèle de déploiement en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interface de ligne de commande Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portail Azure (classique)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connexions entre différents modèles de déploiement - Portail Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connexions entre différents modèles de déploiement - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagramme v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Si vos réseaux virtuels sont situés dans la même région, vous souhaiterez peut-être les connecter à l’aide de VNet Peering. L’homologation de réseaux virtuels (ou VNet Peering) n’utilise pas de passerelle VPN. Pour plus d’informations, consultez l’article [Homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).

Vous pouvez combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels, comme indiqué dans le schéma suivant :

![À propos des connexions](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "À propos des connexions")

### <a name="why-connect-virtual-networks"></a>Pourquoi connecter des réseaux virtuels ?

Vous pouvez décider de connecter des réseaux virtuels pour les raisons suivantes :

* **Géo-redondance et présence géographique dans plusieurs régions**
  
  * Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
  * Avec Traffic Manager et l’équilibreur de charge Azure, vous pouvez configurer une charge de travail hautement disponible avec la géo-redondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.
* **Applications multiniveaux régionales avec une limite d’isolement ou administrative**
  
  * Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés pour des besoins d’isolement ou d’administration.

Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez le [Forum Aux Questions sur l’interconnexion de réseaux virtuels](#faq) à la fin de cet article. Notez que si vos réseaux virtuels figurent dans des abonnements différents, vous ne pourrez pas créer la connexion dans le portail. Dans ce cas, vous pouvez utiliser [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

### <a name="values"></a>Exemples de paramètres
Lorsque vous suivez ces étapes dans le cadre d’un exercice, vous pouvez vous servir de ces exemples de paramètres. À titre d’exemple, nous utilisons plusieurs espaces d’adressage pour chaque réseau virtuel. Toutefois, les configurations de réseau virtuel à réseau virtuel ne nécessitent pas plusieurs espaces d’adressage.

**Valeurs pour TestVNet1 :**

* Nom du réseau virtuel : TestVNet1
* Espace d’adressage : 10.11.0.0/16
  * Nom du sous-réseau : FrontEnd
  * Plage d’adresses de sous-réseau : 10.11.0.0/24
* Groupe de ressources : TestRG1
* Emplacement : Est des États-Unis
* Espace d’adressage : 10.12.0.0/16
  * Nom du sous-réseau : BackEnd
  * Plage d’adresses de sous-réseau : 10.12.0.0/24
* Nom du sous-réseau de passerelle : GatewaySubnet (renseigné automatiquement dans le portail)
  * Plage d’adresses de sous-réseau de passerelle : 10.11.255.0/27
* Serveur DNS : utilisez l’adresse IP de votre serveur DNS
* Nom de passerelle de réseau virtuel : TestVNet1GW
* Type de passerelle : VPN
* Type de VPN : Route-based
* Référence (SKU): la référence de la passerelle que vous souhaitez utiliser
* Nom de l’adresse IP publique : TestVNet1GWIP
* Valeurs de connexion :
  * Nom : TestVNet1toTestVNet4
  * Clé partagée : vous pouvez créer la clé partagée vous-même. Pour cet exemple, nous allons utiliser abc123. L’important est que lorsque vous créez la connexion entre les réseaux virtuels, la valeur doit correspondre.

**Valeurs pour TestVNet4 :**

* Nom du réseau virtuel : TestVNet4
* Espace d’adressage : 10.41.0.0/16
  * Nom du sous-réseau : FrontEnd
  * Plage d’adresses de sous-réseau : 10.41.0.0/24
* Groupe de ressources : TestRG1
* Emplacement : États-Unis de l’Ouest
* Espace d’adressage : 10.42.0.0/16
  * Nom du sous-réseau : BackEnd
  * Plage d’adresses de sous-réseau : 10.42.0.0/24
* Nom du sous-réseau de passerelle : GatewaySubnet (renseigné automatiquement dans le portail)
  * Plage d’adresses de GatewaySubnet : 10.41.255.0/27
* Serveur DNS : utilisez l’adresse IP de votre serveur DNS
* Nom de passerelle de réseau virtuel : TestVNet4GW
* Type de passerelle : VPN
* Type de VPN : Route-based
* Référence (SKU): la référence de la passerelle que vous souhaitez utiliser
* Nom de l’adresse IP publique : TestVNet4GWIP
* Valeurs de connexion :
  * Nom : TestVNet4toTestVNet1
  * Clé partagée : vous pouvez créer la clé partagée vous-même. Pour cet exemple, nous allons utiliser abc123. L’important est que lorsque vous créez la connexion entre les réseaux virtuels, la valeur doit correspondre.

## <a name="CreatVNet"></a>1. Créer et configurer TestVNet1
Si vous disposez déjà d’un réseau virtuel, vérifiez que les paramètres sont compatibles avec la conception de votre passerelle VPN, avec une attention particulière pour tous les sous-réseaux qui pourraient chevaucher d’autres réseaux. Si vos sous-réseaux se chevauchent, votre connexion ne fonctionnera pas correctement. Si votre réseau virtuel est correctement configuré, vous pouvez commencer à suivre les étapes de la section [Spécifier un serveur DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Ajouter des espaces d’adressage supplémentaires et créer des sous-réseaux
Vous pouvez ajouter des espaces d’adressage supplémentaires et créer des sous-réseaux pour votre réseau virtuel une fois qu’il a été créé.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Créer un sous-réseau de passerelle
Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Si possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour satisfaire les exigences de configuration future supplémentaires.

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [Exemples de paramètres](#values) lorsque vous créez votre sous-réseau de passerelle.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Spécifier un serveur DNS (facultatif)
Aucun DNS n’est nécessaire pour les connexions entre des réseaux virtuels. Toutefois, si vous souhaitez résoudre les noms des ressources qui sont déployées sur votre réseau virtuel, vous devez spécifier un serveur DNS. Ce paramètre vous permet de spécifier le serveur DNS que vous souhaitez utiliser pour la résolution de noms pour ce réseau virtuel. Il n'entraîne pas la création d'un serveur DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Créer une passerelle de réseau virtuel
Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. La création d’une passerelle nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle sélectionnée. Si vous créez cette configuration dans le cadre d’un exercice, vous pouvez vous reporter aux [Exemples de paramètres](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Créer et configurer TestVNet4
Une fois que vous avez configuré TestVNet1, créez TestVNet4 en répétant les étapes précédentes, en remplaçant les valeurs par celles de TestVNet4. Vous n’avez pas besoin d’attendre que la création de la passerelle de réseau virtuel pour TestVNet1 soit terminée pour configurer TestVNet4. Si vous utilisez vos propres valeurs, assurez-vous que les espaces d’adressage ne chevauchent pas les réseaux virtuels auxquels vous souhaitez vous connecter.

## <a name="TestVNet1Connection"></a>7. Configurer la connexion TestVNet1
Lorsque les passerelles de réseau virtuel pour TestVNet1 et TestVNet4 sont terminées, vous pouvez créer vos connexions de passerelle de réseau virtuel. Dans cette section, vous allez créer une connexion de VNet1 à VNet4. Ces étapes s’appliquent uniquement aux réseaux virtuels situés dans le même abonnement. Si vos réseaux virtuels figurent dans des abonnements différents, vous devrez utiliser PowerShell pour établir la connexion. Consultez l’article concernant [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

1. Dans **Toutes les ressources**, accédez à la passerelle de réseau virtuel pour votre réseau virtuel. Par exemple, **TestVNet1GW**. Cliquez sur **TestVNet1GW** pour ouvrir le panneau de la passerelle de réseau virtuel.
   
    ![Panneau Connexions](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Panneau Connexions")
2. Cliquez sur **+Ajouter** pour ouvrir le panneau **Ajouter une connexion**.
3. Dans le panneau **Ajouter une connexion**, dans le champ Nom, tapez un nom pour votre connexion. Par exemple, **TestVNet1toTestVNet4**.
   
    ![Nom de la connexion](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nom de la connexion")
4. Pour **Type de connexion**. sélectionnez **Réseau virtuel à réseau virtuel** dans la liste déroulante.
5. La valeur du champ **Première passerelle de réseau virtuel** est automatiquement renseignée parce que vous créez cette connexion à partir de la passerelle de réseau virtuel spécifiée.
6. Le champ **Deuxième passerelle de réseau virtuel** correspond à la passerelle de réseau virtuel sur laquelle vous souhaitez créer une connexion. Cliquez sur **Choisir une autre passerelle de réseau virtuel** pour ouvrir le panneau **Choisir la passerelle de réseau virtuel**.
   
    ![Ajouter une connexion](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Ajouter une connexion")
7. Affichez les passerelles de réseau virtuel qui sont répertoriées dans ce panneau. Notez que seules les passerelles de réseau virtuel incluses dans votre abonnement sont répertoriées. Si vous souhaitez vous connecter à une passerelle de réseau virtuel qui ne se trouve pas dans votre abonnement, veuillez utiliser l’[article PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Cliquez sur la passerelle de réseau virtuel à laquelle vous souhaitez vous connecter.
9. Dans le champ **Clé partagée**, tapez une clé partagée pour votre connexion. Vous pouvez générer ou créer cette clé vous-même. Dans une connexion de site à site, la clé que vous utilisez serait exactement la même pour votre appareil local et votre connexion de passerelle de réseau virtuel. Le concept est similaire, sauf qu’au lieu de se connecter à un périphérique VPN, vous vous connectez à une autre passerelle de réseau virtuel.
   
    ![Clé partagée](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Clé partagée")
10. Cliquez sur **OK** en bas du panneau pour enregistrer vos modifications.

## <a name="TestVNet4Connection"></a>8. Configurer la connexion TestVNet4
Créez ensuite une connexion de TestVNet4 à TestVNet1. Utilisez la même méthode que celle utilisée pour créer la connexion entre TestVNet1 et TestVNet4. Vérifiez que vous utilisez la même clé partagée.

## <a name="VerifyConnection"></a>9. Vérifier votre connexion
Vérifiez la connexion. Pour chaque passerelle de réseau virtuel, procédez comme suit :

1. Recherchez le panneau de passerelle de réseau virtuel. Par exemple, **TestVNet4GW**. 
2. Dans le panneau de la passerelle de réseau virtuel, cliquez sur **Connexions** pour afficher le panneau des connexions de la passerelle de réseau virtuel.

Affichez les connexions et vérifiez l’état. Une fois que la connexion créée, vous verrez les valeurs d’état **Réussi** et **Connecté**.

![Réussite](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Réussite")

Vous pouvez double-cliquer sur chaque connexion séparément pour afficher plus d’informations sur la connexion.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>Forum Aux Questions sur l’interconnexion de réseaux virtuels
Consultez les détails du Forum Aux Questions pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez la [documentation relative aux machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) .

