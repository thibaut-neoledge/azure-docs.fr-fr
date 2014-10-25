<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Prise en main de Hadoop 1.2 dans HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Prise en main de Hadoop&nbsp;2.2 dans HDInsight">Hadoop&nbsp;2.2</a>
<a href="../hdinsight-get-started-31" title="Prise en main de Hadoop&nbsp;2.4 dans HDInsight">Hadoop&nbsp;2.4</a>
<a href="../hdinsight-get-started-21" title="Prise en main de Hadoop&nbsp;1.2 dans HDInsight" class="current">Hadoop&nbsp;1.2</a>
</div>

HDInsight rend [Apache Hadoop][Apache Hadoop] accessible en tant que service dans le cloud. L'infrastructure logicielle MapReduce devient ainsi disponible dans un environnement Azure plus simple, plus extensible et plus économique. HDInsight propose également une approche économique de la gestion et du stockage de données à l'aide du stockage d'objets blob Azure.

Dans ce didacticiel, vous allez approvisionner un cluster HDInsight via le portail de gestion Azure, envoyer une tâche Hive à interroger à l'aide d'un exemple de table Hive utilisant le tableau de bord du cluster, puis importer les données de sortie de la tâche Hive dans Excel pour examen.

> [WACOM.NOTE] Ce didacticiel couvre l'utilisation de clusters Hadoop 1.2 sous HDInsight. Pour une autre version prise en charge, cliquez sur le sélecteur en haut de la page. Pour obtenir des informations de version, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight][Nouveautés des versions de cluster fournies par HDInsight].

Démonstration en direct de cet article :

<center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center>

Parallèlement à la mise à disposition générale d'Azure HDInsight, Microsoft a également publié l'émulateur HDInsight pour Azure, connu auparavant sous le nom de *Microsoft HDInsight Developer Preview*. Ce produit cible les scénarios de développement et, à ce titre, ne prend en charge que les déploiements sur nœud unique. Pour utiliser l'émulateur HDInsight, consultez la rubrique [Prise en main de l'émulateur HDInsight][Prise en main de l'émulateur HDInsight].

**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][formules d'abonnement], les [offres spéciales membres][offres spéciales membres] ou la [version d'évaluation gratuite][version d'évaluation gratuite].
-   Un ordinateur avec Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

**Durée de réalisation estimée :** 30 minutes

## Dans ce didacticiel

-   [Approvisionnement d'un cluster HDInsight][Approvisionnement d'un cluster HDInsight]
-   [Exécution d'une tâche Hive][Exécution d'une tâche Hive]
-   [Connexion aux outils décisionnels Microsoft][Connexion aux outils décisionnels Microsoft]
-   [Étapes suivantes][Étapes suivantes]

## <a name="provision"></a>Approvisionnement d'un cluster HDInsight

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Lors de l'approvisionnement d'un cluster HDInsight, un compte Azure Storage et un conteneur de stockage d'objets blob spécifique de ce compte sont désignés en tant que système de fichiers par défaut, comme dans HDFS. Ce compte de stockage doit se situer dans le même centre de données que les ressources de calcul HDInsight. Pour l'heure, les clusters HDInsight ne peuvent être approvisionnés que dans les centres de données suivants :

-   Asie du Sud-Est
-   Europe du Nord
-   Europe de l'Ouest
-   Est des États-Unis
-   Ouest des États-Unis

En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage à partir du même abonnement Azure ou depuis d'autres abonnements Azure. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][Approvisionnement de clusters HDInsight].

Afin de simplifier ce didacticiel, seuls le conteneur d'objets blob par défaut et le compte de stockage par défaut sont utilisés. Tous les fichiers sont stockés dans le conteneur de système de fichiers par défaut, situé sous */tutorials/getstarted/*. En pratique, les fichiers de données sont généralement stockés dans un compte de stockage précis.

**Pour créer un compte Azure Storage**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Entrez l'**URL**, l'**emplacement** et la **réplication**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d'affinités ne sont pas pris en charge. Le nouveau compte de stockage figure dès lors dans la liste de stockage.
4.  Attendez que le **statut** du nouveau compte de stockage passe à **Online**.
5.  Cliquez sur le nouveau compte de stockage dans la liste pour le sélectionner.
6.  Cliquez sur **GÉRER LES CLÉS D'ACCÈS** dans le bas de la page.
7.  Notez le **NOM DU COMPTE DE STOCKAGE** et la **CLÉ D'ACCÈS PRIMAIRE** (ou la **CLÉ D'ACCÈS SECONDAIRE**. Les deux clés fonctionnent). Vous en aurez besoin plus loin dans le didacticiel.

