---
title: "Homologation de réseaux virtuels Azure - Portail | Microsoft Docs"
description: "Découvrez comment créer une homologation de réseaux virtuels au moyen du portail Azure."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: a4a770ec6f258c62864cd3fcc79b33b5cac38ace
ms.openlocfilehash: 8f05957e8c40dee8ceb7fb1355d8d8776935cf5a
ms.lasthandoff: 02/24/2017


---
# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Créer une homologation de réseaux virtuels à l’aide du portail Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer une homologation de réseaux virtuels selon le scénario ci-dessus à l’aide du portail Azure, suivez les étapes ci-dessous :

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Pour établir une homologation de réseaux virtuels, vous devez créer un lien entre deux réseaux virtuels pour chaque direction. Vous pouvez créer tout d’abord le lien d’homologation de réseaux virtuels de VNet1 à VNet2. Dans le portail Azure, cliquez sur **Parcourir** > ** et choisissez Réseaux virtuels**

    ![Créer une homologation de réseaux virtuels dans le portail Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. Dans le panneau **Réseaux virtuels**, choisissez *VNET1* et cliquez sur **Homologations**, puis sur **Ajouter**, comme illustré dans l’image suivante :

    ![Choisir l’homologation](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. Dans le panneau **Ajouter l’homologation**, entrez *LinkToVnet2* comme **Nom**, choisissez un abonnement et le **réseau virtuel** homologue *VNET2*, puis cliquez sur **OK**.

    ![Lien vers le réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. Une fois le lien d’homologation de réseaux virtuels créé, vous pouvez voir l’état du lien, tel qu’illustré dans l’image suivante :

    ![État du lien](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. Créez le lien d’homologation de réseaux virtuels de VNET2 à VNET1. Dans le panneau **Réseaux virtuels**, choisissez *VNET2* et cliquez sur **Homologations**, puis sur **Ajouter**, comme illustré dans l’image suivante :

    ![Homologue depuis l’autre réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. Dans le panneau **Ajouter l’homologation**, entrez *LinkToVnet1* comme **Nom**, choisissez l’abonnement, sélectionnez *VNET1* comme **réseau virtuel**, puis cliquez sur **OK**.

    ![Mosaïque de création du réseau virtuel](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. Une fois le lien d’homologation de réseaux virtuels créé, vous pouvez voir l’état du lien, tel qu’illustré dans l’image suivante :

    ![État final du lien](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. Vérifiez l’état du lien **LinkToVnet2**. Celui-ci est à présent également défini sur *Connecté*.  
    
    ![État final du lien 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)

    > [!NOTE]
    > L’homologation de réseaux virtuels est établie uniquement si les deux liens sont connectés.
    > 
    > 

Il existe plusieurs propriétés configurables pour chaque lien :

| Option | Description | Default |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |Indique si l’espace d’adressage du réseau virtuel homologue doit être inclus dans le cadre de la balise Virtual_network. |Oui |
| AllowForwardedTraffic |Spécifie si le trafic ne provenant pas d’un réseau virtuel homologué est accepté ou rejeté. |Non |
| AllowGatewayTransit |Permet au réseau virtuel homologue d’utiliser votre passerelle de réseau virtuel. |Non |
| UseRemoteGateways |Permet d’utiliser la passerelle de votre réseau virtuel homologue. Le réseau virtuel homologue doit avoir une passerelle configurée avec la propriété AllowGatewayTransit sélectionnée. Vous ne pouvez pas utiliser cette option si vous avez une passerelle configurée. |Non |

Chaque lien de l’homologation de réseaux virtuels présente l’ensemble des propriétés ci-dessus. Depuis le portail, vous pouvez cliquer sur le lien **d’homologation de réseaux virtuels** et modifier les options disponibles. Cliquez ensuite sur **Enregistrer** pour appliquer les modifications.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Dans cet exemple, l’UtilisateurA est doté d’autorisations administratives pour l’AbonnementA et l’UtilisateurB est doté d’autorisations administratives pour l’AbonnementB. Les deux abonnements sont associés au même locataire Azure Active Directory. Vous ne pouvez pas créer d’homologation entre des abonnements associés à différents locataires Azure Active Directory.
3. Dans le portail, cliquez sur **Parcourir** et choisissez **Réseaux virtuels**. Cliquez sur le réseau virtuel pour lequel vous souhaitez configurer l’homologation.
4. Dans le panneau du réseau virtuel sélectionné, cliquez sur **Contrôle d’accès**, puis cliquez sur **Ajouter**, comme illustré dans l’image suivante :

    ![Parcourir Scénario 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. Dans le panneau **Ajouter un accès**, cliquez sur un rôle et choisissez **Collaborateur de réseau**. Cliquez sur **Ajouter des utilisateurs**, tapez le nom de connexion de l’UtilisateurB, puis cliquez sur OK.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

5. Connectez-vous au portail Azure en tant qu’UtilisateurB, qui est l’utilisateur privilégié de l’AbonnementB. Suivez les étapes précédentes pour ajouter l’UtilisateurA au rôle Collaborateur de réseau, comme illustré dans l’image suivante :

    ![RBAC&2;](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [!NOTE]
    > Vous pouvez vous déconnecter et vous connecter avec les comptes des deux utilisateurs dans le navigateur pour vous assurer que l’autorisation a bien été activée.
    >
    >

    > [!IMPORTANT]
    > Si l’homologation que vous créez est entre deux réseaux virtuels créés à l’aide du modèle de déploiement Azure Resource Manager, exécutez les étapes restantes de cette section. Si les deux réseaux virtuels ont été créés via des modèles de déploiement différents, ignorez les étapes restantes de cette section et suivez les étapes de la section [Homologation de réseaux virtuels créés via des modèles de déploiement différents](#x-model) de cet article.

6. Connectez-vous au portail en tant qu’UtilisateurA, accédez au panneau du réseau virtuel VNET3, cliquez sur **Homologation**, cochez la case **Je connais mon ID de ressource** et entrez l’ID de ressource de VNET5 au format indiqué dans l’exemple ci-dessous :
   
    /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/virtualNetworks/{VNETname}
   
    ![ID de ressource](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. Connectez-vous au portail en tant qu’UtilisateurB et répétez les étapes ci-dessus pour créer un lien d’homologation de VNET5 à VNET3.
   
    ![ID de ressource 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. L’homologation est établie. Toute machine virtuelle connectée à VNet3 doit être en mesure de communiquer avec n’importe quelle machine virtuelle connectée à VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans un premier temps, créez le lien d’homologation de réseaux virtuels de HubVnet à VNET1. Notez que l’option Autoriser le trafic transféré n’est pas sélectionnée pour ce lien.
   
    ![Homologation de base](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. Dans un second temps, créez le lien homologation de VNET1 à HubVnet. Notez que l’option Autoriser le trafic transféré est sélectionnée.
   
    ![Homologation de base](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. Une fois l’homologation établie, vous pouvez vous reporter à [cet article](virtual-network-create-udr-arm-ps.md) et créer un itinéraire défini par l’utilisateur (UDR) pour rediriger le trafic du réseau virtuel VNET1 via une appliance virtuelle afin d’utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse du tronçon suivant dans l’itinéraire, vous pouvez la définir sur l’adresse IP de l’appliance virtuelle du réseau virtuel homologue HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Si vous créez une homologation entre des réseaux virtuels déployés via des modèles de déploiement différents dans le *même* abonnement, passez directement à l’étape 3. La possibilité de créer une homologation de réseaux virtuels créés via des modèles de déploiement différents qui se trouvent dans des abonnements *différents* est disponible dans la **version préliminaire**. Les fonctionnalités de la version préliminaire n’ont pas le même niveau de fiabilité et de contrat de niveau de service que les fonctionnalités disponibles en version générale. Si vous créez une homologation entre des réseaux virtuels déployés via des modèles de déploiement différents dans des abonnements différents, vous devez tout d’abord effectuer les tâches suivantes :
    - Enregistrez la fonction de version préliminaire dans votre abonnement Azure en entrant la commande suivante à partir de PowerShell : `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` et `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network` Cette étape ne peut pas être effectuée dans le portail.
    - Exécutez les étapes 1 à 6 de la section [Homologation entre des abonnements](#x-sub) de cet article.
3. Pour établir une homologation de réseaux virtuels dans ce scénario, vous n’avez besoin de créer qu’un seul lien, du réseau virtuel d’Azure Resource Manager à celui du portail Classic, c’est-à-dire de **VNET1** à **VNET2**. Dans le portail, cliquez sur **Parcourir** et choisissez **Réseaux virtuels**.
4. Dans le panneau Réseaux virtuels, choisissez **VNET1**. Cliquez sur **Homologations**, puis sur **Ajouter**.
5. Dans le panneau **Ajouter l’homologation**, nommez votre lien. Ici, il est appelé **LinkToVNet2**. Sous Détails de l’homologue, sélectionnez **Classique**.
6. Choisissez l’abonnement et le réseau virtuel homologue **VNET2**. Puis cliquez sur OK.

    ![Lien de Vnet1 à VNet2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
7. Une fois ce lien d’homologation de réseaux virtuels créé, les deux réseaux virtuels sont appariés et l’écran suivant est affiché :

    ![Vérification de la connexion d’homologation](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## <a name="remove-vnet-peering"></a>Supprimer l’homologation de réseaux virtuels
1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Accédez au panneau Réseaux virtuels, cliquez sur Homologations, choisissez le lien à supprimer, puis cliquez sur **Supprimer**.

    ![Supprimer&1;](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. Une fois que vous supprimez un lien de l’homologation de réseaux virtuels, l’état du lien d’homologation passe à Disconnected (Déconnecté).

    ![Supprimer&2;](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. Dans cet état, vous ne pouvez pas recréer le lien tant que l’état du lien d’homologation n’est pas défini sur Initiated (Initialisé). Nous vous recommandons de supprimer les deux liens avant de recréer l’homologation de réseaux virtuels.


