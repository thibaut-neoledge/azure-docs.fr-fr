---
title: Rubriques tendances Twitter avec Apache Storm sur HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Trident pour créer une topologie Apache Storm qui détermine les rubriques tendances à partir des hashtags sur Twitter."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 63b280ea-5c07-4dc8-a35f-dccf5a96ba93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: d588221586f151319436525c5098b0bb2694e5f9
ms.lasthandoff: 04/17/2017


---
# <a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>Détermination de rubriques tendances Twitter avec Apache Storm dans HDInsight

Découvrez comment utiliser Trident pour créer une topologie Storm qui détermine les rubriques tendances (hashtags) sur Twitter.

Trident est une abstraction de haut niveau qui fournit des outils comme les jointures, les agrégations, le regroupement, les fonctions et les filtres. En outre, Trident ajoute des primitives pour le traitement incrémentiel avec état. L’exemple utilisé dans ce document est une topologie Trident avec un spout et une fonction personnalisés. Il utilise également plusieurs fonctions intégrées fournies par Trident.

## <a name="requirements"></a>Configuration requise

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java et le JDK 1.8</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">Git</a>

* Un compte de développeur Twitter

## <a name="download-the-project"></a>Téléchargez le projet

Utilisez le code suivant pour cloner le projet localement.

    git clone https://github.com/Blackmist/TwitterTrending

## <a name="understanding-the-topology"></a>Présentation de la topologie

Le diagramme suivant présente le flux des données dans cette topologie :

![Topologie](./media/hdinsight-storm-twitter-trending/trident.png)

> [!NOTE]
> Ce diagramme est un affichage très simplifié de la topologie. Plusieurs instances des composants sont réparties entre les nœuds du cluster.


Le code Trident qui implémente la topologie est le suivant :

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

Le code effectue les opérations suivantes :

1. Il crée un flux à partir du spout. Le spout extrait les tweets de Twitter et les filtres selon des mots clés spécifiques (amour, musique et café dans cet exemple).

2. HashtagExtractor, une fonction personnalisée, est utilisée pour extraire les hashtags de chaque tweet. Les hashtags sont transmis au flux de données.

3. Le flux est ensuite regroupé par hashtag et transmis à une agrégation. Elle produit un décompte du nombre de fois où chaque hashtag s’est produit. Ces données sont conservées en mémoire. Enfin, un nouveau flux de données contenant le hashtag et le compte est émis.

4. L’assembly **FirstN** est appliqué pour renvoyer uniquement les 10 premières valeurs, sur la base du champ de compte.

> [!NOTE]
> Pour plus d’informations sur l’utilisation de Trident, consultez la [Présentation de l’API Trident](http://storm.apache.org/releases/current/Trident-API-Overview.html).

### <a name="the-spout"></a>Le spout

Le spout **TwitterSpout** utilise [Twitter4j](http://twitter4j.org/en/) pour extraire des tweets de Twitter. Un filtre est créé pour les mots __aime__, **musique** et **café**. Les tweets (status) entrants qui correspondent au filtre sont stockés dans une file d’attente de blocage liée. Enfin, les éléments sont retirés de la file d’attente et émis dans la topologie.

### <a name="the-hashtagextractor"></a>HashtagExtractor

Pour extraire les hashtags, [getHashtagEntities](http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--) est utilisé pour récupérer tous les hashtags contenus dans le tweet. Ils sont transmis au flux de données.

## <a name="configure-twitter"></a>Configurer Twitter

Procédez comme suit pour inscrire une nouvelle application Twitter et obtenir les informations de jeton consommateur et d’accès nécessaires pour lire à partir de Twitter :

1. Accédez à [Applications Twitter](https://apps.twitter.com) et cliquez sur le bouton **Créer une nouvelle application**. Lorsque vous remplissez le formulaire, laissez le champ **URL de rappel** vide.

2. Lorsque l’application est créée, cliquez sur l'onglet **Clés et jetons d'accès** .

3. Copiez les informations de **Clé du client** et de **Question secrète du client**.

4. En bas de la page, sélectionnez **Créer mon jeton d’accès** si aucun jeton n’existe. Lorsque les jetons sont créés, copiez les informations de **Jeton d’accès** et de **Question secrète du jeton d’accès**.

5. Dans le projet **TwitterSpoutTopology** que vous avez cloné auparavant, ouvrez le fichier **resources/twitter4j.properties**, ajoutez les informations collectées dans les étapes précédentes, puis enregistrez le fichier.

## <a name="build-the-topology"></a>Génération de la topologie

Utilisez le code suivant pour générer le projet :

        cd [directoryname]
        mvn compile

## <a name="test-the-topology"></a>Test de la topologie

Utilisez la commande suivante pour tester la topologie en local :

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

Après le démarrage de la topologie, vous devez voir des informations de débogage contenant les hashtags et les décomptes émis par la topologie. Le résultat doit ressembler au texte suivant :

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez testé la topologie localement, découvrez comment déployer cette topologie : [Déploiement et gestion des topologies Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Les rubriques Storm suivantes peuvent également vous intéresser :

* [Développement de topologies Java pour Storm sur HDInsight à l’aide de Maven](hdinsight-storm-develop-java-topology.md)
* [Développement de topologies C# pour Storm sur HDInsight à l’aide de Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Pour obtenir plus d'exemples Storm pour HDInsight :

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)