Pour plus d'informations, consultez les rubriques
[Création d'un compte de stockage][Création d'un compte de stockage] et [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

**Pour mettre en service un cluster HDInsight**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur **HDINSIGHT** à gauche pour afficher les clusters HDInsight sous votre compte. Dans la capture d'écran suivante, il n'existe aucun cluster HDInsight.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Dans la partie inférieure gauche de l'écran, cliquez sur **NOUVEAU**, sur **SERVICES DE DONNÉES**, sur **HDINSIGHT**, puis sur **CRÉATION PERSONNALISÉE**.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

4.  Dans l'onglet Détails du cluster, entrez ou sélectionnez les valeurs suivantes :

    | Nom                      | Valeur                                                                                                                                                                                                                                                                                                              |
    |--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nom du cluster**       | Nom du cluster.                                                                                                                                                                                                                                                                                                     |
    | **Nœuds de données**     | Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique.                                                                                                                                                                                                         
                                La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.                                                                                                                                                                                 |
    | **Version de HDInsight** | Choisissez **2.1** pour créer un cluster Hadoop 1.2 dans HDInsight.                                                                                                                                                                                                                                                 |
    | **Région**               | Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici. |

5.  Cliquez sur la flèche vers la droite dans le coin inférieur droit pour configurer l'utilisateur du cluster.
6.  Dans l'onglet Configuration de l'utilisateur du cluster, entrez un **nom d'utilisateur** et un **mot de passe** pour le compte d'utilisateur de cluster HDInsight. Outre ce compte, vous pouvez créer un compte utilisateur RDP après l'approvisionnement du cluster, de sorte que vous pouvez activer un Bureau à distance dans le cluster. Pour obtenir des instructions, consultez la rubrique [Administration de HDInsight à l'aide du portail de gestion][hdinsight-admin-portal].
7.  Cliquez sur la flèche vers la droite dans le coin inférieur droit pour configurer le compte de stockage.
8.  Dans l'onglet Compte de stockage, entrez ou sélectionnez les valeurs suivantes :

    | Nom                                 | Valeur                                                                                                                                                                                        |
    |-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | STORAGE ACCOUNT                     | Sélectionnez **Utiliser le stockage existant**. Vous pouvez également utiliser le portail de gestion pour créer un nouveau compte de stockage, si vous n'en avez pas créé.                    |
    | ACCOUNT NAME                        | Spécifiez le compte de stockage que vous avez créé dans la dernière procédure de ce didacticiel. Notez que seuls les comptes de stockage de la même région s'affichent dans la zone de liste. |
    | DEFAULT CONTAINER                   | Sélectionnez **Créer un conteneur par défaut**. Lorsque cette option est sélectionnée, le nom du conteneur par défaut est identique à celui du cluster.                                       |
    | Comptes de stockage supplémentaires | Sélectionnez **0**. Vous pouvez connecter le cluster à 7 autres comptes de stockage maximum.                                                                                                  |

9.  Cliquez sur l'icône en forme de coche dans le coin inférieur droit pour créer le cluster. À l'issue du processus d'approvisionnement, la colonne de statut indique **En cours d'exécution**.

## <a name="sample"></a>Exécution d'une tâche Hive

Maintenant que vous avez approvisionné un cluster HDInsight, La prochaine étape consiste à exécuter une tâche Hive afin d'interroger un exemple de table Hive fournie avec les clusters HDInsight. Le nom de la table est *hivesampletable*.

**Ouverture d'un tableau de bord de cluster**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters créés s'affiche, avec celui que vous avez créé dans la dernière section.
3.  Cliquez sur le nom du cluster où vous voulez exécuter la tâche Hive
4.  Cliquez sur **GÉRER LE CLUSTER** en bas de page pour ouvrir le tableau de bord du cluster. Une page web s'ouvre dans un autre onglet du navigateur.
5.  Entrez le nom d'utilisateur et le mot de passe du compte d'utilisateur Hadoop. Le nom d'utilisateur par défaut est **admin**, le mot de passe est celui que vous avez entré pendant l'approvisionnement. Le tableau de bord ressemble à ceci :

    ![hdi.dashboard][hdi.dashboard]

    Plusieurs onglets sont situés en haut. L'onglet par défaut est *Éditeur Hive*. Vous trouverez également Tâches et Fichiers. À l'aide de ce tableau de bord, vous pouvez envoyer des requêtes Hive, vérifier les fichiers journaux Hadoop et parcourir les fichiers WASB.

> [wacom.note] Remarquez que l'URL est *\<Nomducluster\>.azurehdinsight.net*. Au lieu d'ouvrir le tableau de bord à partir du portail de gestion, vous pouvez également l'ouvrir à partir d'un navigateur Internet à l'aide de l'URL.

**Exécution d'une requête Hive**

