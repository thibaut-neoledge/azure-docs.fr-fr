<properties
	pageTitle="Azure Insights : exemples de démarrage rapide de l’interface de ligne de commande (CLI) Azure Insights | Microsoft Azure"
	description="Les exemples de commandes de l’interface de ligne de commande (CLI) peuvent vous aider à accéder aux fonctionnalités de surveillance d’Azure Insights. Azure Insights est un service Microsoft Azure qui vous permet de mettre à l’échelle automatiquement des services cloud, des machines virtuelles et des applications web, d’envoyer des notifications d’alerte, ou d’appeler des URL web en fonction des valeurs des données de télémétrie configurées."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Exemple de démarrage rapide de l’interface de ligne de commande (CLI) multiplateforme Azure Insights

Cet article vous montre des exemples de commandes d’interface de ligne de commande (CLI) qui vous permettent d’accéder aux fonctionnalités de surveillance d’Azure Insights. Azure Insights permet de mettre à l’échelle automatiquement des services cloud, des machines virtuelles et des applications web, et d’envoyer des notifications d’alerte ou d’appeler des URL web basées sur des valeurs de données de télémétrie configurées.

## Configuration requise

Si vous n’avez pas déjà installé l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure](../xplat-cli-install.md). Si vous ne connaissez pas bien l’interface de ligne de commande Azure, découvrez-la plus en détails dans l’article [Utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md).


Dans Windows, installez npm à partir du [site web Node.js](https://nodejs.org/). Une fois l’installation terminée, à l’aide de CMD.exe avec les privilèges d’exécution en tant qu’administrateur, exécutez les commandes suivantes à partir du dossier où npm est installé :

```
npm install azure-cli --global
```

Accédez ensuite à un dossier/emplacement quelconque et tapez à la ligne de commande :

```
azure help
```

## Connexion à Azure

La première étape consiste à vous connecter à votre compte Azure.

```
azure login
```

Vous devez pour cela vous identifier. Une fois cette opération effectuée, vos compte, ID de locataire et ID d’abonnement par défaut s’affichent. Toutes les commandes fonctionnent dans le cadre de votre abonnement par défaut.

Pour répertorier les détails de votre abonnement actuel, utilisez la commande suivante.

```
azure account show
```

Pour remplacer le contexte de travail par un autre abonnement, utilisez la commande suivante.

```
azure account set "subscription ID or subscription name"
```

Pour utiliser les commandes Azure Resource Manager et Azure Insights, vous devez être en mode ARM.

```
azure config mode arm
```

Pour afficher une liste de toutes les commandes Azure Insights prises en charge, effectuez les opérations suivantes.

```
azure insights
```

## Afficher les journaux d’audit d’un abonnement

Pour afficher une liste des journaux d’audit, effectuez les opérations suivantes.

```
azure insights logs list [options]
```

Pour voir toutes les options disponibles, essayez les opérations suivantes.

```
azure insights logs list -help
```

Voici un exemple permettant de répertorier les journaux par groupe de ressources

```
azure insights logs list --resourceGroup "myrg1"
```

Exemple pour répertorier les journaux par appelant

```
azure insights logs list --caller "myname@company.com"
```

Exemple pour répertorier les journaux par appelant sur un type de ressource, entre une date de début et une date de fin

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## Utilisation des alertes
Vous pouvez utiliser les informations figurant dans la section pour utiliser les alertes.

### Obtenir des règles d’alerte dans un groupe de ressources

```
node bin\azure insights alerts rule list abhingrgtest123
node bin\azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### Créer une règle d’alerte métrique

```
node bin\azure insights alerts actions email create --customEmails foo@microsoft.com
node bin\azure insights alerts actions webhook create https://someuri.com
node bin\azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### Créer une règle d’alerte de journal

```
insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### Créer une règle d’alerte de test web

```
node bin\azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### Supprimer une règle d’alerte

```
node bin\azure insights alerts rule delete abhingrgtest123 andy0323
```

## Profils de journal
Utilisez les informations figurant dans cette section pour utiliser les profils de journal.

### Obtenir un profil de journal

```
node bin\azure insights logprofile list
node bin\azure insights logprofile get -n default
```


### Ajouter un profil de journal sans rétention

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Supprimer un profil de journal

```
node bin\azure insights logprofile delete --name default
```

### Ajouter un profil de journal avec rétention

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### Ajouter un profil de journal avec rétention et EventHub

```
node bin\azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## Diagnostics
Utilisez les informations figurant dans cette section pour utiliser les paramètres de diagnostic.

### Obtenir un paramètre de diagnostic

```
node bin\azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### Désactiver un paramètre de diagnostic

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### Activer un paramètre de diagnostic sans rétention

```
node bin\azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## Autoscale
Utilisez les informations figurant dans cette section pour utiliser les paramètres de mise à l’échelle automatique. Vous devrez modifier ces exemples.

### Obtenir les paramètres de mise à l’échelle automatique pour un groupe de ressources

```
node bin\azure insights autoscale setting list montest2
```

### Obtenir les paramètres de mise à l’échelle automatique par nom dans un groupe de ressources

```
node bin\azure insights autoscale setting list montest2 -n setting2
```


### Définir les paramètres de mise à l’échelle automatique

```
node bin\azure insights autoscale setting set montest2 -n setting2 --settingSpec
```

<!---HONumber=AcomDC_0330_2016-->