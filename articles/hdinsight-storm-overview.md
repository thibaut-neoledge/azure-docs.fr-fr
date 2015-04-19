<properties 
	pageTitle="Découvrez Apache Storm dans HDInsight (hadoop)" 
	description="Découvrez comment utiliser Apache Storm dans HDInsight (hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Présentation d'Apache Storm dans HDInsight

## Qu'est-ce Storm ?

[Apache Storm][apachestorm] est un système de calcul open source distribué à tolérance de panne qui permet de traiter des données en temps réel. Les solutions Storm peuvent également permettre un traitement garanti des données, ainsi que la possibilité de relire les données dont le traitement a échoué une première fois.

## Présentation de Storm sur HDInsight

Storm est fourni en tant que cluster géré intégré à l'environnement Azure, où il peut être utilisé dans le cadre d'une solution Azure plus vaste. Par exemple, Storm peut consommer des données à partir de services tels que des files d'attente ServiceBus ou un concentrateur d'événements et utiliser des sites web ou des services cloud pour afficher les données. Vous pouvez également configurer les clusters Storm sur un réseau Azure Virtual Network, afin de réduire la latence de communication avec les autres ressources de ce réseau et sécuriser la communication avec les ressources d'un centre de données privé.

Pour découvrir Storm, consultez la page [Prise en main de Storm dans HDInsight][gettingstarted].

## Mode de gestion des données de Storm sur HDInsight

Un cluster Storm traite des **topologies** au lieu des tâches MapReduce que vous connaissez peut-être si vous utilisez HDInsight ou Hadoop. Un cluster Storm contient deux types de nœuds : les nœuds principaux qui exécutent **Nimbus** et les nœuds de travail qui exécutent **Supervisor**.

* **Nimbus** : tout comme JobTracker dans Hadoop, est responsable de la distribution du code dans le cluster, de l'assignation des tâches aux machines et de la surveillance des défaillances. HDInsight fournit deux nœuds Nimbus, il n'y a donc aucun point de défaillance pour un cluster Storm.

* **Supervisor** : le superviseur de chaque nœud de travail est responsable du démarrage et de l'arrêt des **processus de travail** sur le nœud.

* **Processus de travail** : un processus de travail exécute un sous-ensemble de **topologie**. Une topologie en cours d'exécution est distribuée entre plusieurs processus de travail via le cluster.

* **Topologie** : définit un graphique de calcul traitant des **flux** de données. Contrairement aux tâches MapReduce, les topologies s'exécutent jusqu'à ce que vous les arrêtiez.

* **Flux** : une collection non liée de **tuples**. Les flux sont produits par des **spouts** et des **bolts**, et consommés par les **bolts**.

* **Tuple** : une liste nommée de valeurs entrées de façon dynamique.

* **Spout** : consomme des données à partir d'une source de données et émet un ou plusieurs **flux**.

	> [AZURE.NOTE] Dans bien des cas, les données sont lues à partir d'une file d'attente telle que Kafka, des files d'attente Azure ServiceBus ou des concentrateurs d'événements. Les files d'attente garantissent la conservation des données en cas de panne.

* **Bolt** : consomme des **flux**, traite les **tuples** et peut émettre des **flux**. Les bolts sont également responsables de l'écriture des données sur un emplacement de stockage externe, comme une file d'attente, HDInsight HBase, un objet blob ou un autre magasin de données.

* **Thrift** : Apache Thrift est une infrastructure logicielle permettant un développement de service évolutif interlangage. Il vous permet de développer des services fonctionnant entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk et d'autres langages.

	* **Nimbus** est un service de Thrift et une **topologie** est une définition de Thrift. Il est donc possible de développer des topologies en utilisant de nombreux langages de programmation. 

Pour plus d'informations sur les composants de Storm, consultez le [didacticiel Storm][apachetutorial] sur apache.org.

## Scénarios : Cas d'utilisation de Storm

Voici quelques scénarios courants pour lesquels Apache Storm peut être utile. Pour plus d'informations sur des scénarios réels, consultez la rubrique [Comment les entreprises utilisent-elles Storm ?][poweredby]..

### Analyses en temps réel

Comme Storm traite des flux de données en temps réel, il est idéal pour l'analyse de données impliquant des recherches, des réactions, des événements spécifiques ou des modèles dans les flux de données durant leur arrivée. Par exemple, une topologie Storm peut surveiller les données d'un capteur pour déterminer l'intégrité d'un système et générer des messages texte pour vous alerter si un modèle spécifique survient.

### Extraire, transformer et charger (ETL)

L'ETL peut être vu comme un effet secondaire du traitement Storm. Par exemple, si vous effectuez une détection de fraude via une analyse en temps réel, vous recevez et transformez déjà les données. Vous pouvez également avoir besoin d'un magasin bolt pour stocker les données dans HBase, Hive, ou d'autres magasins de données pour les utiliser lors d'analyses ultérieures.

### RPC distribués

Les RPC distribués constituent un modèle que vous pouvez créer avec Storm. Une demande est transmise à Storm, qui distribue ensuite le calcul entre plusieurs nœuds, puis renvoie un flux de résultat vers le client en attente.

Pour plus d'informations sur les RPC distribués et le DRPCClient fourni avec Storm, consultez la page [RPC distribués](https://storm.incubator.apache.org/documentation/Distributed-RPC.html).

### Apprentissage automatique en ligne

