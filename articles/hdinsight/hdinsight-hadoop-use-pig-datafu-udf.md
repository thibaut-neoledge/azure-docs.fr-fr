<properties
pageTitle="Utilisation de DataFu avec Pig sur HDInsight"
description="DataFu est une collection de bibliothèques à utiliser avec Hadoop. Découvrez comment vous pouvez utiliser DataFu avec Pig sur votre cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#Utilisation de DataFu avec Pig dans HDInsight

DataFu est un ensemble de bibliothèques Open Source à utiliser avec Hadoop. Dans ce document, vous apprendrez comment utiliser DataFu dans votre cluster HDInsight et comment utiliser les fonctions définies par l'utilisateur (UDF) DataFu avec Pig.

##Composants requis

* Un abonnement Azure.

* Un cluster Azure HDInsight (Linux ou Windows)

* Des connaissances élémentaire de [l'utilisation de Pig dans HDInsight](hdinsight-use-pig.md)

##Installation de DataFu dans HDInsight basé sur Linux

> [AZURE.NOTE] DataFu est installé sur les clusters basés sur Linux 3.3 et versions ultérieures, ainsi que sur les clusters Windows. Il n’est pas installé sur les clusters Linux avant la version 3.3.
>
> Si vous utilisez un cluster basé sur Linux 3.3 ou une version ultérieure, ou un cluster Windows, vous pouvez ignorer cette section.

DataFu peut être téléchargé et installé à partir du référentiel Maven. Procédez comme suit pour ajouter DataFu à votre cluster HDInsight :

1. Connectez-vous à votre cluster HDInsight Linux en utilisant SSH. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez l’un des articles suivants :

    * [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Utilisez la commande suivante pour télécharger le fichier jar DataFu à l'aide de l'utilitaire wget, ou copiez et collez le lien dans votre navigateur pour commencer le téléchargement.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Ensuite, chargez le fichier du stockage par défaut pour votre cluster HDInsight. Le fichier est alors disponible pour tous les nœuds du cluster, et le fichier reste stocké même si vous supprimez et recréez le cluster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] L'exemple ci-dessus stocke le fichier jar dans `wasbs:///example/jars`, car ce répertoire existe déjà sur le stockage en cluster. Vous pouvez utiliser n'importe quel emplacement dans le stockage en cluster HDInsight.

##Utilisation de DataFu avec Pig

Les étapes de cette section supposent que vous êtes familiarisé avec l'utilisation de Pig dans HDInsight et indiquent uniquement les instructions Pig Latin et non les étapes décrivant leur utilisation avec le cluster. Pour plus d'informations sur l'utilisation de Pig avec HDInsight, consultez la rubrique [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Lorsque vous utilisez DataFu à partir de Pig dans un cluster HDInsight basé sur Linux, vous devez tout d'abord enregistrer le fichier jar à l'aide de l'instruction Pig Latin suivante :
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu est enregistré par défaut dans des clusters HDInsight basés sur Windows.

Vous définissez généralement un alias pour les fonctions DataFu. Par exemple :

    DEFINE SHA datafu.pig.hash.SHA();
    
Cette instruction définit un alias nommé `SHA` pour la fonction de hachage SHA. Vous pouvez ensuite l'utiliser dans un script Pig Latin pour générer un hachage pour les données d'entrée. Par exemple, le texte suivant remplace les noms dans les données d'entrée avec une valeur de hachage :

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

S'il est utilisé avec les données d'entrée suivantes :

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Le résultat obtenu sera le suivant :

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##Étapes suivantes

Pour plus d'informations sur DataFu ou Pig, consultez les documents suivants :

* [Guide Apache DataFu Pig](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0914_2016-->