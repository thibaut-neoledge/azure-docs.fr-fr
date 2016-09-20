<properties
 pageTitle="Cluster Linux RDMA pour exécuter des applications MPI | Microsoft Azure"
 description="Créer un cluster Linux de machines virtuelles de taille A8 ou A9 pour utiliser RDMA afin d’exécuter des applications MPI"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# Configuration d’un cluster Linux RDMA pour exécuter des applications MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Apprenez à configurer un cluster Linux RDMA dans Azure avec des machines virtuelles de taille A8 et A9 pour exécuter des applications MPI (Message Passing Interface) parallèles. Lorsque des machines virtuelles de taille A8 et A9 exécutent une distribution HPC Linux et une implémentation MPI prises en charge, les applications MPI communiquent efficacement sur un réseau à latence faible et à débit élevé, reposant sur la technologie d’accès direct à la mémoire à distance (RDMA).

>[AZURE.NOTE] La prise en charge de le technologie RDMA Azure Linux requiert actuellement une bibliothèque Intel MPI version 5 sur les machines virtuelles créées à partir de SUSE Linux Enterprise Server 12 HPC ou basée sur des images HPC CentOS 6.5 ou 7.1 dans Azure Marketplace. Pour plus d’informations et de considérations, voir [À propos des instances A8, A9, A10 et A11](virtual-machines-linux-a8-a9-a10-a11-specs.md).



## Options de déploiement de cluster

Voici des méthodes que vous pouvez utiliser pour créer un cluster Linux RDMA avec ou sans planificateur de tâches.

* **HPC Pack** : créez un cluster Microsoft HPC Pack dans Azure et ajouter des nœuds de calcul de taille A8 ou A9 qui exécutent des distributions Linux prises en charge pour accéder au réseau RDMA. Consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

* **Scripts CLI azure** : comme indiqué plus loin dans cet article, utilisez l’[interface de ligne de commande Azure](../xplat-cli-install.md) (CLI) pour créer un script pour le déploiement d’un cluster de machines virtuelles de taille A8 ou A9. La CLI en mode Service Management crée les nœuds de cluster en série dans le modèle de déploiement classique. Le déploiement d’un grand nombre de nœuds de calcul peut donc prendre plusieurs minutes. Dans le modèle de déploiement classique, des machines virtuelles de taille A8 ou A9 doivent être déployées dans le même service cloud pour la connexion via le réseau RDMA.

* **Modèles Azure Resource Manager** : utilisez le modèle de déploiement Resource Manager pour déployer un cluster de machines virtuelles A8 et A9 qui se connecte au réseau RDMA pour exécuter des charges de travail MPI. Vous pouvez [créer votre propre modèle](../resource-group-authoring-templates.md) ou consulter la page [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) pour trouver des modèles fournis par Microsoft ou la communauté, pour déployer la solution de votre choix. Les modèles Resource Manager peuvent fournir un moyen rapide et fiable pour déployer un cluster Linux. Dans le modèle de déploiement Resource Manager, les machines virtuelles A8 ou A9 doivent être déployées dans le même groupe à haute disponibilité pour se connecter via le réseau RDMA.

## Exemple de déploiement en modèle classique

Les étapes suivantes montrent comment utiliser la CLI Azure pour déployer une machine virtuelle HPC SUSE Linux Enterprise Server (SLES) 12 à partir d’Azure Marketplace, installer la bibliothèque Intel MPI et créer une image de machine virtuelle personnalisée. Utilisez ensuite l’image pour créer un script de déploiement d’un cluster de machines virtuelles A8 ou A9.

>[AZURE.TIP]  Utilisez une procédure similaire pour déployer un cluster de machines virtuelles A8 ou A9 en fonction basées sur l’image HPC basée sur CentOS 6.5 ou 7.1 dans Azure Marketplace. Il y a quelques différences. Par exemple, étant donné que les images HPC basées sur CentOS incluent Intel MPI, il est inutile d’installer Intel MPI séparément sur les machines virtuelles créées à partir de ces images.

### Conditions préalables

*   **Ordinateur client** : vous avez besoin d’un ordinateur client Mac, Linux ou Windows pour communiquer avec Azure. Ces étapes supposent que vous utilisez un client Linux.

*   **Abonnement Azure** : si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes. Pour les clusters de grande taille, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat.

