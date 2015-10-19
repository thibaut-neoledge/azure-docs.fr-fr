<properties
 pageTitle="Machines virtuelles de calcul Linux dans un cluster HPC Pack | Microsoft Azure"
 description="Comment créer un script pour le déploiement d'un cluster HPC Pack dans Azure, contenant un nœud principal exécutant Windows Server et avec des nœuds de calcul Linux."
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
 ms.date="09/01/2015"
 ms.author="danlep"/>

# Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure

Cet article vous montre comment utiliser un script Azure PowerShell pour configurer un cluster Microsoft HPC Pack dans Azure qui contient un nœud principal exécutant Windows Server et plusieurs nœuds de calcul exécutant une distribution Linux CentOS. Nous montrons également plusieurs façons de déplacer des fichiers de données vers des nœuds de calcul Linux. Vous pouvez utiliser ce cluster pour exécuter des charges de travail Linux HPC dans Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement classique.

Le diagramme général suivant montre le cluster HPC Pack que vous allez créer.

![Cluster HPC avec des nœuds Linux][scenario]

## Déployer un cluster HPC Pack avec des nœuds de calcul Linux

Vous allez utiliser le script de déploiement IaaS de Microsoft HPC Pack (**New-HpcIaaSCluster.ps1**) pour automatiser le déploiement de cluster dans les services d'infrastructure Azure (IaaS). Ce script Azure PowerShell utilise une image de machine virtuelle HPC Pack d’Azure Marketplace pour un déploiement rapide, et fournit un ensemble complet de paramètres de configuration pour rendre le déploiement facile et flexible. Le script déploie le réseau virtuel Azure, les comptes de stockage, les services cloud, le contrôleur de domaine, le serveur de base de données SQL Server distinct facultatif, le nœud principal du cluster, les nœuds de calcul, les nœuds de service Broker, les nœuds Azure PaaS (« rafale ») et les nœuds de calcul Linux (support Linux introduit dans [HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx)).

Pour une vue d'ensemble des options de déploiement de cluster HPC Pack, consultez le [Guide de prise en main pour HPC Pack 2012 R2 et HPC Pack 2012](https://technet.microsoft.com/library/jj884144.aspx).

### Composants requis

* **Ordinateur client** : vous aurez besoin d'un ordinateur client Windows pour exécuter le script de déploiement de cluster.

* **Azure PowerShell** : [installez et configurez Azure PowerShell](../powershell-install-configure.md) (version 0.8.10 ou ultérieure) sur votre ordinateur client.

* **Script de déploiement IaaS de HPC Pack** : téléchargez et décompressez la dernière version du script à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Vous pouvez vérifier la version du script en exécutant `New-HPCIaaSCluster.ps1 –Version`. Cet article est basé sur la version 4.4.0 ou ultérieure du script.

