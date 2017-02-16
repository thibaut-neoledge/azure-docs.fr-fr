---
title: "Utilisation de Hadoop Pig avec le Bureau à distance dans HDInsight | Microsoft Docs"
description: "Apprenez à utiliser la commande Pig pour exécuter les instructions Pig Latin à partir d&quot;une connexion Bureau à distance vers un cluster Windows Hadoop sur HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: 3cf91abf83359f2157d0f8cd53e0b450bfd58d80


---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Exécution de tâches Pig depuis une connexion Bureau à distance
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit une procédure pas à pas de l'utilisation de la commande Pig pour exécuter les instructions Pig Latin à partir d'une connexion Bureau à distance vers un cluster HDInsight Windows. Pig Latin permet de créer des applications MapReduce en décrivant les transformations de données, plutôt que de mapper et de réduire les fonctions.

> [!IMPORTANT]
> Le Bureau à distance n’est disponible que sur les clusters HDInsight qui utilisent Windows comme système d’exploitation. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).
>
> Pour HDInsight 3.4 ou les versions ultérieures, consultez l’article [Utiliser Pig avec HDInsight et SSH](hdinsight-hadoop-use-pig-ssh.md) pour plus d’informations sur l’exécution interactive de travaux Pig directement sur le cluster à partir d’une ligne de commande.

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Configuration requise
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* un cluster HDInsight basé sur Windows (Hadoop sur HDInsight)
* Un ordinateur client avec Windows 10, Windows 8 ou Windows 7

## <a name="a-idconnectaconnect-with-remote-desktop"></a><a id="connect"></a>Connexion avec le Bureau à distance
Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous à lui en suivant les instructions fournies dans [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a name="a-idpigause-the-pig-command"></a><a id="pig"></a>Utilisation de la commande Pig
1. Lorsque vous disposez d’une connexion Bureau à distance, démarrez la **ligne de commande Hadoop** en cliquant sur l’icône sur le bureau.
2. Utilisez ce qui suit pour lancer la commande Pig :

        %pig_home%\bin\pig

    Une invite `grunt>` s’affiche.
3. Entrez l’instruction suivante :

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Cette commande charge le contenu du fichier sample.log dans les JOURNAUX. Vous pouvez afficher le contenu du fichier à l’aide de la commande suivante :

        DUMP LOGS;
4. Transformez ensuite les données en appliquant une expression régulière pour extraire uniquement le niveau de journalisation de chaque enregistrement :

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Vous pouvez utiliser **DUMP** pour afficher les données après la transformation. Dans ce cas, `DUMP LEVELS;`.
5. Continuez à appliquer des transformations à l’aide des instructions suivantes. Utilisez `DUMP` pour afficher le résultat de la transformation après chaque étape.

    <table>
    <tr>
    <th>Instruction</th><th>Résultat</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Supprime les lignes contenant une valeur null pour le niveau de journal et stocke les résultats dans FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Regroupe les lignes par niveau de journal et stocke les résultats dans GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Crée un jeu de données qui contient chaque valeur unique au niveau journal et le nombre de fois où elle se produit. Ces informations sont stockées dans FREQUENCIES</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Trie les niveaux du journal par décompte (décroissant) et stocke ces informations dans RESULT</td>
    </tr>
    </table>
6. Vous pouvez également enregistrer les résultats d’une transformation à l’aide de l’instruction `STORE` . Par exemple, la commande suivante enregistre `RESULT` dans le répertoire **/example/data/pigout** sur le conteneur de stockage par défaut de votre cluster :

        STORE RESULT into 'wasbs:///example/data/pigout'

   > [!NOTE]
   > Les données sont stockées dans le répertoire spécifié dans des fichiers nommés **part-nnnnn**. Si le répertoire existe déjà, vous recevrez un message d’erreur.
   >
   >
7. Pour quitter l’invite Grunt, entrez l’instruction suivante.

        QUIT;

### <a name="pig-latin-batch-files"></a>Fichiers de commandes Pig Latin
Vous pouvez également utiliser la commande Pig pour exécuter le Pig Latin contenu dans un fichier.

1. Après avoir quitté l’invite Grunt, ouvrez le **bloc-notes** et créez un nouveau fichier nommé **pigbatch.pig** dans le répertoire **%PIG_HOME%**.
2. Tapez ou collez les lignes suivantes dans le fichier **pigbatch.pig** , puis enregistrez-le.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Utilisez les éléments suivants pour exécuter le fichier **pigbatch.pig** à l’aide de la commande pig.

        pig %PIG_HOME%\pigbatch.pig

    Une fois le traitement par lots terminé, vous devez voir la sortie suivante, qui doit être la même que lorsque vous avez utilisé `DUMP RESULT;` lors des étapes précédentes :

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Résumé
Comme vous pouvez le voir, la commande Pig vous permet d’exécuter interactivement des opérations MapReduce ou d’exécuter des tâches Pig Latin stockées dans un fichier de commandes.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Pig dans HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)



<!--HONumber=Jan17_HO3-->


