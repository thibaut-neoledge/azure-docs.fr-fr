<properties
   pageTitle="Création de clusters Hadoop dans HDInsight | Microsoft Azure"
   	description="Apprenez à créer des clusters pour Azure HDInsight à l'aide du portail Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Création de clusters Hadoop basés sur Windows dans HDInsight à l'aide du portail Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-create-windows-cluster-selector.md)]

Apprenez à créer un cluster Hadoop dans HDInsight à l'aide du portail Azure. Le [portail Azure](azure-portal-overview.md) Microsoft permet de configurer et de gérer l'ensemble de vos ressources Azure. Le portail Azure est l'un des outils que vous pouvez utiliser pour créer un cluster Hadoop basé sur Linux ou basé sur Windows dans HDInsight. Pour accéder à d'autres outils et fonctions de création de clusters, cliquez sur l'onglet de sélection situé en haut de cette page, ou consultez la section relative aux [méthodes de création de clusters](hdinsight-provision-clusters.md#cluster-creation-methods).

###Configuration requise :

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Créer des clusters


**Création d'un cluster HDInsight**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, sur **Analyse des données**, puis sur **HDInsight**.

    ![Créer un cluster dans le portail Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Créer un cluster dans le portail Azure")

3. Tapez ou sélectionnez les valeurs suivantes :

  * **Nom de cluster** : saisissez le nom du cluster. Une coche verte s‘affiche en face du nom de cluster si le nom est disponible.
  * **Type de cluster** : sélectionnez **Hadoop**. Les autres options incluent **HBase**, **Storm** et **Spark**.
  * **Système d'exploitation de cluster** : sélectionnez **Windows**. Pour créer un cluster basé sur Linux, sélectionnez **Linux**.
  * **Version** : voir [Versions HDInsight](hdinsight-component-versioning.md).
  * **Abonnement** : sélectionnez l’abonnement Azure utilisé pour créer ce cluster.
  * **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un. Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.
  * **Informations d’identification** : configurer le nom d’utilisateur et le mot de passe de l’utilisateur Hadoop (HTTP). Si vous activez le bureau à distance du cluster, vous devrez configurer le nom d'utilisateur et le mot de passe du bureau distant, et une date d'expiration de compte. Cliquez sur **Sélectionner** au bas de l’écran pour enregistrer les modifications.

	   	![Fournir les informations d’identification du cluster](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Source de données** : créez un compte de stockage Azure ou sélectionnez un compte de stockage Azure existant à utiliser comme système de fichiers par défaut pour le cluster.

   		![Panneau Source de données](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Méthode de sélection**: définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d’accès** si vous souhaitez saisir le **Nom de stockage** et la **Clé d’accès** d’un compte de stockage existant.
  		* **Sélectionner le compte de stockage / Créer un nouveau**: cliquez sur **Sélectionner le compte de stockage** pour parcourir et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer un nouveau** pour créer un nouveau compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.
  		* **Choisir le conteneur par défaut**: utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.
  		* **Emplacement**: zone géographique où se trouve le compte de stockage ou dans laquelle il sera créé. Cet emplacement détermine l'emplacement du cluster.  Le cluster et le compte de stockage par défaut doit se situer dans le même datacenter Azure.
  	
  * **NIVEAUX DE TARIFICATION DU NŒUD** : définissez le nombre de nœuds de travail dont vous avez besoin pour le cluster. Le coût estimé du cluster s'affiche dans le panneau.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuration facultative** sert à sélectionner la version de cluster et à configurer d’autres paramètres facultatifs, par exemple ajouter un **Réseau virtuel**, définir un **Metastore externe** pour conserver les données Hive et Oozie, utiliser des actions de script pour personnaliser un cluster et installer des composants personnalisés ou utiliser des comptes de stockage supplémentaires avec le cluster.

  		* **Version de HDInsight**: sélectionnez la version que vous souhaitez utiliser pour le cluster. Pour plus d’informations, consultez la rubrique [Versions de clusters HDInsight](hdinsight-component-versioning.md).
  		* **Réseau virtuel**: sélectionnez un réseau virtuel Azure et le sous-réseau si vous souhaitez placer le cluster dans un réseau virtuel.  

			![Panneau Réseau virtuel](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Le cluster HDInsight Windows ne peut être placé que sur un réseau virtuel classique.
  

  		
		* **Metastores externes**: permet de spécifier la base de données SQL Azure que vous souhaitez utiliser pour enregistrer les métadonnées Hive et Oozie associée au cluster.
 
            > [AZURE.NOTE] La configuration Metastore n’est pas disponible pour les types de cluster HBase.

			![Panneau de metastores personnalisés](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			Pour **Utiliser une base de données SQL existante pour les métadonnées Hive** cliquez sur **Oui**, sélectionnez une base de données SQL, puis indiquez le nom d’utilisateur/mot de passe pour la base de données. Répétez ces étapes si vous souhaitez **utiliser une base de données SQL existante pour les métadonnées Oozie**. Cliquez sur **Sélectionne** jusqu’à ce que vous reveniez au panneau **Configuration facultative**.


			>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.
		
  		* **Actions de script** si vous voulez utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md). Dans le panneau Actions de script, saisissez les informations requises, comme illustré dans la capture d’écran ci-dessous.
  	

			![Panneau Action de script](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Clés de stockage Azure**: permet de spécifier des comptes de stockage supplémentaire à associer au cluster. Dans le panneau **CLÉS DE STOCKAGE AZURE**, cliquez sur **AJOUTER UNE CLÉ DE STOCKAGE**, puis sélectionnez un compte de stockage existant ou créez-en un.
    

			![Panneau Stockage supplémentaire](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Cliquez sur **Créer**. En sélectionnant l’option **Épingler au tableau d’accueil**, une vignette est ajoutée pour le cluster dans le tableau d’accueil de votre portail. L'icône indique que le cluster est en cours de création et sera modifiée pour représenter l'icône HDInsight une fois la création terminée.
	
    La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la mosaïque du tableau d’accueil ou l’entrée **NOTIFICATIONS** à gauche de la page pour vérifier le processus d’approvisionnement.
	

5. Une fois la création terminée, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.


	![Panneau Cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propriétés du cluster")


	Utilisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans la section dans la section **Bases** :


	* **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.
	* **Tableau de bord**, **Tableau de bord du cluster** et **URL** : il s’agit de toutes les façons d’accéder au tableau de bord de cluster, qui est un portail web permettant d’exécuter des tâches sur le cluster.
	* **Bureau à distance** : permet d’activer/de désactiver le Bureau à distance sur les nœuds de cluster.
	* **Mise à l’échelle du cluster** : permet de modifier le nombre de nœuds de travail pour ce cluster.
	* **Supprimer** : permet de supprimer le cluster HDInsight.
	* **Démarrage rapide** (![icône nuage et foudre = démarrage rapide](./media/hdinsight-provision-clusters/quickstart.png)) : affiche des informations qui vous aideront à utiliser HDInsight.
	* **Utilisateurs** (![icône d’utilisateurs](./media/hdinsight-provision-clusters/users.png)) : permet de définir des autorisations pour la _gestion au moyen du portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.
	

		> [AZURE.IMPORTANT] Il affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail et n’a aucun effet sur les personnes autorisées à se connecter au cluster HDInsight ou à soumettre des travaux.
		
	* **Balises** (![icône de balise](./media/hdinsight-provision-clusters/tags.png)) : permet de spécifier des paires clé/valeur pour définir une taxonomie personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.

##Personnalisation des clusters

- Voir [Personnalisation de clusters HDInsight à l'aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Voir [Personnalisation de clusters HDInsight basés sur Windows à l'aide d'une action de script](hdinsight-hadoop-customize-cluster.md).

##Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d’Azure HDInsight](hdinsight-get-started.md) : apprenez à utiliser votre cluster HDInsight
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md) : découvrez comment envoyer des tâches par programme à HDInsight
* [Gérer les clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0128_2016-->