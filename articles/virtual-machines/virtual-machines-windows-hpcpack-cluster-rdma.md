<properties
 pageTitle="Configuration d’un cluster Windows RDMA pour exécuter des applications MPI | Microsoft Azure"
 description="Apprenez à créer un cluster Windows HPC Pack avec des machines virtuelles de taille A8 ou A9 pour utiliser le réseau Azure RDMA pour exécuter des applications MPI."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="01/13/2016"
 ms.author="danlep"/>

# Configurer un cluster Windows RDMA avec HPC Pack et des instances A8 et A9 pour exécuter des applications MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle de gestionnaire de ressources.


Cet article vous montre comment configurer un cluster Windows RDMA dans Azure avec [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) et [des instances de taille A8 et A9 nécessitant beaucoup de ressources système](virtual-machines-a8-a9-a10-a11-specs.md) pour exécuter des applications MPI (Message Passing Interface) parallèles. Lorsque vous configurez des instances Windows Server de taille A8 et A9 pour s'exécuter dans un cluster HPC Pack, les applications MPI communiquent efficacement sur un réseau à latence faible et à débit élevé dans Azure, reposant sur la technologie d'accès direct à la mémoire à distance (RDMA).

Si vous souhaitez exécuter des charges de travail MPI sur des machines virtuelles Linux qui accèdent au réseau Azure RDMA, consultez [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-cluster-rdma.md).

## Options de déploiement de cluster HPC Pack
Microsoft HPC Pack est un outil recommandé pour créer des clusters HPC Windows Server dans Azure. Dans le cas d’une utilisation avec des instances A8 et A9, HPC Pack est un moyen efficace pour exécuter des applications Windows MPI qui accèdent au réseau RDMA dans Azure. HPC Pack comprend un environnement d'exécution pour l'implémentation Microsoft de MPI pour Windows (MSMPI).

Cet article présente deux scénarios pour déployer les instances A8 et A9 en cluster avec Microsoft HPC Pack.

* Scénario 1 Déployer des instances de rôle de travail nécessitant beaucoup de ressources système (PaaS)

* Scénario 2 Déployer des nœuds de calcul sur des machines virtuelles nécessitant beaucoup de ressources système (IaaS)

## Composants requis

* **Consultez les [informations et les considérations générales](virtual-machines-a8-a9-a10-a11-specs.md)** sur les instances nécessitant beaucoup de ressources système.


* **Abonnement Azure** : si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).


* **Quota de cœurs** : vous devrez peut-être augmenter le quota de cœurs pour déployer un cluster de machines virtuelles A8 ou A9. Par exemple, vous devez avoir au moins 128 cœurs si vous souhaitez déployer 8 instances A9 avec HPC Pack. Pour augmenter un quota, ouvrez [une demande de service clientèle en ligne](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) gratuitement.

## Scénario 1 Déployer des instances de rôle de travail nécessitant beaucoup de ressources système (PaaS)


À partir d’un cluster HPC Pack existant, ajoutez des ressources de calcul supplémentaires dans les instances de rôle de travail Azure (nœuds Azure) en cours d’exécution dans un service cloud (PaaS). Cette fonctionnalité, également appelée « Intégration à Azure » à partir de HPC Pack, prend en charge une plage de tailles d’instances de rôle de travail. Pour utiliser des instances nécessitant beaucoup de ressources système, spécifiez simplement une taille A8 ou A9 lors de l’ajout de nœuds Azure.

Voici les considérations et les étapes pour l'intégration aux instances Azure A8 ou A9 à partir d'un cluster existant (généralement local). Utilisez des procédures similaires pour ajouter des instances de rôle de travail à un nœud principal HPC Pack déployé dans une machine virtuelle Azure.

