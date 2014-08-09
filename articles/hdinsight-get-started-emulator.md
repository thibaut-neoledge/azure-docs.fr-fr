<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" authors="" />

Prise en main de l'émulateur HDInsight
======================================

Ce didacticiel vous présente l'utilisation de l'émulateur Microsoft HDInsight pour Azure (anciennement HDInsight Server Developer Preview). L'émulateur HDInsight est fourni avec les mêmes composants de l'écosystème Hadoop qu'Azure HDInsight. Pour plus d'informations, notamment sur les versions déployées, consultez la page [Version de Hadoop dans Azure HDInsight](http://www.windowsazure.com/fr-fr/manage/services/hdinsight/howto-hadoop-version/ "Versions et composants HDInsight").

L'émulateur HDInsight fournit un environnement de développement local pour Azure HDInsight. Si vous connaissez Hadoop, vous pouvez prendre en main l'émulateur à l'aide de HDFS. Toutefois, dans HDInsight, le système de fichiers par défaut étant le stockage d'objets blob Azure (WASB, c'est-à-dire Azure Storage - Objets blob), vous pouvez développer vos tâches à l'aide de WASB. Vous pouvez prendre en main le développement sur WASB à l'aide de l'émulateur Azure Storage, probablement en utilisant uniquement un petit sous-ensemble de vos données (aucune modification de configuration dans l'émulateur HDInsight n'est nécessaire, seul un nom de compte de stockage différent est requis). Puis, vous testez vos tâches en local sur Azure Storage, à nouveau en utilisant uniquement un sous-ensemble de vos données (nécessite une modification de configuration dans l'émulateur HDInsight). Enfin, vous voilà prêt à déplacer la partie calculée de votre travail sur HDInsight et à exécuter une tâche sur les données de production.

> [WACOM.NOTE] L'émulateur HDInsight peut uniquement utiliser un déploiement à nœud unique.

Pour suivre un didacticiel sur HDInsight, consultez la page [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/).

**Configuration requise** 

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   L'émulateur HDInsight requiert une version 64 bits de Windows. L'un des systèmes d'exploitation suivants doit être installé :

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack1
    -   Windows 8
    -   Windows Server 2012.
-   Installez et configurez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

Dans ce didacticiel
-------------------

-   [Installation de l'émulateur HDInsight](#install)
-   [Exécution de l'exemple de comptage des mots](#runwordcount)
-   [Exécution des exemples de prise en main](#rungetstartedsamples)
-   [Connexion au stockage d'objets blob Azure](#blobstorage)
-   [Exécution de PowerShell pour HDInsight](#powershell)
-   [Étapes suivantes](#nextsteps)

Installation de l'émulateur HDInsight
-------------------------------------

L'émulateur Microsoft HDInsight peut être installé via Microsoft Web Platform Installer.

> [WACOM.NOTE] Actuellement, l'émulateur HDInsight prend en charge uniquement les systèmes d'exploitation en anglais.

> [WACOM.NOTE] Si vous avez installé Microsoft HDInsight Developer Preview, vous devez d'abord désinstaller les deux composants suivants à partir du Panneau de configuration/Programmes et fonctionnalités.
><ul>
<li>HDInsight Developer Preview</li>
<li>Plateforme de données Hortonworks Developer Preview</li>
</ul>

**Installation de l'émulateur HDInsight**

1.  Ouvrez Internet Explorer, puis accédez à la [page d'installation de l'émulateur Microsoft HDInsight pour Azure](http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT).
2.  Cliquez sur **Installer maintenant**.
3.  Cliquez sur **Exécuter** lorsque vous êtes invité à installer HDINSIGHT.exe en bas de la page.
4.  Cliquez sur le bouton **Oui** dans la fenêtre **Contrôle de compte d'utilisateur** qui s'affiche pour terminer l'installation. Vous devez voir la fenêtre Web Platform Installer 4.6.
5.  Cliquez sur **Installer** en bas de la page.
6.  Cliquez sur **J'accepte** pour accepter les termes du contrat de licence.
7.  Vérifiez que Web Platform Installer affiche **Les produits suivants ont été correctement installés**, puis cliquez sur **Terminer**.
8.  Cliquez sur **Quitter** pour fermer la fenêtre Web Platform Installer 4.6.

    L'installation doit avoir installé trois icônes sur votre Bureau. Celles-ci sont liées comme suit :

    -   **Hadoop Command Line** : invite de commandes Hadoop à partir de laquelle les tâches MapReduce, Pig et Hive sont exécutées dans l'émulateur HDInsight.

    -   **Hadoop Name Node Status** : NameNode gère une arborescence de répertoires pour tous les fichiers dans HDFS. NameNode effectue également le suivi de l'emplacement des données conservées dans un cluster Hadoop pour tous les fichiers. Les clients communiquent avec NameNode pour obtenir l'emplacement de stockage des nœuds de données pour tous les fichiers.

    -   **Hadoop MapReduce Status** : dispositif de suivi des tâches qui alloue des tâches MapReduce à des nœuds dans un cluster.

    L'installation doit également avoir installé plusieurs services locaux. La capture d'écran suivante présente la fenêtre Services :

    ![HDI.Emulator.Services](./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png)

    Pour plus d'informations sur les problèmes connus lors de l'installation et de l'exécution de HDInsight Server, consultez la page [Notes de publication de l'émulateur HDInsight](http://gettingstarted.hadooponazure.com/releaseNotes.html). Le journal d'installation se trouve à l'emplacement **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

Exécution d'une tâche MapReduce de comptage des mots
----------------------------------------------------

Désormais, l'émulateur HDInsight est configuré sur votre station de travail. Vous pouvez exécuter une tâche MapReduce pour tester l'installation. D'abord, vous chargez quelques fichiers texte vers HDFS, puis vous exécutez une tâche MapReduce de comptage des mots pour compter la fréquence des mots dans ces fichiers.

Le programme MapReduce de comptage des mots a été compressé dans le fichier *hadoop-examples.jar*. Celui-ci se situe dans le dossier *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT*.

La syntaxe de la commande jar est la suivante :

    hadoop jar  [mainClass] args...

Vous utilisez aussi les commandes fs. Pour plus d'informations sur les commandes Hadoop, consultez la page [Manuel des commandes Hadoop](http://hadoop.apache.org/docs/r1.1.1/commands_manual.html).

La tâche MapReduce de comptage des mots prend deux arguments : un dossier d'entrée et un dossier de sortie. Vous utilisez *hdfs://localhost/user/HDIUser* comme dossier d'entrée et *hdfs://localhost/user/HDIUser/WordCount\_Output* comme dossier de sortie. Le dossier de sortie ne peut pas être un dossier existant, sinon la tâche MapReduce échoue. Si vous souhaitez exécuter la tâche MapReduce une deuxième fois, vous devez soit spécifier un autre dossier de sortie soit supprimer le dossier de sortie existant.

**Exécution d'une tâche MapReduce de comptage des mots**

1.  À partir du Bureau, double-cliquez sur **Hadoop Command Line** pour ouvrir la fenêtre de ligne de commande Hadoop. Le dossier actif doit être :

         c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    Si ce n'est pas le cas, exécutez la commande suivante :

         cd %hadoop_home%

2.  Exécutez la commande Hadoop suivante afin de créer un dossier HDFS pour le stockage des fichiers d'entrée et de sortie :

         hadoop fs -mkdir /user/HDIUser

3.  Exécutez la commande Hadoop suivante pour copier certains fichiers locaux vers HDFS :

         hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  Exécutez la commande suivante pour répertorier les fichiers du dossier /user/HDIUser :

         hadoop fs -ls /user/HDIUser

    Les fichiers suivants doivent s'afficher :

         c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
         Found 8 items
         -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
         -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
         -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
         -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
         -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
         -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
         -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
         -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  Exécutez la commande suivante pour exécuter la tâche MapReduce de comptage des mots :

         hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  Exécutez la commande suivante pour répertorier les mots comprenant « windows » à partir du fichier de sortie :

         hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    La sortie doit être :

         c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
         rt-r-00000 | findstr "windows"
         windows 12
         windows+java6.  1
         windows.        3

Exécution des exemples de prise en main
---------------------------------------

L'installation de l'émulateur HDInsight fournit quelques exemples offrant un apprentissage rapide des services Apache Hadoop sur Windows aux nouveaux utilisateurs. Ces exemples présentent quelques tâches généralement nécessaires lors du traitement d'un jeu de données volumineux. Passer par les exemples peut vous familiariser avec les concepts associés au modèle de programmation MapReduce et à son écosystème.

Les exemples sont organisés autour de scénarios de traitement des données du journal W3C IIS. Un outil de génération de données est fourni pour créer et importer les ensembles de données de plusieurs tailles vers HDFS ou WASB (stockage d'objets blob Azure). Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure pour HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/). Ensuite, les tâches MapReduce, Pig ou Hive peuvent être exécutées sur les pages de données générées par le script PowerShell. Notez que les scripts Pig et Hive utilisés se compilent tous les deux en programmes MapReduce. Les utilisateurs peuvent exécuter une série de tâches afin d'observer, pour eux-mêmes, les effets de l'utilisation de ces différentes technologies et les effets de la taille des données sur l'exécution des tâches de traitement.

### Dans cette section

-   [Scénarios des données du journal w3c IIS](#scenarios)
-   [Chargement de l'exemple des données du journal w3c](#loaddata)
-   [Exécution de tâches MapReduce en Java](#javamapreduce)
-   [Exécution de tâches Hive](#hive)
-   [Exécution de tâches Pig](#pig)
-   [Régénération des exemples](#rebuild)

### Scénarios des données du journal w3c IIS

Le scénario w3c génère et importe les données du journal W3C IIS dans trois tailles vers HDFS ou WASB : 1 Mo, 500 Mo et 2 Go. Il fournit trois types de tâches et implémente chacune d'elles en C\#, Java, Pig et Hive.

-   **totalhits** : calcule le nombre total de requêtes pour une page donnée
-   **avgtime** : calcule la durée moyenne prise (en secondes) par une requête par page
-   **errors** : calcule le nombre d'erreurs par page, par heure, pour les requêtes avec un état 404 ou 500.

Ces exemples et leur documentation ne fournissent pas une étude approfondie ou une implémentation complète des principales technologies Hadoop. Le cluster utilisé possède seulement un nœud unique, par conséquent, les effets de l'ajout de plusieurs nœuds ne peuvent pas être observés avec cette version.

### Chargement de l'exemple des données du journal W3c

La génération et l'importation des données vers HDFS sont effectuées à l'aide du script PowerShell importdata.ps1.

**Pour importer l'exemple des données du journal w3c :**

1.  Ouvrez la ligne de commande Hadoop à partir du Bureau.
2.  Exécutez la commande suivante pour accéder au répertoire **C:\\Hadoop\\GettingStarted** :

         cd \Hadoop\GettingStarted

3.  Exécutez la commande suivante pour générer et importer les données vers HDFS :

         powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    Si vous souhaitez plutôt charger les données vers WASB, consultez la rubrique [Connexion au stockage d'objets blob Azure](#blobstorage).

4.  Exécutez la commande suivante à partir de la ligne de commande Hadoop pour répertorier les fichiers importés vers HDFS :

         hadoop fs -lsr /w3c

    Le résultat doit ressembler à ce qui suit :

         c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
         -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
         -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
         -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  Exécutez la commande suivante pour afficher un des fichiers de données dans la fenêtre de la console :

         hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Le fichier de données est créé et importé dans HDFS. Vous pouvez exécuter différentes tâches Hadoop.

### Exécution de tâches MapReduce en Java

MapReduce est le moteur de calcul de base de Hadoop. Par défaut, il est implémenté en Java, mais des exemples utilisent aussi .NET et Hadoop Streaming qui utilisent C\#. La syntaxe pour l'exécution d'une tâche MapReduce est la suivante :

    hadoop jar .jar   

Le fichier .jar et les fichiers sources sont situés dans le dossier C:\\Hadoop\\GettingStarted\\Java.

**Exécution d'une tâche MapReduce pour le calcul des accès à une page Web**

1.  Ouvrez la ligne de commande Hadoop.
2.  Exécutez la commande suivante pour accéder au répertoire **C:\\Hadoop\\GettingStarted** :

         cd \Hadoop\GettingStarted

3.  Exécutez la commande suivante pour supprimer le dossier de sortie dans le cas où ce dossier existerait. La tâche MapReduce échoue si le dossier de sortie existe déjà.

         hadoop fs -rmr /w3c/output

4.  Exécutez la commande suivante :

         hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    Le tableau suivant décrit les éléments de la commande :

    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true">Paramètre</td><td data-morhtml="true">Remarque</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">w3c_scenarios.jar</td><td data-morhtml="true">Le fichier .jar se trouve dans le dossier C:\Hadoop\GettingStarted\Java.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">microsoft.hadoop.w3c.TotalHitsForPage</td><td data-morhtml="true">Le type peut être remplacé par un des suivants : 
 <ul data-morhtml="true">
 <li data-morhtml="true">microsoft.hadoop.w3c.AverageTimeTaken</li>
 <li data-morhtml="true">microsoft.hadoop.w3c.ErrorsByPage</li>
 </ul></td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">/w3c/input/small/data_w3c_small.txt</td><td data-morhtml="true">Le fichier d'entrée peut être remplacé par :
 <ul data-morhtml="true">
 <li data-morhtml="true">/w3c/input/medium/data_w3c_medium.txt</li>
 <li data-morhtml="true">/w3c/input/large/data_w3c_large.txt</li>
 </ul></td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">/w3c/output</td><td data-morhtml="true">Il s'agit du nom du dossier de sortie.</td></tr>
 </table>

5.  Exécutez la commande suivante pour afficher le fichier de sortie :

         hadoop fs -cat /w3c/output/part-00000

    Le résultat doit ressembler à ceci :

         c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
         /Default.aspx   3409
         /Info.aspx      1115
         /UserService    1130

    Le nombre d'accès à la page Default.aspx est donc de 3 409, et ainsi de suite.

### Exécution de tâches Hive

Il ne faut pas beaucoup de temps aux analystes expérimentés en SQL pour être à l'aise avec le moteur de requête Hive. Il fournit une interface semblable à celle de SQL et un modèle de base de données relationnelle pour HDFS. Hive utilise un langage nommé HiveQL (ou HQL), qui est un dialecte de SQL.

**Exécution d'une tâche Hive**

1.  Ouvrez la ligne de commande Hadoop.
2.  Accédez au dossier **C:\\Hadoop\\GettingStarted**.
3.  Exécutez la commande suivante pour supprimer le dossier **/w3c/hive/input** s'il existe déjà. La tâche Hive échoue si le dossier existe.

         hadoop fs -rmr /w3c/hive/input

4.  Exécutez la commande suivante pour créer le dossier **/w3c/hive/input** et copier le fichier de données à partir de la station de travail vers HDFS :

         hadoop fs -mkdir /w3c/hive/input
         hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  Exécutez la commande suivante pour exécuter le fichier de script **w3ccreate.hql**. Le script crée une table Hive et charge les données dans la table Hive :

         C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    Voici le script HiveQL :

         DROP TABLE w3c;

         CREATE TABLE w3c(
          logdate string,
          logtime string,
          c_ip string,
          cs_username string,
          s_ip string,
          s_port string,
          cs_method string,
          cs_uri_stem string,
          cs_uri_query string,
          sc_status int,
          sc_bytes int,
          cs_bytes int,
          time_taken int,
          cs_agent string, 
          cs_Referrer string)
         ROW FORMAT delimited
         FIELDS TERMINATED BY ' ';

         LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    Le résultat doit ressembler à ce qui suit :

         c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea    te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
         Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
         Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
         OK
         Time taken: 0.616 seconds
         OK
         Time taken: 0.139 seconds
         Loading data to table default.w3c
         Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
         OK
         Time taken: 0.573 seconds

6.  Exécutez la commande suivante pour exécuter le fichier de script HiveQL **w3ctotalhitsbypate.hql**.

         C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql
    <p>Le tableau suivant décrit les éléments de la commande :</p>
    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true">Fichier</td><td data-morhtml="true">Description</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">C:\Hadoop\hive-0.9.0\bin\hive.cmd</td><td data-morhtml="true">Script de commande Hive.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">C:\Hadoop\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td data-morhtml="true"> Vous pouvez remplacer le fichier de script Hive par l'un des suivants :
 <ul data-morhtml="true">
 <li data-morhtml="true">C:\Hadoop\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
 <li data-morhtml="true">C:\Hadoop\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
 </ul>
 </td></tr>

 </table>

    <p>Le script HiveQL w3ctotalhitsbypage.hql est :</p>

	The w3ctotalhitsbypage.hql HiveQL script is:

		SELECT filtered.cs_uri_stem,COUNT(*) 
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	The end of the output shall be similar to the following:
		
		MapReduce Total cumulative CPU time: 3 seconds 47 msec
		Ended Job = job_201310291309_0006
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
		rite: 53 SUCCESS
		Total MapReduce CPU Time Spent: 3 seconds 47 msec
		OK
		/Default.aspx   3409
		/Info.aspx      1115
		/UserService    1130
		Time taken: 34.68 seconds

<p>Notez qu'en tant que première étape pour chaque tâche, une table est créée et les données sont chargées dans la table à partir du fichier créé précédemment. Vous pouvez parcourir le fichier qui a été créé en regardant sous le nœud /Hive dans HDFS à l'aide de la commande suivante :</p>

    hadoop fs -lsr /apps/hive/

### Exécution de tâches Pig

Le traitement Pig utilise un langage de flux de données, nommé *Pig Latin*. Les abstractions Pig Latin fournissent des structures de données plus détaillées que MapReduce et effectuent pour Hadoop ce que SQL effectue pour les systèmes SGBDR.

**Pour exécuter les tâches Pig :**

1.  Ouvrez la ligne de commande Hadoop.
2.  Accédez au dossier C:\\Hadoop\\GettingStarted.
3.  Exécutez la commande suivante pour soumettre la tâche Pig :

         C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    <p>Le tableau suivant présente les éléments de la commande :</p>
    <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><td data-morhtml="true">Fichier</td><td data-morhtml="true">Description</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd</td><td data-morhtml="true">Script de commande Pig.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">C:\Hadoop\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td data-morhtml="true"> Vous pouvez remplacer le fichier de script Pig Latin par l'un des suivants :
 <ul data-morhtml="true">
 <li data-morhtml="true">C:\Hadoop\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
 <li data-morhtml="true">C:\Hadoop\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
 </ul>
 </td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">/w3c/input/small/data_w3c_small.txt</td><td data-morhtml="true"> Vous pouvez remplacer le paramètre par un fichier plus volumineux :
	
 <ul data-morhtml="true">
 <li data-morhtml="true">/w3c/input/medium/data_w3c_medium.txt</li>
 <li data-morhtml="true">/w3c/input/large/data_w3c_large.txt</li>
 </ul>
	
 </td></tr>
 </table>

    Le résultat doit ressembler à ce qui suit :

         (/Info.aspx,1115)
         (/UserService,1130)
         (/Default.aspx,3409)

Notez que puisque les scripts Pig se compilent en tâches MapReduce, et éventuellement en plusieurs tâches de ce genre, il se peut que les utilisateurs voient plusieurs tâches MapReduce s'exécuter lors du traitement d'une tâche Pig.

### Régénération des exemples

Les exemples contiennent actuellement tous les fichiers binaires requis, donc la génération n'est pas nécessaire. Si vous souhaitez effectuer des modifications sur les exemples Java ou .NET, vous pouvez les régénérer à l'aide de msbuild ou du script PowerShell inclus.

**Régénération des exemples**

1.  Ouvrez la ligne de commande Hadoop.
2.  Exécutez la commande suivante :

         powershell -F buildsamples.ps1

Connexion au stockage d'objets blob Azure
-----------------------------------------

Azure HDInsight utilise le stockage d'objets blob Azure comme système de fichiers par défaut. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

Il est possible de configurer un cluster local dans l'émulateur HDInsight pour utiliser le stockage d'objets blob Azure plutôt que le stockage local. Cette section traite des éléments suivants :

-   connexion à l'émulateur de stockage ;
-   connexion à un stockage d'objets blob Azure ;
-   configuration d'un stockage d'objets blob Azure comme système de fichiers par défaut pour l'émulateur HDInsight.

### Connexion à l'émulateur de stockage

L'émulateur de stockage Azure est fourni avec le [Kit de développement logiciel (SDK) Azure pour .NET](http://www.windowsazure.com/fr-fr/downloads/). L'émulateur de stockage ne démarre pas automatiquement. Vous devez le démarrer manuellement. Le nom de l'application est *Azure Storage Emulator*. Pour démarrer ou arrêter les émulateurs, cliquez avec le bouton droit sur l'icône Azure bleue dans la zone de notification de Windows, puis cliquez sur Show Storage Emulator UI.

> [WACOM.NOTE] Vous pouvez obtenir le message d'erreur suivant lorsque vous démarrez l'émulateur de stockage :

> ``` {}
> Le processus ne peut pas accéder au fichier, car il est utilisé par un autre processus.
> ```

> Ceci est dû au fait qu'un des services Hadoop Hive utilise également le port 10 000. Pour contourner le problème, suivez la procédure suivante :

><ol>
<li>Arrêtez les deux services Hadoop Hive à l'aide de services.msc : Apache Hadoop hiveserver et Apache Hadoop Hiveserver2.</li>
<li>Démarrez l'émulateur de stockage d'objets blob.</li>
<li>Redémarrez les deux services Hadoop Hive. </li>
</ol>

La syntaxe pour accéder à l'émulateur de stockage est la suivante :

	wasb://<ContainerName>@storageemulator

Par exemple :

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] Si vous obtenez le message d'erreur suivant :

> ``` {}
> ls: No FileSystem for scheme: wasb
> ```

> Ceci est dû au fait que vous utilisez encore la version Developer Preview. Suivez les instructions se trouvant dans la section Installation de l'émulateur HDInsight de cet article pour désinstaller la version developer preview, puis réinstallez l'application.

### Connexion au stockage d'objets blob Azure

Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](/en-us/manage/services/storage/how-to-create-a-storage-account/).

**Création d'un conteneur**

1.  Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).
2.  Cliquez sur **STORAGE** à gauche. Vous devez voir une liste des comptes de stockage sous votre abonnement.
3.  Cliquez sur le compte de stockage dans lequel vous souhaitez créer le conteneur dans la liste.
4.  Cliquez sur **CONTAINERS** en haut de la page.
5.  Cliquez sur **ADD** en bas de la page.
6.  Entrez **NAME** et sélectionnez **ACCESS**. Vous pouvez utiliser un des trois niveaux d'accès. Le niveau par défaut est **Private**.
7.  Cliquez sur **OK** pour enregistrer les modifications. Vous devez voir le nouveau conteneur répertorié sur le portail.

Avant de pouvoir accéder à un compte de stockage Azure, vous devez ajouter le nom de compte et la clé de compte au fichier de configuration.

**Configuration de la connexion à un compte de stockage Azure**

1.  Ouvrez **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** dans le Bloc-notes.
2.  Ajoutez la balise suivante à la suite des autres balises  :

         
		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

    Vous devez remplacer et par les valeurs qui correspondent aux informations de votre compte de stockage.

3.  Enregistrez la modification. Il n'est pas nécessaire de redémarrer les services Hadoop.

Utilisez la syntaxe suivante pour accéder au compte de stockage :

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Par exemple :

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### Utilisation d'un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut

Il est également possible d'utiliser un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut, comme c'est le cas dans Azure HDInsight.

**Configuration du système de fichiers par défaut à l'aide d'un conteneur de stockage d'objets blob Azure**

1.  Ouvrez **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** dans le Bloc-notes.
2.  Repérez la balise suivante :

		<property>
		  <name>fs.default.name</name>
		  <!-- cluster variant -->
		  <value>hdfs://localhost:8020</value>
		  <description>The name of the default file system.  Either the	literal string "local" or a host:port for NDFS.</description>
		  <final>true</final>
		</property>

3.  Remplacez-la par les deux balises suivantes :

         
		<property>
		  <name>fs.default.name</name>
		  <!-- cluster variant -->
		  <!--<value>hdfs://localhost:8020</value>-->
		  <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
		  <description>The name of the default file system.  Either the	literal string "local" or a host:port for NDFS.</description>
		  <final>true</final>
		</property>
		
		<property>
		  <name>dfs.namenode.rpc-address</name>
		  <value>hdfs://localhost:8020</value>
		  <description>A base for other temporary directories.</description>
		</property>
         

    Vous devez remplacer et par les valeurs qui correspondent aux informations de votre compte de stockage.

4.  Enregistrez les modifications.
5.  Ouvrez la ligne de commande Hadoop à partir du Bureau en mode d'exécution élevé (Exécuter en tant qu'administrateur).
6.  Exécutez les commandes suivantes pour redémarrer les services Hadoop :

         C:\Hadoop\stop-onebox.cmd
         C:\Hadoop\start-onebox.cmd

7.  Exécutez la commande suivante pour tester la connexion au système de fichiers par défaut :

         hadoop fs -ls /

    Les commandes suivantes répertorient le contenu du même dossier :

         hadoop fs -ls wasb:///
         hadoop fs -ls wasb://@.blob.core.windows.net/
         hadoop fs -ls wasbs://@.blob.core.windows.net/

    Pour accéder à HDFS, utilisez la commande suivante :

         hadoop fs -ls hdfs://localhost:8020/

Exécution de PowerShell pour HDInsight
--------------------------------------

Certaines cmdlets PowerShell pour HDInsight sont prises en charge sur l'émulateur HDInsight, notamment :

-   Cmdlets de définition de tâche HDInsight

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

Voici un exemple d'envoi d'une tâche Hadoop :

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = 
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Vous devez voir apparaître une boîte de dialogue lors de l'appel à Get-Credential : vous devez utiliser **hadoop** comme nom d'utilisateur. Le mot de passe peut être une chaîne quelconque. Le nom du cluster est toujours **http://localhost:50111**.

Pour plus d'informations sur l'envoi de tâches Hadoop, consultez la page [Envoi de tâches Hadoop par programme](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/). Pour plus d'informations sur les cmdlets PowerShell pour HDInsight, consultez la page [Référence des cmdlets HDInsight](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn479228.aspx).

Étapes suivantes
----------------

Dans ce didacticiel, vous avez installé un émulateur HDInsight et exécuté certaines tâches Hadoop. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Développement de programmes MapReduce en Java pour HDInsight](/fr-fr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [Développement de programmes MapReduce de diffusion en continu Hadoop en C\# pour HDInsight](/en-us/manage/services/hdinsight/develop-deploy-hadoop-streaming-jobs/)
-   [Notes de publication de l'émulateur HDInsight](https://gettingstarted.hadooponazure.com/releaseNotes.html)
-   [Forum MSDN sur HDInsight](http://social.msdn.microsoft.com/Forums/en-US/hdinsight)

