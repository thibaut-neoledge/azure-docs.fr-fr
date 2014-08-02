<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="HDInsight SDK" pageTitle="How to use the HDInsight .NET libraries | Azure" metaKeywords="" description="Learn how to get the HDInsight NuGet packages and use them from your .NET application." metaCanonical="" services="hdinsight" documentationCenter="" title="Use the Hadoop .NET SDK with HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Utilisation du Kit de développement logiciel (SDK) .NET Hadoop avec HDInsight
=============================================================================

Le Kit de développement logiciel (SDK) .NET Hadoop fournit des bibliothèques clientes .NET facilitant l'utilisation de Hadoop à partir de .NET. Ce didacticiel montre comment obtenir le Kit de développement logiciel (SDK) .NET Hadoop et l'utiliser pour créer une application simple basée sur .NET qui exécute des requêtes Hive en utilisant le service Azure HDInsight. Sur la base d'un fichier actors.txt, vous allez écrire une application pour trouver les acteurs et actrices qui reçoivent le plus de récompenses.

Pour activer HDInsight, cliquez [ici](https://account.windowsazure.com/PreviewFeatures).

Dans cet article
----------------

-   [Téléchargement et installation du Kit de développement logiciel (SDK) .NET Hadoop](#install)
-   [Préparation pour le didacticiel](#prepare)
-   [Création de l'application](#create)
-   [Exécution de l'application](#run)
-   [Étapes suivantes](#nextsteps)

Téléchargement et installation du Kit de développement logiciel (SDK) .NET Hadoop
---------------------------------------------------------------------------------

Vous pouvez installer la dernière version du Kit de développement logiciel (SDK) sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Ce SDK se compose des éléments suivants :

-   **Bibliothèque MapReduce** : simplifie l'écriture de tâches MapReduce en langages .NET via l'interface de diffusion en continu Hadoop.

-   **Bibliothèque cliente LINQ to Hive** : traduit les requêtes LINQ C\# ou F\# en requêtes HiveQL et les exécute sur le cluster Hadoop. Cette bibliothèque peut également exécuter des requêtes HiveQL arbitraires depuis une application .NET.

-   **Bibliothèque WebClient** : contient les bibliothèques clientes pour *WebHDFS* et *WebHCat*.

    -   **Bibliothèque cliente WebHDFS** : fonctionne avec les fichiers dans HDFS et le stockage d'objets blob Azure.

    -   **Bibliothèque cliente WebHCat** : gère la programmation et l'exécution des tâches dans le cluster HDInsight.

Voici la syntaxe NuGet pour installer les bibliothèques :

    install-package Microsoft.Hadoop.MapReduce
    install-package Microsoft.Hadoop.Hive 
    install-package Microsoft.Hadoop.WebClient 

Ces commandes ajoutent la bibliothèque et les références au projet Visual Studio en cours.

Préparation pour le didacticiel
-------------------------------

Vous devez disposer d'un [abonnement à Azure](http://www.windowsazure.com/en-us/pricing/free-trial/) et d'un [compte de stockage Azure](http://www.windowsazure.com/en-us/manage/services/storage/how-to-create-a-storage-account/) afin de pouvoir passer aux étapes suivantes. Vous devez également connaître le nom de votre compte de stockage Azure et la clé du compte. Pour savoir comment obtenir ces informations, consultez la section *Affichage, copie et régénération de clés d'accès de stockage* de la page [Gestion des comptes de stockage](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

Vous devez également télécharger le fichier Actors.txt utilisé dans ce didacticiel. Procédez comme suit pour télécharger ce fichier dans votre environnement de développement :

1.  Créez un dossier C:\\Tutorials sur votre ordinateur local.

2.  Téléchargez [Actors.txt](http://www.microsoft.com/en-us/download/details.aspx?id=37003) et enregistrez-le dans le dossier C:\\Tutorials.

Création de l'application
-------------------------

Cette section explique comment télécharger des fichiers vers le cluster Hadoop par programme et comment exécuter des tâches Hive via LINQ vers Hive.

1.  Ouvrez Visual Studio 2012.

2.  Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
	</table>

4.  Cliquez sur **OK** pour créer le projet.

5.  Dans le menu **Outils**, cliquez sur **Library Package Manager**, puis sur **Console du Gestionnaire de package**.

6.  Exécutez les commandes suivantes dans la console pour installer les packages.

         install-package Microsoft.Hadoop.Hive 
         install-package Microsoft.Hadoop.WebClient 

    Ces commandes ajoutent des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7.  Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8.  Ajoutez les instructions using suivantes au début du fichier :

         using Microsoft.Hadoop.WebHDFS.Adapters;
         using Microsoft.Hadoop.WebHDFS;
         using Microsoft.Hadoop.Hive;

9.  Dans la fonction Main(), copiez et collez le code suivant :

         // Téléchargez actors.txt vers le stockage d'objets blob
         var asvAccount = [Storage-account-name.blob.core.windows.net];
         var asvKey = [Storage account key];
         var asvContainer = [Container name];
         var localFile = "C:/Tutorials/Actors.txt";
         var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
         var hadoopUserPassword = [Hadoop user password]; // Mot de passe de l'utilisateur du cluster HDInsight
         var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
            
         var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
         var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
            
         Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
         HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
         HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
            
         // Créez une connexion Hive
         var hiveConnection = new HiveConnection(
           new System.Uri(clusterURI),
           hadoopUser, 
           hadoopUserPassword,
           asvAccount, 
           asvKey);
            
         // Supprimez toute table nommée Actors
         // Utile uniquement si vous souhaitez réexécuter cette application
         // et supprimez une table Actors précédente.
         //hiveConnection.GetTable<HiveRow>("Actors").Drop();

         Console.WriteLine("Creating a Hive table named 'Actors'...");
         string command =
           @"CREATE TABLE Actors(
                     MovieId string, 
                     ActorId string,
                     Name string, 
                     AwardsCount int) 
                     row format delimited fields 
                 terminated by ',';";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
         command =
           @"LOAD DATA INPATH 
                 '/user/hadoop/MovieData/Actors.txt'
             OVERWRITE INTO TABLE Actors;";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Performing Hive query...");
         var result = hiveConnection.ExecuteQuery(@"select name, awardscount
                   from actors sort by awardscount desc
                   limit 1");
         result.Wait();

         Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
         Console.WriteLine("\nPress any key to continue.");
         Console.ReadKey();

10. Mettez à jour les constantes dans l'application. Le service Azure HDInsight utilise le stockage d'objets blob Azure comme système de fichiers par défaut. Durant le processus d'approvisionnement HDInsight, un objet blob est désigné comme système de fichiers par défaut. Vous avez la possibilité d'utiliser le conteneur de système de fichiers par défaut ou un conteneur d'un autre stockage d'objets blob. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

    Si vous choisissez d'utiliser le conteneur de système de fichiers par défaut, le nom du compte de stockage, la clé de stockage et le nom du conteneur sont disponibles dans le fichier de configuration *c:\\apps\\dist\\hadoop-1.1.0-SNAPSHOT\\conf\>core-site.xml* en communication à distance avec le cluster. Pour connaître le conteneur utilisé comme système de fichiers par défaut, recherchez *fs.default.name*. Pour connaître le nom de compte de stockage et la clé de compte, recherchez *fs.azure.account.key*.

Exécution de l'application
--------------------------

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher les étapes exécutées par l'application lorsque des données sont téléchargées, stockées dans une table Hive et, finalement, quand une requête est exécutée sur ces données. Une fois le travail de l'application achevé et les résultats de recherche renvoyés, appuyez sur une touche pour arrêter l'application.

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Applications de console")

**Remarque**

L'exécution de chaque étape par l'application peut prendre quelques secondes, voire quelques minutes. La durée du téléchargement du fichier Actors.txt dépend de votre connexion Internet au centre de données Azure. Les autres étapes dépendent de la taille du cluster.

**Remarque**

L'opération LOAD DATA INPATH consiste en un déplacement des données du fichier Actors.txt dans l'espace de noms de système de fichiers contrôlé par Hive. Ceci entraîne la suppression du fichier Actors.txt de l'emplacement de téléchargement. En conséquence, le fichier Actors.txt doit être téléchargé à chaque exécution de l'application.

Pour plus d'informations sur le chargement de données dans Hive, consultez la page [Mise en route de Hive](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations).

Étapes suivantes
----------------

Vous savez désormais créer une application .NET à l'aide du Kit de développement logiciel (SDK) .NET Hadoop. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Utilisation de Pig avec HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilisation de MapReduce avec HDInsight](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

