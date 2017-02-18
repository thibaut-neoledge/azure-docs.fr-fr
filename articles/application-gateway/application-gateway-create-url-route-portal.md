---
title: "Créer une règle basée sur un chemin - Passerelle Application Gateway Azure - Portail Azure | Microsoft Docs"
description: "Découvrir comment créer une règle basée sur le chemin pour une passerelle Application Gateway à l’aide du portail"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: e92c33b81aa1b69da0336bec1260cbda96c7a72e


---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-portal"></a>Créer une règle basée sur le chemin pour une passerelle Application Gateway à l’aide du portail

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-url-route-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)

Le routage basé sur le chemin d’URL vous permet d’associer des itinéraires basés sur le chemin d’URL de la requête Http. Il vérifie s’il existe un itinéraire vers un pool principal configuré pour les listes d’URL dans la passerelle Application Gateway et envoie le trafic réseau vers le pool principal défini. Une utilisation courante du routage basé sur l’URL consiste à équilibrer la charge des demandes pour différents types de contenu entre différents pools de serveurs principaux.

Le routage basé sur l’URL introduit un nouveau type de règle pour la passerelle Application Gateway. La passerelle Application Gateway comporte deux types de règle : une règle de base et des règles basées sur le chemin. Le type de règle de base fournit le service de tourniquet (round robin) pour les pools principaux alors que les règles basées sur le chemin, en plus de la distribution de tourniquet, prennent également en compte le modèle de chemin de l’URL de demande lors du choix du pool principal.

## <a name="scenario"></a>Scénario

Le scénario suivant passe par la création d’une règle basée sur le chemin sur une passerelle Application Gateway existante.
Le scénario suppose que vous avez déjà suivi la procédure de [Création d’une passerelle Application Gateway](application-gateway-create-gateway-portal.md).

![itinéraire d’URL][scenario]

## <a name="a-namecreateruleacreate-the-path-based-rule"></a><a name="createrule"></a>Créer la règle basée sur le chemin

Une règle basée sur le chemin requiert son propre écouteur. Avant de créer la règle, vérifiez que vous disposez d’un écouteur.

### <a name="step-1"></a>Étape 1

Accédez au [portail Azure](http://portal.azure.com) et sélectionnez une passerelle d’application existante. Cliquer sur **Règles**

![Vue d’ensemble d’Application Gateway][1]

### <a name="step-2"></a>Étape 2

Cliquez sur le bouton **Basé sur le chemin** pour ajouter une nouvelle règle basée sur le chemin.

### <a name="step-3"></a>Étape 3 :

Le panneau **Add path-based rule (Ajouter une règle basée sur le chemin)** comporte deux sections. La première section est celle où vous avez défini l’écouteur, le nom de la règle et les paramètres de chemin par défaut. Les paramètres de chemin par défaut sont des itinéraires qui ne relèvent pas de l’itinéraire personnalisé basé sur le chemin. La deuxième section du panneau **Add path-based rule (Ajouter une règle basée sur le chemin)** est celle où vous définissez les règles basées sur le chemin elles-mêmes.

**Paramètres de base**

* **Nom** - nom convivial de la règle accessible via le portail.
* **Écouteur** - écouteur utilisé pour la règle.
* **Default backend pool (Pool principal par défaut)** - pool principal à utiliser pour la règle par défaut.
* **Default HTTP settings (Paramètres HTTP par défaut)** - paramètres HTTP à utiliser pour la règle par défaut.

**Path-based rules (Règles basées sur le chemin)**

* **Nom** - nom convivial de la règle basée sur le chemin.
* **Chemins d’accès** - chemin d’accès que la règle va rechercher pour transférer le trafic.
* **Pool principal** - pool principal à utiliser pour la règle.
* **Paramètre HTTP** - paramètres HTTP à utiliser pour la règle.

> [!IMPORTANT]
> Chemins d’accès : liste de modèles de chemin d’accès à utiliser pour la correspondance. Chaque modèle doit commencer par le signe / et le seul endroit où un astérisque (\*) est autorisé est à la fin. /xyz, /xyz* ou /xyz/* sont des exemples valides.  

![Ajouter un panneau Règle basée sur le chemin contenant toutes les informations][2]

L’ajout d’une règle basée sur le chemin à une passerelle Application Gateway existante est un processus simple via le portail. Une fois qu’une règle basée sur le chemin a été créée, elle peut être modifiée pour ajouter facilement des règles supplémentaires. 

![ajouter des règles supplémentaires basés sur le chemin][3]

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment configurer le déchargement SSL avec la passerelle Azure Application Gateway, consultez [Configuration du déchargement SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png



<!--HONumber=Jan17_HO4-->


