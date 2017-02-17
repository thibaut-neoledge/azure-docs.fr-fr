---
title: "Développer des tâches MapReduce Scalding avec Maven | Microsoft Docs"
description: "Découvrez comment utiliser Maven pour créer une tâche MapReduce Scalding, puis comment déployer et exécuter la tâche dans un cluster Hadoop sous HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 26a4d4e8-2623-4fae-a0ca-17792b7a5713
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: f3be777497d842f019c1904ec1990bd1f1213ba2
ms.openlocfilehash: 166ff7f3586932494984e87fc0df7d3d3d914c82


---
# <a name="develop-scalding-mapreduce-jobs-with-apache-hadoop-on-hdinsight"></a>Développer des tâches MapReduce Scalding avec Apache Hadoop dans HDInsight
Scalding est une bibliothèque Scala qui permet de créer facilement des tâches MapReduce Hadoop. Scalding offre une syntaxe concise, ainsi qu'une étroite intégration à Scala.

Dans ce document, découvrez comment utiliser Maven pour créer une tâche de comptage de mots MapReduce de base écrite en Scalding. Vous découvrirez ensuite comment déployer et exécuter cette tâche dans un cluster HDInsight.

## <a name="prerequisites"></a>Conditions préalables
* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Cluster HDInsight basé sur Windows ou Linux dans Hadoop**. Pour plus d’informations, consultez la page [Approvisionnement de clusters Hadoop Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md) ou [Approvisionnement de clusters Hadoop Windows dans HDInsight](hdinsight-provision-clusters.md).

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* **[Maven](http://maven.apache.org/)**

* **[Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou version ultérieure**

## <a name="create-and-build-the-project"></a>Créer et générer le projet

1. Utilisez la commande suivante pour créer un projet Maven :
   
        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false
   
    Cette commande crée un répertoire nommé **scaldingwordcount**et la structure d'une application Scala.

2. Dans le répertoire **scaldingwordcount**, ouvrez le fichier **pom.xml** et remplacez le contenu par le code suivant :
   
        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
            <modelVersion>4.0.0</modelVersion>
            <groupId>com.microsoft.example</groupId>
            <artifactId>scaldingwordcount</artifactId>
            <version>1.0-SNAPSHOT</version>
            <name>${project.artifactId}</name>
            <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
            </properties>
            <repositories>
            <repository>
                <id>conjars</id>
                <url>http://conjars.org/repo</url>
            </repository>
            <repository>
                <id>maven-central</id>
                <url>http://repo1.maven.org/maven2</url>
            </repository>
            </repositories>
            <dependencies>
            <dependency>
                <groupId>com.twitter</groupId>
                <artifactId>scalding-core_2.11</artifactId>
                <version>0.13.1</version>
            </dependency>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-core</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
            </dependencies>
            <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
                <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                    <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                        <goal>add-source</goal>
                        <goal>compile</goal>
                    </goals>
                    </execution>
                </executions>
                </plugin>
                <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                    </transformers>
                    <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                    <configuration>
                        <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                            <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                        </transformers>
                    </configuration>
                    </execution>
                </executions>
                </plugin>
            </plugins>
            </build>
        </project>
   
    Ce fichier décrit le projet, les dépendances et les plug-ins. En voici les principales entrées :
   
   * **maven.compiler.source** et **maven.compiler.target** : définissent la version Java du projet.

   * **repositories** : référentiels contenant les fichiers de dépendance utilisés par le projet.

   * **scalding-core_2.11** et **hadoop-core** : ce projet dépend des packages principaux Scalding et Hadoop.

   * **maven-scala-plug-in** : plug-in pour compiler des applications scala.

   * **maven-shade-plug-in**: plug-in pour créer des fichiers jar « shaded (fat) ». Ce plug-in applique des filtres et des transformations, en particulier :
     
     * **filters**: les filtres appliqués modifient les métadonnées incluses dans le fichier jar. Pour éviter les exceptions de signature au moment de l’exécution, cela exclut les différents fichiers de signature qui peuvent être inclus avec les dépendances.

     * **executions** : la configuration d’exécution de la phase du package spécifie la classe **com.twitter.scalding.Tool** comme classe principale pour le package. Sans cela, vous devez spécifier com.twitter.scalding.Tool, ainsi que la classe qui contient la logique d’application, lors de l’exécution de la tâche avec la commande hadoop.
    
3. Supprimez le répertoire **src/test** , car vous n'allez pas créer de tests dans cet exemple.

4. Ouvrez le fichier **src/main/scala/com/microsoft/example/App.scala** et remplacez le contenu par le code suivant :
   
        package com.microsoft.example
   
        import com.twitter.scalding._
   
        class WordCount(args : Args) extends Job(args) {
            // 1. Read lines from the specified input location
            // 2. Extract individual words from each line
            // 3. Group words and count them
            // 4. Write output to the specified output location
            TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))
   
            //Tokenizer to split sentance into words
            def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
            }
        }
   
    Cela permet d'implémenter une tâche de comptage simple.

