---
title: Machines virtuelles de calcul Linux dans un cluster HPC Pack | Microsoft Docs
description: "Apprenez à créer et à utiliser un cluster HPC Pack dans Azure pour des charges de travail de calcul haute performance (HPC) Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 28426f048205e7cf836375d8b8855e06d5de807e
ms.lasthandoff: 03/27/2017


---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure
Configurez un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) dans Azure, contenant un nœud principal qui exécute Windows Server et plusieurs nœuds de calcul qui exécutent une distribution Linux. Explorez les différentes options vous permettant de déplacer les données entre les nœuds Linux et le nœud principal Windows du cluster. Découvrez comment soumettre des travaux Linux HPC au cluster.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Le schéma général suivant illustre le cluster HPC Pack que vous créez et utilisez.

![Cluster HPC Pack avec nœuds Linux][scenario]

Pour d’autres options d’exécution des charges de travail HPC Linux dans Azure, voir [Ressources techniques pour Batch et HPC (calcul haute performance)](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Déployer un cluster HPC Pack avec des nœuds de calcul Linux
Cet article présente deux possibilités de déploiement d’un cluster HPC Pack dans Azure avec des nœuds de calcul Linux. Les deux méthodes utilisent une image Marketplace de Windows Server avec HPC Pack pour créer le nœud principal. 

* **Modèle Azure Resource Manager** : utilisez un modèle issu de la Place de marché Azure ou un modèle de démarrage rapide issu de la communauté pour automatiser la création du cluster dans le modèle de déploiement Resource Manager. Par exemple, le modèle [cluster HPC Pack pour les charges de travail Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) dans Azure Marketplace crée une infrastructure de cluster HPC Pack complète pour les charges de travail HPC Linux.
* **Script PowerShell** : utilisez le [script de déploiement Microsoft HPC Pack  IaaS](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) pour automatiser le déploiement de cluster complet dans le modèle de déploiement classique. Ce script Azure PowerShell utilise une image de machine virtuelle HPC Pack d’Azure Marketplace pour un déploiement rapide, et fournit un ensemble complet de paramètres de configuration pour déployer les nœuds de calcul Linux.

Pour plus d’informations sur les options de déploiement d’un cluster HPC Pack dans Azure, voir les [Options pour créer et gérer un cluster HPC (High Performance Computing) dans Azure avec Microsoft HPC Pack](../../virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Composants requis
* **Abonnement Azure** : vous pouvez utiliser un abonnement dans le service Azure Global ou Azure Chine. Si vous ne possédez pas de compte, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/) en quelques minutes.
* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs, en particulier si vous choisissez de déployer plusieurs nœuds de cluster avec des tailles de machines virtuelles multiprocesseurs. Pour augmenter un quota, ouvrez une demande de service clientèle en ligne gratuitement.
* **Distributions Linux** : actuellement, HPC Pack prend en charge les distributions Linux suivantes pour les nœuds de calcul. Vous pouvez utiliser les versions Marketplace de ces distributions dans la mesure où elles sont disponibles, ou fournissez la vôtre.
  
  * **Basée sur centOS** : 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, HPC 6.5, HPC 7.1
  * **Red Hat Enterprise Linux** : 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server** : SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 pour HPC, SLES 12 pour HPC (Premium)
  * **Ubuntu Server** : 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Pour utiliser le réseau Azure RDMA avec l’une des tailles de machines virtuelles prenant en charge RDMA, spécifiez une image HPC SUSE Linux Enterprise Server 12 HPC ou une image HPC basé sur CentOS de la Place de marché Azure. Pour plus d’informations, voir [À propos des machines virtuelles de série H et de série A nécessitant beaucoup de ressources système](../../virtual-machines-linux-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Autres conditions préalables à respecter pour un déploiement du cluster à l’aide du script de déploiement HPC Pack IaaS :

* **Ordinateur client** : vous avez besoin d’un ordinateur client Windows pour exécuter le script de déploiement de cluster.
* **Azure PowerShell** - [installez et configurez Azure PowerShell](/powershell/azureps-cmdlets-docs) (version 0.8.10 ou ultérieure) sur votre ordinateur client.
* **Script de déploiement IaaS de HPC Pack** : téléchargez et décompressez la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vous pouvez vérifier la version du script en exécutant `.\New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.4.1 ou ultérieure du script.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Option de déploiement 1. Utiliser un modèle Resource Manager
1. Accédez au modèle [Cluster HPC Pack pour charges de travail Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) dans Azure Marketplace, puis cliquez sur **Déployer**.
2. Dans le portail Azure, passez en revue les informations, puis cliquez sur **Créer**.
   
    ![Création de portail][portal]
3. Dans le panneau **Informations de base** , attribuez un nom au cluster, qui est aussi le nom de la machine virtuelle du nœud principal. Vous pouvez choisir un groupe de ressources existant ou créer un groupe pour le déploiement dans un emplacement auquel vous avez accès. L’emplacement a une incidence sur la disponibilité de certaines tailles de machine virtuelle et d’autres services Azure (voir [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/)).
4. Pour un premier déploiement, vous pouvez généralement accepter les paramètres par défaut du panneau **Head node settings** (Paramètres du nœud principal). 
   
   > [!NOTE]
   > Le paramètre facultatif **Post-configuration script URL** (URL du script de post-configuration) permet de spécifier le script Windows PowerShell accessible au public que vous voulez exécuter sur la machine virtuelle du nœud principal une fois qu’il est en cours d’exécution. 
   > 
   > 
5. Dans le panneau **Compute node settings** (Paramètres du nœud de calcul), sélectionnez un modèle d’affectation de noms pour les nœuds, le nombre et la taille des nœuds, ainsi que la distribution Linux à déployer.
6. Dans le panneau **Infrastructure settings** (Paramètres d’infrastructure), entrez les noms du réseau virtuel et du domaine Active Directory, les informations d’identification d’administrateur de domaine et de machine virtuelle, ainsi qu’un modèle d’affectation de noms pour les comptes de stockage.
   
   > [!NOTE]
   > HPC Pack utilise le domaine Active Directory pour authentifier les utilisateurs du cluster. 
   > 
   > 
7. Après avoir exécuté les tests de validation et passé en revue les conditions d’utilisation, cliquez sur **Acheter**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Option de déploiement 2. Utilisation du script de déploiement IaaS
Voici les autres conditions préalables à respecter pour un déploiement du cluster à l’aide du script de déploiement HPC Pack IaaS :

* **Ordinateur client** : vous avez besoin d’un ordinateur client Windows pour exécuter le script de déploiement de cluster.
* **Azure PowerShell** - [installez et configurez Azure PowerShell](/powershell/azureps-cmdlets-docs) (version 0.8.10 ou ultérieure) sur votre ordinateur client.
* **Script de déploiement IaaS de HPC Pack** : téléchargez et décompressez la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vous pouvez vérifier la version du script en exécutant `.\New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.4.1 ou ultérieure du script.

**Fichier de configuration XML**

Le script de déploiement de HPC Pack IaaS utilise un fichier de configuration XML comme entrée pour décrire le cluster HPC. L’exemple de fichier de configuration suivant spécifie un petit cluster constitué d’un nœud principal HPC Pack et de deux nœuds de calcul Linux CentOS 7.0 de taille A7. 

Modifiez le fichier en fonction des besoins de votre environnement et de la configuration de cluster souhaitée, et enregistrez-le avec un nom similaire à HPCDemoConfig.xml. Par exemple, vous devez fournir votre nom d’abonnement, un nom de compte de stockage unique et un nom de service cloud. De plus, vous pouvez choisir une autre image Linux prise en charge pour les nœuds de calcul. Pour plus d’informations sur les éléments du fichier de configuration, consultez le fichier Manual.rtf dans le dossier de script et la rubrique [Create an HPC cluster with the HPC Pack IaaS deployment script](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)(Créer un cluster HPC avec le script de déploiement HPC Pack IaaS).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**Pour exécuter le script de déploiement HPC Pack IaaS**

1. Ouvrez Windows PowerShell sur l’ordinateur client en tant qu’administrateur.
2. Accédez au dossier d’installation du script (E:\IaaSClusterScript dans cet exemple).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Exécutez la commande suivante pour déployer le cluster HPC Pack. Cet exemple part du principe que le fichier de configuration se trouve dans E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Étant donné que le paramètre **AdminPassword** n’est pas spécifié dans la commande précédente, vous êtes invité à entrer le mot de passe pour l’utilisateur *MyAdminName*.
   
    b. Le script commence ensuite à valider le fichier de configuration. Selon la connexion réseau, cette opération peut durer plusieurs minutes.
   
    ![Validation][validate]
   
    c. Une fois les validations effectuées, le script répertorie les ressources de cluster à créer. Tapez *Y* pour continuer.
   
    ![Ressources][resources]
   
    d. Le script commence ensuite à déployer le cluster HPC Pack, puis termine la configuration sans autres étapes manuelles. L’exécution du script peut prendre plusieurs minutes.
   
    ![Déployer][deploy]
   
   > [!NOTE]
   > Dans cet exemple, le script génère un fichier journal automatiquement, car le paramètre **-LogFile** n’est pas spécifié. Les journaux ne sont pas écrits en temps réel, mais sont collectés à la fin de la validation et du déploiement. Si le processus PowerShell est arrêté pendant l’exécution du script, certains journaux sont perdus.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Connexion au nœud principal
Une fois le cluster HPC Pack déployé dans Azure, [connectez-vous via le Bureau à distance au nœud principal](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en utilisant les informations d’identification du domaine que vous avez fournies au moment où vous avez déployé le cluster (par exemple, *hpc\\clusteradmin*). Le cluster est géré à partir du nœud principal.

Sur le nœud principal, démarrez HPC Cluster Manager pour vérifier l’état du cluster HPC Pack. Vous pouvez gérer et surveiller les nœuds de calcul Linux de la même façon que les nœuds de calcul Windows. Par exemple, les nœuds Linux sont répertoriés dans **Gestion des ressources** (ces nœuds sont déployés avec le modèle **LinuxNode**).

![Gestion des nœuds][management]

Les nœuds Linux figurent aussi dans la vue **Carte thermique** .

![Carte thermique][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Comment déplacer des données dans un cluster de nœuds Linux
Vous avez plusieurs possibilités pour déplacer des données entre des nœuds de Linux et le nœud principal Windows du cluster. Ces trois méthodes courantes sont décrites plus en détail dans les sections suivantes :

* **Azure File** : expose un partage de fichiers SMB géré pour stocker les fichiers de données dans le stockage Azure. Les nœuds Windows et les nœuds Linux peuvent monter un partage Azure File comme un lecteur ou un dossier en même temps, même s’ils sont déployés dans des réseaux virtuels différents.
* **Head node SMB share** : monte un dossier partagé Windows standard du nœud principal sur des nœuds Linux.
* **Head node NFS server** : fournit une solution de partage de fichiers pour un environnement mixte Windows et Linux.

### <a name="azure-file-storage"></a>Présentation du stockage de fichiers
Le service [Azure File](https://azure.microsoft.com/services/storage/files/) expose les partages de fichiers en utilisant le protocole SMB 2.1 standard. Les machines virtuelles et les services cloud Azure peuvent partager les données des fichiers entre plusieurs composants d’application grâce à des partages montés. Les applications locales peuvent accéder aux données des fichiers d’un partage via l’API de stockage de fichiers. 

Pour obtenir des instructions détaillées sur la création d’un partage de fichiers Azure et sur son montage sur le nœud principal, consultez [Prise en main du stockage de fichiers Azure sur Windows](../../../storage/storage-dotnet-how-to-use-files.md). Pour monter le partage de fichiers Azure sur les nœuds Linux, consultez [Utilisation du stockage de fichiers Azure avec Linux](../../../storage/storage-how-to-use-files-linux.md). Pour configurer des connexions persistantes, consultez [Connexions persistantes à Microsoft Azure File](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

Dans l’exemple suivant, créez un partage de fichiers Azure sur un compte de stockage. Pour monter le partage sur le nœud principal, ouvrez une invite de commandes et entrez les commandes suivantes :

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Dans cet exemple, allvhdsje est le nom de compte de votre compte de stockage, storageaccountkey est la clé de votre compte de stockage et rdma est le nom du partage de fichiers Azure. Le partage de fichiers Azure est monté en tant que Z: sur le nœud principal.

Pour monter le partage Azure File sur des nœuds Linux, exécutez une commande **clusrun** sur le nœud principal. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** est un outil HPC Pack utile pour effectuer des tâches d’administration sur plusieurs nœuds. (Voir aussi [Clusrun pour les nœuds Linux](#Clusrun-for-Linux-nodes) dans cet article.)

Ouvrez une fenêtre Windows PowerShell et entrez les commandes suivantes :

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

La première commande crée un dossier nommé /rdma sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande monte le partage Azure File allvhdsjw.file.core.windows.net/rdma sur le dossier /rdma avec les bits de mode de répertoire et de fichier définis sur 777. Dans la deuxième commande, allvhdsje est le nom de votre compte de stockage et storageaccountkey est la clé de votre compte de stockage.

> [!NOTE]
> Le symbole « \` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « \`, » signifie que « , » (une virgule) fait partie de la commande.
> 
> 

### <a name="head-node-share"></a>Partage du nœud principal
Il est également possible de monter un dossier partagé du nœud principal sur les nœuds Linux. Un partage constitue la façon la plus simple de partager des fichiers mais, dans ce cas, le nœud principal et tous les nœuds Linux doivent être déployés dans le même réseau virtuel. Voici la procédure à suivre.

1. Créez un dossier sur le nœud principal et partagez-le avec Tout le monde, avec des autorisations en lecture/écriture. Par exemple, partagez D:\OpenFOAM sur le nœud principal en tant que \\CentOS7RDMA-HN\OpenFOAM. Ici, CentOS7RDMA-HN est le nom d’hôte du nœud principal.
   
    ![Autorisations des partages de fichiers][fileshareperms]
   
    ![Partage de fichiers][filesharing]
2. Ouvrez une fenêtre Windows PowerShell, puis exécutez les commandes suivantes :
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /openfoam sur tous les nœuds du groupe LinuxNodes. La deuxième commande monte le dossier partagé //CentOS7RDMA-HN/OpenFOAM sur le dossier avec les bits de mode de répertoire et de fichier définis sur 777. Le nom d’utilisateur et le mot de passe dans la commande doivent être le nom d’utilisateur et le mot de passe d’un utilisateur du cluster sur le nœud principal. (Consultez la page [Add or Remove Cluster Users](https://technet.microsoft.com/library/ff919330.aspx)(Ajouter ou supprimer des utilisateurs du cluster).)

> [!NOTE]
> Le symbole « \` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « \`, » signifie que « , » (une virgule) fait partie de la commande.
> 
> 

### <a name="nfs-server"></a>Serveur NFS
Le service NFS vous permet de partager et de migrer des fichiers entre des ordinateurs exécutant le système d’exploitation Windows Server 2012 utilisant le protocole SMB et des ordinateurs Linux utilisant le protocole NFS. Le serveur NFS et tous les autres nœuds doivent être déployés dans le même réseau virtuel. Il offre une meilleure compatibilité avec les nœuds Linux en comparaison d’un partage SMB. Par exemple, il prend en charge les liaisons de fichiers.

1. Pour installer et configurer un serveur NFS, suivez les étapes indiquées dans [Server for Network File System First Share End-to-End](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx)(en anglais).
   
    Par exemple, créez un partage NFS nommé nfs avec les propriétés suivantes :
   
    ![Autorisation NFS][nfsauth]
   
    ![Autorisations des partages NFS][nfsshare]
   
    ![Autorisations NTFS NFS][nfsperm]
   
    ![Propriétés de la gestion NFS][nfsmanage]
2. Ouvrez une fenêtre Windows PowerShell, puis exécutez les commandes suivantes :
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   La première commande crée un dossier nommé /nfsshared sur tous les nœuds du groupe LinuxNodes. La deuxième commande monte le partage NFS CentOS7RDMA-HN:/nfs sur le dossier. Ici, CentOS7RDMA-HN:/nfs est le chemin d’accès distant de votre partage NFS.

## <a name="how-to-submit-jobs"></a>Comment soumettre des travaux
Il existe plusieurs façons de soumettre des travaux au cluster HPC Pack.

* Gestionnaire de cluster HPC ou interface graphique utilisateur du Gestionnaire de travaux HPC
* Portail web HPC
* API REST

La soumission de travaux au cluster via les outils de l’interface graphique utilisateur HPC Pack et via le portail web HPC est la même que pour les nœuds de calcul Windows. Consultez [HPC Pack Job Manager](https://technet.microsoft.com/library/ff919691.aspx) (Gestionnaire de travaux HPC Pack) et [Envoyer des travaux HPC à partir d’un ordinateur local vers un cluster HPC Pack déployé dans Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour soumettre des travaux via l’API REST, consultez [Création et soumission de travaux à l’aide de l’API REST dans Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Pour soumettre des travaux à partir d’un client Linux, voir également l’exemple Python dans le [Kit de développement logiciel (SDK) HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun pour les nœuds Linux
L’outil [clusrun](https://technet.microsoft.com/library/cc947685.aspx) de HPC Pack permet d’exécuter des commandes sur des nœuds Linux via une invite de commande ou le gestionnaire de cluster HPC. Voici quelques exemples de base.

* Afficher les noms des utilisateurs actuels de tous les nœuds du cluster
  
    ```command
    clusrun whoami
    ```
* Installez l’outil de débogage **gdb** avec **yum** sur tous les nœuds du groupe linuxnodes, puis redémarrez les nœuds après 10 minutes.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Créez un script shell affichant chaque nombre de 1 à 10 pendant une seconde sur chaque nœud du cluster Linux, exécutez-le puis affichez la sortie des nœuds immédiatement.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Il peut être nécessaire d’utiliser certains caractères d’échappement dans les commandes **clusrun** . Comme indiqué dans cet exemple, utilisez ^ dans une invite de commandes comme caractère d’échappement pour le symbole « > ».
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Essayez de mettre à l’échelle du cluster à un plus grand nombre de nœuds, ou exécutez une charge de travail Linux en sur le cluster. Pour trouver un exemple, consultez [Exécuter NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](hpcpack-cluster-namd.md).
* Essayez un cluster avec [des machines virtuelles gourmandes en ressources de calcul et prenant en charge RDMA](../../virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour exécuter des charges de travail MPI. Pour trouver un exemple, consultez [Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](hpcpack-cluster-openfoam.md).
* Si vous êtes intéressé par l’utilisation de nœuds Linux dans un cluster HPC Pack local, consultez les [conseils TechNet](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png

