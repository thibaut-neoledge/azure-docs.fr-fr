<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Prise en main de Hadoop dans HDInsight | Azure" metaKeywords="" description="Prise en main de Hadoop dans HDInsight, solution pour les donn&eacute;es volumineuses. D&eacute;couvrez comment approvisionner les clusters, ex&eacute;cuter des t&acirc;ches Hive et exporter les donn&eacute;es de sortie vers Excel &agrave; des fins d'analyse." metaCanonical="" services="hdinsight" documentationCenter="" title="Prise en main de Hadoop dans HDInsight" authors="nitinme" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="nitinme" />

# Prise en main de Hadoop dans HDInsight

<!--div class="dev-center-tutorial-selector sublanding"> <a href="../hdinsight-get-started" title="Get started using Hadoop 2.4 in HDInsight" class="current">Hadoop 2.4</a> <a href="../hdinsight-get-started-30" title="Get started using Hadoop 2.2 in HDInsight">Hadoop 2.2</a> <!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a> </div-->

HDInsight permet à Apache Hadoop, une infrastructure logicielle MapReduce, d'être disponible dans un environnement Azure plus simple, plus extensible et plus économique. HDInsight propose également une approche économique de la gestion et du stockage de données à l'aide du stockage d'objets blob Azure.

> [WACOM.NOTE] Si les concepts Hadoop et Big Data ne vous sont pas familiers, vous trouverez ici plus d'informations sur les termes [Apache Hadoop][Apache Hadoop], [MapReduce][MapReduce], [HDFS][HDFS] et [Hive][Hive]. Pour comprendre la manière dont HDInsight prend en charge Hadoop dans Azure, consultez la rubrique [Présentation de Hadoop dans HDInsight][Présentation de Hadoop dans HDInsight].

Parallèlement à la mise à disposition générale d'Azure HDInsight, Microsoft fournit également l'émulateur HDInsight pour Azure, connu auparavant sous le nom de *Microsoft HDInsight Developer Preview*. L'émulateur cible les scénarios de développement et ne prend en charge que les déploiements sur un seul nœud. Pour utiliser l'émulateur HDInsight, consultez la rubrique [Prise en main de l'émulateur HDInsight][Prise en main de l'émulateur HDInsight].

> [WACOM.NOTE] Pour des instructions sur l'approvisionnement d'un cluster HBase, consultez la rubrique [Approvisionnement d'un cluster HBase dans HDInsight][Approvisionnement d'un cluster HBase dans HDInsight]. Consultez l'article [Différences entre Hadoop et HBase][Différences entre Hadoop et HBase] pour pouvoir choisir plus facilement entre l'un et l'autre.

## Quel est l'objectif de ce didacticiel ?

Supposons que vous disposiez d'un vaste jeu de données non structurées et que vous souhaitiez y exécuter des requêtes pour extraire des informations significatives. C'est exactement ce que nous allons faire dans ce didacticiel. Voici comment procéder :

![HDI.GetStartedFlow][HDI.GetStartedFlow]

Vous pouvez également regarder une démonstration vidéo de ce didacticiel :

<center>
<iframe width="560" height="315" src="http://www.youtube.com/embed/v=Y4aNjnoeaHA?list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" frameborder="0" allowfullscreen>
</iframe>
</center>
<!--center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center-->

**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][formules d'abonnement], les [offres spéciales membres][offres spéciales membres] ou la [version d'évaluation gratuite][version d'évaluation gratuite].
-   Un ordinateur avec Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

**Durée de réalisation estimée :** 30 minutes

## Dans ce didacticiel

-   [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]
-   [Approvisionnement d'un cluster HDInsight][Approvisionnement d'un cluster HDInsight]
-   [Exécution d'exemples à partir du portail][Exécution d'exemples à partir du portail]
-   [Exécution d'une tâche HIVE][Exécution d'une tâche HIVE]
-   [Étapes suivantes][Étapes suivantes]

## <a name="storage"></a>Création d'un compte de stockage Azure

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Lors de l'approvisionnement d'un cluster HDInsight, vous spécifiez un compte Azure Storage. Un conteneur de stockage d'objets blob spécifique de ce compte est désigné en tant que système de fichiers par défaut, comme dans HDFS. Le cluster HDInsight est approvisionné par défaut dans le même centre de données que le compte de stockage que vous spécifiez.

En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage supplémentaires lorsque vous configurez de façon personnalisée un cluster HDInsight. Ce compte de stockage supplémentaire peut être ajouté à partir du même abonnement Azure ou à partir d'autres abonnements Azure. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][Approvisionnement de clusters HDInsight au moyen d'options personnalisées].

