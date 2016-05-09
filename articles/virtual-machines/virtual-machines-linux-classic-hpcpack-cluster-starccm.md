<properties
 pageTitle="Exécuter StarCCM+ avec HPC Pack sur des machines virtuelles Linux | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et exécuter un travail StarCCM+ sur plusieurs nœuds de calcul Linux d’un réseau RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="xpillons"
 manager="kateh"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="04/13/2016"
 ms.author="xpillons"/>

# Exécuter StarCCM+ avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure
Cet article vous explique comment déployer un cluster Microsoft HPC Pack sur Azure et exécuter un travail [CD-Adapco StarCCM+](http://www.cd-adapco.com/products/star-ccm%C2%AE) sur plusieurs nœuds de calcul Linux interconnectés avec Infiniband.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack fournit des fonctionnalités permettant d’exécuter un éventail d’applications HPC à grande échelle et parallèles, y compris des applications MPI, sur des clusters de machines virtuelles Microsoft Azure. HPC Pack prend également en charge l’exécution d’applications Linux HPC sur les machines virtuelles de nœuds de calcul Linux déployées dans un cluster HPC Pack. Voir [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour une présentation de l’utilisation des nœuds de calcul Linux avec HPC Pack.

## Installer le cluster HPC Pack
Téléchargez les scripts de déploiement IaaS de HPC Pack à partir d’[ici](https://www.microsoft.com/fr-FR/download/details.aspx?id=44949) et extrayez-les localement.

Azure PowerShell doit être présent. Lisez l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) si ce logiciel n’est pas configuré sur votre ordinateur local.

Lors de la rédaction de cet article, les images Linux de la galerie Azure qui contient les pilotes Infiniband pour Azure correspondent à SLES 12, CentOS 6.5 et CentOS 7.1. Cet article utilise SLES 12. Pour récupérer le nom de toutes les images Linux HPC prenant en charge HPC dans la galerie, exécutez la commande PowerShell suivante :

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

La sortie indique l’emplacement où ces images sont disponibles et le nom d’image **ImageName** à utiliser ultérieurement dans le modèle de déploiement. Avant de déployer le cluster, vous devez générer un fichier modèle de déploiement de HPC Pack. Comme notre cluster cible est de taille modeste, le nœud principal est le contrôleur de domaine et héberge une base de données SQL locale. Le modèle ci-dessous déploie un nœud principal, crée le fichier **MyCluster.xml** et remplace les valeurs de **SubscriptionId**, **StorageAccount**, **Location**, **VMName** et **ServiceName** par les vôtres.

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

Lancez la création du nœud principal en exécutant la commande PowerShell dans une fenêtre de commande avec élévation de privilèges :

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Après 20 à 30 min, le nœud principal doit être créé. Vous pouvez vous y connecter à partir du portail Azure en cliquant sur l’icône de connexion de la machine virtuelle.

Vous devrez peut-être corriger le redirecteur DNS. Pour ce faire, démarrez le Gestionnaire DNS.

1.  Cliquez avec le bouton droit sur le nom du serveur dans le Gestionnaire DNS, sélectionnez Propriétés puis l’onglet Redirecteurs.

2.  Cliquez sur le bouton Modifier pour supprimer tous les redirecteurs, puis cliquez sur OK.

3.  Vérifiez que la case « Utiliser les indications de racine si aucun redirecteur n’est disponible » est cochée et cliquez sur OK.

## Créer les nœuds de calcul Linux
Le déploiement des nœuds de calcul Linux s’effectue avec le même modèle de déploiement que celui utilisé pour créer le nœud principal. Copiez le fichier **MyCluster.xml** de votre ordinateur local dans le nœud principal (HeadNode) et modifiez la balise **NodeCount** en fonction du nombre de nœuds à déployer (<= 20). Veillez à avoir un nombre suffisant de cœurs dans votre quota Azure, car chaque instance A9 consomme 16 cœurs dans votre abonnement. Vous pouvez utiliser des instances A8 (8 cœurs) au lieu d’instances A9, si vous souhaitez utiliser davantage de machines virtuelles avec le même budget.

Sur le nœud principal, copiez les scripts de déploiement IaaS de HPC Pack.

Ouvrez une fenêtre Azure PowerShell dans une fenêtre de commande avec élévation de privilèges :

1.  Exécutez **Add-AzureAccount** pour vous connecter à votre abonnement Azure.

2.  Si vous avez plusieurs abonnements, exécutez **Get-AzureSubscription** pour les afficher.

3.  Définissez l’abonnement par défaut en exécutant la commande **Select-AzureSubscription -SubscriptionName xxxx-Default**.

4.  Exécutez **.\\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml** pour commencer à déployer les nœuds de calcul Linux. ![Déploiement d’un nœud principal en action][hndeploy]

Ouvrez l’outil Gestionnaire de cluster de HPC Pack. Après quelques minutes, des nœuds de calcul Linux apparaissent régulièrement dans la liste. En mode de déploiement classique, les machines virtuelles IaaS sont créées de manière séquentielle. Donc, si le nombre de nœuds est important, le déploiement peut prendre beaucoup de temps. ![Nœuds Linux dans le Gestionnaire de cluster de HPC Pack][clustermanager]

Maintenant que tous les nœuds sont opérationnels dans le cluster, il reste quelques opérations de configuration de l’infrastructure à faire.

## Configurer un partage Azure File pour les nœuds Windows et Linux
Pour nous simplifier la tâche, nous utilisons des fichiers Azure File qui fournissent des fonctionnalités CIFS en plus des objets blob Azure comme magasin persistant, pour stocker nos scripts, packages d’application et fichiers de données. Sachez que ce n’est pas la solution la plus évolutive, mais c’est la plus simple et elle ne nécessite aucune machine virtuelle dédiée.

Créez un partage de fichiers Azure en suivant les instructions fournies dans l’article [Prise en main du stockage de fichiers Azure sur Windows](..\storage\storage-dotnet-how-to-use-files.md).

Conservez le nom de votre compte de stockage **saname**, le nom du partage de fichiers **sharename** et la clé du compte de stockage **sakey**.

### Montage du partage Azure File sur le nœud principal
Ouvrez une invite de commandes avec élévation de privilèges et exécutez la commande suivante pour stocker les informations d’identification dans le coffre de l’ordinateur local.

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Puis, pour monter le partage Azure File, exécutez :

```
    net use Z: \<saname>.file.core.windows.net<sharename> /persistent:yes
```

### Montage du partage Azure File sur les nœuds de calcul Linux
L’utilitaire clusrun, fourni avec HPC Pack, est un outil très utile. Il vous permet d’exécuter la même commande simultanément sur plusieurs nœuds de calcul. Dans notre cas, il est utilisé pour monter le partage Azure File et le rendre persistant aux redémarrages. Exécutez les commandes suivantes dans une fenêtre de commande avec élévation de privilèges.

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

## Mettre à jour les pilotes Linux
Il se peut que vous deviez mettre à jour les pilotes Infiniband des nœuds de calcul Linux. Pour plus d’informations sur cette opération, lisez l’article [Mettre à jour les pilotes Linux RDMA pour SLES 12](virtual-machines-linux-classic-rdma-cluster.md/#update-the-linux-rdma-drivers-for-sles-12)

## Installer StarCCM+
Les instances A8 et A9 de machine virtuelle Azure prennent en charge Infiniband et les fonctions RDMA. Au moment de la rédaction de ce document, les pilotes de noyau mettant en œuvre ces fonctionnalités sont disponibles pour les images Windows 2012 R2 et SUSE 12 dans la galerie Azure. CentOS 7.x sera bientôt disponible. Microsoft MPI et Intel MPI (version 5.x) sont les deux bibliothèques MPI qui prennent en charge ces pilotes dans Azure.

CD-Adapco StarCCM+ 11.x et version ultérieure est fourni avec Intel MPI version 5.x et prend donc en charge Infiniband pour Azure.

Récupérez le package Linux64 StarCCM+ sur le [portail CD-Adapco](https://steve.cd-adapco.com). Dans notre cas, nous avons utilisé la version 11.02.010 en précision mixte.

Sur le nœud principal, dans le partage Azure File **/hpcdata**, créez un script shell intitulé **setupstarccm.sh** avec le contenu suivant. Ce script sera lancé sur chaque nœud de calcul pour installer StarCCM+ en local.

#### Exemple de script setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh setup StarCCM+ locally

    # create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # copy the starccm package from the file share to the local dir
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # start a silent installation of starccm without flexlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Maintenant, pour installer StarCCM+ sur tous vos nœuds de calcul Linux, ouvrez une fenêtre de commande avec élévation de privilèges et exécutez la commande suivante :

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Pendant l’exécution de la commande, vous pouvez surveiller l’utilisation du processeur avec la carte thermique du Gestionnaire de cluster. Après quelques minutes, tous les nœuds doivent être correctement installés.

## Exécution de travaux StarCCM+
HPC Pack est utilisé pour ses fonctionnalités de planificateur afin d’exécuter des travaux StarCCM+. Pour ce faire, nous avons besoin de prendre en charge les quelques scripts utilisés pour démarrer le travail et exécuter StarCCM+. Par souci de simplicité, les données d’entrée sont conservées d’abord sur le partage Azure File. Le script PowerShell suivant est utilisé pour mettre en attente un travail StarCCM+. Il faut 3 arguments :

*  Le nom du modèle
*  Le nombre de nœuds à utiliser
*  Le nombre de cœurs sur chaque nœud à utiliser

Comme StarCCM+ peut saturer la bande passante de mémoire, il vaut mieux utiliser moins de cœurs par nœuds de calcul et ajouter de nouveaux nœuds. Le nombre exact de cœurs par nœud dépend de la famille de processeurs et la vitesse d’interconnexion.

Les nœuds sont alloués exclusivement au travail et ne peuvent pas être partagés avec d’autres travaux. Comme vous pouvez le constater, le travail n’est pas directement démarré comme un travail MPI, et le lanceur MPI est démarré à partir du script shell **runstarccm.sh**.

Le modèle d’entrée et le script **runstarccm.sh** sont censés être stockés dans le partage **/hpcdata** précédemment monté.

Les fichiers journaux sont nommés d’après l’ID du travail et stockés dans le partage **/hpcdata** ainsi que les fichiers de sortie StarCCM+.


#### Exemple de script SubmitStarccmJob.ps1
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # create a new job, this will give us the JobId used to identify the name of the uploaded package in azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # submit job 	
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Remplacez le **runner.java** par votre lanceur de modèle java StarCCM+ et votre code de journalisation.

#### Exemple de script runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set mpirun runtime environment
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

    # Run the starccm with hostfile arg
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

Dans notre test, nous avons utilisé un jeton de licence Power-One-Demand dont vous devez régler la variable d’environnement **$CDLMD\_LICENSE\_FILE** sur ****1999@flex.cd-adapco.com** et la clé dans l’option **- podkey** de la ligne de commande.

Après une phase d’initialisation, le script extrait des variables d’environnement **$CCP\_NODES\_CORES** définies par HPC Pack, la liste des nœuds permettant de générer un fichier d’hôte utilisé par le service de lancement MPI. Ce fichier d’hôte contient la liste des noms de nœud de calcul utilisés pour le travail, un nom par ligne.

Le format de **$CCP\_NODES\_CORES** est le suivant :

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

où

* `<Number of nodes>` : nombre de nœuds affectés à ce travail.  
* `<Name of node_n_...>` : nom de chaque nœud affecté à ce travail.
* `<Cores of node_n_...>` : nombre de cœurs présents sur le nœud affecté à ce travail.

Le nombre de cœurs **$NBCORES** est également calculé en fonction du nombre de nœuds **$NBNODES** et du nombre de cœurs par nœud fournis comme paramètre **$NBCORESPERNODE**.

Les options MPI utilisées avec Intel MPI dans Azure sont les suivantes :

*   -mpi intel = > pour spécifier Intel MPI
*   -fabric UDAPL => pour utiliser des verbes Infiniband Azure
*   -cpubind bandwidth,v => pour optimiser la bande passante pour MPI avec StarCCM+
*   -mppflags "-ppn $NBCORESPERNODE -genv I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0 -genv I\_MPI\_DAPL\_UD=0 -genv I\_MPI\_DYNAMIC\_CONNECTION=0" => ces paramètres d’Intel MPI fonctionnent avec Azure Infiniband et définissent le nombre requis de cœurs par nœud
*   -batch => pour démarrer StarCCM+ en mode batch sans interface utilisateur


Enfin, pour démarrer un travail, vérifiez que vos nœuds sont en cours d’exécution et en ligne dans le Gestionnaire de cluster. Ensuite, dans une fenêtre PowerShell, exécutez la commande suivante :

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## Arrêt de nœuds
Ensuite, une fois vos tests terminés, pour arrêter et démarrer des nœuds, vous pouvez utiliser les commandes PowerShell HPC Pack suivantes :

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## Étapes suivantes
Essayez d’exécuter d’autres charges de travail Linux par exemple. Consultez :

* [Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md).

* [Exécuter OpenFoam avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md).


<!--Image references-->
[hndeploy]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/hndeploy.png
[clustermanager]: ./media/virtual-machines-linux-classic-hpcpack-cluster-starccm/ClusterManager.png

<!---HONumber=AcomDC_0427_2016-->