<properties
	pageTitle="Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure en version préliminaire | Microsoft Azure"
	description="Découvrez la façon d'administrer le service HDInsight. Créez un cluster HDInsight, ouvrez la console JavaScript interactive et ouvrez la console de commandes Hadoop."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure en version préliminaire

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]

Le [portail Azure en version préliminaire][azure-portal] vous permet d’approvisionner des clusters Hadoop dans Azure HDInsight, de modifier le mot de passe utilisateur Hadoop et d’activer le protocole RDP (Remote Desktop Protocol) afin d’accéder à la console de commandes Hadoop sur le cluster.

[AZURE.INCLUDE [portail azure hdinsight en version préliminaire](../../includes/hdinsight-azure-preview-portal.md)]

* [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal-v1.md)

> [AZURE.NOTE]Les étapes de cette procédure sont spécifiques aux clusters Hadoop basés sur Windows. Pour plus d’informations sur l’utilisation des clusters basés sur Linux, voir [Gérer des clusters Hadoop dans HDInsight au moyen du portail Azure en version préliminaire](hdinsight-administer-use-portal-linux.md)



> [AZURE.NOTE]Outre le portail Azure en version préliminaire, d’autres outils sont également disponibles pour administrer HDInsight.
>
> - [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
> - [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)

**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Compte Azure Storage** - Le cluster HDInsight utilise un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Pour plus d’informations sur l’expérience transparente offerte par le stockage d’objets blob Azure avec les clusters HDInsight, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la page [Création d’un compte de stockage](../storage-create-storage-account.md).


##Approvisionnement de clusters HDInsight

Pour les instructions d’approvisionnement à l’aide du portail en version préliminaire, voir [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md#portal).

##Personnaliser l’approvisionnement de clusters HDInsight

HDInsight fonctionne avec un large éventail de composants Hadoop. Pour obtenir la liste des composants vérifiés et pris en charge, consultez la rubrique [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md). Vous pouvez personnaliser HDInsight en utilisant l’une des options suivantes :

- Utilisez Action de script pour exécuter des scripts personnalisés pouvant personnaliser un cluster afin de modifier la configuration de cluster ou d’installer des composants personnalisés tels que Giraph ou Solr. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md).
- Utilisez les paramètres de personnalisation de cluster du Kit de développement logiciel (SDK) HDInsight .NET ou Azure PowerShell pendant l’approvisionnement du cluster. Les modifications apportées à la configuration sont ainsi conservées pendant toute la durée de vie du cluster et ne sont pas affectées par les réinitialisations des nœuds du cluster qu’exécute régulièrement la plateforme Azure à des fins de maintenance. Pour plus d'informations sur l'utilisation des paramètres de personnalisation des clusters, consultez la rubrique [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md).
- Certains composants Java natifs, comme Mahout et Cascading, peuvent être exécutés sur le cluster en tant que fichiers JAR. Ces derniers peuvent être distribués au stockage d’objets blob Azure et envoyés aux clusters HDInsight à l’aide des mécanismes d’envoi de tâches Hadoop. Pour plus d’informations, consultez la rubrique [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md).


	>[AZURE.NOTE]En cas de problèmes lors du déploiement ou de l’appel des fichiers JAR sur les clusters HDInsight, contactez le [support Microsoft](http://azure.microsoft.com/support/options/).

	> Cascading n'est pas pris en charge par HDInsight et ne peut pas bénéficier du support Microsoft. Pour obtenir la liste des composants pris en charge, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight.](hdinsight-component-versioning.md).


L’installation de logiciels personnalisés sur le cluster à l’aide d’une connexion Bureau à distance n’est pas prise en charge. Évitez de stocker des fichiers sur les disques du nœud principal, car vous les perdrez si vous devez recréer les clusters. Il est recommandé de stocker les fichiers sur le stockage d’objets blob Azure. Le stockage d'objets blob est permanent.

## Se familiariser avec l’interface du portail cluster

**Pour accéder au cluster**

1. Connectez-vous au [portail en version préliminaire][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche pour ouvrir le panneau **Parcourir**.
3. Cliquez sur **Clusters HDInsight** pour ouvrir le panneau **Clusters HDInsight**.
4. Cliquez sur l’un des clusters de la liste pour ouvrir le panneau du cluster :

	![Portail HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.blade.png)

5. Cliquez sur **Paramètres** pour afficher les détails de configuration et configurer le cluster :

	![Paramètres de cluster du portail HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	|Paramètre|Description|
	|-----|-----|
	|Propriétés| Affichez les propriétés du cluster|
	|Clés de stockage Azure| Affichez les informations de compte de stockage Azure par défaut. |
	|Connexion au cluster| Octroyez/révoquez l’accès aux services web HTTP et configurez les informations de connexion au cluster. |
	|Metastores externes| Affichez les informations de metastore Hive/Oozie.|
	|Cluster de mise à l’échelle| Augmentez/diminuez le nombre de nœuds de travail du cluster.|
	|Bureau à distance| Activez/désactivez la connectivité Bureau à distance, connectez-vous au cluster via le Bureau à distance.|


##Octroi/révocation de l'accès aux services HTTP

Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

Par défaut, l'accès à ces services est octroyé. Vous pouvez révoquer/octroyer l’accès à partir du portail Azure.

>[AZURE.NOTE]En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.

**Pour octroyer/révoquer l’accès aux services web HTTP**

1. Connectez-vous au [portail en version préliminaire][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis cliquez sur **Connexion au cluster**.
5. Cliquez sur **ACTIVÉ** ou **DÉSACTIVÉ** dans **Accès de la connexion au cluster**.  
6. Pour **Nom d’utilisateur de connexion au cluster** et **Mot de passe de connexion au cluster**, saisissez le nouveau nom d’utilisateur et le nouveau mot de passe (respectivement) pour le cluster.
7. Cliquez sur **ENREGISTRER**.

	![hdinsight octroi suppression accès au service web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)


Vous pouvez également utiliser les applets de commande Azure PowerShell :

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consultez la rubrique [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md).

##Modification du nom d'utilisateur et du mot de passe d'un cluster HDInsight
Un cluster HDInsight peut disposer de deux comptes d'utilisateur. Le compte d’utilisateur du cluster HDInsight est créé lors du processus d’approvisionnement. Vous pouvez également créer un compte d’utilisateur RDP pour accéder au cluster via RDP. Consultez la rubrique [Activation du Bureau à distance](#connect-to-hdinsight-clusters-by-using-rdp).

**Modification du nom d’utilisateur et du mot de passe d’un cluster HDInsight**

1. Connectez-vous au [portail en version préliminaire][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis cliquez sur **Connexion au cluster**.
4. Modifiez le **Nom d’utilisateur de connexion au cluster** et/ou le **Mot de passe de connexion au cluster**, puis cliquez sur **Enregistrer**.

	![hdinsight modifier cluster utilisateur nom d’utilisateur mot de passe utilisateur http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

>[AZURE.NOTE]Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés. Voir [Se familiariser avec l’interface du portail cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

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

	![hdinsight storm mise à l’échelle rééquilibrage](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Pour mettre à l’échelle des clusters**

1. Connectez-vous au [portail en version préliminaire][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis cliquez sur **Cluster de mise à l’échelle**.
4. Entrez une valeur dans le champ **Nombre nœuds de travail**. La limite du nombre de nœuds de cluster varie selon les abonnements Azure. Vous pouvez contacter le support de facturation pour augmenter la limite. Les informations de coût reflètent les modifications apportées au nombre de nœuds.


	![hdinsight hadoop hbase storm spark mise à l’échelle](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)



##Connexion à des clusters HDInsight à l’aide de RDP

Les informations d’identification du cluster fournies lors de sa création donnent accès aux services disponibles sur le cluster, mais pas au cluster proprement dit via le Bureau à distance. Vous pouvez activer l’accès Bureau à distance lorsque vous approvisionnez un cluster ou une fois l’approvisionnement effectué. Pour obtenir des instructions sur l’activation du Bureau à distance lors de l’approvisionnement, voir [Approvisionnement d’un cluster HDInsight](hdinsight-provision-clusters.md).

**Activation du Bureau à distance**

1. Connectez-vous au [portail en version préliminaire][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis cliquez sur **Bureau à distance**.
4. Entrez une valeur dans les champs **Expiration**, **Nom d’utilisateur de bureau à distance** et **Mot de passe de bureau à distance**, puis cliquez sur **Activer**.

	![hdinsight activer désactiver configurer Bureau à distance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	La valeur par défaut pour le champ Expiration est d’une semaine.
> [AZURE.NOTE]Vous pouvez également utiliser le Kit de développement logiciel (SDK) .NET HDInsight pour activer le Bureau à distance sur un cluster. Utilisez la méthode **ActiverRdp** sur l’objet client HDInsight de la manière suivante : **client.EnableRdp(nom cluster, emplacement, "utilisateurrdp", "motdepasserdp", DateHeure.Now.AddDays(6))**. De la même manière, pour désactiver le Bureau à distance sur le cluster, vous pouvez utiliser **client.DisableRdp(nom cluster, emplacement)**. Pour plus d’informations sur ces méthodes, consultez la rubrique [Référence du Kit de développement logiciel (SDK) HDInsight .NET](http://go.microsoft.com/fwlink/?LinkId=529017). Cela s’applique uniquement aux clusters HDInsight fonctionnant sous Windows.

**Pour vous connecter à un cluster à l’aide de RDP**

1. Connectez-vous au [portail en version préliminaire][azure-portal].
2. Cliquez sur **Parcourir tout** dans le menu de gauche, sur **Clusters HDInsight**, puis sur votre nom de cluster.
3. Cliquez sur **Paramètres** dans le menu supérieur, puis cliquez sur **Bureau à distance**.
4. Cliquez sur **Connexion**, puis suivez les instructions. Si la connexion est désactivée, vous devez d’abord l’activer. Assurez-vous de bien utiliser le nom d’utilisateur et le mot de passe du Bureau à distance. Vous ne pouvez pas utiliser les informations d’identification utilisateur du cluster.

##Ouvrez une ligne de commande Hadoop.

Pour vous connecter au cluster à l’aide du Bureau à distance et utiliser la ligne de commande Hadoop, vous devez d’abord activer l’accès Bureau à distance sur le cluster, comme décrit à la section précédente.

**Pour ouvrir une ligne de commande Hadoop**

1. Connectez-vous au cluster en utilisant le Bureau à distance.
8. À partir du Bureau, double-cliquez sur **Hadoop Command Line**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Pour plus d’informations sur les commandes Hadoop, consultez la rubrique [Référence aux commandes Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

Sur la capture d’écran précédente, le numéro de la version d’Hadoop est intégré au nom du dossier. Il peut être modifié en fonction de la version des composants Hadoop installés sur le cluster. Vous pouvez utiliser des variables d'environnement Hadoop pour faire référence à ces dossiers. Par exemple :

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%


##Étapes suivantes
Cet article vous a appris à créer un cluster HDInsight à l’aide du portail Azure en version préliminaire et à ouvrir l’outil en ligne de commande Hadoop. Pour en savoir plus, consultez les articles suivants :

* [Administration de HDInsight à l’aide d’Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Administration de HDInsight à l’aide de l’interface de ligne de commande Azure](hdinsight-administer-use-command-line.md)
* [Approvisionnement des clusters HDInsight](hdinsight-provision-clusters.md)
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Prise en main d’Azure HDInsight](../hdinsight-get-started.md)
* [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Page d’accueil du cluster"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Ligne de commande Hadoop"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!---HONumber=August15_HO8-->