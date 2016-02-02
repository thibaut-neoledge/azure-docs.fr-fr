<properties
   pageTitle="Didacticiel Hadoop : prise en main de Hadoop sur Windows | Microsoft Azure"
   description="Prise en main de Hadoop dans HDInsight. Apprenez à approvisionner des clusters Hadoop sur Windows, à exécuter une requête Hive sur des données et à en analyser les résultats dans Excel."
   keywords="didacticiel Hadoop,hadoop sur windows,cluster hadoop,découverte de hadoop,requête hive"
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/29/2015"
   ms.author="nitinme"/>


# Didacticiel Hadoop : prise en main de Hadoop et d'une requête Hive dans HDInsight sous Windows

> [AZURE.SELECTOR]
- [Windows](../hdinsight-hadoop-tutorial-get-started-windows-v1.md)
- [Linux](../hdinsight-hadoop-linux-get-started.md)

Pour vous familiariser plus facilement à Hadoop sur Windows et commencer à utiliser HDInsight, ce didacticiel vous montre comment exécuter une requête Hive sur des données non structurées dans un cluster Hadoop, puis comment analyser les résultats dans Microsoft Excel.

[AZURE.INCLUDE [portail Azure hdinsight](../../includes/hdinsight-azure-portal.md)]

* [Prise en main de Hadoop dans HDInsight sous Windows](hdinsight-hadoop-tutorial-get-started-windows.md)

## À quoi sert ce didacticiel Hadoop ?

