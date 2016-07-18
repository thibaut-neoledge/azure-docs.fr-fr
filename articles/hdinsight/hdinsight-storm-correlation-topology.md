<properties
 pageTitle="Corrélation des événements au fil du temps avec Storm et HBase sur HDInsight"
 description="Apprenez à mettre en corrélation les événements qui se produisent à des moments différents à l’aide de Storm et HBase sur HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/05/2016"
 ms.author="larryfr"/>

# Corrélation des événements au fil du temps avec Storm et HBase sur HDInsight

En utilisant une banque de données persistante avec Apache Storm, vous pouvez associer les entrées de données qui arrivent à des moments différents. Par exemple, la liaison des événements de connexion et de déconnexion d’une session utilisateur pour calculer la durée de la session.

Dans ce document, vous allez apprendre à créer une topologie Storm C# de base qui effectue le suivi des événements de connexion et de déconnexion des sessions utilisateur et calcule la durée de la session. La topologie utilise HBase comme un magasin de données persistant. HBase vous permet également d’effectuer des requêtes par lots sur les données historiques pour produire des informations supplémentaires, telles que le nombre de sessions utilisateur ayant démarré ou ayant pris fin pendant une période spécifique.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

## Configuration requise

-	Visual Studio et les outils HDInsight pour Visual Studio : voir [Prise en main des outils HDInsight pour Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) pour plus d’informations sur l’installation.

-	Apache Storm sur un cluster HDInsight (Windows). Cette commande exécute la topologie Storm, qui traite les données entrantes et les stocke sous HBase.

-	Apache HBase sur un cluster HDInsight (Windows ou Linux). Il s’agit de la source de données pour cet exemple.

## Architecture

![Diagramme de flux de données via la topologie](./media/hdinsight-storm-correlation-topology/correlationtopology.png)

La corrélation des événements requiert un identificateur courant pour la source d’événements. Par exemple, un ID d’utilisateur, un ID de session ou un autre élément de données qui soit a) unique et b) inclus dans toutes les données envoyées à Storm. Cet exemple utilise une valeur GUID pour représenter un ID de session.

Cet exemple se compose de deux clusters HDInsight :

-	HBase : magasin de données persistant pour les données d’historique

-	Storm : utilisé pour recevoir les données entrantes

Les données sont générées de manière aléatoire par la topologie Storm et se composent des éléments suivants :

-	Session ID : GUID qui identifie de façon unique chaque session

-	Event : événement START ou END Pour cet exemple, START se produit toujours avant END

-	Time : heure de l’événement.

Ces données sont traitées et stockées dans HBase.

### Topologie Storm

Quand une session démarre, un événement **START** est reçu par la topologie et enregistré dans HBase. Quand la topologie reçoit un événement **END**, elle extrait l’événement **START** et calcule le délai entre les deux événements. Cette valeur **Duration** est ensuite stockée dans HBase avec les informations relatives à l’événement **END**.

> [AZURE.IMPORTANT] Bien que cette topologie présente le modèle de base, une solution de production nécessite une conception particulière dans le cas des scénarios suivants :
>
> - Événements se produisant dans le désordre
> - Événements en double
> - Événements supprimés

L’exemple de topologie est composé des éléments suivants :

- 	Session.cs : simule une session utilisateur en créant un ID de session aléatoire, l’heure de début et la durée de la session

- 	Spout.cs : crée 100 sessions, émet un événement START, attend le délai aléatoire pour chaque session, puis émet un événement END. Il recycle ensuite les sessions terminées pour en générer de nouvelles.

-	HBaseLookupBolt.cs : utilise l’ID de session pour rechercher les informations de session à partir de HBase. Quand un événement END est traité, il recherche l’événement START correspondant et calcule la durée de la session.

-	HBaseBolt.cs : stocke les informations dans HBase.

-	TypeHelper.cs : permet la conversion de type lors de la lecture/écriture dans HBase.

### Schéma HBase

Dans HBase, les données sont stockées dans une table avec le schéma/les paramètres suivants :

-	Row key : l’ID de session est utilisé comme clé pour les lignes de cette table.

-	Column family : le nom de famille est « cf ». Les colonnes stockées dans cette famille sont :

	- 	event : START ou END

	- 	time : durée de l’événement en millisecondes

	-	duration : durée entre les événements START et END

-	VERSIONS : la famille « cf » est définie pour conserver 5 versions de chaque ligne

	> [AZURE.NOTE] Les versions sont un journal des valeurs précédentes stockées pour une clé de ligne spécifique. Par défaut, HBase retourne uniquement la valeur de la version la plus récente d’une ligne. Dans ce cas, la même ligne est utilisée pour tous les événements (START, END). Chaque version d’une ligne est identifiée par la valeur timestamp. Vous obtenez une vue historique des événements consignés pour un ID spécifique.

## Téléchargez le projet

