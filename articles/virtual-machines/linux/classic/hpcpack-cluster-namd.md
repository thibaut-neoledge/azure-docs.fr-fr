---
title: NAMD avec Microsoft HPC Pack sur des machines virtuelles Linux | Microsoft Docs
description: "Déployer un cluster Microsoft HPC Pack sur Azure et exécuter une simulation NAMD avec charmrun sur plusieurs nœuds de calcul Linux"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 76072c6b-ac35-4729-ba67-0d16f9443bd7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/13/2016
ms.author: danlep
ms.openlocfilehash: 0c0b9875b4153edcc0ec0096577d041d394a842f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="run-namd-with-microsoft-hpc-pack-on-linux-compute-nodes-in-azure"></a>Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure
Cet article vous montre comment exécuter une charge de travail HPC (calcul haute performance) Linux sur des machines virtuelles Azure. Vous allez découvrir comment configurer un cluster [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) sur Azure avec des nœuds de calcul Linux et exécuter une simulation [NAMD](http://www.ks.uiuc.edu/Research/namd/) pour calculer et visualiser la structure d’un système biomoléculaire étendu.  

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

* **NAMD** (programme Nanoscale Molecular Dynamics) est un package de dynamique moléculaire parallèle, conçu pour la simulation hautes performances des systèmes biomoléculaires étendus contenant jusqu’à plusieurs millions d’atomes. Ces systèmes sont par exemple les virus, les structures de cellule et les grandes protéines. NAMD s’adapte à des centaines de cœurs pour les simulations classiques et à plus de 500 000 cœurs pour les simulations les plus étendues.
* **Microsoft HPC Pack** fournit des fonctionnalités permettant d’exécuter un éventail d’applications HPC à grande échelle et parallèles dans des clusters d’ordinateurs locaux ou de machines virtuelles Azure. Développée à l’origine comme solution pour les charges de travail Windows HPC, HPC Pack prend désormais en charge l’exécution d’applications HPC Linux sur les machines virtuelles de nœuds de calcul Linux déployées dans un cluster HPC Pack. Consultez la présentation [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](hpcpack-cluster.md) .

## <a name="prerequisites"></a>Composants requis
* **Cluster HPC Pack avec nœuds de calcul Linux** : déployez un cluster HPC Pack avec des nœuds de calcul Linux dans Azure à l’aide d’un [modèle Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou d’un [script Azure PowerShell](hpcpack-cluster-powershell-script.md). Consultez la configuration requise et la procédure pour chaque option sur la page [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](hpcpack-cluster.md) . Si vous choisissez l’option de déploiement de script PowerShell, voir l’exemple de fichier de configuration dans les fichiers d’exemple à la fin de cet article. Ce fichier configure un cluster HPC Pack basé sur Azure comportant un nœud principal Windows Server 2012 R2 et quatre nœuds de calcul de grande taille CentOS 6.6. Personnalisez ce fichier comme il convient pour votre environnement.
* **Logiciel NAMD et fichiers du didacticiel** : téléchargez le logiciel NAMD pour Linux à partir du site [NAMD](http://www.ks.uiuc.edu/Research/namd/) (inscription obligatoire). Cet article est basé sur la version 2.10 de NAMD et utilise l’archive [Linux-x86_64 (Intel/AMD 64 bits avec Ethernet)](http://www.ks.uiuc.edu/Development/Download/download.cgi?UserID=&AccessCode=&ArchiveID=1310). Téléchargez également les [fichiers du didacticiel NAMD](http://www.ks.uiuc.edu/Training/Tutorials/#namd). Les fichiers ont l’extension .tar et vous devez utiliser un outil Windows pour les extraire sur le nœud principal du cluster. Pour extraire les fichiers, suivez les instructions données plus loin dans cet article. 
* **VMD** (facultatif) : pour afficher les résultats de votre tâche NAMD, téléchargez et installez le programme de visualisation moléculaire [VMD](http://www.ks.uiuc.edu/Research/vmd/) sur un ordinateur de votre choix. La version actuelle est 1.9.2. Consultez le site de téléchargement de VMD pour commencer.  

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configuration de l’approbation mutuelle entre les nœuds de calcul
L’exécution d’une tâche de nœuds croisés sur plusieurs nœuds Linux requiert une approbation mutuelle entre les nœuds (par **rsh** ou **ssh**). Lorsque vous créez le cluster HPC Pack avec le script de déploiement IaaS Microsoft HPC Pack, le script définit automatiquement l’approbation mutuelle permanente pour le compte administrateur que vous spécifiez. Pour les utilisateurs non administrateurs que vous créez dans le domaine du cluster, vous devez configurer l’approbation mutuelle temporaire entre les nœuds lorsqu’une tâche leur est allouée. Puis, détruisez la relation une fois la tâche terminée. Pour ce faire, pour chaque utilisateur, fournissez une paire de clés RSA au cluster que HPC Pack utilise pour établir la relation d’approbation. Instructions ci-dessous.

### <a name="generate-an-rsa-key-pair"></a>Génération d’une paire de clés RSA
Générer une paire de clés RSA contenant une clé publique et une clé privée est facile : il vous suffit d’exécuter la commande Linux **ssh-keygen** .

1. Ouvrez une session sur un ordinateur Linux.
2. Exécutez la commande suivante :
   
   ```bash
   ssh-keygen -t rsa
   ```
   
   > [!NOTE]
   > Appuyez sur **Entrée** pour utiliser les paramètres par défaut jusqu'à ce que la commande soit terminée. N'entrez pas de phrase secrète ici ; à l'invite de mot de passe, appuyez simplement sur **Entrée**.
   > 
   > 
   
   ![Génération d’une paire de clés RSA][keygen]
3. Remplacez le répertoire par le répertoire ~/.ssh. La clé privée est stockée dans id_rsa et la clé publique dans id_rsa.pub.
   
   ![Clés publiques et privées][keys]

### <a name="add-the-key-pair-to-the-hpc-pack-cluster"></a>Ajout de la paire de clés au cluster HPC Pack
1. [Connectez-vous via le Bureau à distance](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à la machine virtuelle du nœud principal en utilisant les informations d’identification du domaine que vous avez fournies au moment de déployer le cluster (par exemple, hpc\admin_cluster). Le cluster est géré à partir du nœud principal.
2. Utilisez les procédures standard Windows Server pour créer un compte d’utilisateur de domaine dans le domaine Active Directory du cluster. Par exemple, utilisez l’outil Utilisateurs et ordinateurs Active Directory sur le nœud principal. Les exemples de cet article supposent que vous créez un utilisateur de domaine nommé hpcuser dans le domaine hpclab (hpclab\hpcuser).
3. Ajoutez l’utilisateur de domaine au cluster HPC Pack en tant qu’utilisateur de cluster. Pour obtenir des instructions, consultez [Ajouter ou supprimer des utilisateurs du cluster](https://technet.microsoft.com/library/ff919330.aspx).
4. Créez un fichier nommé C:\cred.xml et copiez-y les données de clé RSA. Vous trouverez un exemple dans les exemples de fichiers à la fin de cet article.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
5. Ouvrez une invite de commande et entrez la commande suivante pour définir les données d’informations d’identification du compte hpclab\hpcuser. Utilisez le paramètre **extendeddata** pour transmettre le nom du fichier C:\cred.xml que vous avez créé pour les données de clé.
   
   ```command
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Cette commande se termine correctement sans sortie. Après avoir défini les informations d’identification pour les comptes d’utilisateurs dont vous avez besoin pour exécuter des tâches, stockez le fichier cred.xml à un emplacement sécurisé ou supprimez-le.
6. Si vous avez généré la paire de clés RSA sur l’un de vos nœuds Linux, pensez à supprimer les clés une fois que vous avez terminé de les utiliser. HPC Pack ne configure pas l’approbation mutuelle s’il trouve des fichiers id_rsa ou id_rsa.pub existants.

> [!IMPORTANT]
> Nous déconseillons d’exécuter une tâche Linux en tant qu’administrateur de cluster sur un cluster partagé, car une tâche envoyée par un administrateur s’exécute sous le compte racine sur les nœuds Linux. Une tâche envoyée par un utilisateur non-administrateur s’exécute sous un compte utilisateur Linux local du même nom que l’utilisateur de la tâche. Dans ce cas, HPC Pack définit une approbation mutuelle pour cet utilisateur Linux sur tous les nœuds alloués à la tâche. Vous pouvez configurer l’utilisateur Linux manuellement sur les nœuds Linux avant d’exécuter la tâche, ou bien HPC Pack crée automatiquement l’utilisateur lorsque la tâche est envoyée. Si HPC Pack crée l’utilisateur, il se charge de le supprimer une fois la tâche terminée. Pour réduire la menace de sécurité, les clés sont supprimées après l’achèvement de la tâche sur les nœuds.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configuration d’un partage de fichiers pour les nœuds Linux
À présent, configurez un partage de fichiers SMB et montez le dossier partagé sur tous les nœuds Linux pour leur permettre d’accéder aux fichiers NAMD avec un chemin d’accès commun. Les étapes de montage d’un dossier partagé sur le nœud principal sont décrites ci-après. Un partage est recommandé pour les distributions comme CentOS 6.6 qui ne prennent pas en charge Azure File Service pour le moment. Si vos nœuds Linux prennent en charge un partage de fichiers Azure, consultez [Utilisation du stockage de fichiers Azure avec Linux](../../../storage/files/storage-how-to-use-files-linux.md). Pour des options supplémentaires de partage des fichiers avec HPC Pack, consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](hpcpack-cluster.md).

1. Créez un dossier sur le nœud principal et partagez-le avec Tout le monde en définissant des privilèges de lecture/écriture. Dans cet exemple, \\\\CentOS66HN\Namd est le nom du dossier (où CentOS66HN est le nom d’hôte du nœud principal).
2. Créez un sous-dossier nommé namd2 dans le dossier partagé. Créez un autre sous-dossier nommé namdsample dans namd2.
3. Extrayez les fichiers NAMD dans le dossier en utilisant une version Windows de **tar** ou un autre utilitaire Windows qui fonctionne sur les archives .tar. 
   
   * Extrayez l’archive tar NAMD dans \\\\CentOS66HN\Namd\namd2.
   * Extrayez les fichiers du didacticiel dans \\\\CentOS66HN\Namd\namd2\namdsample.
4. Ouvrez une fenêtre Windows PowerShell et exécutez les commandes suivantes pour monter le dossier partagé sur les nœuds Linux.
   
    ```command
    clusrun /nodegroup:LinuxNodes mkdir -p /namd2
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS66HN/Namd/namd2 /namd2 -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

La première commande crée un dossier nommé /namd2 sur tous les nœuds dans le groupe LinuxNodes. La deuxième commande monte le dossier partagé //CentOS66HN/Namd/namd2 sur le dossier avec les bits dir_mode et file_mode définis sur 777. Les valeurs *username* et *password* dans la commande doivent être les informations d’identification d’un utilisateur du nœud principal.

> [!NOTE]
> Le symbole « \` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « \`, » signifie que « , » (une virgule) fait partie de la commande.
> 
> 

## <a name="create-a-bash-script-to-run-a-namd-job"></a>Créer un script Bash pour exécuter une tâche NAMD
Votre tâche NAMD requiert un fichier *nodelist* pour que **charmrun** détermine le nombre de nœuds à utiliser lors du démarrage des processus NAMD. Vous utilisez un script Bash qui génère le fichier nodelist et exécute **charmrun** avec ce fichier. Vous pouvez ensuite envoyer une tâche NAMD dans HPC Cluster Manager qui appelle ce script.

À l’aide de l’éditeur de texte de votre choix, créez un script Bash dans le dossier /namd2 contenant les fichiers du programme NAMD et nommez-le hpccharmrun.sh. Pour une démonstration rapide du concept, copiez l’exemple de script hpccharmrun.sh fourni à la fin de cet article et accédez à [Envoi d’une tâche NAMD](#submit-a-namd-job).

> [!TIP]
> Enregistrez votre script en tant que fichier texte avec des fins de ligne Linux (LF uniquement, pas CR LF). Cela garantit qu’il s’exécute correctement sur les nœuds Linux.
> 
> 

Voici les détails des actions du script Bash. 

1. Définir des variables.
   
   ```bash
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
2. Obtenir des informations de nœud dans les variables d’environnement. $NODESCORES stocke une liste de mots de fractionnement à partir de $CCP_NODES_CORES. $COUNT représente la taille de $NODESCORES.
   
   ```
   # Get node information from the environment variables
   NODESCORES=(${CCP_NODES_CORES})
   COUNT=${#NODESCORES[@]}
   ```    
   
   Le format de la variable $CCP_NODES_CORES est le suivant :
   
   ```
   <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>…
   ```
   
   Cette variable répertorie le nombre total de nœuds, les noms des nœuds et le nombre de cœurs sur chaque nœud qui sont alloués à la tâche. Par exemple, si la tâche requiert 10 cœurs pour s’exécuter, la valeur de $CCP_NODES_CORES ressemble à ceci :
   
   ```
   3 CENTOS66LN-00 4 CENTOS66LN-01 4 CENTOS66LN-03 2
   ```
3. Si la variable $CCP_NODES_CORES n’est pas définie, lancer **charmrun** directement. (Cela doit uniquement se produire lorsque vous exécutez ce script directement sur vos nœuds Linux.)
   
   ```
   if [ ${COUNT} -eq 0 ]
   then
     # CCP_NODES is_CORES is not found or is empty, so just run charmrun without nodelist arg.
     #echo ${CHARMRUN} $*
     ${CHARMRUN} $*
   ```
4. Ou créer un fichier de liste de nœuds pour **charmrun**.
   
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
5. Exécuter **charmrun** avec le fichier de liste de nœuds, obtenir son état de retour et supprimer le fichier de liste de nœuds à la fin.
   
   ${CCP_NUMCPUS} est une autre variable d’environnement définie par le nœud principal HPC Pack. Elle stocke le nombre total de cœurs alloués à cette tâche. Nous l’utilisons pour spécifier le nombre de processus pour charmrun.
   
   ```
   # Run charmrun with nodelist arg
   #echo ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   ${CHARMRUN} ${NUMPROCESS}${CCP_NUMCPUS} ${NODELIST_OPT} ${NODELIST_PATH} $*
   
   RTNSTS=$?
   rm -f ${NODELIST_PATH}
   fi
   
   ```
6. Quitter avec l'état de retour **charmrun** .
   
   ```
   exit ${RTNSTS}
   ```

Voici les informations contenues dans le fichier de liste de nœuds généré par le script :

```
group main
host <Name of node1> ++cpus <Cores of node1>
host <Name of node2> ++cpus <Cores of node2>
…
```

Par exemple :

```
group main
host CENTOS66LN-00 ++cpus 4
host CENTOS66LN-01 ++cpus 4
host CENTOS66LN-03 ++cpus 2
```

## <a name="submit-a-namd-job"></a>Envoi d’une tâche NAMD
Vous êtes maintenant prêt à envoyer une tâche NAMD dans HPC Cluster Manager.

1. Connectez-vous à votre nœud principal de cluster et démarrez HPC Cluster Manager.
2. Dans **Gestion des ressources**, assurez-vous que les nœuds de calcul Linux sont à l’état **En ligne**. Si ce n’est pas le cas, sélectionnez-les et cliquez sur **Mettre en ligne**.
3. Dans **Gestion des tâches**, cliquez sur **Nouveau travail**.
4. Entrez un nom pour la tâche, par exemple *hpccharmrun*.
   
   ![Nouvelle tâche HPC][namd_job]
5. Sur la page **Détails de la tâche**, sous **Ressources de la tâche**, sélectionnez le type de ressource **Nœud** et définissez la valeur **Minimum** sur 3. , nous exécutons la tâche sur trois nœuds Linux et chaque nœud comporte quatre cœurs.
   
   ![ressources de la tâche][job_resources]
6. Cliquez sur **Modifier des tâches** dans le volet de navigation gauche, puis sur **Ajouter** pour ajouter une tâche au travail.    
7. Sur la page **Détails de la tâche et redirection d’E/S** , définissez les valeurs suivantes :
   
   * **Ligne de commande**-
     `/namd2/hpccharmrun.sh ++remote-shell ssh /namd2/namd2 /namd2/namdsample/1-2-sphere/ubq_ws_eq.conf > /namd2/namd2_hpccharmrun.log`
     
     > [!TIP]
     > La ligne de commande précédente est une commande unique sans saut de ligne. Un retour à la ligne est appliqué pour qu’elle apparaisse sur plusieurs lignes sous **Ligne de commande**.
     > 
     > 
   * **Répertoire de travail** : /namd2
   * **Minimum** : 3
     
     ![Détails de la tâche][task_details]
     
     > [!NOTE]
     > Vous définissez le répertoire de travail ici car **charmrun** essaie de naviguer dans le même répertoire de travail sur chaque nœud. Si le répertoire de travail n’est pas défini, HPC Pack démarre la commande dans un dossier nommé de façon aléatoire créé sur l’un des nœuds Linux. Cela provoque l’erreur suivante sur les autres nœuds : `/bin/bash: line 37: cd: /tmp/nodemanager_task_94_0.mFlQSN: No such file or directory.` Pour éviter ce problème, spécifiez un chemin d’accès du dossier accessible par tous les nœuds en tant que répertoire de travail.
     > 
     > 
8. Cliquez sur **OK**, puis sur **Envoyer** pour exécuter cette tâche.
   
   Par défaut, HPC Pack envoie la tâche en tant que votre compte d’utilisateur connecté actuel. Une boîte de dialogue peut vous inviter à entrer le nom d'utilisateur et le mot de passe après avoir cliqué sur **Envoyer**.
   
   ![Informations d’identification de la tâche][creds]
   
   Dans certaines conditions, HPC Pack mémorise les informations utilisateur entrées auparavant et n’affiche plus cette boîte de dialogue. Pour que HPC Pack l’affiche de nouveau, entrez les informations suivantes à l’invite de commande, puis envoyez la tâche.
   
   ```command
   hpccred delcreds
   ```
9. La tâche nécessite plusieurs minutes.
10. Vous trouverez le journal de la tâche sous \\<headnodeName>\Namd\namd2\namd2_hpccharmrun.log et les fichiers de sortie sous \\<headnodeName>\Namd\namd2\namdsample\1-2-sphere\..
11. Si vous le souhaitez, démarrez VMD pour afficher les résultats de votre tâche. Les étapes permettant de visualiser les fichiers de sortie NAMD (dans le cas présent, une molécule protéique ubiquitine dans une sphère d’eau) dépassent le cadre de cet article. Voir [Didacticiel NAMD](http://www.life.illinois.edu/emad/biop590c/namd-tutorial-unix-590C.pdf) pour plus d'informations.
    
    ![Résultats de la tâche][vmd_view]

## <a name="sample-files"></a>Exemple de fichiers
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemple de fichier de configuration XML pour le déploiement de clusters par script PowerShell
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

### <a name="sample-credxml-file"></a>Exemple de fichier cred.xml
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

### <a name="sample-hpccharmrunsh-script"></a>Exemple de script hpccharmrun.sh
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
[keygen]:media/hpcpack-cluster-namd/keygen.png
[keys]:media/hpcpack-cluster-namd/keys.png
[namd_job]:media/hpcpack-cluster-namd/namd_job.png
[job_resources]:media/hpcpack-cluster-namd/job_resources.png
[creds]:media/hpcpack-cluster-namd/creds.png
[task_details]:media/hpcpack-cluster-namd/task_details.png
[vmd_view]:media/hpcpack-cluster-namd/vmd_view.png