Afin de simplifier ce didacticiel, seuls le conteneur d'objets blob par défaut et le compte de stockage par défaut sont utilisés. En pratique, les fichiers de données sont généralement stockés dans un compte de stockage précis.

**Pour créer un compte Azure Storage**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Entrez l'**URL**, l'**emplacement** et la **réplication**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d'affinités ne sont pas pris en charge. Le nouveau compte de stockage figure dès lors dans la liste de stockage.

    > [WACOM.NOTE] L'option de création rapide permettant d'approvisionner un cluster HDInsight, comme celui que nous utilisons dans ce didacticiel, ne nécessite pas d'emplacement pendant l'approvisionnement du cluster. À la place, il colocalise par défaut le cluster dans le même centre de données que le compte de stockage. Par conséquent, veillez à créer votre compte de stockage dans les emplacements pris en charge pour le cluster, à savoir : **Asie de l'Est**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis**, **Sud du centre des États-Unis**.

4.  Attendez que le **statut** du nouveau compte de stockage passe à **Online**.
5.  Sélectionnez le nouveau compte de stockage dans la liste et cliquez sur **GÉRER LES CLÉS D'ACCÈS** au bas de la page.
6.  Notez le **NOM DU COMPTE DE STOCKAGE** et la **CLÉ D'ACCÈS PRIMAIRE** (ou la **CLÉ D'ACCÈS SECONDAIRE**. Les deux clés fonctionnent). Vous en aurez besoin plus loin dans le didacticiel.

Pour plus d'informations, consultez les rubriques
[Création d'un compte de stockage][Création d'un compte de stockage] et [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

## <a name="provision"></a>Approvisionnement d'un cluster HDInsight

Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez des ressources de calcul Azure contenant Hadoop et des applications connexes. Dans cette section, vous approvisionnez un cluster HDInsight version 3.1, qui est basé sur la version 2.4 d'Hadoop. Vous pouvez également créer des clusters Hadoop pour d'autres versions au moyen du portail Azure, des cmdlets HDInsight PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][Approvisionnement de clusters HDInsight au moyen d'options personnalisées]. Pour des informations sur les différentes versions de HDInsight et leurs contrats SLA, consultez la page [Contrôle de version des composants HDInsight][Contrôle de version des composants HDInsight].

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Pour mettre en service un cluster HDInsight**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur **HDInsight** à gauche pour afficher le statut des clusters de votre compte. Dans la capture d'écran suivante, il n'existe aucun cluster HDInsight.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Dans la partie inférieure gauche de l'écran, cliquez sur **NOUVEAU**, sur **Services de données**, sur **HDInsight**, puis sur **Hadoop**.

    ![HDI.QuickCreateCluster][HDI.QuickCreateCluster]

4.  Entrez ou sélectionnez les valeurs suivantes :

    | Nom                | Valeur                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
    |--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nom du cluster     | Nom du cluster                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Cluster Size       | Nombre de nœuds de données que vous souhaitez déployer. La valeur par défaut est 4, mais le menu déroulant propose également 1 ou 2 nœuds de données. Vous pouvez spécifier n'importe quel nombre de clusters en utilisant l'option **Création personnalisée**. Des détails de tarification sur les taux de facturation en fonction des tailles de cluster sont disponibles. Cliquez sur le symbole **?** juste au-dessus de la zone déroulante et suivez le lien dans la fenêtre contextuelle. |
    | Password           | Mot de passe du compte *admin*. Le nom d'utilisateur de cluster « admin » est spécifié lorsque vous n'utilisez pas l'option **Création personnalisée**. Notez qu'il ne s'agit PAS du compte d'administrateur Windows pour les machines virtuelles sur lesquelles les clusters sont approvisionnés. Le nom de compte peut être modifié au moyen de l'Assistant **Création personnalisée**.                                                                                                       |
    | Compte de stockage | Sélectionnez le compte de stockage que vous avez créé dans la zone déroulante.                                                                                                                                                                                                                                                                                                                                                                                                                  
                           Dès lors qu'un compte de stockage est sélectionné, il ne peut plus être modifié. Si le compte de stockage est supprimé, le cluster ne peut plus être utilisé. Le cluster HDInsight est colocalisé dans le même centre de données que le compte de stockage.                                                                                                                                                                                                                                     |

    Conservez une copie du nom de cluster. Vous en aurez besoin plus loin dans le didacticiel.

5.  Cliquez sur **Créer un cluster HDInsight**. À l'issue de l'approvisionnement, la colonne de statut indique **En cours d'exécution**.

    > [WACOM.NOTE] La procédure ci-dessus crée un cluster avec la version 3.1 du cluster HDInsight. Pour créer d'autres versions de clusters, utilisez la méthode de création personnalisée à partir du portail de gestion ou utilisez Azure PowerShell. Pour plus d'informations sur les différences entre chaque version de cluster, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight][Nouveautés des versions de cluster fournies par HDInsight] Pour plus d'informations sur l'utilisation de l'option **CRÉATION PERSONNALISÉE**, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][Approvisionnement de clusters HDInsight au moyen d'options personnalisées].

