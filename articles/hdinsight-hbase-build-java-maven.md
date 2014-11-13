<properties title="Cr&eacute;ation d'une application HBase &agrave; l'aide de Maven" pageTitle="Cr&eacute;ation d'une application HBase &agrave; l'aide de Maven" description="D&eacute;couvrez comment utiliser Apache Maven pour cr&eacute;er une application Apache HBase bas&eacute;e sur Java, puis la d&eacute;ployer dans Azure HDInsight" metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="larryfr" />

## Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop)

Apprenez à créer et à générer une application [Apache HBase][Apache HBase] dans Java à l'aide d'Apache Maven. Utilisez ensuite l'application avec Azure HDInsight (Hadoop).

[Maven][Maven] est un outil de gestion de projets logiciels et d'inclusion qui vous permet de créer des logiciels, de la documentation et des rapports pour les projets Java. Dans cet article, vous apprendrez à l'utiliser de façon à créer une application Java de base permettant de créer ou de supprimer une table HBase dans un cluster Azure HDInsight, ainsi que de lui envoyer des requêtes.

## Configuration requise

-   [Java platform JDK][Java platform JDK] 7 ou supérieur

-   [Maven][Maven]

-   [Un cluster Azure HDInsight avec HBase][Un cluster Azure HDInsight avec HBase]

## Création du projet

1.  À partir de la ligne de commande de votre environnement de développement, modifiez les répertoires vers l'emplacement auquel vous souhaitez créer le projet. Par exemple, `cd code\hdinsight`

2.  Utilisez la commande **mvn**, installée avec Maven, pour générer la structure du projet.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Cela créera un répertoire dans le répertoire actuel avec le nom spécifié par le paramètre **artifactID** (**hbaseapp** dans cet exemple.) Ce répertoire contiendra les éléments ci-après.

    -   **pom.xml** - le modèle d'objet du projet ([POM][POM]) contient les informations et la configuration utilisées pour générer le projet.

    -   **src** - répertoire contenant le répertoire **main\\java\\com\\microsoft\\examples**, dans lequel vous créerez l'application.

3.  Supprimez le fichier **src\\test\\java\\com\\microsoft\\examples\\apptest.java**, car il ne sera pas utilisé dans cet exemple.

## Mise à jour du modèle d'objet du projet

1.  Modifiez le fichier **pom.xml** et ajoutez l'élément suivant dans la section `<dependencies>`.

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    Cela indique à Maven que le projet nécessite **hbase-client** version **0.98.4-hadoop2**. Au moment de la compilation, il sera téléchargé à partir du référentiel Maven par défaut. Vous pouvez utiliser la [recherche du référentiel Maven][recherche du référentiel Maven] pour afficher plus d'informations sur cette dépendance.

2.  Ajoutez le code suivant au fichier **pom.xml**. Il doit être contenu entre les balises `<project>...</project>` dans le fichier, par exemple entre `</dependencies>` et `</project>`.

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

    Cela configure une ressource (**conf\\hbase-site.xml**,) qui contient des informations de configuration pour HBase.

    > [WACOM.NOTE] Vous pouvez également définir les valeurs de configuration par code. Consultez les commentaires de l'exemple **CreateTable** ci-dessous pour la marche à suivre.

    Cela configure également le [maven-shade-plugin][maven-shade-plugin], qui est utilisé pour empêcher la duplication de licence dans le JAR généré par Maven. Il est utilisé, car les fichiers de licence dupliqués entraînent une erreur à l'exécution sur le cluster HDInsight. Le fait d'utiliser le maven-shade-plugin avec l'implémentation `ApacheLicenseResourceTransformer` permet d'éviter cette erreur.

    Le maven-shade-plugin produit également un uberjar (ou fatjar) contenant toutes les dépendances requises par l'application.

3.  Enregistrez le fichier **pom.xml**.

4.  Créez un répertoire appelé **conf** dans le répertoire **hbaseapp**. Dans le répertoire **conf**, créez un fichier appelé **hbase-site.xml** et utilisez les éléments suivants comme contenu.

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

    > [WACOM.NOTE] Il s'agit d'un fichier hbase-site.xml très basique contenant les paramètres minimum pour le cluster HDInsight. Pour une version complète du fichier de configuration hbase-site.xml utilisé par HDInsight, [activez le Bureau à distance dans le cluster][activez le Bureau à distance dans le cluster]. Le fichier hbase-site.xml est situé dans le répertoire C:\\apps\\dist\\hbase-\<numéro de version\>-hadoop2\\conf. La portion du chemin relative au numéro de version changera lors de la mise à jour de HBase sur le cluster.

5.  Enregistrez le fichier **hbase-site.xml**.

## Création de l'application

1.  Allez dans le répertoire **hbaseapp\\src\\com\\microsoft\\examples** et renommez le fichier app.java\_\_ en **CreateTable.java**.

2.  Ouvrez le fichier **CreateTable.java** et remplacez le contenu existant par les données suivantes.

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
            //   in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");

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

    Il s'agit de la classe **CreateTable**, qui crée une table appelée **people** et la remplit avec des utilisateurs prédéfinis.

3.  Enregistrez le fichier **CreateTable.java**.

4.  Dans le répertoire **hbaseapp\\com\\microsoft\\examples**, créez un fichier appelé **SearchByEmail.java**. Utilisez les données suivantes comme contenu de ce fichier.

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

    La classe **SearchByEmail** peut être utilisée pour lancer des requêtes sur les lignes d'adresses électroniques. Puisqu'elle utilise un filtre d'expression régulière, vous pouvez fournir une chaîne ou une expression régulière lorsque vous utilisez la classe.

5.  Enregistrez le fichier **SearchByEmail.java**.

