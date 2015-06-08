<properties
	pageTitle="Commencer avec les applets de commande PowerShell de lot Azure"
	description="Présente les applets de commande PowerShell Azure utilisé pour gérer le service de traitement par lots Azure"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="yidingz"/>

<tags
	ms.service="batch"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="big-compute"
	ms.date="04/15/2015"
	ms.author="danlep"/>

# Commencer avec les applets de commande PowerShell de lot Azure
Cet article est une introduction rapide aux applets de commande PowerShell de Azure que vous pouvez utiliser pour gérer vos comptes de lot et obtenir des informations sur vos éléments de travail par lots, les opérations et les tâches.

Pour la syntaxe détaillée applet de commande, tapez ```get-help <Cmdlet_name>```.


## Composants requis

* **Aperçu du lot de** -Inscrivez-vous à la [Aperçu du lot de](https://account.windowsazure.com/PreviewFeatures), si vous n'avez pas déjà fait, pour travailler avec le service.
* **Azure PowerShell** -voir [comment installer et configurer Azure PowerShell](powershell-install-configure.md) pour les conditions préalables et les instructions de téléchargement et d'installation. Applets de commande de lot ont été introduites dans la version 0.8.10 et les versions ultérieures.

## Utilisez les applets de commande du lot

Utilisez les procédures standard pour démarrer PowerShell Azure et [se connecter à vos abonnements Azure](powershell-install-configure.md#Connect). En outre :

* **Abonnement Azure sélectionnez** - si vous avez plus à l'abonnement, sélectionnez l'abonnement où vous avez ajouté la fonctionnalité d'aperçu de lot :

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Passer en mode AzureResourceManage** -lot les applets de commande sont fournis dans le module Azure Resource Manager. Voir [à l'aide de Windows PowerShell avec le Gestionnaire de ressources](powershell-azure-resource-manager.md) pour plus d'informations. Pour utiliser ce module, exécutez le [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) applet de commande :

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Gérer les clés et les comptes de lot

Vous pouvez utiliser les applets de commande PowerShell Azure pour créer et gérer des clés et des comptes de lot.

### Créer un compte de traitement par lots

**New-AzureBatchAccount** crée un nouveau compte de traitement dans un groupe de ressources spécifié. Si vous ne disposez pas d'un groupe de ressources, en créer un en exécutant le [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx) cmdlet, en spécifiant l'une des régions Azure dans le **emplacement** paramètre. (Vous trouverez les régions disponibles pour les différentes ressources Azure en exécutant [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx).) Par exemple :

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Puis, créer un nouveau compte de compte de traitement par lots dans le groupe de ressources, spécifiez également un nom de compte pour < * account_name * > et l'emplacement où le service de traitement par lots est disponible. Création du compte peut prendre plusieurs minutes. Par exemple :

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Le nom de compte doit être unique dans Azure, contenir entre 3 et 24 caractères et utiliser des minuscules et chiffres uniquement.

### Obtenir les clés d'accès
**Get-AzureBatchAccountKeys** affiche les clés d'accès associés à un compte Azure lot. Par exemple, exécutez la commande suivante pour obtenir les clés primaires et secondaires du compte que vous avez créé.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Générer une nouvelle clé d'accès
**New-AzureBatchAccountKey** génère une nouvelle clé de compte principal ou secondaire pour un compte Azure lot. Par exemple, pour générer une nouvelle clé primaire pour votre compte de commandes, tapez :

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Pour générer une nouvelle clé secondaire, spécifiez « Secondary » pour le **KeyType** paramètre. Vous devez régénérer les clés primaires et secondaires séparément.

### Supprimer un compte de traitement par lots
**Remove-AzureBatchAccount** supprime un compte de traitement par lots. Par exemple :

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Lorsque vous y êtes invité, confirmez que vous souhaitez supprimer le compte. Suppression du compte peut prendre du temps.

## Rechercher des éléments de travail, des projets et des tâches

Utilisez les applets de commande telles que **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask**, et **Get-AzureBatchPool** pour rechercher les entités créées sous un compte de traitement par lots.

Pour utiliser ces applets de commande, vous devez tout d'abord créer un objet AzureBatchContext pour stocker votre nom de compte et des clés :

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Vous transmettre ce contexte dans les applets de commande qui interagissent avec le service de traitement par lots à l'aide de la **BatchContext** paramètre.

> [AZURE.NOTE]Par défaut, la clé primaire du compte est utilisé pour l'authentification, mais vous pouvez sélectionner explicitement la clé à utiliser en modifiant votre objet BatchAccountContext **KeyInUse** propriété : ```$context.KeyInUse = "Secondary"```.


### Requête de données

Par exemple, utilisez **Get-AzureBatchWorkItem** pour rechercher vos éléments de travail. Par défaut cette demande à tous les éléments de travail sous votre compte, en supposant que vous déjà stocké l'objet BatchAccountContext dans *$context*:

```
Get-AzureBatchWorkItem -BatchContext $context
```

Le même peut être effectué avec d'autres entités, telles que les pools :

```
Get-AzureBatchPool -BatchContext $context
```
### Utiliser un filtre OData

Vous pouvez fournir un filtre OData à l'aide du **filtre** paramètre pour rechercher uniquement les objets qui vous intéressent. Par exemple, vous trouverez tous les éléments de travail avec les noms commençant par « mywork, procédez comme » :

```
$filter = "startswith(name,'myWork') and state eq 'active'" 
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
``` 

Cette méthode n'est pas aussi flexible que dans un pipeline local à l'aide de « Where-Object ». Toutefois, la requête est envoyée au service de traitement par lots directement afin que tout le filtrage se produit côté serveur, l'enregistrement de la bande passante Internet.

### Utilisez le paramètre de nom

Une alternative à un filtre OData consiste à utiliser le **nom** paramètre. Pour rechercher un élément de travail spécifique nommée « myWorkItem » :

``` 
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context 

```
Le **nom** paramètre prend en charge uniquement recherche du nom complet, pas de caractères génériques ou filtres de style OData.

### Utilisez le pipleline

Applets de commande de traitement par lots peuvent exploiter le pipeline pour envoyer des données entre les applets de commande PowerShell. Cela a le même effet que la spécification d'un paramètre, mais permet de répertorier plusieurs entités plus faciles. Par exemple, vous pouvez trouver toutes les tâches sous votre compte :

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context 
```

### Utilisez le paramètre MaxCount

Par défaut, chaque cmdlet retourne un maximum de 1000 objets. Si vous atteignez cette limite, vous pouvez affiner votre filtre pour faire revenir moins d'objets, ou définir explicitement une utilisation maximale du **MaxCount** paramètre. Par exemple :

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context 

```

Pour supprimer la limite supérieure, définissez **MaxCount** ou inférieure à 0.

## Rubriques connexes
* [Vue d'ensemble technique de traitement par lots](batch-technical-overview.md)
* [Télécharger PowerShell Azure](http://go.microsoft.com/p/?linkid=9811175)
* [Référence de l'applet de commande Azure](https://msdn.microsoft.com/library/jj554330.aspx)

<!---HONumber=GIT-SubDir-->