## <a name="sample"></a>Exécution d'exemples à partir du portail

Un cluster HDInsight approvisionné correctement fournit une console de requête permettant d'exécuter les exemples directement à partir du portail. Vous pouvez utiliser les exemples pour apprendre à utiliser HDInsight en parcourant quelques scénarios de base. Ces exemples sont fournis avec tous les composants requis, tels que les données à analyser et les requêtes à exécuter sur les données.

**Pour exécuter l'exemple**, à partir du portail de gestion Azure, cliquez sur le nom du cluster dans lequel vous voulez exécuter l'exemple, puis cliquez sur **Console de requête** en bas de la page. Dans la page web qui s'ouvre, cliquez sur l'onglet **Galerie de mise en route**, puis sous la catégorie **Exemples**, cliquez sur l'exemple que vous voulez exécuter. Suivez les instructions dans la page web pour terminer l'exemple. Pour plus d'informations sur la fonction de chaque exemple, cliquez sur les liens ci-dessous.

| Exemple                            | Fonction                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Analyse des données de capteur][Analyse des données de capteur] | Découvrez comment utiliser HDInsight pour traiter les données d'historique produites par les systèmes de chauffage, de ventilation et de climatisation, et ainsi identifier les systèmes qui ne sont pas en mesure de maintenir de façon fiable une température donnée.                 |
| [Analyse du journal de site web][Analyse du journal de site web] | Découvrez comment utiliser HDInsight pour analyser des fichiers journaux de site web afin d'obtenir des informations sur la fréquence des accès au site web en une journée à partir de sites web externes, ainsi qu'un résumé des erreurs de site web rencontrées par les utilisateurs. |

## <a name="hivequery"></a>Exécution d'une requête HIVE à partir du portail

À présent qu'un cluster HDInsight a été approvisionné, la prochaine étape consiste à exécuter une tâche Hive afin d'interroger un exemple de table Hive *hivesampletable*, fournie avec les clusters HDInsight. La table contient des données sur le fabricant, les plateformes et les modèles d'appareils mobiles. Nous interrogeons cette table pour extraire les données relatives aux appareils mobiles d'un fabricant spécifique.

