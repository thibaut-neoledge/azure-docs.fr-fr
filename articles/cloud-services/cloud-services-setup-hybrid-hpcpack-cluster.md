---
title: Configurer un cluster HPC Pack hybride dans Azure | Microsoft Docs
description: "Découvrez comment utiliser Microsoft HPC Pack et Azure pour configurer un petit cluster de calculs complexes (HPC) hybride"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: hpc-pack
ms.assetid: 
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.openlocfilehash: f6dc9657e64160be1e68a7356863b53131e9b3c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Configuration d’un cluster de calcul haute performance (HPC) hybride avec Microsoft HPC Pack et les nœuds de calcul Azure à la demande
Utilisez Microsoft HPC Pack 2012 R2 et Azure pour configurer un petit cluster de calcul haute performance (HPC) hybride. Le cluster illustré dans cet article se compose d'un nœud principal local HPC Pack et de plusieurs nœuds de calcul que vous déployez à la demande dans un service cloud Azure. Vous pouvez ensuite exécuter des tâches de calcul sur le cluster hybride.

![Cluster HPC hybride][Overview] 

Ce didacticiel présente une approche, parfois nommée « cloudburst », visant à utiliser des ressources Azure évolutives à la demande afin d’exécuter des applications nécessitant beaucoup de calculs.

