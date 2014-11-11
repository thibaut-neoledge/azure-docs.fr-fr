<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop-3.0" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Prise en main de Hadoop 2.2 dans HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Prise en main de Hadoop&nbsp;2.4 dans HDInsight">Hadoop&nbsp;2.4</a>
<a href="../hdinsight-get-started-30" title="Prise en main de Hadoop&nbsp;2.2 dans HDInsight" class="current">Hadoop&nbsp;2.2</a>
<!--a href="../hdinsight-get-started-21" title="Get started using Hadoop 1.2 in HDInsight">Hadoop 1.2</a-->
</div>

Avec HDInsight, Apache Hadoop, une infrastructure logicielle MapReduce, devient disponible dans un environnement Azure plus simple, plus extensible et plus économique. HDInsight propose également une approche économique de la gestion et du stockage de données à l'aide du stockage d'objets blob Azure.

> [WACOM.NOTE] Si vous débutez avec Hadoop et les données volumineuses (Big Data), familiarisez-vous avec les termes [Apache Hadoop][Apache Hadoop], [MapReduce][MapReduce], [HDFS][HDFS] et [Hive][Hive].

Parallèlement à la mise à disposition générale d'Azure HDInsight, Microsoft a également publié l'émulateur HDInsight pour Azure, connu auparavant sous le nom de *Microsoft HDInsight Developer Preview*. Ce produit cible les scénarios de développement et ne prend en charge que les déploiements sur un nœud unique. Pour utiliser l'émulateur HDInsight, consultez la rubrique [Prise en main de l'émulateur HDInsight][Prise en main de l'émulateur HDInsight].

> [WACOM.NOTE] Pour des instructions sur l'approvisionnement d'un cluster HBase, consultez la page [Approvisionnement d'un cluster HBase dans HDInsight][Approvisionnement d'un cluster HBase dans HDInsight]. Pour connaître les raisons qui peuvent vous pousser à choisir Hadoop ou HBase, consultez la page [Différences entre Hadoop et HBase][Différences entre Hadoop et HBase].

## Objectif de ce didacticiel

Supposons que vous possédez un ensemble de données non structurées volumineux et que vous souhaitez exécuter des requêtes sur cet ensemble pour extraire des informations importantes. C'est exactement ce que nous allons faire dans ce didacticiel. La procédure est la suivante :

![HDI.GetStartedFlow][HDI.GetStartedFlow]

Vous pouvez également regarder une vidéo de démonstration de ce didacticiel :

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
-   [Exécution d'une tâche Hive][Exécution d'une tâche Hive]
-   [Connexion aux outils décisionnels Microsoft][Connexion aux outils décisionnels Microsoft]
-   [Étapes suivantes][Étapes suivantes]

## <a name="storage"></a>Création d'un compte de stockage Azure

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Lorsque vous approvisionnez un cluster HDInsight, vous spécifiez un compte de stockage Azure. Un conteneur de stockage d'objets blob particulier de ce compte est désigné comme système de fichiers par défaut, exactement comme dans le système HDFS. Le cluster HDInsight est approvisionné par défaut dans le même centre de données que le compte de stockage spécifié.

En plus de ce compte de stockage, vous pouvez ajouter des comptes de stockage supplémentaires lorsque vous effectuez une configuration personnalisée pour un cluster HDInsight. Ces comptes de stockage supplémentaires peuvent être associés au même abonnement Azure ou à des abonnements Azure différents. Pour des instructions, consultez la page [Approvisionnement de clusters HDInsight avec des options personnalisées][Approvisionnement de clusters HDInsight avec des options personnalisées].

Pour simplifier ce didacticiel, seuls le conteneur d'objets blob par défaut et le compte de stockage par défaut sont utilisés. Dans la pratique, les fichiers de données sont généralement stockés dans un compte de stockage désigné.

**Pour créer un compte Azure Storage**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Entrez l'**URL**, l'**emplacement** et la **réplication**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d'affinités ne sont pas pris en charge. Le nouveau compte de stockage figure dès lors dans la liste de stockage.

    > [WACOM.NOTE] Un cluster HDInsight et le compte de stockage Azure associé doivent se trouver dans le même centre de données. Par conséquent, veillez à créer votre compte de stockage aux emplacements pris en charge pour le cluster, c'est-à-dire dans les régions suivantes : **Asie orientale**, **Asie du Sud-Est**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Nord du centre des États-Unis** et **Sud du centre des États-Unis**.

4.  Attendez que le **statut** du nouveau compte de stockage passe à **Online**.
5.  Sélectionnez le nouveau compte de stockage dans la liste et cliquez sur **GÉRER LES CLÉS D'ACCÈS** au bas de la page.
6.  Notez le **NOM DU COMPTE DE STOCKAGE** et la **CLÉ D'ACCÈS PRIMAIRE** (ou la **CLÉ D'ACCÈS SECONDAIRE**. Les deux clés fonctionnent). Vous en aurez besoin plus loin dans le didacticiel.

