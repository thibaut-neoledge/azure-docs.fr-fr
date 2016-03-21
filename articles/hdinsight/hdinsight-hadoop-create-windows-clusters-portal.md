<properties
   pageTitle="Création de clusters Hadoop dans HDInsight | Microsoft Azure"
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
   ms.date="03/08/2016"
   ms.author="jgao"/>

# Création de clusters Hadoop basés sur Windows dans HDInsight à l'aide du portail Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer un cluster Hadoop dans HDInsight à l'aide du portail Azure. Le [portail Azure](../azure-portal-overview.md) Microsoft permet de configurer et de gérer l'ensemble de vos ressources Azure. Le portail Azure est l'un des outils que vous pouvez utiliser pour créer un cluster Hadoop basé sur Linux ou basé sur Windows dans HDInsight. Pour accéder à d'autres outils et fonctions de création de clusters, cliquez sur l'onglet de sélection situé en haut de cette page, ou consultez la section relative aux [méthodes de création de clusters](hdinsight-provision-clusters.md#cluster-creation-methods).

##Configuration requise :

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Créer des clusters


**Création d'un cluster HDInsight**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, sur **Analyse des données**, puis sur **HDInsight**.

    ![Créer un cluster dans le portail Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Créer un cluster dans le portail Azure")

3. Tapez ou sélectionnez les valeurs suivantes :

  * **Nom de cluster** : saisissez le nom du cluster. Une coche verte s‘affiche en face du nom de cluster si le nom est disponible.
  * **Type de cluster** : sélectionnez **Hadoop**. Les autres options incluent **HBase**, **Storm** et **Spark**.
  * **Système d’exploitation de cluster** : sélectionnez **Windows**. Pour créer un cluster basé sur Linux, sélectionnez **Linux**.
  * **Version** : voir [Versions HDInsight](hdinsight-component-versioning.md).
  * **Abonnement** : sélectionnez l’abonnement Azure utilisé pour créer ce cluster.
  * **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez-en un. Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.
  * **Informations d’identification** : configurer le nom d’utilisateur et le mot de passe de l’utilisateur Hadoop (HTTP). Si vous activez le bureau à distance du cluster, vous devrez configurer le nom d'utilisateur et le mot de passe du bureau distant, et une date d'expiration de compte. Cliquez sur **Sélectionner** au bas de l’écran pour enregistrer les modifications.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Source de données** : créez un compte de stockage Azure ou sélectionnez un compte de stockage Azure existant à utiliser comme système de fichiers par défaut pour le cluster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Selection Method**: Set this to **From all subscriptions** to enable browsing of storage accounts from all your subscriptions. Set this to **Access Key** if you want to enter the **Storage Name** and **Access Key** of an existing storage account.
  		* **Select storage account / Create New**: Click **Select storage account** to browse and select an existing storage account you want to associate with the cluster. Or, click **Create New** to create a new storage account. Use the field that appears to enter the name of the storage account. A green check will appear if the name is available.
  		* **Choose Default Container**: Use this to enter the name of the default container to use for the cluster. While you can enter any name here, we recommend using the same name as the cluster so that you can easily recognize that the container is used for this specific cluster.
  		* **Location**: The geographic region that the storage account is in, or will be created in. This location will determine the cluster location.  The cluster and its default storage account must co-locate in the same Azure data center.
  	
  * **NIVEAUX DE TARIFICATION DU NŒUD** : définissez le nombre de nœuds de travail dont vous avez besoin pour le cluster. Le coût estimé du cluster s'affiche dans le panneau.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuration facultative** sert à sélectionner la version de cluster et à configurer d’autres paramètres facultatifs, par exemple ajouter un **Réseau virtuel**, définir un **Metastore externe** pour conserver les données Hive et Oozie, utiliser des actions de script pour personnaliser un cluster et installer des composants personnalisés ou utiliser des comptes de stockage supplémentaires avec le cluster.

  		* **HDInsight Version**: Select the version you want to use for the cluster. For more information, see [HDInsight cluster versions](hdinsight-component-versioning.md).
  		* **Virtual Network**: Select an Azure virtual network and the subnet if you want to place the cluster into a virtual network.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			For information on using HDInsight with a Virtual Network, including specific configuration requirements for the Virtual Network, see [Extend HDInsight capbilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).
  

  		
		* **External Metastores**: Specify an Azure SQL database to store Hive and Oozie metadata associated with the cluster.
 
            > [AZURE.NOTE] Metastore configuration is not available for HBase cluster types.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			For **Use an existing SQL DB for Hive** metadata, click **Yes**, select a SQL database, and then provide the username/password for the database. Repeat these steps if you want to **Use an existing SQL DB for Oozie metadata**. Click **Select** till you are back on the **Optional Configuration** blade.


			>[AZURE.NOTE] The Azure SQL database used for the metastore must allow connectivity to other Azure services, including Azure HDInsight. On the Azure SQL database dashboard, on the right side, click the server name. This is the server on which the SQL database instance is running. Once you are on the server view, click **Configure**, and then for **Azure Services**, click **Yes**, and then click **Save**.
		
  		* **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure Storage Keys**: Specify additional storage accounts to associate with the cluster. In the **Azure Storage Keys** blade, click **Add a storage key**, and then select an existing storage account or create a new account.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Cliquez sur **Créer**. En sélectionnant l’option **Épingler au tableau d’accueil**, une vignette est ajoutée pour le cluster dans le tableau d’accueil de votre portail. L'icône indique que le cluster est en cours de création et sera modifiée pour représenter l'icône HDInsight une fois la création terminée.
	
    La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la mosaïque du tableau d’accueil ou l’entrée **NOTIFICATIONS** à gauche de la page pour vérifier le processus d’approvisionnement.
	

5. Une fois la création terminée, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.


	![Panneau Cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propriétés du cluster")


	Utilisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans la section dans la section **Bases** :


	* **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.
	* **Tableau de bord**, **Tableau de bord du cluster** et **URL** : il s’agit de toutes les façons d’accéder au tableau de bord de cluster, qui est un portail web permettant d’exécuter des tâches sur le cluster.
	* **Bureau à distance** : permet d’activer/de désactiver le Bureau à distance sur les nœuds de cluster.
	* **Mise à l’échelle du cluster** : permet de modifier le nombre de nœuds de travail pour ce cluster.
	* **Supprimer** : permet de supprimer le cluster HDInsight.
	* **Démarrage rapide** (![icône nuage et foudre = démarrage rapide](./media/hdinsight-provision-clusters/quickstart.png)) : affiche des informations qui vous aideront à utiliser HDInsight.
	* **Utilisateurs** (![icône d’utilisateurs](./media/hdinsight-provision-clusters/users.png)) : permet de définir des autorisations pour la _gestion au moyen du portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.
	

		> [AZURE.IMPORTANT] Il affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail et n’a aucun effet sur les personnes autorisées à se connecter au cluster HDInsight ou à soumettre des travaux.
		
	* **Balises** (![icône de balise](./media/hdinsight-provision-clusters/tags.png)) : permet de spécifier des paires clé/valeur pour définir une taxonomie personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.

##Personnalisation des clusters

- Consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consultez [Personnalisation de clusters HDInsight basés sur Windows à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md).

##Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d’Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) : apprenez à utiliser votre cluster HDInsight
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md) : découvrez comment envoyer des tâches par programme à HDInsight
* [Gérer les clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0309_2016-->