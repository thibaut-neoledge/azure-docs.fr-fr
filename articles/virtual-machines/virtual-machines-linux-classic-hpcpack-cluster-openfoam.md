---
title: "Exécuter OpenFOAM avec HPC Pack sur des machines virtuelles Linux | Microsoft Docs"
description: "Déployer un cluster Microsoft HPC Pack sur Azure et exécuter un travail OpenFOAM sur plusieurs nœuds de calcul RDMA."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: c0bb1637-bb19-48f1-adaa-491808d3441f
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 07/22/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 63ea23ca3ec756723b97bcf5290ed0256ed171ea


---
# <a name="run-openfoam-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Exécuter OpenFoam avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure
Cet article vous montre une méthode d’exécution d’OpenFoam dans des machines virtuelles Azure. Ici, vous déployez un cluster Microsoft HPC Pack avec des nœuds de calcul Linux sur Azure, et exécutez une tâche [OpenFoam](http://openfoam.com/) avec Intel MPI. Vous pouvez utiliser des machines virtuelles Azure prenant en charge RDMA pour les nœuds de calcul, de sorte que ceux-ci communiquent sur le réseau RDMA Azure. Les autres options d’exécution d’OpenFoam dans Azure incluent des images commerciales entièrement configurées disponibles sur le Marketplace, notamment [OpenFoam 2.3 sur CentOS 6](https://azure.microsoft.com/marketplace/partners/ubercloud/openfoam-v2dot3-centos-v6/) d’UberCloud, et l’exécution sur [Azure Batch](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/). 

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

OpenFOAM (acronyme d’Open Field Operation and Manipulation) est un logiciel open source de calcul de mécanique des fluides numérique largement utilisé en ingénierie et en sciences, tant au sein d’organisations commerciales que d’instituts universitaires. Il comprend des outils de maillage, notamment snappyHexMesh, un meilleur parallélisé pour les géométries de CAO complexes et de pré et post-traitement. Presque tous les processus s’exécutent en parallèle, permettant aux utilisateurs de tirer le meilleur parti du matériel informatique à leur disposition.  

Microsoft HPC Pack fournit des fonctionnalités permettant d’exécuter un éventail d’applications HPC à grande échelle et parallèles, dont des applications MPI, sur des clusters de machines virtuelles Microsoft Azure. HPC Pack prend également en charge l’exécution d’applications Linux HPC sur les machines virtuelles de nœuds de calcul Linux déployées dans un cluster HPC Pack. Voir [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) pour une présentation de l’utilisation des nœuds de calcul Linux avec HPC Pack.

> [!NOTE]
> Cet article explique comment exécuter une charge de travail Linux MPI avec HPC Pack. Il suppose que vous êtes familiarisé avec l’administration du système Linux et l’exécution de charges de travail MPI sur des clusters Linux. Si vous utilisez des versions MPI et OpenFOAM différentes de celles indiquées dans cet article, vous devrez peut-être adapter certaines étapes d’installation et de configuration. 
> 
> 

## <a name="prerequisites"></a>Composants requis
* **Cluster HPC Pack avec nœuds de calcul Linux prenant en charge RDMA** : déployez un cluster HPC Pack avec des nœuds de calcul Linux de taille A8, A9, H16r ou H16rm à l’aide d’un [modèle Azure Resource Manager](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) ou d’un [script Azure PowerShell](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Consultez la configuration requise et la procédure pour chaque option sur la page [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) . Si vous choisissez l’option de déploiement de script PowerShell, voir l’exemple de fichier de configuration dans les fichiers d’exemple à la fin de cet article. Utilisez cette configuration pour déployer un cluster HPC Pack basé sur Azure, composé d’un nœud principal Windows Server 2012 R2 de taille A8, et de 2 nœuds de calcul SUSE Linux Enterprise Server 12 de taille A8. Remplacez les valeurs appropriées pour les noms de vos abonnement et service. 
  
  **Autres informations à connaître**
  
  * Pour connaître les conditions préalables à la mise en réseau RDMA Linux dans Azure, voir [À propos des machines virtuelles de série H ou de série A nécessitant beaucoup de ressources système](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  * Si vous utilisez l’option de déploiement de script Powershell, déployez tous les nœuds de calcul Linux au sein d’un service cloud pour utiliser la connexion réseau RDMA.
  * Après avoir déployé les nœuds Linux, connectez-vous à SSH pour effectuer toutes tâches administratives supplémentaires. Recherchez les détails de connexion SSH pour chaque machine virtuelle Linux dans le portail Azure.  
* **Intel MPI** : pour exécuter OpenFOAM sur des nœuds de calcul SLES 12 HPC dans Azure, vous devez installer le runtime Intel MPI Library 5 que vous trouverez sur le [site Intel.com](https://software.intel.com/en-us/intel-mpi-library/). (Intel MPI 5 est préinstallé sur des images HPC basées sur CentOS.)  Si nécessaire, vous devez ensuite installer Intel MPI sur vos nœuds de calcul Linux. Pour préparer cette étape, après votre inscription auprès d’Intel, suivez le lien vers la page web associée, figurant dans le message de confirmation. Ensuite, copiez le lien de téléchargement du fichier .tgz pour la version appropriée d’Intel MPI. Cet article est basé sur Intel MPI version 5.0.3.048.
* **OpenFOAM Source Pack** : téléchargez le logiciel OpenFOAM Source Pack pour Linux à partir du [site d’OpenFOAM Foundation](http://openfoam.org/download/2-3-1-source/). Cet article est basé sur la version du Pack Source 2.3.1, disponible en téléchargement en tant que OpenFOAM 2.3.1.tgz. Suivez les instructions présentes plus loin dans cet article pour décompresser et compiler OpenFOAM sur les nœuds de calcul Linux.
* **EnSight** (facultatif) : pour afficher les résultats de votre simulation OpenFOAM, téléchargez et installez le programme de visualisation et d’analyse [EnSight](https://www.ceisoftware.com/download/) . Les informations de licence et de téléchargement se trouvent sur le site EnSight.

## <a name="set-up-mutual-trust-between-compute-nodes"></a>Configuration de l’approbation mutuelle entre les nœuds de calcul
L’exécution d’une tâche de nœuds croisés sur plusieurs nœuds Linux requiert une approbation mutuelle entre les nœuds (par **rsh** ou **ssh**). Lorsque vous créez le cluster HPC Pack avec le script de déploiement IaaS Microsoft HPC Pack, le script définit automatiquement l’approbation mutuelle permanente pour le compte administrateur que vous spécifiez. Pour les utilisateurs non administrateurs que vous créez dans le domaine du cluster, vous devez configurer l’approbation mutuelle temporaire entre les nœuds lorsqu’une tâche leur est allouée, puis détruire la relation une fois la tâche terminée. Pour établir l’approbation, pour chaque utilisateur, fournissez une paire de clés RSA au cluster que HPC Pack utilise pour la relation d’approbation.

### <a name="generate-an-rsa-key-pair"></a>Génération d’une paire de clés RSA
Générer une paire de clés RSA contenant une clé publique et une clé privée est facile : il vous suffit d’exécuter la commande Linux **ssh-keygen** .

1. Ouvrez une session sur un ordinateur Linux.
2. Exécutez la commande suivante :
   
   ```
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
1. Effectuez une connexion Bureau à distance à votre nœud principal avec votre compte administrateur HPC Pack (le compte administrateur que vous avez configuré lorsque vous avez exécuté le script de déploiement).
2. Utilisez les procédures standard Windows Server pour créer un compte d’utilisateur de domaine dans le domaine Active Directory du cluster. Par exemple, utilisez l’outil Utilisateurs et ordinateurs Active Directory sur le nœud principal. Les exemples de cet article supposent que vous créez un utilisateur de domaine nommé hpclab\hpcuser.
3. Créez un fichier nommé C:\cred.xml et copiez-y les données de clé RSA. Un exemple de fichier cred.xml figure à la fin de cet article.
   
   ```
   <ExtendedData>
     <PrivateKey>Copy the contents of private key here</PrivateKey>
     <PublicKey>Copy the contents of public key here</PublicKey>
   </ExtendedData>
   ```
4. Ouvrez une invite de commande et entrez la commande suivante pour définir les données d’informations d’identification du compte hpclab\hpcuser. Utilisez le paramètre **extendeddata** pour transmettre le nom du fichier C:\cred.xml que vous avez créé pour les données de clé.
   
   ```
   hpccred setcreds /extendeddata:c:\cred.xml /user:hpclab\hpcuser /password:<UserPassword>
   ```
   
   Cette commande se termine correctement sans sortie. Après avoir défini les informations d’identification pour les comptes d’utilisateurs dont vous avez besoin pour exécuter des tâches, stockez le fichier cred.xml à un emplacement sécurisé ou supprimez-le.
5. Si vous avez généré la paire de clés RSA sur l’un de vos nœuds Linux, pensez à supprimer les clés une fois que vous avez terminé de les utiliser. HPC Pack recherche un fichier id_rsa ou id_rsa.pub existant. Il ne configure pas d’approbation mutuelle.

> [!IMPORTANT]
> Nous déconseillons d’exécuter une tâche Linux en tant qu’administrateur de cluster sur un cluster partagé, car une tâche envoyée par un administrateur s’exécute sous le compte racine sur les nœuds Linux. Toutefois, une tâche envoyée par un utilisateur non-administrateur s’exécute sous un compte utilisateur Linux local du même nom que l’utilisateur de la tâche. Dans ce cas, HPC Pack définit une approbation mutuelle pour cet utilisateur Linux sur les nœuds alloués à la tâche. Vous pouvez configurer l’utilisateur Linux manuellement sur les nœuds Linux avant d’exécuter la tâche, ou bien HPC Pack crée automatiquement l’utilisateur lorsque la tâche est envoyée. Si HPC Pack crée l’utilisateur, il se charge de le supprimer une fois la tâche terminée. Pour réduire les menaces de sécurité, HPC Pack supprime les clés une fois la tâche accomplie.
> 
> 

## <a name="set-up-a-file-share-for-linux-nodes"></a>Configuration d’un partage de fichiers pour les nœuds Linux
À présent, configurez un partage SMB standard sur un dossier du nœud principal. Pour autoriser les nœuds Linux à accéder aux fichiers d’application avec un chemin d’accès commun, montez le dossier partagé sur les nœuds Linux. Si vous le souhaitez, vous pouvez utiliser une autre option de partage de fichier telle que le partage de fichiers Azure (recommandé dans de nombreux scénarios) ou un partage NFS. Consultez les étapes détaillées et les informations de partage de fichiers dans la section [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

1. Créez un dossier sur le nœud principal et partagez-le avec Tout le monde en définissant des privilèges de lecture/écriture. Par exemple, partagez D:\OpenFOAM sur le nœud principal en tant que \\\\SUSE12RDMA-HN\OpenFOAM. Ici, *SUSE12RDMA-HN* est le nom d’hôte du nœud principal.
2. Ouvrez une fenêtre Windows PowerShell, puis exécutez les commandes suivantes :
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
   clusrun /nodegroup:LinuxNodes mount -t cifs //SUSE12RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
   ```

La première commande crée un dossier nommé /openfoam sur tous les nœuds du groupe LinuxNodes. La deuxième commande monte le dossier partagé //SUSE12RDMA-HN/OpenFOAM sur les nœuds avec les bits dir_mode et file_mode définis sur 777. Les valeurs *username* et *password* dans la commande doivent être les informations d’identification d’un utilisateur du nœud principal.

> [!NOTE]
> Le symbole « \` » dans la deuxième commande est un symbole de caractère d’échappement pour PowerShell. « \`, » signifie que « , » (une virgule) fait partie de la commande.
> 
> 

## <a name="install-mpi-and-openfoam"></a>Installer MPI et OpenFOAM
Pour exécuter OpenFOAM en tant que travail MPI sur le réseau RDMA, vous devez compiler OpenFOAM avec les bibliothèques Intel MPI. 

Tout d’abord, exécutez quelques commandes **clusrun** pour installer (le cas échéant) les bibliothèques Intel MPI et OpenFOAM sur vos nœuds Linux. Utiliser le partage de nœud principal configuré précédemment pour partager les fichiers d’installation sur les nœuds Linux.

> [!IMPORTANT]
> Ces étapes d’installation et de compilation sont des exemples. Pour vous assurer que les bibliothèques et les compilateurs dépendants sont correctement installés, vous devez disposer de connaissances en administration du système Linux. Vous devrez peut-être modifier certaines variables d’environnement ou d’autres paramètres pour vos versions d’Intel MPI et OpenFOAM. Pour plus de détails, voir [Guide d’installation de la bibliothèque Intel MPI pour Linux](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html) et [Installation du pack source d’OpenFOAM](http://openfoam.org/download/2-3-1-source/) pour votre environnement.
> 
> 

### <a name="install-intel-mpi"></a>Installer Intel MPI
Enregistrez le package d’installation téléchargé pour Intel MPI (l_mpi_p_5.0.3.048.tgz dans cet exemple) dans C:\OpenFoam sur le nœud principal afin que les nœuds Linux puissent accéder à ce fichier à partir de /openfoam. Exécutez ensuite **clusrun** pour installer la bibliothèque Intel MPI sur tous les nœuds Linux.

1. Les commandes suivantes copient le package d’installation et l’extraient dans le répertoire /opt/intel de chacun des nœuds.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/intel
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/l_mpi_p_5.0.3.048.tgz /opt/intel/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/intel/l_mpi_p_5.0.3.048.tgz -C /opt/intel/
   ```
2. Pour installer la bibliothèque Intel MPI en mode silencieux, utilisez un fichier silent.cfg. Vous trouverez un exemple dans les exemples de fichiers à la fin de cet article. Placez ce fichier dans le dossier partagé /openfoam. Pour plus de détails sur le fichier silent.cfg, consultez le [Guide d’installation de la bibliothèque MPI Intel pour Linux - Installation silencieuse](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html#silentinstall).
   
   > [!TIP]
   > Assurez-vous que vous enregistrez votre fichier .cfg silencieux en tant que fichier texte avec des fins de ligne Linux (LF uniquement, et non CR LF). Cette étape garantit une exécution correcte sur les nœuds Linux.
   > 
   > 
3. Installez la bibliothèque MPI Intel en mode silencieux.
   
   ```
   clusrun /nodegroup:LinuxNodes bash /opt/intel/l_mpi_p_5.0.3.048/install.sh --silent /openfoam/silent.cfg
   ```

### <a name="configure-mpi"></a>Configuration de MPI
Pour le test, vous devez ajouter les lignes suivantes à /etc/security/limits.conf dans chaque nœud Linux :

    clusrun /nodegroup:LinuxNodes echo "*               hard    memlock         unlimited" `>`> /etc/security/limits.conf
    clusrun /nodegroup:LinuxNodes echo "*               soft    memlock         unlimited" `>`> /etc/security/limits.conf


Redémarrez les nœuds Linux une fois la mise à jour du fichier limits.conf terminée. Par exemple, utilisez la commande **clusrun** suivante :

```
clusrun /nodegroup:LinuxNodes systemctl reboot
```

Après le redémarrage, assurez-vous que le dossier partagé est monté en tant que /openfoam.

### <a name="compile-and-install-openfoam"></a>Compiler et installer OpenFOAM
Enregistrez le package d’installation téléchargé pour le pack source OpenFOAM (OpenFOAM-2.3.1.tgz dans cet exemple) sur C:\OpenFoam sur le nœud principal afin que les nœuds Linux puissent accéder à ce fichier depuis /openfoam. Exécutez ensuite les commandes **clusrun** pour compiler OpenFOAM sur tous les nœuds Linux.

1. Créer un dossier /opt/OpenFOAM sur chaque nœud Linux, copiez le package source dans ce dossier et extrayez-le ici.
   
   ```
   clusrun /nodegroup:LinuxNodes mkdir -p /opt/OpenFOAM
   
   clusrun /nodegroup:LinuxNodes cp /openfoam/OpenFOAM-2.3.1.tgz /opt/OpenFOAM/
   
   clusrun /nodegroup:LinuxNodes tar -xzf /opt/OpenFOAM/OpenFOAM-2.3.1.tgz -C /opt/OpenFOAM/
   ```
2. Pour compiler OpenFOAM avec la bibliothèque MPI Intel, commencez par configurer certaines variables d’environnement à la fois pour Intel MPI et OpenFOAM. Pour définir les variables, utilisez un script bash nommé settings.sh. Vous trouverez un exemple dans les exemples de fichiers à la fin de cet article. Placez ce fichier (enregistré avec des fins de ligne Linux) dans le dossier partagé /openfoam. Ce fichier contient également des paramètres pour les runtimes MPI et OpenFOAM que vous utiliserez par la suite afin d’exécuter un travail OpenFOAM.
3. Installez des packages dépendants nécessaires pour compiler OpenFOAM. En fonction de votre distribution Linux, vous aurez peut-être besoin d’ajouter un référentiel. Exécutez des commandes **clusrun** similaires à la suivante :
   
    ```
    clusrun /nodegroup:LinuxNodes zypper ar http://download.opensuse.org/distribution/13.2/repo/oss/suse/ opensuse
   
    clusrun /nodegroup:LinuxNodes zypper -n --gpg-auto-import-keys install --repo opensuse --force-resolution -t pattern devel_C_C++
    ```
   
    Si nécessaire, exécutez une commande SSH sur chaque nœud Linux pour exécuter les commandes afin de vérifier qu’ils fonctionnent correctement.
4. Exécutez la commande suivante pour compiler OpenFOAM. Le processus de compilation prend un certain temps et génère une grande quantité d’informations de journalisation dans la sortie standard. Nous recommandons donc d’utiliser l’option **/interleaved** pour afficher la sortie entrelacée.
   
   ```
   clusrun /nodegroup:LinuxNodes /interleaved source /openfoam/settings.sh `&`& /opt/OpenFOAM/OpenFOAM-2.3.1/Allwmake
   ```
   
   > [!NOTE]
   > Le symbole « \` » dans la commande est un symbole de caractère d’échappement pour PowerShell. «\`& » signifie que « & » fait partie de la commande.
   > 
   > 

## <a name="prepare-to-run-an-openfoam-job"></a>Préparer l’exécution d’un travail OpenFOAM
Préparez-vous maintenant à exécuter une tâche MPI nommée sloshingTank3D (l’un des exemples OpenFoam) sur deux nœuds Linux. 

### <a name="set-up-the-runtime-environment"></a>Configurer l’environnement runtime
Pour configurer les environnements d’exécution pour MPI et OpenFOAM sur les nœuds Linux, exécutez la commande suivante dans une fenêtre Windows PowerShell sur le nœud principal. (Cette commande est valide pour SUSE Linux uniquement.)

```
clusrun /nodegroup:LinuxNodes cp /openfoam/settings.sh /etc/profile.d/
```

### <a name="prepare-sample-data"></a>Préparer l’exemple de données
Utilisez le partage de nœud principal configuré précédemment pour partager les fichiers d’installation sur les nœuds Linux (monté en tant que /openfoam).

1. Exécutez une commande SSH sur l’un des nœuds de calcul Linux.
2. Exécutez la commande suivante pour configurer l’environnement d’exécution OpenFOAM, si vous ne l’avez pas déjà fait.
   
   ```
   $ source /openfoam/settings.sh
   ```
3. Copiez l’exemple sloshingTank3D dans le dossier partagé et accédez à ce dernier.
   
   ```
   $ cp -r $FOAM_TUTORIALS/multiphase/interDyMFoam/ras/sloshingTank3D /openfoam/
   
   $ cd /openfoam/sloshingTank3D
   ```
4. Lorsque vous utilisez les paramètres par défaut de cet exemple, l’exécution de l’opération peut prendre des dizaines de minutes. Il se peut donc que vous vouliez modifier certains paramètres pour accélérer le processus. Un choix simple consiste à modifier les variables d’étape de temps deltaT et writeInterval dans le fichier system/controlDict. Ce fichier stocke toutes les données d’entrée relatives au contrôle du temps, ainsi qu’à la lecture et à l’écriture des données de la solution. Par exemple, vous pouvez faire passer la valeur deltaT de 0,05 à 0,5 et la valeur writeInterval de 0,05 à 0,5.
   
   ![Modifier les variables d’étape][step_variables]
5. Spécifiez les valeurs souhaitées pour les variables dans le fichier system/decomposeParDict. Dans cet exemple, deux nœuds Linux de 8 cœurs chacun sont utilisés. Définissez donc la valeur numberOfSubdomains sur 16, et le n de hierarchicalCoeffs sur (1 1 16), ce qui signifie qu’OpenFOAM doit s’exécuter en parallèle avec 16 processus. Pour plus d’informations, consultez [Guide de l’utilisateur OpenFOAM : 3.4 Exécution d’applications en parallèle](http://cfd.direct/openfoam/user-guide/running-applications-parallel/#x12-820003.4).
   
   ![Décomposer les processus][decompose]
6. Exécutez les commandes suivantes à partir du répertoire sloshingTank3D pour préparer les exemples de données.
   
   ```
   $ . $WM_PROJECT_DIR/bin/tools/RunFunctions
   
   $ m4 constant/polyMesh/blockMeshDict.m4 > constant/polyMesh/blockMeshDict
   
   $ runApplication blockMesh
   
   $ cp 0/alpha.water.org 0/alpha.water
   
   $ runApplication setFields  
   ```
7. Sur le nœud principal, vous devez voir que les exemples de fichiers de données sont copiés dans C:\OpenFoam\sloshingTank3D. (C:\OpenFoam est le dossier partagé sur le nœud principal.)
   
   ![Fichiers de données sur le nœud principal][data_files]

### <a name="host-file-for-mpirun"></a>Fichier hôte pour mpirun
Au cours de cette étape, vous créez un fichier hôte (une liste de nœuds de calcul) que la commande **mpirun** utilise.

1. Sur l’un des nœuds Linux, créez un fichier nommé hostfile sous /openfoam, afin que ce fichier soit accessible dans /openfoam/hostfile sur tous les nœuds Linux.
2. Écrivez vos noms de nœud Linux dans ce fichier. Dans cet exemple, le fichier contient les noms suivants :
   
   ```       
   SUSE12RDMA-LN1
   SUSE12RDMA-LN2
   ```
   
   > [!TIP]
   > Vous pouvez également créer ce fichier dans C:\OpenFoam\hostfile sur le nœud principal. Si vous optez pour cette solution, enregistrez le fichier au format texte avec des fins de lignes Linux (LF uniquement, pas CR LF). Cela garantit qu’il s’exécute correctement sur les nœuds Linux.
   > 
   > 
   
   **Wrapper de script d’interpréteur de commandes**
   
   Si vous disposez de plusieurs nœuds Linux et voulez que votre tâche s’exécute uniquement sur certains d’entre eux, nous vous recommandons d’utiliser un fichier hôte fixe, car vous ignorez les nœuds qui seront alloués à votre tâche. Dans ce cas, écrivez un wrapper de script Bash pour **mpirun** afin de créer le fichier hôte automatiquement. Vous pouvez trouver un exemple de wrapper de script bash nommé hpcimpirun.sh à la fin de cet article, et l’enregistrer en tant que /openfoam/hpcimpirun.sh. L’exemple de script effectue les opérations suivantes :
   
   1. Configure les variables d’environnement pour **mpirun**, et certains paramètres de commande supplémentaires pour exécuter le travail MPI via le réseau RDMA. Dans ce cas, il définit les variables suivantes :
      
      * I_MPI_FABRICS=shm:dapl
      * I_MPI_DAPL_PROVIDER=ofa-v2-ib0
      * I_MPI_DYNAMIC_CONNECTION=0
   2. Crée un fichier d’hôte en fonction de la variable d’environnement $CCP_NODES_CORES définie par le nœud principal HPC lorsque le travail est activé.
      
      Le format de $CCP_NODES_CORES suit ce modèle :
      
      ```
      <Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
      ```
      
      où
      
      * `<Number of nodes>` : nombre de nœuds affectés à ce travail.  
      * `<Name of node_n_...>` : nom de chaque nœud affecté à ce travail.
      * `<Cores of node_n_...>` : nombre de cœurs présents sur le nœud affecté à ce travail.
      
      Par exemple, si l’exécution de la tâche requiert deux nœuds, la valeur de $CCP_NODES_CORES se présente comme suit
      
      ```
      2 SUSE12RDMA-LN1 8 SUSE12RDMA-LN2 8
      ```
   3. Appelle la commande **mpirun** , et ajoute deux paramètres à la ligne de commande.
      
      * `--hostfile <hostfilepath>: <hostfilepath>` : le chemin d’accès du fichier hôte que le script crée
      * `-np ${CCP_NUMCPUS}: ${CCP_NUMCPUS}` : une variable d’environnement définie par le nœud principal HPC Pack, qui stocke le nombre total de cœurs affectés à ce travail. Dans ce cas, elle spécifie le nombre de processus pour **mpirun**.

## <a name="submit-an-openfoam-job"></a>Envoi d’un travail OpenFOAM
Maintenant, vous pouvez envoyer un travail dans HPC Cluster Manager. Vous devez transférer le script hpcimpirun.sh dans les lignes de commande pour certaines des tâches du travail.

1. Connectez-vous à votre nœud principal de cluster et démarrez HPC Cluster Manager.
2. Dans **Gestion des ressources**, assurez-vous que les nœuds de calcul Linux sont à l’état **En ligne**. Si ce n’est pas le cas, sélectionnez-les et cliquez sur **Mettre en ligne**.
3. Dans **Gestion des tâches**, cliquez sur **Nouveau travail**.
4. Entrez un nom pour le travail, par exemple *sloshingTank3D*.
   
   ![Détails du travail][job_details]
5. Dans les **ressources de la tâche**, sélectionnez le type de ressource en tant que « Nœud » et définissez la valeur minimale sur 2. Cette configuration exécute la tâche sur deux nœuds Linux, chacun d’eux comportant huit cœurs dans cet exemple.
   
   ![ressources de la tâche][job_resources]
6. Cliquez sur **Modifier des tâches** dans le volet de navigation gauche, puis sur **Ajouter** pour ajouter une tâche au travail. Ajoutez quatre tâches au travail avec les lignes de commande et les paramètres suivants.
   
   > [!NOTE]
   > L’exécution de  `source /openfoam/settings.sh` configure les environnements d’exécution OpenFOAM et MPI pour que chacune des tâches suivantes l’appelle avant la commande OpenFOAM.
   > 
   > 
   
   * **Tâche 1**. Exécutez **decomposePar** pour générer des fichiers de données pour l’exécution de **interDyMFoam** en parallèle.
     
     * Affecter un nœud à la tâche
     * **Ligne de commande** - `source /openfoam/settings.sh && decomposePar -force > /openfoam/decomposePar${CCP_JOBID}.log`
     * **Répertoire de travail** - /openfoam/sloshingTank3D
     
     Voir la figure suivante. Vous configurez les tâches restantes de la même manière.
     
     ![Détails de la tâche 1][task_details1]
   * **Tâche 2**. Exécutez **interDyMFoam** en parallèle pour calculer l’exemple.
     
     * Affectez deux nœuds à la tâche
     * **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh interDyMFoam -parallel > /openfoam/interDyMFoam${CCP_JOBID}.log`
     * **Répertoire de travail** - /openfoam/sloshingTank3D
   * **Tâche 3**. Exécutez la commande **reconstructPar** pour fusionner les jeux de répertoires temporels à partir de chaque répertoire processor_N en un seul jeu.
     
     * Affecter un nœud à la tâche
     * **Ligne de commande** - `source /openfoam/settings.sh && reconstructPar > /openfoam/reconstructPar${CCP_JOBID}.log`
     * **Répertoire de travail** - /openfoam/sloshingTank3D
   * **Tâche 4**. Exécutez **foamToEnsight** en parallèle pour convertir les fichiers de résultats OpenFOAM au format EnSight et placez les fichiers EnSight dans un répertoire nommé Ensight dans le répertoire du cas.
     
     * Affectez deux nœuds à la tâche
     * **Ligne de commande** - `source /openfoam/settings.sh && /openfoam/hpcimpirun.sh foamToEnsight -parallel > /openfoam/foamToEnsight${CCP_JOBID}.log`
     * **Répertoire de travail** - /openfoam/sloshingTank3D
7. Ajoutez des dépendances à ces tâches dans l’ordre croissant des tâches.
   
   ![Dépendances de la tâche][task_dependencies]
8. Cliquez sur **Envoyer** pour exécuter ce travail.
   
   Par défaut, HPC Pack envoie la tâche en tant que votre compte d’utilisateur connecté actuel. Une fois que vous avez cliqué sur **Envoyer**, une boîte de dialogue peut vous inviter à entrer le nom d’utilisateur et le mot de passe.
   
   ![Informations d’identification de la tâche][creds]
   
   Dans certaines conditions, HPC Pack mémorise les informations utilisateur entrées auparavant et n’affiche plus cette boîte de dialogue. Pour que HPC Pack l’affiche de nouveau, entrez les informations suivantes à l’invite de commande, puis envoyez le travail.
   
   ```
   hpccred delcreds
   ```
9. Ce travail prend de quelques dizaines de minutes à plusieurs heures en fonction des paramètres que vous avez définis pour l’exemple. Sur la carte thermique, vous voyez le travail s’exécutant sur les nœuds Linux. 
   
   ![Carte thermique][heat_map]
   
   Sur chaque nœud, huit processus sont démarrés.
   
   ![Processus Linux][linux_processes]
10. Lorsque le travail se termine, recherchez les résultats du travail dans les dossiers sous C:\OpenFoam\sloshingTank3D et les fichiers journaux dans C:\OpenFoam.

## <a name="view-results-in-ensight"></a>Afficher les résultats dans EnSight
Vous pouvez également utiliser [EnSight](https://www.ceisoftware.com/) pour visualiser et analyser les résultats du travail OpenFOAM. Pour plus d’informations sur la visualisation et l’animation dans EnSight, consultez le [guide vidéo](http://www.ceisoftware.com/wp-content/uploads/screencasts/vof_visualization/vof_visualization.html).

1. Après avoir installé EnSight sur le nœud principal, démarrez le service.
2. Ouvrez C:\OpenFoam\sloshingTank3D\EnSight\sloshingTank3D.case.
   
   Vous verrez une citerne dans la visionneuse.
   
   ![Réservoir dans EnSight][tank]
3. Créez une **Isosurface** à partir de **internalMesh**, puis choisissez la variable **alpha_water**.
   
   ![Créer une isosurface][isosurface]
4. Définissez la couleur de l’élément **Isosurface_part** créé à l’étape précédente. Par exemple, définissez la sur bleu océan.
   
   ![Modifier la couleur de l’isosurface][isosurface_color]
5. Créez un **Isovolume** à partir des **parois** en sélectionnant **parois** dans le panneau **Pièces**, puis cliquez sur le bouton **Isosurfaces** dans la barre d’outils.
6. Dans la boîte de dialogue, sélectionnez **Type** en tant qu’**Isovolume** et définissez la **plage Isovolume** minimale sur 0,5. Pour créer l’isovolume, cliquez sur **Créer avec des pièces sélectionnées**.
7. Définissez la couleur de l’élément **Iso_volume_part** créé à l’étape précédente. Par exemple, définissez sur bleu profond.
8. Définissez la couleur des **parois**. Par exemple, définissez-le sur blanc transparent.
9. Cliquez maintenant sur **Lire** pour afficher les résultats de la simulation.
   
    ![Résultat du réservoir][tank_result]

## <a name="sample-files"></a>Exemple de fichiers
### <a name="sample-xml-configuration-file-for-cluster-deployment-by-powershell-script"></a>Exemple de fichier de configuration XML pour le déploiement de clusters par script PowerShell
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
### <a name="sample-silentcfg-file-to-install-mpi"></a>Exemple de fichier silent.cfg pour installer MPI
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

### <a name="sample-settingssh-script"></a>Exemple de script settings.sh
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


### <a name="sample-hpcimpirunsh-script"></a>Exemple de script hpcimpirun.sh
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



<!--HONumber=Nov16_HO3-->


