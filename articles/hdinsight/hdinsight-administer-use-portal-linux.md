<properties
	pageTitle="Gestion des clusters Hadoop basés sur Linux dans HDInsight avec le portail Azure | Microsoft Azure"
	description="Apprenez à créer et à gérer des clusters HDInsight basés sur Linux avec le portail Azure."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/07/2016"
	ms.author="jgao"/>

#Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]

À l’aide du [portail Azure][azure-portal], vous pouvez gérer des clusters Linux dans Azure HDInsight. Utilisez le sélecteur de tabulations pour obtenir des informations sur la création de clusters Hadoop dans HDInsight à l’aide d’autres outils.

**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##Ouvrir le portail

1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Une fois que vous ouvrez le portail, vous pouvez :

	- Cliquez sur **Nouveau** dans le menu de gauche pour créer un nouveau cluster :
	
		![bouton nouveau cluster HDInsight](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
	- Cliquez sur **Clusters HDInsight** dans le menu de gauche pour afficher les clusters existants
	
		![bouton de cluster HDinsight du portail Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

        Si **HDInsight** n’apparaît pas dans le menu de gauche, cliquez sur **Parcourir**.

        ![bouton de cluster Parcourir du portail Azure](./media/hdinsight-administer-use-portal-linux/azure-portal-browse-button.png)

##Créer des clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight fonctionne avec un large éventail de composants Hadoop. Pour obtenir la liste des composants vérifiés et pris en charge, consultez la rubrique [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md). Pour obtenir des informations générales sur la création de clusters, consultez [Création de clusters Hadoop dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

##Énumération et affichage des clusters

1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche pour afficher les clusters existants.
3. Cliquez sur le nom du cluster. Si la liste de clusters est longue, vous pouvez utiliser le filtre en haut de la page.
4. Double-cliquez sur un cluster dans la liste pour afficher les détails.

	**Menu et essentials** :

	![essentials du cluster hdinsight du portail Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
	
	- **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.
	- **Tableau de bord**, **Tableau de bord du cluster** et **URL : il s’agit de toutes les façons d’accéder au tableau de bord de cluster, qui est une interface Ambari Web pour les clusters basés sur Linux.
- **Secure Shell** : affiche les instructions pour se connecter au cluster à l’aide d’une connexion Secure Shell (SSH).
	- **Mise à l’échelle du cluster** : permet de modifier le nombre de nœuds de travail pour ce cluster.
	- **Supprimer** : supprime le cluster.
	- **Démarrage rapide (![icône nuage et foudre = démarrage rapide](./media/hdinsight-administer-use-portal-linux/quickstart.png))** : affiche des informations qui vous aideront à prendre en main HDInsight.
	- **Utilisateurs(![icône d’utilisateurs](./media/hdinsight-administer-use-portal-linux/users.png))** : permet de définir des autorisations pour la _gestion via le portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.
	
		> [AZURE.IMPORTANT] Ce paramètre affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail Azure et n’a aucun effet sur les personnes autorisées à se connecter ou à soumettre des tâches au cluster HDInsight.
	- **Balises(![icône de balise](./media/hdinsight-administer-use-portal-linux/tags.png))** : permettent de spécifier des paires clé/valeur pour définir une classification personnalisée de vos Cloud Services. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.
	- **Vues Ambari** : liens vers Ambari Web.
	
	> [AZURE.IMPORTANT] Pour gérer les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation d’Ambari, consultez [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md).

	**Utilisation** :
	
	![utilisation de cluster hdinsight du portail Azure](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-cluster-usage.png)
	
5. Cliquez sur **Paramètres**.

	![utilisation de cluster hdinsight du portail Azure](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.cluster.settings.png)

	- **Journaux d’audit** :
    - **Démarrage rapide** : affiche des informations qui vous aideront à prendre en main HDInsight.
	- **Connexion de cluster** : modifie le nom d’utilisateur de cluster et le mot de passe HTTP.
	- **Mise à l’échelle de cluster** : augmenter et diminuer le nombre de nœuds de travail de cluster.
    - **Secure Shell** : affiche les instructions pour se connecter au cluster à l’aide d’une connexion Secure Shell (SSH).
    - **Partenaire HDInsight** : permet d’ajouter ou de supprimer le partenaire HDInsight actuel.
	- **Metastores externes** : affiche les metastores Hive et Oozie. Les metastores ne peuvent être configurés qu’au cours du processus de création de cluster.
    - **Actions de script** : exécutent des scripts Bash sur le cluster.
    - **Propriétés** : affichez les propriétés du cluster
	- **Clés de stockage Azure** : permet d’afficher le compte de stockage par défaut et sa clé. Le compte de stockage est configuré pendant le processus de création de cluster.
	- **Identité AAS de cluster** : 
	- **Utilisateurs** : permet de définir des autorisations pour la _gestion via le portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.
	- **Balises** : elles permettent de spécifier des paires clé/valeur pour définir une classification personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.
    
    > [AZURE.NOTE] Ceci est une liste générique des paramètres disponibles. Ils ne sont pas tous présents pour tous les types de clusters.

6. Cliquez sur **Propriétés** :

	Les propriétés sont les suivantes :
	
	- **Nom d’hôte** : nom du Cluster.
	- **URL de cluster**.
	- **État** : inclut Abandonné, Accepté, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, En fonctionnement, En cours d’exécution, Erreur, En cours de suppression, Supprimé, TimedOut, DeleteQueued, DeleteTimedOut, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
	- **Région** : emplacement Azure. Pour obtenir la liste des emplacements Azure pris en charge, consultez la zone de liste déroulante **Région** sur [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
	- **Données créées**.
	- **Système d’exploitation** : **Windows** ou **Linux**.
	- **Type** : Hadoop, Hbase, Storm, Spark. 
	- **Version**. Voir [Versions HDInsight](hdinsight-component-versioning.md)
	- **Abonnement** : nom de l’abonnement.
	- **ID d’abonnement**.
    - **Source de données par défaut** : système de fichiers de cluster par défaut.
	- **Niveau de tarification des nœuds de travail**.
	- **Niveau de tarification du nœud principal**.

##Suppression des clusters

La suppression d’un cluster ne supprime pas le compte de stockage par défaut ou les comptes de stockage liés. Vous pouvez recréer le cluster en utilisant les mêmes comptes de stockage et les mêmes metastores. Si possible, utilisez un nouveau conteneur d’objets blob par défaut quand vous recréez le cluster.

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Supprimer** dans le menu supérieur, puis suivez les instructions.

Voir aussi [Pause/arrêt de clusters](#pauseshut-down-clusters).

##Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

>[AZURE.NOTE] Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés. Voir [Énumération et affichage des clusters](hdinsight-adminster-use-management-portal/#list-and-show-clusters).

Impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

- Hadoop

	Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

	Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Pour cette raison, toutes les tâches en cours ou en attente échouent lors de la réalisation de l'opération de mise à l'échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.

- HBase

	Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster et en exécutant les commandes suivantes à partir d’une fenêtre d’invite de commandes :

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Pour plus d’informations sur l’utilisation de l’interpréteur de commandes HBase, voir
- Storm

	Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Mais une fois l’opération de mise à l’échelle terminée avec succès, vous devrez rééquilibrer la topologie.

	Cela peut se faire de deux façons à l’aide de :

	* l'interface utilisateur Web de Storm
	* l’outil d’interface de ligne de commande (CLI)

	Pour plus d’informations, consultez la documentation [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

	![hdinsight storm mise à l’échelle rééquilibrage](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Pour mettre à l’échelle des clusters**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis sur **Mettre le cluster à l’échelle**.
4. Entrez une valeur dans le champ **Nombre de nœuds de travail**. La limite du nombre de nœuds de cluster varie selon les abonnements Azure. Vous pouvez contacter le support de facturation pour augmenter la limite. Les informations de coût reflètent les modifications apportées au nombre de nœuds.

	![hdinsight hadoop hbase storm spark mise à l’échelle](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

##Pause/Arrêt de clusters

La plupart des travaux Hadoop sont les traitements par lots exécutés occasionnellement seulement. Pour la plupart des clusters Hadoop, il existe de longues périodes pendant lequel le cluster n’est pas utilisé pour le traitement. Avec HDInsight, vos données sont stockées Azure Storage, pour que vous puissiez supprimer un cluster en toute sécurité s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés.

Il existe de nombreuses façons de programmer le processus :

- Utilisateur d’Azure Data Factory. Pour créer des services liés HDInsight à la demande, consultez [Création de clusters Hadoop à la demande basés sur Linux dans HDInsight avec Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md).
- Utilisation d’Azure PowerShell Voir [Analyse des données sur les retards de vol](hdinsight-analyze-flight-delay-data.md).
- Utiliser l’interface de ligne de commande Microsoft Azure Voir [Gestion des clusters HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
- Utilisation du kit de développement logiciel .NET. Voir [Envoyer des tâches Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Pour les informations de tarification, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Pour supprimer un cluster du portail, consultez [Supprimer les clusters](#delete-clusters)

##Modifier le nom d’utilisateur du cluster

Un cluster HDInsight peut disposer de deux comptes d'utilisateur. Le nom d’utilisateur du cluster HDInsight ( le compte d’utilisateur HTTP) et le compte d’utilisateur SSH sont créés durant le processus de création du cluster. Vous pouvez utiliser l’interface utilisateur web d’Ambari pour modifier le nom d’utilisateur et le mot de passe du compte d’utilisateur du cluster :

**Pour modifier le mot de passe de l’utilisateur du cluster HDInsight**

1. Connectez-vous à l’interface utilisateur web d’Ambari à l’aide des informations d’identification de l’utilisateur du cluster HDInsight. Le nom d’utilisateur par défaut est **admin**. L’URL est **https://<HDInsight Cluster Name>azurehdinsight.net**.
2. Cliquez sur **Admin** dans le menu supérieur et cliquez sur « Gérer Ambari ». 
3. Dans le menu de gauche, cliquez sur **Utilisateurs**.
4. Cliquez sur **Admin**.
5. Cliquez sur **Modifier le mot de passe**.

##Octroyer/Révoquer l’accès

Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Par défaut, l'accès à ces services est octroyé. Vous pouvez révoquer ou octroyer l’accès à l’aide de l’interface [Azure CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) ou d’[Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access).

##Rechercher l’ID d’abonnement

**Pour rechercher vos ID d’abonnement Azure**

1. Connectez-vous au [portail][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, puis cliquez sur **Abonnements**. Chaque abonnement a un nom et un ID.

Chaque cluster est lié à un abonnement Azure. L’ID d’abonnement est indiqué dans la mosaïque **Essential** du cluster. Voir [Énumération et affichage des clusters](#list-and-show-clusters).

##Trouvez le groupe de ressources 

En mode ARM, chaque cluster HDInsight est créé avec un groupe de ressources Azure. Le groupe de ressources Azure appartenant à un cluster apparaît dans :

- La liste de clusters comporte une colonne **Groupe de ressources**.
- Mosaïque **Essential** du cluster.  

Voir [Énumération et affichage des clusters](#list-and-show-clusters).


##Trouvez le compte de stockage par défaut

Chaque cluster HDInsight dispose d’un compte de stockage par défaut. Le compte de stockage par défaut et ses clés pour un cluster se trouvent sous **Paramètres**/**Propriétés**/**Clés Azure Storage**. Voir [Énumération et affichage des clusters](#list-and-show-clusters).


##Exécuter des requêtes Hive

Vous ne pouvez pas exécuter un travail Hive directement à partir du portail Azure. À la place, utilisez l’affichage Hive dans l’interface utilisateur web d’Ambari.

**Pour exécuter des requêtes Hive à l’aide de l’affichage Hive d’Ambari**

1. Connectez-vous à l’interface utilisateur web d’Ambari à l’aide des informations d’identification de l’utilisateur du cluster HDInsight. Le nom d’utilisateur par défaut est **admin**. L’URL est **https://<HDInsight Cluster Name>azurehdinsight.net**.
2. Ouvrez l’affichage Hive comme illustré dans la capture d’écran suivante :  

	![Affichage Hive dans HDInsight](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. Cliquez sur **Requête** dans le menu supérieur.
4. Entrez une requête Hive dans l’**éditeur de requête**, puis cliquez sur **Exécuter**.

##Surveiller des travaux

Consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md#monitoring).

##Parcourir les fichiers

Vous pouvez utiliser le portail Azure pour parcourir le contenu du conteneur par défaut.

1. Connectez-vous à [https://portal.azure.com](https://portal.azure.com).
2. Cliquez sur **Clusters HDInsight** dans le menu de gauche pour afficher les clusters existants.
3. Cliquez sur le nom du cluster. Si la liste de clusters est longue, vous pouvez utiliser le filtre en haut de la page.
4. Cliquez sur **Paramètres**.
5. Dans le panneau **Paramètres**, cliquez sur **Clés de stockage Azure**.
6. Cliquez sur le nom du compte de stockage par défaut.
7. Cliquez sur la mosaïque **Objets blob**.
8. Cliquez sur le nom du conteneur par défaut.


##Surveiller l’utilisation du cluster

La section __Utilisation__ du panneau du cluster HDInsight affiche des informations sur le nombre de cœurs disponibles pour votre abonnement à utiliser avec HDInsight, ainsi que le nombre de cœurs alloués à ce cluster et la manière dont ils sont alloués pour les nœuds présents dans ce cluster. Voir [Énumération et affichage des clusters](#list-and-show-clusters).

> [AZURE.IMPORTANT] Pour surveiller les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation d’Ambari, voir [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md)

##Se connecter à un cluster

Consultez [Utilisation de Hive avec Hadoop dans HDInsight via SSH](hdinsight-hadoop-use-hive-ssh.md#ssh).
	
##Étapes suivantes
Dans cet article, vous avez appris à créer un cluster HDInsight à l’aide du portail et à ouvrir l’outil en ligne de commande Hadoop. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
* [Création de clusters HDInsight](hdinsight-provision-clusters.md)
* [Utilisation d'Hive dans HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig dans HDInsight](hdinsight-use-pig.md)
* [Utilisation de Sqoop dans HDInsight](hdinsight-use-sqoop.md)
* [Prise en main d’Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Ligne de commande Hadoop"

<!---HONumber=AcomDC_0413_2016-->