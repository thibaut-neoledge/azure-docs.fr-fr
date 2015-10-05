<properties
 pageTitle="Cluster Linux RDMA pour exécuter des applications MPI | Microsoft Azure"
 description="Créer un cluster Linux de machines virtuelles de taille A8 ou A9 pour utiliser RDMA afin d'exécuter des applications MPI."
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
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2015"
 ms.author="danlep"/>

# Configuration d’un cluster Linux RDMA pour exécuter des applications MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la configuration de ressources avec le modèle de déploiement classique.

Cet article vous montre comment configurer un cluster Linux RDMA dans Azure avec [des machines virtuelles de taille A8 et A9](virtual-machines-a8-a9-a10-a11-specs.md) pour exécuter des applications MPI (Message Passing Interface) parallèles. Lorsque vous configurez des machines virtuelles Linux de taille A8 et A9 pour exécuter une implémentation MPI prise en charge, les applications MPI communiquent efficacement sur un réseau à latence faible et à débit élevé dans Azure, reposant sur la technologie d’accès direct à la mémoire à distance (RDMA) .

>[AZURE.NOTE]Le RDMA Linux Azure est actuellement pris en charge par la bibliothèque Intel MPI version 5 s'exécutant sur SUSE Linux Enterprise Server 12 (SLES 12). Cet article est basé sur Intel MPI version 5.0.3.048.
>
> Azure fournit également des instances à calcul intensif A10 et A11, avec des fonctionnalités de traitement identiques aux instances A8 et A9, mais sans connexion à un réseau de serveur principal RDMA. Pour exécuter des charges de travail MPI dans Azure, vous obtenez généralement des performances optimales avec les instances A8 et A9.


## Options de déploiement de cluster Linux

Voici des méthodes que vous pouvez utiliser pour créer un cluster Linux RDMA avec ou sans planificateur de tâches.

* **HPC Pack** : créez un cluster Microsoft HPC Pack dans Azure et ajoutez des nœuds de calcul qui exécutent des distributions Linux prises en charge (la prise en charge de nœud de calcul Linux démarre à partir de HPC Pack 2012 R2 Mise à jour 2). Certains nœuds Linux peuvent être configurés pour accéder au réseau RDMA. Consultez [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-cluster.md).

* **Scripts CLI azure** : comme indiqué dans les étapes dans le reste de cet article, utilisez l'[Interface de ligne de commande Azure](../xplat-cli.md) (CLI) pour Mac, Linux et Windows pour créer des scripts afin de déployer un réseau virtuel et les autres composants nécessaires pour la création d'un cluster Linux. La CLI en mode de déploiement classique (Service Management) crée les nœuds de cluster en série : si vous déployez un grand nombre de nœuds de calcul, plusieurs minutes peuvent être nécessaires pour terminer le déploiement.

* **Modèles Azure Resource Manager** : en créant un simple fichier de modèle JSON Azure Resource Manager et en exécutant des commandes CLI Azure pour Resource Manager ou à l'aide du portail Azure en version préliminaire, déployez plusieurs machines virtuelles Linux A8 et A9 et définissez des réseaux virtuels, des adresses IP statiques, des paramètres DNS et d'autres ressources pour créer un cluster de calcul qui peut tirer parti du réseau RDMA pour exécuter des charges de travail MPI. Vous pouvez [créer votre propre modèle](../resource-group-authoring-templates.md) ou consulter la [page Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates/) pour trouver des modèles fournis par Microsoft ou la communauté, pour déployer la solution de votre choix. Les modèles Resource Manager fournissent généralement le moyen le plus rapide et le plus fiable de déploiement d’un cluster Linux.

## Déploiement dans Azure Service Management avec des scripts CLI Azure

Les étapes suivantes vous aident à utiliser la CLI Azure pour déployer une machine virtuelle SLES 12, à installer la bibliothèque Intel MPI et d'autres personnalisations, à créer une image de machine virtuelle personnalisée, puis à générer un script de déploiement d’un cluster de machines virtuelles A8 ou A9.

### Composants requis

*   **Ordinateur client** : vous aurez besoin d'un ordinateur client Mac, Linux ou Windows pour communiquer avec Azure. Ces étapes supposent que vous utilisez un client Linux.

*   **Un abonnement Azure** : si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).

