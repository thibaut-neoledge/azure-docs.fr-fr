<properties
 pageTitle="NAMD avec Microsoft HPC Pack sur des machines virtuelles Linux | Microsoft Azure"
 description="Déployer un cluster Microsoft HPC Pack sur Azure et exécuter une simulation NAMD avec charmrun sur plusieurs nœuds de calcul Linux."
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager,hpc-pack"/>
<tags
 ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="03/22/2016"
 ms.author="danlep"/>

# Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure

Cet article vous explique comment déployer un cluster Microsoft HPC Pack sur Azure avec plusieurs nœuds de calcul Linux et exécuter une tâche [NAMD](http://www.ks.uiuc.edu/Research/namd/) avec **charmrun** pour calculer et visualiser la structure d’un système biomoléculaire étendu.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].

NAMD (programme Nanoscale Molecular Dynamics) est un package de dynamique moléculaire parallèle, conçu pour la simulation hautes performances des systèmes biomoléculaires étendus contenant jusqu’à plusieurs millions d’atomes, comme les virus, les structures de cellules et les protéines volumineuses. NAMD s’adapte à des centaines de cœurs pour les simulations classiques et à plus de 500 000 cœurs pour les simulations les plus étendues.

Microsoft HPC Pack fournit des fonctionnalités permettant d’exécuter un éventail d’applications HPC à grande échelle et parallèles, y compris des applications MPI, sur des clusters de machines virtuelles Microsoft Azure. Développée à l’origine comme solution pour les charges de travail Windows HPC, HPC Pack prend désormais en charge l’exécution d’applications HPC Linux sur les machines virtuelles de nœuds de calcul Linux déployées dans un cluster HPC Pack. Consultez la présentation [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).


## Composants requis

* **Cluster HPC Pack avec nœuds de calcul Linux** : déployez un cluster HPC Pack avec des nœuds de calcul Linux dans Azure à l’aide d’un [modèle Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou d’un [script Azure PowerShell](virtual-machines-hpcpack-cluster-powershell-script). Consultez la configuration requise et la procédure pour chaque option sur la page [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md). Si vous choisissez l’option de déploiement de script Powershell, consultez l’exemple de fichier de configuration dans les fichiers d’exemple à la fin de cet article pour déployer un cluster HPC Pack basé sur Azure constitué d’un nœud principal Windows Server 2012 R2 et de 4 nœuds de calcul CentOS 6.6 de grande taille (A3). Adaptez ces éléments à votre environnement si nécessaire.


* **Logiciel NAMD et fichiers du didacticiel** : téléchargez le logiciel NAMD pour Linux à partir du site [NAMD](http://www.ks.uiuc.edu/Research/namd/) (inscription obligatoire). Cet article est basé sur NAMD version 2.10 et utilise l'archive [Linux-x86\_64 (Intel/AMD 64 bits avec Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310), que vous utiliserez pour exécuter NAMD sur plusieurs nœuds de calcul Linux dans un réseau en cluster. Téléchargez également les [fichiers du didacticiel NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Les fichiers ayant pour extension .tar, vous devrez utiliser un outil Windows pour les extraire sur le nœud principal du cluster. Pour ce faire, suivez les instructions données plus loin dans cet article.

* **VMD** (facultatif) : pour afficher les résultats de votre tâche NAMD, téléchargez et installez le programme de visualisation moléculaire [VMD](http://www.ks.uiuc.edu/Research/vmd/) sur un ordinateur de votre choix. La version actuelle est 1.9.2. Consultez le site de téléchargement de VMD pour commencer.


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

3. Ajoutez l’utilisateur de domaine au cluster HPC Pack en tant qu’utilisateur de cluster. Consultez la page [Ajouter ou supprimer des utilisateurs du cluster](https://technet.microsoft.com/library/ff919330.aspx).

2.	Créez un fichier nommé C:\\cred.xml et copiez-y les données de clé RSA. Vous trouverez un exemple dans les exemples de fichiers à la fin de cet article.

    ```
    <ExtendedData>
        <PrivateKey>Copy the contents of private key here</PrivateKey>
        <PublicKey>Copy the contents of public key here</PublicKey>
    </ExtendedData>
    ```

3.	Ouvrez une invite de commande et entrez la commande suivante pour définir les données d’informations d’identification du compte hpclab\\hpcuser. Utilisez le paramètre **extendeddata** pour transmettre le nom du fichier C:\\cred.xml que vous avez créé pour les données de clé.

    ```
    hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
    ```

    Cette commande se termine correctement sans sortie. Après avoir défini les informations d’identification pour les comptes d’utilisateurs dont vous avez besoin pour exécuter des tâches, stockez le fichier cred.xml à un emplacement sécurisé ou supprimez-le.

5.	Si vous avez généré la paire de clés RSA sur l’un de vos nœuds Linux, pensez à supprimer les clés une fois que vous avez terminé de les utiliser. HPC Pack ne configure pas l’approbation mutuelle s’il trouve des fichiers id\_rsa ou id\_rsa.pub existants.

>[AZURE.IMPORTANT] Nous déconseillons d’exécuter une tâche Linux en tant qu’administrateur de cluster sur un cluster partagé, car une tâche envoyée par un administrateur s’exécute sous le compte racine sur les nœuds Linux. Une tâche envoyée par un utilisateur non administrateur s’exécute sous un compte d’utilisateur Linux local avec le même nom que l’utilisateur de la tâche, et HPC Pack définit l’approbation mutuelle pour cet utilisateur Linux sur tous les nœuds alloués à la tâche. Vous pouvez configurer l’utilisateur Linux manuellement sur les nœuds Linux avant d’exécuter la tâche, ou bien HPC Pack crée automatiquement l’utilisateur lorsque la tâche est envoyée. Si HPC Pack crée l’utilisateur, il se charge de le supprimer une fois la tâche terminée. Les clés sont supprimées après l’achèvement de la tâche sur les nœuds afin de réduire les menaces de sécurité.

## Configuration d’un partage de fichiers pour les nœuds Linux

À présent, configurez un partage de fichiers SMB et montez le dossier partagé sur tous les nœuds Linux pour leur permettre d’accéder aux fichiers NAMD avec un chemin d’accès commun. Consultez les options et les étapes de partage des fichiers sur la page [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md). Voici les étapes pour monter un dossier partagé sur le nœud principal, opération recommandée pour les distributions telles que CentOS 6.6 qui ne prennent actuellement pas en charge le service de fichiers Azure. Si vos nœuds Linux prennent en charge un partage de fichiers Azure, consultez [Utilisation d’Azure File Storage avec Linux](../storage/storage-how-to-use-files-linux.md).

1.	Créez un dossier sur le nœud principal et partagez-le avec tout le monde en définissant des privilèges de lecture/écriture. Dans cet exemple, \\\CentOS66HN\\Namd est le nom du dossier (où CentOS66HN est le nom d’hôte du nœud principal).

2. Extrayez les fichiers NAMD dans le dossier en utilisant une version Windows de **tar** ou un autre utilitaire Windows qui fonctionne sur les archives .tar. Extrayez l’archive tar NAMD vers \\\CentOS66HN\\Namd\\namd2 et extrayez les fichiers du didacticiel sous \\\CentOS66HN\\Namd\\namd2\\namdsample.

2.	Ouvrez une fenêtre Windows PowerShell et exécutez les commandes suivantes pour monter le dossier partagé.

    ```
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2

    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /namd2 sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande monte le dossier partagé //CentOS66HN/Namd/namd2 sur le dossier avec les bits dir\_mode et file\_mode définis sur 777. Les valeurs *username* et *password* dans la commande doivent être les informations d’identification d’un utilisateur du nœud principal.

>[AZURE.NOTE]Le symbole « ` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « `, » signifie que « , » (une virgule) est une partie de la commande.


## Créer un script Bash pour exécuter une tâche NAMD

Votre tâche NAMD requiert un fichier *nodelist* pour que **charmrun** détermine le nombre de nœuds à utiliser lors du démarrage des processus NAMD. Vous allez utiliser un script Bash qui génère le fichier nodelist et exécuter **charmrun** avec ce fichier. Vous pouvez ensuite envoyer une tâche NAMD dans HPC Cluster Manager qui appelle ce script.

À l’aide de l’éditeur de texte de votre choix, créez un script Bash dans le dossier contenant les fichiers du programme NAMD et nommez-le hpccharmrun.sh. Vous pouvez simplement copier l’exemple fourni dans les exemples de fichiers à la fin de cet article.

>[AZURE.TIP] Enregistrez votre script en tant que fichier texte avec des fins de ligne Linux (LF uniquement, pas CR LF). Cela garantit qu’il s’exécute correctement sur les nœuds Linux.

Voici les détails des actions du script Bash. Si vous effectuez une preuve de concept et souhaitez simplement exécuter une tâche NAMD, enregistrez votre script hpccharmrun.sh sur le partage de fichiers et accédez à [Envoi d’une tâche NAMD](#submit-a-namd-job).

1.	Définir des variables.

    ```
    #!/bin/bash

    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    # Charmrun command
    CHARMRUN=${SCRIPT_PATH}/charmrun
    # Argument of ++nodelist
    NODELIST_OPT="++nodelist"
    # Argument of ++p
    NUMPROCESS="+p"
    ```

2.	Obtenir des informations de nœud dans les variables d’environnement. $NODESCORES stocke une liste de mots de fractionnement à partir de $CCP\_NODES\_CORES. $COUNT représente la taille de $NODESCORES.
    ```
    # Get node information from the environment variables
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    ```    
    
    Le format de la variable $CCP\_NODES\_CORES est le suivant :

    ```
    <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
    ```

    Ceci répertorie le nombre total de nœuds, les noms des nœuds et le nombre de cœurs sur chaque nœud qui sont alloués à la tâche. Par exemple, si la tâche requiert 10 cœurs pour s’exécuter, la valeur de $CCP\_NODES\_CORES doit ressembler à ceci :

    ```
    3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
    ```
        
3.	Si la variable $CCP\_NODES\_CORES n’est pas définie, lancer **charmrun** directement. (Cela doit uniquement se produire lorsque vous exécutez ce script directement sur vos nœuds Linux.)

    ```
    if [ ${COUNT} -eq 0 ]
    then
    	# CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
    	#echo ${CHARMRUN} $*
    	${CHARMRUN} $*
    ```

4.	Ou créer un fichier de liste de nœuds pour **charmrun**.

    ```
    else
    	# Create the nodelist file
    	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

    	# Write the head line
    	echo "group main" > ${NODELIST_PATH}

    	# Get every node name and number of cores and write into the nodelist file
    	I=1
    	while [ ${I} -lt ${COUNT} ]
    	do
    		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
    		let "I=${I}+2"
    	done
```
5.	Exécuter **charmrun** avec le fichier de liste de nœuds, obtenir son état de retour et supprimer le fichier de liste de nœuds à la fin.

    ${CCP\_NUMCPUS} est une autre variable d’environnement définie par le nœud principal HPC Pack. Elle stocke le nombre total de cœurs alloués à cette tâche. Nous l’utilisons pour spécifier le nombre de processus pour charmrun.

    ```
	# Run charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
    fi

    ```
6.	Quitter avec l'état de retour **charmrun**.

    ```
    exit ${RTNSTS}
    ```



Voici les informations contenues dans le fichier de liste de nœuds qui sera généré par le script :

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Par exemple :

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## Envoi d’une tâche NAMD

Vous êtes maintenant prêt à envoyer une tâche NAMD dans HPC Cluster Manager.

1.	Connectez-vous à votre nœud principal de cluster et démarrez HPC Cluster Manager.

2.  Dans **Gestion des ressources**, assurez-vous que les nœuds de calcul Linux ont l’état **En ligne**. Si ce n'est pas le cas, sélectionnez-les et cliquez sur **Mettre en ligne**.

2.  Dans **Gestion des tâches**, cliquez sur **Nouvelle tâche**.

3.	Entrez un nom pour la tâche, par exemple *hpccharmrun*.

    ![Nouvelle tâche HPC][namd_job]

4.	Sur la page **Détails de la tâche**, sous **Ressources de la tâche**, sélectionnez le type de ressource **Nœud** et définissez la valeur **Minimum** sur 3. Dans cet exemple, nous allons exécuter la tâche sur 3 nœuds Linux ; chaque nœud comporte 4 cœurs.

    ![Ressources de la tâche][job_resources]

5. Cliquez sur **Modifier des tâches** dans le volet de navigation gauche, puis sur **Ajouter** pour ajouter une tâche à la tâche.


6. Sur la page **Détails de la tâche et redirection d’E/S**, définissez les valeurs suivantes.

    * **Ligne de commande** : `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`

    >[AZURE.TIP] La ligne de commande précédente est une commande unique sans saut de ligne. Un retour à la ligne sera appliqué pour qu’elle apparaisse sur plusieurs lignes sous **Ligne de commande**.

    * **Répertoire de travail** : /namd2

    * **Minimum** : 3

    ![Détails de la tâche][task_details]

    >[AZURE.NOTE] Vous définissez le répertoire de travail ici car **charmrun** essaie de naviguer dans le même répertoire de travail sur chaque nœud. Si le répertoire de travail n’est pas défini, HPC Pack démarre la commande dans un dossier nommé de façon aléatoire créé sur l’un des nœuds Linux. Cela provoque l’erreur suivante sur les autres nœuds : `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Pour éviter ce problème, spécifiez un chemin d’accès du dossier accessible par tous les nœuds en tant que répertoire de travail.

5.	Cliquez sur **Envoyer** pour exécuter cette tâche.

    Par défaut, HPC Pack envoie la tâche en tant que votre compte d’utilisateur connecté actuel. Une boîte de dialogue peut vous inviter à entrer le nom d’utilisateur et le mot de passe après avoir cliqué sur **Envoyer**.

    ![Informations d’identification de la tâche][creds]

    Sous certaines conditions, HPC Pack mémorise les informations utilisateur entrées auparavant et n’affiche plus cette boîte de dialogue. Pour que HPC Pack l’affiche de nouveau, entrez les informations suivantes dans une fenêtre de commande, puis envoyez la tâche.

    ```
    hpccred delcreds
    ```

6.	La tâche nécessite plusieurs minutes.

7.	Vous trouverez le journal de la tâche sous \<headnodeName>\\Namd\\namd2\\namd2\_hpccharmrun.log et les fichiers de sortie sous \<headnode>\\Namd\\namd2\\namdsample\\1-2-sphere.

8.	Si vous le souhaitez, démarrez VMD pour afficher les résultats de votre tâche. Les étapes permettant de visualiser les fichiers de sortie NAMD (dans le cas présent, une molécule protéique ubiquitine dans une sphère d’eau) dépassent le cadre de cet article. Voir [Didacticiel NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) pour plus d'informations.

    ![Résultats de la tâche][vmd_view]

## Exemple de fichiers

### Exemple de fichier de configuration XML pour le déploiement de clusters par script PowerShell

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
      <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpclab.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS66HN</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS66LN-%00%</VMNamePattern>
    <ServiceName>MyLnxCNService</ServiceName>
     <VMSize>Large</VMSize>
     <NodeCount>4</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-66-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>    
```

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

### Exemple de script hpccharmrun.sh

```
#!/bin/bash

# The path of this script
SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
# Charmrun command
CHARMRUN=${SCRIPT_PATH}/charmrun
# Argument of ++nodelist
NODELIST_OPT="++nodelist"
# Argument of ++p
NUMPROCESS="+p"

# Get node information from ENVs
# CCP_NODES_CORES=3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 4
NODESCORES=(${CCP_NODES_CORES})
COUNT=${#NODESCORES[@]}

if [ ${COUNT} -eq 0 ]
then
	# If CCP_NODES_CORES is not found or is empty, just run the charmrun without nodelist arg.
	#echo ${CHARMRUN} $*
	${CHARMRUN} $*
else
	# Create the nodelist file
	NODELIST_PATH=${SCRIPT_PATH}/nodelist_$$

	# Write the head line
	echo "group main" > ${NODELIST_PATH}

	# Get every node name & cores and write into the nodelist file
	I=1
	while [ ${I} -lt ${COUNT} ]
	do
		echo "host ${NODESCORES[${I}]} ++cpus ${NODESCORES[$(($I+1))]}" >> ${NODELIST_PATH}
		let "I=${I}+2"
	done

	# Run the charmrun with nodelist arg
	#echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
	${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*

	RTNSTS=$?
	rm -f ${NODELIST_PATH}
fi

exit ${RTNSTS}
```

 





<!--Image references-->
[keygen]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keygen.png
[keys]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/keys.png
[namd_job]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/namd_job.png
[job_resources]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/job_resources.png
[creds]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/creds.png
[task_details]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/task_details.png
[vmd_view]: ./media/virtual-machines-linux-classic-hpcpack-cluster-namd/vmd_view.png

<!---HONumber=AcomDC_0330_2016-->