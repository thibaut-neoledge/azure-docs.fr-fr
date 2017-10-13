---
title: "Mise à l’échelle automatique des nœuds de calcul de cluster HPC Pack | Microsoft Docs"
description: "Augmenter ou diminuer automatiquement le nombre de nœuds de calcul de cluster HPC Pack dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: 
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 0dc0d15c64d8951c3c457df73588c37418a3c8a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Augmenter ou diminuer automatiquement les ressources du cluster HPC Pack dans Azure en fonction de la charge de travail du cluster
Si vous déployez des nœuds de rafale Azure dans votre cluster HPC Pack ou si vous créez un cluster HPC Pack dans des machines virtuelles Azure, un moyen d’augmenter ou de diminuer automatiquement les ressources du cluster, comme des nœuds ou des cœurs, en fonction de la charge de travail du cluster peut s’avérer utile. En mettant à l’échelle les ressources du cluster de cette façon, vous pouvez utiliser vos ressources Azure plus efficacement et contrôler leurs coûts.

Cet article vous montre deux moyens fournis par HPC Pack pour mettre à l’échelle automatiquement des ressources de calcul :

* la propriété **AutoGrowShrink**du cluster HPC Pack ;

* le script HPC PowerShell **AzureAutoGrowShrink.ps1**.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Actuellement, vous ne pouvez qu’augmenter ou diminuer automatiquement les nœuds de calcul HPC Pack qui exécutent un système d’exploitation Windows Server.


## <a name="set-the-autogrowshrink-cluster-property"></a>Définir la propriété de cluster AutoGrowShrink
### <a name="prerequisites"></a>Configuration requise

* **Cluster HPC Pack 2012 R2 Update 2 ou version ultérieure** : le nœud principal du cluster peut être déployé localement ou dans une machine virtuelle Azure. Pour prendre en main un nœud principal local et des nœuds « d’extension » Azure, consultez [Configurer un cluster hybride avec HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) . Pour déployer rapidement un cluster HPC Pack sur des machines virtuelles Azure, consultez le [script de déploiement IaaS de HPC Pack](hpcpack-cluster-powershell-script.md) .

