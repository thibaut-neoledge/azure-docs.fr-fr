<properties linkid="itpro-windows-howto-howto-setup-hybrid-cluster-hpcpack" urlDisplayName="" pageTitle="Set up a Hybrid Compute Cluster in Azure with Microsoft HPC Pack" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Compute Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep"></tags>

# Configuration d'un cluster de calcul hybride avec Microsoft HPC Pack

Ce didacticiel vous indique comment utiliser Microsoft HPC Pack 2012 R2 et Azure pour configurer un petit cluster hybride de calculs complexes (HPC). Le cluster se compose d'un nœud principal local (ordinateur exécutant le système d'exploitation Windows Server et HPC Pack) et de plusieurs nœuds de calcul que vous déployez comme instances de rôle de travail dans un service cloud Azure. Vous pouvez ensuite exécuter des tâches de calcul sur le cluster hybride.

![Cluster HPC hybride][]

Ce didacticiel présente une approche, parfois nommée « cloudburst », visant à utiliser des ressources évolutives à la demande dans Azure afin d'exécuter des applications nécessitant beaucoup de calculs.

Ce didacticiel ne requiert pas d'expérience préalable avec les clusters de calcul ou HPC Pack. Il vise uniquement à vous aider à déployer rapidement un cluster de calcul hybride à des fins de démonstration. Pour connaître les éléments à prendre en compte ainsi que la procédure à suivre pour déployer un HPC Pack hybride à plus grande échelle dans un environnement de production, consultez les [instructions détaillées][].

> [WACOM.NOTE] Azure propose [plusieurs tailles][] pour vos ressources de calcul, afin de répondre aux différentes charges de travail. Par exemple, les instances A8 et A9 combinent hautes performances et accès à des applications à faible latence et débit élevé, nécessaires à certaines applications HPC. Pour plus d'informations, consultez [À propos des instances de calcul intensif A8 et A9][].

Ce didacticiel vous familiarise avec ces étapes de base :

