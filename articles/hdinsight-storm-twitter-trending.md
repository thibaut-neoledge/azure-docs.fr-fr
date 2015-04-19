<properties
   pageTitle="Rubriques tendances Twitter avec Apache Storm sur HDInsight | Azure"
   description="Découvrez comment utiliser Trident pour créer une topologie Storm qui détermine les rubriques tendances, selon les hashtags."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Rubriques tendances Twitter avec Apache Storm sur HDInsight

Découvrez comment utiliser Trident pour créer une topologie Storm qui détermine les rubriques tendances (hashtags) sur Twitter. 

Trident est une abstraction de haut niveau qui fournit des outils comme les jointures, les agrégations, le regroupement, les fonctions et les filtres. En outre, Trident ajoute des primitives pour le traitement incrémentiel avec état. Cet exemple montre comment créer une topologie à l'aide d'un spout et de fonctions personnalisés, ainsi que de plusieurs fonctions intégrées fournies par Trident.

> [AZURE.NOTE] Cet article repose largement sur l'exemple [trident-storm](https://github.com/jalonsoramos/trident-storm) de Juan Alonso.

##Configuration requise

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java et le JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Un compte de développeur Twitter

##Téléchargez le projet

Utilisez les éléments suivants pour cloner le projet localement.

	git clone https://github.com/Blackmist/TwitterTrending

##Topologie

La topologie de cet exemple est la suivante :

![topology](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] Il s'agit d'une vue simplifiée de la topologie : plusieurs instances des composants sont réparties entre les nœuds du cluster.

Le code Trident qui implémente la topologie est le suivant :

	topology.newStream("spout", spout)
	    .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
	    .groupBy(new Fields("hashtag"))
	    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
	    .newValuesStream()
	    .applyAssembly(new FirstN(10, "count"))
		.each(new Fields("hashtag", "count"), new Debug());

Il effectue les opérations suivantes :

1. Création d'un flux de données à partir du spout. Le spout extrait les tweets de Twitter, filtrés selon des mots clés spécifiques : amour, musique et café dans cet exemple.

2. HashtagExtractor, une fonction personnalisée, est utilisée pour extraire les hashtags de chaque tweet. Ils sont transmis au flux de données.

3. Le flux est ensuite regroupé par hashtag et transmis à une agrégation. Elle crée un décompte du nombre de fois où chaque hashtag s'est produit, qui est conservé en mémoire. Enfin, un flux de données contenant le hashtag et le compte est émis.

4. Étant donné que nous nous intéressons uniquement aux hashtags les plus populaires pour un lot donné de tweets, l'assembly FirstN est appliquée pour retourner uniquement les 10 premières valeurs, selon le champ nombre.

> [AZURE.NOTE] En plus du spout et de HashtagExtractor, nous utilisons une fonctionnalité intégrée de Trident.
> 
> Pour plus d'informations sur les opérations intégrées, consultez <a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">storm.trident.operation.builtin</a>.
> 
> Pour les implémentations d'état de Trident autres que MemoryMapState, consultez les rubriques suivantes :
> 
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">https://github.com/fhussonnois/storm-trident-elasticsearch</a>
> 
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">https://github.com/kstyrc/trident-redis</a>

###Le spout

Le spout, **TwitterSpout**, utilise <a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a> pour extraire des tweets de Twitter. Un filtre est créé (amour, musique et café) et les tweets entrants (état) qui correspondent au filtre sont stockés dans une <a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">LinkedBlockingQueue</a>. Pour finir, les éléments sont retirés de la file d'attente et émis dans la topologie.

###HashtagExtractor

Pour extraire les hashtags, <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a> est utilisé pour récupérer tous les hashtags contenus dans le tweet. Ils sont transmis au flux de données.

##Activer Twitter

Procédez comme suit pour inscrire une nouvelle application Twitter et obtenir les informations de jeton consommateur et d'accès nécessaires pour lire à partir de Twitter.

1. Accédez à <a href="" target="_blank">https://apps.twitter.com/</a> et utilisez le bouton **Create new app**. Lorsque vous remplissez le formulaire, laissez **URL de rappel** vide.

2. Une fois que l'application a été créée, sélectionnez l'onglet **Clés et jetons d'accès**.

3. Copiez les informations de **Clé du client** et de **Question secrète du client**.

4. En bas de la page, sélectionnez **Créer mon jeton d'accès** si aucun jeton n'existe. Une fois que les jetons ont été créés, copiez les informations de **Jeton d'accès** et **Question secrète du jeton d'accès**.

5. Dans le projet **TwitterSpoutTopology** que vous avez cloné auparavant, ouvrez le fichier **resources/twitter4j.properties**, ajoutez les informations collectées dans les étapes précédentes, puis enregistrez le fichier.

##Génération de la topologie

Utilisez les éléments suivants pour générer le projet.

		cd [directoryname]
		mvn compile

##Test de la topologie

Utilisez la commande suivante pour tester la topologie en local :

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Après le démarrage de la topologie, vous devez voir des informations de débogage contenant les hashtags et les décomptes émis par la topologie. Le résultat doit ressembler à ce qui suit.

	DEBUG: [Quicktellervalentine, 7]
	DEBUG: [GRAMMYs, 7]
	DEBUG: [AskSam, 7]
	DEBUG: [poppunk, 1]
	DEBUG: [rock, 1]
	DEBUG: [punkrock, 1]
	DEBUG: [band, 1]
	DEBUG: [punk, 1]
	DEBUG: [indonesiapunkrock, 1]

##Étapes suivantes

Maintenant que vous avez testé la topologie localement, découvrez comment [déployer cette topologie vers Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Les rubriques Storm suivantes peuvent également vous intéresser :

* [Développement de topologies Java pour Storm sur HDInsight à l'aide de Maven](hdinsight-storm-develop-java-topology.md)

* [Développement de topologies C# pour Storm sur HDInsight à l'aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Pour obtenir plus d'exemples Storm pour HDinsight :

* [Exemples Storm sur HDInsight](https://github.com/hdinsight/hdinsight-storm-examples)

* [Analyse des données de capteur de hub d'événements avec Storm sur HDInsight](hdinsight-storm-sensor-data-analysis.md)
<!--HONumber=47-->
