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
   ms.date="05/29/2015"
   ms.author="danlep"/>

# Prise en main des applets de commande Azure Batch PowerShell
Cet article est une présentation rapide des applets de commande Azure PowerShell que vous pouvez utiliser pour gérer vos comptes Batch et obtenir des informations sur vos éléments de travail, travaux et tâches Batch.

Pour la syntaxe détaillée des applets de commande, tapez `get-help <Cmdlet_name>` ou consultez les [informations de référence sur les applets de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).


## Composants requis

* **Fonctionnalité préliminaire de Batch** : inscrivez-vous à la [fonctionnalité préliminaire de Batch](https://account.windowsazure.com/PreviewFeatures), si vous ne l'avez pas déjà fait, pour utiliser le service.
* **Azure PowerShell** : consultez la rubrique [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md) pour connaître la configuration requise et obtenir des instructions sur le téléchargement et l'installation. Les applets de commande Batch ont été introduites dans la version 0.8.10 et les versions ultérieures.

## Utilisation des applets de commande Batch

Utilisez les procédures standard pour démarrer Azure PowerShell et [vous connecter à vos abonnements Azure](../powershell-install-configure.md#Connect). En outre :

* **Sélectionnez un abonnement Azure**. Si vous possédez plusieurs abonnements, sélectionnez l'abonnement auquel vous avez ajouté la fonctionnalité préliminaire Batch :

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Basculez sur le mode AzureResourceManage**. Les applets de commande Batch sont fournies dans le module Azure Resource Manager. Consultez [Utilisation de Windows PowerShell avec le Resource Manager](../powershell-azure-resource-manager.md) pour plus de détails. Pour utiliser ce module, exécutez l'applet de commande [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) :

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Gestion des clés et des comptes Batch

Vous pouvez utiliser les applets de commande Azure PowerShell pour créer et gérer des clés et des comptes Batch.

### Création d’un compte Batch

**New-AzureBatchAccount** crée un compte Batch dans un groupe de ressources spécifié. Si vous ne disposez pas déjà d'un groupe de ressources, créez-en un en exécutant l'applet de commande [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx), en spécifiant l'une des régions Azure dans le paramètre **Location**. (Vous trouverez les régions disponibles pour les différentes ressources Azure en exécutant [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx).) Par exemple :

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Créez ensuite un compte Batch dans le groupe de ressources, spécifiez également un nom de compte pour <*account_name*> et la région où le service Batch est disponible. La création du compte peut prendre plusieurs minutes. Par exemple :

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

## Interrogez des éléments de travail, des travaux et des tâches

Utilisez les applets de commande telles que **Get-AzureBatchWorkItem**, **Get-AzureBatchJob**, **Get-AzureBatchTask** et **Get-AzureBatchPool** pour interroger les entités créées sous un compte Batch.

Pour utiliser ces applets de commande, vous devez d'abord créer un objet AzureBatchContext pour stocker le nom et les clés de votre compte :

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Vous transmettez ce contexte aux applets de commande qui interagissent avec le service Batch à l'aide du paramètre **BatchContext**.

> [AZURE.NOTE]Par défaut, la clé primaire du compte est utilisée pour l'authentification, mais vous pouvez sélectionner explicitement la clé à utiliser en modifiant la propriété **KeyInUse** de votre objet BatchAccountContext : ```$context.KeyInUse = "Secondary"```.


### Interrogation des données

Par exemple, utilisez **Get-AzureBatchWorkItem** pour rechercher vos éléments de travail. Par défaut, cette demande interroge tous les éléments de travail sous votre compte, en supposant que vous avez déjà stocké l'objet BatchAccountContext dans *$context* :

```
Get-AzureBatchWorkItem -BatchContext $context
```

Vous effectuez la même opération avec d'autres entités, telles que les pools :

```
Get-AzureBatchPool -BatchContext $context
```
### Utilisation d’un filtre OData

Vous pouvez fournir un filtre OData à l'aide du paramètre **Filter** pour rechercher uniquement les objets qui vous intéressent. Par exemple, vous pouvez rechercher tous les éléments de travail dont le nom commence par « myWork » :

```
$filter = "startswith(name,'myWork') and state eq 'active'"
Get-AzureBatchWorkItem -Filter $filter -BatchContext $context
```

Cette méthode n'est pas aussi flexible que l'utilisation de « Where-Object » dans un pipeline local. Toutefois, la requête est envoyée au service Batch directement pour que tout le filtrage se produise côté serveur et économise ainsi la bande passante Internet.

### Utilisation du paramètre Name

Une alternative au filtre OData consiste à utiliser le paramètre **Name**. Pour interroger un élément de travail spécifique nommé « myWorkItem » :

```
Get-AzureBatchWorkItem -Name "myWorkItem" -BatchContext $context

```
Le paramètre **Name** prend en charge uniquement la recherche du nom complet et non les caractères génériques ou les filtres de style OData.

### Utilisation du pipeline

Les applets de commande Batch peuvent exploiter le pipeline PowerShell pour envoyer des données entre les applets de commande. Cela a le même effet que la spécification d'un paramètre, mais permet de répertorier plus facilement plusieurs entités. Par exemple, vous pouvez rechercher toutes les tâches sous votre compte :

```
Get-AzureBatchWorkItem -BatchContext $context | Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### Utilisation du paramètre MaxCount

Par défaut, chaque applet de commande retourne un maximum de 1 000 objets. Si vous atteignez cette limite, vous pouvez affiner votre filtre pour limiter le nombre d'objets retournés, ou définir explicitement une utilisation maximale à l'aide du paramètre **MaxCount**. Par exemple :

```
Get-AzureBatchWorkItem -MaxCount 2500 -BatchContext $context

```

Pour supprimer la limite supérieure, définissez **MaxCount** sur 0 ou une valeur inférieure.

## Rubriques connexes
* [Vue d'ensemble technique de Batch](batch-technical-overview.md)
* [Télécharger Azure PowerShell](http://go.microsoft.com/p/?linkid=9811175)
* [Informations de référence sur les applets de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Requêtes de liste efficaces](batch-efficient-list-queries.md)

<!---HONumber=58_postMigration-->