Vous pouvez utiliser Storm avec une solution d'apprentissage automatique préalablement formée par traitement par lot, comme une solution Mahout. Cependant, le modèle de calcul générique et distribué de Storm peut également vous permettre d'utiliser des solutions d'apprentissage automatique basées sur des flux. Par exemple, le [projet SAMOA (Scalable Advanced Massive Online Analysis, Analyse en ligne massive avancée évolutive)][samoa] est une bibliothèque d'apprentissage automatique utilisant le traitement de flux, compatible avec Storm.

## Quels langages de programmation utiliser ?

Le cluster Storm sur HDInsight prend naturellement en charge .NET, Java et Python. Même si Storm [prend en charge d'autres langages](https://storm.incubator.apache.org/about/multi-language.html), vous devrez les installer sur le cluster HDInsight en plus des autres modifications de configuration. 

### .NET

SCP est un projet permettant aux développeurs .NET de concevoir et d'implémenter une topologie (notamment des spouts et des bolts). La prise en charge de SCP est fournie par défaut avec les clusters Storm.

Pour plus d'informations sur le développement avec SCP, consultez la page [Développement d'applications de traitement des données de diffusion avec SCP.NET et C# sur Storm dans HDInsight](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

### Java

La plupart des exemples Java que vous allez rencontrer sont complètement rédigés en Java ou en Trident. Trident est une abstraction globale facilitant le déroulement des tâches telles que les jointures, les agrégations, le regroupement et le filtrage. Cependant, Trident agit sur des lots de tuples, tandis qu'une solution Java brute traitera un flux d'un tuple à la fois.

Pour plus d'informations sur Trident, consultez le [didacticiel Trident](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) sur apache.org.

Pour consulter des exemples de topologies Java brutes et Trident, consultez le répertoire **%storm_home%\contrib\storm-starter** sur votre cluster Storm.

## Quels sont les différents modèles de développement courants ?

### Traitement de message garanti

Storm peut fournir différents niveaux de traitement de message garanti. Par exemple, une application Storm de base peut garantir un traitement Une fois au minimum, tandis que Trident peut garantir un traitement Exactement une.

Pour plus d'informations, consultez la page [Garanties sur le traitement des données](https://storm.apache.org/about/guarantees-data-processing.html) sur apache.org.

### BasicBolt

Le modèle de lecture d'une entrée tuple, qui émet entre zéro et plusieurs tuples, puis le mécanisme d'accusé de réception de l'entrée tuple appliqué dès la fin de la méthode d'exécution, sont si courants que Storm fournit l'interface [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) pour automatiser ce modèle.

### Jointures

Les jonctions de deux flux de données varient selon les applications. Par exemple, vous pouvez joindre chaque tuple provenant de plusieurs flux dans un nouveau flux ou joindre uniquement des lots de tuples pour une fenêtre spécifique. Dans tous les cas, la jointure s'effectue en utilisant [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), une méthode permettant de définir la façon dont les tuples sont acheminés vers les bolts.

Dans l'exemple Java suivant, fieldsGrouping est utilisé pour acheminer les tuples d'origine des composants " 1 ", " 2 " et " 3 " vers le bolt **MyJoiner**.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

### Traitement par lots

Il existe plusieurs façons d'effectuer un traitement par lot. Avec une topologie Java Storm de base, vous pouvez utiliser un simple compteur pour traiter par lot un nombre X de tuples avant de les émettre, ou utiliser un mécanisme de synchronisation interne nommé " tick tuple " pour émettre un lot toutes les X secondes.

Pour consulter un exemple d'utilisation de " tick tuples ", consultez la page [Analyse des données du capteur avec Storm et HDInsight](hdinsight-storm-sensor-data-analysis.md)

Si vous utilisez Trident, ce dernier est basé sur le traitement par lot des tuples.

### Mise en cache

La mise en cache en mémoire est souvent utilisée pour accélérer le traitement, car elle garde en mémoire les éléments les plus utilisés. Comme une topologie est distribuée entre plusieurs nœuds et que plusieurs processus sont menés dans chaque nœud, nous vous conseillons d'utiliser [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) pour être sûr que les tuples contenant des champs utilisés pour la recherche dans le cache sont toujours acheminés vers le même processus. Ceci permet d'éviter la duplication des entrées de cache à travers les processus.

### Diffusion des N premiers

Lorsque votre topologie est basée sur le calcul d'une valeur N 'top', comme les 5 premières tendances sur Twitter, nous vous conseillons de calculer la valeur N premiers en parallèle, puis de fusionner le résultat de ces calculs dans une valeur globale. Pour ce faire, utilisez [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) pour procéder à l'acheminement par champ vers les bolts parallèles, qui partitionne les données par valeur de champ, avant de les acheminer vers un bolt qui détermine la valeur N premiers de façon globale.

L'exemple [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) présente cette méthode.

## Étapes suivantes

* [Prise en main de Storm dans HDInsight][gettingstarted]

* [Analyse des données du capteur avec Storm et HDInsight](hdinsight-storm-sensor-data-analysis.md)

* [Développement d'applications de traitement des données de diffusion avec SCP.NET et C# sur Storm dans HDInsight](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[apachestorm]: https://storm.incubator.apache.org
[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[poweredby]: https://storm.incubator.apache.org/documentation/Powered-By.html
[gettingstarted]: /fr-fr/documentation/articles/hdinsight-storm-getting-started
<!--HONumber=42-->
