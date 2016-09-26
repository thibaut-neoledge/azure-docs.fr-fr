<properties
	pageTitle="Création d’une application HBase à l’aide de Maven et Java, puis déploiement sur HDInsight basé sur Linux | Microsoft Azure"
	description="Découvrez comment utiliser Apache Maven pour créer une application Java Apache HBase, puis la déployer dans HDInsight Linux dans le cloud Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="larryfr"/>

#Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight (Hadoop) sous Linux

Apprenez à créer et à générer une application [Apache HBase](http://hbase.apache.org/) dans Java à l'aide d'Apache Maven. Puis utilisez l’application avec un cluster HDInsight Linux.

[Maven](http://maven.apache.org/) est un outil de gestion de projets logiciels et d'inclusion qui vous permet de créer des logiciels, de la documentation et des rapports pour les projets Java. Dans cet article, vous apprendrez à l’utiliser de façon à créer une application Java de base permettant de créer ou de supprimer une table HBase dans un cluster HDInsight Linux, ainsi que de lui envoyer des requêtes.

> [AZURE.NOTE] Les étapes présentes dans ce document supposent que vous utilisez un cluster HDInsight Linux. Pour plus d’informations sur l’utilisation d’un cluster HDInsight basé sur Windows, voir [Utilisation de Maven pour créer des applications Java utilisant HBase avec HDInsight sous Windows](hdinsight-hbase-build-java-maven.md)

##Configuration requise

* [Java platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou version supérieure

* [Maven](http://maven.apache.org/)

* [Un cluster Azure HDInsight sous Linux avec HBase](../hdinsight-hbase-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] Les étapes décrites dans ce document ont été testées avec des versions de cluster HDInsight 3.2, 3.3 et 3.4. Les valeurs par défaut fournies dans les exemples concernent un cluster HDInsight 3.4.

* **Des connaissances en SSH et SCP**. Pour plus d’informations sur l’utilisation de SSH et SCP avec HDInsight, consultez les articles suivants :

    * **Clients Linux, Unix ou OS X** : consultez la page [Utilisation de SSH avec Hadoop dans HDInsight sous Linux à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Clients Windows** : consultez la page [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##Création du projet

1. À partir de la ligne de commande de votre environnement de développement, modifiez les répertoires vers l'emplacement sur lequel vous souhaitez créer le projet, par exemple `cd code/hdinsight`.

2. Utilisez la commande __mvn__, installée avec Maven, pour générer la structure du projet.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Cela crée un répertoire dans le répertoire actuel avec le nom spécifié par le paramètre __artifactID__ (**hbaseapp** dans cet exemple.) Ce répertoire contiendra les éléments ci-après :

	* __pom.xml__ : le modèle d'objet du projet ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) contient les informations et la configuration utilisées pour générer le projet.

	* __src__ : répertoire contenant le répertoire __main\\java\\com\\microsoft\\examples__, dans lequel vous créerez l’application.

3. Supprimez le fichier __src\\test\\java\\com\\microsoft\\examples\\apptest.java__, car il ne sera pas utilisé dans cet exemple.

##Mise à jour du modèle d'objet du projet

1. Modifiez le fichier __pom.xml__ et ajoutez le code suivant dans la section `<dependencies>` :

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

	Cela indique à Maven que le projet nécessite __hbase-client__ version __1.1.2__. Au moment de la compilation, il sera téléchargé à partir du référentiel Maven par défaut. Vous pouvez utiliser la [Recherche du référentiel central Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) pour en savoir plus sur cette dépendance.

    > [AZURE.IMPORTANT] Le numéro de version doit correspondre à la version de HBase fournie avec votre cluster HDInsight. Utilisez le tableau suivant pour trouver le numéro de version correct.

    | Version de cluster HDInsight | Version HBase à utiliser |
    | ----- | ----- |
    | 3\.2 | 0\.98.4-hadoop2 |
    | 3\.3 et 3.4 | 1\.1.2 |

    Pour plus d’informations sur les versions et composants HDInsight, voir [Quels sont les différents composants Hadoop disponibles avec HDInsight ?](hdinsight-component-versioning.md)

2. Si vous utilisez un cluster HDInsight 3.3 ou 3.4, vous devez également ajouter le code suivant à la section `<dependencies>` :

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Cette opération charge les composants de base phoenix nécessaires à Hbase version 1.1.x.

2. Ajoutez le code suivant au fichier __pom.xml__. Il doit être contenu entre les balises `<project>...</project>` dans le fichier, par exemple entre `</dependencies>` et `</project>`.

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

	Cela configure une ressource (__conf\\hbase-site.xml__,) qui contient des informations de configuration pour HBase.

	> [AZURE.NOTE] Vous pouvez également définir les valeurs de configuration par code. Consultez les commentaires de l'exemple __CreateTable__ ci-dessous pour la marche à suivre.

	Cela configure également les plug-ins [Maven Compiler Plugin](http://maven.apache.org/plugins/maven-compiler-plugin/) et [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/). Le plug-in compiler est utilisé pour compiler la topologie. Le plug-in shade est utilisé pour empêcher la duplication de licence dans le package JAR généré par Maven. Il est utilisé, car les fichiers de licence dupliqués entraînent une erreur à l'exécution sur le cluster HDInsight. Le fait d'utiliser le plug-in maven-shade-plugin avec l'implémentation `ApacheLicenseResourceTransformer` permet d'éviter cette erreur.

	Le plug-in maven-shade-plugin produit également un uber jar (ou fat jar) contenant toutes les dépendances requises par l'application.

3. Enregistrez le fichier __pom.xml__.

4. Créez un répertoire appelé __conf__ dans le répertoire __hbaseapp__. Ceci sera utilisé pour conserver les informations de configuration pour la connexion à HBase.

5. Utilisez la commande suivante pour copier la configuration de HBase à partir du serveur de HDInsight vers le répertoire __conf__. Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

		scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

	> [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous serez invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa` :
	>
	> `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##Création de l'application

1. Accédez au répertoire __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ et renommez le fichier app.java en __CreateTable.java__.

2. Ouvrez le fichier __CreateTable.java__ et remplacez le contenu existant par les données suivantes :

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
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

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

	Il s'agit de la classe __CreateTable__, qui crée une table appelée __people__ et la remplit avec des utilisateurs prédéfinis.

3. Enregistrez le fichier __CreateTable.java__.

4. Dans le répertoire __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__, créez un fichier nommé __SearchByEmail.java__. Utilisez les données suivantes comme contenu de ce fichier :

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

	La classe __SearchByEmail__ peut être utilisée pour lancer des requêtes sur les lignes d'adresses électroniques. Puisqu'elle utilise un filtre d'expression régulière, vous pouvez fournir une chaîne ou une expression régulière lorsque vous utilisez la classe.

5. Enregistrez le fichier __SearchByEmail.java__.

6. Dans le répertoire __hbaseapp\\src\\main\\hava\\com\\microsoft\\examples__, créez un fichier nommé __DeleteTable.java__. Utilisez les données suivantes comme contenu de ce fichier :

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

	Cette classe permet de nettoyer cet exemple en désactivant puis en supprimant la table créée par la classe __CreateTable__.

7. Enregistrez le fichier __DeleteTable.java__.

##Génération et package de l'application

2. Utilisez la commande suivante à partir du répertoire __hbaseapp__ pour générer un fichier JAR contenant l’application :

		mvn clean package

	Cela nettoie les artefacts de build précédents, télécharge toute dépendance non encore installée, puis génère et met l'application en package.

3. Une fois la commande exécutée, le répertoire __hbaseapp\\target__ contient un fichier appelé __hbaseapp-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] Le fichier __hbaseapp-1.0-SNAPSHOT.jar__ est un uber jar (parfois appelé fat jar) contenant toutes les dépendances requises pour exécuter l'application.

##Téléchargement du fichier JAR et exécution de tâches

1. Utilisez la commande suivante pour télécharger le fichier jar dans le cluster HDInsight : Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

		scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

	Ceci télécharge le fichier dans le répertoire de base de votre compte d’utilisateur SSH.

	> [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous serez invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa` :
	>
	> `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Utilisez SSH pour vous connecter au cluster HDInsight. Remplacez **USERNAME** par le nom de votre connexion SSH. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight :

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	> [AZURE.NOTE] Si vous avez utilisé un mot de passe pour votre compte SSH, vous serez invité à le saisir. Si vous avez utilisé une clé SSH avec le compte, vous devrez peut-être utiliser le paramètre `-i` pour spécifier le chemin d’accès au fichier de clé. L’exemple suivant charge la clé privée à partir de `~/.ssh/id_rsa` :
	>
	> `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Une fois connecté, utilisez ce qui suit pour créer une nouvelle table HBase à l’aide de l’application Java :

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

	Ceci crée une nouvelle table HBase nommée __people__ en la remplissant avec des données.

4. Ensuite, utilisez ce qui suit pour rechercher des adresses de messagerie stockées dans la table :

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

	Les résultats suivants doivent s'afficher :

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

##Suppression de la table

Lorsque vous avez terminé, utilisez la commande ci-après dans la session Azure PowerShell pour supprimer la table __people__ utilisée dans l'exemple :

	hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

<!---HONumber=AcomDC_0914_2016-->