* **Pour un cluster avec un nœud principal dans Azure (modèle de déploiement Resource Manager)** : à partir de HPC Pack 2016, l’authentification par certificat dans une application Azure Active Directory est utilisée pour la croissance et la réduction automatiques de machines virtuelles de cluster déployées à l’aide d’Azure Resource Manager. Configurez un certificat de la façon suivante :

  1. Après le déploiement du cluster, connectez-vous par Bureau à distance à un seul nœud principal.

  2. Chargez le certificat (format PFX avec clé privée) sur chaque nœud principal et installez-le sur Cert:\LocalMachine\My et Cert:\LocalMachine\Root.

  3. Lancez Azure PowerShell en tant qu’administrateur et exécutez les commandes suivantes sur un seul nœud principal :

    ```powershell
        cd $env:CCP_HOME\bin

        Login-AzureRmAccount
    ```
        
    Si votre compte se trouve dans plus d’un client Azure Active Directory ou d’un abonnement Azure, vous pouvez exécuter la commande suivante pour sélectionner le client approprié et l’abonnement :
  
    ```powershell
        Login-AzureRMAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Exécutez la commande suivante pour afficher le client actuellement sélectionné et l’abonnement :
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Exécutez le script suivant

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    où

    **DisplayName** : nom d’affichage de l’application Azure Active. Si l’application n’existe pas, elle est créée dans Azure Active Directory.

    **HomePage** : page d’accueil de l’application. Vous pouvez configurer une URL factice, comme dans l’exemple précédent.

    **IdentifierUri** : identificateur de l’application. Vous pouvez configurer une URL factice, comme dans l’exemple précédent.

    **CertificateThumbprint** : empreinte numérique du certificat que vous avez installé sur le nœud principal à l’étape 1.

    **TenantId** : ID client de votre Azure Active Directory. Vous pouvez obtenir l’ID client à partir de la page **Propriétés** du portail Azure Active Directory.

    Pour plus d’informations sur **ConfigARMAutoGrowShrinkCert.ps1**, exécutez `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **Pour un cluster avec un nœud principal dans Azure (modèle de déploiement Classic)** : si vous utilisez le script de déploiement IaaS de HPC Pack pour créer le cluster selon le modèle de déploiement Classic, activez la propriété de cluster **AutoGrowShrink** en définissant l’option AutoGrowShrink dans le fichier de configuration du cluster. Pour plus d’informations, consultez la documentation fournie avec le [téléchargement du script](https://www.microsoft.com/download/details.aspx?id=44949).

    L’autre solution consiste à activer la propriété de cluster **AutoGrowShrink** après avoir déployé le cluster à l’aide des commandes HPC PowerShell décrites dans la section suivante. Pour préparer cette opération, suivez d’abord la procédure ci-dessous :

  1. Configurez un certificat de gestion Azure sur le nœud principal et dans l’abonnement Azure. Pour un déploiement de test, vous pouvez utiliser le certificat auto-signé Microsoft HPC Azure par défaut, que HPC Pack installe sur le nœud principal, puis charger ce certificat dans votre abonnement Azure. Pour en savoir plus sur les options et les étapes à suivre, consultez le document sur la [Bibliothèque TechNet](https://technet.microsoft.com/library/gg481759.aspx).

  2. Exécutez **regedit** sur le nœud principal, accédez à HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo et ajoutez une valeur de type chaîne. Dans Nom de la valeur, indiquez « ThumbPrint », et dans Données de la valeur, indiquez le thumbprint du certificat à l’étape 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>Commandes HPC PowerShell HPC permettant de définir la propriété AutoGrowShrink
Voici des exemples de commandes HPC PowerShell permettant de définir **AutoGrowShrink** et d’affiner son comportement avec des paramètres supplémentaires. Pour une liste complète de paramètres, consultez [Paramètres d’AutoGrowShrink](#AutoGrowShrink-parameters) plus avant dans cet article.

Pour exécuter ces commandes, démarrez HPC PowerShell sur le nœud principal du cluster en tant qu’administrateur.

**Pour activer la propriété AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Pour désactiver la propriété AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Pour modifier l’intervalle d’augmentation en minutes**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Pour modifier l’intervalle de diminution en minutes**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Pour afficher la configuration actuelle d’AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Pour exclure des groupes de nœuds d’AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Ce paramètre est pris en charge dans HPC Pack 2016.
>

### <a name="autogrowshrink-parameters"></a>Paramètres d’AutoGrowShrink
Voici les paramètres d’AutoGrowShrink que vous pouvez modifier à l’aide de la commande **Set-HpcClusterProperty** .

* **EnableGrowShrink** : argument permettant d’activer ou de désactiver la propriété **AutoGrowShrink**.
* **ParamSweepTasksPerCore** : nombre de tâches de balayage paramétrique pour augmenter un cœur. La valeur par défaut augmente d’un cœur par tâche.

  > [!NOTE]
  > HPC Pack QFE KB3134307 modifie **ParamSweepTasksPerCore** en **TasksPerResourceUnit**. Il est basé sur le type de ressource de travail et peut avoir pour valeur node, socket ou core.
  >
  >
* **GrowThreshold** : seuil de tâches en attente pour déclencher une augmentation automatique. La valeur par défaut est 1, ce qui signifie que s’il existe une ou plusieurs tâches en attente, le nombre de nœuds augmente automatiquement.
* **GrowInterval** : intervalle en minutes à l’issue duquel déclencher la croissance automatique. L’intervalle par défaut est de 5 minutes.
* **ShrinkInterval** : intervalle en minutes à l’issue duquel déclencher la diminution automatique. L’intervalle par défaut est de 5 minutes.|
* **ShrinkIdleTimes** : nombre de vérifications continues de diminution, pour indiquer que les nœuds sont inactifs. La valeur par défaut est de 3 fois Par exemple, si l’intervalle **ShrinkInterval** est de cinq minutes, HPC Pack vérifie si le nœud est inactif toutes les cinq minutes. Si les nœuds sont dans l’état inactif après 3 vérifications continues (15 minutes), HPC Pack diminue ce nœud.
* **ExtraNodesGrowRatio** : pourcentage supplémentaire de nœuds à augmenter pour les travaux MPI (Message Passing Interface). La valeur par défaut est 1, ce qui signifie que HPC Pack augmente les nœuds de 1 % pour les travaux MPI.
* **GrowByMin** : argument permettant d’indiquer si la stratégie d’augmentation automatique est basée sur les ressources minimales requises pour la tâche. La valeur par défaut est false, ce qui signifie que HPC Pack augmente les nœuds des travaux basés sur les ressources maximales requises pour les tâches.
* **SoaJobGrowThreshold** : nombre limite de demandes SOA entrantes avant le déclenchement du processus d’augmentation automatique. La valeur par défaut est 50 000.

  > [!NOTE]
  > Ce paramètre est pris en charge dans Microsoft HPC Pack 2012 R2 Update 3.
  >
  >
* **SoaRequestsPerCore** : nombre de demandes SOA entrantes avant l’augmentation d’un cœur. La valeur par défaut est 20 000.

  > [!NOTE]
  > Ce paramètre est pris en charge dans Microsoft HPC Pack 2012 R2 Update 3.
  >
* **ExcludeNodeGroups** : les nœuds des groupes de nœuds spécifiés ne sont pas automatiquement agrandis ou réduits.
  
  > [!NOTE]
  > Ce paramètre est pris en charge dans HPC Pack 2016.
  >

### <a name="mpi-example"></a>Exemple de MPI
Par défaut, HPC Pack augmente de 1 % les nœuds supplémentaires pour les travaux MPI (**ExtraNodesGrowRatio** réglé sur 1). La raison en est que MPI peut nécessiter plusieurs nœuds et que la tâche ne peut s’exécuter que lorsque tous les nœuds sont prêts. Lorsqu’Azure démarre les nœuds, un nœud peut parfois avoir besoin de davantage de temps que les autres pour démarrer, plaçant les autres dans un état d’inactivité pendant qu’il se prépare. En augmentant les nœuds supplémentaires, HPC Pack réduit le temps d’attente de cette ressource et réduit potentiellement les coûts. Pour augmenter le pourcentage de nœuds supplémentaires pour les travaux MPI (par exemple, à 10 %), exécutez une commande similaire à

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>Exemple SOA
Par défaut, **SoaJobGrowThreshold** est réglé sur 50 000 et **SoaRequestsPerCore** sur 200 000. Si vous envoyez un travail SOA avec 70 000 demandes, il y a une tâche en attente et les demandes entrantes sont au nombre de 70 000. Dans ce cas, HPC Pack agrandit un cœur pour la tâche en attente et, pour les demandes entrantes, agrandit (70 000 - 50 000) / 20 000 = 1 cœur, soit au total deux cœurs pour ce travail SOA.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Exécuter le script AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>Composants requis

* **Cluster HPC Pack 2012 R2 Update 1 ou version ultérieure** - Le script **AzureAutoGrowShrink.ps1** est installé dans le dossier %CCP_HOME%bin. Le nœud principal du cluster peut être déployé localement ou dans une machine virtuelle Azure. Pour prendre en main un nœud principal local et des nœuds « d’extension » Azure, consultez [Configurer un cluster hybride avec HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) . Pour déployer rapidement un cluster HPC Pack dans des machines virtuelles Azure, consultez le [script de déploiement IaaS de HPC Pack](hpcpack-cluster-powershell-script.md) ou utilisez un [modèle de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Azure PowerShell 1.4.0** : le script dépend actuellement de cette version d’Azure PowerShell.
* **Pour un cluster de nœuds d’extension Azure** : exécutez le script sur un ordinateur client où HPC Pack est installé ou sur le nœud principal. Si vous effectuez l’exécution sur un ordinateur client, définissez la variable $env:CCP_SCHEDULER de manière à ce qu’elle pointe vers le nœud principal. Les nœuds « d’extension » Azure doivent être ajoutés au cluster, mais ils peuvent être dans l’état Non déployé.
* **Pour un cluster déployé sur des machines virtuelles Azure (modèle de déploiement Resource Manager)** - Pour un cluster de machines virtuelles Azure déployées selon le modèle de déploiement Resource Manager, le script prend en charge deux méthodes d’authentification Azure : connectez-vous à votre compte Azure pour exécuter le script à chaque fois (en exécutant `Login-AzureRmAccount`), ou configurez un principal du service pour vous authentifier avec un certificat. HPC Pack fournit le script **ConfigARMAutoGrowShrinkCert.ps** pour créer un principal du service avec certificat. Le script crée une application Azure Active Directory (Azure AD) et un principal du service, et attribue le rôle de contributeur au principal du service. Pour exécuter le script, lancez Azure PowerShell en tant qu’administrateur et exécutez les commandes suivantes :

    ```powershell
    cd $env:CCP_HOME\bin

    Login-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Pour plus d’informations sur **ConfigARMAutoGrowShrinkCert.ps1**, exécutez `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.

* **Pour un cluster déployé sur des machines virtuelles Azure (modèle de déploiement Classic)** : exécutez le script sur la machine virtuelle à nœud principal, car il repose sur les scripts **Start-HpcIaaSNode.ps1** et **Stop-HpcIaaSNode.ps1** qui sont installés sur cette machine. En outre, ces scripts nécessitent un certificat de gestion Azure ou un fichier de paramètres de publication (consultez [Gérer des nœuds de calcul dans un cluster HPC Pack dans Azure](hpcpack-cluster-node-manage.md)). Vérifiez que toutes les machines virtuelles à nœud de calcul dont vous avez besoin sont déjà ajoutées au cluster. Elles peuvent être à l’état Arrêté.



### <a name="syntax"></a>Syntaxe
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>parameters
* **NodeTemplates** : nom des modèles de nœud pour définir l’étendue des nœuds à agrandir ou à réduire. Si ce paramètre n’est pas spécifié (la valeur par défaut est @(), tous les nœuds du groupe **AzureNodes** appartiennent à l’étendue lorsque le paramètre **NodeType** a la valeur AzureNodes. Il en va de même pour tous les nœuds du groupe **ComputeNodes** lorsque **NodeType** a la valeur ComputeNodes.
* **JobTemplates** : noms des modèles de travail pour définir l’étendue des nœuds à agrandir.
* **NodeType** : type de nœud à augmenter ou à réduire. Les valeurs prises en charge sont les suivantes :

  * **AzureNodes** : pour les nœuds Azure PaaS (extension) dans un cluster IaaS Azure ou local.
  * **ComputeNodes** : pour les machines virtuelles à nœud de calcul uniquement dans un cluster IaaS Azure.

* **NumOfQueuedJobsPerNodeToGrow** : nombre de travaux en file d’attente nécessaire pour agrandir un nœud.
* **NumOfQueuedJobsToGrowThreshold** : nombre de travaux en file d’attente pour démarrer le processus d’agrandissement.
* **NumOfActiveQueuedTasksPerNodeToGrow** : nombre de tâches en file d’attente actives nécessaires pour agrandir un nœud. Si **NumOfQueuedJobsPerNodeToGrow** est spécifié avec une valeur supérieure à 0, ce paramètre est ignoré.
* **NumOfActiveQueuedTasksToGrowThreshold** : nombre de tâches en file d’attente actives pour démarrer le processus d’agrandissement.
* **NumOfInitialNodesToGrow** : nombre initial de nœuds à agrandir si tous les nœuds dans l’étendue ont pour état **Non déployé** ou **Arrêté (libéré)**.
* **GrowCheckIntervalMins** : intervalle en minutes entre les vérifications d’agrandissement.
* **ShrinkCheckIntervalMins** : intervalle en minutes entre les vérifications de réduction.
* **ShrinkCheckIdleTimes** : nombre de vérifications de réduction continues (séparées par l’intervalle **ShrinkCheckIntervalMins**) pour indiquer que les nœuds sont inactifs.
* **UseLastConfigurations** : configurations précédemment enregistrées dans le fichier d’arguments.
* **ArgFile**: nom du fichier d’arguments utilisé pour enregistrer et mettre à jour les configurations pour exécuter le script.
* **LogFilePrefix** : nom du préfixe du fichier journal. Vous pouvez spécifier un chemin d’accès. Par défaut, le journal est écrit dans le répertoire de travail actif.

### <a name="example-1"></a>Exemple 1
L’exemple suivant configure les nœuds d’extension Azure déployés avec le modèle Default AzureNode Template pour qu’ils puissent s’agrandir ou se réduire automatiquement. Si tous les nœuds sont initialement dans l’état **Non déployé** , au moins 3 nœuds sont démarrés. Si le nombre de travaux en file d’attente dépasse 8, le script démarre les nœuds jusqu’à ce que leur nombre soit supérieur au rapport travaux en file d’attente/ **NumOfQueuedJobsPerNodeToGrow**. Si un nœud s’avère inactif pendant 3 périodes d’inactivité consécutives, il est arrêté.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exemple 2
L’exemple suivant configure les machines virtuelles à nœud de calcul Azure déployées avec le modèle Default ComputeNode Template pour qu’elles puissent s’agrandir ou se réduire automatiquement.
Les travaux configurés par le modèle de travail Default définissent l’étendue de la charge de travail sur le cluster. Si tous les nœuds sont initialement arrêtés, au moins 5 nœuds sont démarrés. Si le nombre de tâches en file d’attente actives dépasse 15, le script démarre les nœuds jusqu’à ce que leur nombre soit supérieur au rapport tâches en file d’attente actives/ **NumOfActiveQueuedTasksPerNodeToGrow**. Si un nœud s’avère inactif pendant 10 périodes d’inactivité consécutives, il est arrêté.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
