<properties
	pageTitle="Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure | Microsoft Azure"
	description="Découvrez la façon d'administrer le service HDInsight. Créez un cluster HDInsight, ouvrez la console JavaScript interactive et ouvrez la console de commandes Hadoop."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/06/2015"
	ms.author="larryfr"/>

# Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure en version préliminaire

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]


À l’aide du [portail Azure en version préliminaire][preview-portal], vous pouvez approvisionner et gérer des clusters Hadoop basés sur Linux dans Azure HDInsight.

> [AZURE.NOTE]Les étapes de cette procédure sont spécifiques aux clusters Hadoop basés sur Linux. Pour plus d’informations sur l’utilisation des clusters basés sur Windows, voir [Gérer des clusters Hadoop dans HDInsight au moyen du portail Azure en version préliminaire](hdinsight-administer-use-management-portal.md)


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Autres outils d’administration de HDInsight
Outre le portail Azure, d’autres outils sont également disponibles pour administrer HDInsight.

- [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md) : l’interface de ligne de commande Azure est une commande interplateforme qui vous permet de gérer les services Azure.

- [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md) : Azure PowerShell propose des applets de commande PowerShell pour la gestion des services Azure.

## Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Voir [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## Approvisionnement de clusters HDInsight

Vous pouvez approvisionner des clusters HDInsight à partir du portail Azure en suivant les étapes ci-dessous :

1. Connectez-vous au [portail Azure en version préliminaire][preview-portal].

2. Sélectionnez **NOUVEAU**, __Analyse des données__, puis __HDInsight__

	![Création d’un cluster dans le portail Azure en version préliminaire](./media/hdinsight-administer-use-portal-linux/new-cluster.png)

3. Entrez une valeur dans le champ __Nom de cluster__, puis sélectionnez le __Type de cluster__ que vous souhaitez créer. Une coche verte s’affiche en regard du __Nom de cluster__ s’il est disponible.

	![Nom du cluster, type de cluster et type de système d’exploitation](./media/hdinsight-administer-use-portal-linux/clustername.png)

4. Si vous avez plusieurs abonnements, sélectionnez l’entrée __Abonnement__ pour sélectionner l’abonnement Azure qui sera utilisé pour le cluster.

5. Pour __Groupe de ressources__, sélectionnez l’entrée de manière à afficher la liste des groupes de ressources existants, puis sélectionnez celui dans lequel créer le cluster. Vous pouvez également sélectionner __Créer un nouveau__, puis saisir le nom du nouveau groupe de ressources. Une coche verte s’affiche pour indiquer si le nouveau nom de groupe est disponible.

	> [AZURE.NOTE]Cette entrée ira par défaut dans l’un des groupes de ressources existants, si l’un d’eux est disponible.

6. Sélectionnez __Informations d’identification__, puis saisissez une valeur dans le champ __Mot de passe de connexion au cluster__ et dans le champ __Nom d’utilisateur de connexion au cluster__. Vous devez également saisir un __nom d’utilisateur SSH__ et un __MOT DE PASSE__ ou __CLÉ PUBLIQUE__, qui seront utilisés pour authentifier l’utilisateur SSH. Enfin, utilisez le bouton __Sélectionner__ pour définir les informations d’identification. Le Bureau à distance ne sera pas utilisé dans ce document, vous pouvez donc laisser cette option désactivée.

	![Panneau Informations d’identification du cluster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)
    
    > [AZURE.NOTE]SSH permet d’accéder à distance au cluster HDInsight à l’aide d’une ligne de commande. Le nom d’utilisateur et le mot de passe ou la clé publique que vous employez ici sont utilisés lors de la connexion au cluster via SSH. Par ailleurs, le nom d’utilisateur SSH doit être unique, car il crée un compte d’utilisateur sur tous les nœuds du cluster HDInsight. Voici quelques-uns des noms de compte dont l’usage est réservé aux services sur le cluster et qui __ne doivent pas__ être utilisés en tant que nom d’utilisateur SSH :
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows)

7. Pour __Source de données__, vous pouvez choisir une source de données existante ou en créer une.

	![Panneau Source de données](./media/hdinsight-administer-use-portal-linux/datasource.png)

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Utilisez ce qui suit pour comprendre les saisies du panneau __Source de données__.

	- __Méthode de sélection__ : définissez cette propriété sur la valeur __De tous les abonnements__ pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur __Clé d’accès__ si vous souhaitez saisir le __nom de stockage__ et la __clé d’accès__ d’un compte de stockage existant.

	- __Créer un compte__ : utilisez cette option pour créer un autre compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.

	- __Choisir le conteneur par défaut__ : utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- __Emplacement__ : zone géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT]La sélection de l’emplacement de la source de données par défaut définira également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même zone géographique.

	- __Sélectionner__ : utilisez cette option pour enregistrer la configuration de la source de données.

8. Sélectionnez __Niveaux de tarification du nœud__ pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Par défaut, le nombre de nœuds de travail est fixé à __4__.

	Le coût estimé du cluster s’affiche au bas de ce panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-administer-use-portal-linux/nodepricingtiers.png)

	Utilisez le bouton __Sélectionner__ pour enregistrer les informations de __Niveaux de tarification de nœud__.

9. Sélectionnez __Configuration facultative__. Ce panneau vous permet de configurer les éléments suivants :

	* __HDInsight Version__ : version de HDInsight utilisée pour le cluster. Pour plus d’informations sur le contrôle de version HDInsight, consultez [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md)

	* __Metastores externes__ : cela vous permet de sélectionner une base de données SQL, qui sera utilisée pour stocker les informations de configuration pour Oozie et Hive. Cela vous permet de réutiliser la configuration lors de la suppression et la recréation d’un cluster, au lieu d’avoir à recréer la configuration Hive et Oozie chaque fois.

	* __Réseau virtuel__ : cela vous permet de placer le cluster HDInsight sur le même réseau virtuel que d’autres ressources, telles que la base de données SQL ou une Machine virtuelle Azure. Le fait de placer des ressources sur un réseau virtuel permet de communiquer directement d’une personne à l’autre, en ignorant les passerelles publiques qui gèrent le trafic entrant depuis Internet. Pour plus d’informations sur la façon dont HDInsight tire parti de réseaux virtuels Azure, consultez [Étendre les fonctionnalités HDInsight à l’aide d’un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

		> [AZURE.IMPORTANT]Vous devez créer le réseau virtuel Azure avant de créer le cluster HDInsight, car vous ne pouvez pas créer un nouveau réseau à partir d’une configuration HDInsight.
		>
		> Actuellement (25/08/2015) il existe une limite disant qu’un seul cluster HDInsight basé sur Linux peut être présent sur un réseau virtuel Azure.

	* __Actions de script__ : permet de spécifier des scripts Bash qui personnalisent le cluster HDInsight lors de la configuration. Par exemple, il existe un [script qui installe Hue](hdinsight-hadoop-hue-linux.md) (client graphique pour travailler avec Hadoop.) Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).

	* __Clés de stockage azure__ : elle vous permet d’associer des comptes supplémentaires de stockage au serveur HDInsight.

		> [AZURE.NOTE]HDInsight peut accéder uniquement aux comptes de stockage Azure utilisés comme magasin de données par défaut, ajoutés à cette section de configuration, ou publiquement accessibles.

	![Panneau Configuration facultative](./media/hdinsight-administer-use-portal-linux/optionalconfiguration.png)

10. Vérifiez que l’option __Épingler au tableau d’accueil__ est sélectionnée, puis sélectionnez __Créer__. Le cluster est créé et la vignette correspondante ajoutée au tableau d’accueil de votre portail Azure. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.

	| Pendant l’approvisionnement | Approvisionnement terminé |
	| ------------------ | --------------------- |
	| ![Indicateur d’approvisionnement sur le tableau d’accueil](./media/hdinsight-administer-use-portal-linux/provisioning.png) | ![Vignette de cluster approvisionné](./media/hdinsight-administer-use-portal-linux/provisioned.png) |

	> [AZURE.NOTE]La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du tableau d’accueil, ou l’entrée __Notifications__ à gauche de la page pour vérifier le processus de configuration.

## Gérer un cluster

Sélectionnez un cluster à partir du portail Azure en version préliminaire pour afficher des informations essentielles sur le cluster, tel que le nom, le groupe de ressources, le système d’exploitation et l’URL du tableau de bord de cluster (utilisée pour accéder à l’interface Ambari Web pour les clusters Linux).

![Détails du cluster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Lisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans les sections __Bases__ et __Liens rapides__ :

* __Paramètres__ et __Tous les paramètres__ : affichent le panneau __Paramètres__ du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.

* __Tableau de bord__, __Tableau de bord du cluster__ et __URL__ : il s’agit de toutes les façons d’accéder au tableau de bord de cluster, qui est une interface Ambari Web pour les clusters basés sur Linux.

* __Secure Shell__ : informations nécessaires à l’accès au cluster à l’aide de SSH.

* __Mise à l’échelle du cluster__ : permet de modifier le nombre de nœuds de travail pour ce cluster.

* __Supprimer__ : permet de supprimer le cluster HDInsight.

* __Démarrage rapide (![icône nuage et foudre = démarrage rapide](./media/hdinsight-administer-use-portal-linux/quickstart.png)) __ : affiche des informations qui vous aideront à prendre en main HDInsight.

* __Utilisateurs (![icône d’utilisateurs](./media/hdinsight-administer-use-portal-linux/users.png))__ : permet de définir des autorisations pour la _gestion via le portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.

	> [AZURE.IMPORTANT]Ce paramètre affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail Azure en version préliminaire et n’a aucun effet sur les personnes autorisées à se connecter ou à soumettre des tâches au cluster HDInsight.

* __Balises (![icône de balise](./media/hdinsight-administer-use-portal-linux/tags.png))__ : les balises vous permettent de définir des paires clé/valeur afin de déterminer une taxonomie personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.

* __Documentation__ : liens vers la documentation correspondant à Azure HDInsight.

> [AZURE.IMPORTANT]Pour gérer les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation d’Ambari, voir [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md)

### <a name="scaling"> </a>Mise à l’échelle

Pour mettre à l’échelle un cluster à l’aide du portail, sélectionnez votre cluster HDInsight, puis sélectionnez __Mettre le cluster à l’échelle__. Entrez le __nombre de nœuds de travail__ que vous souhaitez définir pour le cluster, puis cliquez sur __Enregistrer__.

![image de mise à l’échelle de l’interface utilisateur](./media/hdinsight-administer-use-portal-linux/scaling.png)

Pour plus d’informations sur les opérations de mise à l’échelle, consultez [Autre informations sur l’utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md#scaling).

## Surveiller un cluster

La section __Utilisation__ du panneau du cluster HDInsight affiche des informations sur le nombre de mémoires à tore disponibles pour votre abonnement à utiliser avec HDInsight, ainsi que le nombre de mémoires à tore magnétique affectées à ce cluster et la façon dont elles sont attribuées aux nœuds de ce cluster.

![Informations sur l’utilisation](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT]Pour surveiller les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation de l’interface Ambari, voir [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md)

## Étapes suivantes
Cet article vous a appris à créer un cluster HDInsight à l’aide du portail Azure et à ouvrir l’outil en ligne de commande Hadoop. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
* [Approvisionnement des clusters HDInsight](hdinsight-provision-clusters.md)
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Prise en main d’Azure HDInsight](../hdinsight-get-started.md)
* [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=Sept15_HO3-->