* **Abonnement Azure** : vous pouvez utiliser un abonnement dans le service Azure Global ou Azure Chine. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs, en particulier si vous choisissez de déployer plusieurs nœuds de cluster avec des tailles de machines virtuelles multiprocesseurs. Par l’exemple de cet article, vous aurez besoin d’au moins 24 cœurs. Pour augmenter un quota, [ouvrez une demande de support client en ligne](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.

### Créer le fichier de configuration
Le script de déploiement de HPC Pack IaaS utilise un fichier de configuration XML comme entrée, qui décrit l’infrastructure du cluster HPC. Pour déployer un petit cluster constitué d’un nœud principal et de deux nœuds de calcul Linux, remplacez les valeurs par celles convenant à votre environnement dans l’exemple de fichier de configuration suivant. Pour plus d'informations sur le fichier de configuration, consultez le fichier Manual.rtf dans le dossier de script et la rubrique [Créer un cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md).

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
  <VMSize>A4</VMSize>
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

Voici une brève description des éléments du fichier de configuration.

* **IaaSClusterConfig** : élément racine du fichier de configuration.

* **Subscription** : abonnement Azure utilisé pour déployer le cluster HPC Pack. Utilisez la commande ci-dessous pour vous assurer que le nom de l’abonnement Azure est configuré et unique dans votre ordinateur client. Dans cet exemple, nous utilisons l’abonnement Azure « Subscription-1 ».

    ```
    PS > Get-AzureSubscription –SubscriptionName <SubscriptionName>
    ```

    >[AZURE.NOTE]Vous pouvez aussi utiliser l’ID d’abonnement pour spécifier l’abonnement que vous voulez utiliser. Consultez le fichier Manual.rtf dans le dossier de script.

* **StorageAccount** : toutes les données persistantes du cluster HPC Pack seront stockées dans le compte de stockage spécifié (allvhdsje dans cet exemple). Si le compte de stockage n'existe pas, le script le crée dans la région spécifiée dans **Location**.

* **Location** : région Azure où vous allez déployer le cluster HPC Pack (Est du Japon dans cet exemple).

* **VNet** : paramètres du réseau virtuel et du sous-réseau où le cluster HPC sera créé. Vous pouvez créer le réseau virtuel et le sous-réseau vous-même avant d’exécuter ce script. Sinon, le script crée un réseau virtuel avec l’espace d’adressage 192.168.0.0/20 et le sous-réseau avec l’espace d’adressage 192.168.0.0/23. Dans cet exemple, le script crée le réseau virtuel centos7rdmavnetje et le sous-réseau CentOS7RDMACluster.

* **Domain** : paramètres du domaine Active Directory pour le cluster HPC Pack. Toutes les machines virtuelles Windows créées par le script seront jointes au domaine. Actuellement, le script prend en charge trois options de domaine : ExistingDC, NewDC et HeadNodeAsDC. Dans cet exemple, nous allons configurer le nœud principal comme contrôleur de domaine avec le nom de domaine complet hpc.local.

* **Database** : paramètres de base de données pour le cluster HPC Pack. Actuellement, le script prend en charge trois options de base de données : ExistingDB, NewRemoteDB et LocalDB. Dans cet exemple, nous allons créer une base de données locale sur le nœud principal.

* **HeadNode** : paramètres du nœud principal HPC Pack. Dans cet exemple, nous allons créer un nœud principal de taille A7 nommé CentOS7RDMA-HN dans le service cloud centos7rdma-je. Pour prendre en charge la soumission de travaux HPC effectuée depuis des ordinateurs clients distants (non joints à un domaine), le script activera l’API REST du planificateur de travaux HPC et le portail web HPC.

* **LinuxComputeNodes** : paramètres des nœuds de calcul Linux du HPC Pack. Dans cet exemple, nous allons créer deux nœuds de calcul Linux CentOS 7 de taille A7 (CentOS7RDMA-LN1 et CentOS7RDMA-LN2) dans le service cloud centos7rdma-je.

### Considérations supplémentaires pour les nœuds de calcul Linux

* Actuellement HPC Pack prend en charge les distributions Linux suivantes pour les nœuds de calcul : Ubuntu Server 14.10, CentOS 6.6, CentOS 7.0 et SUSE Linux Enterprise Server 12.

* L’exemple de cet article utilise une version spécifique de CentOS disponible dans Azure Marketplace pour créer le cluster. Pour utiliser d'autres images disponibles, utilisez l'applet de commande Azure PowerShell **get-azurevmimage** pour trouver celle dont vous avez besoin. Par exemple, pour répertorier toutes les images CentOS 7.0, exécutez la commande suivante : ```
    get-azurevmimage | ?{$_.Label -eq "OpenLogic 7.0"}
    ```

    Recherchez l'image dont vous avez besoin et remplacez la valeur **ImageName** dans le fichier de configuration.

* Des images Linux qui prennent en charge la connectivité RDMA pour les machines virtuelles de taille A8 et A9 sont disponibles. Si vous spécifiez une image avec des pilotes Linux RDMA installés et activés, le script de déploiement HPC Pack IaaS les déploiera. Par exemple, spécifiez le nom d'image `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` pour SUSE Linux Enterprise Server 12, qui est optimisée pour l'image HPC dans le Marketplace.

* Pour activer Linux RDMA sur des machines virtuelles Linux créées à partir d’images prises en charge pour exécuter des travaux MPI, installez et configurez une bibliothèque MPI spécifique sur les nœuds Linux après le déploiement du cluster en fonction des besoins de votre application. Pour plus d'informations sur l'utilisation de RDMA dans les nœuds Linux sur Azure, consultez [Configurer un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-cluster-rdma.md).

* Assurez-vous de déployer tous les nœuds Linux RDMA dans un même service afin que la connexion réseau RDMA fonctionne entre les nœuds.


## Exécuter le script de déploiement du HPC Pack IaaS

1. Ouvrez la console PowerShell sur l’ordinateur client en tant qu’administrateur.

2. Accédez au dossier de script (E:\\IaaSClusterScript dans cet exemple).

    ```
    cd E:\IaaSClusterScript
    ```

3. Exécutez la commande suivante pour déployer le cluster HPC Pack. Cet exemple suppose que le fichier de configuration se trouve dans E:\\HPCDemoConfig.xml.

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

    Le script génère un fichier journal automatiquement, car le paramètre **-LogFile** n'est pas spécifié. Les journaux ne sont pas écrits en temps réel, mais ils sont collectés à la fin de la validation et du déploiement : si le processus PowerShell est arrêté pendant l’exécution du script, certains journaux seront donc perdus.

    a. Étant donné que le paramètre **AdminPassword** n'est pas spécifié dans la commande ci-dessus, vous serez invité à entrer le mot de passe pour l'utilisateur *MyAdminName*.

    b. Le script commence ensuite à valider le fichier de configuration. Selon la connexion réseau, cette opération peut durer de quelques dizaines de secondes à plusieurs minutes.

    ![Validation][validate]

    c. Une fois les validations réussies, le script répertorie les ressources qui seront créées pour le cluster HPC. Tapez *Y* pour continuer.

    ![Ressources][resources]

    d. Le script commence ensuite à déployer le cluster HPC Pack, puis termine la configuration sans autres étapes manuelles. Ceci peut prendre plusieurs minutes.

    ![Déploiement][deploy]

4. Une fois la configuration terminée, connectez-vous via le Bureau à distance au nœud principal et ouvrez le gestionnaire de cluster HPC pour vérifier l’état du cluster HPC Pack. Vous pouvez gérer et surveiller les nœuds de calcul Linux de la même façon que les nœuds de calcul Windows. Par exemple, vous voyez les nœuds Linux répertoriés dans la gestion des nœuds.

    ![Gestion des nœuds][management]

    Vous voyez aussi les nœuds Linux dans la vue Carte thermique.

    ![Carte thermique][heatmap]

## Comment déplacer des données dans un cluster de nœuds Linux

Vous avez plusieurs possibilités pour déplacer des données entre des nœuds de Linux et le nœud principal Windows du cluster. Voici trois méthodes courantes.

* **Azure File** : expose un partage de fichiers pour stocker les fichiers de données dans le stockage Azure. Les nœuds Windows et les nœuds Linux peuvent monter un partage Azure File comme un lecteur ou un dossier en même temps, même s’ils sont déployés dans des réseaux virtuels différents.

* **Head node SMB share** : monte un dossier partagé du nœud principal sur des nœuds Linux.

* **Head node NFS server** : fournit une solution de partage de fichiers pour un environnement mixte Windows et Linux.

### Azure File

Le service [Azure File](https://azure.microsoft.com/services/storage/files/) expose les partages de fichiers en utilisant le protocole SMB 2.1 standard. Les machines virtuelles et les services cloud Azure peuvent partager les données des fichiers entre plusieurs composants d’application grâce à des partages montés. Les applications locales peuvent accéder aux données des fichiers d’un partage via l’API de stockage de fichiers. Pour plus d'informations, consultez [Utilisation du stockage Azure File avec PowerShell et .NET](../storage/storage-dotnet-how-to-use-files.md).

Pour créer un partage Azure File, consultez les étapes détaillées dans [Présentation du Service Microsoft Azure File](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx). Pour configurer des connexions persistantes, consultez [Connexions persistantes à Microsoft Azure File](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

Dans cet exemple, nous créons un partage Azure File nommé rdma sur notre compte de stockage allvhdsje. Pour monter le partage sur le nœud principal, nous ouvrons une fenêtre Commande et nous entrons les commandes suivantes :

```
> cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>
> net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

Dans cet exemple, allvhdsje est le nom de compte de stockage, storageaccountkey est la clé du compte de stockage et rdma est le nom du partage Azure File. Le partage Azure File est monté sur Z: sur votre nœud principal.

Pour monter le partage Azure File sur des nœuds Linux, exécutez une commande **clusrun** sur le nœud principal. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** est un outil HPC Pack utile pour effectuer des tâches d'administration sur plusieurs nœuds. (Voir aussi [CLusrun pour les nœuds Linux](#CLusrun-for-Linux-nodes) dans cet article.)

Ouvrez une fenêtre Windows PowerShell et entrez les commandes suivantes.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /rdma

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

La première commande crée un dossier nommé /rdma sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande monte le partage Azure File allvhdsjw.file.core.windows.net/rdma sur le dossier /rdma avec les bits de mode de répertoire et de fichier définis sur 777. Dans la deuxième commande, allvhdsje est le nom de votre compte de stockage et storageaccountkey est la clé de votre compte de stockage.

>[AZURE.NOTE]Le symbole « ` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « `, » signifie que « , » (une virgule) est une partie de la commande.

### Partage du nœud principal

Vous pouvez également monter un dossier partagé du nœud principal sur les nœuds Linux. C’est la façon la plus simple de partager des fichiers, mais dans ce cas, le nœud principal et tous les nœuds Linux doivent être déployés dans le même réseau virtuel. Voici la procédure à suivre.

1. Créez un dossier sur le nœud principal et partagez-le avec Tout le monde, avec des autorisations en lecture/écriture. Par exemple, nous partageons D:\\OpenFOAM sur le nœud principal en tant que \\CentOS7RDMA-HN\\OpenFOAM. Ici, CentOS7RDMA-HN est le nom d’hôte de notre nœud principal.

    ![Autorisations des partages de fichiers][fileshareperms]

    ![Partage de fichiers][filesharing]

2. Ouvrez une fenêtre Windows PowerShell et exécutez les commandes suivantes pour monter le dossier partagé.

```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

PS > clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
```

La première commande crée un dossier nommé /openfoam sur tous les nœuds du groupe LinuxNodes. La deuxième commande monte le dossier partagé //CentOS7RDMA-HN/OpenFOAM sur le dossier avec les bits de mode de répertoire et de fichier définis sur 777. Le nom d’utilisateur et le mot de passe dans la commande doivent être le nom d’utilisateur et le mot de passe d’un utilisateur du cluster sur le nœud principal.

>[AZURE.NOTE]Le symbole « ` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « `, » signifie que « , » (une virgule) est une partie de la commande.


### Serveur NFS

Le service NFS permet aux utilisateurs de partager et de migrer des fichiers entre des ordinateurs exécutant le système d’exploitation Windows Server 2012 utilisant le protocole SMB et des ordinateurs Linux utilisant le protocole NFS. Le serveur NFS et tous les autres nœuds doivent être déployés dans le même réseau virtuel. Il offre une meilleure compatibilité avec les nœuds Linux en comparaison d’un partage SMB ; par exemple, il prend en charge la liaison de fichiers.

1. Pour installer et configurer un serveur NFS, suivez les étapes indiquées dans [Server for Network File System First Share End-to-End](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx) (en anglais).

    Par exemple, créez un partage NFS nommé nfs avec les propriétés suivantes.

    ![Autorisation NFS][nfsauth]

    ![Autorisations des partages NFS][nfsshare]

    ![Autorisations NTFS NFS][nfsperm]

    ![Propriétés de la gestion NFS][nfsmanage]

2. Ouvrez une fenêtre Windows PowerShell et exécutez les commandes suivantes pour monter le partage NFS.

    ```
PS > clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
PS > clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
```

    La première commande crée un dossier nommé /nfsshared sur tous les nœuds du groupe LinuxNodes. La deuxième commande monte le partage NFS CentOS7RDMA-HN:/nfs sur le dossier. Ici, CentOS7RDMA-HN:/nfs est le chemin d’accès distant de votre partage NFS.

## Comment soumettre des travaux
Il existe plusieurs façons de soumettre des travaux au cluster HPC Pack.

* Gestionnaire de cluster HPC ou interface graphique utilisateur du Gestionnaire de travaux HPC

* Portail web HPC

* API REST

La soumission de travaux au cluster via les outils de l’interface graphique utilisateur HPC Pack et via le portail web HPC est la même que pour les nœuds de calcul Windows. Consultez [Gestionnaire de travaux HPC Pack](https://technet.microsoft.com/library/ff919691.aspx) et [Soumission de travaux à partir d'un client local](https://msdn.microsoft.com/library/azure/dn689084.aspx).

Pour soumettre des travaux via l'API REST, consultez [Création et soumission de travaux à l'aide de l'API REST dans Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). Reportez-vous également à l'exemple Python dans le [Kit de développement logiciel (SDK) HPC Pack](https://www.microsoft.com/download/details.aspx?id=47756) pour soumettre des travaux à partir d'un client Linux.

## Clusrun pour les nœuds Linux

L'outil **clusrun** de HPC Pack permet d'exécuter des commandes sur des nœuds Linux via une fenêtre Command ou le gestionnaire de cluster HPC. Voici quelques exemples.

* Afficher les noms des utilisateurs actuels de tous les nœuds du cluster

    ```
    > clusrun whoami
    ```

* Installez l'outil de débogage **gdb** avec **yum** sur tous les nœuds du groupe linuxnodes, puis redémarrez les nœuds après 10 minutes.

    ```
    > clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```

* Créez un script shell affichant chaque nombre de 1 à 10 pour une seconde sur chaque nœud du cluster, exécutez-le et affichez la sortie des nœuds immédiatement.

    ```
    > clusrun /interleaved echo "for i in {1..10}; do echo \\"\$i\\"; sleep 1; done" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

>[AZURE.NOTE]Il peut être nécessaire d'utiliser certains caractères d'échappement dans les commandes **clusrun**. Comme indiqué dans cet exemple, utilisez ^ dans une fenêtre de commande comme échappement pour le symbole « > ».

## Étapes suivantes

* Essayez d’exécuter une charge de travail Linux sur le cluster. Pour un exemple, consultez [Exécuter NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-cluster-hpcpack-namd.md).

* Essayez d'étendre le cluster à un plus grand nombre de nœuds ou déployez des nœuds de calcul de taille [A8 ou A9](virtual-machines-a8-a9-a10-a11-specs.md) pour exécuter des charges de travail MPI.

* Essayez un [modèle de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/) avec Azure Resource Manager pour accélérer les déploiements de HPC Pack avec un plus grand nombre de nœuds de calcul Linux.

<!--Image references-->
[scenario]: ./media/virtual-machines-linux-cluster-hpcpack/scenario.png
[validate]: ./media/virtual-machines-linux-cluster-hpcpack/validate.png
[resources]: ./media/virtual-machines-linux-cluster-hpcpack/resources.png
[deploy]: ./media/virtual-machines-linux-cluster-hpcpack/deploy.png
[management]: ./media/virtual-machines-linux-cluster-hpcpack/management.png
[heatmap]: ./media/virtual-machines-linux-cluster-hpcpack/heatmap.png
[fileshareperms]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare1.png
[filesharing]: ./media/virtual-machines-linux-cluster-hpcpack/fileshare2.png
[nfsauth]: ./media/virtual-machines-linux-cluster-hpcpack/nfsauth.png
[nfsshare]: ./media/virtual-machines-linux-cluster-hpcpack/nfsshare.png
[nfsperm]: ./media/virtual-machines-linux-cluster-hpcpack/nfsperm.png
[nfsmanage]: ./media/virtual-machines-linux-cluster-hpcpack/nfsmanage.png

<!---HONumber=Oct15_HO2-->