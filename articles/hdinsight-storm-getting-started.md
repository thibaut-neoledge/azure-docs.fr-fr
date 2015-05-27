<properties
	pageTitle="Didacticiel Apache Storm : tester les exemples Storm Starter | Microsoft Azure"
	description="Prise en main de l’analyse des big data avec Apache Storm en exécutant les exemples Starter Storm sur HDInsight. Découvrez comment utiliser Apache Storm pour traiter les données en temps réel."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="larryfr"/>


# Didacticiel Apache Storm sur HDInsight : prise en main de l’analyse de données volumineuses (« Big Data ») à l’aide d’exemples Storm Starter 

Apache Storm est un système de calcul en temps réel, évolutif, distribué, à tolérance de panne, qui permet de traiter des flux de données. Avec Storm sur Azure HDInsight, vous pouvez créer un cluster Storm basé sur le cloud qui effectue l’analyse de données volumineuses en temps réel.

##Avant de commencer

Vous devez disposer de ce qui suit pour suivre jusqu’au bout ce didacticiel Storm Apache :

* Un abonnement Azure

##Création d'un compte Azure Storage

Storm sur HDInsight utilise un stockage d’objet blob Azure pour stocker les fichiers journaux et les topologies envoyés au cluster. Utilisez les étapes suivantes pour créer un compte de stockage Azure à utiliser avec votre cluster :

