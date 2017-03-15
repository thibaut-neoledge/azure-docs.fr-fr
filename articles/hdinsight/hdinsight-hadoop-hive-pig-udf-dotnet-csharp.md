---
title: Utilisation de C# avec Hive et Pig sur Hadoop dans HDInsight | Microsoft Docs
description: "Découvrez comment utiliser des fonctions définies par l’utilisateur C# avec la diffusion en continu Hive et Pig dans Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 257138fddc75b39985ba974b1314e978a554b1e2
ms.lasthandoff: 03/07/2017


---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Utilisation des fonctions définies par l’utilisateur C# avec la diffusion en continu Hive et Pig dans HDInsight

Les langages Hive et Pig sont parfaits pour traiter des données dans Azure HDInsight, mais vous avez parfois besoin d’un langage plus généraliste. Hive et Pig vous permettent d’appeler le code externe via des fonctions définies par l’utilisateur ou la diffusion en continu.

Dans ce document, découvrez comment utiliser C# avec Hive et Pig.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Windows comme système d’exploitation. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Composants requis

* Windows 7 ou version ultérieure.

* Visual Studio avec les versions suivantes :

  * Visual Studio 2012 Professional/Premium/Ultimate avec [mise à jour 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 Community/Professional/Premium/Ultimate avec [mise à jour 4](https://www.microsoft.com/download/details.aspx?id=44921)

  * Visual Studio 2015

  * Visual Studio 2017

* Hadoop sur un cluster HDInsight - Consultez la page [Approvisionnement d’un cluster HDInsight](hdinsight-provision-clusters.md) pour connaître les étapes de création d’un cluster

* Outils Hadoop pour Visual Studio ou Data Lake Tools pour Visual Studio. Consultez la page [Prise en main des outils HDInsight Hadoop pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour connaître les étapes d’installation et de configuration des outils.

    > [!NOTE]
    > Les outils Data Lake Tools sont installés dans Visual Studio 2017 en sélectionnant la charge de travail __Développement Azure__ lors de l’installation.

## <a name="net-on-hdinsight"></a>.NET sur HDInsight

Le Common Language Runtime .NET (CLR) et les infrastructures sont installés par défaut sur les clusters HDInsight basés sur Windows. Le CLR .NET vous permet d’utiliser des applications C# avec une diffusion en continu Hive et Pig (données transmises entre Hive/Pig et l’application C# via stdout/stdin).

> [!NOTE]
> Il n’existe actuellement aucune prise en charge pour l’exécution des fonctions définies par l’utilisateur .NET Framework sur les clusters HDInsight basés sur Linux.


## <a name="net-and-streaming"></a>.NET et diffusion en continu

La diffusion en continu exige que Hive et Pig transmettent des données à une application externe via stdout et reçoivent les résultats via stdin. Pour les applications C#, utilisez `Console.ReadLine()` et `Console.WriteLine()`.

Étant donné que Hive et Pig doivent appeler l’application au moment de l’exécution, le modèle **Application console (.NET Framework)** doit être utilisé pour vos projets C#.

## <a name="hive-and-c35"></a>Hive et C&#35;

### <a name="create-the-c-project"></a>Création du projet C#

1. Ouvrez Visual Studio et créez une solution. Pour le type de projet, sélectionnez **Application console (.NET Framework)**, puis nommez le nouveau projet **HiveCSharp**.

2. Remplacez le contenu de **Program.cs** par le code suivant :

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Créez le projet.

### <a name="upload-to-storage"></a>Téléchargement vers le stockage

1. Dans Visual Studio, ouvrez l' **Explorateur de serveurs**.

2. Développez **Azure**, puis **HDInsight**.

3. Si vous y êtes invité, entrez les informations d’identification de votre abonnement Azure, puis cliquez sur **Se connecter**.

4. Développez le cluster HDInsight dans lequel vous souhaitez déployer cette application, puis **Compte de stockage par défaut**.

    ![Explorateur de serveurs affichant le compte de stockage pour le cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

5. Double-cliquez sur **Conteneur par défaut** pour le cluster. Cette opération permet d’afficher le contenu du conteneur par défaut.
6. Cliquez sur l’icône de téléchargement, puis accédez au dossier **bin\debug** du projet **HiveCSharp**. Enfin, sélectionnez le fichier **HiveCSharp.exe** et cliquez sur **Ok**.

    ![icône télécharger](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

7. Une fois le chargement terminé, vous pouvez utiliser l’application à partir d’une requête Hive.

### <a name="hive-query"></a>Requête Hive

1. Dans Visual Studio, ouvrez l' **Explorateur de serveurs**.

2. Développez **Azure**, puis **HDInsight**.

3. Cliquez avec le bouton droit sur le cluster dans lequel vous avez déployé l’application **HiveCSharp**, puis sélectionnez **Écrire une requête Hive**.

4. Pour la requête Hive, utilisez le texte suivant :

    ```hiveql
    add file wasbs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    Cette requête permet de sélectionner les champs `clientid`, `devicemake` et `devicemodel` dans `hivesampletable`, puis de les transmettre à l’application HiveCSharp.exe. La requête s’attend à ce que l’application renvoie les trois champs, qui sont stockés en tant que `clientid`, `phoneLabel` et `phoneHash`. Elle s’attend également à trouver HiveCSharp.exe à la racine du conteneur de stockage par défaut (`add file wasbs:///HiveCSharp.exe`).

5. Cliquez sur **Envoyer** pour envoyer la tâche au cluster HDInsight. La fenêtre **Résumé de la tâche Hive** s’ouvre.

6. Cliquez sur **Actualiser** pour actualiser le résumé jusqu’à ce que **État du travail** soit défini sur **Terminé**. Pour afficher le résultat de la tâche, cliquez sur **Sortie de la tâche**.

## <a name="pig-and-c35"></a>Pig et C&#35;

### <a name="create-the-c-project"></a>Création du projet C#

1. Ouvrez Visual Studio et créez une solution. Pour le type de projet, sélectionnez **Application console**, puis nommez le nouveau projet **PigUDF**.

2. Remplacez le contenu du fichier **Program.cs** par le code suivant :

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Cette application analyse les lignes envoyées à partir de Pig et reformate les lignes qui commencent par `java.lang.Exception`.

3. Enregistrez **Program.cs**, puis générez le projet.

### <a name="upload-the-application"></a>Téléchargement de l’application

1. La diffusion en continu Pig s’attend à ce que l’application soit locale sur le système de fichiers de cluster. Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous à lui en suivant les instructions fournies dans [Connexion à des clusters HDInsight avec RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. Une fois connecté, copiez **PigUDF.exe** du répertoire **bin/debug** vers le projet PigUDF sur votre ordinateur local, puis collez-le dans le répertoire **%PIG_HOME%** sur le cluster.

### <a name="use-the-application-from-pig-latin"></a>Utilisation de l’application à partir de Pig Latin

1. À partir de la session Bureau à distance, démarrez la **ligne de commande Hadoop** en cliquant sur l’icône correspondante sur le bureau.

2. Utilisez ce qui suit pour lancer la ligne de commande Pig :

        cd %PIG_HOME%
        bin\pig

    Une invite `grunt>` s’affiche.
    
3. Entrez la commande suivante pour exécuter une tâche Pig à l’aide de l’application .NET Framework :

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    L’instruction `DEFINE` crée l’alias `streamer` pour les applications pigudf.exe et `SHIP` le distribue entre les nœuds du cluster. Plus tard, `streamer` est utilisé avec l’opérateur `STREAM` pour traiter les lignes uniques contenues dans le journal et renvoyer les données sous forme de colonnes.

    > [!NOTE]
    > Le nom d’application utilisé pour la diffusion en continu doit être entouré du caractère \` (accent grave) s’il s’agit d’un alias ou du caractère ’ (apostrophe) en cas d’utilisation avec `SHIP`.

4. Après avoir entré la dernière ligne, la tâche doit démarrer. Elle retourne un résultat semblable au texte suivant :

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="summary"></a>Résumé

Dans ce document, vous avez appris à utiliser une application .NET Framework à partir de Hive et Pig sur HDInsight. Si vous souhaitez apprendre à utiliser Python avec Hive et Pig, consultez la page [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md).

Pour connaître d’autres façons d’utiliser Pig et Hive et pour en savoir plus sur l’utilisation de MapReduce, consultez les documents suivants :

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