5. Enregistrez et fermez les fichiers.

6. Utilisez la commande suivante dans le répertoire **scaldingwordcount** pour générer et empaqueter l'application :
   
        mvn package
   
    Une fois cette tâche terminée, le package qui contient l'application WordCount se trouve dans **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## <a name="run-the-job-on-a-linux-based-cluster"></a>Exécuter la tâche dans un cluster Linux

> [!NOTE]
> Les étapes suivantes utilisent SSH et la commande Hadoop. Pour d'autres méthodes d'exécution des tâches MapReduce, consultez [Utilisation de MapReduce dans Hadoop sous HDInsight](hdinsight-use-mapreduce.md).


1. Utilisez la commande suivante pour télécharger le package dans votre cluster HDInsight :
   
        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:
   
    De cette façon, les fichiers du système local sont copiés dans le nœud principal.
   
   > [!NOTE]
   > Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d'accès à la clé privée. Par exemple, `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`
   > 
   > 
2. Utilisez la commande suivante pour vous connecter au nœud principal du cluster :
   
        ssh username@clustername-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d'accès à la clé privée. Par exemple, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Une fois connecté au nœud principal, utilisez la commande suivante pour exécuter le travail de comptage de mots
   
        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout
   
    Cette commande exécute la classe WordCount implémentée précédemment. `--hdfs` indique à la tâche d'utiliser HDFS. `--input` spécifie le fichier texte d’entrée, tandis que `--output` spécifie l’emplacement de sortie.
4. Une fois la tâche terminée, utilisez la commande suivante pour afficher la sortie.
   
        hdfs dfs -text wasbs:///example/wordcountout/*
   
    Des informations similaires à celles qui suivent s'affichent :
   
        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="run-the-job-on-a-windows-based-cluster"></a>Exécuter la tâche dans un cluster Windows

Les étapes suivantes utilisent Windows PowerShell. Pour d'autres méthodes d'exécution des tâches MapReduce, consultez [Utilisation de MapReduce dans Hadoop sous HDInsight](hdinsight-use-mapreduce.md).

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Démarrer Azure PowerShell et se connecter à votre compte Azure. Une fois que vous avez entré vos informations d'identification, la commande retourne les informations relatives à votre compte.
   
        Add-AzureRMAccount
   
        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...

2. Si vous avez plusieurs abonnements, fournissez l’ID d’abonnement que vous souhaitez utiliser pour le déploiement.
   
        Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>
   
   > [!NOTE]
   > Vous pouvez utiliser `Get-AzureRMSubscription` pour obtenir la liste de tous les abonnements associés à votre compte, y compris l’ID d’abonnement de chacun d’eux.

3. Utilisez le script suivant pour télécharger et exécuter la tâche de comptage de mots. Remplacez `CLUSTERNAME` par le nom de votre cluster HDInsight, puis assurez-vous que `$fileToUpload` est le chemin correct du fichier **scaldingwordcount-1.0-SNAPSHOT.jar**.
   
   ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"
    $fileToUpload = Read-Host -Prompt "Enter the path to the scaldingwordcount-1.0-SNAPSHOT.jar file"
    $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"

    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    Set-AzureStorageBlobContent `
        -File $fileToUpload `
        -Blob $blobPath `
        -Container $container `
        -Context $context

    #Create a job definition and start the job
    $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
        -JobName ScaldingWordCount `
        -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
        -ClassName com.microsoft.example.WordCount `
        -arguments "--hdfs", `
                    "--input", `
                    "wasbs:///example/data/gutenberg/davinci.txt", `
                    "--output", `
                    "wasbs:///example/wordcountout"
    $job = Start-AzureRmHDInsightJob `
        -clustername $clusterName `
        -jobdefinition $jobDef `
        -HttpCredential $creds
    Write-Output "Job ID is: $job.JobId"
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    #Download the output of the job
    Get-AzureStorageBlobContent `
        -Blob example/wordcountout/part-00000 `
        -Container $container `
        -Destination output.txt `
        -Context $context
    #Log any errors that occured
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
   ```
   
     Quand vous exécutez le script, vous devez entrer le nom d’utilisateur et le mot de passe administrateur pour votre cluster HDInsight. Les erreurs qui surviennent éventuellement pendant l’exécution de la tâche sont enregistrées dans la console.

4. Une fois la tâche terminée, la sortie est téléchargée vers le fichier **output.txt** dans le répertoire actif. Utilisez la commande suivante pour afficher les résultats.
   
        cat output.txt
   
    Le fichier doit contenir des valeurs similaires à ce qui suit :
   
        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez vu comment utiliser Scalding pour créer des tâches MapReduce pour HDInsight, utilisez les liens suivants pour découvrir d'autres façons d'utiliser Azure HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Jan17_HO3-->


