<properties
   pageTitle="Utilisation de Hadoop Pig avec SSH sur un cluster HDInsight | Microsoft Azure"
   description="Découvrez comment se connecter à un cluster Hadoop Linux avec SSH, avant d’utiliser la commande Pig pour exécuter interactivement des instructions Pig Latin ou avec le traitement par lots."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>


#<a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-(ssh)"></a>Exécution de tâches Pig sur un cluster Linux avec la commande Pig (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document vous guidera lors du processus de connexion à un cluster HDInsight Azure basé sur Linux à l’aide de Secure Shell (SSH), puis lors de l’utilisation de la commande Pig pour exécuter des instructions Pig Latin interactivement ou en tant que traitement par lots.

Le langage de programmation Pig Latin permet de décrire les transformations appliquées aux données d’entrée pour produire le résultat souhaité.

> [AZURE.NOTE] Si vous connaissez déjà l’utilisation de serveurs Hadoop basés sur Linux, mais pas HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md).

##<a name="<a-id="prereq"></a>prerequisites"></a><a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster HDInsight sous Linux (Hadoop sur HDInsight).

* Un client SSH. Mac OS, Linux et Unix doivent être accompagnés d’un client SSH. Les utilisateurs Windows doivent télécharger un client, comme [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a name="<a-id="ssh"></a>connect-with-ssh"></a><a id="ssh"></a>Connexion avec SSH

Connectez-vous au nom de domaine complet de votre cluster HDInsight à l’aide de la commande SSH. Le nom de domaine complet est le nom attribué au cluster, suivi de **.azurehdinsight.net**. Par exemple, la commande suivante permettrait de se connecter à un cluster nommé **myhdinsight**.

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez spécifier l’emplacement de la clé privée sur votre système client.

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez la rubrique [Utilisation de SSH avec Hadoop dans HDInsight sur Linux à partir de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-(windows-based-clients)"></a>PuTTY (clients Windows)

Windows ne fournit pas de client SSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, consultez la rubrique [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a name="<a-id="pig"></a>use-the-pig-command"></a><a id="pig"></a>Utilisation de la commande Pig

2. Une fois connecté, lancez l’interface de ligne de commande Pig à l’aide de la commande suivante.

        pig

    Après quelques instants, vous devriez voir une invite `grunt>` .

3. Entrez l’instruction suivante :

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Cette commande charge le contenu du fichier sample.log dans les JOURNAUX. Vous pouvez afficher le contenu du fichier à l’aide de la commande suivante.

        DUMP LOGS;

4. Transformez ensuite les données en appliquant une expression régulière pour extraire uniquement le niveau de journalisation de chaque enregistrement à l’aide de la commande suivante.

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Vous pouvez utiliser **DUMP** pour afficher les données après la transformation. Dans ce cas, utilisez `DUMP LEVELS;`.

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
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Crée un jeu de données qui contient chaque valeur unique au niveau journal et le nombre de fois où elle se produit. Ces informations sont stockées dans FREQUENCIES.</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Trie les niveaux du journal par décompte (décroissant) et stocke ces informations dans RESULT.</td>
    </tr>
    </table>

6. Vous pouvez également enregistrer les résultats d’une transformation à l’aide de l’instruction `STORE` . Par exemple, ce qui suit enregistre `RESULT` dans le répertoire **/example/data/pigout** sur le conteneur de stockage par défaut de votre cluster.

        STORE RESULT into 'wasbs:///example/data/pigout';

    > [AZURE.NOTE] Les données sont stockées dans le répertoire spécifié dans des fichiers nommés **part-nnnnn**. Si le répertoire existe déjà, vous recevrez un message d’erreur.

7. Pour quitter l’invite Grunt, entrez l’instruction suivante.

        QUIT;

###<a name="pig-latin-batch-files"></a>Fichiers de commandes Pig Latin

Vous pouvez également utiliser la commande Pig pour exécuter le Pig Latin contenu dans un fichier.

3. Après avoir quitté l’invite Grunt, utilisez la commande suivante pour canaliser STDIN dans un fichier nommé **pigbatch.pig**. Ce fichier sera créé dans le répertoire de base pour le compte avec lequel vous êtes connecté à la session SSH.

        cat > ~/pigbatch.pig

4. Tapez ou collez les lignes suivantes, puis utilisez Ctrl + D lorsque vous avez terminé.

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utilisez les éléments suivants pour exécuter le fichier **pigbatch.pig** à l’aide de la commande Pig.

        pig ~/pigbatch.pig

    Une fois le traitement par lots terminé, vous devez voir la sortie suivante, qui doit être la même que lorsque vous avez utilisé `DUMP RESULT;` lors des étapes précédentes.

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a name="<a-id="summary"></a>summary"></a><a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande Pig vous permet d’exécuter interactivement des opérations MapReduce à l’aide de Pig Latin, ainsi que des instructions stockées dans un fichier de commandes.

##<a name="<a-id="nextsteps"></a>next-steps"></a><a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Pig sur HDInsight.

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)



<!--HONumber=Oct16_HO2-->


