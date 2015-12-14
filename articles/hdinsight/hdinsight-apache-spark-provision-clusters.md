<properties
   pageTitle="Approvisionner des clusters Apache Spark dans HDInsight | Microsoft Azure"
   description="Découvrez comment configurer des clusters Spark pour Azure HDInsight à l’aide du portail Azure Classic, d’Azure PowerShell, d’une ligne de commande ou du Kit de développement logiciel (SDK) .NET HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="11/07/2015"
    ms.author="nitinme"/>

# Approvisionner des clusters Apache Spark dans HDInsight à l’aide d’options personnalisées

Dans la plupart des scénarios, vous pouvez approvisionner un cluster Spark à l’aide de la méthode de création rapide décrite dans la rubrique [Prendre en main Apache Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Dans certains scénarios, vous souhaiterez peut-être approvisionner un cluster personnalisé. Par exemple, vous pouvez souhaiter attacher un magasin de métadonnées externe pour que vos métadonnées Hive demeurent persistantes au-delà de la durée de vie d’un cluster, ou utiliser un espace de stockage supplémentaire avec le cluster.

Pour ces scénarios, entre autres, cet article fournit des instructions sur l’utilisation du portail Azure Classic, d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight pour approvisionner un cluster Spark personnalisé sur HDInsight.


**Configuration requise :**

Avant de suivre les instructions de cet article, vous devez disposer d’un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quelles sont les différentes options de configuration ?

###Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d’objet blob Azure, ainsi qu’un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier un compte de stockage Azure supplémentaire, également associé à votre cluster.

>[AZURE.NOTE]Ne partagez pas un conteneur de stockage d’objets blob sur plusieurs clusters. Ce n’est pas pris en charge.

Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

###Metastore

Spark permet de définir un schéma et des tables Hive sur des données brutes. Vous pouvez enregistrer ces schémas et ces métadonnées de table dans des metastores externes. L’utilisation de metastores vous permet de conserver vos métadonnées Hive, afin de ne pas devoir recréer de tables Hive lorsque vous approvisionnez un nouveau cluster. Hive utilise par défaut une base de données intégrée pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé.

Pour obtenir des instructions sur la création d’une base de données SQL, consultez [Créer votre première base de données SQL Azure](sql-database-get-started.md).

### Personnalisation des clusters

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la configuration. Ces scripts sont appelés à l’aide de l’option **ACTION DE SCRIPT**, une option de configuration qui peut être utilisée à partir du portail Azure Classic, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script][hdinsight-customize-cluster].


###Réseau virtuel

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Vous devez créer le réseau Azure Virtual Network avant d’approvisionner un cluster. Pour plus d’informations, consultez [Création d’un réseau virtuel](virtual-networks-create-vnet.md).
>
> Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

##Utiliser le portail Azure

Les clusters Spark sur HDInsight utilisent un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire avant de pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la rubrique [Création d’un compte de stockage][azure-create-storageaccount].

**Pour créer un cluster HDInsight en utilisant l’option Création personnalisée**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, sur **ANALYSE DES DONNÉES**, puis sur **HDINSIGHT**.

    ![Créer un cluster dans le portail Azure](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.1.png "Créer un cluster dans le portail Azure")

3. Saisissez un **NOM DE CLUSTER**, sélectionnez **Spark** comme **TYPE DE CLUSTER**, et à partir du menu déroulant **SYSTÈME D'EXPLOITATION DU CLUSTER**, sélectionnez **Windows Server 2012 R2 Datacenter**. Une coche verte apparaît en regard du nom de cluster s'il est disponible.

	![Saisir le nom et le type du cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.2.png "Saisir le nom et le type du cluster")

4. Si vous avez plusieurs abonnements, cliquez sur l’entrée **ABONNEMENT** pour sélectionner l’abonnement Azure qui sera utilisé pour le cluster.

5. Cliquez sur **GROUPE DE RESSOURCES** pour afficher une liste des groupes de ressources existants, puis sélectionnez celui dans lequel créer le cluster. Vous pouvez également cliquer sur **CRÉER UN NOUVEAU**, puis saisir le nom du nouveau groupe de ressources. Une coche verte s’affiche pour indiquer si le nouveau nom de groupe est disponible.

	> [AZURE.NOTE]Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.

