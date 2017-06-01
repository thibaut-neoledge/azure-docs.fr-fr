---
title: Utilisation de Hadoop Pig avec SSH sur un cluster HDInsight | Microsoft Docs
description: "Découvrez comment se connecter à un cluster Hadoop Linux avec SSH, avant d’utiliser la commande Pig pour exécuter interactivement des instructions Pig Latin ou avec le traitement par lots."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 36ede2b1c2964f85c454ffd1a653c90296bb307a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017


---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Exécution de tâches Pig sur un cluster Linux avec la commande Pig (SSH)

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Découvrez comment exécuter interactivement des tâches Pig à partir d’une connexion SSH sur votre cluster HDInsight. Le langage de programmation Pig Latin permet de décrire les transformations appliquées aux données d’entrée pour produire le résultat souhaité.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a id="ssh"></a>Connexion avec SSH

Utilisez SSH pour vous connecter à votre cluster HDInsight. L’exemple suivant se connecte à un cluster nommé **myhdinsight** à l’aide du nom de compte **sshuser** :

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez spécifier l’emplacement de la clé privée sur votre système client.

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, fournissez le mot de passe lorsque vous y êtes invité.

Pour en savoir plus sur l’utilisation de SSH avec HDInsight, voir [Utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Utilisation de la commande Pig

1. Une fois connecté, lancez l’interface de ligne de commande Pig à l’aide de la commande suivante :

        pig

    Après quelques instants, vous devriez voir une invite `grunt>` .

2. Entrez l’instruction suivante :

        LOGS = LOAD '/example/data/sample.log';

    Cette commande charge le contenu du fichier sample.log dans les JOURNAUX. Vous pouvez afficher le contenu du fichier à l’aide de l’instruction suivante :

        DUMP LOGS;

3. Transformez ensuite les données en appliquant une expression régulière pour extraire uniquement le niveau de journalisation de chaque enregistrement à l’aide de l’instruction suivante :

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Vous pouvez utiliser **DUMP** pour afficher les données après la transformation. Dans ce cas, utilisez `DUMP LEVELS;`.

4. Continuez à appliquer des transformations à l’aide des instructions dans le tableau suivant :

    | Instruction Pig Latin | Descriptif de l’instruction |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Supprime les lignes contenant une valeur null pour le niveau de journal et stocke les résultats dans `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Regroupe les lignes par niveau de journal et stocke les résultats dans `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Crée un jeu de données qui contient chaque valeur unique au niveau journal et le nombre de fois où elle se produit. Le jeu de données est stocké dans `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Trie les niveaux du journal par décompte (décroissant) et stocke ces informations dans `RESULT`. |

    > [!TIP]
    > Utilisez `DUMP` pour afficher le résultat de la transformation après chaque étape.

5. Vous pouvez également enregistrer les résultats d’une transformation à l’aide de l’instruction `STORE` . Par exemple, l’instruction qui suit enregistre `RESULT` dans le répertoire `/example/data/pigout` sur le stockage par défaut de votre cluster :

        STORE RESULT into '/example/data/pigout';

   > [!NOTE]
   > Les données sont stockées dans le répertoire spécifié dans des fichiers nommés `part-nnnnn`. Si le répertoire existe déjà, vous recevez un message d’erreur.

6. Pour quitter l’invite Grunt, entrez l’instruction suivante :

        QUIT;

### <a name="pig-latin-batch-files"></a>Fichiers de commandes Pig Latin

Vous pouvez également utiliser la commande Pig pour exécuter le Pig Latin contenu dans un fichier.

1. Après avoir quitté l’invite Grunt, utilisez la commande suivante pour canaliser STDIN dans un fichier nommé `pigbatch.pig`. Ce fichier est créé dans le répertoire de base du compte d’utilisateur SSH.

        cat > ~/pigbatch.pig

2. Tapez ou collez les lignes suivantes, puis utilisez Ctrl + D lorsque vous avez terminé.

        LOGS = LOAD '/example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

3. Utilisez la commande suivante pour exécuter le fichier `pigbatch.pig` à l’aide de la commande Pig.

        pig ~/pigbatch.pig

    Une fois le traitement par lots terminé, vous observez la sortie suivante :

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig sur HDInsight, consultez le document suivant :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres façons de travailler avec Hadoop sur HDInsight, consultez les documents suivants :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