*   **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs pour déployer un cluster de machines virtuelles A8 ou A9. Par exemple, vous devez avoir au moins 128 cœurs si vous souhaitez déployer 8 machines virtuelles A9, comme indiqué dans cet article. Pour augmenter un quota, [ouvrez une demande de service clientèle en ligne](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.

*   **CLI azure** : [installez](../xplat-cli-install.md) la CLI Azure et [configurez-la](../xplat-cli-connect.md) pour vous connecter à votre abonnement Azure sur l'ordinateur client.

*   **MPI Intel** : dans le cadre de la personnalisation d'une image de machine virtuelle Linux pour votre cluster (voir les détails plus loin dans cet article), vous devez télécharger et installer le runtime Intel MPI Library 5 à partir du [site Intel.com](https://software.intel.com/fr-FR/intel-mpi-library/) sur une machine virtuelle Azure Linux que vous approvisionnez. Pour vous y préparer, après votre inscription auprès d'Intel, suivez le lien dans le message de confirmation vers la page web associée et copiez le lien de téléchargement du fichier .tgz pour la version appropriée d'Intel MPI. Cet article est basé sur Intel MPI version 5.0.3.048.

### Mise en service d’une machine virtuelle SLES 12

Après la connexion à Azure avec la CLI Azure, exécutez la commande `azure config list` pour confirmer que la sortie affiche le mode **asm**, indiquant que la CLI est en mode Azure Service Management. Si ce n'est pas le cas, définissez le mode en exécutant la commande suivante :

```
azure config mode asm
```

Tapez la commande suivante pour répertorier tous les abonnements que vous êtes autorisé à utiliser :

```
azure account list
```

L'abonnement actif actuel est identifié par `Current`, avec la valeur `true`. Si ce n'est pas celui que vous souhaitez utiliser pour créer le cluster, définissez le numéro d'abonnement pertinent comme abonnement actif :

```
azure account set <subscription-number>
```

Pour afficher les images SLES 12 HPC publiquement disponibles dans Azure, exécutez une commande similaire à celle qui suit, si votre environnement d'interpréteur de commandes prend en charge **grep** :

```
azure vm image list | grep "suse.*hpc"
```

>[AZURE.NOTE]Les images SLES 12 HPC sont préconfigurés avec les pilotes Linux RDMA nécessaires pour Azure.

À présent, configurez une machine virtuelle de taille A9 avec une image SLES 12 HPC disponible en exécutant une commande semblable à la suivante :

```
azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708
```

où

* la taille (A9 dans cet exemple) peut être A8 ou A9

* le numéro de port SSH externe (22 dans cet exemple, qui est la valeur SSH par défaut) est un numéro de port valide ; le numéro de port SSH interne est défini sur 22

* un service cloud sera créé dans la région Azure spécifiée par l'emplacement ; spécifiez un emplacement tel que « Ouest des États-Unis » dans laquelle les instances A8 et A9 sont disponibles

* le nom de l'image peut actuellement être `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708` (gratuit) ou `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-priority-v20150708` pour la prise en charge de la priorité SUSE (des frais sont appliqués)

### Personnalisation de la machine virtuelle

Après le provisionnement de la machine virtuelle, utilisez SSH pour vous connecter à la machine virtuelle à l’aide de l’adresse IP externe de la machine virtuelle (ou du nom DNS) et du numéro de port externe configuré et personnalisez-la. Pour plus d'informations sur la connexion, consultez [Connexion à une machine virtuelle exécutant Linux](virtual-machines-linux-how-to-log-on.md). Vous devez exécuter les commandes comme l'utilisateur que vous avez configuré sur la machine virtuelle, à moins que l'accès racine ne soit nécessaire pour terminer une étape.

>[AZURE.IMPORTANT]Microsoft Azure ne fournit pas d'accès racine aux machines virtuelles Linux. Pour obtenir un accès administratif lorsque vous êtes connecté en tant qu'utilisateur à la machine virtuelle, exécutez les commandes avec `sudo`.

*   **Mises à jour** : installez les mises à jour à l'aide de **zypper**. Vous pouvez également installer les utilitaires NFS.  

    >[AZURE.IMPORTANT]À l’heure actuelle, nous vous recommandons de ne pas appliquer les mises à jour du noyau, qui peuvent provoquer des problèmes avec les pilotes RDMA Linux.

*   **Intel MPI** : téléchargez et installez le runtime Intel MPI Library 5 en exécutant des commandes semblables à ce qui suit.

    ```
    sudo wget <download link for your registration>

    sudo tar xvzf <tar-file>

    cd <mpi-directory>

    sudo ./install.sh

    ```

    Acceptez les paramètres par défaut pour installer Intel MPI sur la machine virtuelle.

*   **Verrouiller la mémoire** : pour que les codes MPI verrouillent la mémoire disponible pour RDMA, vous devez ajouter ou modifier les paramètres suivants dans le fichier /etc/security/limits.conf. (L'accès racine est requis pour modifier ce fichier.)

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

    >[AZURE.NOTE]À des fins de test, vous pouvez également définir memlock comme illimité. Par exemple : `<User or group name>    hard    memlock unlimited`.


*   **Clés SSH** : générez des clés SSH pour établir l'approbation de votre compte utilisateur entre tous les nœuds de calcul du cluster lors de l'exécution des travaux MPI. Exécutez la commande suivante pour créer des clés SSH. Appuyez sur Entrée pour générer les clés dans l'emplacement par défaut sans définir une phrase secrète.

    ```
    ssh-keygen
    ```

    Ajoutez la clé publique au fichier authorized\_keys pour les clés publiques connues.

    ```
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
    ```

    Dans le répertoire ~/.ssh, modifiez ou créez le fichier ssh\_config. Fournissez la plage d'adresses IP du réseau privé que vous utiliserez dans Azure (10.32.0.0/16 dans cet exemple) :

    ```
    host 10.32.0.*
    StrictHostKeyChecking no
    ```

    Vous pouvez également répertorier l'adresse IP du réseau privé de chaque machine virtuelle dans votre cluster comme suit :

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

    >[AZURE.NOTE]La configuration de `StrictHostKeyChecking no` peut créer un risque de sécurité potentiel si une adresse IP ou une plage d'adresses IP spécifiques n'est pas spécifiée comme indiqué dans ces exemples.

* **Applications** : installez les applications dont vous avez besoin sur cette machine virtuelle ou effectuez d'autres personnalisations avant de capturer l'image.

### capture de l’image

Pour capturer l'image, exécutez d’abord la commande suivante dans la machine virtuelle Linux. Ceci arrête la mise en service de la machine virtuelle mais conserve les comptes utilisateurs et les clés SSH que vous avez configurés.

```
sudo waagent -deprovision
```

Puis, depuis votre ordinateur client, exécutez les commandes CLI Azure suivantes pour capturer l’image. Consultez la page [Capture d'une machine virtuelle Linux à utiliser comme modèle](virtual-machines-linux-capture-image.md) pour plus d'informations.

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

Après avoir exécuté ces commandes, l'image de machine virtuelle est capturée pour que vous puissiez l'utiliser et la machine virtuelle est supprimée. Vous disposez maintenant d’une image personnalisée prête à être déployée sur un cluster.

### Déploiement d’un cluster avec l'image

Modifiez le script Bash suivant avec les valeurs appropriées pour votre environnement et exécutez-le à partir de votre ordinateur client. Dans la mesure où la méthode de déploiement Service Management déploie les machines virtuelles en série, quelques minutes sont nécessaires pour déployer les 8 machines virtuelles A9 suggérées dans ce script.

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

# Save this script with a name like makecluster.sh and run it in your shell environnment to provision your cluster
```
## Configuration et exécution d’Intel MPI

Pour exécuter des applications MPI sur RDMA Azure Linux, vous devez configurer certaines variables d'environnement spécifiques à Intel MPI. Voici un exemple de script Bash pour configurer les variables et exécuter une application.

```
#!/bin/bash -x

source /opt/intel/impi_latest/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLEAND MUST BE SET BEFORE RUNNING ANY JOB
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
source /opt/intel/impi_latest/bin64/mpivars.sh
```

### Exécution d'une commande MPI simple

Exécutez une commande MPI simple sur l'un des nœuds de calcul pour montrer que MPI est installé correctement et peut communiquer entre au moins deux nœuds de calcul. La commande **mpirun** suivante exécute la commande **hostname** sur deux nœuds.

```
/opt/intel/impi_latest/bin64/mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Votre sortie doit répertorier les noms de tous les nœuds que vous avez transmis comme entrée pour `-hosts`. Par exemple, une commande **mpirun** avec deux nœuds renvoie un résultat semblable au suivant :

```
cluster11
cluster12
```

### Exécution d'un test d'évaluation MPI

La commande Intel MPI suivante vérifie la configuration du cluster et sa connexion au réseau RDMA à l'aide d'un banc d'essai pingpong.

```
/opt/intel/impi_latest/bin64/mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
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

## Considérations sur la topologie réseau

* Sur les machines virtuelles Linux, Eth1 est réservé au trafic réseau RDMA. Ne modifiez aucun paramètre Eth1 ou des informations du fichier de configuration faisant référence à ce réseau.

* Dans Azure, IP over Infiniband (IB) n'est pas pris en charge. Seul RDMA over IB est pris en charge.

* Sur les machines virtuelles Linux, Eth0 est réservé au trafic réseau Azure normal.

## Étapes suivantes

* Essayez de déployer et d’exécuter vos applications MPI Linux sur votre cluster Linux.

* Consultez la [documentation de la bibliothèque Intel MPI](https://software.intel.com/fr-FR/articles/intel-mpi-library-documentation/) pour obtenir des conseils sur Intel MPI.

<!---HONumber=Sept15_HO4-->