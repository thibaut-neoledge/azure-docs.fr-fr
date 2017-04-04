---
title: "Connecter votre réseau local à un réseau virtuel Azure : VPN site à site : portail Classic | Microsoft Docs"
description: "Étapes de création d’une connexion IPsec entre votre réseau local et un réseau virtuel Azure via l’Internet public. Ces étapes vous aideront à créer une connexion de passerelle VPN de site à site à l’aide du portail."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 619ea430b13c16e8e4338413613d5798f36458ba
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Création d’une connexion de site à site à l’aide du portail Azure (Classic)
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Classic - Portail Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Classic - Portail Classic](vpn-gateway-site-to-site-create.md)
>
>


Une connexion par passerelle VPN site à site (S2S) est une connexion via un tunnel VPN IPsec/IKE (S2S ou IKEv1). Ce type de connexion requiert un périphérique VPN local auquel est affectée une IP publique, et qui ne se situe pas derrière un NAT. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux.

Cet article vous guide lors de la création d’un réseau virtuel et d’une connexion de passerelle VPN de site à site à votre réseau local, à l’aide du modèle de déploiement classique et du portail Azure. 

![Schéma de connexion intersite d’une passerelle VPN site à site](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Méthodes et modèles de déploiement pour les connexions de site à site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Le tableau suivant présente les modèles et les méthodes de déploiement disponibles pour les configurations de site à site. Quand un article avec les étapes de configuration est disponible, le lien vers cet article est ajouté à ce tableau.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurations supplémentaires
Si vous souhaitez établir une connexion entre des réseaux virtuels, mais si vous ne créez pas une connexion à un emplacement local, consultez [configurer une connexion de réseau virtuel à réseau virtuel](virtual-networks-configure-vnet-to-vnet-connection.md). Si vous souhaitez ajouter une connexion de site à site à un réseau virtuel qui possède déjà une connexion, consultez [Connecter plusieurs sites locaux à un réseau virtuel](vpn-gateway-multi-site.md).

## <a name="before-you-begin"></a>Avant de commencer
Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous ne maîtrisez pas la configuration de votre appareil VPN ou les plages d’adresses IP mentionnées dans la configuration de votre réseau local, vous devez contacter une personne qui peut vous fournir ces informations.
* Une adresse IP IPv4 publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial).
* Actuellement, PowerShell est requis pour spécifier la clé partagée et créer la connexion de passerelle VPN. Installez la dernière version des applets de commande PowerShell Azure Service Management (SM). Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Si vous utilisez PowerShell pour cette configuration, vérifiez que vous exécutez PowerShell en tant qu’administrateur. 

> [!NOTE]
> Lorsque vous configurez une connexion site à site, une adresse IPv4 publique est requise pour votre périphérique VPN.                                                                                                                                                                               
>
>

### <a name="values"></a>Exemples de valeurs de configuration pour cet exercice
Lorsque vous suivez ces étapes dans le cadre d’un exercice, vous pouvez utiliser les exemples de valeurs de configuration suivantes :

* **Nom du réseau virtuel :** TestVNet1
* **Espace d’adressage :** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (facultatif pour cet exercice)
* **Sous-réseaux :**
  * FrontEnd : 10.11.0.0/24
  * BackEnd : 10.12.0.0/24 (facultatif pour cet exercice)
* **Sous-réseau de passerelle :** 10.11.255.0/27
* **Groupe de ressources :** TestRG1
* **Emplacement :** États-Unis de l’Est
* **Serveur DNS :** 8.8.8.8 (facultatif pour cet exercice)
* **Nom du site local :** Site2

## <a name="CreatVNet"></a>1. Créez un réseau virtuel

Lorsque vous créez un réseau virtuel qui sera utilisé pour une connexion de site à site, vous devez vous assurer que les espaces d’adressage que vous spécifiez ne se chevauchent pas avec les espaces d’adressage du client pour les sites locaux auxquels vous souhaitez vous connecter. Si vos sous-réseaux se chevauchent, votre connexion ne fonctionnera pas correctement.

* Si vous disposez déjà d’un réseau virtuel, vérifiez que les paramètres sont compatibles avec la conception de votre passerelle VPN, avec une attention particulière pour tous les sous-réseaux qui pourraient chevaucher d’autres réseaux. 

