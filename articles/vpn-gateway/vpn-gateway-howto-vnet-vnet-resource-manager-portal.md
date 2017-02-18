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
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eadb1f29da69e7f6fcc2c7c19ba67f4e3072c346
ms.openlocfilehash: 7796ec3a7c65e320ca142de4d03f6de5d0698e21


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configurer une connexion de réseau virtuel à réseau virtuel à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Classic - Portail Classic](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Cet article vous guidera au long des étapes de création d’une connexion entre des réseaux virtuels dans le modèle de déploiement Resource Manager à l’aide d’une passerelle VPN et du portail Azure.

Lorsque vous utilisez le portail Azure pour connecter des réseaux virtuels, les réseaux virtuels doivent se trouver dans le même abonnement. Si vos réseaux virtuels se trouvent dans des abonnements différents, vous pouvez toujours les connecter à l’aide des étapes [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

![Diagramme v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modèles et méthodes de déploiement pour les connexions de réseau virtuel à réseau virtuel
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant présente les modèles et les méthodes de déploiement disponibles pour les configurations de connexion de réseau virtuel à réseau virtuel. Quand un article avec les étapes de configuration est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet Peering**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>À propos des connexions de réseau virtuel à réseau virtuel
La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN Azure pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Les réseaux virtuels que vous connectez peuvent être situés dans différents abonnements et différentes régions.

Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels, comme indiqué dans le schéma suivant :

![À propos des connexions](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "À propos des connexions")

### <a name="why-connect-virtual-networks"></a>Pourquoi connecter des réseaux virtuels ?
Vous pouvez décider de connecter des réseaux virtuels pour les raisons suivantes :

* **Géo-redondance et présence géographique dans plusieurs régions**
  
  * Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
  * Avec Traffic Manager et l’équilibreur de charge Azure, vous pouvez configurer une charge de travail hautement disponible avec la géo-redondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.
* **Applications multiniveaux régionales avec une limite d’isolement ou administrative**
  
  * Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés pour des besoins d’isolement ou d’administration.

Pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel, consultez la partie [Interconnexion de réseaux virtuels](#faq) à la fin de cet article.

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Exemples de paramètres
Lorsque vous suivez ces étapes dans le cadre d’un exercice, vous pouvez utiliser les exemples de valeurs de configuration suivantes : À titre d’exemple, nous utilisons plusieurs espaces d’adressage pour chaque réseau virtuel. Toutefois, les configurations de réseau virtuel à réseau virtuel ne nécessitent pas plusieurs espaces d’adressage.

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

## <a name="a-namecreatvneta1-create-and-configure-testvnet1"></a><a name="CreatVNet"></a>1. Créer et configurer TestVNet1
Si vous disposez déjà d’un réseau virtuel, vérifiez que les paramètres sont compatibles avec la conception de votre passerelle VPN, avec une attention particulière pour tous les sous-réseaux qui pourraient chevaucher d’autres réseaux. Si vos sous-réseaux se chevauchent, votre connexion ne fonctionnera pas correctement. Si votre réseau virtuel est correctement configuré, vous pouvez commencer à suivre les étapes de la section [Spécifier un serveur DNS](#dns).

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-create-subnets"></a><a name="subnets"></a>2. Ajouter des espaces d’adressage supplémentaires et créer des sous-réseaux
Vous pouvez ajouter des espaces d’adressage supplémentaires et créer des sous-réseaux pour votre réseau virtuel une fois qu’il a été créé.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namegatewaysubneta3-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>3. Créer un sous-réseau de passerelle
Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Si possible, il est préférable de créer un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou /27 afin de fournir suffisamment d’adresses IP pour satisfaire les exigences de configuration future supplémentaires.

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [Exemples de paramètres](#values) lorsque vous créez votre sous-réseau de passerelle.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Pour créer un sous-réseau de passerelle
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namednsservera4-specify-a-dns-server-optional"></a><a name="DNSServer"></a>4. Spécifier un serveur DNS (facultatif)
Si vous souhaitez résoudre les noms des machines virtuelles qui sont déployées sur vos réseaux virtuels, vous devez spécifier un serveur DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Créer une passerelle de réseau virtuel
Dans cette étape, vous créez la passerelle de réseau virtuel de votre réseau virtuel. Cette étape peut prendre jusqu’à 45 minutes. Si vous créez cette configuration dans le cadre d’un exercice, vous pouvez vous reporter aux [Exemples de paramètres](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Pour créer une passerelle de réseau virtuel
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namecreatetestvnet4a6-create-and-configure-testvnet4"></a><a name="CreateTestVNet4"></a>6. Créer et configurer TestVNet4
Une fois que vous avez configuré TestVNet1, créez TestVNet4 en répétant les étapes précédentes, en remplaçant les valeurs par celles de TestVNet4. Vous n’avez pas besoin d’attendre que la création de la passerelle de réseau virtuel pour TestVNet1 soit terminée pour configurer TestVNet4. Si vous utilisez vos propres valeurs, assurez-vous que les espaces d’adressage ne chevauchent pas les réseaux virtuels auxquels vous souhaitez vous connecter.

## <a name="a-nametestvnet1connectiona7-configure-the-testvnet1-connection"></a><a name="TestVNet1Connection"></a>7. Configurer la connexion TestVNet1
Lorsque les passerelles de réseau virtuel pour TestVNet1 et TestVNet4 sont terminées, vous pouvez créer vos connexions de passerelle de réseau virtuel. Dans cette section, vous allez créer une connexion de VNet1 à VNet4.

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

## <a name="a-nametestvnet4connectiona8-configure-the-testvnet4-connection"></a><a name="TestVNet4Connection"></a>8. Configurer la connexion TestVNet4
Créez ensuite une connexion de TestVNet4 à TestVNet1. Utilisez la même méthode que celle utilisée pour créer la connexion entre TestVNet1 et TestVNet4. Vérifiez que vous utilisez la même clé partagée.

## <a name="a-nameverifyconnectiona9-verify-your-connection"></a><a name="VerifyConnection"></a>9. Vérifier votre connexion
Vérifiez la connexion. Pour chaque passerelle de réseau virtuel, procédez comme suit :

1. Recherchez le panneau de passerelle de réseau virtuel. Par exemple, **TestVNet4GW**. 
2. Dans le panneau de la passerelle de réseau virtuel, cliquez sur **Connexions** pour afficher le panneau des connexions de la passerelle de réseau virtuel.

Affichez les connexions et vérifiez l’état. Une fois que la connexion créée, vous verrez les valeurs d’état **Réussi** et **Connecté**.

![Réussite](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Réussite")

Vous pouvez double-cliquer sur chaque connexion séparément pour afficher plus d’informations sur la connexion.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>Interconnexion de réseaux virtuels
Consultez les détails du Forum Aux Questions pour plus d’informations sur les connexions de réseau virtuel à réseau virtuel.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez la [documentation relative aux machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) .



<!--HONumber=Jan17_HO4-->


