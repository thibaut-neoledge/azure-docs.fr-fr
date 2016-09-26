<properties
   	pageTitle="Créer des clusters Hadoop, HBase, Storm ou Spark sur Linux dans HDInsight à l’aide du portail | Microsoft Azure"
   	description="Découvrez comment créer des clusters Hadoop, HBase, Storm ou Spark sur Linux pour HDInsight avec un navigateur web et le portail Azure en version préliminaire."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="jhubbard"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="07/07/2016"
   	ms.author="nitinme"/>


#Créer des clusters Linux dans HDInsight à l’aide du portail Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-selector-create-clusters.md)]

Le portail Azure est un outil web qui permet de gérer les services et les ressources hébergés dans le cloud Microsoft Azure. Dans cet article, vous apprendrez à créer des clusters HDInsight Linux avec le portail.

## Composants requis

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- __Un navigateur web récent__. Le portail Azure utilise les langages HTML5 et Javascript. Il est possible qu’il ne fonctionne pas correctement sur les anciens navigateurs web.

##Créer des clusters

Le portail Azure expose la plupart des propriétés du cluster. Avec le modèle Azure Resource Manager, vous pouvez masquer de nombreux détails. Pour plus d’informations, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **NOUVEAU**, sur **Analyse des données**, puis sur **HDInsight**.

    ![Création d’un cluster dans le portail Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Création d’un cluster dans le portail Azure")
3. Entrez le **nom du cluster** : ce nom doit être globalement unique.
4. Cliquez sur **Sélectionner un type de cluster**, puis choisissez :

    - **Type de cluster** : si vous ne savez pas lequel choisir, sélectionnez **Hadoop**. Il s’agit du type de cluster le plus répandu.
    - **Système d’exploitation** : sélectionnez **Linux**.
    - **Version** : utilisez la version par défaut si vous ne savez pas laquelle choisir. Pour plus d’informations, consultez [Versions de clusters HDInsight](hdinsight-component-versioning.md).
    - **Niveau de cluster**: Azure HDInsight propose deux catégories d’offres de cloud Big Data : Niveau Standard et Niveau Premium. Pour plus d’informations, consultez [Niveaux de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-tiers).
    
    ![Configuration du niveau premium HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)

4. Cliquez sur **Abonnement** pour sélectionner l’abonnement Azure qui sera utilisé pour le cluster.

5. Cliquez sur **Groupe de ressources** pour sélectionner un groupe de ressources existant, ou sur **Nouveau** pour créer un nouveau groupe de ressources

	> [AZURE.NOTE] Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.

6. Cliquez sur **Informations d’identification**, puis entrez un mot de passe pour l’utilisateur admin. Vous devez également entrer un **Nom d’utilisateur SSH** et un **MOT DE PASSE** ou une **CLÉ PUBLIQUE**, qui seront utilisés pour authentifier l’utilisateur SSH. L'utilisation d'une clé publique est l'approche recommandée. Cliquez sur **Sélectionner** en bas de l’écran pour enregistrer la configuration des informations d’identification.

	![Fournir les informations d’identification du cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Fournir les informations d’identification du cluster")

	Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Cliquez sur **Source de données** pour choisir une source de données existante pour le cluster ou en créer une.

	![Panneau Source de données](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Fournir la configuration de la source de données")

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Lisez ce qui suit pour comprendre à quoi correspondent les entrées du panneau **Source de données**.

	- **Méthode de sélection** : définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d’accès** si vous souhaitez saisir le **Nom de stockage** et la **Clé d’accès** d’un compte de stockage existant.

	- **Sélectionner le compte de stockage / Nouveau** : cliquez sur **Sélectionner le compte de stockage** pour parcourir et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Nouveau** pour créer un nouveau compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.

	- **Choisir le conteneur par défaut** : utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- **Emplacement** : zone géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT] La sélection de l’emplacement de la source de données par défaut définira également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même zone géographique.
        
    - **Identité AAD de cluster**: en la configurant, vous rendez le cluster accessible aux magasins Azure Data Lake basés sur la configuration AAD.

	Cliquez sur **Sélectionner** pour enregistrer la configuration de source de données.

8. Cliquez sur **Niveaux tarifaires de nœud** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Définissez le nombre de nœuds worker dont vous avez besoin pour le cluster. Le coût estimé du cluster s’affiche dans le panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Spécifier le nombre de nœuds de cluster")
    
    > [AZURE.IMPORTANT] Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
    >
    > Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Cliquez sur **Sélectionner** pour enregistrer la configuration de tarification du nœud.

9. Cliquez sur **Configuration facultative** pour sélectionner la version de cluster et configurer d’autres paramètres facultatifs (connexion à un **réseau virtuel**, configuration d’un **metastore externe** pour conserver les données Hive et Oozie, etc.), utiliser des actions de script pour personnaliser un cluster et installer des composants personnalisés ou encore utiliser des comptes de stockage supplémentaires avec le cluster.

	* **Réseau virtuel** : sélectionnez un réseau virtuel Azure et le sous-réseau si vous souhaitez placer le cluster dans un réseau virtuel.

		![Panneau Réseau virtuel](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Spécifier les détails du réseau virtuel")

    	Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Étendre les capacités de HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

	* Cliquez sur **Metastores externes** pour spécifier la base de données SQL à utiliser pour enregistrer les métadonnées Hive et Oozie associées au cluster.
    
        > [AZURE.NOTE] La configuration Metastore n’est pas disponible pour les types de cluster HBase.

		![Panneau de metastores personnalisés](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Spécification des metastores externes")

		Pour **Utiliser une base de données SQL existante pour les métadonnées Hive**, cliquez sur **Oui**, sélectionnez une base de données SQL, puis indiquez le nom d’utilisateur/mot de passe pour la base de données. Répétez ces étapes si vous souhaitez **utiliser une base de données SQL existante pour les métadonnées Oozie**. Cliquez sur **Sélectionner** jusqu’à ce que vous reveniez au panneau **Configuration facultative**.

		>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

        &nbsp;

        > [AZURE.IMPORTANT] Lors de la création d’un metastore, n’utilisez pas de nom de base de données contenant des traits d’union ou des tirets, car cela risque d’entraîner l’échec du processus de création du cluster.

	* Cliquez sur **Actions de script** si vous voulez utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md). Dans le panneau Actions de script, saisissez les informations requises, comme illustré dans la capture d’écran ci-dessous.

		![Panneau Action de script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Spécifier l'action de script")

	* Cliquez sur **Comptes de stockage liés** pour spécifier les comptes de stockage supplémentaires à associer au cluster. Dans le panneau **CLÉS DE STOCKAGE AZURE**, cliquez sur **AJOUTER UNE CLÉ DE STOCKAGE**, puis sélectionnez un compte de stockage existant ou créez-en un.

		![Panneau Stockage supplémentaire](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Spécifier des comptes de stockage supplémentaires")

		Vous pouvez également ajouter des comptes de stockage supplémentaires après la création d’un cluster. Consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

		Cliquez sur **Sélectionner** jusqu’à ce que vous reveniez au panneau **Nouveau cluster HDInsight **.
        
        En plus du compte de stockage d'objets blob, vous pouvez également lier des magasins Azure Data Lake. La configuration peut être effectuée en configurant AAD à partir de la source de données dans laquelle vous avez configuré le compte de stockage par défaut et le conteneur par défaut.

10. Dans le panneau **Nouveau cluster HDInsight**, vérifiez que l’option **Épingler au tableau d’accueil** est sélectionnée, puis cliquez sur **Créer**. Le cluster est créé et la vignette correspondante ajoutée au tableau d’accueil de votre portail Azure. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.

	| Pendant l’approvisionnement | Approvisionnement terminé |
	| ------------------ | --------------------- |
	| ![Indicateur d’approvisionnement sur le tableau d'accueil](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Vignette de cluster approvisionné](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE] La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la mosaïque du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour vérifier le processus d’approvisionnement.

11. Une fois la création terminée, cliquez sur la vignette du cluster dans le tableau d’accueil pour lancer le volet du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.

	![Panneau Cluster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Propriétés du cluster")

	Utilisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans la section dans la section **Bases** :

	* **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.

	* **Tableau de bord**, **Tableau de bord du cluster** et **URL** : il s’agit de toutes les façons d’accéder au tableau de bord de cluster, qui est un portail Web permettant d’exécuter des tâches sur le cluster.

	* **Secure Shell** : informations nécessaires à l’accès au cluster à l’aide de SSH.

	* **Supprimer** : permet de supprimer le cluster HDInsight.

	* **Démarrage rapide** (![icône nuage et foudre = démarrage rapide](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)) : affiche des informations qui vous aideront à utiliser HDInsight.

	* **Utilisateurs** (![icône d’utilisateurs](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)) : permet de définir des autorisations pour la _gestion au moyen du portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.

		> [AZURE.IMPORTANT] Ce paramètre affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail Azure et n’a aucun effet sur les personnes autorisées à se connecter ou à soumettre des tâches au cluster HDInsight.

	* **Balises** (![icône de balise](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)) : permet de spécifier des paires clé/valeur pour définir une taxonomie personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.

##Personnalisation des clusters

- Consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

##Suppression du cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Étapes suivantes

Vous avez créé un cluster HDInsight. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

###Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###Clusters HBase

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux.md)

###Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Clusters Spark

* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : utilisez Spark dans HDInsight pour créer des applications de streaming en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0914_2016-->