Ce didacticiel ne requiert pas d'expérience préalable avec les clusters de calcul ou HPC Pack. Il vise uniquement à vous aider à déployer rapidement un cluster de calcul hybride à des fins de démonstration. Pour connaître les éléments à prendre en compte, ainsi que la procédure à suivre pour déployer un cluster HPC Pack hybride à plus grande échelle dans un environnement de production ou pour utiliser un cluster HPC Pack 2016, consultez les [instructions détaillées](http://go.microsoft.com/fwlink/p/?LinkID=200493). Pour d’autres scénarios avec HPC Pack, notamment le déploiement automatisé de clusters dans des machines virtuelles Azure, consultez [Options pour créer et gérer un cluster de calcul haute performance dans Azure avec Microsoft HPC Pack](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Composants requis
* **Abonnement Azure** : si vous n’en avez pas, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/) en quelques minutes.
* **Un ordinateur local sous Windows Server 2012 R2 ou Windows Server 2012** : utilisez-le comme nœud principal du cluster HPC. Si vous n'utilisez pas déjà Windows Server, vous pouvez télécharger et installer une [version d'évaluation](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Cet ordinateur doit être associé à un domaine Active Directory. À des fins de test, vous pouvez configurer l’ordinateur servant de nœud principal comme contrôleur de domaine. Pour ajouter le rôle de serveur Active Directory Domain Services et promouvoir l’ordinateur servant de nœud principal comme contrôleur de domaine, consultez la documentation de Windows Server.
  * Le système d'exploitation doit être installé dans l'une des langues suivantes pour prendre en charge HPC Pack : anglais, japonais ou chinois (simplifié).
  * Vérifiez que les mises à jour importantes et critiques sont installées.
* **HPC Pack 2012 R2** - [Téléchargez](http://go.microsoft.com/fwlink/p/?linkid=328024) le package d’installation pour obtenir gratuitement la dernière version et copiez les fichiers sur l’ordinateur servant de nœud principal. Sélectionnez les fichiers d'installation de la même langue que votre serveur Windows Server.

    >[!NOTE]
    > Si vous souhaitez utiliser un cluster HPC Pack 2016 plutôt qu’un cluster HPC Pack 2012 R2, une configuration supplémentaire est nécessaire. Consultez les [instructions détaillées](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Compte de domaine** : ce compte doit disposer d’autorisations d’administrateur local sur le nœud principal pour installer HPC Pack.
* **Connectivité TCP sur le port 443** entre le nœud principal et Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Installation du HPC Pack sur le nœud principal
Vous devez tout d'abord installer Microsoft HPC Pack sur votre ordinateur local qui exécute Windows Server pour en faire le nœud principal du cluster.

1. Connectez-vous au nœud principal en utilisant un compte de domaine disposant des autorisations d'administrateur.

2. Dans les fichiers d'installation HPC Pack, lancez l'Assistant Installation en exécutant Setup.exe.

3. Sur l’écran **Installation de HPC Pack 2012 R2**, cliquez sur **Nouvelle installation ou ajout de fonctionnalités à une installation existante**.

    ![Configuration de HPC Pack 2012][install_hpc1]

4. Sur la **page Contrat d’utilisateur de logiciels Microsoft**, cliquez sur **Suivant**.

5. Sur la page **Sélectionner le type d’installation**, cliquez sur **Créer un cluster HPC en créant un nœud principal**, puis sur **Suivant**.

6. L'Assistant exécute plusieurs tests préalablement à l'installation. Cliquez sur **Suivant** on the **Règles d'installation** si tous les tests ont réussi. Sinon, consultez les informations fournies et apportez les modifications nécessaires à votre environnement. Réexécutez le test ou, si nécessaire, relancez l'Assistant Installation.
7. Sur la page **Configuration de base de données HPC**, veillez à ce que **Nœud principal** soit sélectionné pour toutes les bases de données HPC, puis cliquez sur **Suivant**. 

    ![Configuration de base de données][install_hpc4]

8. Acceptez les sélections par défaut sur les pages suivantes de l'Assistant. Sur la page **Installer les composants nécessaires**, cliquez sur **Installer**.
   
    ![Installer][install_hpc6]

9. Une fois l’installation terminée, désactivez **Démarrer HPC Cluster Manager**, puis cliquez sur **Terminer**. (Vous pourrez démarrer HPC Cluster Manager à une étape ultérieure.)
   
    ![Terminer][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Préparation de l'abonnement à Azure
Utilisez le [portail Azure](https://portal.azure.com) pour réaliser les étapes suivantes avec votre abonnement Azure. Vous pourrez ensuite déployer les nœuds Azure à partir du nœud principal local. 
  
  > [!NOTE]
  > Notez votre ID d’abonnement Azure. Il vous sera utile par la suite. Dans le portail, recherchez l’ID sous **Abonnements**.
  > 

### <a name="upload-the-default-management-certificate"></a>Téléchargement du certificat de gestion par défaut
HPC Pack installe un certificat auto-signé sur le nœud principal, nommé « Default Microsoft HPC Azure Management certificate ». Vous pouvez le télécharger en tant que certificat de gestion Azure. Ce certificat est fourni à des fins de test et pour les déploiements pour validation technique dans le but de sécuriser la connexion entre le nœud principal et Azure.

1. Sur l'ordinateur faisant office de nœud principal, connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **Abonnements** > *nom_de_votre_abonnement*.

3. Cliquez sur **Certificats de gestion** > **Télécharger**.4. Sur le nœud principal, accédez au fichier C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer. Cliquez ensuite sur **Télécharger**.

   
Le certificat **Default HPC Azure Management** s'affiche dans la liste des certificats de gestion.

### <a name="create-an-azure-cloud-service"></a>Création d'un service cloud Azure
> [!NOTE]
> Pour des performances optimales, créez le service cloud et le compte de stockage (dans une étape ultérieure) dans la même région.
> 
> 

1. Dans le portail, cliquez sur **Services cloud (classiques)** > **+Ajouter**.

2.  Attribuez un nom DNS au service, sélectionnez un groupe de ressources et un emplacement, puis cliquez sur **Créer**.


### <a name="create-an-azure-storage-account"></a>Création d'un compte de stockage Azure
1. Dans le portail, cliquez sur **Comptes de stockage (classiques)** > **+ Ajouter**.

2. Attribuez un nom au compte et sélectionnez le modèle de déploiement **Classic**.

3. Sélectionnez un groupe de ressources et un emplacement, et conservez les valeurs par défaut des autres paramètres. Cliquez ensuite sur **Créer**.

## <a name="configure-the-head-node"></a>Configuration du nœud principal
Effectuez quelques unes des étapes de configuration de cluster requises afin que HPC Cluster Manager puisse déployer les nœuds Azure et soumettre des tâches.

1. Lancez HPC Cluster Manager sur le nœud principal. Si la boîte de dialogue **Sélectionner un nœud principal** s’affiche, cliquez sur **Ordinateur local**. La liste **Deployment To-do List** s'affiche.

2. Sous **Tâches de déploiement requises**, cliquez sur **Configurer votre réseau**.
   
    ![Configurer le réseau][config_hpc2]

3. Dans l'Assistant Configuration du réseau, sélectionnez **All nodes only on an enterprise network** (Topologie 5). Cette configuration réseau est la plus simple à des fins de démonstration.
   
    ![Topologie 5][config_hpc3]
   
4. Cliquez sur **Suivant** pour accepter les valeurs par défaut sur les pages suivantes de l'Assistant. Ensuite, dans l’onglet **Passer en revue**, cliquez sur **Configurer** pour terminer la configuration réseau.

5. Dans la liste **Deployment To-do List**sur **Provide installation credentials**.

6. Dans la boîte de dialogue **Installation Credentials** , entrez les informations d'identification du compte de domaine utilisé pour installer HPC Pack. Cliquez ensuite sur **OK**. 
   
    ![Installation Credentials][config_hpc6]
   
7. Dans la liste **Deployment To-do List**sur **Configure the naming of new nodes**.

8. Dans la boîte de dialogue **Spécifier la série de noms des nœuds**, acceptez la série de noms par défaut, puis cliquez sur **OK**. Effectuez cette étape, même si les nœuds Azure que vous ajoutez dans ce didacticiel sont automatiquement nommés.
   
    ![Noms de nœuds][config_hpc8]
   
9. Dans la liste **Deployment To-do List**sur **Create a node template**. Un peu plus loin dans ce didacticiel, vous utiliserez le modèle de nœud pour ajouter des nœuds Azure au cluster.

10. Procédez comme suit dans l'Assistant Create Node Template :
    
    a. Sur la page **Choisir un type de modèle de nœud**, cliquez sur **Modèle de nœud Windows Azure**, puis sur **Suivant**.
    
    ![Modèle de nœud][config_hpc10]
    
    b. Cliquez sur **Suivant** pour accepter le nom de modèle par défaut.
    
    c. Sur la page **Provide Subscription Information** (Fournir les informations sur l’abonnement), entrez votre ID d’abonnement Azure (disponible dans les informations de compte Azure). Puis, sous **Certificat de gestion**, recherchez **Gestion Azure Microsoft HPC par défaut**. Cliquez ensuite sur **Suivant**.
    
    ![Modèle de nœud][config_hpc12]
    
    d. Sur la page **Fournir des informations sur le service** , sélectionnez le service cloud et le compte de stockage que vous avez créés à l’étape précédente. Cliquez ensuite sur **Suivant**.
    
    ![Modèle de nœud][config_hpc13]
    
    e. Cliquez sur **Suivant** pour accepter les valeurs par défaut sur les pages suivantes de l'Assistant. Ensuite, dans l’onglet **Passer en revue**, cliquez sur **Créer** pour créer le modèle de nœud.
    
    > [!NOTE]
    > Par défaut, le modèle de nœud Azure inclut des paramètres vous permettant de lancer (d'approvisionner) et d'arrêter manuellement les nœuds, avec HPC Cluster Manager. Vous pouvez également, si vous le souhaitez, programmer le démarrage et l’arrêt automatiques de nœuds Azure.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Ajout de nœuds Azure au cluster
Utilisez à présent le modèle de nœud pour ajouter des nœuds Azure au cluster. L'ajout de nœuds au cluster stocke les informations de configuration de ces nœuds afin que vous puissiez les démarrer (approvisionner) à tout moment dans le service cloud. Votre compte est uniquement facturé pour les nœuds Azure une fois que les instances sont exécutées dans le service cloud.

Suivez ces étapes pour ajouter deux petits nœuds.

1. Dans HPC Cluster Manager, cliquez sur **Gestion des nœuds** (ou **Gestion des ressources** dans les versions actuelles de HPC Pack) > **Ajouter un nœud**.
   
    ![Add Node][add_node1]

2. Dans l’Assistant Ajouter un nœud, sur la page **Sélectionner une méthode de déploiement**, cliquez sur **Ajouter des nœuds Windows Azure**, puis sur **Suivant**.
   
    ![Ajouter un nœud Azure][add_node1_1]

3. Sur la page **Spécifier les nouveaux nœuds**, sélectionnez le modèle de nœud Azure créé précédemment (par défaut, son nom est **Default AzureNode Template**). Spécifiez ensuite **2** nœuds de taille **Petite**, puis cliquez sur **Suivant**.
   
    ![Spécifier les nœuds][add_node2]
   
4. Sur la page **Fin de l’Assistant Ajouter un nœud**, cliquez sur **Terminer**.
    
     Deux nœuds Azure, nommés **AzureCN-0001** et **AzureCN-0002**, sont désormais affichés dans HPC Cluster Manager. Leur état est **Not-Deployed** (Non déployé).
   
    ![Nœuds ajoutés][add_node3]

## <a name="start-the-azure-nodes"></a>Démarrage des nœuds Azure
Lorsque vous souhaitez utiliser les ressources de cluster dans Azure, utilisez HPC Cluster Manager pour démarrer (approvisionner) les nœuds Azure et les mettre en ligne.

1. Dans HPC Cluster Manager, cliquez sur **Gestion des nœuds** (ou **Gestion des ressources** dans les versions actuelles de HPC Pack) et sélectionnez les nœuds Azure.

2. Cliquez sur **Démarrer**, puis sur **OK**.
   
   ![Démarrer les nœuds][add_node4]
   
    Le nœud passe à l'état **Provisioning** . Consultez le journal d’approvisionnement pour suivre l’avancée de l’approvisionnement.
   
    ![Nœuds d'approvisionnement][add_node6]

3. Après quelques minutes, les nœuds Azure terminent leur approvisionnement et passent à l'état **Offline** . À cette étape, les instances de rôle sont exécutées mais ne peuvent pas encore accepter les tâches de cluster.

4. Pour confirmer l’exécution des instances de rôle, dans le portail Azure, cliquez sur **Services cloud (classiques)** > *nom_de_votre_service_cloud*.
   
   Vous devriez voir deux instances (nœuds) **HpcWorkerRole** en cours d’exécution dans le service. HPC Pack déploie également deux instances **HpcProxy** (taille moyenne) pour gérer la communication entre le nœud principal et Azure.

   ![Instances en cours d'exécution][view_instances1]

5. Pour faire passer les nœuds Azure en ligne afin qu'ils exécutent des tâches de cluster, sélectionnez-les, cliquez avec le bouton droit, puis cliquez sur **Bring Online**.
   
    ![Nœuds hors ligne][add_node7]
   
    HPC Cluster Manager indique que les nœuds sont dans l'état **Online** .

## <a name="run-a-command-across-the-cluster"></a>Exécution d'une commande sur le cluster
Pour vérifier l’installation, utilisez la commande **clusrun** de HPC Pack pour exécuter une commande ou une application sur un ou plusieurs nœuds de cluster. Par exemple, utilisez **clusrun** pour obtenir la configuration IP des nœuds Azure.

1. Sur le nœud principal, ouvrez une invite de commandes en tant qu’administrateur.

2. Tapez la commande suivante :
   
    `clusrun /nodes:azurecn* ipconfig`

3. Si vous y êtes invité, entrez votre mot de passe administrateur de cluster. Le résultat de la commande devrait ressembler à ce qui suit.
   
    ![clusrun][clusrun1]

## <a name="run-a-test-job"></a>Exécution d'une tâche de test
Envoyez maintenant une tâche de test à exécuter sur le cluster hybride. Cet exemple est une simple tâche de « balayage paramétrique » (un type de calcul intrinsèquement parallèle). Cet exemple exécute des sous-tâches qui ajoutent un entier à lui-même à l'aide de la commande **set /a** . Tous les nœuds du cluster contribuent à terminer les sous-tâches pour les entiers de 1 à 100.

1. Dans HPC Cluster Manager, cliquez sur **Gestion des tâches** > **Nouvelle tâche de balayage paramétrique**.
   
    ![Nouvelle tâche][test_job1]

2. Dans la boîte de dialogue **Nouvelle tâche de balayage paramétrique**, sous **Ligne de commande**, tapez `set /a *+*` (en remplaçant la ligne de commande par défaut qui s’affiche). Ne modifiez pas les autres paramètres par défaut, puis cliquez sur **Submit** pour envoyer la tâche.
   
    ![Balayage paramétrique][param_sweep1]

3. Une fois la tâche terminée, cliquez deux fois sur la tâche **My Sweep Task** .

4. Cliquez sur **View Tasks**, puis sur une sous-tâche pour afficher la sortie calculée associée.
   
    ![Résultats de la tâche][view_job361]

5. Pour connaître le nœud ayant effectué le calcul pour cette sous-tâche, cliquez sur **Nœuds alloués**. (votre cluster peut indiquer un nom de nœud différent).
   
    ![Résultats de la tâche][view_job362]

## <a name="stop-the-azure-nodes"></a>Arrêt des nœuds Azure
Après avoir testé le cluster, arrêtez les nœuds Azure pour éviter toute facturation inutile de votre compte. Cette étape arrête le service cloud et supprime les instances de rôle Azure.

1. Dans HPC Cluster Manager, sous **Gestion des nœuds** (ou **Gestion des ressources** dans les versions antérieures de HPC Pack), sélectionnez les deux nœuds Azure. Cliquez ensuite sur **Arrêter**.
   
    ![Arrêter les nœuds][stop_node1]

2. Dans la boîte de dialogue **Arrêter les nœuds Windows Azure**, cliquez sur **Arrêter**. 
   
3. Le nœud passe à l'état **Stopping** . Après quelques minutes, HPC Cluster Manager signale les nœuds comme **Not-Deployed**.
   
    ![Nœuds non déployés][stop_node4]

4. Pour confirmer la non-exécution des instances de rôle dans Azure, dans le portail Azure, cliquez sur **Services cloud (classiques)** > *nom_de_votre_service_cloud*. Aucune instance n'est déployée dans l'environnement de production. 
   
    C'est ici que s'achève ce didacticiel.

## <a name="next-steps"></a>Étapes suivantes
* Consultez la documentation de [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Pour configurer un déploiement de cluster HPC Pack hybride à plus grande échelle, consultez [Burst to Azure Worker Role Instances with Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)(Débordement vers des instances de rôle de travail Azure avec Microsoft HPC Pack).
* Pour accéder à d’autres méthodes de création d’un cluster HPC Pack dans Azure, notamment avec des modèles Azure Resource Manager, consultez [Options de cluster HPC avec Microsoft HPC Pack dans Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png
