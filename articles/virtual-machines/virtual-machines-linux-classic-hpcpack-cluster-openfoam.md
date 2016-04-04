<properties
 pageTitle="Exécuter OpenFOAM avec HPC Pack sur des machines virtuelles Linux | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et exécuter un travail OpenFOAM sur plusieurs nœuds de calcul RDMA."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="11/25/2015"
 ms.author="danlep"/>

# Exécuter OpenFoam avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure

Cet article explique comment déployer un cluster Microsoft HPC Pack sur Azure et exécuter une tâche [OpenFoam](http://openfoam.com/) avec Intel MPI sur plusieurs nœuds de calcul Linux se connectant par le biais d’un réseau RDMA (accès direct à la mémoire à distance Azure).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

OpenFOAM (acronyme d’Open Field Operation and Manipulation) est un logiciel open source de mécanique des fluides numérique largement utilisé dans l’ingénierie et les sciences, aussi bien dans les organisations commerciales que les organismes universitaires. Il comprend des outils de maillage, notamment snappyHexMesh, un meilleur parallélisé pour les géométries de CAO complexes et de pré et post-traitement. Presque tous les processus s’exécutent en parallèle, permettant aux utilisateurs de tirer le meilleur parti du matériel informatique à leur disposition.

Microsoft HPC Pack fournit des fonctionnalités permettant d’exécuter un éventail d’applications HPC à grande échelle et parallèles, y compris des applications MPI, sur des clusters de machines virtuelles Microsoft Azure. À partir de Microsoft HPC Pack 2012 R2 Update 2, HPC Pack prend également en charge l’exécution d’applications HPC Linux sur les machines virtuelles de nœuds de calcul Linux déployées dans un cluster HPC Pack. Voir [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour une présentation de l’utilisation des nœuds de calcul Linux avec HPC Pack.

>[AZURE.NOTE] Cet article suppose que vous êtes familiarisé avec l’administration du système Linux et l’exécution de charges de travail MPI sur des clusters HPC Linux.

## Composants requis

*   **Cluster HPC Pack avec nœuds de calcul Linux** : voir [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md) pour connaître la configuration requise et les étapes de déploiement d’un cluster HPC Pack avec les nœuds de calcul Linux dans Azure à l’aide d’un script Azure PowerShell et d’images HPC Pack dans Azure Marketplace. Pour obtenir des informations supplémentaires sur l’utilisation d’instances A8 nécessitant beaucoup de ressources système pour accéder au réseau Azure RDMA, consultez la section [À propos d’instances pour calcul intensif sur A8, A9, A10 et A11](virtual-machines-windows-a8-a9-a10-a11-specs.md).

    Voici un exemple de fichier de configuration XML à utiliser avec le script pour déployer un cluster HPC Pack basé sur Azure composé d’un nœud principal Windows Server 2012 R2 A8 et de 2 grands nœuds de calcul A8 SUSE Linux Enterprise Server 12. Remplacez les valeurs appropriées pour les noms de vos abonnement et service.

    >[AZURE.NOTE]Actuellement, les réseaux RDMA Linux dans Azure sont pris en charge uniquement sur les machines virtuelles créées à partir de l’image SUSE Linux Enterprise Server 12 compatible RDMA dans Azure Marketplace (b4590d9e3ed742e4a1d46e5424aa335e\_\_suse-sles-12-hpc-v20150708).

    ```
    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionName>Subscription-1</SubscriptionName>
        <StorageAccount>allvhdsje</StorageAccount>
      </Subscription>
      <Location>Japan East</Location>  
      <VNet>
        <VNetName>suse12rdmavnet</VNetName>
        <SubnetName>SUSE12RDMACluster</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpclab.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>SUSE12RDMA-HN</VMName>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <EnableRESTAPI />
        <EnableWebPortal />
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>SUSE12RDMA-LN%1%</VMNamePattern>
        <ServiceName>suse12rdma-je</ServiceName>
        <VMSize>A8</VMSize>
        <NodeCount>2</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>
```

    **Autres informations à connaître**

    *   Déployez tous les nœuds de calcul Linux au sein d’un service cloud pour utiliser la connexion réseau RDMA entre les nœuds.

    *   Après avoir déployé les nœuds Linux, si vous devez vous connecter par SSH pour effectuer certaines tâches administratives supplémentaires, détectez les détails de connexion SSH pour chaque machine virtuelle Linux du portail Azure.
        
*   **Intel MPI** : pour exécuter OpenFOAM sur des nœuds de calcul Linux dans Azure, vous devez disposer du runtime Intel MPI Library 5 que vous trouverez sur le [site Intel.com](https://software.intel.com/fr-FR/intel-mpi-library/). Vous devez ensuite installer Intel MPI sur vos nœuds de calcul Linux. Pour vous y préparer, après votre inscription auprès d'Intel, suivez le lien dans le message de confirmation vers la page web associée et copiez le lien de téléchargement du fichier .tgz pour la version appropriée d'Intel MPI. Cet article est basé sur Intel MPI version 5.0.3.048.

*   **OpenFOAM Source Pack** : téléchargez le logiciel OpenFOAM Source Pack pour Linux à partir du [site d’OpenFOAM Foundation](http://www.openfoam.org/download/source.php). Cet article est basé sur la version du Pack Source 2.3.1, disponible en téléchargement en tant que OpenFOAM 2.3.1.tgz. Suivez les instructions présentes plus loin dans cet article pour décompresser et compiler OpenFOAM sur les nœuds de calcul Linux.

*   **EnSight** (facultatif) : pour afficher les résultats de votre simulation OpenFOAM, téléchargez et installez la version Windows du programme de visualisation et d’analyse [EnSight](https://www.ceisoftware.com/download/) sur le nœud principal du cluster HPC Pack. Les informations de licence et de téléchargement se trouvent sur le site EnSight.


## Configuration de l’approbation mutuelle entre les nœuds de calcul

L'exécution d'une tâche de nœuds croisés sur plusieurs nœuds Linux requiert une approbation mutuelle entre les nœuds (par **rsh** ou **ssh**). Lorsque vous créez le cluster HPC Pack avec le script de déploiement IaaS Microsoft HPC Pack, le script définit automatiquement l’approbation mutuelle permanente pour le compte administrateur que vous spécifiez. Pour les utilisateurs non administrateurs que vous créez dans le domaine du cluster, vous devez configurer l’approbation mutuelle temporaire entre les nœuds lorsqu’une tâche leur est allouée, puis détruire la relation une fois la tâche terminée. Pour ce faire, pour chaque utilisateur, fournissez une paire de clés RSA au cluster que HPC Pack utilise pour établir la relation d’approbation.

### Génération d’une paire de clés RSA

Générer une paire de clés RSA, contenant une clé publique et une clé privée, est facile : il vous suffit d'exécuter la commande Linux **ssh-keygen**.

1.	Ouvrez une session sur un ordinateur Linux.

2.	Exécutez la commande ci-dessous.

    ```
    ssh-keygen -t rsa
    ```

    >[AZURE.NOTE] Appuyez sur **Entrée** pour utiliser les paramètres par défaut jusqu'à ce que la commande soit terminée. Ne saisissez pas de phrase secrète ici. À l’invite de mot de passe, appuyez simplement sur **Entrée**.

    ![Génération d’une paire de clés RSA][keygen]

3.	Remplacez le répertoire par le répertoire ~/.ssh. La clé privée est stockée dans id\_rsa et la clé publique dans id\_rsa.pub.

    ![Clés publiques et privées][keys]

### Ajout de la paire de clés au cluster HPC Pack
1.	Effectuez une connexion Bureau à distance à votre nœud principal avec votre compte administrateur HPC Pack (le compte administrateur que vous avez configuré lorsque vous avez exécuté le script de déploiement).

2. Utilisez les procédures standard Windows Server pour créer un compte d’utilisateur de domaine dans le domaine Active Directory du cluster. Par exemple, utilisez l’outil Utilisateurs et ordinateurs Active Directory sur le nœud principal. Les exemples de cet article supposent que vous créez un utilisateur de domaine nommé hpclab\\hpcuser.

3.	Créez un fichier nommé C:\\cred.xml et copiez-y les données de clé RSA. Vous trouverez un exemple de ce fichier à la fin de cet article.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

4.	Ouvrez une invite de commande et entrez la commande suivante pour définir les données d’informations d’identification du compte hpclab\\hpcuser. Utilisez le paramètre **extendeddata** pour transmettre le nom du fichier C:\\cred.xml que vous avez créé pour les données de clé.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Cette commande se termine correctement sans sortie. Après avoir défini les informations d’identification pour les comptes d’utilisateurs dont vous avez besoin pour exécuter des tâches, stockez le fichier cred.xml à un emplacement sécurisé ou supprimez-le.

5.	Si vous avez généré la paire de clés RSA sur l’un de vos nœuds Linux, pensez à supprimer les clés une fois que vous avez terminé de les utiliser. HPC Pack ne configure pas l’approbation mutuelle s’il trouve des fichiers id\_rsa ou id\_rsa.pub existants.

>[AZURE.IMPORTANT] Nous déconseillons d’exécuter une tâche Linux en tant qu’administrateur de cluster sur un cluster partagé, car une tâche envoyée par un administrateur s’exécute sous le compte racine sur les nœuds Linux. Une tâche envoyée par un utilisateur non administrateur s’exécute sous un compte d’utilisateur Linux local avec le même nom que l’utilisateur de la tâche, et HPC Pack définit l’approbation mutuelle pour cet utilisateur Linux sur tous les nœuds alloués à la tâche. Vous pouvez configurer l’utilisateur Linux manuellement sur les nœuds Linux avant d’exécuter la tâche, ou bien HPC Pack crée automatiquement l’utilisateur lorsque la tâche est envoyée. Si HPC Pack crée l’utilisateur, il se charge de le supprimer une fois la tâche terminée. Les clés sont supprimées après l’achèvement de la tâche sur les nœuds afin de réduire les menaces de sécurité.

## Configuration d’un partage de fichiers pour les nœuds Linux

À présent, configurez un partage SMB standard sur un dossier sur le nœud principal et montez le dossier partagé sur tous les nœuds Linux pour leur permettre d’accéder aux fichiers d’application avec un chemin d’accès commun. Si vous le souhaitez, vous pouvez utiliser une autre option de partage de fichier telle que le partage de fichiers Azure (recommandé dans de nombreux scénarios) ou un partage NFS. Consultez les étapes détaillées et les informations de partage de fichiers dans la section [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

1.	Créez un dossier sur le nœud principal et partagez-le avec tout le monde en définissant des privilèges de lecture/écriture. Par exemple, partagez D:\\OpenFOAM sur le nœud principal en tant que \\\SUSE12RDMA-HN\\OpenFOAM. Ici, *SUSE12RDMA-HN* est le nom d’hôte du nœud principal.

2.	Ouvrez une fenêtre Windows PowerShell et exécutez les commandes suivantes pour monter le dossier partagé.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam

    clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>’`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /openfoam sur tous les nœuds du groupe LinuxNodes. La deuxième commande monte le dossier partagé //SUSE12RDMA-HN/OpenFOAM sur les nœuds avec les bits dir\_mode et file\_mode définis sur 777. Les valeurs *username* et *password* dans la commande doivent être les informations d’identification d’un utilisateur du nœud principal.

>[AZURE.NOTE]Le symbole « ` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « `, » signifie que « , » (une virgule) est une partie de la commande.

## Installer MPI et OpenFOAM

Pour exécuter OpenFOAM en tant que travail MPI sur le réseau RDMA, vous devez compiler OpenFOAM avec les bibliothèques Intel MPI.

Vous allez tout d’abord exécuter plusieurs commandes **clusrun** pour installer les bibliothèques Intel MPI et OpenFOAM sur tous vos nœuds Linux. Utiliser le partage de nœud principal configuré précédemment pour partager les fichiers d’installation sur les nœuds Linux.

>[AZURE.IMPORTANT]Ces opérations d’installation et de compilation constituent des exemples et exigent quelques connaissances en matière d’administration du système Linux, en particulier lorsqu’il s’agit de s’assurer que les bibliothèques et les compilateurs dépendants sont correctement installés. Vous devrez peut-être modifier certaines variables d’environnement ou d’autres paramètres nécessaires à vos versions d’Intel MPI et OpenFOAM. Pour plus de détails, consultez le [Guide d’installation de la bibliothèque Intel MPI pour Linux](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html) et [Installation d’OpenFOAM Source Pack](http://www.openfoam.org/download/source.php).


### Installer Intel MPI

Enregistrez le package d’installation téléchargé pour Intel MPI (l\_mpi\_p\_5.0.3.048.tgz dans cet exemple) dans C:\\OpenFoam sur le nœud principal afin que les nœuds Linux puissent accéder à ce fichier à partir de /openfoam. Exécutez ensuite **clusrun** pour installer la bibliothèque Intel MPI sur tous les nœuds Linux.

1.  Les commandes suivantes copient le package d’installation et l’extraient dans le répertoire /opt/intel de chacun des nœuds.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel

    clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
    ```

2.  Pour installer la bibliothèque Intel MPI en mode silencieux, utilisez un fichier silent.cfg. Vous trouverez un exemple dans les exemples de fichiers à la fin de cet article. Placez ce fichier dans le dossier partagé /openfoam. Pour plus de détails sur le fichier silent.cfg, consultez le [Guide d’installation de la bibliothèque MPI Intel pour Linux - Installation silencieuse](http://scc.ustc.edu.cn/zlsc/tc4600/intel/impi/INSTALL.html#silentinstall).

    >[AZURE.TIP]Assurez-vous que vous enregistrez votre fichier .cfg silencieux en tant que fichier texte avec des fins de ligne Linux (LF uniquement, et non CR LF). Cela garantit qu’il s’exécute correctement sur les nœuds Linux.

3.  Installez la bibliothèque MPI Intel en mode silencieux.
 
    ```
    clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
    ```
    
### Configuration de MPI

Pour le test, vous devez ajouter les lignes suivantes à /etc/security/limits.conf dans chaque nœud Linux :

```
*               hard    memlock         unlimited
*               soft    memlock         unlimited
```

Redémarrez les nœuds Linux une fois la mise à jour du fichier limits.conf terminée. Par exemple, utilisez la commande **clusrun** suivante.

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Après le redémarrage, assurez-vous que le dossier partagé est monté en tant que /openfoam.

### Compiler et installer OpenFOAM

Enregistrez le package d’installation téléchargé pour le pack source OpenFOAM (OpenFOAM-2.3.1.tgz dans cet exemple) sur C:\\OpenFoam sur le nœud principal afin que les nœuds Linux puissent accéder à ce fichier depuis /openfoam. Exécutez ensuite **clusrun** pour compiler OpenFOAM dans tous les nœuds Linux.


1.  Créer un dossier /opt/OpenFOAM sur chaque nœud Linux, copiez le package source dans ce dossier et extrayez-le ici.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM

    clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/

    clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
    ```

2.  Pour compiler OpenFOAM avec la bibliothèque MPI Intel, commencez par configurer certaines variables d’environnement à la fois pour Intel MPI et OpenFOAM. Pour ce faire, utilisez un script d’interpréteur de commandes appelé settings.sh. Vous trouverez un exemple dans les exemples de fichiers à la fin de cet article. Placez ce fichier (enregistré avec des fins de ligne Linux) dans le dossier partagé /openfoam. Ce fichier contient également des paramètres pour les runtimes MPI et OpenFOAM que vous utiliserez par la suite afin d’exécuter un travail OpenFOAM.

3. Installez des packages dépendants nécessaires pour compiler OpenFOAM. En fonction de votre distribution Linux, vous aurez peut-être besoin d’ajouter un référentiel. Exécutez des commandes **clusrun** similaires à la suivante :

    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
    
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
    
    Si nécessaire, exécutez une commande ssh sur chaque nœud Linux pour exécuter les commandes afin de vérifier qu’ils fonctionnent correctement.

4.  Exécutez la commande suivante pour compiler OpenFOAM. Le processus de compilation prend du temps et génère une grande quantité d’informations de journalisation dans la sortie standard. Nous recommandons donc d’utiliser l’option **/interleaved** pour afficher la sortie entrelacée.

    ```
    clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
    ```
    
    >[AZURE.NOTE]Le symbole « ` » dans la commande est un symbole de caractère d’échappement pour PowerShell. « & » signifie que « & » fait partie de la commande.

## Préparer l’exécution d’un travail OpenFOAM

Préparez-vous maintenant à exécuter un travail MPI appelé sloshingTank3D (l’un des exemples OpenFoam) sur deux nœuds Linux.

### Configurer l’environnement runtime

Exécutez la commande suivante dans une fenêtre Windows PowerShell sur le nœud principal pour configurer les environnements d’exécution de MPI et OpenFOAM sur tous les nœuds Linux. (Cette commande est valide pour SUSE Linux uniquement.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### Préparer l’exemple de données

Utilisez le partage de nœud principal configuré précédemment pour partager les fichiers d’installation sur les nœuds Linux (monté en tant que /openfoam).

1.  Exécutez une commande SSH sur l’un des nœuds de calcul Linux.

2.  Exécutez la commande suivante pour configurer l’environnement d’exécution OpenFOAM, si vous ne l’avez pas déjà fait.

    ```
    $ source /openfoam/settings.sh
    ```
    
3.  Copiez l’exemple sloshingTank3D dans le dossier partagé et accédez à ce dernier.

    ```
    $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/

    $ cd /openfoam/sloshingTank3D
    ```

4.  Lorsque vous utilisez les paramètres par défaut de cet exemple, l’opération peut prendre des dizaines de minutes, voire plus pour s’exécuter. Alors il se peut que vous vouliez modifier certains paramètres pour accélérer l’opération. Un choix simple consiste à modifier la variable de temps d’opération deltaT et writeInterval dans le fichier system/controlDict, qui stocke toutes les données d’entrée relatives au contrôle du temps et à lecture et l’écriture des données de la solution. Par exemple, vous pouvez faire passer la valeur deltaT de 0,05 à 0,5 et la valeur writeInterval de 0,05 à 0,5.

    ![Modifier les variables d’étape][step_variables]

5.  Spécifiez les valeurs souhaitées pour les variables dans le fichier system/decomposeParDict. Dans cet exemple 2 nœuds Linux avec chacun 8 cœurs sont utilisés. Alors, définissez la valeur numberOfSubdomains à 16 et n de hierarchicalCoeffs à (1 1 16), ce qui signifie exécuter OpenFOAM en parallèle avec 16 processus. Pour plus d’informations sur la façon d’exécuter OpenFOAM en parallèle, consultez [Guide de l’utilisateur OpenFOAM : 3.4 Exécution d’applications en parallèle (en anglais)](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).

    ![Décomposer les processus][decompose]

6.  Exécutez les commandes suivantes à partir du répertoire sloshingTank3D pour préparer les exemples de données.

    ```
    $ . $WM_PROJECT_DIR/bin/tools/RunFunctions

    $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict

    $ runApplication blockMesh

    $ cp 0/alpha.water.org 0/alpha.water

    $ runApplication setFields  
    ```
    
7.  Sur le nœud principal, vous devez voir que les exemples de fichiers de données sont copiés dans C:\\OpenFoam\\sloshingTank3D. (C:\\OpenFoam est le dossier partagé sur le nœud principal.)

    ![Fichiers de données sur le nœud principal][data_files]

### Fichier hôte pour mpirun

Au cours de cette étape, vous créez un fichier hôte (une liste de nœuds de calcul) que la commande **mpirun** utilise.

1.	Sur l’un des nœuds Linux, créez un nouveau fichier nommé hostfile sous /openfoam afin que ce fichier puisse être atteint dans /openfoam/hostfile sur tous les nœuds Linux.

2.	Écrivez vos noms de nœud Linux dans ce fichier. Dans cet exemple, le fichier ressemble à ce qui suit :
    
    ```       
    SUSE12RDMA-LN1
    SUSE12RDMA-LN2
    ```
    
    >[AZURE.TIP]Vous pouvez également créer ce fichier dans C:\\OpenFoam\\hostfile sur le nœud principal. Si vous faites cela, enregistrez-le fichier texte avec les fins de lignes Linux (LF uniquement, pas CR LF). Cela garantit qu’il s’exécute correctement sur les nœuds Linux.

    **Wrapper de script d’interpréteur de commandes**

    Si vous disposez de plusieurs nœuds Linux et si votre travail s’exécute uniquement sur certains d’entre eux, l’utilisation d’un fichier hôte fixe, car vous ne connaissez pas les nœuds qui seront alloués à votre travail. Dans ce cas, écrivez un wrapper de script Bash pour **mpirun** afin de créer le fichier hôte automatiquement. Vous pouvez trouver un exemple de wrapper de script Bash appelé hpcimpirun.sh à la fin de cet article et l’enregistrer comme /openfoam/hpcimpirun.sh. L’exemple de script effectue les opérations suivantes :

    1.	Configure les variables d’environnement pour **mpirun**, et certains paramètres de commande supplémentaires pour exécuter le travail MPI via le réseau RDMA. Dans ce cas, il définit les éléments suivants :

        *	I\_MPI\_FABRICS=shm:dapl
        *	I\_MPI\_DAPL\_PROVIDER=ofa-v2-ib0
        *	I\_MPI\_DYNAMIC\_CONNECTION=0

    2.	Crée un fichier d’hôte en fonction de la variable d’environnement $CCP\_NODES\_CORES définie par le nœud principal HPC lorsque le travail est activé.

        Le format de $CCP\_NODES\_CORES suit ce modèle :

        ```
        <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
        ```

        où

        * `<Number of nodes>` : nombre de nœuds affectés à ce travail.  
        
        * `<Name of node_n_...>` : nom de chaque nœud affecté à ce travail.
        
        * `<Cores of node_n_...>` : nombre de cœurs présents sur le nœud affecté à ce travail.

        Par exemple, si le travail requiert 2 cœurs pour s’exécuter, la valeur de $CCP\_NODES\_CORES doit se présenter comme suit
        
        ```
        2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
        ```
        
    3.	Appelle la commande **mpirun** et ajoute les 2 paramètres à la ligne de commande.

        * `--hostfile <hostfilepath>: <hostfilepath>` : le chemin d’accès du fichier hôte que le script crée

        * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` : une variable d’environnement définie par le nœud principal HPC Pack, qui stocke le nombre total de cœurs affectés à ce travail. Dans ce cas, elle spécifie le nombre de processus pour **mpirun**.


## Envoi d’un travail OpenFOAM

Maintenant, vous pouvez envoyer un travail dans HPC Cluster Manager. Vous devez transférer le script hpcimpirun.sh dans les lignes de commande pour certaines des tâches du travail.

1. Connectez-vous à votre nœud principal de cluster et démarrez HPC Cluster Manager.

2. Dans **Gestion des ressources**, assurez-vous que les nœuds de calcul Linux sont à l’état **En ligne**. Si ce n’est pas le cas, sélectionnez-les et cliquez sur **Mettre en ligne**.

3.  Dans **Gestion des tâches**, cliquez sur **Nouveau travail**.

4.  Entrez un nom pour le travail, par exemple _sloshingTank3D_.

    ![Détails du travail][job_details]

5.	Dans les **ressources de la tâche**, sélectionnez le type de ressource en tant que « Nœud » et définissez la valeur minimale sur 2. Le travail sera exécuté sur les 2 nœuds Linux comportant chacun 8 cœurs dans cet exemple.

    ![Ressources de la tâche][job_resources]

6.	Ajoutez 4 tâches au travail avec les lignes de commande et les paramètres de tâches suivantes.

    >[AZURE.NOTE]L’exécution de `source /openfoam/settings.sh` configure les environnements d’exécution OpenFOAM et MPI pour que chacune des tâches suivantes l’appelle avant la commande OpenFOAM.

    *   **Tâche 1**. Exécutez **decomposePar** pour générer des fichiers de données pour l’exécution de **interDyMFoam** en parallèle.
    
        *   Affectez 1 nœud à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
    
        *   **Répertoire de travail** - /openfoam/sloshingTank3D
        
        Voir la figure suivante. Vous configurez les tâches restantes de la même manière.

        ![Détails de la tâche 1][task_details1]

    *   **Tâche 2**. Exécutez **interDyMFoam** en parallèle pour calculer l’exemple.

        *   Affectez 2 nœuds à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`

        *   **Répertoire de travail** - /openfoam/sloshingTank3D

    *   **Tâche 3**. Exécutez la commande **reconstructPar** pour fusionner les jeux de répertoires temporels à partir de chaque répertoire processor\_N dans un seul ensemble de répertoires temporels.

        *   Affectez 1 nœud à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`

        *   **Répertoire de travail** - /openfoam/sloshingTank3D

    *   **Tâche 4**. Exécutez **foamToEnsight** en parallèle pour convertir les fichiers de résultats OpenFOAM au format EnSight et placez les fichiers EnSight dans un répertoire nommé Ensight dans le répertoire du cas.

        *   Affectez 2 nœuds à la tâche

        *   **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`

        *   **Répertoire de travail** - /openfoam/sloshingTank3D

6.	Ajoutez des dépendances à ces tâches dans l’ordre croissant des tâches.

    ![Dépendances de la tâche][task_dependencies]

7.	Cliquez sur **Envoyer** pour exécuter ce travail.

    Par défaut, HPC Pack envoie la tâche en tant que votre compte d’utilisateur connecté actuel. Une fois que vous avez cliqué sur **Envoyer**, une boîte de dialogue peut vous inviter à entrer le nom d’utilisateur et le mot de passe.

    ![Informations d’identification de la tâche][creds]

    Sous certaines conditions, HPC Pack mémorise les informations utilisateur entrées auparavant et n’affiche plus cette boîte de dialogue. Pour que HPC Pack l’affiche de nouveau, entrez les informations suivantes dans une invite de commande, puis envoyez le travail.

    ```
    hpccred delcreds
    ```

8.	Ce travail prend de quelques dizaines de minutes à plusieurs heures en fonction des paramètres que vous avez définis pour l’exemple. Dans la carte thermique, vous verrez le travail s’exécutant sur 2 nœuds Linux.

    ![Carte thermique][heat_map]

    Sur chaque nœud, 8 processus sont démarrés.

    ![Processus Linux][linux_processes]

9.  Lorsque le travail se termine, recherchez les résultats du travail dans les dossiers sous C:\\OpenFoam\\sloshingTank3D et les fichiers journaux dans C:\\OpenFoam.


## Afficher les résultats dans EnSight

Vous pouvez également utiliser [EnSight](https://www.ceisoftware.com/) pour visualiser et analyser les résultats du travail OpenFOAM. Pour plus d’informations sur la visualisation et l’animation dans EnSight, consultez le [guide vidéo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1.  Après avoir installé EnSight sur le nœud principal, démarrez le service.

2.  Ouvrez C:\\OpenFoam\\sloshingTank3D\\EnSight\\sloshingTank3D.case.

    Vous verrez une citerne dans la visionneuse.

    ![Réservoir dans EnSight][tank]

3.	Créez une **Isosurface** à partir de **internalMesh**, puis choisissez la variable **alpha\_water**.

    ![Créer une isosurface][isosurface]

4.	Définissez la couleur de l’élément **Isosurface\_part** créé à l’étape précédente. Par exemple, définissez la sur bleu océan.

    ![Modifier la couleur de l’isosurface][isosurface_color]

5.  Créez un **Isovolume** à partir des **parois** en sélectionnant **parois** dans le panneau **Pièces**, puis cliquez sur le bouton **Isosurfaces** dans la barre d’outils.

6.	Dans la boîte de dialogue, sélectionnez **Type** en tant qu’**Isovolume** et définissez la **plage Isovolume** minimale sur 0,5. Cliquez sur **Créer avec des pièces sélectionnées** pour créer l’isovolume.

7.	Définissez la couleur de l’élément **Iso\_volume\_part** créé à l’étape précédente. Par exemple, définissez sur bleu profond.

8.	Définissez la couleur des **parois**. Par exemple, définissez-le sur blanc transparent.

9. Cliquez maintenant sur **Lire** pour afficher les résultats de la simulation.

    ![Résultat du réservoir][tank_result]

## Exemple de fichiers


### Exemple de fichier cred.xml

```
<ExtendedData>
  <PrivateKey>-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEAxJKBABhnOsE9eneGHvsjdoXKooHUxpTHI1JVunAJkVmFy8JC
qFt1pV98QCtKEHTC6kQ7tj1UT2N6nx1EY9BBHpZacnXmknpKdX4Nu0cNlSphLpru
lscKPR3XVzkTwEF00OMiNJVknq8qXJF1T3lYx3rW5EnItn6C3nQm3gQPXP0ckYCF
Jdtu/6SSgzV9kaapctLGPNp1Vjf9KeDQMrJXsQNHxnQcfiICp21NiUCiXosDqJrR
AfzePdl0XwsNngouy8t0fPlNSngZvsx+kPGh/AKakKIYS0cO9W3FmdYNW8Xehzkc
VzrtJhU8x21hXGfSC7V0ZeD7dMeTL3tQCVxCmwIDAQABAoIBAQCve8Jh3Wc6koxZ
qh43xicwhdwSGyliZisoozYZDC/ebDb/Ydq0BYIPMiDwADVMX5AqJuPPmwyLGtm6
9hu5p46aycrQ5+QA299g6DlF+PZtNbowKuvX+rRvPxagrTmupkCswjglDUEYUHPW
05wQaNoSqtzwS9Y85M/b24FfLeyxK0n8zjKFErJaHdhVxI6cxw7RdVlSmM9UHmah
wTkW8HkblbOArilAHi6SlRTNZG4gTGeDzPb7fYZo3hzJyLbcaNfJscUuqnAJ+6pT
iY6NNp1E8PQgjvHe21yv3DRoVRM4egqQvNZgUbYAMUgr30T1UoxnUXwk2vqJMfg2
Nzw0ESGRAoGBAPkfXjjGfc4HryqPkdx0kjXs0bXC3js2g4IXItK9YUFeZzf+476y
OTMQg/8DUbqd5rLv7PITIAqpGs39pkfnyohPjOe2zZzeoyaXurYIPV98hhH880uH
ZUhOxJYnlqHGxGT7p2PmmnAlmY4TSJrp12VnuiQVVVsXWOGPqHx4S4f9AoGBAMn/
vuea7hsCgwIE25MJJ55FYCJodLkioQy6aGP4NgB89Azzg527WsQ6H5xhgVMKHWyu
Q1snp+q8LyzD0i1veEvWb8EYifsMyTIPXOUTwZgzaTTCeJNHdc4gw1U22vd7OBYy
nZCU7Tn8Pe6eIMNztnVduiv+2QHuiNPgN7M73/x3AoGBAOL0IcmFgy0EsR8MBq0Z
ge4gnniBXCYDptEINNBaeVStJUnNKzwab6PGwwm6w2VI3thbXbi3lbRAlMve7fKK
B2ghWNPsJOtppKbPCek2Hnt0HUwb7qX7Zlj2cX/99uvRAjChVsDbYA0VJAxcIwQG
TxXx5pFi4g0HexCa6LrkeKMdAoGAcvRIACX7OwPC6nM5QgQDt95jRzGKu5EpdcTf
g4TNtplliblLPYhRrzokoyoaHteyxxak3ktDFCLj9eW6xoCZRQ9Tqd/9JhGwrfxw
MS19DtCzHoNNewM/135tqyD8m7pTwM4tPQqDtmwGErWKj7BaNZARUlhFxwOoemsv
R6DbZyECgYEAhjL2N3Pc+WW+8x2bbIBN3rJcMjBBIivB62AwgYZnA2D5wk5o0DKD
eesGSKS5l22ZMXJNShgzPKmv3HpH22CSVpO0sNZ6R+iG8a3oq4QkU61MT1CfGoMI
a8lxTKnZCsRXU1HexqZs+DSc+30tz50bNqLdido/l5B4EJnQP03ciO0=
-----END RSA PRIVATE KEY-----</PrivateKey>
  <PublicKey>ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDEkoEAGGc6wT16d4Ye+yN2hcqigdTGlMcjUlW6cAmRWYXLwkKoW3WlX3xAK0oQdMLqRDu2PVRPY3qfHURj0EEellpydeaSekp1fg27Rw2VKmEumu6Wxwo9HddXORPAQXTQ4yI0lWSerypckXVPeVjHetbkSci2foLedCbeBA9c/RyRgIUl227/pJKDNX2Rpqly0sY82nVWN/0p4NAyslexA0fGdBx+IgKnbU2JQKJeiwOomtEB/N492XRfCw2eCi7Ly3R8+U1KeBm+zH6Q8aH8ApqQohhLRw71bcWZ1g1bxd6HORxXOu0mFTzHbWFcZ9ILtXRl4Pt0x5Mve1AJXEKb username@servername;</PublicKey>
</ExtendedData>
```
### Exemple de fichier silent.cfg

```
# Patterns used to check silent configuration file
#
# anythingpat - any string
# filepat     - the file location pattern (/file/location/to/license.lic)
# lspat       - the license server address pattern (0123@hostname)
# snpat       - the serial number pattern (ABCD-01234567)

# accept EULA, valid values are: {accept, decline}
ACCEPT_EULA=accept

# optional error behavior, valid values are: {yes, no}
CONTINUE_WITH_OPTIONAL_ERROR=yes

# install location, valid values are: {/opt/intel, filepat}
PSET_INSTALL_DIR=/opt/intel

# continue with overwrite of existing installation directory, valid values are: {yes, no}
CONTINUE_WITH_INSTALLDIR_OVERWRITE=yes

# list of components to install, valid values are: {ALL, DEFAULTS, anythingpat}
COMPONENTS=DEFAULTS

# installation mode, valid values are: {install, modify, repair, uninstall}
PSET_MODE=install

# directory for non-RPM database, valid values are: {filepat}
#NONRPM_DB_DIR=filepat

# Serial number, valid values are: {snpat}
#ACTIVATION_SERIAL_NUMBER=snpat

# License file or license server, valid values are: {lspat, filepat}
#ACTIVATION_LICENSE_FILE=

# Activation type, valid values are: {exist_lic, license_server, license_file, trial_lic, serial_number}
ACTIVATION_TYPE=trial_lic

# Path to the cluster description file, valid values are: {filepat}
#CLUSTER_INSTALL_MACHINES_FILE=filepat

# Intel(R) Software Improvement Program opt-in, valid values are: {yes, no}
PHONEHOME_SEND_USAGE_DATA=no

# Perform validation of digital signatures of RPM files, valid values are: {yes, no}
SIGNING_ENABLED=yes

# Select yes to enable mpi-selector integration, valid values are: {yes, no}
ENVIRONMENT_REG_MPI_ENV=no

# Select yes to update ld.so.conf, valid values are: {yes, no}
ENVIRONMENT_LD_SO_CONF=no

```

### Exemple de script settings.sh

```
#!/bin/bash

# impi
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# openfoam
export FOAM_INST_DIR=/opt/OpenFOAM
source /opt/OpenFOAM/OpenFOAM-2.3.1/etc/bashrc
export WM_MPLIB=INTELMPI
```


###Exemple de script hpcimpirun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"

# Set mpirun runtime evironment
source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh
export MPI_ROOT=$I_MPI_ROOT
export I_MPI_FABRICS=shm:dapl
export I_MPI_DAPL_PROVIDER=ofa-v2-ib0
export I_MPI_DYNAMIC_CONNECTION=0

# mpirun command
MPIRUN=mpirun
# Argument of "--hostfile"
NODELIST_OPT="--hostfile"
# Argument of "-np"
NUMPROCESS_OPT="-np"

# Get node information from ENVs
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# CCP_NODES_CORES is not found or is empty, just run the mpirun without hostfile arg.
	${MPIRUN} $*
else
	# Create the hostfile file
	NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$

	# Get every node name and write into the hostfile file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the mpirun with hostfile arg
	${MPIRUN} ${NUMPROCESS_OPT} ${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}

```





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/keys.png
[step_variables]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/step_variables.png
[data_files]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/data_files.png
[decompose]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/decompose.png
[job_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_details.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/job_resources.png
[task_details1]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_details1.png
[task_dependencies]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/task_dependencies.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/creds.png
[heat_map]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/heat_map.png
[tank]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank.png
[tank_result]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/tank_result.png
[isosurface]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface.png
[isosurface_color]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/isosurface_color.png
[linux_processes]: ./media/virtual-machines-linux-classic-hpcpack-cluster-openfoam/linux_processes.png

<!---HONumber=AcomDC_0323_2016-->