Pour plus d'informations, consultez les pages
[Création d'un compte de stockage][Création d'un compte de stockage] et [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

## <a name="provision"></a>Approvisionnement d'un cluster HDInsight

Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez des ressources de calcul Azure qui contiennent Hadoop et les applications apparentées. Dans cette section, vous allez approvisionner un cluster HDInsight version 3.0, basé sur Hadoop version 2.2. Si vous voulez approvisionner un cluster HDInsight avec Hadoop version 2.4, cliquez sur l'onglet de la version appropriée au début de cet article. Vous pouvez également créer des clusters Hadoop pour d'autres versions en utilisant des cmdlets HDInsight PowerShell ou le Kit de développement logiciel (SDK) HDInsight .NET. Pour des instructions, consultez la page [Approvisionnement de clusters HDInsight avec des options personnalisées][Approvisionnement de clusters HDInsight avec des options personnalisées]. Pour des informations sur les différentes versions de HDInsight et les contrats SLA associés, consultez la page [Contrôle de version des composants HDInsight][Contrôle de version des composants HDInsight].

**Pour mettre en service un cluster HDInsight**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Cliquez sur **HDInsight** à gauche pour afficher le statut des clusters de votre compte. Dans la capture d'écran suivante, il n'existe aucun cluster HDInsight.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Dans la partie inférieure gauche de l'écran, cliquez sur **NOUVEAU**, sur **Services de données**, sur **HDInsight**, puis sur **Création personnalisée**.

    ![HDI.CustomCreateCluster][HDI.CustomCreateCluster]

    Entrez ou sélectionnez les valeurs comme indiqué dans l'image ci-dessus, puis cliquez sur la flèche vers la droite.

4.  Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
	<tr><td>Région/Réseau virtuel</td><td>Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici. Les régions disponibles sont les suivantes : <strong>Est de l'Asie</strong>, <strong>Asie du Sud-Est</strong>, <strong>Europe du Nord</strong>, <strong>Europe de l'Ouest</strong>, <strong>Est des États-Unis</strong>, <strong>Ouest des États-Unis</strong>, <strong>Nord du centre des États-Unis</strong> et <strong>Sud du centre des États-Unis</strong>.</td></tr>
	</table>

    Cliquez sur la flèche droite.

5.  Sur la page **Configurer l'utilisateur du cluster**, fournissez les valeurs suivantes :

    ![HDI.CustomCreateCluster.ClusterUser][HDI.CustomCreateCluster.ClusterUser]

    | Propriété                                      | Valeur                                                                                                                                                                                                                                                                                                                                                      |
    |------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nom d'utilisateur                              | Spécifiez le nom d'utilisateur de cluster HDInsight.                                                                                                                                                                                                                                                                                                        |
    | Mot de passe/Confirmer le mot de passe         | Spécifiez le mot de passe de cluster HDInsight.                                                                                                                                                                                                                                                                                                             |
    | Enter Hive/Oozie Metastore                     | Activez cette case à cocher pour spécifier une base de données SQL dans le même centre de données que le cluster devant être utilisé comme metastore Hive/Oozie. Cela est utile si vous voulez conserver les métadonnées à propos des tâches Hive/Oozie même après la suppression d'un cluster.                                                             |
    | Base de données de metastore                   | Spécifiez la base de données SQL Azure qui sera utilisée comme metastore pour Hive/Oozie. Cette base de données SQL doit se trouver dans le même centre de données que le cluster HDInsight. La zone de liste répertorie uniquement les bases de données SQL situées dans le même centre de données que celui spécifié dans la page **Détails du cluster**. |
    | Utilisateur de la base de données              | Spécifiez l'utilisateur de base de données SQL qui sera utilisé pour se connecter à la base de données.                                                                                                                                                                                                                                                     |
    | Mot de passe utilisateur de la base de données | Spécifiez le mot de passe de la base de données SQL.                                                                                                                                                                                                                                                                                                        |

    > [WACOM.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données SQL Azure, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est en cours d'exécution. Une fois que vous êtes dans la vue serveur, cliquez sur **Configurer**, puis, pour **Services Windows Azure**, cliquez sur **Oui**. Cliquez ensuite sur **Enregistrer**.

    Cliquez sur la flèche droite.

6.  Sur la page **Compte de stockage**, entrez la valeur suivante :

    ![HDI.CustomCreateCluster.StorageAccount][HDI.CustomCreateCluster.StorageAccount]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propriété</th>
    <th align="left">Valeur</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Compte de stockage</td>
    <td align="left">Spécifiez le compte de stockage Azure qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes :
    <ul>
    <li>Use Existing Storage</li>
    <li>Create New Storage</li>
    <li>Use Storage From Another Subscription</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Nom du compte</td>
    <td align="left"><ul>
    <li>Si vous avez choisi d'utiliser un stockage existant, sélectionnez un compte un stockage existant pour <strong>Nom du compte</strong>. La liste déroulante répertorie uniquement les comptes de stockage situés dans le même centre de données que celui où vous avez choisi d'approvisionner le cluster.</li>
    <li>Si vous avez choisi l'option <strong>Créer un nouveau stockage</strong> ou <strong>Utiliser le stockage associé à un autre abonnement</strong>, vous devez fournir le nom du compte de stockage.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Clé du compte</td>
    <td align="left">Si vous avez choisi l'option <strong>Utiliser le stockage associé à un autre abonnement</strong>, spécifiez la clé de compte pour ce compte de stockage.</td>
    </tr>
    <tr class="even">
    <td align="left">Conteneur par défaut</td>
    <td align="left"><p>Spécifie le conteneur par défaut sur le compte de stockage qui est utilisé comme système de fichiers par défaut pour le cluster HDInsight. Si vous avez choisi <strong>Utiliser le stockage existant</strong> pour le champ <strong>Compte de stockage</strong> et qu'il n'existe pas de conteneurs dans ce compte, le conteneur est créé par défaut avec le même nom que le cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite. Cependant, si le compte de stockage existant a un conteneur avec un nom différent de celui du cluster que vous avez spécifié, vous pouvez utiliser également ce conteneur.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Comptes de stockage supplémentaire</td>
    <td align="left">HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n'est pas limité. Toutefois, si vous créez un cluster au moyen du portail de gestion, la limite est établie à sept en raison de contraintes liées à l'interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez ajoute une page Compte de stockage supplémentaire à l'Assistant, vous permettant de spécifier les informations de compte. Par exemple, dans la capture d'écran ci-dessus, un compte de stockage supplémentaire étant sélectionné, la page 5 est ajoutée à la boîte de dialogue.</td>
    </tr>
    </tbody>
    </table>

    Si vous avez opté pour des comptes de stockage supplémentaires, cliquez sur la flèche vers la droite. Sinon, cliquez sur la coche pour commencer l'approvisionnement du cluster. À l'issue du processus d'approvisionnement, la colonne de statut indique **En cours d'exécution**.

7.  Sur la page **Compte de stockage**, entrez les informations du compte de stockage supplémentaire si vous avez opté pour cette solution.

    ![HDI.CustomCreateCluster.AddOnStorage][HDI.CustomCreateCluster.AddOnStorage]

    Vous avez ici aussi la possibilité de choisir un stockage existant, de créer un nouveau stockage ou d'utiliser un stockage d'un autre abonnement Azure. La procédure à suivre pour fournir les valeurs est similaire à celle décrite à l'étape précédente.

    Cliquez sur la coche pour démarrer l'approvisionnement du cluster. À l'issue du processus d'approvisionnement, la colonne de statut indique **En cours d'exécution**.

## <a name="sample"></a>Exécution d'une tâche Hive

Maintenant que vous avez approvisionné un cluster HDInsight, l'étape suivante consiste à exécuter une tâche Hive pour lancer une requête sur un exemple de table Hive, *hivesampletable*, qui est fourni avec les clusters HDInsight. La table contient des données sur le fabricant d'appareils mobiles, les plateformes et les modèles. Nous interrogeons cette table afin d'extraire des données pour les appareils mobiles d'un fabricant particulier.

**Pour exécuter une tâche Hive à partir du tableau de bord du cluster**

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters créés, y compris celui créé dans la dernière section, s'affiche.
3.  Cliquez sur le nom du cluster où vous voulez exécuter la tâche Hive, puis cliquez sur **GÉRER LE CLUSTER** en bas de la page.
4.  Une page web s'ouvre dans un autre onglet du navigateur. Entrez le compte et le mot de passe utilisateur Hadoop. Le nom d'utilisateur par défaut est **admin** ; le mot de passe est celui que vous avez entré pendant l'approvisionnement du cluster. Le tableau de bord a l'aspect suivant :

    ![hdi.dashboard][hdi.dashboard]

    Plusieurs onglets apparaissent en haut de la page. L'onglet par défaut est **Éditeur Hive** ; les autres onglets sont **Historique des tâches** et **Explorateur de fichiers**. À l'aide du tableau de bord, vous pouvez envoyer des requêtes Hive, vérifier les journaux des tâches Hadoop et parcourir les fichiers WASB.

    > [WACOM.NOTE] Notez que l'URL de la page web est *\<NomCluster\>.azurehdinsight.net*. Ainsi, au lieu d'ouvrir le tableau de bord à partir du portail de gestion, vous pouvez également l'ouvrir à partir d'un navigateur web en utilisant l'URL.

5.  Sous l'onglet **Éditeur Hive**, pour **Nom de la requête**, entrez **HTC20**. Le nom de la requête est le titre de la tâche.

6.  Dans le volet de requête, entrez la requête suivante :

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

7.  Cliquez sur **Submit**. Il faut un certain temps pour obtenir les résultats. L'écran s'actualise toutes les 30 secondes. Vous pouvez également cliquer sur **Actualiser** pour actualiser l'écran.

    Une fois l'opération terminée, l'écran a l'aspect suivant :

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

8.  Cliquez sur le nom de la requête à l'écran pour afficher le résultat. Notez l'**Heure de début de la tâche (UTC)**. Vous en aurez besoin plus tard.

    ![hdi.dashboard.query.select.result.output][hdi.dashboard.query.select.result.output]

    La page affiche également la **Sortie de la tâche** et le **Journal de la tâche**. Vous avez également la possibilité de télécharger le fichier de sortie (\_stdout) et le fichier journal (\_stderr).

    > [WACOM.NOTE] Le tableau **Session de tâches** sous l'onglet **Éditeur Hive** répertorie les tâches terminées ou en cours tant que vous restez sur cet onglet. Il ne répertorie aucune tâche si vous quittez cette page. Sous l'onglet **Historique des tâches** s'affiche la liste de toutes les tâches terminées ou en cours d'exécution.

**Pour accéder au fichier de sortie**

1.  Dans le tableau de bord du cluster, cliquez en haut sur **Explorateur de fichiers**.
2.  Cliquez sur le nom de votre compte de stockage, cliquez sur le nom de votre conteneur (qui est identique au nom de votre cluster), puis cliquez sur **user**.
3.  Cliquez sur admin, puis sur le GUID auquel correspond la date de la dernière modification, peu après l'heure de début de la tâche que vous avez notée précédemment. Notez ce GUID. Vous en aurez besoin dans la section suivante.

    ![hdi.dashboard.query.browse.output][hdi.dashboard.query.browse.output]

## <a name="powerquery"></a>Connexion aux outils décisionnels Microsoft

Vous pouvez utiliser le complément Microsoft Power Query pour Excel pour importer la sortie de la tâche de HDInsight dans Excel, où les outils décisionnels (BI) Microsoft permettent d'approfondir l'analyse des résultats.

Vous devez avoir installé Excel 2010 ou 2013 pour mener à bien cette partie du didacticiel.

**Pour télécharger Microsoft Power Query pour Excel**

-   Téléchargez Microsoft Power Query pour Excel depuis le [Centre de téléchargement Microsoft][Centre de téléchargement Microsoft] et installez-le.

**Pour importer des données HDInsight**

1.  Ouvrez Excel et créez un classeur vierge.
2.  Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  Entrez le **nom de compte** du compte de stockage d'objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il s'agit du compte de stockage que vous avez créé plus tôt dans le didacticiel.
4.  Entrez la **clé de compte** du compte de stockage d'objets blob Azure, puis cliquez sur **Enregistrer**.
5.  Dans le volet Navigateur situé à droite, double-cliquez sur le nom du conteneur de stockage d'objets blob. Par défaut, le nom du conteneur est identique à celui du cluster.

6.  Recherchez **stdout** dans la colonne **Nom**. Vérifiez que le GUID dans la colonne Chemin du dossier correspondante est identique à celui que vous avez noté précédemment. Cliquez sur **Binaire** à gauche de **stdout**.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Cliquez sur **Fermer et charger** dans le coin supérieur gauche pour importer la sortie de la tâche Hive dans Excel.

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
  [Prise en main de l'émulateur HDInsight]: ../hdinsight-get-started-emulator/
  [Approvisionnement d'un cluster HBase dans HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hbase-get-started/
  [Différences entre Hadoop et HBase]: http://go.microsoft.com/fwlink/?LinkId=510237
  [HDI.GetStartedFlow]: ./media/hdinsight-get-started/HDI.GetStartedFlow.png
  [formules d'abonnement]: http://azure.microsoft.com/fr-fr/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/fr-fr/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/fr-fr/pricing/free-trial/
  [Création d'un compte de stockage Azure]: #storage
  [Approvisionnement d'un cluster HDInsight]: #provision
  [Exécution d'une tâche Hive]: #sample
  [Connexion aux outils décisionnels Microsoft]: #powerquery
  [Étapes suivantes]: #nextsteps
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Approvisionnement de clusters HDInsight avec des options personnalisées]: ../hdinsight-provision-clusters/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Création d'un compte de stockage]: ../storage-create-storage-account/
  [Contrôle de version des composants HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
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
