---
title: "Répliquer des machines virtuelles Hyper-V dans des clouds VMM à l’aide d’Azure Site Recovery et de PowerShell | Microsoft Docs"
description: "Découvrez comment automatiser la réplication de machines virtuelles Hyper-V dans des clouds VMM à l&quot;aide de Site Recovery et PowerShell."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: tysonn
ms.assetid: 9011f567-e0b4-4306-951a-b30da19f5db6
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7ccf60c1f83a50b948b9855f2fedefeb75a0393b


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de PowerShell et du déploiement classique
> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-vmm-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Portail Classic](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - Classique](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Vue d'ensemble
Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement, consultez la [Vue d’ensemble d’Azure Site Recovery](site-recovery-overview.md).

Cet article vous montre comment utiliser PowerShell pour automatiser les tâches courantes à effectuer lorsque vous configurez Azure Site Recovery pour répliquer des machines virtuelles Hyper-V dans des clouds System Center VMM vers le stockage Azure.

L’article indique les conditions prérequises pour le scénario et montre comment configurer un coffre Récupération de sites, installer le fournisseur Azure Site Recovery sur le serveur VMM source, inscrire le serveur dans le coffre, ajouter un compte de stockage Azure, installer l'agent Azure Recovery Services sur les serveurs hôtes Hyper-V, configurer les paramètres de protection des clouds VMM qui seront appliqués à toutes les machines virtuelles protégées, puis activer la protection pour ces machines virtuelles. Pour finir, vous pourrez tester le basculement pour vous assurer que tout fonctionne comme prévu.

Si vous rencontrez des problèmes pour mettre en œuvre ce scénario, posez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique.
>
>

## <a name="before-you-start"></a>Avant de commencer
Assurez-vous que les conditions préalables sont remplies :

### <a name="azure-prerequisites"></a>Conditions préalables pour Azure
* Vous aurez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. La géo-réplication doit être activée pour ce compte. Il doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement. [En savoir plus sur Azure Storage](../storage/storage-introduction.md).
* Vous devez vous assurer que les machines virtuelles que vous souhaitez protéger sont conformes à la [configuration requise pour les machines virtuelle Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### <a name="vmm-prerequisites"></a>Configuration requise pour VMM
* Vous aurez besoin d’un serveur VMM exécuté sur System Center 2012 R2.
* Vous aurez besoin d'au moins un cloud sur le serveur VMM que vous souhaitez protéger. Le coud doit contenir :
  * un ou plusieurs groupes hôtes VMM ;
  * un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte ;
  * une ou plusieurs machines virtuelles sur le serveur Hyper-V source.

### <a name="hyper-v-prerequisites"></a>Conditions préalables liées à Hyper-V
* Les serveurs hôtes Hyper-V doivent exécuter au moins **Windows Server 2012** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012** et les dernières mises à jour doivent être installées.
* Si vous utilisez Hyper-V dans un cluster, notez que le service Broker du cluster n'est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker du cluster. Pour ce faire, dans le Gestionnaire de serveur > Gestionnaire du cluster de basculement, connectez-vous au cluster, cliquez sur **Configurer un rôle** et sélectionnez **Service Broker de réplication Hyper-V** dans l’écran **Sélectionner un rôle** de l’Assistant Haute disponibilité.
* Tout cluster ou serveur hôte Hyper-V pour lequel vous souhaitez gérer la protection doit être inclus dans un cloud VMM.

### <a name="network-mapping-prerequisites"></a>Conditions préalables liées au mappage réseau
Quand vous protégez des machines virtuelles dans Azure, le mappage réseau effectue un mappage entre les réseaux de machines virtuelles sur le serveur VMM source et les réseaux Azure cibles pour permettre ce qui suit :

* Toutes les machines qui basculent sur le même réseau peuvent se connecter les unes aux autres, quel que soit le plan de récupération auquel elles appartiennent.
* Si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent se connecter à d'autres machines virtuelles locales.
* Si vous ne configurez pas le mappage réseau, seules les machines virtuelles qui basculent dans le même plan de récupération pourront se connecter les unes aux autres après le basculement vers Azure.

Si vous souhaitez déployer le mappage réseau, les conditions suivantes doivent être remplies :

* Les machines virtuelles que vous souhaitez protéger sur le serveur VMM source doivent être connectées à un réseau de machines virtuelles. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
* Un réseau Azure auquel les machines virtuelles répliquées peuvent se connecter après le basculement. Vous sélectionnerez ce réseau au moment du basculement. Le réseau doit être dans la même région que votre abonnement Azure Site Recovery.
* [En savoir plus](site-recovery-network-mapping.md) sur le mappage réseau :

### <a name="powershell-prerequisites"></a>Conditions préalables pour PowerShell
Assurez-vous qu’Azure PowerShell est prêt à l’emploi. Si vous utilisez déjà PowerShell, vous devrez passer à la version 0.8.10 ou ultérieure. Pour plus d'informations sur la configuration de PowerShell, consultez la section [Installation et configuration d'Azure PowerShell](/powershell/azureps-cmdlets-docs). Une fois PowerShell configuré, vous pouvez afficher toutes les applets de commande disponibles pour le service [ici](https://msdn.microsoft.com/library/dn850420.aspx).

Pour obtenir des conseils sur l'utilisation des applets de commande, par exemple la façon dont les valeurs de paramètres, les entrées et les sorties sont gérées dans Azure PowerShell, consultez la section [Prise en main des applets de commande Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## <a name="step-1-set-the-subscription"></a>Étape 1 : Définition de l’abonnement
Dans PowerShell, exécutez ces applets de commande :

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

Remplacez les éléments entre « < > » par vos informations spécifiques.

## <a name="step-2-create-a-site-recovery-vault"></a>Étape 2 : Création d’un coffre Site Recovery
Dans PowerShell, remplacez les éléments entre « < > » par vos informations spécifiques, et exécutez ces commandes :

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>Étape 3 : Génération d’une clé d'inscription du coffre
Générez une clé d'inscription dans le coffre. Une fois que vous aurez téléchargé et installé le fournisseur Azure Site Recovery sur le serveur VMM, vous utiliserez cette clé pour inscrire le serveur VMM dans le coffre.

1. Récupérez le fichier de configuration du coffre et définissez le contexte :

   ```

   $VaultName = "<testvault123>"
   $VaultGeo  = "<Southeast Asia>"
   $OutputPathForSettingsFile = "<c:\>"

   $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

   ```
2. Définissez le contexte du coffre en exécutant les commandes suivantes :

   ```

   $VaultSettingFilePath = $vaultSetingsFile.FilePath
   $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

   ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>Étape 4 : Installation du fournisseur Azure Site Recovery
1. Sur la machine VMM, créez un répertoire en exécutant la commande suivante :

   ```

   pushd C:\ASR\

   ```
2. Extrayez les fichiers à l'aide du fournisseur téléchargé en exécutant la commande suivante

   ```

   AzureSiteRecoveryProvider.exe /x:. /q

   ```
3. Installez le fournisseur à l’aide de la commande suivante :

   ```

   .\SetupDr.exe /i

   ```

   ```

   $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
         $isNotInstalled = $false;
     }
   }While($isNotInstalled)

   ```

   Attendez que l'installation se termine.
4. Inscrivez le serveur dans le coffre à l'aide de la commande suivante :

   ```

   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\"
   .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

   ```

## <a name="step-5-create-an-azure-storage-account"></a>Étape 5 : Création d’un compte de stockage Azure
Si vous n'avez pas de compte de stockage Azure, créez un compte avec activation de la géo-réplication en exécutant la commande suivante :

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Le compte de stockage doit se trouver dans la même région que le service Azure Site Recovery et être associé au même abonnement.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>Étape 6 : Installation de l'agent Azure Recovery Services
À partir du portail Azure, installez l'agent Azure Recovery Services sur chaque serveur hôte Hyper-V situé dans les clouds VMM que vous souhaitez protéger.

Exécutez la commande suivante sur l’ensemble des hôtes VMM :

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>Étape 7 : Configuration des paramètres de protection de cloud
1. Créez un profil de protection cloud pour Azure en exécutant la commande suivante :

   ```

   $ReplicationFrequencyInSeconds = "300";
   $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds     $ReplicationFrequencyInSeconds;

   ```
2. Récupérez un conteneur de protection en exécutant les commandes suivantes :

   ```

   $PrimaryCloud = "testcloud"
   $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

   ```
3. Lancez l'association du conteneur de protection avec le cloud :

   ```

   $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

   ```
4. Une fois la tâche terminée, exécutez la commande suivante :

        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


1. Une fois la tâche terminée, exécutez la commande suivante :

        Do
        {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

        if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
        }While($isJobLeftForProcessing)



Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

## <a name="step-8-configure-network-mapping"></a>Étape 8 : Configuration du mappage réseau
Avant de commencer le mappage réseau, vérifiez que les machines virtuelles sur le serveur VMM source sont connectées à un réseau de machines virtuelles. En outre, vous devez créer un ou plusieurs réseaux virtuels Azure. Notez que plusieurs réseaux de machines virtuelles peuvent être mappés à un seul réseau Azure.

Notez que si le réseau cible a plusieurs sous-réseaux et que l'un d'entre eux a le même nom que le sous-réseau où se trouve la machine virtuelle source, la machine virtuelle de réplication sera connectée à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.

La première commande récupère les serveurs pour le coffre Azure Site Recovery actuel. La commande stocke les serveurs Microsoft Azure Site Recovery dans la variable tableau $Servers.

    $Servers = Get-AzureSiteRecoveryServer


La deuxième commande récupère le réseau Site Recovery pour le premier serveur du tableau $Servers. La commande stocke les réseaux dans la variable $Networks.

    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

La troisième commande extrait vos abonnements Azure à l’aide de l’applet de commande Get-AzureSubscription, puis stocke cette valeur dans la variable $Subscriptions.

    $Subscriptions = Get-AzureSubscription



La quatrième commande récupère les réseaux virtuels Azure à l'aide de l'applet de commande Get-AzureVNetSite, puis stocke cette valeur dans la variable $AzureVmNetworks.

    $AzureVmNetworks = Get-AzureVNetSite



L'applet de commande finale crée un mappage entre le réseau principal et le réseau de la machine virtuelle Azure. L'applet de commande fixe le réseau principal comme premier élément de $Networks. L'applet de commande fixe un réseau de machine virtuelle comme premier élément de $AzureVmNetworks à l'aide de son ID. La commande inclut l’identifiant de votre abonnement Azure.

    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>Étape 9 : Activation de la protection des machines virtuelles
Dès lors que les serveurs, les clouds et les réseaux ont été configurés correctement, vous pouvez activer la protection pour les machines virtuelles du cloud. Notez les points suivants :

Les machines virtuelles doivent répondre à la [configuration requise pour les machines virtuelles Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

Pour activer la protection, vous devez définir les propriétés du système d'exploitation et du disque du système d'exploitation pour la machine virtuelle. Lorsque vous créez une machine virtuelle dans VMM à l'aide d'un modèle de machine virtuelle, vous pouvez définir la propriété. Vous pouvez également définir ces propriétés pour des machines virtuelles existantes sous les onglets **Général** et **Configuration matérielle** des propriétés de la machine virtuelle. Si vous ne définissez pas ces propriétés dans VMM, vous pourrez les configurer dans le portail Azure Site Recovery.

1. Afin d’activer la protection, exécutez la commande suivante pour récupérer le conteneur de protection :

     $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
2. Récupérez l’entité de protection (machine virtuelle) en exécutant les commandes suivantes :

        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



1. Activez la récupération d’urgence de la machine virtuelle en exécutant la commande suivante :

        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity     -Protection Enable -Force



## <a name="test-your-deployment"></a>Tester votre déploiement
Pour tester votre déploiement, vous pouvez exécuter un test de basculement pour une seule machine virtuelle, ou créer un plan de récupération comportant plusieurs machines virtuelles et exécuter sur lui un test de basculement. Il simule votre mécanisme de basculement et de récupération dans un réseau isolé. Notez les points suivants :

* Si vous voulez vous connecter à la machine virtuelle dans Azure avec le Bureau à distance après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement.
* Après le basculement, vous utiliserez une adresse IP publique pour vous connecter à la machine virtuelle dans Azure avec le Bureau à distance. Dans ce cas, assurez-vous qu'aucune de vos stratégies de domaine ne vous empêche de vous connecter à une machine virtuelle avec une adresse publique.

Pour vérifier que l'opération est terminée, suivez les étapes décrites dans la section [Suivi de l'activité](#monitor).

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération
1. Créez un fichier .xml comme modèle pour votre plan de récupération en utilisant les données ci-dessous, puis enregistrez-le sous « C:\RPTemplatePath.xml ».
2. Modifiez l'Id, Name, PrimaryServerId et SecondaryServerId du nœud RecoveryPlan.
3. Modifiez le PrimaryProtectionEntityId du nœud ProtectionEntity (vmid pour VMM).
4. Vous pouvez ajouter davantage de machines virtuelles en ajoutant des nœuds ProtectionEntity.

        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"     PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"     SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-    cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"     Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"     After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



1. Remplissez les données du modèle :

        $TemplatePath = "C:\RPTemplatePath.xml";



1. Créez le RecoveryPlan :

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;

### <a name="run-a-test-failover"></a>Exécution d’un test de basculement
1. Récupérez l'objet RecoveryPlan en exécutant la commande suivante :

     $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
2. Lancez le test de basculement en exécutant la commande suivante :

        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


## <a name="a-namemonitora-monitor-activity"></a><a name=monitor></a> Suivi de l'activité
Utilisez les commandes suivantes pour suivre l’activité. Vous devez attendre la fin du traitement entre les tâches.

    Do
    {
            $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
            Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
            if($job -eq $null -or $job.StateDescription -ne "Completed")
            {
                $isJobLeftForProcessing = $true;
            }

        if($isJobLeftForProcessing)
            {
                Start-Sleep -Seconds 60
            }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>Étapes suivantes
[Découvrez plus](https://msdn.microsoft.com/library/dn850420.aspx) d'informations sur les applets de commande PowerShell Azure Site Recovery. </a>.



<!--HONumber=Dec16_HO2-->


