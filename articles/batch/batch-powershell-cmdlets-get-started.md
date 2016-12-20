---
title: "Prise en main d’Azure Batch PowerShell | Microsoft Docs"
description: "Obtenir une brève présentation des applets de commande Azure PowerShell à utiliser pour gérer le service Azure Batch"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 10/20/2016
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 1ea51f4053ba0d4e08628d29eca7cb7c82a26d61


---
# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Prise en main des applets de commande Azure Batch PowerShell
Avec les applets de commande Azure Batch PowerShell, vous pouvez effectuer pratiquement les mêmes tâches qu’avec les API Batch, le portail Azure et l’interface de ligne de commande Azure, et en écrire les scripts. Il s’agit d’une présentation rapide des applets de commande que vous pouvez utiliser pour gérer vos comptes Batch et travailler avec des ressources Batch telles que les pools, les travaux et les tâches.

Pour obtenir une liste complète des applets de commande Batch et la syntaxe détaillée des applets de commande, consultez [Référence d’applet de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

Cet article est basé sur les applets de commande d’Azure PowerShell version 3.0.0. Nous vous recommandons de mettre à jour votre Azure PowerShell fréquemment pour tirer parti des améliorations et des mises à jour de service.

## <a name="prerequisites"></a>Composants requis
Effectuez les opérations suivantes pour utiliser Azure PowerShell pour gérer vos ressources de traitement par lots.

* [Installation et configuration d'Azure PowerShell](/powershell/azureps-cmdlets-docs)
* Exécutez l’applet de commande **Login-AzureRmAccount** pour vous connecter à votre abonnement (les applets de commande Azure Batch font partie du module Azure Resource Manager) :
  
    `Login-AzureRmAccount`
* **Inscrivez-vous dans l’espace de noms de fournisseur Batch**. Cette opération ne doit être effectuée qu’**une fois par abonnement**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gestion des clés et des comptes Batch
### <a name="create-a-batch-account"></a>Création d’un compte Batch
**New-AzureBatchAccount** crée un compte Batch dans un groupe de ressources spécifié. Si vous ne disposez pas d’un groupe de ressources, créez-en un en exécutant l’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx). Spécifiez une des régions Azure dans le paramètre **Emplacement**, « États-Unis du Centre » par exemple. Par exemple :

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Créez ensuite un compte Batch dans le groupe de ressources en spécifiant le nom du compte dans <*nom_compte*> et l’emplacement et le nom de votre groupe de ressources. La procédure de création du compte Batch peut prendre un certain temps. Par exemple :

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> Le nom du compte Batch doit être unique dans la région Azure du groupe de ressources, contenir entre 3 et 24 caractères, et utiliser des minuscules et des chiffres uniquement.
> 
> 

### <a name="get-account-access-keys"></a>Obtenir les clés d'accès au compte
**Get-AzureRmBatchAccountKeys** affiche les clés d’accès associées à un compte Azure Batch. Par exemple, exécutez la commande suivante pour obtenir les clés primaires et secondaires du compte que vous avez créé.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Générer une nouvelle clé d'accès
**New-AzureRmBatchAccountKey** génère une nouvelle clé de compte primaire ou secondaire pour un compte Azure Batch. Par exemple, pour générer une nouvelle clé primaire pour votre compte Batch, tapez :

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Pour générer une nouvelle clé secondaire, spécifiez « Secondary » pour le paramètre **KeyType** . Vous devez régénérer les clés primaires et secondaires séparément.
> 
> 

### <a name="delete-a-batch-account"></a>Suppression d’un compte Batch
**Remove-AzureRmBatchAccount** supprime un compte Batch. Par exemple :

    Remove-AzureRmBatchAccount -AccountName <account_name>

Quand vous y êtes invité, confirmez que vous voulez supprimer le compte. La suppression du compte peut prendre un certain temps.

## <a name="create-a-batchaccountcontext-object"></a>Créer un objet BatchAccountContext
Pour une authentification à l’aide des applets de commande Batch PowerShell lors de la création et la gestion des pools, des travaux, des tâches et d’autres ressources, vous devez d’abord créer un objet BatchAccountContext pour stocker vos nom et clés de compte :

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Vous passez l’objet BatchAccountContext dans les applets de commande utilisant le paramètre **BatchContext** .

> [!NOTE]
> Par défaut, la clé primaire du compte est utilisée pour l’authentification, mais vous pouvez sélectionner explicitement la clé à utiliser en modifiant la propriété **KeyInUse** de votre objet BatchAccountContext : `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Créer et modifier les ressources Batch
Utilisez les applets de commande telles que **New-AzureBatchPool**, **New-AzureBatchJob** et **New-AzureBatchTask** pour créer des ressources sous un compte Batch. Il existe des applets de commande **Get-** et **Set-** correspondantes pour mettre à jour les propriétés des ressources existantes, et des applets de commande **Remove-** pour supprimer des ressources sous un compte Batch.

Si vous utilisez plusieurs de ces applets de commande, en plus de transmettre un objet BatchContext, vous devez créer ou transmettre les objets qui contiennent des paramètres de ressources détaillés, comme illustré dans l’exemple suivant. Pour obtenir d’autres exemples, consultez l’aide détaillée de chaque applet de commande.

### <a name="create-a-batch-pool"></a>Créer un pool Batch
Lors de la création ou de la mise à jour d’un pool Batch, sélectionnez une configuration de service cloud ou une configuration de machine virtuelle correspondant au système d’exploitation dans les nœuds de calcul (voir [Aperçu des fonctionnalités d’Azure Batch](batch-api-basics.md#pool)). Votre choix détermine si vos nœuds de calcul sont mis en image avec l’une des [versions du système d’exploitation invité d’Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou avec l’une des images de machines virtuelles Linux ou Windows prises en charge dans Azure Marketplace.

Si vous exécutez **New-AzureBatchPool**, transmettez les paramètres du système d’exploitation dans un objet PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Par exemple, l’applet de commande suivante crée un pool Batch à l’aide des nœuds de calcul de petite taille dans la configuration de service cloud, avec l’image de la dernière version du système d’exploitation de la famille 3 (Windows Server 2012). Ici, le paramètre **CloudServiceConfiguration** spécifie la variable *$configuration* comme objet PSCloudServiceConfiguration. Le paramètre **BatchContext** spécifie une variable *$context* définie au préalable en tant qu’objet BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Le nombre cible de nœuds de calcul dans le nouveau pool est déterminé par une formule de mise à l’échelle automatique. Dans ce cas, la formule est simplement **$TargetDedicated = 4**, ce qui indique que le nombre de nœuds de calcul dans le pool est de 4 au maximum.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Requête relative aux pools, aux travaux, aux tâches et autres détails
Utilisez les applets de commande telles que **Get-AzureBatchPool**, **Get-AzureBatchJob** et **Get-AzureBatchTask** pour interroger les entités créées sous un compte Batch.

### <a name="query-for-data"></a>Interrogation des données
Par exemple, utilisez **Get-AzureBatchPools** pour rechercher vos pools. Par défaut, cette demande interroge tous les pools sous votre compte, en supposant que vous avez déjà stocké l’objet BatchAccountContext dans *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Utilisation d’un filtre OData
Vous pouvez fournir un filtre OData à l'aide du paramètre **Filter** pour rechercher uniquement les objets qui vous intéressent. Par exemple, vous pouvez rechercher tous les pools dont l’identificateur commence par « myPool » :

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Cette méthode n'est pas aussi flexible que l'utilisation de « Where-Object » dans un pipeline local. Toutefois, la requête est envoyée au service Batch directement pour que tout le filtrage se produise côté serveur et économise ainsi la bande passante Internet.

### <a name="use-the-id-parameter"></a>Utilisation du paramètre Id
Une alternative au filtre OData consiste à utiliser le paramètre **Id** . Pour rechercher un pool spécifique présentant l’identificateur « myPool » :

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Le paramètre **Id** prend uniquement en charge la recherche de l’identificateur complet, et non les caractères génériques ni les filtres de style OData.

### <a name="use-the-maxcount-parameter"></a>Utilisation du paramètre MaxCount
Par défaut, chaque applet de commande retourne un maximum de 1 000 objets. Si vous atteignez cette limite, affinez votre filtration pour limiter le nombre d’objets retournés, ou définissez explicitement une utilisation maximale à l’aide du paramètre **MaxCount** . Par exemple :

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Pour supprimer la limite supérieure, définissez **MaxCount** sur 0 ou une valeur inférieure.

### <a name="use-the-powershell-pipeline"></a>Utilisation du pipeline PowerShell
Les applets de commande Batch peuvent exploiter le pipeline PowerShell pour envoyer des données entre les applets de commande. Cela a le même effet que la spécification d’un paramètre, mais permet d’utiliser plus facilement plusieurs entités.

Par exemple, recherchez et affichez toutes les tâches sous votre compte :

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Redémarrez chaque nœud de calcul dans un pool :

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gestion des packages d’application
Les packages d’application permettent de déployer facilement des applications vers les nœuds de calcul dans vos pools. Grâce aux applets de commande PowerShell pour Batch, vous pouvez télécharger et gérer des packages d’application dans votre compte Batch, et déployer des versions de package sur des nœuds de calcul.

**Créez** une application :

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Ajoutez** un package d’application :

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Définissez la **version par défaut** pour l’application :

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Répertorier** les packages d’une application

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Supprimer** un package d’application

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Supprimer** une application

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Vous devez supprimer toutes les versions de packages d’application d’une application avant de supprimer l’application. Vous recevrez une erreur « Conflit » si vous essayez de supprimer une application qui possède des packages d’applications.
> 
> 

### <a name="deploy-an-application-package"></a>Déployer un package d’application
Vous pouvez spécifier un ou plusieurs packages d’application pour le déploiement lorsque vous créez un pool. Lorsque vous spécifiez un package lors de la création du pool, il est déployé sur chaque nœud lorsque le nœud rejoint le pool. Les packages sont également déployés lorsqu’un nœud est redémarré ou réinitialisé.

Spécifiez l’option `-ApplicationPackageReference` lors de la création d’un pool pour déployer un package d’application sur les nœuds du pool dès qu’ils rejoignent le pool. Commencez par créer un objet **PSApplicationPackageReference**, puis configurez-le avec l’ID d’application et la version du package que vous souhaitez déployer sur les nœuds de calcul du pool :

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Créez maintenant le pool et spécifiez l’objet de référence du package comme argument dans l’option `ApplicationPackageReferences` :

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Pour plus d’informations sur les packages d’application, consultez [Déploiement d’applications avec des packages d’application Azure Batch](batch-application-packages.md).

> [!IMPORTANT]
> Pour utiliser les packages d’application, vous devez commencer par [lier un compte de stockage Azure](#linked-storage-account-autostorage) à votre compte Batch.
> 
> 

### <a name="update-a-pools-application-packages"></a>Mise à jour des packages d’applications d’un pool
Pour mettre à jour les applications associées à un pool existant, commencez par créer un objet PSApplicationPackageReference avec les propriétés souhaitées (ID d’application et version de package) :

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Obtenez ensuite le pool dans Batch, effacez tous les packages existants, ajoutez notre nouvelle référence de package, puis mettez à jour le service Batch avec les nouveaux paramètres de pool :

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Vous avez mis à jour les propriétés du pool dans le service Batch. Pour déployer réellement le nouveau package d’application sur des nœuds de calcul dans le pool, vous devez redémarrer ou réinitialiser ces nœuds. Vous pouvez redémarrer tous les nœuds dans un pool avec la commande suivante :

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> Vous pouvez déployer plusieurs packages d’application sur les nœuds de calcul dans un pool. Si vous souhaitez *ajouter* un package d’application au lieu de remplacer les packages actuellement déployés, omettez la ligne `$pool.ApplicationPackageReferences.Clear()` ci-dessus.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Pour connaître la syntaxe détaillée des applets de commande et obtenir des exemples, consultez les [informations de référence sur les applets de commande Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).
* Pour plus d’informations sur les applications et les packages d’application dans Batch, consultez [Déploiement d’applications avec des packages d’application Azure Batch](batch-application-packages.md).




<!--HONumber=Dec16_HO2-->