>[AZURE.NOTE]Pour obtenir un didacticiel sur l’intégration à Azure avec HPC Pack, consultez [Configurer un cluster hybride avec HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Tenez compte des considérations dans les étapes suivantes qui s’appliquent spécifiquement aux nœuds Azure de taille A8 et A9.

![Intégration à Azure][burst]

### Considérations sur l'utilisation des instances A8 et A9

* **Nœuds proxy** : dans chaque déploiement d'intégration à Azure avec les instances nécessitant beaucoup de ressources système, HPC Pack déploie automatiquement un minimum de 2 instances de taille A8 comme nœuds proxy, en plus des instances de rôle de travail Azure que vous spécifiez. Les nœuds proxy utilisent des cœurs qui sont affectés à l’abonnement et occasionnent des frais avec les instances de rôle de travail Azure.

### Étapes

4. **Déployer et configurer un nœud principal HPC Pack 2012 R2**

    Téléchargez le dernier package d’installation de HPC Pack à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Pour la configuration requise et les instructions pour préparer un déploiement d’intégration à Azure, consultez [Guide de prise en main de HPC Pack](https://technet.microsoft.com/library/jj884144.aspx) et [Intégration à Azure avec Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

5. **Configurer un certificat de gestion dans l’abonnement Azure**

    Configurez un certificat pour sécuriser la connexion entre le nœud principal et Azure. Pour connaître les options et les procédures, consultez [Scénarios pour configurer le certificat de gestion Azure pour HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Pour les déploiements de test, HPC Pack installe un certificat Microsoft HPC Azure Management par défaut que vous pouvez télécharger rapidement dans votre abonnement Azure.

6. **Créer un nouveau service cloud et un compte de stockage**

    Utilisez le portail Azure Classic pour créer un service cloud et un compte de stockage pour le déploiement dans une région où les instances nécessitant beaucoup de ressources système sont disponibles.

7. **Créer un modèle de nœud Azure**

    Utilisez l’Assistant Créer un modèle de nœud dans HPC Cluster Manager. Pour les étapes, consultez [Créer un modèle de nœud Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) dans « Étapes à suivre pour déployer des nœuds Azure avec Microsoft HPC Pack ».

    Pour les premiers tests, nous vous suggérons de configurer une stratégie de disponibilité manuelle dans le modèle.

8. **Ajouter un nœud au cluster**

    Utilisez l’Assistant Ajouter un nœud dans HPC Cluster Manager. Pour plus d’informations, consultez [Ajout des nœuds Azure au cluster Windows HPC](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Lorsque vous spécifiez la taille des nœuds, sélectionnez A8 ou A9.

9. **Démarrer (approvisionner) les nœuds et les mettre en ligne pour exécuter des travaux**

    Sélectionnez les nœuds et utilisez l’action **Démarrer** dans HPC Cluster Manager. Lorsque l’approvisionnement est terminé, sélectionnez les nœuds et utilisez l’action **Mettre en ligne** dans HPC Cluster Manager. Les nœuds sont prêts à exécuter des travaux.

10. **Envoyer des travaux au cluster**

    Utilisez les outils de soumission de travaux HPC Pack pour exécuter des travaux de cluster. Consultez [Microsoft HPC Pack : gestion des travaux](http://technet.microsoft.com/library/jj899585.aspx).

11. **Arrêter (annuler le déploiement) les nœuds**

    Lorsque vous avez terminé l’exécution des travaux, déconnectez les nœuds et utilisez l’action **Arrêter** dans HPC Cluster Manager.





## Scénario 2 Déployer des nœuds de calcul sur des machines virtuelles nécessitant beaucoup de ressources système (IaaS)

Dans ce scénario, vous déployez le nœud principal HPC Pack et les nœuds de calcul de cluster sur des machines virtuelles jointes à un domaine Active Directory dans un réseau virtuel Azure. HPC Pack fournit un certain nombre d'[options de déploiement sur les machines virtuelles Azure](virtual-machines-hpcpack-cluster-options.md), y compris les scripts de déploiement automatisé et les modèles de démarrage rapide Microsoft Azure. Par exemple, les considérations et les étapes ci-dessous vous guident pour l'utilisation du [script de déploiement IaaS HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) pour automatiser la majeure partie de ce processus.

![Cluster sur les machines virtuelles Azure][iaas]

### Considérations sur l'utilisation des instances A8 et A9

* **Virtual Network** : spécifiez un réseau virtuel dans une région dans laquelle les instances A8 et A9 sont disponibles.


* **Système d’exploitation Windows Server** : pour prendre en charge la connectivité RDMA, spécifiez un système d’exploitation Windows Server 2012 R2 ou Windows Server 2012 pour les machines virtuelles de nœud de calcul de taille A8 ou A9.


* **Services cloud** : nous vous recommandons de déployer votre nœud principal dans un service cloud et vos nœuds de calcul A8 et A9 dans un autre service cloud.


* **Taille du nœud principal** : lors de l’ajout de machines virtuelles à nœud de calcul de la taille A8 ou A9, envisagez une taille d’au moins A4 (très grande) pour le nœud principal.

* **Extension HpcVmDrivers** : le script de déploiement installe l’agent de machine virtuelle Azure et l’extension HpcVmDrivers automatiquement lors du déploiement de nœuds de taille A8 ou A9 avec un système d’exploitation Windows Server. HpcVmDrivers installe des pilotes sur des machines virtuelles à nœud de calcul afin qu’ils puissent se connecter au réseau RDMA.

* **Configuration du réseau de clusters** : le script de déploiement configure automatiquement le cluster HPC Pack dans la topologie 5 (tous les nœuds sur le réseau d’entreprise). Cette topologie est obligatoire pour tous les déploiements de clusters HPC Pack dans les machines virtuelles, y compris ceux des nœuds de calcul de taille A8 ou A9. Ne modifiez pas la topologie de réseau de clusters ultérieurement.

### Étapes

1. **Créer un nœud principal de cluster et des machines virtuelles à nœud de calcul en exécutant le script de déploiement de HPC Pack IaaS sur un ordinateur client**

    Téléchargez le package de script de déploiement de HPC Pack IaaS à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).

    Pour préparer l’ordinateur client, créez le fichier de configuration de script, puis exécutez le script. Consultez [Créer un Cluster HPC avec le script de déploiement HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md). Pour déployer les nœuds de calcul de taille A8 et A9, consultez les considérations supplémentaires, plus loin dans cet article.

2. **Mettre en ligne les nœuds de calcul pour exécuter des travaux**

    Sélectionnez les nœuds et utilisez l’action **Mettre en ligne** dans HPC Cluster Manager. Les nœuds sont prêts à exécuter des travaux.

3. **Envoyer des travaux au cluster**

    Connectez-vous au nœud principal pour soumettre des travaux, ou configurez un ordinateur local pour ce faire. Pour plus d’informations, consultez [Envoyer des travaux à un cluster HPC dans Azure](virtual-machines-hpcpack-cluster-submit-jobs.md).

4. **Déconnecter les nœuds et les arrêter (désallouer)**

    Lorsque vous avez terminé l’exécution des travaux, déconnectez les nœuds dans HPC Cluster Manager. Puis, utilisez les outils de gestion Azure pour les arrêter.




## Exécuter des applications MPI sur des instances A8 et A9

### Exemple : Exécuter mpipingpong sur un cluster HPC Pack

Pour vérifier un déploiement HPC Pack des instances nécessitant beaucoup de ressources système, exécutez la commande Pack HPC **mpipingpong** sur le cluster. **mpipingpong** envoie des paquets de données entre des nœuds associés de façon répétée pour calculer la latence et les mesures et statistiques de débit sur le réseau d’application RDMA. Cet exemple montre un modèle classique pour l’exécution d’un travail MPI (dans ce cas, **mpipingpong**) à l’aide de la commande de cluster **mpiexec**.

Cet exemple suppose que vous avez ajouté des nœuds Azure dans une configuration de type « intégration à Azure » ([scénario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS) dans cet article). Si vous avez déployé HPC Pack sur un cluster de machines virtuelles Azure, vous devez modifier la syntaxe de commande pour spécifier un autre groupe de nœuds et définir des variables d’environnement supplémentaires pour diriger le trafic réseau vers le réseau RDMA.


Pour exécuter mpipingpong sur le cluster :


1. Sur le nœud principal ou sur un ordinateur client correctement configuré, ouvrez une invite de commande.

2. Pour estimer la latence entre les paires de nœuds dans un déploiement d’intégration à Azure de 4 nœuds, tapez la commande suivante afin de soumettre un travail pour exécuter mpipingpong avec une petite taille de paquet et un grand nombre d’itérations :

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    La commande retourne l’ID du travail soumis.

    Si vous avez déployé le cluster HPC Pack déployé sur les machines virtuelles Azure, spécifiez un groupe de nœuds qui contient des machines virtuelles à nœud de calcul déployées dans un même service cloud et modifiez la commande **mpiexec** comme suit :

    ```
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```

3. Lorsque le travail est terminé, pour afficher le résultat (dans ce cas, la sortie de la tâche 1 du travail), tapez la commande suivante :

    ```
    task view <JobID>.1
    ```

    où &lt;*JobID*&gt; est l’ID du travail qui a été soumis.

    La sortie indiquera des résultats de latence semblables à ceci :

    ![Latence ping pong][pingpong1]

4. Pour estimer le débit entre les paires de nœuds d’extension Azure, tapez la commande suivante afin de soumettre un travail pour exécuter **mpipingpong** avec une grande taille de paquet et un petit nombre d’itérations :

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    La commande retourne l’ID du travail soumis.

    Sur un cluster HPC Pack déployé sur les machines virtuelles Azure, modifiez la commande comme indiqué à l’étape 2.

5. Une fois le travail terminé, pour afficher le résultat (dans ce cas, la sortie de la tâche 1 du travail), tapez la commande suivante :

    ```
    task view <JobID>.1
    ```

  La sortie indiquera des résultats de débit semblables à ceci :

  ![Débit ping pong][pingpong2]


### Considérations sur les applications MPI


Vous trouverez ci-dessous des considérations relatives à l’exécution d’applications MPI sur des instances Azure : Certaines s’appliquent uniquement aux déploiements de nœuds Azure (instances de rôle de travail ajoutées dans une configuration de type « intégration à Azure »).

* Les instances de rôle de travail dans un service cloud sont régulièrement réapprovisionnées sans préavis par Azure (par exemple, pour la maintenance du système ou en cas d’échec d’une instance). Si une instance est réapprovisionnée alors qu’elle exécute un travail MPI, l’instance perd toutes ses données et retourne à l’état de son premier déploiement, ce qui peut entraîner l’échec du travail MPI. Plus vous utilisez de nœuds pour un même travail MPI et plus le travail sera long, plus la probabilité de réapprovisionnement de l’une des instances au cours d’un travail sera élevée. Vous devez également prendre ce point en considération si vous désignez un seul nœud dans le déploiement comme serveur de fichiers.


* Vous n’êtes pas obligé d’utiliser les instances A8 et A9 pour exécuter des travaux MPI dans Azure. Vous pouvez utiliser n’importe quelle taille d’instance prise en charge par HPC Pack. Toutefois, les instances A8 et A9 sont recommandées pour l’exécution de travaux MPI relativement volumineux qui sont sensibles à la latence et à la bande passante du réseau qui connecte les nœuds. Si vous utilisez des instances autres que A8 et A9 pour exécuter des travaux MPI de sensibilité de bande passante et de latence, nous vous recommandons d’exécuter de petits travaux, dans lequel une seule tâche s’exécute sur quelques nœuds seulement.

* Les applications déployées sur des instances Azure sont soumises aux termes du contrat de licence associés à l’application. Vérifiez auprès du fournisseur de toute application commerciale les questions de licence ou toute autre restriction relative à l’exécution dans le cloud. Tous les fournisseurs ne proposent pas le paiement à l'utilisation pour les licences.


* Les instances Azure nécessitent une configuration supplémentaire pour accéder aux nœuds locaux, partages et serveurs de licences. Par exemple, pour activer les nœuds Azure pour accéder à un serveur de licences local, vous pouvez configurer un réseau virtuel Azure de site à site.


* Pour exécuter des applications MPI sur des instances Azure, enregistrez chaque application MPI sur les instances avec le pare-feu Windows en exécutant la commande **hpcfwutil**. Cela permet aux communications MPI d’avoir lieu sur un port affecté dynamiquement par le pare-feu.

    >[AZURE.NOTE]Pour des déploiements d’intégration à Azure, vous pouvez également configurer une commande d’exception de pare-feu pour qu’elle s’exécute automatiquement sur tous les nouveaux nœuds Azure ajoutés à votre cluster. Après avoir exécuté la commande **hpcfwutil** et vérifié que votre application fonctionne, ajoutez la commande à un script de démarrage pour vos nœuds Azure. Pour plus d'informations, consultez [Utiliser un script de démarrage pour les nœuds Azure](https://technet.microsoft.com/library/jj899632.aspx).



* HPC Pack utilise la variable d’environnement de cluster CCP\_MPI\_NETMASK pour spécifier une plage d’adresses acceptables pour la communication MPI. Depuis HPC Pack 2012 R2, la variable d’environnement de cluster CCP\_MPI\_NETMASK affecte uniquement la communication MPI entre les nœuds de calcul cluster appartenant à un domaine (localement ou sur des machines virtuelles Azure). La variable est ignorée par les nœuds ajoutés dans une configuration d’intégration à Azure.


* Les travaux MPI ne peuvent pas s’exécuter sur des instances Azure déployées dans différents services cloud (par exemple, dans des déploiements d’intégration à Azure avec différents modèles de nœud ou des nœuds de calcul dans des machines virtuelles Azure déployés dans plusieurs services cloud). Si vous avez plusieurs déploiements de nœuds Azure démarrés avec différents modèles de nœud, le travail MPI doit s’exécuter sur un seul ensemble de nœuds Azure.


* Lorsque vous ajoutez des nœuds Azure à votre cluster et que vous les mettez en ligne, le service de planification de travaux HPC essaie immédiatement de démarrer les travaux sur les nœuds. Si seulement une partie de votre charge de travail peut s’exécuter sur Azure, assurez-vous de mettre à jour ou de créer des modèles de projet pour définir les types de travaux qui peuvent s’exécuter sur Azure. Par exemple, pour vous assurer que les travaux soumis avec un modèle de travail s’exécutent uniquement sur des nœuds Azure, ajoutez la propriété Node Groups au modèle de travail et sélectionnez AzureNodes comme valeur requise. Pour créer des groupes personnalisés pour vos nœuds Azure, utilisez l'applet de commande Add-HpcGroup HPC PowerShell.


## Étapes suivantes

* Si vous souhaitez exécuter des applications MPI qui accèdent au réseau Azure RDMA, consultez [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-cluster-rdma.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png

<!---HONumber=AcomDC_0121_2016-->