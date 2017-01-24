---
title: "Exécuter Star-CCM+ avec HPC Pack sur des machines virtuelles Linux | Microsoft Docs"
description: "Déployer un cluster Microsoft HPC Pack sur Azure et exécuter un travail Star-CCM+ sur plusieurs nœuds de calcul Linux d’un réseau RDMA."
services: virtual-machines-linux
documentationcenter: 
author: xpillons
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
translationtype: Human Translation
ms.sourcegitcommit: 550db52c2b77ad651b4edad2922faf0f951df617
ms.openlocfilehash: 94a6a0968336f74ff116bf71aaa2e7c2ee135105


---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Exécuter Star-CCM+ avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure
Cet article vous explique comment déployer un cluster Microsoft HPC Pack sur Azure et exécuter un travail [CD-adapco STAR-CCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) sur plusieurs nœuds de calcul Linux interconnectés avec InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack fournit des fonctionnalités permettant d’exécuter un éventail d’applications HPC à grande échelle et parallèles, y compris des applications MPI, sur des clusters de machines virtuelles Microsoft Azure. HPC Pack prend également en charge l’exécution d’applications Linux HPC sur les machines virtuelles de nœuds de calcul Linux déployées dans un cluster HPC Pack. Pour une présentation de l’utilisation des nœuds de calcul Linux avec HPC Pack, consultez l’article [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="set-up-an-hpc-pack-cluster"></a>Configurer un cluster HPC Pack
Téléchargez les scripts de déploiement IaaS de HPC Pack à partir du [Centre de téléchargement](https://www.microsoft.com/en-us/download/details.aspx?id=44949) et extrayez-les localement.

Azure PowerShell doit être installé et configuré. Si le logiciel PowerShell n’est pas configuré sur votre ordinateur local, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

Lors de la rédaction de cet article, les images Linux d’Azure Marketplace (qui contient les pilotes InfiniBand pour Azure) correspondent à SLES 12, CentOS 6.5 et CentOS 7.1. Cet article utilise SLES 12. Pour récupérer le nom de toutes les images Linux prenant en charge HPC dans le Marketplace, exécutez la commande PowerShell suivante :

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

La sortie indique l’emplacement où ces images sont disponibles et le nom d’image (**ImageName**) à utiliser ultérieurement dans le modèle de déploiement.

Avant de déployer le cluster, vous devez générer un fichier modèle de déploiement de HPC Pack. Comme notre cluster cible est de taille modeste, le nœud principal est le contrôleur de domaine et héberge une base de données SQL locale.

Le modèle ci-dessous déploie un nœud principal, crée le fichier XML **MyCluster.xml** et remplace les valeurs de **SubscriptionId**, **StorageAccount**, **Location**, **VMName** et **ServiceName** par les vôtres.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Lancez la création du nœud principal en exécutant la commande PowerShell dans une invite de commandes avec élévation de privilèges :

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Le nœud principal devrait être prêt au bout de 20 à 30 minutes. Vous pouvez vous y connecter à partir du portail Azure en cliquant sur l’icône **Connecter** de la machine virtuelle.

Vous devrez peut-être corriger le redirecteur DNS. Pour ce faire, démarrez le Gestionnaire DNS.

1. Cliquez avec le bouton droit sur le nom du serveur dans le Gestionnaire DNS, sélectionnez **Propriétés**, puis cliquez sur l’onglet **Redirecteurs**.
2. Cliquez sur le bouton **Modifier** pour supprimer tous les redirecteurs, puis cliquez sur **OK**.
3. Vérifiez que la case **Utiliser les indications de racine si aucun redirecteur n’est disponible** est cochée et cliquez sur **OK**.

## <a name="set-up-linux-compute-nodes"></a>Créer les nœuds de calcul Linux
Pour déployer les nœuds de calcul Linux, utilisez le même modèle de déploiement que vous avez utilisé pour créer le nœud principal.

Copiez le fichier **MyCluster.xml** de votre ordinateur local vers le nœud principal et modifiez la balise **NodeCount** en fonction du nombre de nœuds à déployer (<= 20). Veillez à avoir un nombre suffisant de cœurs dans votre quota Azure, car chaque instance A9 consomme 16 cœurs dans votre abonnement. Vous pouvez utiliser des instances A8 (8 cœurs) au lieu d’instances A9, si vous souhaitez utiliser davantage de machines virtuelles avec le même budget.

Sur le nœud principal, copiez les scripts de déploiement IaaS de HPC Pack.

Exécutez les commandes Azure PowerShell suivantes dans une invite de commandes avec élévation de privilèges :

1. Exécutez **Add-AzureAccount** pour vous connecter à votre abonnement Azure.
2. Si vous avez plusieurs abonnements, exécutez **Get-AzureSubscription** pour les afficher.
3. Définissez l’abonnement par défaut en exécutant la commande **Select-AzureSubscription -SubscriptionName xxxx-Default** .
4. Exécutez **.\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** pour commencer à déployer les nœuds de calcul Linux.
   
   ![Déploiement d’un nœud principal en action][hndeploy]

Ouvrez le Gestionnaire de cluster de HPC Pack. Après quelques minutes, des nœuds de calcul Linux apparaîtront régulièrement dans la liste. En mode de déploiement classique, les machines virtuelles IaaS sont créées de manière séquentielle. Par conséquent, si le nombre de nœuds est important, le déploiement de l’ensemble des nœuds peut prendre un certain temps.

![Nœuds Linux dans le Gestionnaire de cluster de HPC Pack][clustermanager]

Maintenant que tous les nœuds sont opérationnels dans le cluster, il reste quelques opérations de configuration de l’infrastructure à effectuer.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Configurer un partage Azure File pour les nœuds Windows et Linux
Vous pouvez utiliser le service Azure File pour stocker les scripts, les packages d’applications et les fichiers de données. Azure File fournit des fonctionnalités CIFS en plus d’un stockage d’objets blob Azure comme magasin persistant. Sachez que ce n’est pas la solution la plus évolutive, mais c’est la plus simple et elle ne nécessite aucune machine virtuelle dédiée.

Créez un partage Azure File en suivant les instructions fournies dans l’article [Prise en main d’Azure File Storage sur Windows](../storage/storage-dotnet-how-to-use-files.md).

Conservez le nom de votre compte de stockage **saname**, le nom du partage de fichiers **sharename** et la clé du compte de stockage **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montage du partage Azure File sur le nœud principal
Ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante pour stocker les informations d’identification dans le coffre de l’ordinateur local :

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Puis, pour monter le partage Azure File, exécutez :

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montage du partage Azure File sur les nœuds de calcul Linux
L’utilitaire clusrun, fourni avec HPC Pack, est particulièrement utile. Cet outil de ligne de commande vous permet d’exécuter la même commande simultanément sur plusieurs nœuds de calcul. Dans notre cas, il est utilisé pour monter le partage Azure File et le rendre persistant aux redémarrages.
Exécutez les commandes suivantes dans une invite de commandes avec élévation de privilèges sur le nœud principal.

Pour créer le répertoire du point de montage :

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Pour monter le partage Azure File :

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Pour rendre le partage permanent :

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Installer STAR-CCM+
Les instances A8 et A9 de machine virtuelle Azure prennent en charge InfiniBand et les fonctionnalités RDMA. Les pilotes de noyau qui mettent en œuvre ces fonctionnalités sont disponibles pour les images Windows Server 2012 R2, SUSE 12, CentOS 6.5 et CentOS 7.1 dans Azure Marketplace. Microsoft MPI et Intel MPI (version 5.x) sont les deux bibliothèques MPI qui prennent en charge ces pilotes dans Azure.

CD-adapco STAR-CCM+ 11.x et version ultérieure est fourni avec Intel MPI version 5.x et prend donc en charge InfiniBand pour Azure.

Récupérez le package Linux64 STAR-CCM+ sur le [portail CD-adapco](https://steve.cd-adapco.com). Dans notre cas, nous avons utilisé la version 11.02.010 en précision mixte.

Sur le nœud principal, dans le partage Azure File **/hpcdata**, créez un script shell intitulé **setupstarccm.sh** avec le contenu suivant. Ce script s’exécutera sur chaque nœud de calcul pour configurer STAR-CCM+ localement.

#### <a name="sample-setupstarcmsh-script"></a>Exemple de script setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Maintenant, pour installer Star-CCM+ sur tous vos nœuds de calcul Linux, ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante :

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Pendant l’exécution de la commande, vous pouvez surveiller l’utilisation du processeur à l’aide de la carte thermique du Gestionnaire de cluster. Après quelques minutes, tous les nœuds devraient être correctement installés.

## <a name="run-star-ccm-jobs"></a>Exécuter des travaux STAR-CCM+
HPC Pack est utilisé pour ses fonctionnalités de planificateur afin d’exécuter des travaux Star-CCM+. Pour ce faire, nous avons besoin de prendre en charge les quelques scripts utilisés pour démarrer le travail et exécuter Star-CCM+. Pour plus de simplicité, les données d’entrée sont d’abord conservées sur le partage Azure File.

Le script PowerShell suivant est utilisé pour mettre en attente un travail Star-CCM+. Il tient compte de trois arguments :

* Le nom du modèle
* Le nombre de nœuds à utiliser
* Le nombre de cœurs sur chaque nœud à utiliser

Comme Star-CCM+ peut saturer la bande passante de mémoire, il vaut mieux utiliser moins de cœurs par nœud de calcul et ajouter de nouveaux nœuds. Le nombre exact de cœurs par nœud dépend de la gamme de processeurs et de la vitesse d’interconnexion.

Les nœuds sont alloués exclusivement au travail et ne peuvent pas être partagés avec d’autres travaux. Le travail n’est pas directement démarré comme un travail MPI. Le script shell **runstarccm.sh** permet de démarrer le lanceur MPI.

Le modèle d’entrée et le script **runstarccm.sh** sont stockés dans le partage **/hpcdata** précédemment monté.

Les fichiers journaux sont nommés d’après l’ID du travail et stockés dans le **partage /hpcdata**avec les fichiers de sortie STAR-CCM+.

#### <a name="sample-submitstarccmjobps1-script"></a>Exemple de script SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Remplacez le **runner.java** par le lanceur de modèle Java STAR-CCM+ de votre choix et votre code de journalisation.

#### <a name="sample-runstarccmsh-script"></a>Exemple de script runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

Dans notre test, nous avons utilisé un jeton de licence Power-On-Demand, dont vous devez définir la variable d’environnement **$CDLMD_LICENSE_FILE** sur **1999@flex.cd-adapco.com** et la clé dans l’option **-podkey** de la ligne de commande.

Après une phase d’initialisation, le script extrait, à partir des variables d’environnement **$CCP_NODES_CORES** définies par HPC Pack, la liste des nœuds permettant de générer un fichier d’hôte utilisé par le lanceur MPI. Ce fichier d’hôte contient la liste des noms de nœuds de calcul utilisés pour le travail (un nom par ligne).

Le format de **$CCP_NODES_CORES** suit ce modèle :

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Où :

* `<Number of nodes>` est le nombre de nœuds affectés à ce travail.
* `<Name of node_n_...>` est le nom de chaque nœud affecté à ce travail.
* `<Cores of node_n_...>` est le nombre de cœurs présents sur le nœud affecté à ce travail.

Le nombre de cœurs (**$NBCORES**) est également calculé en fonction du nombre de nœuds (**$NBNODES**) et du nombre de cœurs par nœud (fourni comme paramètre **$NBCORESPERNODE**).

Les options MPI utilisées avec Intel MPI dans Azure sont les suivantes :

* `-mpi intel` pour définir Intel MPI.
* `-fabric UDAPL` pour utiliser des verbes Azure InfiniBand.
* `-cpubind bandwidth,v` pour optimiser la bande passante pour MPI avec STAR-CCM+.
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` pour faire fonctionner Intel MPI avec Azure InfiniBand et pour définir le nombre de cœurs par nœud requis.
* `-batch` pour démarrer STAR-CCM+ en mode batch sans interface utilisateur.

Enfin, pour démarrer un travail, vérifiez que vos nœuds sont opérationnels et en ligne dans le Gestionnaire de cluster. Puis, dans une invite de commandes PowerShell, exécutez la commande suivante :

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Arrêter les nœuds
Ensuite, une fois vos tests terminés, vous pouvez utiliser les commandes PowerShell HPC Pack suivantes pour arrêter et démarrer des nœuds :

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Étapes suivantes
Essayez d’exécuter d’autres charges de travail Linux, par exemple :

* [Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png



<!--HONumber=Dec16_HO1-->