Partons du principe que vous avez un vaste jeu de données non structurées et que vous voulez exécuter une requête Hive sur ces données pour en extraire des informations pertinentes. C'est exactement ce que nous allons faire dans ce didacticiel. Voici comment procéder :

   ![Didacticiel Hadoop : création d'un compte ; approvisionnement d'un cluster Hadoop ; envoi d'une requête Hive ; analyse de données dans Excel.][image-hdi-getstarted-flow]

Visionnez une vidéo de démonstration de ce didacticiel pour vous familiariser avec Hadoop sur HDInsight :

![Vidéo d'un premier didacticiel Hadoop : envoi d'une requête Hive sur un cluster Hadoop et analyse des résultats dans Excel.][img-hdi-getstarted-video]

**[Regarder le didacticiel Hadoop pour HDInsight sur YouTube](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS)**


Parallèlement à la mise à disposition générale d'Azure HDInsight, Microsoft fournit également l'émulateur HDInsight pour Azure, connu auparavant sous le nom de *Microsoft HDInsight Developer Preview*. L'émulateur cible les scénarios de développement et ne prend en charge que les déploiements sur un seul nœud. Pour plus d'informations sur l'utilisation de l'émulateur HDInsight, consultez la rubrique [Prise en main de l'émulateur HDInsight][hdinsight-emulator].

> [AZURE.NOTE] Pour obtenir des instructions sur l'approvisionnement d'un cluster HBase, consultez [Approvisionnement d'un cluster HBase dans HDInsight][hdinsight-hbase-custom-provision]. Pour pouvoir choisir plus facilement entre une base de données et une autre, consultez l'article <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Différences entre Hadoop et HBase</a>.

## Composants requis

Avant de commencer ce didacticiel pour Hadoop sur Windows, vous devez disposer des éléments suivants :


- **Un abonnement Azure**. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un poste de travail** avec Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

**Durée estimée pour effectuer ce didacticiel :** 30 minutes



##<a name="storage"></a>Création d'un compte de stockage Azure

Lors de l'approvisionnement d'un cluster Hadoop dans HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans un système de fichiers distribués Hadoop (HDFS). Par défaut, le cluster HDInsight est approvisionné dans le même centre de données que le compte de stockage que vous spécifiez. Pour plus d'informations, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

>[AZURE.NOTE] Ne partagez pas un conteneur de stockage d'objets blob par défaut avec plusieurs clusters Hadoop.

En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage supplémentaires quand vous configurez de façon personnalisée un cluster. Ce compte de stockage supplémentaire peut être ajouté à partir du même abonnement Azure ou à partir d'autres abonnements Azure. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][hdinsight-provision].

Ce didacticiel utilise uniquement l'objet blob par défaut et le compte de stockage par défaut.

**Pour créer un compte Azure Storage**

1. Connectez-vous au [portail Azure Classic][azure-management-portal].
2. Cliquez sur **NOUVEAU** dans l'angle inférieur gauche, puis entrez les valeurs comme indiqué dans l'image.

	![Portail Azure Classic où vous pouvez utiliser l’option Création rapide pour configurer un nouveau compte de stockage.][image-hdi-storageaccount-quickcreate]

>[AZURE.NOTE]  Veillez à créer le compte de stockage à un emplacement pris en charge pour le cluster. À savoir : **Asie de l'Est**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis**, **Sud du centre des États-Unis**.

Sélectionnez le nouveau compte de stockage dans la liste et cliquez sur **GÉRER LES CLÉS D'ACCÈS** au bas de la page. Notez la **CLÉ D'ACCÈS PRIMAIRE** (ou la **CLÉ D'ACCÈS SECONDAIRE**, les deux peuvent être utilisées). Vous en aurez besoin plus tard dans le didacticiel. Pour plus d'informations, consultez [Création d'un compte de stockage][azure-create-storageaccount].

##<a name="provision"></a>Approvisionnement d'un cluster Hadoop

Quand vous approvisionnez un cluster, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Dans cette section, vous approvisionnez un cluster HDInsight version 3.1, qui est basé sur la version 2.4 d'Hadoop. Vous pouvez également créer des clusters Hadoop pour d’autres versions au moyen du portail Azure Classic, des applets de commande HDInsight PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][hdinsight-provision]. Pour en savoir plus sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


**Pour approvisionner un cluster Hadoop**

1. Connectez-vous au [portail Azure Classic][azure-management-portal].

2. Cliquez sur **NOUVEAU** dans l'angle inférieur gauche, puis entrez les valeurs comme indiqué dans l'image.

	![Création d'un cluster Hadoop dans HDInsight.][image-hdi-quickcreatecluster]

<!-- COMMENTED OUT TEXT BEGINS --

4. Enter or select the following values:


	<table border="1">
	<tr><th>Name</th><th>Value</th></tr>
	<tr><td>Cluster Name</td><td>Name of the cluster.</td></tr>
	<tr><td>Cluster Size</td><td>Number of data nodes you want to deploy. The default value is 4. But the option to use 1 or 2 data nodes is also available from the drop-down list. Any number of cluster nodes can be specified by using the <strong>Custom Create</strong> option. Pricing details about the billing rates for various cluster sizes are available. Click the <strong>?</strong> symbol above the drop-down list and follow the link that appears.</td></tr>
	<tr><td>Password</td><td>The password for the <i>admin</i> account. The cluster user name "admin" is specified when you are not using the <strong>Custom Create</strong> option. Note that this is NOT the Windows Administrator account for the VMs on which the clusters are provisioned. The account name can be changed by using the <strong>Custom Create</strong> wizard.</td></tr>
	<tr><td>Storage Account</td><td>Click the drop-down list, and select the storage account that you created. <br/>

	When a storage account is chosen, it cannot be changed. If the storage account is removed, the cluster will no longer be available for use.

	The HDInsight cluster is located in the same datacenter as the storage account.
	</td></tr>
	</table>

	Keep a copy of the cluster name. You will need it later in the tutorial.


5. Click **Create HDInsight Cluster**. When the provisioning completes, the  status column shows **Running**.

-- COMMENTED OUT TEXT ENDS -->

>[AZURE.NOTE] Ces étapes approvisionnent un cluster HDInsight avec la version 3.1. Pour créer un cluster avec d’autres versions, utilisez la méthode **Création personnalisée** à partir du portail ou utilisez Azure PowerShell. Pour plus d’informations sur les différences entre chaque version, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight][hdinsight-versions]. Pour plus d’informations sur l’utilisation de l’option **CRÉATION PERSONNALISÉE**, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d’options personnalisées][hdinsight-provision].


##<a name="sample"></a>Exécution d'exemples de données à partir du portail

Un cluster HDInsight approvisionné correctement fournit une console de requête incluant une galerie de prise en main permettant d’exécuter les exemples directement à partir du portail. Vous pouvez utiliser les exemples pour apprendre à utiliser HDInsight en parcourant quelques scénarios de base. Ces exemples sont fournis avec tous les composants requis, tels que les données à analyser et les requêtes à exécuter sur les données. Pour plus d’informations sur les exemples de la galerie de prise en main, consultez la page [Découverte de Hadoop à l’aide de la galerie de prise en main de HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md).

**Pour exécuter l’exemple**, à partir du portail Azure Classic, cliquez sur le nom du cluster dans lequel vous voulez exécuter l’exemple, puis cliquez sur **Console de requête** en bas de la page. Dans la page web qui s’ouvre, cliquez sur l’onglet **Galerie de prise en main**, puis sous la catégorie**Exemples**, cliquez sur l’exemple que vous voulez exécuter. Suivez les instructions de la page web pour terminer l’exemple. Le tableau suivant répertorie quelques exemples et fournit plus d’informations sur les chaque exemple.

Exemple | Fonction
------ | ---------------
[Analyse des données de capteur][hdinsight-sensor-data-sample] | Découvrez comment utiliser HDInsight pour traiter les données d’historique produites par les systèmes de chauffage, de ventilation et de climatisation, et ainsi identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée.
[Analyse du journal de site web][hdinsight-weblogs-sample] | Découvrez comment utiliser HDInsight pour analyser des fichiers journaux de site web afin d’obtenir des informations sur la fréquence des accès au site web en une journée à partir de sites web externes, ainsi qu’un résumé des erreurs de site web rencontrées par les utilisateurs.
[Analyse de tendances Twitter](hdinsight-analyze-twitter-data.md) | Apprenez à utiliser HDInsight pour analyser les tendances dans Twitter.



##<a name="hivequery"></a>Exécution d'une requête HIVE à partir du portail
À présent qu'un cluster HDInsight a été configuré, la prochaine étape consiste à exécuter un travail Hive afin d'interroger un exemple de table Hive. Nous allons utiliser *hivesampletable*, qui est fournie avec les clusters HDInsight. Cette table contient des données sur des fabricants, des plateformes et des modèles d'appareils mobiles. Une requête Hive exécutée sur cette table permet d'extraire les données relatives aux appareils mobiles d'un fabricant spécifique.

> [AZURE.NOTE] HDInsight Hadoop Tools pour Visual Studio est fourni avec le Kit de développement logiciel (SDK) Azure pour .NET version 2.5 ou ultérieure. L'utilisation des outils de Visual Studio vous permet de vous connecter à un cluster HDInsight, de créer des tables Hive et d'exécuter des requêtes Hive. Pour plus d'informations, consultez la page [Prise en main de HDInsight Hadoop Tools pour Visual Studio][1].

**Exécution d'un travail Hive à partir d'un tableau de bord du cluster**

1. Connectez-vous au [portail Azure Classic][azure-management-portal].
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters s'affiche, avec celui que vous avez créé dans la section précédente.
3. Cliquez sur le nom du cluster que vous voulez utiliser pour l'exécution du travail Hive, puis cliquez sur **CONSOLE DE REQUÊTE** au bas de la page.
4. Une page web s'ouvre dans un autre onglet du navigateur. Entrez le compte et le mot de passe de l'utilisateur Hadoop. Le nom d'utilisateur par défaut est **admin**, le mot de passe est celui que vous avez entré pendant l'approvisionnement du cluster. Le tableau de bord ressemble à ceci :

	![Onglet Éditeur Hive dans le tableau de bord du cluster HDInsight.][img-hdi-dashboard]

	Le haut de la page contient plusieurs onglets. L'onglet par défaut est **Éditeur Hive**, tandis que les autres onglets sont **Historique des travaux** et **Explorateur de fichiers**. À l'aide du tableau de bord, vous pouvez envoyer des requêtes Hive, vérifier les fichiers journaux Hadoop et parcourir les fichiers du stockage.

	> [AZURE.NOTE] Notez que l'URL de la page web est *&lt;ClusterName&gt;.azurehdinsight.net*. Ainsi, au lieu d'ouvrir le tableau de bord à partir du portail, vous pouvez également l'ouvrir à partir d'un navigateur web à l'aide de l'URL.

6. Dans l'onglet **Éditeur Hive**, pour **Nom de requête**, entrez **HTC20**. Le nom de la requête correspond au nom de la tâche. Dans le volet de requête, entrez la requête Hive, comme indiqué dans l'image :

	![Requête Hive entrée dans le volet de requête de l'éditeur Hive.][img-hdi-dashboard-query-select]

4. Cliquez sur **Envoyer**. Il faut quelques instants pour obtenir les résultats. L'écran est actualisé toutes les 30 secondes. Vous pouvez également cliquer sur **Actualiser** pour actualiser l'écran.

    ![Résultats d'une requête Hive répertoriés en bas du tableau de bord du cluster.][img-hdi-dashboard-query-select-result]

5. Une fois que l'état indique que la tâche est terminée, cliquez sur le nom de la requête à l'écran pour afficher le résultat. Notez **l'heure de début de la tâche (UTC)**. Vous en aurez besoin ultérieurement.

    ![Heure de début du travail répertoriée sous l'onglet Historique des travaux du tableau de bord du cluster HDInsight.][img-hdi-dashboard-query-select-result-output]

    La page indique également le **Résultat de la tâche** et le **Journal de la tâche**. Vous avez également la possibilité de télécharger le fichier de sortie (\_stdout) et le fichier journal (\_stderr).


**Accès au fichier de sortie**

1. Dans le tableau de bord du cluster, cliquez sur **Explorateur de fichiers**.
2. Cliquez sur le nom de votre compte de stockage, sur le nom de votre conteneur (qui est le même que le nom de votre cluster), puis sur **utilisateur**.
3. Cliquez sur **admin**, puis sur le GUID dont l'heure de dernière modification est légèrement postérieure à l'heure de début de tâche que vous avez notée précédemment. Copiez ce GUID. Vous en aurez besoin dans la prochaine section.


   	![GUID du fichier de sortie de la requête Hive, répertorié sous l'onglet Explorateur de fichiers.][img-hdi-dashboard-query-browse-output]


##<a name="powerquery"></a>Connexion aux outils décisionnels Microsoft pour Excel

Vous pouvez utiliser le complément Power Query pour Microsoft Excel afin d'importer le résultat de la tâche depuis HDInsight dans Excel, où des outils décisionnels Microsoft peuvent être utilisés afin d'effectuer une analyse plus approfondie des résultats.

Vous devez avoir installé Excel 2013 ou 2010 pour mener à bien cette partie du didacticiel.

**Téléchargement de Microsoft Power Query pour Excel**

- Téléchargez Microsoft Power Query pour Microsoft Excel depuis le [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=39379) et installez-le.

**Pour importer des données HDInsight**

1. Ouvrez Excel et créez un classeur.
3. Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Microsoft Azure HDInsight**.

	![Menu Importation Excel PowerQuery ouvert pour Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Entrez le **Nom de compte** du compte de stockage d'objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il s'agit du compte de stockage que vous avez créé plus tôt dans le didacticiel.
4. Entrez la **Clé du compte** du compte de stockage d’objets blob Azure, puis cliquez sur **Enregistrer**.
5. Dans le volet droit, double-cliquez sur le nom de l'objet blob. Par défaut, le nom de l'objet blob est identique à celui du cluster.

6. Localisez **stdout** dans la colonne **Nom**. Vérifiez que le GUID de la colonne** Chemin du dossier** correspond au GUID que vous avez copié précédemment. Une correspondance suggère que les données de sortie correspondent à la tâche que vous avez envoyée. Cliquez sur **Fichier binaire** dans la colonne à gauche de **stdout**.

	![Recherche des données produites par le GUID dans la liste du contenu.][image-hdi-gettingstarted-powerquery-importdata2]

9. Cliquez sur **Fermer et charger** dans le coin supérieur gauche pour importer le résultat de la tâche Hive dans Excel.


##<a name="nextsteps"></a>Étapes suivantes
Dans ce didacticiel Hadoop, vous avez appris à approvisionner un cluster Hadoop sur Windows dans HDInsight, à exécuter une requête Hive sur des données et à importer les résultats dans Excel, où ils peuvent faire l'objet d'un traitement plus poussé et être affichés sous forme de graphique avec des outils décisionnels. Pour plus d'informations, consultez les didacticiels suivants :

- [Prise en main de HDInsight Hadoop Tools pour Visual Studio][1]
- [Prise en main de l'émulateur HDInsight][hdinsight-emulator]
- [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage]
- [Administration de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell]
- [Téléchargement de données vers HDInsight][hdinsight-upload-data]
- [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
- [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie]
- [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-streaming]
- [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-emulator]: hdinsight-hadoop-emulator-get-started.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: hdinsight-hbase-tutorial-get-started.md


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install


[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/hdi-get-started-video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows-v1/HDI.GettingStarted.PowerQuery.ImportData2.png
 

<!---HONumber=AcomDC_0128_2016-->