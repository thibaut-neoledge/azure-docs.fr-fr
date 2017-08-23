---
title: "Créer une passerelle d’application avec des règles de routage d’URL -Azure CLI 2.0 | Microsoft Docs"
description: "Cette page fournit des instructions pour créer et configurer une passerelle Application Gateway Azure avec les règles de routage d’URL"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 958049830d6753ec26635f18f8f8b2fabdec0733
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-using-path-based-routing-with-azure-cli-20"></a>Créer une passerelle d’application à l’aide du routage basé sur le chemin avec Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-url-route-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

Le routage basé sur le chemin d’URL vous permet d’associer des routes basées sur le chemin d’URL de la requête HTTP. Il vérifie s’il existe une route vers un pool principal configuré pour les listes d’URL dans la passerelle Application Gateway et envoie le trafic réseau vers le pool principal défini. Une utilisation courante du routage basé sur l’URL consiste à équilibrer la charge des demandes pour différents types de contenu entre différents pools de serveurs principaux.

Le routage basé sur l’URL introduit un nouveau type de règle pour la passerelle Application Gateway. La passerelle Application Gateway comporte deux types de règles : une règle de base et PathBasedRouting. Le type de règle niveau De base de sites Web fournit le service de tourniquet (round robin) pour les pools principaux alors que PathBasedRouting, en plus de la distribution de tourniquet, prend également en compte le modèle de chemin de l’URL de requête lors du choix du pool principal.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com avec deux pools de serveurs principaux : un pool de serveurs par défaut et un pool de serveurs d’images.

Les requêtes concernant http://contoso.com/image* sont acheminées vers le pool de serveurs d’images (imagesBackendPool). Si le modèle de chemin d’accès ne correspond pas, un pool de serveurs par défaut (appGatewayBackendPool) est sélectionné.

![itinéraire d’URL](./media/application-gateway-create-url-route-cli/scenario.png)

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

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Ajouter une règle basée sur le chemin à une passerelle d’application existante

Créer une passerelle d’application avec une règle de chemin personnalisée

### <a name="create-a-new-back-end-pool"></a>Créer un pool principal

Configurez le paramètre de passerelle d’application **imagesBackendPool** pour le trafic à charge équilibrée dans le pool principal. Dans cet exemple, vous configurez différents paramètres pour le nouveau pool principal. Chaque pool principal peut avoir son propre paramètre de pool principal.  Les paramètres HTTP du serveur principal sont utilisés par des règles pour router le trafic vers les membres appropriés du pool principal. Ils déterminent le protocole et le port utilisés lors de l’envoi du trafic vers les membres du pool principal. Les sessions basées sur les cookies sont également déterminées par les paramètres HTTP du serveur principal.  Si elle est activée, l’affinité de session basée sur les cookies envoie le trafic vers le même serveur principal que les requêtes précédentes pour chaque paquet.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port"></a>Créer un port frontal

Configurez le port frontal pour une passerelle Application Gateway. L’objet de configuration du port frontal est utilisé par un écouteur pour définir le port écouté par Application Gateway pour connaître le trafic sur l’écouteur.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Créer un écouteur

Configurez l’écouteur. Cette étape configure l’écouteur pour l’adresse IP publique et le port utilisé pour recevoir le trafic réseau entrant. L’exemple suivant utilise la configuration de l’adresse IP frontale configurée précédemment, la configuration de port frontal et un protocole (http ou https), et configure l’écouteur. Dans cet exemple, l’écouteur écoute le trafic HTTP sur le port 82 sur l’adresse IP publique créée précédemment.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Créer le mappage de chemin d’accès d’URL

Configurez les chemins de règles d’URL pour les pools principaux. Cette étape configure le chemin relatif utilisé par la passerelle Application Gateway pour définir le mappage entre le chemin d’URL et le pool principal qui est affecté pour gérer le trafic entrant.

> [!IMPORTANT]
> Chaque chemin d’accès doit commencer par le signe / et le seul endroit où un astérisque (\*) est autorisé est à la fin. /xyz, /xyz ou /xyz/ sont des exemples valides. La chaîne transmise à l’outil de correspondance de chemin n’inclut pas de texte après le premier signe ? ou #. De plus, ces caractères ne sont pas autorisés. 

L’exemple suivant crée une règle pour le chemin d’accès « /images/* » acheminant le trafic vers le pool principal « imagesBackendPool ». Cette règle garantit que le trafic de chaque ensemble d’URL est acheminé vers le pool principal. Par exemple, http://adatum.com/images/figure1.jpg accède à « imagesBackendPool ». Si le chemin ne correspond à aucune des règles de chemins prédéfinies, la configuration de mappage des chemins de règles configure également un pool d’adresses principal par défaut. Par exemple, http://adatum.com/shoppingcart/test.html accède à pool1, car il est défini en tant que pool par défaut pour le trafic sans correspondance.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déchargement SSL (Secure Sockets Layer), consultez [Configurer une passerelle d’application pour le déchargement SSL](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

