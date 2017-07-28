---
title: "Créer une application Java HBase pour Azure HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser Apache Maven pour créer une application Java Apache HBase, puis la déployer dans un cluster Azure HDInsight basé sur Windows."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f4a4e02-45ab-40dd-842b-3ec034f256c9
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 83aeb23275a86e5d4706a0ad7ea72d11d71f9604
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop) sous Windows
Apprenez à créer et à générer une application [Apache HBase](http://hbase.apache.org/) dans Java à l'aide d'Apache Maven. Utilisez ensuite l'application avec Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) est un outil de gestion de projets logiciels et d'inclusion qui vous permet de créer des logiciels, de la documentation et des rapports pour les projets Java. Dans cet article, vous apprendrez à l’utiliser de façon à créer une application Java de base permettant de créer ou de supprimer une table HBase dans un cluster Azure HDInsight, ainsi que de lui envoyer des requêtes.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Windows. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Suppression de HDInsight sous Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Configuration requise
* [Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou version supérieure
* [Maven](http://maven.apache.org/)
* Un cluster HDInsight sous Windows avec HBase

    > [!NOTE]
    > Les étapes décrites dans ce document ont été testées avec des versions de cluster HDInsight 3.2 et 3.3. Les valeurs par défaut fournies dans les exemples concernent un cluster HDInsight 3.3.

## <a name="create-the-project"></a>Création du projet
1. À partir de la ligne de commande de votre environnement de développement, modifiez les répertoires pour indiquer l’emplacement auquel vous souhaitez créer le projet, par exemple `cd code\hdinsight`.
2. Utilisez la commande **mvn** , installée avec Maven, pour générer la structure du projet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Cette commande permet de créer un répertoire dans le répertoire actuel avec le nom spécifié par le paramètre **artifactID** (**hbaseapp** dans cet exemple.) Le répertoire contient les éléments suivants :

   * **pom.xml** : le modèle d’objet du projet ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contient les informations et la configuration utilisées pour générer le projet.
   * **src** : répertoire contenant le répertoire **main\java\com\microsoft\examples**, dans lequel vous créerez l’application.
3. Supprimez le fichier **src\test\java\com\microsoft\examples\apptest.java**, car il ne sera pas utilisé dans cet exemple.

## <a name="update-the-project-object-model"></a>Mise à jour du modèle d'objet du projet
1. Modifiez le fichier **pom.xml** et ajoutez le code suivant dans la section `<dependencies>` :

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Cette section indique à Maven que le projet nécessite **hbase-client** version **1.1.2**. Au moment de la compilation, cette dépendance est téléchargée à partir du référentiel Maven par défaut. Vous pouvez utiliser la [Recherche du référentiel central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) pour en savoir plus sur cette dépendance.

   > [!IMPORTANT]
   > Le numéro de version doit correspondre à la version de HBase fournie avec votre cluster HDInsight. Utilisez le tableau suivant pour trouver le numéro de version correct.
   >
   >

   | Version de cluster HDInsight | Version HBase à utiliser |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    Pour plus d’informations sur les versions et composants HDInsight, voir [Quels sont les différents composants Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md)
2. Si vous utilisez un cluster HDInsight 3.3, vous devez également ajouter le code suivant à la section `<dependencies>` :

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    Cette dépendance charge les composants de base phoenix utilisés par HBase version 1.1.x.
3. Ajoutez le code suivant au fichier **pom.xml**. Cette section doit être contenue entre les balises `<project>...</project>` dans le fichier, par exemple entre `</dependencies>` et `</project>`.

        <build>
          <sourceDirectory>src</sourceDirectory>
          <resources>
              <resource>
                <directory>${basedir}/conf</directory>
                <filtering>false</filtering>
                <includes>
                  <include>hbase-site.xml</include>
                </includes>
              </resource>
            </resources>
          <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                </configuration>
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
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                </execution>
              </executions>
            </plugin>
          </plugins>
        </build>

    La section `<resources>` configure une ressource (**conf\hbase-site.xml**) qui contient des informations de configuration pour HBase.

   > [!NOTE]
   > Vous pouvez également définir les valeurs de configuration par code. Consultez les commentaires de l’exemple **CreateTable** ci-dessous pour la marche à suivre.
   >
   >

    La section `<plugins>` configure également les plug-ins [Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) et [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/). Le plug-in compiler est utilisé pour compiler la topologie. Le plug-in shade est utilisé pour empêcher la duplication de licence dans le package JAR généré par Maven. Il est utilisé, car les fichiers de licence dupliqués entraînent une erreur à l'exécution sur le cluster HDInsight. Le fait d'utiliser le plug-in maven-shade-plugin avec l'implémentation `ApacheLicenseResourceTransformer` permet d'éviter cette erreur.

    Le plug-in maven-shade-plugin produit également un uber jar (ou fat jar) contenant toutes les dépendances requises par l’application.
4. Enregistrez le fichier **pom.xml** .
5. Créez un répertoire appelé **conf** dans le répertoire **hbaseapp**. Dans le répertoire **conf**, créez un fichier nommé **hbase-site.xml**. Utilisez les données suivantes comme contenu du fichier :

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Ce fichier sera utilisé pour charger la configuration HBase pour un cluster HDInsight.

   > [!NOTE]
   > Il s’agit d’un fichier hbase-site.xml de base contenant les paramètres minimaux pour le cluster HDInsight.

6. Enregistrez le fichier **hbase-site.xml**.

## <a name="create-the-application"></a>Création de l'application
1. Accédez au répertoire **hbaseapp\src\main\java\com\microsoft\examples** et renommez le fichier app.java en **CreateTable.java**.
2. Ouvrez le fichier **CreateTable.java** et remplacez le contenu existant par le code suivant :

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;
        import org.apache.hadoop.hbase.HTableDescriptor;
        import org.apache.hadoop.hbase.TableName;
        import org.apache.hadoop.hbase.HColumnDescriptor;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Put;
        import org.apache.hadoop.hbase.util.Bytes;

        public class CreateTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Example of setting zookeeper values for HDInsight
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

            // create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // create the table...
            HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
            // ... with two column families
            tableDescriptor.addFamily(new HColumnDescriptor("name"));
            tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
            admin.createTable(tableDescriptor);

            // define some people
            String[][] people = {
                { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
                { "2", "Franklin", "Holtz", "franklin@contoso.com" },
                { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
                { "4", "Rae", "Schroeder", "rae@contoso.com" },
                { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
                { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

            HTable table = new HTable(config, "people");

            // Add each person to the table
            //   Use the `name` column family for the name
            //   Use the `contactinfo` column family for the email
            for (int i = 0; i< people.length; i++) {
              Put person = new Put(Bytes.toBytes(people[i][0]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
              person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
              person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
              table.put(person);
            }
            // flush commits and close the table
            table.flushCommits();
            table.close();
          }
        }

    Il s’agit de la classe **CreateTable**, qui crée une table appelée **people** et la remplit avec des utilisateurs prédéfinis.
3. Enregistrez le fichier **CreateTable.java**.
4. Dans le répertoire **hbaseapp\src\main\java\com\microsoft\examples**, créez un fichier nommé **SearchByEmail.java**. Utilisez le code suivant comme contenu de ce fichier :

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HTable;
        import org.apache.hadoop.hbase.client.Scan;
        import org.apache.hadoop.hbase.client.ResultScanner;
        import org.apache.hadoop.hbase.client.Result;
        import org.apache.hadoop.hbase.filter.RegexStringComparator;
        import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
        import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
        import org.apache.hadoop.hbase.util.Bytes;
        import org.apache.hadoop.util.GenericOptionsParser;

        public class SearchByEmail {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Use GenericOptionsParser to get only the parameters to the class
            // and not all the parameters passed (when using WebHCat for example)
            String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
            if (otherArgs.length != 1) {
              System.out.println("usage: [regular expression]");
              System.exit(-1);
            }

            // Open the table
            HTable table = new HTable(config, "people");

            // Define the family and qualifiers to be used
            byte[] contactFamily = Bytes.toBytes("contactinfo");
            byte[] emailQualifier = Bytes.toBytes("email");
            byte[] nameFamily = Bytes.toBytes("name");
            byte[] firstNameQualifier = Bytes.toBytes("first");
            byte[] lastNameQualifier = Bytes.toBytes("last");

            // Create a new regex filter
            RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
            // Attach the regex filter to a filter
            //   for the email column
            SingleColumnValueFilter filter = new SingleColumnValueFilter(
              contactFamily,
              emailQualifier,
              CompareOp.EQUAL,
              emailFilter
            );

            // Create a scan and set the filter
            Scan scan = new Scan();
            scan.setFilter(filter);

            // Get the results
            ResultScanner results = table.getScanner(scan);
            // Iterate over results and print  values
            for (Result result : results ) {
              String id = new String(result.getRow());
              byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
              String firstName = new String(firstNameObj);
              byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
              String lastName = new String(lastNameObj);
              System.out.println(firstName + " " + lastName + " - ID: " + id);
              byte[] emailObj = result.getValue(contactFamily, emailQualifier);
              String email = new String(emailObj);
              System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
            }
            results.close();
            table.close();
          }
        }

    La classe **SearchByEmail** peut être utilisée pour lancer des requêtes sur les lignes d’adresses électroniques. Puisqu'elle utilise un filtre d'expression régulière, vous pouvez fournir une chaîne ou une expression régulière lorsque vous utilisez la classe.
5. Enregistrez le fichier **SearchByEmail.java**.
6. Dans le répertoire **hbaseapp\src\main\hava\com\microsoft\examples**, créez un fichier nommé **DeleteTable.java**. Utilisez le code suivant comme contenu de ce fichier :

        package com.microsoft.examples;
        import java.io.IOException;

        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.hbase.HBaseConfiguration;
        import org.apache.hadoop.hbase.client.HBaseAdmin;

        public class DeleteTable {
          public static void main(String[] args) throws IOException {
            Configuration config = HBaseConfiguration.create();

            // Create an admin object using the config
            HBaseAdmin admin = new HBaseAdmin(config);

            // Disable, and then delete the table
            admin.disableTable("people");
            admin.deleteTable("people");
          }
        }

    Cette classe permet de nettoyer cet exemple en désactivant puis en supprimant la table créée par la classe **CreateTable**.
7. Enregistrez le fichier **DeleteTable.java**.

## <a name="build-and-package-the-application"></a>Génération et package de l'application
1. Ouvrez une invite de commandes et passez au répertoire **hbaseapp**.
2. Utilisez la commande suivante pour générer un fichier JAR contenant l'application :

        mvn clean package

    Cela nettoie les artefacts de build précédents, télécharge toute dépendance non encore installée, puis génère et met l'application en package.
3. Une fois la commande exécutée, le répertoire **hbaseapp\target** contient un fichier appelé **hbaseapp-1.0-SNAPSHOT.jar**.

   > [!NOTE]
   > Le fichier **hbaseapp-1.0-SNAPSHOT.jar** est un uber jar (parfois appelé fat jar) contenant toutes les dépendances requises pour exécuter l’application.

## <a name="upload-the-jar-file-and-start-a-job"></a>Téléchargement du fichier JAR et démarrage d'une tâche
Il existe de nombreuses façons de télécharger un fichier vers votre cluster HDInsight, comme décrit dans la rubrique [Téléchargement de données pour les tâches Hadoop dans HDInsight](hdinsight-upload-data.md). Les étapes suivantes utilisent Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Après avoir installé et configuré Azure PowerShell, créez un fichier appelé **hbase-runner.psm1**. Utilisez les données suivantes comme contenu de ce fichier :

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"

        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"

        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>

        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,

        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,

        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,

        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get the login for the HDInsight cluster
        $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

        # The JAR
        $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex

        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds `
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -HttpCredential $creds
        }

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>

        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,

                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,

                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,

                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )

            Set-StrictMode -Version 3

            # Is the Azure module installed?
            FindAzure

            # Get authentication for the cluster
            $creds=Get-Credential

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }

        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}

            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Ce fichier contient deux modules :

   * **Add-HDInsightFile** permet de télécharger des fichiers vers HDInsight
   * **Start-HBaseExample** permet d’exécuter les classes créées antérieurement
2. Enregistrez le fichier **hbase-runner.psm1**.
3. Ouvrez une nouvelle fenêtre Azure PowerShell, accédez au répertoire **hbaseapp**, puis exécutez la commande ci-après.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Remplacez le chemin d’accès par l’emplacement du fichier **hbase-runner.psm1** créé plus tôt. Cela enregistrera le module pour cette session Azure PowerShell.
4. Utilisez la commande ci-après pour télécharger le fichier **hbaseapp-1.0-SNAPSHOT.jar** sur votre cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight. Cette commande télécharge le fichier **hbaseapp-1.0-SNAPSHOT.jar** vers le répertoire **example/jars** du stockage principal de votre cluster HDInsight.
5. Une fois les fichiers téléchargés, utilisez le code suivant pour créer une table avec **hbaseapp** :

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight.

    Cette commande crée une table appelée **people** sur votre cluster HDInsight. Cette commande n'affiche aucune sortie dans la fenêtre de console.
6. Pour rechercher des entrées dans la table, utilisez la commande suivante :

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight.

    Cette commande utilise la classe **SearchByEmail** pour rechercher les lignes dans lesquelles la famille de colonnes **contactinformation** et la colonne **email** contiennent la chaîne **contoso.com**. Les résultats suivants doivent s'afficher :

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    L’utilisation de **fabrikam.com** pour la valeur `-emailRegex` renvoie les utilisateurs dont le champ email contient **fabrikam.com**. Cette recherche étant implémentée à l’aide d’un filtre d’expression régulière, vous pouvez également saisir des expressions régulières telles que **^r**, qui renvoie les entrées dont le champ email commence par la lettre r.

## <a name="delete-the-table"></a>Suppression de la table
Lorsque vous avez terminé, utilisez la commande ci-après dans la session Azure PowerShell pour supprimer la table **people** utilisée dans l’exemple :

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight.

## <a name="troubleshooting"></a>Résolution des problèmes
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Aucun résultat ou résultat inattendu lors de l'utilisation de Start-HBaseExample
Utilisez le paramètre `-showErr` pour afficher l’erreur standard (STDERR) produite lors de l'exécution de la tâche.