6. Cliquez sur **INFORMATIONS D’IDENTIFICATION**, puis saisissez une valeur dans les champs **NOM DE CONNEXION AU CLUSTER** et **MOT DE PASSE DE CONNEXION AU CLUSTER**. Si vous souhaitez activer le Bureau à distance sur le nœud de cluster, cliquez sur l’option **OUI** pour **ACTIVER LE BUREAU À DISTANCE**. Sélectionnez une date d’expiration pour l’accès au cluster via le Bureau à distance et fournissez le nom d’utilisateur/mot de passe de l’utilisateur du Bureau à distance. Cliquez sur **SÉLECTIONNER** au bas de l’écran pour enregistrer la configuration des informations d’identification.

	![Fournir les informations d’identification du cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.3.png "Fournir les informations d’identification du cluster")

7. Cliquez sur **SOURCE DE DONNÉES** pour choisir une source de données existante pour le cluster ou en créer une.

	![Panneau Source de données](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.4.png "Fournir la configuration de la source de données")

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Lisez ce qui suit pour comprendre les entrées du panneau **SOURCE DE DONNÉES**.

	- **MÉTHODE DE SÉLECTION** : définissez cette propriété sur la valeur **DE TOUS LES ABONNEMENTS** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **CLÉ D'ACCÈS** si vous souhaitez saisir le **NOM DE STOCKAGE** et la **CLÉ D'ACCÈS** d'un compte de stockage existant.

	- **SÉLECTIONNER LE COMPTE DE STOCKAGE / CRÉER UN COMPTE** : cliquez sur **SÉLECTIONNER LE COMPTE DE STOCKAGE** pour parcourir et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **CRÉER UN COMPTE** pour créer un nouveau compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.

	- **CHOISIR LE CONTENEUR PAR DÉFAUT** : utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- **EMPLACEMENT** : zone géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT]La sélection de l’emplacement de la source de données par défaut définira également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même région.

	Cliquez sur **SÉLECTIONNER** pour enregistrer la configuration de la source de données.

8. Cliquez sur **NIVEAUX TARIFAIRES DE NŒUD** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Définissez le nombre de nœuds worker dont vous avez besoin pour le cluster. Le coût estimé du cluster s’affiche dans le panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.5.png "Spécifier le nombre de nœuds de cluster")

	Cliquez sur **SÉLECTIONNER** pour enregistrer la configuration de la tarification du nœud.

9. Cliquez sur **CONFIGURATION FACULTATIVE** pour sélectionner la version de cluster et configurer d’autres paramètres facultatifs (connexion à un **réseau virtuel**, configuration d’un **metastore externe** pour conserver les données Hive et Oozie, etc.), utiliser des actions de script pour personnaliser un cluster et installer des composants personnalisés ou encore utiliser des comptes de stockage supplémentaires avec le cluster.

	* Cliquez sur le menu déroulant **VERSION HDINSIGHT** et sélectionnez la version à utiliser pour le cluster. Pour plus d’informations, consultez la rubrique [Versions de clusters HDInsight](hdinsight-component-versioning.md).

	* Cliquez sur **RÉSEAU VIRTUEL** pour fournir les informations permettant de configurer le cluster en tant que composant d’un réseau virtuel. Dans le panneau **RÉSEAU VIRTUEL**, cliquez sur **RÉSEAU VIRTUEL**, puis sur le réseau à utiliser. En procédant de même, sélectionnez un **sous-réseau** pour le réseau, puis cliquez sur **SÉLECTIONNER** pour enregistrer la configuration du réseau virtuel.

		![Panneau Réseau virtuel](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.6.png "Spécifier les détails du réseau virtuel")

	* Cliquez sur **METASTORES EXTERNES** pour spécifier la base de données SQL à utiliser pour enregistrer les métadonnées Hive et Oozie associées au cluster.

		![Panneau de metastores personnalisés](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.7.png "Spécification des metastores externes")

		Pour les métadonnées **utiliser une base de données SQL existante pour Hive**, cliquez sur **OUI**, sélectionnez une base de données SQL, puis indiquez le nom d'utilisateur/mot de passe pour la base de données. Répétez ces étapes si vous souhaitez **utiliser une base de données SQL existante pour les métadonnées Oozie**. Cliquez sur **SÉLECTIONNER** jusqu’à ce que vous reveniez au panneau **CONFIGURATION FACULTATIVE**.

		>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

	* Cliquez sur **ACTIONS DE SCRIPT** si vous souhaitez utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md). Dans le panneau Actions de script, saisissez les informations requises, comme illustré dans la capture d’écran ci-dessous.

		![Panneau Action de script](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.8.png "Spécifier l'action de script")

		Cliquez sur **SÉLECTIONNER** pour enregistrer les modifications apportées à la configuration d’action de script.

	* Cliquez sur **CLÉS DE STOCKAGE AZURE** pour spécifier les comptes de stockage supplémentaires à associer au cluster. Dans le panneau **CLÉS DE STOCKAGE AZURE**, cliquez sur **AJOUTER UNE CLÉ DE STOCKAGE**, puis sélectionnez un compte de stockage existant ou créez-en un.

		![Panneau Stockage supplémentaire](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.9.png "Spécifier des comptes de stockage supplémentaires")

		Cliquez sur **SÉLECTIONNER** jusqu’à ce que vous reveniez au panneau **Nouveau cluster HDInsight **.

