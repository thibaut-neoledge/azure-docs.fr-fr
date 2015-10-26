<properties
 pageTitle="Adapter automatiquement les ressources de calcul d’un cluster HPC | Microsoft Azure"
 description="Découvrez comment augmenter ou réduire automatiquement les ressources de calcul d’un cluster HPC Pack dans Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Adapter automatiquement les ressources de calcul Azure dans un cluster HPC Pack en fonction de la charge de travail du cluster

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Si vous déployez des nœuds « d’extension » Azure dans votre cluster HPC Pack ou que vous créez un cluster HPC Pack dans des machines virtuelles Azure, un moyen d’augmenter ou de réduire automatiquement les ressources de calcul Azure en fonction de la charge de travail actuelle des travaux et des tâches sur le cluster peut s’avérer utile. Vous pouvez ainsi utiliser vos ressources Azure plus efficacement et contrôler leurs coûts. Pour ce faire, utilisez le script PowerShell HPC **AzureAutoGrowShrink.ps1** qui est installé avec HPC Pack.

>[AZURE.TIP]À compter de HPC Pack 2012 R2 Update 2, HPC Pack inclut un service intégré pour agrandir et réduire les nœuds d’extension Azure ou les nœuds de calcul dans les machines virtuelles Azure. Configurez le service avec un paramètre dans le [script de déploiement HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md) ou définissez manuellement la propriété **AutoGrowShrink** du cluster. Consultez [Nouveautés de Microsoft HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx).

## Composants requis

* **Cluster HPC Pack 2012 R2 Update 1 ou version ultérieure** - Le script **AzureAutoGrowShrink.ps1** est installé dans le dossier %CCP\_HOME%bin. Le nœud principal du cluster peut être déployé localement ou dans une machine virtuelle Azure. Pour prendre en main un nœud principal local et des nœuds « d’extension » Azure, consultez [Configurer un cluster hybride avec HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Pour déployer rapidement un cluster HPC Pack sur des machines virtuelles Azure, consultez le [script de déploiement HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md).

* **Pour un cluster de nœuds d’extension Azure** : exécutez le script sur un ordinateur client où HPC Pack est installé ou sur le nœud principal. Si vous effectuez l’exécution sur un ordinateur client, définissez la variable $env:CCP\_SCHEDULER de manière à ce qu’elle pointe vers le nœud principal. Les nœuds « d’extension » Azure doivent déjà être ajoutés au cluster, mais ils peuvent être dans l’état Non déployé.


* **Pour un cluster déployé sur des machines virtuelles Azure** : exécutez le script sur la machine virtuelle à nœud principal, car il repose sur les scripts **Start-HpcIaaSNode.ps1** et **Stop-HpcIaaSNode.ps1** qui sont installés sur cette machine. En outre, ces scripts nécessitent un certificat de gestion Azure ou un fichier de paramètres de publication (consultez [Gérer des nœuds de calcul dans un cluster HPC Pack dans Azure](virtual-machines-hpcpack-cluster-node-manage.md)). Assurez-vous que toutes les machines virtuelles à nœud de calcul dont vous avez besoin sont déjà ajoutées au cluster (elles peuvent être toutefois dans l’état Arrêté).

## Syntaxe

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## Paramètres

 * **NodeTemplates** : noms des modèles de nœud pour définir l’étendue des nœuds à agrandir ou à réduire. Si ce paramètre n’est pas spécifié (la valeur par défaut est @()), tous les nœuds du groupe **AzureNodes** appartiennent à l’étendue quand **NodeType** a la valeur AzureNodes. Il en va de même pour tous les nœuds du groupe **ComputeNodes** quand **NodeType** a la valeur ComputeNodes.

 * **JobTemplates** : noms des modèles de travail pour définir l’étendue des nœuds à agrandir.

 * **NodeType** : type de nœud à augmenter ou à réduire. Les valeurs prises en charge sont les suivantes :

     * **AzureNodes** : pour les nœuds Azure PaaS (extension) dans un cluster IaaS Azure ou local.

     * **ComputeNodes** : pour les machines virtuelles à nœud de calcul uniquement dans un cluster IaaS Azure.

* **NumOfQueuedJobsPerNodeToGrow** : nombre de travaux en file d’attente nécessaire pour agrandir un nœud.

* **NumOfQueuedJobsToGrowThreshold** : nombre de travaux en file d’attente pour démarrer le processus d’agrandissement.

* **NumOfActiveQueuedTasksPerNodeToGrow** : nombre de tâches en file d’attente actives nécessaires pour agrandir un nœud. Si **NumOfQueuedJobsPerNodeToGrow** est spécifié avec une valeur supérieure à 0, ce paramètre est ignoré.

* **NumOfActiveQueuedTasksToGrowThreshold** : nombre de tâches en file d’attente actives pour démarrer le processus d’agrandissement.

* **NumOfInitialNodesToGrow** : nombre initial de nœuds à agrandir si tous les nœuds dans l’étendue ont pour état **Non déployé** ou **Arrêté (désalloué)**.

* **GrowCheckIntervalMins** : intervalle en minutes entre les vérifications d’agrandissement.

* **ShrinkCheckIntervalMins** : intervalle en minutes entre les vérifications de réduction.

* **ShrinkCheckIdleTimes** : nombre de vérifications de réduction continues (séparées par l’intervalle **ShrinkCheckIntervalMins**) pour indiquer que les nœuds sont inactifs.

* **UseLastConfigurations** : configurations précédemment enregistrées dans le fichier d’arguments.

* **ArgFile** : nom du fichier d’arguments utilisé pour enregistrer et mettre à jour les configurations pour exécuter le script.

* **LogFilePrefix** : nom du préfixe du fichier journal. Vous pouvez spécifier un chemin d’accès. Par défaut, le journal est écrit dans le répertoire de travail actif.

### Exemple 1

L’exemple suivant configure les nœuds d’extension Azure déployés avec le modèle Default AzureNode Template pour qu’ils puissent s’agrandir ou se réduire automatiquement. Si tous les nœuds sont initialement dans l’état **Non déployé**, au moins 3 nœuds sont démarrés. Si le nombre de travaux en file d’attente dépasse 8, le script démarre les nœuds jusqu’à ce que leur nombre soit supérieur au rapport travaux en file d’attente/**NumOfQueuedJobsPerNodeToGrow**. Si un nœud s’avère inactif pendant 3 périodes d’inactivité consécutives, il est arrêté.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### Exemple 2

L’exemple suivant configure les machines virtuelles à nœud de calcul Azure déployées avec le modèle Default ComputeNode Template pour qu’elles puissent s’agrandir ou se réduire automatiquement. Les travaux configurés par le modèle de travail Default définissent l’étendue de la charge de travail sur le cluster. Si tous les nœuds sont initialement arrêtés, au moins 5 nœuds sont démarrés. Si le nombre de tâches en file d’attente actives dépasse 15, le script démarre les nœuds jusqu’à ce que leur nombre soit supérieur au rapport tâches en file d’attente actives/**NumOfActiveQueuedTasksPerNodeToGrow**. Si un nœud s’avère inactif pendant 10 périodes d’inactivité consécutives, il est arrêté.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=Oct15_HO3-->