**Exécution d'une tâche Hive à partir d'un tableau de bord du cluster**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters créés s'affiche, avec celui que vous avez créé dans la dernière section.
3.  Cliquez sur le nom du cluster où vous voulez exécuter la tâche Hive, puis cliquez sur **CONSOLE DE REQUÊTE** dans la partie inférieure de la page.
4.  Une page web s'ouvre dans un autre onglet du navigateur. Entrez le compte et le mot de passe de l'utilisateur Hadoop. Le nom d'utilisateur par défaut est **admin**, le mot de passe est celui que vous avez entré pendant l'approvisionnement du cluster. Le tableau de bord ressemble à ceci :

    ![hdi.dashboard][hdi.dashboard]

    Plusieurs onglets sont situés en haut. L'onglet par défaut est **Éditeur Hive**, tandis que les autres onglets sont **Historique des tâches** et **Explorateur de fichiers**. À l'aide du tableau de bord, vous pouvez envoyer des requêtes Hive, vérifier les fichiers journaux Hadoop et parcourir les fichiers WASB.

    > [WACOM.NOTE] Notez que l'URL de la page web est *\<NomCluster\>.azurehdinsight.net*. Ainsi, au lieu d'ouvrir le tableau de bord à partir du portail de gestion, vous pouvez également l'ouvrir à partir d'un navigateur Internet à l'aide de l'URL.

5.  Dans l'onglet **Éditeur Hive**, pour **Nom de requête**, entrez **HTC20**. Le nom de la requête correspond au nom de la tâche.

6.  Dans le volet de requête, saisissez la requête suivante :

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

7.  Cliquez sur **Envoyer**. Il faut quelques instants pour obtenir les résultats. L'écran est actualisé toutes les 30 secondes. Vous pouvez également cliquer sur **Actualiser** pour actualiser l'écran.

    Lorsque c'est terminé, l'écran a l'aspect suivant :

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

8.  Cliquez sur le nom de la requête à l'écran pour afficher le résultat. Notez **l'heure de début de la tâche**. Vous en aurez besoin ultérieurement.

    ![hdi.dashboard.query.select.result.output][hdi.dashboard.query.select.result.output]

    La page indique également le **Résultat de la tâche** et le **Journal de la tâche**. Vous avez également la possibilité de télécharger le fichier de sortie (\_stdout) et le fichier journal (\_stderr).

    > [WACOM.NOTE] La table **Session de la tâche** sous l'onglet **Éditeur Hive** indique les tâches terminées ou en cours d'exécution tant que vous restez dans cet onglet. Elle n'indique aucune tâche si vous quittez la page. L'onglet **Historique des tâches** conserve une liste de toutes les tâches, terminées ou en cours d'exécution.

**Accès au fichier de sortie**

1.  Dans le tableau de bord du cluster, en haut, cliquez sur **Explorateur de fichiers**.
2.  Cliquez sur le nom de votre compte de stockage, sur le nom de votre conteneur (qui est le même que le nom de votre cluster), puis sur **utilisateur**.
3.  Cliquez sur **admin**, puis cliquez sur le GUID dont l'heure de dernière modification est légèrement postérieure à l'heure de début de tâche que vous avez notée précédemment. Notez cette valeur GUID. Vous en aurez besoin dans la prochaine section.

    ![hdi.dashboard.query.browse.output][hdi.dashboard.query.browse.output]

### <a name="powerquery"></a>Connexion aux outils décisionnels Microsoft

Vous pouvez utiliser le complément Power Query pour Microsoft Excel afin d'importer le résultat de la tâche depuis HDInsight dans Excel, où des outils décisionnels Microsoft peuvent être utilisés afin d'effectuer une analyse plus approfondie des résultats.

Vous devez avoir installé Excel 2010 ou 2013 pour mener à bien cette partie du didacticiel.

**Téléchargement de Microsoft Power Query pour Excel**

-   Téléchargez Microsoft Power Query pour Microsoft Excel depuis le [Centre de téléchargement Microsoft][Centre de téléchargement Microsoft] et installez-le.

**Pour importer des données HDInsight**