*   **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs pour déployer un cluster de machines virtuelles A8 ou A9. Par exemple, vous devez avoir au moins 128 cœurs si vous souhaitez déployer 8 machines virtuelles A9, comme indiqué dans cet article. Pour augmenter un quota, [ouvrez une demande de service clientèle en ligne](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.

*   **Interface de ligne de commande Azure** : [installez](../xplat-cli-install.md) l’interface de ligne de commande (CLI) Azure et [connectez-vous à votre abonnement Azure](../xplat-cli-connect.md) sur l’ordinateur client.

*   **MPI Intel** : pour personnaliser une image de machine virtuelle SLES 12 HPC pour votre cluster, vous devez télécharger et installer le runtime Intel MPI Library 5 actuel à partir du [site Intel.com](https://software.intel.com/fr-FR/intel-mpi-library/). Les détails figurent plus loin dans cet article. Pour préparer l’installation, après vous être inscrit auprès d’Intel, copiez le lien de téléchargement du fichier .tgz correspondant à la version appropriée d’Intel MPI. Cet article est basé sur Intel MPI version 5.0.3.048.

    >[AZURE.NOTE] Si vous utilisez l’image HPC CentOS 6.5 ou CentOS 7.1 dans Azure Marketplace pour créer vos nœuds de cluster, Intel MPI version 5.1.3.181 est préinstallé.

### Mise en service d’une machine virtuelle SLES 12

Après la connexion à Azure avec l’interface de ligne de commande Azure, exécutez la commande `azure config list` pour confirmer que la sortie affiche le mode Service Management. Si ce n'est pas le cas, définissez le mode en exécutant la commande suivante :


    azure config mode asm


Tapez la commande suivante pour répertorier tous les abonnements que vous êtes autorisé à utiliser :


    azure account list

L’abonnement actif actuel est identifié par `Current` avec la valeur `true`. S’il ne s’agit pas de l’abonnement que vous souhaitez utiliser pour créer le cluster, définissez l’ID d’abonnement approprié comme abonnement actif :

    azure account set <subscription-Id>

Pour afficher les images SLES 12 HPC publiquement disponibles dans Azure, exécutez une commande similaire à celle qui suit, si votre environnement d'interpréteur de commandes prend en charge **grep** :


    azure vm image list | grep "suse.*hpc"

À présent, configurez une machine virtuelle de taille A9 avec une image SLES 12 HPC en exécutant une commande semblable à la suivante :

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708

où

* La taille (A9 dans cet exemple) peut être A8 ou A9

* Le numéro de port SSH externe (22 dans cet exemple, qui est le SHH par défaut) est un numéro de port valide. Le numéro de port SSH interne est défini sur 22

* Un service cloud est créé dans la région Azure spécifiée par l’emplacement. Spécifiez un emplacement dans lequel les instances A8 et A9 sont disponibles.

* Le nom de l’image SLES 12 peut actuellement être `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` pour la prise en charge de la priorité SUSE (des frais supplémentaires s’appliquent)

    >[AZURE.NOTE]Si vous voulez utiliser une image HPC basée sur CentOS, les noms actuels des images sont `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-65-HPC-20160408` et `5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-71-HPC-20160408`.

### Personnalisation de la machine virtuelle

Après le provisionnement de la machine virtuelle, utilisez SSH pour vous connecter à la machine virtuelle à l’aide de l’adresse IP externe de la machine virtuelle (ou du nom DNS) et du numéro de port externe configuré et personnalisez-la. Pour plus d'informations sur la connexion, consultez [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-mac-create-ssh-keys.md). Exécutez les commandes comme l’utilisateur que vous avez configuré sur la machine virtuelle, à moins qu’un accès racine soit nécessaire pour accomplir une étape.

>[AZURE.IMPORTANT]Microsoft Azure ne fournit pas d'accès racine aux machines virtuelles Linux. Pour obtenir un accès administratif lorsque vous êtes connecté en tant qu'utilisateur à la machine virtuelle, exécutez les commandes avec `sudo`.

* **Mises à jour** : installez les mises à jour à l'aide de **zypper**. Vous pouvez également installer les utilitaires NFS.

    >[AZURE.IMPORTANT]Si vous avez déployé une machine virtuelle SLES 12 HPC, nous vous recommandons de ne pas appliquer les mises à jour du noyau, qui peuvent provoquer des problèmes avec les pilotes RDMA Linux.
    >
    >Sur les images HPC basées sur CentOS provenant du Marketplace, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum**. Du fait que les pilotes RDMA Linux sont distribués sous forme de package RPM, les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.

* **Mises à jour des pilotes RDMA Linux** : si vous avez déployé une machine virtuelle SLES 12 HPC, vous devez mettre à jour les pilotes RDMA. Pour plus d’informations, consultez [À propos des instances de calcul intensif A8, A9, A10 et A11](virtual-machines-linux-a8-a9-a10-a11-specs.md#rdma-driver-updates-for-sles-12).

* **Intel MPI** : si vous avez déployé une machine virtuelle HPC SLES 12, téléchargez et installez le runtime Intel MPI Library 5 en exécutant des commandes semblables à ce qui suit. Cette étape n’est pas nécessaire si vous avez déployé une machine virtuelle HPC basée sur CentOS 6.5 ou 7.1.

        sudo wget <download link for your registration>

        sudo tar xvzf <tar-file>

        cd <mpi-directory>

        sudo ./install.sh

    Acceptez les paramètres par défaut pour installer Intel MPI sur la machine virtuelle.

* **Verrouiller la mémoire** : pour que les codes MPI verrouillent la mémoire disponible pour RDMA, vous devez ajouter ou modifier les paramètres suivants dans le fichier /etc/security/limits.conf. (Un accès racine est requis pour modifier ce fichier.) Si vous avez déployé une machine virtuelle HPC basée sur CentOS 6.5 ou 7.1, les paramètres sont déjà ajoutés à ce fichier.

        <User or group name> hard    memlock <memory required for your application in KB>

        <User or group name> soft    memlock <memory required for your application in KB>

    >[AZURE.NOTE]À des fins de test, vous pouvez également définir memlock comme illimité. Par exemple : <nom d’utilisateur ou du groupe> hard memlock unlimited.

* **Clés SSH pour les machines virtuelles SLES 12** : générez des clés SSH pour établir l’approbation de votre compte utilisateur entre les nœuds de calcul du cluster HPC SLES 12 lors de l’exécution des travaux MPI. (Si vous avez déployé une machine virtuelle HPC basée sur CentOS, ne suivez pas cette étape. Consultez les instructions de la suite de cet article pour définir une confiance SSH sans mot de passe entre les nœuds du cluster une fois que vous avez capturé l’image et déployé le cluster).

    Exécutez la commande suivante pour créer des clés SSH. Lorsque vous y êtes invité, appuyez sur Entrée pour générer les clés dans l’emplacement par défaut sans définir de phrase secrète.

        ssh-keygen

    Ajoutez la clé publique au fichier authorized\_keys pour les clés publiques connues.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    Dans l’annuaire ~/.ssh, modifiez ou créez le fichier « config ». Fournissez la plage d’adresses IP du réseau privé que vous prévoyez d’utiliser dans Azure (10.32.0.0/16 dans cet exemple) :

        host 10.32.0.*
        StrictHostKeyChecking no

    Vous pouvez également répertorier l'adresse IP du réseau privé de chaque machine virtuelle dans votre cluster comme suit :

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]La configuration de `StrictHostKeyChecking no` peut créer un risque de sécurité potentiel si une adresse IP ou une plage d’adresses IP spécifiques ne sont pas spécifiées.

* **Applications** : installez les applications dont vous avez besoin sur cette machine virtuelle ou effectuez d'autres personnalisations avant de capturer l'image.

### capture de l’image

Pour capturer l'image, exécutez d’abord la commande suivante dans la machine virtuelle Linux. Cette commande désaffecte la machine virtuelle, mais conserve les comptes utilisateurs et les clés SSH que vous avez configurés.

```
sudo waagent -deprovision
```

Puis, depuis votre ordinateur client, exécutez les commandes CLI Azure suivantes pour capturer l’image. Pour plus d’informations, consultez la page [Capture d’une machine virtuelle Linux classique en tant qu’image](virtual-machines-linux-classic-capture-image.md).

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Après avoir exécuté ces commandes, l'image de machine virtuelle est capturée pour que vous puissiez l'utiliser et la machine virtuelle est supprimée. Vous disposez maintenant d’une image personnalisée prête à être déployée sur un cluster.

### Déploiement d’un cluster avec l'image

Modifiez le script Bash suivant avec les valeurs appropriées pour votre environnement et exécutez-le à partir de votre ordinateur client. Dans la mesure où Azure déploie les machines virtuelles en série dans le modèle de déploiement Service Management, quelques minutes sont nécessaires pour déployer les 8 machines virtuelles A9 suggérées dans ce script.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Select a region where A8 and A9 VMs are available, such as West US
# See Azure Pricing pages for prices and availability of A8 and A9 VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the A8 and A9 instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## Confiance SSH sans mot de passe sur un cluster CentOS

Si vous avez déployé un cluster à l’aide d’une image HPC basée sur CentOS, il existe deux méthodes pour établir une confiance entre les nœuds de calcul : l’authentification basée sur l’hôte et l’authentification basée sur l’utilisateur. L’authentification basée sur l’hôte n’est pas expliquée dans cet article. Elle doit généralement être configurée à l’aide d’un script d’extension pendant le déploiement. L’authentification basée sur l’utilisateur est pratique pour établir une confiance après le déploiement, et nécessite la génération et le partage de clés SSH entre les nœuds de calcul dans le cluster. Cette méthode est communément appelée connexion SSH sans mot de passe. Elle est requise lors de l’exécution de travaux MPI.

Un exemple de script fourni par la communauté est disponible sur [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) pour permettre une authentification utilisateur simple sur un cluster HPC basé sur CentOS. Vous pouvez télécharger et utiliser ce script en procédant comme suit. Vous pouvez également modifier ce script ou utiliser toute autre méthode permettant d’établir une authentification SSH sans mot de passe entre les nœuds de calcul du cluster.

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh
    
Pour exécuter le script, vous devez connaître le préfixe des adresses IP de votre sous-réseau. Vous pouvez obtenir ces informations en exécutant la commande suivante sur l’un des nœuds du cluster. Votre résultat doit ressembler à 10.1.3.5, et le préfixe est 10.1.3.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Ensuite, exécutez le script à l’aide de trois paramètres : le nom d’utilisateur courant utilisé sur les nœuds de calcul, le mot de passe pour cet utilisateur sur les nœuds de calcul et le préfixe de sous-réseau renvoyé par la commande précédente.


    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Ce script effectue les opérations suivantes :

* Crée sur le nœud hôte un répertoire nommé .ssh, qui est requis pour la connexion sans mot de passe.
* Crée un fichier de configuration dans le répertoire .ssh, qui indique à la méthode de connexion sans mot de passe d’autoriser la connexion à partir de n’importe quel nœud du cluster.
* Crée des fichiers contenant les noms et adresses IP de chacun des nœuds du cluster. Ces fichiers sont conservés après l’exécution du script à des fins de référence ultérieure.
* Crée une paire de clés privée et publique pour chaque nœud du cluster, y compris le nœud hôte, et crée des entrées dans le fichier authorized\_keys.

>[AZURE.WARNING]L’exécution de ce script peut créer un risque de sécurité potentiel. Assurez-vous que les informations de clé publique dans ~/.ssh ne sont pas distribuées.


## Configuration et exécution d’Intel MPI

Pour exécuter des applications MPI sur RDMA Azure Linux, vous devez configurer certaines variables d'environnement spécifiques à Intel MPI. Voici un exemple de script Bash pour configurer les variables et exécuter une application. Modifiez le chemin d’accès à mpivars.sh selon les besoins de votre installation d’Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh


export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Le format du fichier hôte est le suivant. Ajoutez une ligne pour chaque nœud de votre cluster. Spécifiez des adresses IP privées à partir du réseau virtuel défini précédemment, pas des noms DNS. Par exemple, pour deux hôtes avec les adresses IP 10.32.0.1 et 10.32.0.2, le fichier contient les éléments suivants :

```
10.32.0.1:16
10.32.0.2:16
```

## Vérification d’un cluster de base à deux nœuds après l’installation d’Intel MPI

Si ce n'est pas déjà fait, commencez par configurer l'environnement pour Intel MPI.

```
# For a SLES 12 HPC cluster

source /opt/intel/impi_latest/bin64/mpivars.sh

# For a CentOS-based HPC cluster 

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### Exécution d'une commande MPI simple

Exécutez une commande MPI simple sur l'un des nœuds de calcul pour montrer que MPI est installé correctement et peut communiquer entre au moins deux nœuds de calcul. La commande **mpirun** suivante exécute la commande **hostname** sur deux nœuds.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Votre sortie doit répertorier les noms de tous les nœuds que vous avez transmis comme entrée pour `-hosts`. Par exemple, une commande **mpirun** avec deux nœuds renvoie un résultat semblable au suivant :

```
cluster11
cluster12
```

### Exécution d'un test d'évaluation MPI

La commande Intel MPI suivante vérifie la configuration du cluster et sa connexion au réseau RDMA à l'aide d'un banc d'essai pingpong.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Vous devez voir une sortie similaire à ce qui suit sur un cluster opérationnel avec deux nœuds. Sur le réseau Azure RDMA, une latence égale ou inférieure à 3 microsecondes est attendue pour les tailles de messages allant jusqu'à 512 octets.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## Étapes suivantes

* Essayez de déployer et d’exécuter vos applications MPI Linux sur votre cluster Linux.

* Consultez la [documentation de la bibliothèque Intel MPI](https://software.intel.com/fr-FR/articles/intel-mpi-library-documentation/) pour obtenir des conseils sur Intel MPI.

* Essayez un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) pour créer un cluster Intel Lustre en utilisant une image HPC basée sur CentOS. Pour plus de détails, voir ce [billet de blog](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).

<!---HONumber=AcomDC_0907_2016-->