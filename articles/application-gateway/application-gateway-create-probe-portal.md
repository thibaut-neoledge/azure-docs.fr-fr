---
title: "Créer une sonde personnalisée pour une passerelle d’application à l’aide du portail | Microsoft Docs"
description: "Découvrez comment créer une sonde personnalisée pour Application Gateway à l’aide du portail"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: aaf13418331f29287399621cb911e4b9f5b33dc0
ms.openlocfilehash: 0fafaff220dfb5d224bc5b57b630f8b2912155f7


---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Créer une sonde personnalisée pour Application Gateway à l’aide du portail
> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-probe-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Scénario

Le scénario suivant passe par la création d’une sonde d’intégrité personnalisée sur une passerelle d’application existante.
Le scénario suppose que vous avez déjà suivi la procédure de [Création d’une passerelle Application Gateway](application-gateway-create-gateway-portal.md).

## <a name="a-namecreateprobeacreate-the-probe"></a><a name="createprobe"></a>Créer la sonde

Les sondes sont configurées via un processus en deux étapes sur le portail. La première étape consiste à créer la sonde. Ensuite, vous ajoutez la sonde aux paramètres http principaux de la passerelle d’application.

### <a name="step-1"></a>Étape 1

Accédez au [portail Azure](http://portal.azure.com) et sélectionnez une passerelle d’application existante.

![Vue d’ensemble d’Application Gateway][1]

### <a name="step-2"></a>Étape 2 :

Cliquez sur **Sondes**, puis cliquez sur le bouton **Ajouter** pour ajouter une sonde.

![Ajouter un panneau Sonde contenant toutes les informations][2]

### <a name="step-3"></a>Étape 3

Remplissez les informations requises pour la sonde et lorsque vous avez terminé, cliquez sur **OK**.

* **Nom** - cette valeur est le nom convivial de la sonde accessible par le biais du portail.
* **Hôte** - cette valeur est le nom d’hôte utilisé pour la sonde. S’applique uniquement lorsque plusieurs sites sont configurés sur Application Gateway, sinon utilisez '127.0.0.1'. Cette valeur est différente du nom d’hôte de la machine virtuelle.
* **Chemin d’accès** - reste de l’URL complète de la sonde personnalisée. Un chemin d’accès valide commence par « / »
* **Intervalle (secondes)** - fréquence d’exécution de la sonde pour vérifier l’intégrité. Il n’est pas recommandé de définir la valeur sur moins de 30 secondes.
* **Délai d’expiration (secondes)** - intervalle de temps précédant l’expiration de la sonde. L’intervalle de délai d’attente doit être suffisamment élevé pour qu’un appel HTTP puisse être envoyé afin de garantir que la page d’intégrité du serveur principal est disponible.
* **Seuil de défaillance sur le plan de l’intégrité** - nombre d’échecs nécessaires pour marquer l’instance comme défaillante. Un seuil de 0 signifie qu’en cas d’échec de contrôle d’intégrité, le serveur principal est immédiatement identifié comme défaillant.

> [!IMPORTANT]
> Le nom d’hôte n’est pas identique au nom du serveur. Cette valeur est le nom de l’hôte virtuel en cours d’exécution sur le serveur d’application. La sonde est envoyée à http://(nom d’hôte):(port des paramètres http)/urlPath

![paramètres de configuration de sonde][3]

## <a name="add-probe-to-the-gateway"></a>Ajouter une sonde à la passerelle

Maintenant que la sonde a été créée, il est temps de l’ajouter à la passerelle. Les paramètres de la sonde sont définis sur les paramètres http principaux de la passerelle d’application.

### <a name="step-1"></a>Étape 1

Cliquez sur les **paramètres HTTP** de la passerelle d’application pour afficher le panneau de configuration, puis cliquez sur les paramètres http principaux actuels dans la fenêtre.

![fenêtre de paramètres https][4]

### <a name="step-2"></a>Étape 2

Dans le panneau des paramètres **appGatewayBackEndHttp**, cliquez sur **Utiliser la sonde personnalisée** et choisissez la sonde créée à la section [Créer la sonde](#createprobe).
Lorsque vous avez terminé, cliquez sur **OK** pour appliquer les paramètres.

![panneau de paramètres appgatewaybackend][5]

La sonde par défaut vérifie l’accès par défaut à l’application web. Maintenant qu’une sonde personnalisée a été créée, la passerelle d’application utilise le chemin d’accès personnalisé défini pour surveiller l’intégrité pour le serveur principal sélectionné. Selon les critères définis, la passerelle d’application vérifie le fichier spécifié dans la sonde. Si l’appel à host:Port/path ne renvoie pas de réponse d’état Http 200 OK, le serveur est mis hors service après avoir atteint le seuil de fonctionnement anormal. La détection continue sur l’instance défaillante pour déterminer quand elle sera rétablie. Une fois l’instance ajoutée au pool de serveurs intègre, le trafic recommence à circuler vers elle et la détection de l’instance continue comme d’habitude en fonction des intervalles définis par l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment configurer le déchargement SSL avec la passerelle Azure Application Gateway, consultez la [configuration du déchargement SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png



<!--HONumber=Dec16_HO3-->