1.  Ouvrez Excel et créez un classeur vierge.
2.  Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  Entrez le **nom de compte** du compte de stockage d'objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il s'agit du compte de stockage que vous avez créé plus tôt dans le didacticiel.
4.  Entrez la **clé de compte** du compte de stockage d'objets blob Azure, puis cliquez sur **Enregistrer**.
5.  Dans le volet Navigateur situé à droite, double-cliquez sur le nom du conteneur de stockage d'objets blob. Par défaut, le nom du conteneur est identique à celui du cluster.

6.  Localisez **stdout** dans la colonne **Nom**. Vérifiez que le GUID dans la colonne **Chemin du dossier** correspond au GUID que vous avez noté précédemment. Une correspondance suggère que les données de sortie correspondent à la tâche que vous avez envoyée. Cliquez sur **Binaire** à gauche de **stdout**.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Cliquez sur **Fermer et charger** dans le coin supérieur gauche pour importer le résultat de la tâche Hive dans Excel.

## <a name="nextsteps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à approvisionner un cluster avec HDInsight, à exécuter une tâche MapReduce sur ce dernier et à importer les résultats dans Excel, où ils peuvent faire l'objet d'un traitement plus poussé et être affichés sous forme de graphique à l'aide des outils BI. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main de l'émulateur HDInsight][Prise en main de l'émulateur HDInsight]
-   [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight]
-   [Administration de HDInsight à l'aide de PowerShell][Administration de HDInsight à l'aide de PowerShell]
-   [Téléchargement de données vers HDInsight][Téléchargement de données vers HDInsight]
-   [Utilisation de MapReduce avec HDInsight][Utilisation de MapReduce avec HDInsight]
-   [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight]
-   [Utilisation de Pig avec HDInsight][Utilisation de Pig avec HDInsight]
-   [Utilisation d'Oozie avec HDInsight][Utilisation d'Oozie avec HDInsight]
-   [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight]
-   [Développement de programmes MapReduce en Java pour HDInsight][Développement de programmes MapReduce en Java pour HDInsight]

  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [MapReduce]: http://go.microsoft.com/fwlink/?LinkId=510086
  [HDFS]: http://go.microsoft.com/fwlink/?LinkId=510087
  [Hive]: http://go.microsoft.com/fwlink/?LinkId=510085
  [Présentation de Hadoop dans HDInsight]: ../hdinsight-hadoop-introduction/
  [Prise en main de l'émulateur HDInsight]: ../hdinsight-get-started-emulator/
  [Approvisionnement d'un cluster HBase dans HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hbase-get-started/
  [Différences entre Hadoop et HBase]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [formules d'abonnement]: http://azure.microsoft.com/fr-fr/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/fr-fr/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/fr-fr/pricing/free-trial/
  [Création d'un compte de stockage Azure]: #storage
  [Approvisionnement d'un cluster HDInsight]: #provision
  [Exécution d'exemples à partir du portail]: #sample
  [Exécution d'une tâche HIVE]: #hivequery
  [Étapes suivantes]: #nextsteps
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Approvisionnement de clusters HDInsight au moyen d'options personnalisées]: ../hdinsight-provision-clusters/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Création d'un compte de stockage]: ../storage-create-storage-account/
  [Contrôle de version des composants HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.QuickCreateCluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
  [Nouveautés des versions de cluster fournies par HDInsight]: ../hdinsight-component-versioning/
  [Analyse des données de capteur]: ../hdinsight-hive-analyze-sensor-data/
  [Analyse du journal de site web]: ../hdinsight-hive-analyze-website-log/
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [hdi.dashboard.query.select.result.output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
  [hdi.dashboard.query.browse.output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
  [Centre de téléchargement Microsoft]: http://www.microsoft.com/fr-fr/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Administration de HDInsight à l'aide de PowerShell]: ../hdinsight-administer-use-powershell/
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
  [Utilisation de MapReduce avec HDInsight]: ../hdinsight-use-mapreduce
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
  [Utilisation d'Oozie avec HDInsight]: ../hdinsight-use-oozie/
  [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Développement de programmes MapReduce en Java pour HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