10. Dans le panneau **NOUVEAU CLUSTER HDINSIGHT**, assurez-vous que l’option **ÉPINGLER AU TABLEAU D’ACCUEIL** est sélectionnée, puis cliquez sur **CRÉER**. Le cluster est créé et la mosaïque correspondante ajoutée au tableau d’accueil de votre portail Azure Classic. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.

	| Pendant l’approvisionnement | Approvisionnement terminé |
	| ------------------ | --------------------- |
	| ![Indicateur d’approvisionnement sur le tableau d’accueil](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Vignette de cluster approvisionné](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la mosaïque du tableau d’accueil ou l’entrée **NOTIFICATIONS** à gauche de la page pour vérifier le processus d’approvisionnement.

11. Une fois l’approvisionnement terminé, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.

	![Panneau Cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.Cluster.Blade.png "Propriétés du cluster")

	Lisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans les sections **BASES** et **LIENS RAPIDES** :

	* **PARAMÈTRES** et **TOUS LES PARAMÈTRES** : affichent le panneau **PARAMÈTRES** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.

	* **TABLEAU DE BORD** et **URL** : permettent d’accéder au tableau de bord du cluster, portail web permettant d’exécuter des tâches sur le cluster.

	* **BUREAU À DISTANCE** : permet d’activer/de désactiver le Bureau à distance sur les nœuds de cluster.

	* **METTRE À L’ÉCHELLE LE CLUSTER** : permet de modifier le nombre de nœuds de travail pour ce cluster.

	* **SUPPRIMER** : permet de supprimer le cluster HDInsight.

	* **DÉMARRAGE RAPIDE** (![icône nuage et foudre = démarrage rapide](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)) : affiche des informations qui vous aideront à prendre en main HDInsight.

	* **UTILISATEURS (![icône d’utilisateurs](./media/hdinsight-apache-spark-provision-clusters/users.png))** : permet de définir des autorisations pour la _gestion via le portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.

		> [AZURE.IMPORTANT]Ce paramètre affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail Azure et n’a aucun effet sur les personnes autorisées à se connecter ou à soumettre des tâches au cluster HDInsight.

	* **BALISES (![icône de balise](./media/hdinsight-apache-spark-provision-clusters/tags.png))** : permettent de spécifier des paires clé/valeur pour définir une classification personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.

	* **TABLEAU DE BORD DU CLUSTER** : permet d’ouvrir le panneau Tableau de bord du cluster, d’où vous pouvez lancer le tableau de bord du cluster lui-même ou les blocs-notes Zeppelin et Jupyter.


## Utilisation d'Azure PowerShell

Consultez [Créer des clusters HDInsight](hdinsight-provision-clusters.md#create-using-azure-powershell).

Spécifiez le commutateur de type de cluster Spark dans l'applet de commande New-AzureRmHDInsightCluster :

	-ClusterType Spark

## Utiliser le Kit de développement logiciel (SDK) .NET HDInsight basé sur ARM

Consultez [Créer des clusters HDInsight](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk).

Spécifiez un type de cluster Spark :

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


## Étapes suivantes

* Consultez [Effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](hdinsight-apache-spark-use-bi-tools.md) pour découvrir comment utiliser Apache Spark dans HDInsight avec des outils décisionnels tels que Power BI et Tableau.
* Consultez [Utiliser Spark sur HDInsight pour créer des applications d’apprentissage automatique](hdinsight-apache-spark-ipython-notebook-machine-learning.md) pour découvrir comment créer des applications d’apprentissage automatique à l’aide d’Apache Spark sur HDInsight.
* Consultez [Utiliser Spark sur HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) pour découvrir comment créer des applications de diffusion en continu à l’aide d’Apache Spark sur HDInsight.
* Consultez [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md) pour découvrir comment utiliser le gestionnaire de ressources pour gérer les ressources allouées au cluster Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Utilisation de Sqoop avec HDInsight"

<!---HONumber=AcomDC_1203_2015-->