<properties
   pageTitle="Prise en main des applets de commande Azure Batch PowerShell | Microsoft Azure"
	description="Présente les applets de commande Azure PowerShell utilisées pour gérer le service Azure Batch"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
   ms.service="batch"
	ms.devlang="NA"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="powershell"
	ms.workload="big-compute"
	ms.date="08/07/2015"
	ms.author="danlep"/>

# Prise en main des applets de commande Azure Batch PowerShell
Cet article est une présentation rapide des applets de commande Azure PowerShell que vous pouvez utiliser pour gérer vos comptes Batch et obtenir des informations sur vos travaux et tâches Batch notamment.

Pour la syntaxe détaillée des applets de commande, tapez `get-help <Cmdlet_name>` ou consultez les [informations de référence sur les applets de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

## Composants requis

* **Azure PowerShell** : consultez la rubrique [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md) pour connaître la configuration requise et obtenir des instructions sur le téléchargement et l'installation. Les applets de commande Batch ont été introduites dans la version 0.8.10 et les versions ultérieures. Les applets de commande Batch ont été mises à jour de façon à utiliser l’API à disponibilité générale dans la version 0.9.6.

## Utilisation des applets de commande Batch

Utilisez les procédures standard pour démarrer Azure PowerShell et [vous connecter à vos abonnements Azure](../powershell-install-configure.md#Connect). En outre :

* **Sélectionnez l’abonnement Azure** : si vous avez plusieurs abonnements, sélectionnez un abonnement :

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Basculez sur le mode AzureResourceManage**. Les applets de commande Batch sont fournies dans le module Azure Resource Manager. Consultez [Utilisation de Windows PowerShell avec le Resource Manager](../powershell-azure-resource-manager.md) pour plus de détails. Pour utiliser ce module, exécutez l'applet de commande [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) :

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

* **S’inscrire auprès de l’espace de noms de fournisseur Batch (opération ponctuelle)** - Avant de pouvoir gérer vos comptes Batch, vous devez vous inscrire auprès de l’espace de noms de fournisseur Batch. Cette opération ne doit être effectuée qu’une fois par abonnement.

    ```
    Register-AzureProvider -ProviderNamespace Microsoft.Batch
    ```

## Gestion des clés et des comptes Batch

Vous pouvez utiliser les applets de commande Azure PowerShell pour créer et gérer des clés et des comptes Batch.

### Création d’un compte Batch

**New-AzureBatchAccount** crée un compte Batch dans un groupe de ressources spécifié. Si vous ne disposez pas déjà d'un groupe de ressources, créez-en un en exécutant l'applet de commande [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx), en spécifiant l'une des régions Azure dans le paramètre **Location**. (Vous trouverez les régions disponibles pour les différentes ressources Azure en exécutant [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx).) Par exemple :

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Créez ensuite un compte Batch dans le groupe de ressources, spécifiez également un nom de compte pour <*account\_name*> et la région où le service Batch est disponible. La création du compte peut prendre plusieurs minutes. Par exemple :

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Le nom du compte Batch doit être unique dans Azure, contenir entre 3 et 24 caractères, et utiliser des minuscules et des chiffres uniquement.

### Obtenir les clés d'accès au compte
**Get-AzureBatchAccountKeys** affiche les clés d'accès associées à un compte Azure Batch. Par exemple, exécutez la commande suivante pour obtenir les clés primaires et secondaires du compte que vous avez créé.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Générer une nouvelle clé d'accès
**New-AzureBatchAccountKey** génère une nouvelle clé de compte primaire ou secondaire pour un compte Azure Batch. Par exemple, pour générer une nouvelle clé primaire pour votre compte Batch, tapez :

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Pour générer une nouvelle clé secondaire, spécifiez « Secondary » pour le paramètre **KeyType**. Vous devez régénérer les clés primaires et secondaires séparément.

### Suppression d’un compte Batch
**Remove-AzureBatchAccount** supprime un compte Batch. Par exemple :

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Quand vous y êtes invité, confirmez que vous voulez supprimer le compte. La suppression du compte peut prendre un certain temps.

## Requête pour les travaux, tâches et autres détails

Utilisez les applets de commande telles que **Get-AzureBatchJob**, **Get-AzureBatchTask** et **Get-AzureBatchPool** pour interroger les entités créées sous un compte Batch.

Pour utiliser ces applets de commande, vous devez d'abord créer un objet AzureBatchContext pour stocker le nom et les clés de votre compte :

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Vous transmettez ce contexte aux applets de commande qui interagissent avec le service Batch à l'aide du paramètre **BatchContext**.

> [AZURE.NOTE]Par défaut, la clé primaire du compte est utilisée pour l'authentification, mais vous pouvez sélectionner explicitement la clé à utiliser en modifiant la propriété **KeyInUse** de votre objet BatchAccountContext : `$context.KeyInUse = "Secondary"`.


### Interrogation des données

Par exemple, utilisez **Get-AzureBatchPools** pour rechercher vos pools. Par défaut, cette demande interroge tous les pools sous votre compte, en supposant que vous avez déjà stocké l’objet BatchAccountContext dans *$context* :

```
Get-AzureBatchPool -BatchContext $context
```
### Utilisation d’un filtre OData

Vous pouvez fournir un filtre OData à l'aide du paramètre **Filter** pour rechercher uniquement les objets qui vous intéressent. Par exemple, vous pouvez rechercher tous les pools dont l’identificateur commence par « myPool » :

```
$filter = "startswith(id,'myPool')"
Get-AzureBatchPool -Filter $filter -BatchContext $context
```

Cette méthode n'est pas aussi flexible que l'utilisation de « Where-Object » dans un pipeline local. Toutefois, la requête est envoyée au service Batch directement pour que tout le filtrage se produise côté serveur et économise ainsi la bande passante Internet.

### Utilisation du paramètre Id

Une alternative au filtre OData consiste à utiliser le paramètre **Id**. Pour rechercher un pool spécifique présentant l’identificateur « myPool » :

```
Get-AzureBatchPool -Id "myPool" -BatchContext $context

```
Le paramètre **Id** prend uniquement en charge la recherche de l’identificateur complet, et non les caractères génériques ni les filtres de style OData.

### Utilisation du pipeline

Les applets de commande Batch peuvent exploiter le pipeline PowerShell pour envoyer des données entre les applets de commande. Cela a le même effet que la spécification d'un paramètre, mais permet de répertorier plus facilement plusieurs entités. Par exemple, vous pouvez rechercher toutes les tâches sous votre compte :

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### Utilisation du paramètre MaxCount

Par défaut, chaque applet de commande retourne un maximum de 1 000 objets. Si vous atteignez cette limite, vous pouvez affiner votre filtre pour limiter le nombre d'objets retournés, ou définir explicitement une utilisation maximale à l'aide du paramètre **MaxCount**. Par exemple :

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

Pour supprimer la limite supérieure, définissez **MaxCount** sur 0 ou une valeur inférieure.

## Rubriques connexes
* [Vue d'ensemble technique de Batch](batch-technical-overview.md)
* [Télécharger Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)
* [Informations de référence sur les applets de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Requêtes de liste efficaces](batch-efficient-list-queries.md)

<!---HONumber=September15_HO1-->