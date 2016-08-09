<properties
   pageTitle="Prise en main d’Azure Batch PowerShell | Microsoft Azure"
   description="Obtenir une brève présentation des applets de commande Azure PowerShell à utiliser pour gérer le service Azure Batch"
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
   ms.date="07/28/2016"
   ms.author="danlep"/>

# Prise en main des applets de commande Azure Batch PowerShell
Avec les applets de commande Azure Batch PowerShell, vous pouvez effectuer pratiquement les mêmes tâches qu’avec les API Batch, le portail Azure et l’interface de ligne de commande Azure, et en écrire les scripts. Il s’agit d’une présentation rapide des applets de commande que vous pouvez utiliser pour gérer vos comptes Batch et travailler avec des ressources Batch telles que les pools, les travaux et les tâches. Cet article est basé sur les applets de commande d’Azure PowerShell version 1.6.0.

Pour obtenir une liste complète des applets de commande Batch et la syntaxe détaillée des applets de commande, consultez [Référence d’applet de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).


## Configuration requise

* **Azure PowerShell** : consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour obtenir des instructions sur le téléchargement et l’installation d’Azure PowerShell.
   
    * Étant donné que les applets de commande Azure Batch font partie du module Azure Resource Manager, vous devez exécuter l’applet de commande **Login-AzureRmAccount** pour vous connecter à votre abonnement.
    
    * Nous vous recommandons de mettre à jour votre Azure PowerShell fréquemment pour tirer parti des améliorations et des mises à jour de service.
    
* **S’inscrire auprès de l’espace de noms de fournisseur Batch (opération ponctuelle)** - Avant de travailler avec vos comptes Batch, vous devez vous inscrire auprès de l’espace de noms de fournisseur Batch. Cette opération ne doit être effectuée qu’une fois par abonnement. Exécutez l’applet de commande suivante :

        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch


## Gestion des clés et des comptes Batch

### Création d’un compte Batch

**New-AzureBatchAccount** crée un nouveau compte Batch dans un groupe de ressources spécifié. Si vous ne disposez pas déjà d’un groupe de ressources, créez-en un en exécutant l’applet de commande [New-AzureResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx), en spécifiant l’une des régions Azure dans le paramètre **Location**, par exemple, « Centre des US ». Par exemple :


    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


Créez ensuite un compte Batch dans le groupe de ressources en spécifiant le nom du compte dans <*nom\_compte*> et l’emplacement et le nom de votre groupe de ressources. La procédure de création du compte Batch peut prendre un certain temps. Par exemple :


    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [AZURE.NOTE] Le nom du compte Batch doit être unique dans la région Azure du groupe de ressources, contenir entre 3 et 24 caractères, et utiliser des minuscules et des chiffres uniquement.

### Obtenir les clés d'accès au compte
**Get-AzureRmBatchAccountKeys** affiche les clés d’accès associées à un compte Azure Batch. Par exemple, exécutez la commande suivante pour obtenir les clés primaires et secondaires du compte que vous avez créé.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### Générer une nouvelle clé d'accès
**New-AzureRmBatchAccountKey** génère une nouvelle clé de compte primaire ou secondaire pour un compte Azure Batch. Par exemple, pour générer une nouvelle clé primaire pour votre compte Batch, tapez :


    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [AZURE.NOTE] Pour générer une nouvelle clé secondaire, spécifiez « Secondary » pour le paramètre **KeyType**. Vous devez régénérer les clés primaires et secondaires séparément.

### Suppression d’un compte Batch
**Remove-AzureRmBatchAccount** supprime un compte Batch. Par exemple :


    Remove-AzureRmBatchAccount -AccountName <account_name>

Quand vous y êtes invité, confirmez que vous voulez supprimer le compte. La suppression du compte peut prendre un certain temps.

## Créer un objet BatchAccountContext

Pour une authentification à l’aide des applets de commande Batch PowerShell lors de la création et la gestion des pools, des travaux, des tâches et d’autres ressources, vous devez d’abord créer un objet BatchAccountContext pour stocker vos nom et clés de compte :

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Vous passez l’objet BatchAccountContext dans les applets de commande utilisant le paramètre **BatchContext**.

> [AZURE.NOTE] Par défaut, la clé primaire du compte est utilisée pour l'authentification, mais vous pouvez sélectionner explicitement la clé à utiliser en modifiant la propriété **KeyInUse** de votre objet BatchAccountContext : `$context.KeyInUse = "Secondary"`.