1. Connectez-vous au [portail Azure](http://manage.windowsazure.com/).

2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, puis sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

	![Vous pouvez utiliser la fonctionnalité Création rapide pour configurer un compte de stockage sur le portail Azure.](./media/hdinsight-storm-getting-started/HDI.StorageAccount.QuickCreate.png)

3. Renseignez les champs **URL**, **EMPLACEMENT** et **RÉPLICATION**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Ne sélectionnez pas de groupe d’affinités lors de la création du stockage pour HDInsight. Vous verrez le nouveau compte de stockage dans la liste de stockage.

	>[AZURE.NOTE]L'option de création rapide permettant d'approvisionner un cluster HDInsight, comme celui que nous utilisons dans ce didacticiel, ne demande pas d'emplacement pendant l'approvisionnement du cluster. Au lieu de cela, elle installe, par défaut, le cluster dans le même centre de données que le compte de stockage. Par conséquent, assurez-vous de créer votre compte de stockage dans les emplacements pris en charge par le cluster, qui sont : **Asie de l’est**, **Asie du Sud-est**, **Europe du Nord**, **Europe de l’Ouest**, **est des États-Unis**, **ouest des États-Unis**, **centre des États-Unis**, **Amérique du Sud**.

4. Attendez que l’**ÉTAT** du nouveau compte de stockage passe à **En ligne**.

Pour plus d’informations sur la création des comptes de stockage, consultez la rubrique <a href="../storage-create-storage-account/" target="_blank">Création d’un compte de stockage</a>.

##Approvisionnement d'un cluster Storm dans le portail Azure

Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure qui contiennent Apache Storm et les applications associées. Vous pouvez également créer des clusters Hadoop pour d’autres versions en utilisant le portail Azure, les cmdlets PowerShell Azure pour HDInsight ou HDInsight .NET SDK. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters HDInsight au moyen d'options personnalisées][hdinsight-provision]. Pour plus d’informations sur les différentes versions de HDInsight et leurs contrats de niveau de service (service level agreements, SLA), consultez la page [Versions des composants de HDInsight](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Connectez-vous au [portail Azure][azureportal].

2. Cliquez sur **HDInsight** à gauche, puis sur **+ NOUVEAU** dans le coin inférieur gauche de la page.

3. Cliquez sur l’icône HDInsight dans la deuxième colonne, puis sélectionnez **STORM**.

	![création rapide](./media/hdinsight-storm-getting-started/quickcreate.png)

4. Entrez un nom de cluster et un mot de passe pour le compte administrateur. Pour **COMPTE DE STOCKAGE**, sélectionnez le compte de stockage créé précédemment.

	Pour la **TAILLE DU CLUSTER**, sélectionnez une taille d’un **nœud de données** à utiliser pour ce cluster. Cela permet de réduire le coût associé au cluster. À des fins de production, il vous faudra créer un cluster plus grand.

	> [AZURE.NOTE]Le compte d’administrateur pour le cluster est nommé **admin**. Le mot de passe que vous entrez est le mot de passe pour ce compte. Vous aurez besoin de ces informations pour effectuer des actions avec le cluster, comme envoyer ou gérer des topologies Storm.

5. Enfin, sélectionnez la case à cocher située à côté de **CRÉATION D’UN CLUSTER HDINSIGHT** pour créer le cluster.

> [AZURE.NOTE]L’approvisionnement de clusters prend un certain temps, généralement moins de 15 minutes, pour créer le cluster, configurer les logiciels et installer les exemples de données et de topologies.

##Exécution d’un exemple Starter Storm sur HDInsight

Avec ce didacticiel Apache Storm vous apprendrez à analyser des big data à l’aide d’exemples Starter Storm sur GitHub.

Chaque cluster Storm sur HDInsight est doté d’un tableau de bord Storm, qui peut être utilisé pour télécharger et exécuter les topologies Storm sur le cluster. Chaque cluster est également fourni avec des exemples de topologies pouvant être exécutés directement depuis le tableau de bord Storm.

###<a id="connect"></a>Connexion au tableau de bord

Le tableau de bord se trouve dans **https://&lt;clustername>.azurehdinsight.net//**, où **clustername** est le nom du cluster. Vous trouverez également un lien pour votre cluster qui mène vers le tableau de bord, en bas de la page du portail Azure 

![Portail Azure avec un lien vers le tableau de bord Storm](./media/hdinsight-storm-getting-started/dashboard-link.png)

> [AZURE.NOTE]Lorsque vous vous connectez au tableau de bord, vous devrez entrer un nom d’utilisateur et un mot de passe. Il s’agit du nom de l’administrateur (** admin **) et du mot de passe utilisé lors de la création du cluster.

Une fois le tableau de bord Storm chargé, le formulaire **Envoi de la topologie** s’affichera.

![Tableau de bord Storm avec le formulaire d’envoi de la topologie](./media/hdinsight-storm-getting-started/submit.png)

Le formulaire**Envoi de la topologie** peut servir à télécharger et exécuter des fichiers .jar contenant des topologies Storm. Il inclut également plusieurs exemples de base fournis avec le cluster.

###<a id="run"></a>Exécution de l’exemple de statistiques du projet de départ Storm dans GitHub

Les exemples fournis avec le cluster incluent plusieurs variantes d’une topologie de statistique. Ces exemples incluent un **spout** qui émet des phrases aléatoirement, et des **bolts** qui fragmentent chaque phrase en mots, puis comptent le nombre d’occurrences de chaque mot. Ces exemples sont tirés des <a href="https://github.com/apache/storm/tree/master/examples/storm-starter" target="_blank">exemples Storm Starter</a>, qui font partie d’Apache Storm.

Procédez comme suit pour exécuter un exemple Storm Starter :

1. Sélectionnez **StormStarter - WordCount** dans la liste déroulante du **fichier Jar**. Cela doit remplir les champs **Nom de la classe** et **Paramètres supplémentaires** avec les paramètres de cet exemple.

	![Tableau de bord Storm avec la statistique sélectionnée](./media/hdinsight-storm-getting-started/submit.png)

	* **Nom de la classe** : classe dans le fichier .jar qui envoie la topologie.
	* **Paramètres supplémentaires** : tous les paramètres requis par la topologie. Dans cet exemple, le champ sert à fournir un nom convivial pour la topologie envoyée.

2. Cliquez sur le bouton **Envoyer**. Après quelques instants, le champ **Résultat** affiche la commande utilisée pour envoyer la tâche, ainsi que les résultats de la commande. Le champ **Erreur** affiche toutes les erreurs qui se produisent au cours de l’envoi de la topologie.

	![Bouton Envoyer et résultats](./media/hdinsight-storm-getting-started/submit-results.png)

	> [AZURE.NOTE]Les résultats n’indiquent pas que la topologie est terminée : **une topologie Storm, une fois démarrée, s’exécute jusqu’à ce que vous l’arrêtiez.** La topologie de statistiques va générer des phrases aléatoires tout en conservant le décompte du nombre de fois qu’elle rencontrera chaque mot, jusqu’à ce que vous l’arrêtiez.

###<a id="monitor"></a>Analyse de la topologie

L’interface utilisateur Storm peut servir à analyser la topologie.

1. Dans la partie supérieure du tableau de bord Storm, sélectionnez **Interface utilisateur Storm**. Un résumé des informations sur le cluster et les topologies en cours d’exécution s’affichera.

	![Interface utilisateur Storm affichant le résumé de la topologie](./media/hdinsight-storm-getting-started/stormui.png)

	Dans la page ci-dessus, vous pouvez voir la durée pendant laquelle la topologie a été active, ainsi que le nombre de processus de travail, d’exécuteurs et de tâches actuellement utilisés.

	> [AZURE.NOTE]La colonne **Nom** contient le nom convivial fourni précédemment via le champ**Paramètres supplémentaires**.

4. Sous **Résumé de la topologie**, sélectionnez l’entrée **Statistiques** située dans la colonne **Nom**. Vous obtiendrez plus d’informations sur la topologie.

	![storm ui](./media/hdinsight-storm-getting-started/topology-summary.png)

	Cette page fournit les informations suivantes :

	* **Statistiques de topologie** : informations de base sur les performances de la topologie, organisées dans des fenêtres de temps.

		> [AZURE.NOTE]La sélection d’une fenêtre de temps spécifique modifie la fenêtre de temps pour les informations affichées dans d’autres sections de la page.

	* **Spouts** : informations de base sur les spouts, y compris la dernière erreur retournée par chaque spout.

	* **Bolts** : informations de base sur les bolts.

	* **Configuration de la topologie** : informations détaillées sur la configuration de la topologie.

	Cette page présente également les actions qui peuvent être effectuées sur la topologie :

	* **Activer** : reprend le traitement d’une topologie désactivée.

	* **Désactiver** : suspend une topologie en cours d’exécution.

	* **Rééquilibrer** : ajuste le parallélisme de la topologie. Il convient de rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet à la topologie d’ajuster le parallélisme pour compenser l’augmentation/la réduction du nombre de nœuds du cluster. Pour plus d’informations, consultez la rubrique <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Présentation du parallélisme d’une topologie Storm</a>.

	* **Supprimer** : met fin à une topologie Storm après expiration du délai spécifié.

5. À partir de cette page, sélectionnez une entrée dans la section **Spouts** ou **Bolts**. Vous obtiendrez des informations relatives au composant sélectionné.

	![storm ui](./media/hdinsight-storm-getting-started/component-summary.png)

	Cette page affiche les informations suivantes :

	* **Statistiques du spout/bolt** : informations de base sur les performances de la topologie, organisées dans des fenêtres de temps.

		> [AZURE.NOTE]La sélection d’une fenêtre de temps spécifique modifie la fenêtre de temps pour les informations affichées dans d’autres sections de la page.

	* **Statistiques d’entrée** (bolt uniquement) : informations sur les composants qui produisent des données consommées par le bolt.

	* **Statistiques de sortie** : informations sur les données émises par ce bolt.

	* **Exécuteurs** : informations sur les instances de ce composant.

	* **Erreurs** : erreurs générées par ce composant.

5. Lorsque vous affichez les détails d’un spout ou d’un bolt, sélectionnez une entrée depuis la colonne **Port** située dans la section **Exécuteurs** pour afficher les détails d’une instance spécifique du composant.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	À partir de ces données, vous pouvez voir qu’il y a 1 493 957 occurrences du mot **seven**. C’est le nombre de fois où il a été détecté depuis le démarrage de cette topologie.

###Arrêt de la topologie

Retournez à la page **Résumé de la topologie**, puis sélectionnez le bouton **Supprimer** de la section**Actions de topologie**. Lorsque vous êtes invité à entrer le nombre de secondes à attendre avant l’arrêt de la topologie, entrez le nombre 10. Après le délai d’expiration, la topologie n’apparaîtra plus lorsque vous vous rendrez dans la section **Interface utilisateur Storm** du tableau de bord.

##Résumé

Dans ce didacticiel sur Storm Apache, vous avez appris à créer un cluster Storm sur HDInsight à l’aide de Storm Starter et à déployer, surveiller et gérer des topologies Storm à l’aide du tableau de bord Storm.

##<a id="next"></a>Étapes suivantes

* **HDInsight Tools pour Visual Studio** : HDInsight Tools vous permet d’utiliser Visual Studio pour envoyer, surveiller et gérer des topologies Storm semblables à celles du tableau de bord Storm mentionnées plus haut. HDInsight Tools offre la possibilité de créer des topologies Storm C# et inclut des exemples de topologies que vous pouvez déployer et exécuter sur votre cluster.

	Pour plus d’informations, consultez la rubrique <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Prise en main de HDInsight Tools pour pour Visual Studio</a>.

* **Exemples de fichiers** : le cluster Storm HDInsight contient plusieurs exemples dans le répertoire **%STORM_HOME%\\contrib**. Chaque exemple doit contenir les éléments suivants :

	* Le code source : par exemple, storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Les documents Java : par exemple, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* L'exemple : par exemple, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Utilisez la commande « jar » pour extraire le code source ou les documents Java. Par exemple, « jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar ».

	> [AZURE.NOTE]Les documents Java sont composés de pages Web. Lorsque vous les avez extraits, utilisez un navigateur pour afficher le fichier **index.html**.

	Pour accéder à ces exemples, vous devez activer le bureau à distance pour le cluster Storm sur HDInsight, puis copier les fichiers à partir de **%STORM_HOME%\\contrib**.

* Le document suivant contient une liste d’autres exemples pouvant être utilisés avec Storm sur HDInsight :

	* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md

<!--HONumber=54-->