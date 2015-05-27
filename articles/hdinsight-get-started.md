<properties 
   pageTitle="Prise en main de Hadoop avec Hive dans HDInsight | Azure" 
   description="Prise en main d'Hadoop in HDInsight, une solution de données puissante du cloud. Apprenez à configurer des clusters, interroger des données avec Hive et à produire les résultats dans Excel pour l'analyse." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/16/2015"
   ms.author="nitinme"/>


# Prise en main de Hadoop avec Hive dans HDInsight sur Windows

> [AZURE.SELECTOR]
- [Windows](hdinsight-get-started.md)
- [Linux](hdinsight-hadoop-linux-get-started.md)

Pour vous aider à prendre en main rapidement HDInsight, ce didacticiel vous montre comment exécuter une requête Hive pour extraire des informations pertinentes sur l’utilisation des téléphones portables à partir de données non structurées dans un cluster Hadoop. Ensuite, vous analyserez les résultats dans Microsoft Excel.


> [AZURE.NOTE]Si vous ne connaissez pas Hadoop et les données volumineuses (Big Data), vous pouvez vous familiariser avec les termes [Apache Hadoop][apache-hadoop], [MapReduce][apache-mapreduce], [Fichier Hadoop (HDFS)][apache-hdfs]et [Hive][apache-hive]. Pour comprendre la manière dont HDInsight prend en charge Hadoop dans Azure, consultez la rubrique [Présentation de Hadoop dans HDInsight][hadoop-hdinsight-intro].

Parallèlement à la mise à disposition générale d'Azure HDInsight, Microsoft fournit également l'émulateur HDInsight pour Azure, connu auparavant sous le nom de *Microsoft HDInsight Developer Preview*. L'émulateur cible les scénarios de développement et ne prend en charge que les déploiements sur un seul nœud. Pour plus d’informations sur l’utilisation de l’émulateur HDInsight, consultez la rubrique [Prise en main de l’émulateur HDInsight][hdinsight-emulator].