* Si vous n’avez pas de réseau virtuel, créez-en un. Les captures d’écran sont fournies à titre d’exemple. Assurez-vous de remplacer ces valeurs par les vôtres.

### <a name="to-create-a-virtual-network"></a>Pour créer un réseau virtuel

1. Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau**. Dans le champ **Rechercher dans le marketplace**, tapez « réseau virtuel ». Localisez **Réseau virtuel** dans la liste renvoyée et cliquez pour ouvrir le panneau **Réseau virtuel**.

    ![Rechercher le panneau Réseau virtuel](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. En bas du panneau Réseau virtuel, à partir de la liste **Sélectionner un modèle de déploiement**, choisissez **Classique** puis cliquez sur **Créer**.

    ![Sélectionner le modèle de déploiement](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. Dans le panneau **Créer un réseau virtuel** , configurez les paramètres du réseau virtuel. Dans ce panneau, vous ajoutez votre premier espace d’adressage et une plage d’adresses de sous-réseau unique. Après avoir créé le réseau virtuel, vous pouvez revenir en arrière et ajouter des espaces d’adressage et des sous-réseaux supplémentaires.

    ![Panneau Créer un réseau virtuel](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Panneau Créer un réseau virtuel")
5. Vérifiez qu’il s’agit de l’ **abonnement** approprié. Vous pouvez modifier des abonnements à l’aide de la liste déroulante.
6. Cliquez sur **Groupe de ressources** et sélectionnez un groupe de ressources existant, ou créez un groupe de ressources en tapant un nom pour ce dernier. Pour plus d’informations sur les groupes de ressources, consultez [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Ensuite, sélectionnez les paramètres d’ **emplacement** pour votre réseau virtuel. L’emplacement détermine où se trouveront les ressources que vous déployez sur ce réseau virtuel.
8. Sélectionnez **Épingler au tableau de bord** si vous souhaitez être en mesure de trouver votre réseau virtuel facilement sur le tableau de bord, puis cliquez sur **Créer**.

    ![Épingler au tableau de bord](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Épingler au tableau de bord")
9. Après avoir cliqué sur « Créer », vous verrez une vignette apparaître sur votre tableau de bord. Celle-ci indique la progression de votre réseau virtuel. La vignette change lorsque le réseau virtuel est créé.

    ![Vignette de création du réseau virtuel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Création du réseau virtuel")

Une fois votre réseau virtuel créé, la mention **Créé** apparaît sous **État** sur la page Réseaux du portail Azure Classic.

## <a name="additionaladdress"></a>2. Ajouter un espace d’adressage supplémentaire

Après avoir créé votre réseau virtuel, vous pouvez ajouter un espace d’adressage supplémentaire. L’ajout d’un espace d’adressage supplémentaire n’est pas obligatoire dans une configuration de site à site. Toutefois, si vous avez besoin de plusieurs espaces d’adressage, procédez comme suit :

1. Recherchez les réseaux virtuels dans le portail.
2. Dans le panneau de votre réseau virtuel, dans la section **Paramètres**, cliquez sur **Espace d’adressage**.
3. Dans le panneau Espace d’adressage, cliquez sur **+Ajouter** et indiquez un espace d’adressage supplémentaire.
 
## <a name="dns"></a>3. Spécifier un serveur DNS
Les paramètres DNS ne sont pas indispensables à une configuration de site à site. Toutefois, vous devrez les configurer pour bénéficier de la résolution de noms.

Après avoir créé votre réseau virtuel, vous pouvez ajouter l’adresse IP d’un serveur DNS afin de gérer la résolution de noms. Ouvrez les paramètres de votre réseau virtuel, cliquez sur Serveurs DNS et ajoutez l’adresse IP du serveur DNS que vous souhaitez utiliser pour la résolution de noms. Ce paramètre n’entraîne pas la création de serveur DNS. Dans les paramètres de l’exemple, nous utilisons un serveur DNS public. Vous utiliserez généralement un serveur DNS privé. Veillez à ajouter un serveur DNS avec lequel vos ressources peuvent communiquer.

1. Recherchez les réseaux virtuels dans le portail.
2. Dans le panneau de votre réseau virtuel, dans la section **Paramètres**, cliquez sur **Serveurs DNS**.
3. Ajoutez un serveur DNS.
4. Cliquez sur **Enregistrer** en haut de la page pour enregistrer vos paramètres.
 
## <a name="localsite"></a>4. Configurer le site local

Le site local fait généralement référence à votre emplacement local. Il contient l’adresse IP du périphérique VPN avec lequel vous allez créer une connexion et les plages d’adresses IP qui seront acheminées via la passerelle VPN vers le périphérique VPN.

1. Dans le portail, accédez au réseau virtuel pour lequel vous souhaitez créer une passerelle.
2. Dans le panneau de votre réseau virtuel, dans le panneau **Vue d’ensemble**, puis dans la section Connexions VPN, cliquez sur **Passerelle** pour ouvrir le panneau **Nouvelle connexion VPN**.

    ![Cliquez ici pour configurer les paramètres de la passerelle](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Cliquez ici pour configurer les paramètres de la passerelle")
3. Dans le panneau **Nouvelle connexion VPN**, sélectionnez **De site à site**.

    ![Cliquez sur De site à site](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site.png "De site à site")
4. Cliquez sur **Site local- Configurer les paramètres requis** pour ouvrir le panneau **Site local**. Configurez les paramètres, puis cliquez sur **OK** pour les enregistrer.
    - **Nom :** créez un nom pour votre site local afin de l’identifier plus facilement.
    - **Adresse IP de la passerelle VPN :** il s’agit de l’adresse IP publique du périphérique VPN pour votre réseau local. Le périphérique VPN requiert une adresse IP IPv4 publique. Spécifiez une adresse IP publique valide pour le périphérique VPN auquel vous souhaitez vous connecter. Il ne peut pas se trouver derrière NAT et doit être accessible par Azure.
    - **Espace d’adressage du client :** spécifiez les plages d’adresses IP qui devront être acheminées vers le réseau local via cette passerelle. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne se chevauchent pas avec des plages d’adresses d’autres réseaux auxquels votre réseau virtuel se connecte, ou avec les propres plages d’adresses du réseau virtuel.

    ![Site local](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Configurez le site local")

## <a name="gatewaysubnet"></a>5. Configurer le sous-réseau de passerelle

Vous devez créer un sous-réseau de passerelle pour votre passerelle VPN. Le sous-réseau de passerelle contient les adresses IP qui seront utilisées par les services de passerelle VPN.

1. Dans le panneau **Nouvelle connexion VPN**, cochez la case **Créer une passerelle immédiatement**. Le volet « Configuration de passerelle facultative » s’affiche. Si vous ne cochez pas cette case, le panneau de configuration du sous-réseau de passerelle ne s’affichera pas.

    ![Configuration de la passerelle - Sous-réseau, taille, type de routage](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Configuration de la passerelle - Sous-réseau, taille, type de routage")
2. Cliquez sur **Configuration de passerelle facultative - Sous-réseau, taille et type de routage** pour ouvrir le panneau **Configuration de la passerelle**.
3. Dans le panneau **Configuration de la passerelle**, cliquez sur **Sous-réseau - Configurer les paramètres requis** pour ouvrir le panneau **Ajouter un sous-réseau**.

    ![Configuration de la passerelle - sous-réseau de passerelle](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Configuration de la passerelle - sous-réseau de passerelle")
4. Dans le panneau **Ajouter un sous-réseau**, ajoutez le sous-réseau de passerelle. Lorsque vous ajoutez le sous-réseau de passerelle, créez, si possible, un sous-réseau de passerelle à l’aide d’un bloc CIDR de /28 ou /27. Vous disposerez ainsi de suffisamment d’adresses IP pour satisfaire les exigences de configuration future supplémentaires.  Cliquez sur **OK** pour enregistrer les paramètres.

    ![Ajoutez un sous-réseau de passerelle](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Ajoutez un sous-réseau de passerelle")

## <a name="sku"></a>6. Spécifier la référence et le type de VPN
1. Sélectionnez la **taille** de la passerelle. Il s’agit de la référence de passerelle que vous utiliserez pour créer votre passerelle de réseau virtuel. Dans le portail, la référence par défaut est **De base**. Pour plus d’informations sur les références de passerelle, consultez [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![Sélectionnez la référence et le type de VPN](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Sélectionnez la référence et le type de VPN")
2. Sélectionnez le **type de routage** pour votre passerelle. Cela correspond également au type de VPN. Il est important de sélectionner le type de passerelle approprié, car vous ne pourrez plus le modifier par la suite. Votre périphérique VPN doit être compatible avec le type de routage que vous sélectionnez. Pour plus d’informations sur le type de VPN, consultez la rubrique [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Certains articles font référence aux types de VPN « RouteBased » et « PolicyBased ». « RouteBased » correspond à un routage dynamique, et « PolicyBased » à un routage statique.
3. Cliquez sur **OK** pour enregistrer les paramètres.
4. Dans le panneau **Nouvelle connexion VPN**, cliquez sur **OK** en bas du panneau pour commencer à créer votre passerelle de réseau virtuel. Cette opération peut prendre jusqu’à 45 minutes.

## <a name="vpndevice"></a>7. Configuration de votre périphérique VPN

Consultez le fabricant de votre périphérique pour obtenir des informations de configuration spécifiques et configurer votre périphérique. Consultez [Périphériques VPN](vpn-gateway-about-vpn-devices.md) pour obtenir plus d’informations sur les périphériques VPN qui fonctionnent bien avec Azure. Identifiez également les éventuels [Problèmes de compatibilité connus avec le matériel](vpn-gateway-about-vpn-devices.md#known) pour le périphérique VPN que vous souhaitez utiliser. 

Au moment de configurer votre périphérique VPN, vous aurez besoin de l’adresse IP de la passerelle VPN que vous avez créée. Vous la trouverez dans le panneau **Vue d’ensemble** de votre réseau virtuel.

## <a name="CreateConnection"></a>8. Créer la connexion
Désormais, vous allez définir la clé partagée et créer la connexion. La clé que vous définissez doit être la même que celle qui a été utilisée lors la configuration de votre périphérique VPN.

> [!NOTE]
> Pour le moment, cette étape n’est pas disponible dans le portail Azure. Vous devez utiliser la version Service Management (SM) des applets de commande Azure PowerShell.                                                                                                                                                                             
>
>

### <a name="step-1-connect-to-your-azure-account"></a>Étape 1. Se connecter au compte Azure

1. Ouvrez la console PowerShell avec des droits élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

        Login-AzureRmAccount
2. Vérifiez les abonnements associés au compte.

        Get-AzureRmSubscription
3. Si vous avez plusieurs abonnements, sélectionnez celui que vous souhaitez utiliser.

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Ajoutez la version SM des applets de commande PowerShell.

        Add-AzureAccount

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Étape 2. Définir la clé partagée et créer la connexion

Lorsque vous utilisez PowerShell et le modèle de déploiement classique, il arrive que les noms de ressources dans le portail ne correspondent pas aux noms qu’Azure s’attend à voir lors de l’utilisation de PowerShell. Les étapes suivantes vous expliquent comment exporter le fichier de configuration réseau pour obtenir les noms appropriés.

1. Créez un répertoire sur votre ordinateur, puis exportez le fichier de configuration réseau dans ce répertoire. Dans cet exemple, le fichier de configuration réseau est exporté vers C:\AzureNet.

         Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
2. Ouvrez le fichier de configuration réseau avec un éditeur xml et recherchez les valeurs « Nom LocalNetworkSite » et « Nom VirtualNetworkSite ». Modifiez l’exemple pour appliquer ces valeurs. Lorsque vous spécifiez un nom qui contient des espaces, encadrez la valeur avec des guillemets.

3. Définissez la clé partagée et créez la connexion. La valeur « -SharedKey » est une valeur que vous pouvez générer et spécifier. Dans l’exemple, nous avons utilisé « abc123 », mais vous pouvez (et devriez) générer et utiliser une valeur plus complexe. L’important, c’est que la valeur que vous spécifiez ici doit être identique à celle spécifiée lors de la configuration de votre périphérique VPN.

        Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
        -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
Lorsque la connexion est créée, le message **État : réussi** s’affiche.

## <a name="verify"></a>9. Vérifier votre connexion

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).


