---
title: "Exemples pour le démarrage rapide de l’interface de ligne de commande Azure Monitor. | Microsoft Docs"
description: "Exemples de commandes de l’interface de ligne de commande pour les fonctionnalités d’Azure Monitor. Azure Monitor est un service Microsoft Azure qui vous permet d’envoyer des notifications d’alerte, ou d’appeler des URL web en fonction des valeurs des données de télémétrie configurées ainsi que de mettre à l’échelle automatiquement des services cloud, des machines virtuelles et des applications web."
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 981056249213c53bb8198fd792b815c5d9b091f5


---
# <a name="azure-monitor-cross-platform-cli-quick-start-samples"></a>Exemple de démarrage rapide de l’interface de ligne de commande (CLI) multiplateforme Azure Monitor
Cet article vous montre des exemples de commandes d’interface de ligne de commande (CLI) qui vous permettent d’accéder aux fonctionnalités de surveillance d’Azure Monitor. Azure Monitor permet une mise à l'échelle automatique des services cloud, des machines virtuelles et des applications web, et d’envoyer des notifications d'alerte ou d’appeler des URL web basées sur des valeurs de données de télémétrie configurées.

> [!NOTE]
> Azure Monitor est le nouveau nom de ce qui était appelé « Azure Insights » jusqu’au 25 septembre 2016. Toutefois, les espaces de noms et, par conséquent, les commandes ci-dessous contiennent toujours « insights ».
> 
> 

## <a name="prerequisites"></a>Conditions préalables
Si vous n’avez pas déjà installé l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure](../xplat-cli-install.md). Si vous ne connaissez pas bien l’interface de ligne de commande Azure, découvrez-la plus en détails dans l’article [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

Dans Windows, installez npm à partir du [site web Node.js](https://nodejs.org/). Une fois l’installation terminée, à l’aide de CMD.exe avec les privilèges d’exécution en tant qu’administrateur, exécutez les commandes suivantes à partir du dossier où npm est installé :

```console
npm install azure-cli --global
```

Accédez ensuite à un dossier/emplacement quelconque et tapez à la ligne de commande :

```console
azure help
```

## <a name="log-in-to-azure"></a>Connexion à Azure
La première étape consiste à vous connecter à votre compte Azure.

```console
azure login
```

Après avoir exécuté cette commande, vous devez vous connecter via les instructions à l’écran. Une fois cette opération effectuée, vos compte, ID de locataire et ID d’abonnement par défaut s’affichent. Toutes les commandes fonctionnent dans le cadre de votre abonnement par défaut.

Pour répertorier les détails de votre abonnement actuel, utilisez la commande suivante.

```console
azure account show
```

Pour remplacer le contexte de travail par un autre abonnement, utilisez la commande suivante.

```console
azure account set "subscription ID or subscription name"
```

Pour utiliser les commandes Azure Resource Manager et Azure Monitor, vous devez être en mode Azure Resource Manager.

```console
azure config mode arm
```

Pour afficher une liste de toutes les commandes Azure Monitor prises en charge, effectuez les opérations suivantes.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Afficher le journal d’activité pour un abonnement
Pour afficher une liste d’événements du journal d’activité, effectuez les opérations suivantes.

```console
azure insights logs list [options]
```

Pour voir toutes les options disponibles, essayez les opérations suivantes.

```console
azure insights logs list -help
```

Voici un exemple permettant de répertorier les journaux par groupe de ressources

```console
azure insights logs list --resourceGroup "myrg1"
```

Exemple pour répertorier les journaux par appelant

```console
azure insights logs list --caller "myname@company.com"
```

Exemple pour répertorier les journaux par appelant sur un type de ressource, entre une date de début et une date de fin

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Utilisation des alertes
Vous pouvez utiliser les informations figurant dans la section pour utiliser les alertes.

### <a name="get-alert-rules-in-a-resource-group"></a>Obtenir des règles d’alerte dans un groupe de ressources
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Créer une règle d’alerte métrique
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Créer une règle d’alerte de journal
```console
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Créer une règle d’alerte de test web
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Supprimer une règle d’alerte
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Profils de journal
Utilisez les informations figurant dans cette section pour utiliser les profils de journal.

### <a name="get-a-log-profile"></a>Obtenir un profil de journal
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Ajouter un profil de journal sans rétention
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Supprimer un profil de journal
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Ajouter un profil de journal avec rétention
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Ajouter un profil de journal avec rétention et EventHub
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnostics
Utilisez les informations figurant dans cette section pour utiliser les paramètres de diagnostic.

### <a name="get-a-diagnostic-setting"></a>Obtenir un paramètre de diagnostic
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Désactiver un paramètre de diagnostic
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Activer un paramètre de diagnostic sans rétention
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
Utilisez les informations figurant dans cette section pour utiliser les paramètres de mise à l’échelle automatique. Vous devez modifier ces exemples.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtenir les paramètres de mise à l’échelle automatique pour un groupe de ressources
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtenir les paramètres de mise à l’échelle automatique par nom dans un groupe de ressources
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Définir les paramètres de mise à l’échelle automatique
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```



<!--HONumber=Nov16_HO3-->


