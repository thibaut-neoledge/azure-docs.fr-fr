---
title: "Créer une passerelle Azure Application Gateway - Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment créer une passerelle Application Gateway à l’aide d’Azure CLI 2.0 dans Resource Manager"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 14dab2197ff7c1eaff012066e321ef1b99f05bb3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/02/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Créer une passerelle Application Gateway à l’aide d’Azure CLI 2.0

> [!div class="op_single_selector"]
> * [portail Azure](application-gateway-create-gateway-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies et déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées et prise en charge de plusieurs sites.

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager.
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="prerequisite-install-the-azure-cli-20"></a>Condition préalable : installer Azure CLI 2.0

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

> [!NOTE]
> Si vous n’avez pas de compte Azure, vous devez vous en procurer un. Inscrivez-vous à un [essai gratuit ici](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Scénario

Dans ce scénario, vous allez apprendre à créer une passerelle Application Gateway à l’aide du portail Azure.

Ce scénario va :

* créer une passerelle Application Gateway moyenne avec deux instances ;
* créer un réseau virtuel nommé AdatumAppGatewayVNET avec un bloc CIDR réservé de 10.0.0.0/16 ;
* créer un sous-réseau appelé Appgatewaysubnet qui utilise 10.0.0.0/28 comme bloc CIDR ;
* configurer un certificat pour le déchargement SSL.

![Exemple de scénario][scenario]

> [!NOTE]
> La configuration supplémentaire de la passerelle Application Gateway, y compris les sondes d’intégrité personnalisées, les adresses de pool principal et les règles supplémentaires sont configurées après avoir configuré la passerelle Application Gateway et non lors du déploiement initial.

## <a name="before-you-begin"></a>Avant de commencer

La passerelle Application Gateway Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois que vous avez déployé une passerelle Application Gateway sur un sous-réseau, seules les passerelles Application Gateway supplémentaires peuvent être ajoutées au sous-réseau.

## <a name="log-in-to-azure"></a>Connexion à Azure

Ouvrez **l’invite de commandes Microsoft Azure**et connectez-vous. 

```azurecli
az login -u "username"
```

> [!NOTE]
> Vous pouvez également utiliser `az login` sans le commutateur destiné à la connexion de l’appareil et qui nécessite la saisie d’un code sur aka.ms/devicelogin.

Une fois que vous avez tapé l’exemple précédent, un code est fourni. Accédez à https://aka.ms/devicelogin dans un navigateur pour continuer le processus de connexion.

![Invite de commande affichant le code de l’appareil][1]

Dans le navigateur, entrez le code que vous avez reçu. Vous êtes redirigé vers une page de connexion.

![Page de navigateur pour la saisie du code][2]

Une fois que vous êtes connecté, fermez le navigateur pour poursuivre le scénario.

![Connexion réussie][3]

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Avant de créer la passerelle Application Gateway, un groupe de ressources est créé pour contenir cette dernière. Voici la commande.

```azurecli
az resource group create --name myresourcegroup --location "West US"
```

## <a name="create-a-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau

Une fois le groupe de ressources créé, un réseau virtuel est créé pour la passerelle Application Gateway.  Dans l’exemple suivant, l’espace d’adressage est 10.0.0.0/16 pour le réseau virtuel et 10.0.0.0/28 pour le sous-réseau, comme indiqué dans les notes du scénario précédent.

```azurecli
az network vnet create \
--name AdatumAppGatewayVNET \
--address-prefix 10.0.0.0/16 \
--subnet-name Appgatewaysubnet \
--subnet-prefix 10.0.0.0/28 \
--resource-group AdatumAppGateway \
--location eastus
```

## <a name="create-the-application-gateway"></a>Créer la passerelle Application Gateway

Une fois le réseau virtuel et le sous-réseau créés, les conditions préalables pour la passerelle Application Gateway sont remplies. En outre, un certificat .pfx exporté précédemment et le mot de passe de ce dernier sont requis pour l’étape suivante. Les adresses IP utilisées pour le backend sont celles de votre serveur principal. Ces valeurs peuvent être des adresses IP privées dans le réseau virtuel, des adresses IP publiques ou des noms de domaine complets pour vos serveurs principaux.

```azurecli
az network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group AdatumAppGatewayRG \
--vnet-name AdatumAppGatewayVNET \
--vnet-address-prefix 10.0.0.0/16 \
--subnet Appgatewaysubnet \
--subnet-address-prefix 10.0.0.0/28 \
--servers 10.0.0.4 10.0.0.5 \
--cert-file /mnt/c/Users/username/Desktop/application-gateway/fabrikam.pfx \
--cert-password P@ssw0rd \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 443 \
--routing-rule-type Basic \
--http-settings-port 80

```

> [!NOTE]
> Pour obtenir la liste de paramètres pouvant être fournis lors de la création, exécutez la commande suivante : **az network application-gateway create --help**.

Cet exemple crée une passerelle Application Gateway de base avec les paramètres par défaut pour l’écouteur, le pool principal, les paramètres http principaux et les règles. Cela configure également le déchargement SSL. Vous pouvez modifier ces paramètres en fonction de votre déploiement une fois l’approvisionnement réussi.
Si vous avez déjà défini votre application web avec le pool principal aux étapes précédentes, une fois la création effective, l’équilibrage de charge commence.

## <a name="delete-all-resources"></a>Supprimer toutes les ressources

Pour supprimer toutes les ressources créées dans cet article, procédez comme suit :

```azurecli
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>Étapes suivantes

Apprenez à créer des sondes d’intégrité personnalisées en vous rendant sur [Créer une sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md)

Découvrez comment configurer le déchargement SSL et éviter à vos serveurs web le déchiffrement SSL coûteux en vous rendant sur [Configurer le déchargement SSL](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