1.  Dans le tableau de bord du cluster, en haut, cliquez sur **Éditeur Hive**.
2.  Dans la zone **Nom de la requête**, entrez **HTC20** Le nom de la requête est le nom de la tâche.
3.  Dans le volet de requête, saisissez la requête suivante :

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4.  Cliquez sur **Submit**. Il faut quelques instants pour obtenir les résultats. L'écran est actualisé toutes les 30 secondes. Vous pouvez également cliquer sur **Actualiser** pour actualiser l'écran.

    Lorsque c'est terminé, l'écran a l'aspect suivant :

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    Notez **l'heure de début de la tâche**. Vous en aurez besoin ultérieurement.

    Faites défiler la page et vous verrez **le journal de la tâche**. La sortie de la tâche est stdout, et le journal de la tâche stderr.

5.  Si vous voulez rouvrir le fichier journal, cliquez sur **Tâches** en haut de l'écran, puis sur le nom de la tâche (nom de la requête). Par exemple, **HTC20** dans ce cas.

**Accès au fichier de sortie**

1.  Dans le tableau de bord du cluster, en haut, cliquez sur **Fichiers**.
2.  Cliquez sur **Templeton-Job-Status**.
3.  Cliquez sur la valeur GUID qui a la dernière heure de modification juste après l'heure de début de la tâche notée précédemment. Notez cette valeur GUID. Vous en aurez besoin dans la prochaine section.
4.  Le fichier **stdout** contient les données dont vous aurez besoin dans la prochaine section. Pour télécharger une copie du fichier de données, au besoin, cliquez sur **stdout**.

## <a name="powerquery"></a>Connexion aux outils décisionnels Microsoft

Le complément Power Query pour Excel peut être utilisé pour exporter une sortie de HDInsight vers Excel qui, combiné aux outils décisionnels (BI) Microsoft, permet de mieux traiter ou afficher les résultats.

Vous devez avoir installé Excel 2010 ou 2013 pour mener à bien cette partie du didacticiel.

**Téléchargement de Microsoft Power Query pour Excel**

-   Téléchargez Microsoft Power Query pour Excel depuis le [Centre de téléchargement Microsoft][Centre de téléchargement Microsoft] et installez-le.

**Pour importer des données HDInsight**

1.  Ouvrez Excel et créez un classeur vierge.
2.  Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  Entrez le **nom de compte** du compte de stockage d'objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il s'agit du compte de stockage que vous avez créé plus tôt dans le didacticiel.
4.  Entrez la **clé de compte** du compte de stockage d'objets blob Azure, puis cliquez sur **Enregistrer**.
5.  Dans le volet Navigateur situé à droite, double-cliquez sur le nom du conteneur de stockage d'objets blob. Par défaut, le nom du conteneur est identique à celui du cluster.

6.  Localisez **stdout** dans la colonne **Nom** (le chemin du dossier est *.../Templeton-Job-Status/<guid>*), puis cliquez sur **Binaire** à gauche de **stdout**. <guid> doit correspondre à ce que vous avez écrit à la dernière section.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Cliquez sur **Appliquer et fermer** dans le coin supérieur gauche. La requête importe ensuite la sortie de la tâche Hive dans Excel.

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
-   [Développement de programmes de diffusion en continu Hadoop en C\# pour HDInsight][Développement de programmes de diffusion en continu Hadoop en C\# pour HDInsight]
-   [Développement de programmes MapReduce en Java pour HDInsight][Développement de programmes MapReduce en Java pour HDInsight]

  [Hadoop 2.2]: ../hdinsight-get-started "Prise en main de Hadoop 2.2 dans HDInsight"
  [Hadoop 2.4]: ../hdinsight-get-started-31 "Prise en main de Hadoop 2.4 dans HDInsight"
  [Hadoop 1.2]: ../hdinsight-get-started-21 "Prise en main de Hadoop 1.2 dans HDInsight"
  [Apache Hadoop]: http://hadoop.apache.org/
  [Nouveautés des versions de cluster fournies par HDInsight]: ../hdinsight-component-versioning/
  
  [Prise en main de l'émulateur HDInsight]: ../hdinsight-get-started-emulator/
  [formules d'abonnement]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Approvisionnement d'un cluster HDInsight]: #provision
  [Exécution d'une tâche Hive]: #sample
  [Connexion aux outils décisionnels Microsoft]: #powerquery
  [Étapes suivantes]: #nextsteps
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Approvisionnement de clusters HDInsight]: ../hdinsight-provision-clusters/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Création d'un compte de stockage]: ../storage-create-storage-account/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Centre de téléchargement Microsoft]: http://www.microsoft.com/en-us/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Administration de HDInsight à l'aide de PowerShell]: ../hdinsight-administer-use-powershell/
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
  [Utilisation de MapReduce avec HDInsight]: ../hdinsight-use-mapreduce
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
  [Utilisation d'Oozie avec HDInsight]: ../hdinsight-use-oozie/
  [Développement de programmes de diffusion en continu Hadoop en C\# pour HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Développement de programmes MapReduce en Java pour HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [img-hdi-getstarted-video]: https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS