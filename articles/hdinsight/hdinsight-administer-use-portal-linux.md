<properties
	pageTitle="Gestion des clusters Hadoop basés sur Linux dans HDInsight avec le portail Azure | Microsoft Azure"
	description="Apprenez à créer et à gérer des clusters HDInsight basés sur Linux avec le portail Azure."
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
	ms.date="03/30/2016"
	ms.author="larryfr"/>

#Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]


À l’aide du [portail Azure][preview-portal], vous pouvez approvisionner et gérer des clusters Hadoop basés sur Linux dans Azure HDInsight.

> [AZURE.NOTE] Les étapes de cette procédure sont spécifiques aux clusters Hadoop basés sur Linux. Pour plus d’informations sur l’utilisation des clusters basés sur Windows, voir [Gérer des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md)


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Autres outils d’administration de HDInsight
Outre le portail Azure, d’autres outils sont également disponibles pour administrer HDInsight.

- [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md) : l’interface de ligne de commande Azure est une commande interplateforme qui vous permet de gérer les services Azure.

- [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md) : Azure PowerShell propose des applets de commande PowerShell pour la gestion des services Azure.

## Composants requis

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Voir [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## Création de clusters HDInsight

Vous pouvez créer des clusters HDInsight à partir du portail Azure. Cette procédure est expliquée en détail dans le document [Créer des clusters Linux dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## Gérer un cluster

Sélectionnez un cluster à partir du portail Azure pour afficher des informations essentielles sur le cluster, tel que le nom, le groupe de ressources, le système d’exploitation et l’URL du cluster (utilisée pour accéder à l’interface Ambari Web pour les clusters basés sur Linux).

![Détails du cluster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Lisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans les sections __Bases__ et __Liens rapides__ :

* __Paramètres__ et __Tous les paramètres__ : affichent le panneau __Paramètres__ du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.

* __Tableau de bord__, __Tableau de bord du cluster__ et __URL__ : il s’agit de toutes les façons d’accéder au tableau de bord de cluster. Selon le type de cluster, une liste des tableaux de bord sur le cluster peut apparaître. Par exemple, le type de cluster Spark affichera une liste des tableaux de bord lorsque vous sélectionnez l'icône __Tableau de bord__, tandis qu'un cluster Hadoop ouvrira l'interface utilisateur Web d'Ambari.

* __Secure Shell__ : informations nécessaires à l’accès au nœud principal de cluster à l’aide de SSH.

* __Mise à l’échelle du cluster__ : permet de modifier le nombre de nœuds de travail pour ce cluster.

* __Supprimer__ : permet de supprimer le cluster HDInsight.

* __Démarrage rapide__ (![icône nuage et foudre = démarrage rapide](./media/hdinsight-administer-use-portal-linux/quickstart.png)) : affiche des informations qui vous aideront à prendre en main HDInsight.

* __Utilisateurs(![icône d’utilisateurs](./media/hdinsight-administer-use-portal-linux/users.png))__ : permet de définir des autorisations pour la _gestion via le portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.

	> [AZURE.IMPORTANT] Ce paramètre affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail Azure et n’a aucun effet sur les personnes autorisées à se connecter ou à soumettre des tâches au cluster HDInsight.

* __Balises(![icône de balise](./media/hdinsight-administer-use-portal-linux/tags.png))__ : permettent de spécifier des paires clé/valeur pour définir une classification personnalisée de vos Cloud Services. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.

* __Documentation__ : liens vers la documentation correspondant à Azure HDInsight.

> [AZURE.IMPORTANT] Pour gérer les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d’informations sur l’utilisation d’Ambari, consultez [Gestion des clusters HDInsight à l’aide d’Ambari](hdinsight-hadoop-manage-ambari.md).

### Paramètres

Si vous sélectionnez l'icône __Paramètres__ ou le lien __Tous les paramètres__, le panneau Paramètres apparaît. Plusieurs fonctions disponibles dans la section __Essentials__ et abordées précédemment sont également disponibles ici, notamment les informations Scaling ou Secure Shell. Vous pouvez également accéder aux options suivantes à partir du menu Paramètres :

* __Journaux d'audit__ : informations de journalisation utiles pour diagnostiquer les problèmes liés à l'intégrité des clusters.

* __Connexion au cluster__ : affiche le __nom de connexion au cluster__ et l'__adresse distante__ qui permettent d'accéder au cluster à l'aide de HTTPS.

* __Metastores externes__ : affiche des informations sur les metastores utilisés par votre cluster, le cas échéant. Si vous n'avez pas configuré un metastore personnalisé lors de la configuration du cluster, aucune information ne s'affichera.

* __Actions de script__ : affiche des informations sur les actions de Script qui ont été exécutées sur ce cluster. Vous pouvez également exécuter de nouvelles actions de script et conserver ou supprimer des actions de script exécutées précédemment. Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).

* __Apps__ : affiche des informations sur les applications qui ont été installées sur le cluster, et vous permet d'ajouter de nouvelles applications au cluster à partir d'Azure Marketplace.

* __Clés de stockage Azure__: affiche des informations sur les comptes de stockage Azure utilisés par le cluster. La sélection d'un compte de stockage chargera le panneau Compte de stockage du compte sélectionné.

* __Identité AAD de cluster__ : affiche des informations sur le principal du service pour ce cluster HDInsight. Le principal du service permet d'accéder à Azure Data Lake Store. Si vous n'avez pas associé votre cluster à Azure Data Lake Store lors de la création du cluster, les entrées de ce panneau afficheront __Non configuré__. Pour plus d’informations sur l’utilisation d'Azure Data Lake Store avec HDInsight, voir [Créer un cluster HDInsight avec Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="scaling"> </a>Mise à l’échelle

Pour mettre à l’échelle un cluster à l’aide du portail, sélectionnez votre cluster HDInsight, puis sélectionnez __Mettre le cluster à l’échelle__. Entrez le __nombre de nœuds de travail__ que vous souhaitez définir pour le cluster, puis cliquez sur __Enregistrer__.

![image de mise à l’échelle de l’interface utilisateur](./media/hdinsight-administer-use-portal-linux/scaling.png)

Pour plus d’informations sur les opérations de mise à l’échelle, consultez [Autres informations sur l’utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md#scaling).

## Surveiller un cluster

La section __Utilisation__ du panneau du cluster HDInsight affiche des informations sur le nombre de mémoires à tore disponibles pour votre abonnement à utiliser avec HDInsight, ainsi que le nombre de mémoires à tore magnétique affectées à ce cluster et la façon dont elles sont attribuées aux nœuds de ce cluster.

![Informations sur l’utilisation](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] Pour surveiller les services fournis par le cluster HDInsight, vous devez utiliser l’interface Ambari Web ou l’API Ambari REST. Pour plus d'informations sur l'utilisation de l'interface Ambari, voir [Gestion des clusters HDInsight à l'aide d'Ambari](hdinsight-hadoop-manage-ambari.md)

## Étapes suivantes
Cet article vous a appris à créer un cluster HDInsight à l’aide du portail Azure et à ouvrir l’outil en ligne de commande Hadoop. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
* [Approvisionnement des clusters HDInsight](hdinsight-provision-clusters.md)
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Prise en main d’Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->