> [AZURE.NOTE]Pour obtenir des instructions sur l'approvisionnement d'un cluster HBase, consultez [Approvisionnement d'un cluster HBase dans HDInsight][hdinsight-hbase-custom-provision]. Pour pouvoir choisir plus facilement entre une base de données et une autre, consultez l’article <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Différences entre Hadoop et HBase</a>.

## Quel est l'objectif de ce didacticiel ? ##

Supposons que vous disposiez d'un vaste jeu de données non structurées et que vous souhaitiez y exécuter des requêtes pour extraire des informations significatives. C'est exactement ce que nous allons faire dans ce didacticiel. Voici comment procéder :

   ![« Prise en main de Hadoop avec Hive dans HDInsight » étapes du didacticiel illustrées : Création d’un compte ; Configuration d’un cluster ; Interrogation des données et Analyse dans Excel.][image-hdi-getstarted-flow]

Vous pouvez également [regarder une démonstration vidéo de ce didacticiel](https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS).

![HDI.getstarted.video][img-hdi-getstarted-video]


**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :


- Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][azure-purchase-options], les [offres spéciales membres][azure-member-offers] ou la [version d'évaluation gratuite][azure-free-trial].
- Un ordinateur avec Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

**Durée estimée pour effectuer ce didacticiel :** 30 minutes

##Dans ce didacticiel

* [Création d'un compte de stockage Azure](#storage)
* [Approvisionnement d’un cluster HDInsight](#provision)
* [Exécution d’exemples à partir du portail](#sample)
* [Exécution d’un travail HIVE](#hivequery)
* [Étapes suivantes](#nextsteps)

##<a name="storage"></a>Création d’un compte de stockage Azure

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Pour plus d’informations, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage].

Lors de l'approvisionnement d'un cluster HDInsight, vous spécifiez un compte Azure Storage. Un conteneur d’objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans un système de fichiers distribué Hadoop (HDFS). Par défaut, le cluster HDInsight est approvisionné par défaut dans le même centre de données que le compte de stockage que vous spécifiez.

>[AZURE.NOTE]Ne partagez pas un conteneur de stockage d’objets blob par défaut avec plusieurs clusters HDInsight.

En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage supplémentaires lorsque vous configurez de façon personnalisée un cluster HDInsight. Ce compte de stockage supplémentaire peut être ajouté à partir du même abonnement Azure ou à partir d’autres abonnements Azure. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][hdinsight-provision].

Afin de simplifier ce didacticiel, seuls l’objet blob par défaut et le compte de stockage par défaut sont utilisés. En pratique, les fichiers de données sont généralement stockés dans un compte de stockage précis.

**Pour créer un compte Azure Storage**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

	![Portail Azure où vous pouvez utiliser l’option Création rapide pour configurer un nouveau compte de stockage.][image-hdi-storageaccount-quickcreate]

3. Entrez l’**URL**, l’**EMPLACEMENT** et les informations de **RÉPLICATION**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d’affinités ne sont pas pris en charge. Le nouveau compte de stockage figure dès lors dans la liste de stockage.

	>[AZURE.NOTE]L’option CRÉATION RAPIDE permettant d’approvisionner un cluster HDInsight, comme celui que nous utilisons dans ce didacticiel, ne demande pas d’emplacement pendant l’approvisionnement du cluster. Il colocalise par défaut le cluster dans le même centre de données que le compte de stockage. Par conséquent, veillez à créer votre compte de stockage dans un emplacement pris en charge pour le cluster. À savoir : **Asie de l’Est**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l’Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis**, **Sud du centre des États-Unis**.

4. Attendez que le **STATUT** du nouveau compte de stockage passe à **En ligne**.
5. Sélectionnez le nouveau compte de stockage dans la liste et cliquez sur **GÉRER LES CLÉS D’ACCÈS** au bas de la page.
7. Notez le **NOM DU COMPTE DE STOCKAGE** et la **CLÉ D’ACCÈS PRIMAIRE** (ou la **CLÉ D’ACCÈS SECONDAIRE**, les deux peuvent être utilisées). Vous en aurez besoin plus loin dans le didacticiel.


Pour plus d’informations, consultez les rubriques [Création d’un compte de stockage][azure-create-storageaccount] et [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage].
	
##<a name="provision"></a>Approvisionnement d’un cluster HDInsight

Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Dans cette section, vous approvisionnez un cluster HDInsight version 3.1, qui est basé sur la version 2.4 d’Hadoop. Vous pouvez également créer des clusters Hadoop pour d’autres versions au moyen du portail Azure, des applets de commande HDInsight PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][hdinsight-provision]. Pour en savoir plus sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


**Pour mettre en service un cluster HDInsight**

1. Connectez-vous au [portail Azure][azure-management-portal]. 

2. Cliquez sur **HDInsight** sur le volet de gauche pour afficher le statut des clusters de votre compte. Dans la capture d'écran suivante, il n'existe aucun cluster HDInsight.

	![État des clusters HDInsight dans le portail Azure.][image-hdi-clusterstatus]

3. Dans la partie inférieure gauche de l’écran, cliquez sur **NOUVEAU**, sur **Services de données**, sur **HDInsight**, puis sur **Hadoop**.

	![Création d’un cluster Hadoop dans HDInsight.][image-hdi-quickcreatecluster]

4. Entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>Nom du cluster</td><td>Nom du cluster.</td></tr>
<tr><td>Cluster Size</td><td>Nombre de nœuds de données que vous souhaitez déployer. La valeur par défaut est 4. Mais la liste déroulante propose également 1&#160;ou&#160;2&#160;nœuds de données. Vous pouvez spécifier n'importe quel nombre de clusters en utilisant l'option <strong>Création personnalisée</strong>. Des détails de tarification sur les taux de facturation en fonction des tailles de cluster sont disponibles. Cliquez sur le point d’interrogation (<strong>?</strong>) placé au-dessus de la liste déroulante et suivez le lien qui s’affiche.</td></tr>
<tr><td>Mot de passe</td><td>Mot de passe du compte <i>admin</i>. Le nom d'utilisateur de cluster «&#160;admin&#160;» est spécifié lorsque vous n'utilisez pas l'option <strong>Création personnalisée</strong>. Notez qu'il ne s'agit PAS du compte d'administrateur Windows pour les machines virtuelles sur lesquelles les clusters sont approvisionnés. Le nom de compte peut être modifié au moyen de l'Assistant <strong>Création personnalisée</strong>.</td></tr>
<tr><td>Compte de stockage</td><td>Cliquez sur la liste déroulante et sélectionnez le compte de stockage que vous avez créé. <br/>

Lorsqu’un compte de stockage est sélectionné, il ne peut plus être modifié. Si le compte de stockage est supprimé, le cluster ne peut plus être utilisé. Le cluster HDInsight est situé dans le même centre de données que le compte de stockage. 
</td></tr>
</table>Conservez une copie du nom de cluster. Vous en aurez besoin plus loin dans le didacticiel.

	
5. Cliquez sur **Créer un cluster HDInsight**. À l'issue de l'approvisionnement, la colonne de statut indique **En cours d'exécution**.

	>[AZURE.NOTE]La procédure précédente crée un cluster à l’aide de la version 3.1 de HDInsight. Pour créer un cluster avec d’autres versions, utilisez la méthode **Création personnalisée** à partir du portail ou utilisez Azure PowerShell. Pour plus d’informations sur les différences entre chaque version, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight][hdinsight-versions]. Pour plus d’informations sur l’utilisation de l’option **CRÉATION PERSONNALISÉE**, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d’options personnalisées][hdinsight-provision].


##<a name="sample"></a>Exécution d’exemples à partir du portail

Un cluster HDInsight approvisionné correctement fournit une console de requête incluant une galerie de prise en main permettant d’exécuter les exemples directement à partir du portail. Vous pouvez utiliser les exemples pour apprendre à utiliser HDInsight en parcourant quelques scénarios de base. Ces exemples sont fournis avec tous les composants requis, tels que les données à analyser et les requêtes à exécuter sur les données. Pour plus d’informations sur les exemples de la galerie de prise en main, consultez la page [Découverte de Hadoop à l’aide de la galerie de prise en main de HDInsight](hdinsight-learn-hadoop-use-sample-gallery.md).

**Pour exécuter l’exemple**, à partir du portail Azure, cliquez sur le nom du cluster dans lequel vous voulez exécuter l’exemple, puis cliquez sur **Console de requête** en bas de la page. Dans la page web qui s’ouvre, cliquez sur l’onglet **Galerie de prise en main**, puis sous la catégorie**Exemples**, cliquez sur l’exemple que vous voulez exécuter. Suivez les instructions de la page web pour terminer l’exemple. Le tableau suivant répertorie quelques exemples et fournit plus d’informations sur les chaque exemple.

Exemple | Fonction
------ | ---------------
[Analyse des données de capteur][hdinsight-sensor-data-sample] | Découvrez comment utiliser HDInsight pour traiter les données d’historique produites par les systèmes de chauffage, de ventilation et de climatisation, et ainsi identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée.
[Analyse du journal de site web][hdinsight-weblogs-sample] | Découvrez comment utiliser HDInsight pour analyser des fichiers journaux de site web afin d’obtenir des informations sur la fréquence des accès au site web en une journée à partir de sites web externes, ainsi qu’un résumé des erreurs de site web rencontrées par les utilisateurs.
[Analyse de tendances Twitter](hdinsight-analyze-twitter-data.md) | Apprenez à utiliser HDInsight pour analyser les tendances dans Twitter.



##<a name="hivequery"></a>Exécution d’une requête HIVE à partir du portail
À présent qu’un cluster HDInsight a été configuré, la prochaine étape consiste à exécuter un travail Hive afin d’interroger un exemple de table Hive. Nous allons utiliser *hivesampletable*, qui est fournie avec les clusters HDInsight. Cette table contient des données sur des fabricants, des plateformes et des modèles d’appareils mobiles. Nous interrogeons cette table pour extraire les données relatives aux appareils mobiles d'un fabricant spécifique.

> [AZURE.NOTE]HDInsight Hadoop Tools pour Visual Studio est fourni avec le Kit de développement logiciel (SDK) Azure pour .NET version 2.5 ou ultérieure. L’utilisation des outils de Visual Studio vous permet de vous connecter à un cluster HDInsight, de créer des tables Hive et d’exécuter des requêtes Hive. Pour plus d’informations, consultez la page [Prise en main de HDInsight Hadoop Tools pour Visual Studio][1].



**Exécution d’un travail Hive à partir d’un tableau de bord du cluster**

1. Connectez-vous au [portail Azure][azure-management-portal]. 
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters s’affiche, avec celui que vous avez créé dans la section précédente.
3. Cliquez sur le nom du cluster que vous voulez utiliser pour l’exécution du travail Hive, puis cliquez sur **CONSOLE DE REQUÊTE** dans la partie inférieure de la page. 
4. Une page Web s’ouvre dans un autre onglet du navigateur. Entrez le compte et le mot de passe de l'utilisateur Hadoop. Le nom d'utilisateur par défaut est **admin**, le mot de passe est celui que vous avez entré pendant l'approvisionnement du cluster. Le tableau de bord ressemble à ceci :

	![Onglet Éditeur Hive dans le tableau de bord du cluster HDInsight.][img-hdi-dashboard]

	Le haut de la page contient plusieurs onglets. L’onglet par défaut est **Éditeur Hive**, tandis que les autres onglets sont **Historique des travaux** et **Explorateur de fichiers**. À l’aide du tableau de bord, vous pouvez envoyer des requêtes Hive, vérifier les fichiers journaux Hadoop et parcourir les fichiers du stockage.

	> [AZURE.NOTE]Notez que l’URL de la page web est *&lt;ClusterName&gt;.azurehdinsight.net*. Ainsi, au lieu d’ouvrir le tableau de bord à partir du portail, vous pouvez également l’ouvrir à partir d’un navigateur web à l’aide de l’URL.

6. Dans l'onglet **Éditeur Hive**, pour **Nom de requête**, entrez **HTC20**. Le nom de la requête correspond au nom de la tâche.

7. Dans le volet de requête, saisissez la requête suivante :

		SELECT * FROM hivesampletable
			WHERE devicemake LIKE "HTC%"
			LIMIT 20;

	![Requête entrée dans le volet de requête de l’éditeur Hive.][img-hdi-dashboard-query-select]

4. Cliquez sur **Envoyer**. Il faut quelques instants pour obtenir les résultats. L'écran est actualisé toutes les 30 secondes. Vous pouvez également cliquer sur **Actualiser** pour actualiser l'écran.

    Lorsque le travail est terminé, l’écran ressemble à ceci :

	![Résultats d’une requête Hive répertoriés en bas du tableau de bord du cluster.][img-hdi-dashboard-query-select-result]

5. Cliquez sur le nom de la requête à l'écran pour afficher le résultat. Notez **l'heure de début de la tâche**. Vous en aurez besoin ultérieurement.

    ![Heure de début du travail répertoriée sous l’onglet Historique des travaux du tableau de bord du cluster HDInsight.][img-hdi-dashboard-query-select-result-output]

    La page indique également le **Résultat de la tâche** et le **Journal de la tâche**. Vous avez également la possibilité de télécharger le fichier de sortie (_stdout) et le fichier journal (_stderr).


	> [AZURE.NOTE]La table **Session du travail** sous l’onglet **Éditeur Hive** indique les travaux terminés ou en cours d’exécution tant que vous restez dans cet onglet. Elle n'indique aucune tâche si vous quittez la page. L'onglet **Historique des tâches** conserve une liste de toutes les tâches, terminées ou en cours d'exécution.
 

**Accès au fichier de sortie**

1. Dans le tableau de bord du cluster, cliquez sur **Explorateur de fichiers**. 
2. Cliquez sur le nom de votre compte de stockage, sur le nom de votre conteneur (qui est le même que le nom de votre cluster), puis sur **utilisateur**.
3. Cliquez sur **admin**, puis sur le GUID dont l’heure de dernière modification est légèrement postérieure à l’heure de début de tâche que vous avez notée précédemment. Copiez ce GUID. Vous en aurez besoin dans la prochaine section.


   	![GUID du fichier de sortie répertorié sous l’onglet Explorateur de fichiers.][img-hdi-dashboard-query-browse-output]


###<a name="powerquery"></a>Connexion aux outils décisionnels Microsoft 

Vous pouvez utiliser le complément Power Query pour Microsoft Excel afin d’importer le résultat de la tâche depuis HDInsight dans Excel, où des outils décisionnels Microsoft peuvent être utilisés afin d’effectuer une analyse plus approfondie des résultats.

Vous devez avoir installé Excel 2013 ou 2010 pour mener à bien cette partie du didacticiel.

**Téléchargement de Microsoft Power Query pour Excel**

- Téléchargez Microsoft Power Query pour Microsoft Excel depuis le [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=39379) et installez-le.

**Pour importer des données HDInsight**

1. Ouvrez Excel et créez un classeur.
3. Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.

	![Menu Importation Excel PowerQuery ouvert pour Azure HDInsight.][image-hdi-gettingstarted-powerquery-importdata]

3. Entrez le **Nom de compte** du compte de stockage d’objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il s’agit du compte de stockage que vous avez créé plus tôt dans le didacticiel.
4. Entrez la **Clé du compte** du compte de stockage d’objets blob Azure, puis cliquez sur **Enregistrer**. 
5. Dans le volet droit, double-cliquez sur le nom de l’objet blob. Par défaut, le nom de l’objet blob est identique à celui du cluster. 

6. Localisez **stdout** dans la colonne **Nom**. Vérifiez que le GUID de la colonne** Chemin du dossier** correspond au GUID que vous avez copié précédemment. Une correspondance suggère que les données de sortie correspondent à la tâche que vous avez envoyée. Cliquez sur **Fichier binaire** dans la colonne à gauche de **stdout**.

	![Recherche des données produites par le GUID dans la liste du contenu.][image-hdi-gettingstarted-powerquery-importdata2]

9. Cliquez sur **Fermer et charger** dans le coin supérieur gauche pour importer le résultat de la tâche Hive dans Excel.


##<a name="nextsteps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à configurer un cluster avec HDInsight, à exécuter une tâche MapReduce sur ce dernier et à importer les résultats dans Excel, où ils peuvent faire l’objet d’un traitement plus poussé et être affichés sous forme de graphique à l’aide des outils décisionnels. Pour en savoir plus, consultez les articles suivants :

- [Prise en main de HDInsight Hadoop Tools pour Visual Studio][1]
- [Prise en main de l’émulateur HDInsight][hdinsight-emulator]
- [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage]
- [Administration de HDInsight à l’aide de PowerShell][hdinsight-admin-powershell]
- [Téléchargement de données vers HDInsight][hdinsight-upload-data]
- [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce]
- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
- [Utilisation d’Oozie avec HDInsight][hdinsight-use-oozie]
- [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-streaming]
- [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]


[1]: hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-versions]: hdinsight-component-versioning.md

[hdinsight-get-started-30]: hdinsight-get-started-30.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[apache-hive]: http://go.microsoft.com/fwlink/?LinkId=510085
[apache-mapreduce]: http://go.microsoft.com/fwlink/?LinkId=510086
[apache-hdfs]: http://go.microsoft.com/fwlink/?LinkId=510087
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/


[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: install-configure-powershell.md
[powershell-open]: install-configure-powershell.md#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png

[img-hdi-getstarted-video]: ./media/hdinsight-get-started/HDI.GetStarted.Video.png


[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-getstarted-flow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png

[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png

<!--HONumber=54-->