## Créer et modifier les ressources Batch
Utilisez les applets de commande telles que **New-AzureBatchPool**, **New-AzureBatchJob** et **New-AzureBatchTask** pour créer des ressources sous un compte Batch. Il existe des applets de commande **Get-** et **Set-** correspondantes pour mettre à jour les propriétés des ressources existantes, et des applets de commande **Remove-** pour supprimer des ressources sous un compte Batch.

Si vous utilisez plusieurs de ces applets de commande, en plus de transmettre un objet BatchContext, vous devez créer ou transmettre les objets qui contiennent des paramètres de ressources détaillés, comme illustré dans l’exemple suivant. Pour obtenir d’autres exemples, consultez l’aide détaillée de chaque applet de commande.

### Créer un pool Batch

Lors de la création ou de la mise à jour d’un pool Batch, sélectionnez une configuration de service cloud ou une configuration de machine virtuelle correspondant au système d’exploitation dans les nœuds de calcul (voir [Aperçu des fonctionnalités d’Azure Batch](batch-api-basics.md#pool)). Votre choix détermine si vos nœuds de calcul sont mis en image avec l’une des [versions du système d’exploitation invité d’Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou avec l’une des images de machines virtuelles Linux ou Windows prises en charge dans Azure Marketplace.

Si vous exécutez **New-AzureBatchPool**, transmettez les paramètres du système d’exploitation dans un objet PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Par exemple, l’applet de commande suivante crée un pool Batch à l’aide des nœuds de calcul de petite taille dans la configuration de service cloud, avec l’image de la dernière version du système d’exploitation de la famille 3 (Windows Server 2012). Ici, le paramètre **CloudServiceConfiguration** spécifie la variable *$configuration* comme objet PSCloudServiceConfiguration. Le paramètre **BatchContext** spécifie une variable *$context* définie au préalable en tant qu’objet BatchAccountContext.


    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(3,"*")
    
    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Le nombre cible de nœuds de calcul dans le nouveau pool est déterminé par une formule de mise à l’échelle automatique. Dans ce cas, la formule est simplement **$TargetDedicated = 4**, ce qui indique que le nombre de nœuds de calcul dans le pool est de 4 au maximum.

## Requête relative aux pools, aux travaux, aux tâches et autres détails

Utilisez les applets de commande telles que **Get-AzureBatchPool**, **Get-AzureBatchJob** et **Get-AzureBatchTask** pour interroger les entités créées sous un compte Batch.


### Interrogation des données

Par exemple, utilisez **Get-AzureBatchPools** pour rechercher vos pools. Par défaut, cette demande interroge tous les pools sous votre compte, en supposant que vous avez déjà stocké l’objet BatchAccountContext dans *$context* :


    Get-AzureBatchPool -BatchContext $context

### Utilisation d’un filtre OData

Vous pouvez fournir un filtre OData à l'aide du paramètre **Filter** pour rechercher uniquement les objets qui vous intéressent. Par exemple, vous pouvez rechercher tous les pools dont l’identificateur commence par « myPool » :


    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


Cette méthode n'est pas aussi flexible que l'utilisation de « Where-Object » dans un pipeline local. Toutefois, la requête est envoyée au service Batch directement pour que tout le filtrage se produise côté serveur et économise ainsi la bande passante Internet.

### Utilisation du paramètre Id

Une alternative au filtre OData consiste à utiliser le paramètre **Id**. Pour rechercher un pool spécifique présentant l’identificateur « myPool » :


    Get-AzureBatchPool -Id "myPool" -BatchContext $context


Le paramètre **Id** prend uniquement en charge la recherche de l’identificateur complet, et non les caractères génériques ni les filtres de style OData.



### Utilisation du paramètre MaxCount

Par défaut, chaque applet de commande retourne un maximum de 1 000 objets. Si vous atteignez cette limite, affinez votre filtration pour limiter le nombre d’objets retournés, ou définissez explicitement une utilisation maximale à l’aide du paramètre **MaxCount**. Par exemple :


    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Pour supprimer la limite supérieure, définissez **MaxCount** sur 0 ou une valeur inférieure.

### Utilisation du pipeline

Les applets de commande Batch peuvent exploiter le pipeline PowerShell pour envoyer des données entre les applets de commande. Cela a le même effet que la spécification d'un paramètre, mais permet de répertorier plus facilement plusieurs entités. Par exemple, l’exemple suivant détecte toutes les tâches sous votre compte :


    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## Étapes suivantes
* Pour connaître la syntaxe détaillée des applets de commande et obtenir des exemples, consultez les [informations de référence sur les applets de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Consultez la page [Interroger efficacement le service Batch](batch-efficient-list-queries.md) pour en savoir plus sur la réduction du nombre d’éléments et le type d’informations retournées pour les requêtes envoyées au service Batch.

<!---HONumber=AcomDC_0803_2016-->