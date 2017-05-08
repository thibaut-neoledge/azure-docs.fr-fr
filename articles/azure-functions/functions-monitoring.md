---
title: Test des fonctions Azure | Microsoft Docs
description: "Découvrez comment surveiller vos fonctions Azure."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: b01ffb52f75fd23901f4bb245396f649e14c0389
ms.lasthandoff: 04/27/2017

---

# <a name="monitoring-azure-functions"></a>Surveillance d’Azure Functions

## <a name="overview"></a>Vue d’ensemble 


L’onglet **Surveiller** pour chaque fonction vous permet de vérifier chaque exécution d’une fonction.

![Onglet Surveiller d’Azure Functions](./media/functions-monitoring/monitor-tab.png) 

Cliquer sur une exécution vous permet de consulter la durée, les données d’entrée, les erreurs et les fichiers journaux associés. Cela est utile pour le débogage et l’optimisation de vos fonctions.


> [!IMPORTANT]
> Lorsque vous utilisez le [plan de consommation d’hébergement](functions-overview.md#pricing) pour Azure Functions, la vignette **Surveillance** dans le panneau de vue d’ensemble de Function App n’affiche pas toutes les données. Cela est dû au fait que la plateforme met à l’échelle et gère automatiquement les instances de calcul pour vous. Ces mesures ne sont donc pas pertinentes pour un plan de consommation. Pour surveiller l’utilisation de vos Function Apps, utilisez plutôt les instructions de cet article.
> 
> La capture d’écran suivante présente un exemple :
> 
> ![Surveillance dans le panneau principal de la ressource](./media/functions-monitoring/app-service-overview-monitoring.png)



## <a name="real-time-monitoring"></a>Surveillance en temps réel

L’analyse en temps réel est disponible en cliquant sur **flux d’événements en direct**, comme indiqué ci-dessous. 

![Option de flux d’événement en direct pour l’onglet Surveiller](./media/functions-monitoring/monitor-tab-live-event-stream.png)

Le flux d’événements en direct est représenté graphiquement dans un nouvel onglet de navigateur, comme indiqué ci-dessous. 

![Exemple de flux de données d’événement en direct](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> Il existe un problème connu qui peut provoquer l’échec du remplissage de vos données. Si c’est le cas, vous devrez peut-être fermer l’onglet du navigateur contenant le flux d’événements en direct, puis cliquer à nouveau sur **flux d’événements en direct** pour lui permettre de remplir correctement vos données de flux de données d’événement. 

Le flux d’événements en direct représentera graphiquement les statistiques suivantes de votre fonction :

* Exécutions démarrées par seconde
* Exécutions réussies par seconde
* Exécutions ayant échoué par seconde
* Temps d’exécution moyen en millisecondes.

Ces statistiques sont en temps réel, mais les graphiques réels des données d’exécution peuvent avoir une latence d’environ 10 secondes.






## <a name="monitoring-log-files-from-a-command-line"></a>Analyse des fichiers journaux à partir d’une ligne de commande


Vous pouvez diffuser des fichiers journaux vers une session de ligne de commande sur une station de travail à l’aide de l’Interface de ligne de commande Azure (CLI) ou PowerShell.

### <a name="monitoring-function-app-log-files-with-the-azure-cli"></a>Analyse des fichiers journaux Function App avec l’interface CLI Azure

Pour commencer, [installez l’interface de ligne de commande Azure](../cli-install-nodejs.md)

Connectez-vous à votre compte Azure à l’aide de la commande suivante, ou n’importe quelle autre des options couvertes dans [Connexion à Azure à partir de l’interface de ligne de commande (CLI) Azure](../xplat-cli-connect.md).

    azure login

Utilisez la commande suivante pour activer les commandes Azure CLI en mode Service Management (ASM) :.

    azure config mode asm

Si vous avez plusieurs abonnements, utilisez les commandes suivantes pour les répertorier et définir l’abonnement qui contient votre Function App comme abonnement actif.

    azure account list
    azure account set <subscriptionNameOrId>

La commande suivante diffusera les fichiers journaux de votre Function App vers la ligne de commande :

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>Analyse des fichiers journaux Function App avec PowerShell

Pour commencer, [installez et configurez Azure PowerShell](/powershell/azure/overview).

Ajoutez votre compte Azure en exécutant la commande suivante :

    PS C:\> Add-AzureAccount

Si vous avez plusieurs abonnements, vous pouvez les répertorier par nom avec la commande suivante pour voir si l’abonnement approprié est sélectionné selon la propriété `IsCurrent` :

    PS C:\> Get-AzureSubscription

Si vous avez besoin de définir l’abonnement actif sur celui qui contient votre Function App, utilisez la commande suivante :

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

Diffusez les journaux vers votre session PowerShell avec la commande suivante :

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

Pour plus d’informations, reportez-vous à [Procédure : diffusion de journaux pour les applications web](../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

* [Test d’une fonction](functions-test-a-function.md)
* [Mettre à l’échelle une fonction](functions-scale.md)


