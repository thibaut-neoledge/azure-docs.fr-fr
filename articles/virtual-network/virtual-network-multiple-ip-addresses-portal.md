---
title: "Plusieurs adresses IP pour les machines virtuelles - Portail | Microsoft Docs"
description: "Affecter plusieurs adresses IP à une machine virtuelle à l’aide du portail Azure | Resource Manager"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 7ce83952f0f16e01a4837ce2155571d223d7b551
ms.openlocfilehash: b1a2549e0f04dbc00a47a57ecc888ca36339c646


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Affecter plusieurs adresses IP à une machine virtuelle à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portail](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](virtual-network-multiple-ip-addresses-cli.md)
>

Une ou plusieurs cartes réseau sont attachées à une machine virtuelle Azure. Une ou plusieurs adresses IP publiques ou privées, statiques ou dynamiques, peuvent être affectées à chaque carte réseau. L’affectation de plusieurs adresses IP à une machine virtuelle permet :

* D’héberger plusieurs sites web ou services avec des adresses IP différentes et des certificats SSL sur un serveur unique.
* de jouer le rôle d’une appliance virtuelle réseau, telle qu’un pare-feu ou un équilibreur de charge.
* La possibilité d’ajouter l’une des adresses IP privées pour toutes les cartes réseau à un pool principal Azure Load Balancer. Auparavant, seule l’adresse IP principale de la carte réseau principale pouvait être ajoutée à un pool principal. Pour plus d’informations sur la façon d’équilibrer la charge entre plusieurs configurations IP, consultez l’article [Équilibrage de la charge entre plusieurs configurations IP](../load-balancer/load-balancer-multiple-ip.md).

Une ou plusieurs configurations IP sont associées à chaque carte réseau attachée à une machine virtuelle. Une adresse IP privée statique ou dynamique est affectée à chaque configuration. Une ressource d’adresse IP publique peut également être associée à chaque configuration. Une adresse IP statique ou dynamique est affectée à une ressource d’adresse IP publique. Pour plus d’informations sur les adresses IP dans Azure, consultez l’article [Adresses IP dans Azure](virtual-network-ip-addresses-overview-arm.md).