Le projet type peut être téléchargé à l’adresse [https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation](https://github.com/Azure-Samples/hdinsight-storm-dotnet-event-correlation).

Ce téléchargement contient les projets C# suivants :

-	CorrelationTopology : topologie Storm C# qui émet de manière aléatoire des événements start et end pour les sessions utilisateur. Chaque session dure entre 1 et 5 minutes.

-	SessionInfo : application console C# qui crée la table HBase et fournit des exemples de requêtes pour renvoyer des informations sur les données de session stockées.

## Créer la table

1. Ouvrez le projet **SessionInfo** dans Visual Studio.

2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **SessionInfo** et sélectionnez **Propriétés**.

	![Capture d’écran de menu avec les propriétés sélectionnées](./media/hdinsight-storm-correlation-topology/selectproperties.png)

3. Sélectionnez **Paramètres**, puis définissez les valeurs suivantes :

	- 	HBaseClusterURL : URL vers votre cluster HBase. Par exemple, https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName : compte d’utilisateur admin/HTTP pour votre cluster

	- 	HBaseClusterPassword : mot de passe pour le compte d’utilisateur admin/HTTP

	- 	HBaseTableName : nom de la table à utiliser avec cet exemple

	-	HBaseTableColumnFamily : nom de famille de la colonne

	![Image de la boîte de dialogue Paramètres](./media/hdinsight-storm-correlation-topology/settings.png)

5. Exécutez la solution. Quand vous y êtes invité, sélectionnez la clé « c » pour créer la table sur votre cluster HBase.

## Générer et déployer la topologie Storm

1.	Ouvrez la solution **CorrelationTopology** dans Visual Studio.

2.	Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **CorrelationTopology**, puis sélectionnez les propriétés.

3.	Dans la fenêtre des propriétés, sélectionnez **Paramètres** et fournissez les informations suivantes. Les cinq premières valeurs doivent être similaires à celles utilisées par le projet **SessionInfo** :

	- 	HBaseClusterURL : URL vers votre cluster HBase. Par exemple, https://myhbasecluster.azurehdinsight.net

	- 	HBaseClusterUserName : compte d’utilisateur admin/HTTP pour votre cluster

	- 	HBaseClusterPassword : mot de passe pour le compte d’utilisateur admin/HTTP

	- 	HBaseTableName : nom de la table à utiliser avec cet exemple. Il doit contenir le même nom de table que celui utilisé dans le projet SessionInfo

	- 	HBaseTableColumnFamily : nom de famille de la colonne. Il doit contenir le même nom de famille de colonne que celui utilisé dans le projet SessionInfo

	> [AZURE.IMPORTANT] Ne modifiez pas les HBaseTableColumnNames, car les valeurs par défaut sont les noms utilisés par **SessionInfo** pour récupérer des données.

4.  Enregistrez les propriétés, puis générez le projet.

5.	Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Envoyer à Storm sur HDInsight**. Si vous y êtes invité, entrez les informations d’identification de votre abonnement Azure.

	![Image de l’élément de menu Envoyer à Storm](./media/hdinsight-storm-correlation-topology/submittostorm.png)

6.	Dans la boîte de dialogue **Submit Topology**, sélectionnez le cluster Storm qui va exécuter cette topologie.

	> [AZURE.NOTE] La première fois que vous soumettez une topologie, la récupération du nom des clusters HDInsight peut prendre quelques secondes.

7.	Une fois la topologie téléchargée et envoyée au cluster, **Storm Topology View** s’ouvre et affiche la topologie en cours d’exécution. Sélectionnez **CorrelationTopology** et utilisez le bouton d’actualisation en haut à droite de la page pour actualiser les informations de topologie.

	![Image de la vue de la topologie](./media/hdinsight-storm-correlation-topology/topologyview.png)

	Quand la topologie commence à générer des données, la valeur de la colonne **EMITTED** s’incrémente.

	> [AZURE.NOTE] Si **Storm Topology View** ne s’ouvre pas automatiquement, procédez comme suit pour l’ouvrir :
	>
	> 1. Dans l’**Explorateur de solutions**, développez **Azure**, puis **HDInsight**.
	>
	> 2. Cliquez avec le bouton droit sur le cluster Storm sur lequel s’exécute la topologie, puis sélectionnez **View Storm Topologies**

## Interroger les données

Une fois les données émises, procédez comme suit pour interroger les données.

1. Revenez au projet **SessionInfo**. S’il n’est pas en cours d’exécution, démarrez une nouvelle instance du projet.

2. Quand vous y êtes invité, sélectionnez **s** pour rechercher un événement START. Vous devez entrer une heure de début et de fin pour définir une plage de temps : seuls les événements entre ces deux heures sont renvoyés.

	Utilisez le format suivant quand vous entrez les heures de début et de fin : HH:MM et « am » ou « pm ». Par exemple, 11:20.

	Étant donné que la topologie vient de démarrer, utilisez une heure de début (avant son déploiement) et une heure de fin (actuelle). Ces choix vont permettre de capturer la plupart des événements START qui ont été générés à son démarrage. Quand la requête s’exécute, vous devez voir une liste des entrées similaire à ce qui suit :

		Session e6992b3e-79be-4991-afcf-5cb47dd1c81c started at 6/5/2015 6:10:15 PM. Timestamp = 1433527820737

La recherche des événements END fonctionne de la même façon que pour les événements START. Cependant, les événements END sont générés de manière aléatoire entre 1 et 5 minutes après l’événement START. Par conséquent, vous devez essayer plusieurs plages horaires pour trouver les événements END. Les événements END contiennent également la durée de la session, c’est-à-dire la différence entre l’heure de l’événement START et l’heure de l’événement END. Voici un exemple de données pour les événements END :

	Session fc9fa8e6-6892-4073-93b3-a587040d892e lasted 2 minutes, and ended at 6/5/2015 6:12:15 PM

> [AZURE.NOTE] Les valeurs d’heure s’expriment en heure locale ; l’heure retournée par la requête s’exprime en UTC.

##Arrêt de la topologie

Quand vous êtes prêt à arrêter la topologie, revenez au projet **CorrelationTopology** dans Visual Studio. Dans **Storm Topology View**, sélectionnez la topologie et utilisez le bouton **Kill** en haut de l’affichage de la topologie.

##Supprimer votre cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Étapes suivantes

Pour plus d’exemples Storm, consultez la page [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md).
 

<!---HONumber=AcomDC_0706_2016-->