-   [Conditions préalables][]
-   [Installation du HPC Pack sur le nœud principal][]
-   [Préparation de l'abonnement à Azure][]
-   [Configuration du nœud principal][]
-   [Ajout de nœuds Azure au cluster][]
-   [Démarrage des nœuds Azure][]
-   [Exécution d'une commande sur le cluster][]
-   [Exécution d'une tâche de test][]
-   [Arrêt des nœuds Azure][]

## Conditions préalables

> [WACOM.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Création d’un compte Azure][].

Vous devez également disposer des éléments suivants pour ce didacticiel :

-   Un ordinateur local qui exécute une version de Windows Server 2012 R2 ou Windows Server 2012, et qui sera le nœud principal du cluster HPC. Si vous n'utilisez pas déjà Windows Server, vous pouvez télécharger et installer une [version d'évaluation][].

    -   Cet ordinateur doit être associé à un domaine Active Directory.

    -   Vérifiez qu'aucun autre rôle serveur ou service de rôle n'est installé.

    -   Le système d'exploitation doit être installé dans l'une des langues suivantes pour prendre en charge HPC Pack : anglais, japonais ou chinois (simplifié).

    -   Vérifiez que les mises à jour importantes et critiques sont installées.

-   Fichiers d'installation de HPC Pack 2012 R2, disponibles gratuitement. [Téléchargez][] le package d'installation complet et copiez les fichiers sur l'ordinateur servant de nœud principal ou vers un emplacement sur le réseau. Sélectionnez les fichiers d'installation de la même langue que votre serveur Windows Server.

-   Un compte de domaine avec des autorisations d'administrateur local sur le nœud principal.

-   La connectivité TCP sur le port 443 entre le nœud principal et Azure.

## Installation du HPC Pack sur le nœud principal

Vous devez tout d'abord installer Microsoft HPC Pack sur un ordinateur qui exécute Windows Server pour en faire le nœud principal du cluster.

1.  Connectez-vous au nœud principal en utilisant un compte de domaine disposant des autorisations d'administrateur.

2.  Dans les fichiers d'installation HPC Pack, lancez l'Assistant Installation en exécutant Setup.exe.

3.  Sur l'écran **HPC Pack 2012 R2 Setup**, cliquez sur **New installation or add new features to an existing installation**.

    ![Configuration de HPC Pack 2012][]

4.  Sur la page **Microsoft Software User Agreement page**, cliquez sur **Suivant**.

5.  Sur la page **Sélectionner le type d’installation**, cliquez sur **Create a new HPC cluster by creating a head node**, puis sur **Suivant**.

    ![Sélectionner le type d'installation][]

6.  L'Assistant exécute plusieurs tests préalablement à l'installation. Cliquez sur **Suivant** sur la page **Règles d'installation** si tous les tests ont réussi. Sinon, consultez les informations fournies et apportez les modifications nécessaires à votre environnement. Réexécutez le test ou, si nécessaire, relancez l'Assistant Installation.

    ![Règles d'installation][]

7.  Sur la page **HPC DB Configuration**, veillez à ce que **Head Node** soit sélectionné pour toutes les bases de données HPC, puis cliquez sur **Suivant**.

    ![Configuration de base de données][]

8.  Acceptez les sélections par défaut sur les pages suivantes de l'Assistant. Sur la page **Installer les composants nécessaires**, cliquez sur **Installer**.

    ![Installer][]

9.  Une fois l'installation terminée, désactivez **Start HPC Cluster Manager**, puis cliquez sur **Finish**. Vous lancerez HPC Cluster Manager à une étape ultérieure pour terminer la configuration du nœud principal.

    ![Terminer][]

## Préparation de l'abonnement à Azure

Utilisez le [portail de gestion][] pour réaliser les étapes suivantes avec votre abonnement Azure. Ces instructions doivent être suivies afin de pouvoir par la suite déployer les nœuds Azure à partir du nœud principal local.

-   Téléchargement d'un certificat de gestion (requis pour les connexions sécurisées entre le nœud principal et les services Azure)

-   Création d'un service cloud Azure dans lequel les nœuds Azure (instances de rôle de travail) seront exécutés

-   Création d'un compte de stockage Azure

    > [WACOM.NOTE]Notez votre ID d'abonnement Azure. Il vous sera utile par la suite. Cette information est disponible dans vos [informations de compte][] Azure.

### Téléchargement du certificat de gestion par défaut

HPC Pack installe un certificat auto-signé sur le nœud principal, nommé « Default Microsoft HPC Azure Management certificate ». Vous pouvez le télécharger en tant que certificat de gestion Azure. Ce certificat est fourni à des fins de test et pour les déploiements pour validation technique.

1.  Sur l'ordinateur faisant office de nœud principal, connectez-vous au [portail de gestion][].

2.  Cliquez sur **Paramètres**, puis sur **Certificats de gestion**.

3.  Dans la barre de commandes, cliquez sur **Télécharger**.

    ![Paramètres de certificat][]

4.  Sur le nœud principal, accédez au fichier C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer. Cliquez ensuite sur le bouton **Check**.

    ![Télécharger le certificat][]

**Default HPC Azure Management** s'affiche dans la liste des certificats de gestion.

### Création d'un service cloud Azure

> [WACOM.NOTE]Pour des performances optimales, créez le service cloud et le compte de stockage dans la même zone géographique.

1.  Dans la barre de commandes du portail de gestion, cliquez sur **Nouveau**.

2.  Cliquez sur **Compute**, sur **Cloud Service**, puis sur **Création rapide**.

3.  Saisissez l'URL du service cloud, puis cliquez sur **Créer un service de cloud computing**.

    ![Créer le service][]

### Création d'un compte de stockage Azure

1.  Dans la barre de commandes du portail de gestion, cliquez sur **Nouveau**.

2.  Cliquez sur **Services de données**, sur **Stockage**, puis sur **Création rapide**.

3.  Saisissez l'URL du compte, puis cliquez sur **Créer un Compte de stockage**.

    ![Créer le stockage][]

## Configuration du nœud principal

Effectuez quelques unes des étapes de configuration de cluster requises afin que HPC Cluster Manager puisse déployer les nœuds Azure et soumettre des tâches.

1.  Lancez HPC Cluster Manager sur le nœud principal. Si la boîte de dialogue **Select Head Node** s'affiche, cliquez sur **Local Computer**. La liste **Deployment To-do List** s'affiche.

2.  Sous **Required deployment tasks**, cliquez sur **Configure your network**.

    ![Configurer le réseau][]

3.  Dans l'Assistant Configuration du réseau, sélectionnez **All nodes only on an enterprise network** (Topologie 5).

    ![Topologie 5][]

    > [WACOM.NOTE]Pour cette démonstration, il s'agit de la configuration la plus simple, car le nœud principal ne nécessite qu'un adaptateur réseau pour se connecter à Active Directory et à Internet. Ce didacticiel ne couvre pas les scénarios de clusters nécessitant plusieurs réseaux.

4.  Cliquez sur **Suivant** pour accepter les valeurs par défaut sur les pages suivantes de l'Assistant. Ensuite, sous l'onglet **Review**, cliquez sur **Configure** pour terminer la configuration réseau.

5.  Dans la liste **Deployment To-do List**, cliquez sur **Provide installation credentials**.

6.  Dans la boîte de dialogue **Installation Credentials**, entrez les informations d'identification du compte de domaine utilisé pour installer HPC Pack. Cliquez ensuite sur **OK**.

    ![Informations d'identification pour l'installation][]

    > [WACOM.NOTE]Les services HPC Pack utilisent les informations d'identification pour l'installation afin de déployer les nœuds de calcul locaux.

7.  Dans la liste **Deployment To-do List**, cliquez sur **Configure the naming of new nodes**.

8.  Dans la boîte de dialogue **Specify Node Naming Series**, acceptez la série de noms par défaut, puis cliquez sur **OK**.

    ![Noms de nœuds][]

    > [WACOM.NOTE]La série de noms génère des noms uniquement pour les nœuds de calcul associés au domaine. Les nœuds Azure sont nommés de façon automatique.

9.  Dans la liste **Deployment To-do List**, cliquez sur **Create a node template**. Le modèle de nœud vous sert d'exemple pour ajouter des nœuds Azure au cluster.

10. Procédez comme suit dans l'Assistant Create Node Template :

    a. Sur la page **Choose Node Template Type**, cliquez sur **Azure node template**, puis sur **Suivant**.

    ![Modèle de nœud][]

    b. Cliquez sur **Suivant** pour accepter le nom de modèle par défaut.

    c. Sur la page **Provide Subscription Information**, entrez votre ID d'abonnement Azure (disponible dans les [informations de compte][] Azure). Ensuite, dans **Management certificate**, cliquez sur **Browse**, puis sélectionnez **Default HPC Azure Management.** Cliquez ensuite sur **Suivant**.

    ![Modèle de nœud][1]

    d. Sur la page **Provide Service Information**, sélectionnez le service cloud et le compte de stockage créés plus tôt. Cliquez ensuite sur **Suivant**.

    ![Modèle de nœud][2]

    e. Cliquez sur **Suivant** pour accepter les valeurs par défaut sur les pages suivantes de l'Assistant. Ensuite, sous l'onglet **Review**, cliquez sur **Create** pour créer le modèle de nœud.

    > [WACOM.NOTE]Par défaut, le modèle de nœud Azure inclut des paramètres vous permettant de lancer (d'approvisionner) et d'arrêter manuellement les nœuds. Vous pouvez également programmer le démarrage et l'arrêt automatique de nœuds Azure.

## Ajout de nœuds Azure au cluster

Le modèle de nœud vous sert d'exemple pour ajouter des nœuds Azure au cluster. L'ajout de nœuds au cluster stocke les informations de configuration de ces nœuds afin que vous puissiez les démarrer (approvisionner) à tout moment en tant qu'instances de rôle dans le service cloud. Votre compte est uniquement facturé pour les nœuds Azure une fois que les instances de rôle sont exécutées dans le service cloud.

Dans le cadre de ce didacticiel, vous allez ajouter deux petits nœuds.

1.  Dans HPC Cluster Manager, sous **Node Management**, dans le volet **Actions**, cliquez sur **Add Node**.

    ![Ajouter un nœud][]

2.  Dans l'Assistant Add Node, sur la page **Select Deployment Method**, cliquez sur **Add Azure nodes**, puis sur **Suivant**.

    ![Ajouter un nœud Azure][]

3.  Sur la page **Specify New Nodes**, sélectionnez le modèle de nœud Azure créé précédemment (par défaut, son nom est **Default AzureNode Template**). Indiquez ensuite **2** nœuds de format « **Small** », puis cliquez sur **Suivant**.

    ![Spécifier les nœuds][]

    Pour plus d'informations sur les tailles de machines virtuelles disponibles, consultez la page [Tailles de machines virtuelles et services cloud pour Microsoft Azure][].

4.  Sur la page **Completing the Add Node Wizard**, cliquez sur **Finish**.

    Deux nœuds Azure, nommés **AzureCN-0001** et **AzureCN-0002**, sont désormais affichés dans HPC Cluster Manager. Leur état est **Not-Deployed**.

    ![Nœuds ajoutés][]

## Démarrage des nœuds Azure

Lorsque vous souhaitez utiliser les ressources de cluster dans Azure, utilisez HPC Cluster Manager pour démarrer (approvisionner) les nœuds Azure et les mettre en ligne.

1.  Dans HPC Cluster Manager, sous **Node Management**, cliquez sur un ou plusieurs nœuds, puis, dans le volet **Actions**, cliquez sur **Start**.

    ![Démarrer les nœuds][]

2.  Dans la boîte de dialogue **Start Azure Nodes**, cliquez sur **Start**.

    ![Démarrer les nœuds][3]

    Le nœud passe à l'état **Provisioning**. Le journal d'approvisionnement vous permet de suivre l'avancée de l'approvisionnement.

    ![Nœuds d'approvisionnement][]

3.  Après quelques minutes, les nœuds Azure terminent leur approvisionnement et passent à l'état **Offline**. À cette étape, les instances de rôle sont exécutées mais n'acceptent pas encore les tâches de cluster.

4.  Pour confirmer l'exécution des instances de rôle, dans le [portail de gestion][], cliquez sur **Cloud Services**, cliquez sur le nom de votre service cloud, puis sur **Instances**.

    ![Instances en cours d'exécution][]

    Deux instances de rôle de travail sont affichées comme étant exécutées dans le service. HPC Pack déploie également deux instances **HpcProxy** (taille moyenne) pour gérer la communication entre le nœud principal et Azure.

5.  Pour faire passer les nœuds Azure en ligne afin qu'ils exécutent des tâches de cluster, sélectionnez-les, cliquez avec le bouton droit, puis cliquez sur **Bring Online**.

    ![Nœuds hors ligne][]

    HPC Cluster Manager indique que les nœuds sont dans l'état **Online**.

## Exécution d'une commande sur le cluster

Vous pouvez utiliser la commande **clusrun** de HPC Pack pour exécuter une commande ou une application sur un ou plusieurs nœuds de cluster. Par exemple, utilisez **clusrun** pour obtenir la configuration IP des nœuds Azure.

1.  Ouvrez une invite de commandes sur le nœud principal.

2.  Tapez la commande suivante :

    `clusrun /nodes:azurecn* ipconfig`

3.  Vous devez voir des informations similaires à ce qui suit.

    ![Clusrun][]

## Exécution d'une tâche de test

Vous pouvez envoyer une tâche de test à exécuter sur le cluster hybride. Le présent exemple est un simple balayage paramétrique (un type de calcul intrinsèquement parallèle) qui exécute des sous-tâches qui ajoutent un entier à lui-même via la commande **set /a**. Tous les nœuds du cluster contribuent à terminer les sous-tâches pour les entiers de 1 à 100.

1.  Dans HPC Cluster Manager, sous **Job Management**, dans le volet **Actions**, cliquez sur **New Parametric Sweep Job**.

    ![Nouvelle tâche][]

2.  Dans la boîte de dialogue **New Parametric Sweep Job**, sous **Command line**, tapez `set /a *+*` (en remplaçant la ligne de commande par défaut qui s'affiche). Ne modifiez pas les autres paramètres par défaut, puis cliquez sur **Submit** pour envoyer la tâche.

    ![Balayage paramétrique][]

3.  Une fois la tâche terminée, cliquez deux fois sur la tâche **My Sweep Task**.

4.  Cliquez sur **View Tasks**, puis sur une sous-tâche pour afficher la sortie calculée associée.

    ![Résultats de la tâche][]

5.  Pour connaître le nœud ayant effectué le calcul pour cette sous-tâche, cliquez sur **Allocated Nodes** (votre cluster peut indiquer un nom de nœud différent).

    ![Résultats de la tâche][4]

## Arrêt des nœuds Azure

Après avoir testé le cluster, utilisez HPC Cluster Manager pour arrêter les nœuds Azure et ainsi éviter toute facturation inutile de votre compte. Ceci arrête le service cloud et supprime les instances de rôle Azure.

1.  Dans HPC Cluster Manager, sous **Node Management,**, sélectionnez les deux nœuds Azure. Ensuite, dans le volet **Actions**, cliquez sur **Stop**.

    ![Arrêter les nœuds][]

2.  Dans la boîte de dialogue **Stop Azure Nodes**, cliquez sur **Stop**.

    ![Arrêter les nœuds][5]

3.  Le nœud passe à l'état **Stopping**. Après quelques minutes, HPC Cluster Manager signale les nœuds comme **Not-Deployed**.

    ![Nœuds non déployés][]

4.  Pour confirmer que les instances de rôle ne sont plus exécutées dans Azure, dans le [portail de gestion][], cliquez sur **Cloud Services**, sur le nom de votre service cloud, puis sur **Instances**. Aucune instance n'est déployée dans l'environnement de production.

    ![Aucune instance][]

    C'est ici que s'achève ce didacticiel.

## Ressources associées

-   [HPC Pack 2012 R2 et HPC Pack 2012][]
-   [Intégration à Azure avec Microsoft HPC Pack][instructions détaillées]
-   [Déploiement d'applications sur des nœuds Azure][]
-   [Microsoft HPC Pack dans les machines virtuelles Azure][]

  [Cluster HPC hybride]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
  [instructions détaillées]: http://go.microsoft.com/fwlink/p/?LinkID=200493
  [plusieurs tailles]: http://go.microsoft.com/fwlink/p/?LinkId=389844
  [À propos des instances de calcul intensif A8 et A9]: http://go.microsoft.com/fwlink/p/?Linkid=328042
  [Conditions préalables]: #BKMK_Prereq
  [Installation du HPC Pack sur le nœud principal]: #BKMK_DeployHN
  [Préparation de l'abonnement à Azure]: #BKMK_Prpare
  [Configuration du nœud principal]: #BKMK_ConfigHN
  [Ajout de nœuds Azure au cluster]: #BKMK_worker
  [Démarrage des nœuds Azure]: #BKMK_start
  [Exécution d'une commande sur le cluster]: #BKMK_RunCommand
  [Exécution d'une tâche de test]: #BKMK_RunJob
  [Arrêt des nœuds Azure]: #BKMK_stop
  [Création d’un compte Azure]: http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/
  [version d'évaluation]: http://technet.microsoft.com/evalcenter/dn205286.aspx
  [Téléchargez]: http://go.microsoft.com/fwlink/p/?linkid=389557
  [Configuration de HPC Pack 2012]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
  [Sélectionner le type d'installation]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
  [Règles d'installation]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
  [Configuration de base de données]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
  [Installer]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
  [Terminer]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
  [portail de gestion]: https://manage.windowsazure.com
  [informations de compte]: [https://account.windowsazure.com/Subscriptions
  [Paramètres de certificat]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
  [Télécharger le certificat]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
  [Créer le service]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
  [Créer le stockage]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
  [Configurer le réseau]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
  [Topologie 5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
  [Informations d'identification pour l'installation]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
  [Noms de nœuds]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
  [Modèle de nœud]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
  [1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
  [2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
  [Ajouter un nœud]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
  [Ajouter un nœud Azure]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
  [Spécifier les nœuds]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
  [Tailles de machines virtuelles et services cloud pour Microsoft Azure]: http://msdn.microsoft.com/library/windowsazure/dn197896.aspx
  [Nœuds ajoutés]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
  [Démarrer les nœuds]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
  [3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
  [Nœuds d'approvisionnement]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
  [Instances en cours d'exécution]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
  [Nœuds hors ligne]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
  [Clusrun]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
  [Nouvelle tâche]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
  [Balayage paramétrique]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
  [Résultats de la tâche]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
  [4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
  [Arrêter les nœuds]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
  [5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
  [Nœuds non déployés]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
  [Aucune instance]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png
  [HPC Pack 2012 R2 et HPC Pack 2012]: http://go.microsoft.com/fwlink/p/?LinkID=263697
  [Déploiement d'applications sur des nœuds Azure]: http://go.microsoft.com/fwlink/p/?LinkId=325317
  [Microsoft HPC Pack dans les machines virtuelles Azure]: http://go.microsoft.com/fwlink/p/?linkid=330375