6.  Dans le répertoire **hbaseapp\\com\\microsoft\\examples**, créez un fichier appelé **DeleteTable.java**. Utilisez les données suivantes comme contenu de ce fichier.

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

    Cette classe permet uniquement de nettoyer cet exemple en désactivant puis en supprimant la table créée par la classe **CreateTable**.

7.  Enregistrez le fichier **CreateTable.java**.

## Génération et package de l'application

1.  Ouvrez une invite de commandes et passez au répertoire **hbaseapp**.

2.  Utilisez la commande ci-après pour générer un JAR contenant l'application.

        mvn clean package

    Cela nettoiera les artefacts de build précédents, téléchargera toute dépendance non encore installée, puis générera et mettra l'application en package.

3.  Une fois la commande exécutée, le répertoire **hbaseapp\\target** contient un fichier appelé **hbaseapp-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] Le fichier **hbaseapp-1.0-SNAPSHOT.jar** est un uberjar (parfois appelé fatjar) contenant toutes les dépendances requises pour exécuter l'application.

## Téléchargement du JAR et démarrage d'une tâche

> [WACOM.NOTE] Il existe de nombreuses façons de télécharger un fichier vers votre cluster HDInsight, comme décrit dans la rubrique [Téléchargement de données pour les tâches Hadoop dans HDInsight][Téléchargement de données pour les tâches Hadoop dans HDInsight]. Les étapes ci-dessous utilisent [Azure PowerShell][Azure PowerShell].

1.  Après avoir installé et configuré [Azure PowerShell][Azure PowerShell], créez un fichier appelé **hbase-runner.psm1**. Utilisez les données suivantes comme contenu de ce fichier.

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

            # The JAR
            $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

            # The job definition
            $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
              -JarFile $jarFile `
              -ClassName $className `
              -Arguments $emailRegex

            # Get the job output
            $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
            Write-Host "Wait for the job to complete ..." -ForegroundColor Green
            Wait-AzureHDInsightJob -Job $job
            if($showErr)
            {
                Write-Host "STDERR"
                Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
            }
            Write-Host "Display the standard output ..." -ForegroundColor Green
            Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
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

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
                                        -Container $storage.container `
                                        -Context $storage.context
        }

        function FindAzure {
            # Is the Azure module installed?
            if (-not(Get-Module -ListAvailable Azure))
            {
                throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/"
            }

            # Is there an active Azure subscription?
            $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureHDInsightCluster -name $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            # Get the primary storage account information
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
            $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
            # Build the hash of storage account name/keys
            $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
            foreach($account in $hdi.StorageAccounts)
            {
                $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
            }
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $hdi.DefaultStorageAccount.StorageContainerName
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccounts = $storageAccounts

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Ce fichier contient deux modules :

    -   **Add-HDInsightFile** - permet de télécharger des fichiers vers HDInsight

    -   **Start-HBaseExample** - permet d'exécuter les classes créées antérieurement

2.  Enregistrez le fichier **hbase-runner.psm1**.

3.  Ouvrez une nouvelle fenêtre Azure PowerShell, accédez au répertoire **hbaseapp**, puis exécutez la commande ci-après.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Remplacez le chemin d'accès par l'emplacement du fichier **hbase-runner.psm1** créé plus tôt. Cela enregistrera le module pour cette session PowerShell.

4.  Utilisez la commande ci-après pour télécharger le fichier **hbaseapp-1.0-SNAPSHOT.jar** sur votre cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight. Cette commande télécharge le fichier **hbaseapp-1.0-SNAPSHOT.jar** vers le répertoire **example/jars** du stockage principal de votre cluster HDInsight.

5.  Une fois les fichiers téléchargés, utilisez la commande suivante pour créer une table avec **hbaseapp**

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight.

    Cette commande crée une table appelée **people** sur votre cluster HDInsight.

6.  Pour rechercher des entrées dans la table, utilisez la commande suivante.

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight.

    La classe SearchByEmail est alors utilisée pour rechercher les lignes dans lesquelles la famille de colonnes **contactinformation**, colonne **email**, contient la chaîne **contoso.com**. Les résultats suivants doivent s'afficher :

        Rae Schroeder - rae@contoso.com - ID: 032439814
        Franklin Holtz - franklin@contoso.com - ID: 229772110
        Gabriela Ingram - gabriela@contoso.com - ID: 655336201

    L'utilisation de **fabrikam.com** pour la valeur `-emailRegex` renvoie les utilisateurs dont le champ email contient **fabrikam.com**. Cette recherche étant implémentée à l'aide d'un filtre d'expression régulière, vous pouvez également saisir des expressions régulières telles que **^r**, qui renverra les entrées dont le champ email commence par la lettre r.

## Suppression de la table

Lorsque vous avez terminé, utilisez la commande ci-après dans la session PowerShell pour supprimer la table **people** utilisée dans l'exemple.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Remplacez **hdinsightclustername** par le nom de votre cluster HDInsight.

## Résolution des problèmes

### Aucun résultat ou résultat inattendu lors de l'utilisation de **Start-HBaseExample**

Utilisez le code `-showErr` pour afficher le STDERR produit lors de l'exécution de la tâche.

  [Apache HBase]: http://hbase.apache.org/
  [Maven]: http://maven.apache.org/
  [Java platform JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Un cluster Azure HDInsight avec HBase]: /fr-fr/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [recherche du référentiel Maven]: http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [activez le Bureau à distance dans le cluster]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Téléchargement de données pour les tâches Hadoop dans HDInsight]: /fr-fr/documentation/articles/hdinsight-upload-data/
  [Azure PowerShell]: /fr-fr/documentation/articles/install-configure-powershell/