Cet article explique comment utiliser PowerShell pour affecter plusieurs adresses IP à une machine virtuelle créée à l’aide du modèle de déploiement Azure Resource Manager. Il n’est pas possible d’affecter plusieurs adresses IP à des ressources créées à l’aide du modèle de déploiement classique. Pour en savoir plus sur les modèles de déploiement Azure, voir [Comprendre les modèles de déploiement](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>Scénario
Une machine virtuelle avec une seule carte réseau est créée et connectée à un réseau virtuel. La machine virtuelle nécessite trois adresses IP *privées* différentes et deux adresses IP *publiques*. Les adresses IP sont affectées aux configurations IP suivantes :

* **IPConfig-1 :** attribue une adresse IP privée *dynamique* (par défaut) et une adresse IP publique *statique*.
* **IPConfig-2 :** attribue une adresse IP privée *statique* et une adresse IP publique *statique*.
* **IPConfig-3 :** attribue une adresse IP privée *dynamique* et aucune adresse IP publique.
  
    ![Plusieurs adresses IP](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Les configurations IP sont associées à la carte réseau lors de sa création, et la carte réseau est attachée à la machine virtuelle lors de la création de celle-ci. Les types d’adresses IP utilisés pour le scénario sont indiqués à titre d’illustration. Vous pouvez affecter les types d’adresses IP et d’attribution que vous souhaitez.

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Créer une machine virtuelle avec plusieurs adresses IP

Si vous souhaitez créer une machine virtuelle avec plusieurs adresses IP, vous devez utilise  PowerShell ou l’interface Azure CLI. Pour savoir comment procéder, cliquez sur l’option relative à PowerShell ou à Azure CLI dans la zone située en haut de cet article. Vous pouvez utiliser le portail pour créer une machine virtuelle avec une seule adresse IP privée statique et (éventuellement) une seule adresse IP publique. Pour cela, suivez les étapes décrites dans l’article [Créer votre première machine virtuelle Windows dans le portail Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Création d’une machine virtuelle Linux sur Azure à l’aide du portail](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Une fois la machine virtuelle créée, vous pouvez modifier les types d’adresse IP et ajouter d’autres adresses à l’aide du portail, en suivant les étapes de la section [Ajouter des adresses IP à une machine virtuelle](#add) du présent article.

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>Ajouter des adresses IP à une machine virtuelle

Vous pouvez ajouter des adresses IP privées et publiques à une carte réseau en suivant les étapes décrites ci-après. Les exemples fournis dans les sections suivantes supposent que vous disposez déjà d’une machine virtuelle avec les trois configurations IP décrites dans le [scénario](#Scenario) de cet article, mais ce n’est pas une condition obligatoire.

> [!NOTE]
> Bien que cet article affecte toutes les configurations IP à une seule carte réseau, vous pouvez également affecter plusieurs configurations IP à toute carte réseau dans une machine virtuelle. Pour découvrir comment créer une machine virtuelle avec plusieurs cartes réseau, voir [Créer une machine virtuelle avec plusieurs cartes d’interface réseau](virtual-network-deploy-multinic-arm-ps.md).

### <a name="a-namecoreaddacore-steps"></a><a name="coreadd"></a>Étapes de base

1. Inscrivez-vous pour la version préliminaire en envoyant à [Plusieurs adresses IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) un message électronique contenant votre ID d’abonnement et l’utilisation prévue. N’essayez pas d’effectuer les étapes restantes :
    - tant que vous n’avez pas reçu de message vous informant que vous avez été accepté dans la version préliminaire ;
    - sans suivre les instructions fournies dans le message que vous recevez.
2. Accédez au portail Azure en saisissant l’adresse https://portal.azure.com. Connectez-vous, si nécessaire.
3. Dans le portail, cliquez sur **Plus de services**, puis saisissez *Machines virtuelles* dans la zone de filtre et cliquez sur **Machines virtuelles**.
4. Dans le panneau **Machines virtuelles**, cliquez sur la machine virtuelle à laquelle ajouter des adresses IP. Dans le panneau qui s’affiche, cliquez sur **Interfaces réseau** et sélectionnez l’interface réseau à laquelle ajouter les adresses IP. Dans l’exemple illustré dans l’image suivante, la carte réseau nommée *myNIC* associée à la machine virtuelle *myVM* est sélectionnée :

    ![Interface réseau](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

5. Dans le panneau qui s’affiche pour la carte réseau que vous avez sélectionnée, cliquez sur **Configurations IP**, comme illustré dans l’image suivante :

    ![Configurations IP](./media/virtual-network-multiple-ip-addresses-portal/figure2.png)

Effectuez les étapes décrites dans l’une des sections qui suivent, selon le type d’adresse IP que vous souhaitez ajouter.

### <a name="add-a-private-ip-address"></a>**Ajouter une adresse IP privée**

Procédez comme suit pour ajouter une nouvelle adresse IP privée :

1. Suivez les étapes de la section [Étapes de base](#coreadd) du présent article.
2. Cliquez sur **Ajouter**. Dans le panneau **Ajouter une configuration IP** qui s’affiche, créez une configuration IP appelée *IPConfig-4*, en lui associant l’adresse *10.0.0.7* à titre d’adresse IP privée *statique*, puis cliquez sur **OK**, comme indiqué dans l’image suivante :

    ![Ajouter une adresse IP privée](./media/virtual-network-multiple-ip-addresses-portal/figure3.png)

    > [!NOTE]
    > Lorsque vous ajoutez une adresse IP statique, vous devez spécifier une adresse valide, non utilisée, sur le sous-réseau auquel la carte réseau est connectée. Si l’adresse IP que vous sélectionnez n’est pas disponible, le portail lui associe une croix et vous devez en choisir une autre.

    Si vous voulez que la valeur du champ **Méthode d’allocation** de l’adresse IP privée soit *Dynamique*, sélectionnez-la. Dans ce cas, vous n’aurez plus besoin de spécifier une adresse IP.
3. Lorsque vous cliquez sur OK, le panneau se ferme et la liste des nouvelles configurations IP s’affiche, comme l’indique l’image suivante :

    ![Configurations IP](./media/virtual-network-multiple-ip-addresses-portal/figure4.png)

    Cliquez sur **OK** pour fermer le panneau **Ajouter une configuration IP**.
4. Vous pouvez cliquer sur **Ajouter** pour ajouter des configurations IP supplémentaires, ou fermer tous les panneaux ouverts pour terminer l’ajout d’adresses IP.
5. Ajoutez les adresses IP privées dans le système d’exploitation de la machine virtuelle en suivant les étapes relatives à ce système décrites dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) du présent article.

### <a name="add-a-public-ip-address"></a>Ajouter une adresse IP publique

Vous ajoutez une adresse IP publique en associant une ressource d’adresse IP publique à une configuration IP nouvelle ou existante.

> [!NOTE]
> Les adresses IP publiques ont un coût nominal. Pour en savoir plus, lisez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Il existe une limite au nombre d’adresses IP publiques qui peuvent être utilisées dans un abonnement. Pour plus d’informations sur les limites, voir [Limites d’Azure](../azure-subscription-service-limits.md#networking-limits).
> 

### <a name="a-namecreate-public-ipacreate-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Créer une ressource d’adresse IP publique

Une adresse IP publique correspond à un paramètre de configuration d’une ressource d’adresse IP publique. Si vous disposez d’une ressource d’adresse IP publique qui n’est pas actuellement associée à une configuration IP, effectuez cette opération en ignorant la procédure ci-après, et appliquez les étapes décrites dans l’une des sections qui suivent, le cas échéant. Si aucune ressource d’adresse IP publique n’est disponible, procédez comme suit pour en créer une :

1. Accédez au portail Azure en saisissant l’adresse https://portal.azure.com. Connectez-vous, si nécessaire.
3. Dans le portail, cliquez sur **Nouveau** > **Mise en réseau** > **Adresse IP publique**.
4. Dans le panneau **Créer une adresse IP publique** qui s’affiche, indiquez un nom dans le champ **Nom**, sélectionnez une valeur dans les champs **Affectation d’adresses IP**, **Abonnement**, **Groupe de ressources** et **Emplacement**, puis cliquez sur **Créer**, comme indiqué dans l’image suivante :

    ![Créer une ressource d’adresse IP publique](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Effectuez les étapes décrites dans l’une des sections qui suivent pour associer la ressource d’adresse IP publique à une configuration IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associer la ressource d’adresse IP publique à une nouvelle configuration IP

1. Suivez les étapes de la section [Étapes de base](#coreadd) du présent article.
2. Cliquez sur **Ajouter**. Dans le panneau **Ajouter une configuration IP** qui s’affiche, créez une configuration IP nommée *IPConfig-4*. Activez la case à cocher **Adresse IP publique** et choisissez une ressource d’adresse IP publique disponible dans le panneau **Choisir une adresse IP publique** qui s’affiche, comme indiqué dans l’image suivante :

    ![Nouvelle configuration IP](./media/virtual-network-multiple-ip-addresses-portal/figure6.png)

    Une fois que vous avez sélectionné la ressource d’adresse IP publique, cliquez sur **OK**. Le panneau se ferme. Le cas échéant, vous pouvez créer une adresse IP publique en suivant la procédure de la section [Créer une ressource d’adresse IP publique](#create-public-ip) du présent article. 

3. Passez en revue la nouvelle configuration IP, comme indiqué dans l’image suivante :

    ![Configurations IP](./media/virtual-network-multiple-ip-addresses-portal/figure7.png)

    > [!NOTE]
    > Même si aucune adresse IP privée n’a été explicitement affectée, une adresse de ce type a été automatiquement affectée à la configuration IP, car toutes les configurations IP doivent avoir une adresse IP privée.
    >

4. Vous pouvez cliquer sur **Ajouter** pour ajouter des configurations IP supplémentaires, ou fermer tous les panneaux ouverts pour terminer l’ajout d’adresses IP.
5. Ajoutez l’adresse IP privée dans le système d’exploitation de la machine virtuelle en suivant les étapes relatives à ce système décrites dans la section [Ajouter des adresses IP à un système d’exploitation de machine virtuelle](#os-config) du présent article. N’ajoutez pas l’adresse IP publique dans le système d’exploitation.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associer la ressource d’adresse IP publique à une configuration IP existante

1. Suivez les étapes de la section [Étapes de base](#coreadd) du présent article.
2. Sélectionnez la configuration IP à laquelle ajouter la ressource d’adresse IP publique, activez la case à cocher Adresse IP publique, puis sélectionnez une ressource d’adresse IP publique disponible. Dans l’exemple illustré dans l’image suivante, la ressource d’adresse IP publique *myPublicIp3* est associée à la configuration *IPConfig-3*.

    ![Configuration IP existante](./media/virtual-network-multiple-ip-addresses-portal/figure8.png)

    Une fois que vous avez sélectionné la ressource d’adresse IP publique, cliquez sur **Enregistrer**. Les panneaux se ferment. Le cas échéant, vous pouvez créer une adresse IP publique en suivant la procédure de la section [Créer une ressource d’adresse IP publique](#create-public-ip) du présent article.

3. Passez en revue la nouvelle configuration IP, comme indiqué dans l’image suivante :

    ![Configurations IP](./media/virtual-network-multiple-ip-addresses-portal/figure9.png)

4. Vous pouvez cliquer sur **Ajouter** pour ajouter des configurations IP supplémentaires, ou fermer tous les panneaux ouverts pour terminer l’ajout d’adresses IP. N’ajoutez pas l’adresse IP publique dans le système d’exploitation.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]



<!--HONumber=Dec16_HO2-->


