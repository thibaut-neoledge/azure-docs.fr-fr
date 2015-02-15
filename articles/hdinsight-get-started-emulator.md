<properties 
	pageTitle="Prise en main de l'émulateur HDInsight | Azure" 
	description="Découvrez comment utiliser l'émulateur HDInsight pour Azure." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	author="nitinme" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="nitinme"/>

# Prise en main de l'émulateur HDInsight 

Ce didacticiel vous présente l'utilisation de clusters Hadoop dans l'émulateur Microsoft HDInsight pour Azure (anciennement HDInsight Server Developer Preview). L'émulateur HDInsight est fourni avec les mêmes composants de l'écosystème Hadoop qu'Azure HDInsight. Pour plus d'informations, notamment sur les versions déployées, consultez la rubrique [Version de Hadoop dans Azure HDInsight][hdinsight-versions].

>[AZURE.NOTE] L'émulateur HDInsight inclut uniquement un cluster Hadoop. Il n'inclut pas HBase.

L'émulateur HDInsight fournit un environnement de développement local pour Azure HDInsight. Si vous connaissez Hadoop, vous pouvez prendre en main l'émulateur à l'aide de HDFS. Dans HDInsight, le système de fichiers par défaut est le stockage d'objets blob Azure (WASB, Azure Storage - Blobs). Ainsi, vous pouvez développer vos tâches à l'aide de WASB. Pour pouvoir utiliser WASB avec l'émulateur HDInsight, vous devez apporter des modifications à la configuration de ce dernier. 

> [AZURE.NOTE] L'émulateur HDInsight peut uniquement utiliser un déploiement à nœud unique. 


**Conditions préalables**	
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- L'émulateur HDInsight requiert une version 64 bits de Windows. L'un des systèmes d'exploitation suivants doit être installé :

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack1
	- Windows 8 
	- Windows Server 2012

- Installez et configurez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure]. 

## Dans ce didacticiel

* [Installation de l'émulateur HDInsight](#install)
* [Exécution de l'exemple de comptage des mots](#runwordcount)
* [Exécution des exemples de prise en main](#rungetstartedsamples)
* [Connexion au stockage d'objets blob Azure](#blobstorage)
* [Exécution de PowerShell pour HDInsight](#powershell)
* [Suppression de l'émulateur HDInsight](#remove)
* [Étapes suivantes](#nextsteps)

##<a name="install"></a>Installation de l'émulateur HDInsight

L'émulateur Microsoft HDInsight peut être installé via Microsoft Web Platform Installer.  

> [AZURE.NOTE] Actuellement, l'émulateur HDInsight prend en charge uniquement les systèmes d'exploitation en anglais. Si une version antérieure de l'émulateur est installée, vous devez désinstaller les deux composants suivants à partir du Panneau de configuration/Programmes et fonctionnalités avant d'installer la dernière version de l'émulateur.
><ul>
<li>L'émulateur Microsoft HDInsight pour Windows Azure ou HDInsight Developer Preview, selon celui qui est installé.</li>
<li>Hortonworks Data Platform</li>
</ul>


**Installation de l'émulateur HDInsight**

1. Ouvrez Internet Explorer, puis accédez à la [page d'installation de l'émulateur Microsoft HDInsight pour Azure][hdinsight-emulator-install].
2. Cliquez sur **Installer maintenant**. 
3. Cliquez sur **Exécuter** lorsque vous êtes invité à installer HDINSIGHT.exe en bas de la page. 
4. Cliquez sur le bouton **Oui** dans la fenêtre **Contrôle de compte d'utilisateur** qui s'affiche pour terminer l'installation. Vous devez voir la boîte de dialogue Web Platform Installer.
6. Cliquez sur **Installer** en bas de la page.
7. Cliquez sur **J'accepte** pour accepter les termes du contrat de licence.
8. Vérifiez que Web Platform Installer affiche l'état **Les produits suivants ont été correctement installés**, puis cliquez sur **Terminer**.
9. Cliquez sur **Quitter** pour fermer la fenêtre Web Platform Installer.

**Vérification de l'installation de l'émulateur HDInsight**
	
L'installation doit avoir installé trois icônes sur votre Bureau. Celles-ci sont liées comme suit : 
	
- **Ligne de commande Hadoop** : invite de commandes Hadoop à partir de laquelle les tâches MapReduce, Pig et Hive sont exécutées dans l'émulateur HDInsight.

- **État NameNode Hadoop** : NameNode gère une arborescence de répertoires pour tous les fichiers dans HDFS. NameNode effectue également le suivi de l'emplacement des données conservées dans un cluster Hadoop pour tous les fichiers. Les clients communiquent avec NameNode pour obtenir l'emplacement de stockage des nœuds de données pour tous les fichiers.
	
- **État Yarn Hadoop** : dispositif de suivi des tâches qui alloue des tâches MapReduce à des nœuds dans un cluster.

L'installation doit également avoir installé plusieurs services locaux. La capture d'écran suivante présente la fenêtre Services :

![HDI.Emulator.Services][0]

Les services liés à l'émulateur HDInsight ne sont pas démarrés par défaut. Pour lancer ces services, à partir de la ligne de commande Hadoop, exécutez **start\_local\_hdp_services.cmd** sous <lecteur_système\>\hdp. Pour démarrer automatiquement les services après le redémarrage de l'ordinateur, exécutez **set-onebox-autostart.cmd**.  

Pour plus d'informations sur les problèmes connus lors de l'installation et de l'exécution de HDInsight Server, consultez la page [Notes de publication de l'émulateur HDInsight][hdinsight-emulator-release-notes]. Le journal d'installation se trouve à l'emplacement **C:\HadoopFeaturePackSetup\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log**.


##<a name="runwordcount"></a>Exécution d'une tâche MapReduce de comptage des mots

Désormais, l'émulateur HDInsight est configuré sur votre station de travail.  Vous pouvez exécuter une tâche MapReduce pour tester l'installation. D'abord, vous allez charger quelques fichiers de données vers HDFS, puis vous exécuterez une tâche MapReduce de comptage des mots pour compter la fréquence de mots spécifiques dans ces fichiers. 

Le programme MapReduce de comptage des mots a été empaqueté dans le fichier  *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*.  Le fichier jar se trouve dans le dossier  *C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce*.

La tâche MapReduce de comptage des mots prend deux arguments :

- Un dossier d'entrée. Vous utiliserez *hdfs://localhost/user/HDIUser* comme dossier d'entrée.
- Un dossier de sortie. Vous utiliserez *hdfs://localhost/user/HDIUser/WordCount_Output* comme dossier de sortie. Le dossier de sortie ne peut pas être un dossier existant, sinon la tâche MapReduce échoue. Si vous souhaitez exécuter la tâche MapReduce une deuxième fois, vous devez soit spécifier un autre dossier de sortie soit supprimer le dossier de sortie existant. 

**Exécution d'une tâche MapReduce de comptage des mots**

1. À partir du Bureau, double-cliquez sur **Ligne de commande Hadoop** pour ouvrir la fenêtre de ligne de commande Hadoop.  Le dossier actif doit être :

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	Si ce n'est pas le cas, exécutez la commande suivante :

		cd %hadoop_home%

2. Exécutez les commandes Hadoop suivantes afin de créer un dossier HDFS pour le stockage des fichiers d'entrée et de sortie :

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser
	
3. Exécutez la commande Hadoop suivante pour copier certains fichiers texte locaux vers HDFS :

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /user/HDIUser

4. Exécutez la commande suivante pour répertorier les fichiers du dossier /user/HDIUser :

		hadoop fs -ls /user/HDIUser

	Les fichiers suivants doivent s'afficher :

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Exécutez la commande suivante pour exécuter la tâche MapReduce de comptage des mots :

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981> hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Exécutez la commande suivante pour répertorier le nombre de mots comprenant " windows " à partir du fichier de sortie :

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	La sortie doit être :

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Pour plus d'informations sur les commandes Hadoop, consultez le [Guide des commandes Hadoop][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Exécution des exemples de prise en main

L'installation de l'émulateur HDInsight fournit quelques exemples offrant un apprentissage rapide des services Apache Hadoop sur Windows aux utilisateurs. Ces exemples présentent quelques tâches généralement nécessaires lors du traitement d'un jeu de données volumineux. En parcourant les exemples, vous vous familiariserez avec les concepts associés au modèle de programmation MapReduce et à son écosystème.

Les exemples sont organisés autour de scénarios de traitement des données du journal W3C IIS. Un outil de génération de données est fourni pour créer et importer les ensembles de données de plusieurs tailles vers HDFS ou WASB (stockage d'objets blob Azure). Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure pour HDInsight][hdinsight-storage]. Ensuite, les tâches MapReduce, Pig ou Hive peuvent être exécutées sur les pages de données générées par le script PowerShell. Notez que les scripts Pig et Hive forment une couche d'abstraction par-dessus MapReduce, et peuvent éventuellement être compilés en programmes MapReduce. Les utilisateurs peuvent exécuter une série de tâches afin d'observer les effets de l'utilisation de ces différentes technologies et les effets de la taille des données sur l'exécution des tâches de traitement. 

### Dans cette section

- [Scénarios des données du journal w3c IIS](#scenarios)
- [Chargement de l'exemple des données du journal w3c](#loaddata)
- [Exécution de tâches MapReduce en Java](#javamapreduce)
- [Exécution de tâches Hive](#hive)
- [Exécution de tâches Pig](#pig)
- [Régénération des exemples](#rebuild)

###<a name="scenarios"></a>Scénarios des données du journal w3c IIS

Le scénario w3c génère et importe les données du journal W3C IIS dans trois tailles vers HDFS ou WASB : 1 Mo (petit), 500 Mo (moyen) et 2 Go (grand). Il fournit trois types de tâches et implémente chacune d'elles en C#, Java, Pig et Hive.

- **totalhits** : calcule le nombre total de requêtes pour une page donnée 
- **avgtime** : calcule la durée moyenne prise (en secondes) par une requête par page 
- **errors** : calcule le nombre d'erreurs par page, par heure, pour les requêtes avec un état 404 ou 500. 

Ces exemples et leur documentation ne fournissent pas une étude approfondie ou une implémentation complète des principales technologies Hadoop. Le cluster utilisé possède seulement un nœud unique, par conséquent, les effets de l'ajout de plusieurs nœuds ne peuvent pas être observés avec cette version. 

###<a name="loaddata"></a>Chargement de l'exemple des données du journal W3c

La génération et l'importation des données vers HDFS sont effectuées à l'aide du script PowerShell importdata.ps1.

**Importation de l'exemple des données du journal w3c**

1. Ouvrez la ligne de commande Hadoop à partir du Bureau.
2. Définissez le répertoire sur **C:\hdp\GettingStarted**.
3. Exécutez la commande suivante pour générer et importer les données vers HDFS :

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

	Si vous souhaitez plutôt charger les données vers WASB, consultez la rubrique [Connexion au stockage d'objets blob Azure](#blobstorage).

4. Exécutez la commande suivante à partir de la ligne de commande Hadoop pour répertorier les fichiers importés vers HDFS :

		hadoop fs -ls -R /w3c

	Le résultat doit ressembler à ce qui suit : 

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Si vous voulez vérifier le contenu du fichier, exécutez la commande suivante pour afficher un des fichiers de données dans la fenêtre de la console :

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Les fichier de données sont à présent créés et importés dans HDFS. Vous pouvez commencer à exécuter différentes tâches Hadoop.

###<a name="javamapreduce"></a> Exécution de tâches MapReduce en Java

MapReduce est le moteur de calcul de base de Hadoop. Par défaut, il est implémenté en Java, mais des exemples utilisent aussi .NET et Hadoop Streaming qui utilisent C#. La syntaxe pour l'exécution d'une tâche MapReduce est la suivante :

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Le fichier .jar et les fichiers sources sont situés dans le dossier C:\Hadoop\GettingStarted\Java.

**Exécution d'une tâche MapReduce pour le calcul des accès à une page Web**

1. Ouvrez la ligne de commande Hadoop.
2. Définissez le répertoire sur **C:\hdp\GettingStarted**.
3. Exécutez la commande suivante pour supprimer le dossier de sortie dans le cas où ce dossier existerait.  La tâche MapReduce échoue si le dossier de sortie existe déjà.

		hadoop fs -rm -r /w3c/output

3. Exécutez la commande suivante :

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	Le tableau suivant décrit les éléments de la commande :
	<table border="1">
	<tr><td>Paramètre</td><td>Remarque</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>Le fichier jar se trouve dans le dossier C:\hdp\GettingStarted\Java.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>Le type peut être remplacé par un des suivants : 
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>Le fichier d'entrée peut être remplacé par :
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>Il s'agit du nom du dossier de sortie.</td></tr>
	</table>

4. Exécutez la commande suivante pour afficher le fichier de sortie :

		hadoop fs -cat /w3c/output/part-00000

	Le résultat doit ressembler à ceci :

		c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137

	Le nombre d'accès à la page Default.aspx s'élève à 3 360, et ainsi de suite. Essayez de réexécuter les commandes en remplaçant les valeurs de la façon suggérée dans le tableau ci-dessus et observez la façon dont le résultat change en fonction du type de tâche et de la taille des données.

### <a name="hive"></a>Exécution de tâches Hive
Il ne faut pas beaucoup de temps aux analystes expérimentés en SQL pour être à l'aise avec le moteur de requête Hive. Il fournit une interface semblable à celle de SQL et un modèle de base de données relationnelle pour HDFS. Hive utilise un langage nommé HiveQL, qui est très similaire à SQL. Hive fournit une couche d'abstraction par-dessus l'infrastructure MapReduce en Java et les requêtes Hive sont compilées dans MapReduce au moment de l'exécution.

**Exécution d'une tâche Hive**

1. Ouvrez la ligne de commande Hadoop.
2. Définissez le répertoire sur **C:\hdp\GettingStarted**.
3. Exécutez la commande suivante pour supprimer le dossier **/w3c/hive/input** s'il existe déjà.  La tâche Hive échoue si le dossier existe.

		hadoop fs -rmr /w3c/hive/input

4. Exécutez la commande suivante pour créer les dossiers **/w3c/hive/input**, puis copiez les fichiers de données dans le dossier /hive/input :

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input
        
		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Exécutez la commande suivante pour exécuter le fichier de script **w3ccreate.hql**.  Le script crée une table Hive et charge les données dans la table Hive :
        
		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	Le résultat doit ressembler à ce qui suit :

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. Exécutez la commande suivante pour exécuter le fichier de script HiveQL **w3ctotalhitsbypage.hql**.  

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	Le tableau suivant décrit les éléments de la commande :
	<table border="1">
	<tr><td>Fichier</td><td>Description</td></tr>
	<tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>Script de commande Hive.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Vous pouvez remplacer le fichier de script Hive par l'un des suivants :
	<ul>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

	</table>

	Le script HiveQL w3ctotalhitsbypage.hql est :

		SELECT filtered.cs_uri_stem,COUNT(*) 
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	La fin du résultat doit ressembler à ce qui suit :
		
		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Notez qu'en tant que première étape pour chaque tâche, une table est créée et les données sont chargées dans la table à partir du fichier créé précédemment. Vous pouvez parcourir le fichier qui a été créé en regardant sous le nœud /Hive dans HDFS à l'aide de la commande suivante :

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Exécution de tâches Pig

Le traitement Pig utilise un langage de flux de données, nommé *Pig Latin*. Les abstractions Pig Latin fournissent des structures de données plus détaillées que MapReduce et effectuent pour Hadoop ce que SQL effectue pour les systèmes SGBDR. 


**Exécution des tâches Pig**

1. Ouvrez la ligne de commande Hadoop.
2. Accédez au dossier **C:\hdp\GettingStarted**.
3. Exécutez la commande suivante pour soumettre la tâche Pig :

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	Le tableau suivant présente les éléments de la commande :
	<table border="1">
	<tr><td>Fichier</td><td>Description</td></tr>
	<tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>Script de commande Pig.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Vous pouvez remplacer le fichier de script Pig Latin par l'un des suivants :
	<ul>
	<li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Vous pouvez remplacer le paramètre par un fichier plus volumineux :
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

	Le résultat doit ressembler à ce qui suit :

		(/Info.aspx,1156)
		(/UserService,1137)
		(/Default.aspx,3360)
		
Notez que puisque les scripts Pig se compilent en tâches MapReduce, et éventuellement en plusieurs tâches de ce genre, vous verrez peut-être plusieurs tâches MapReduce s'exécuter lors du traitement d'une tâche Pig.


### <a name="rebuild"></a>Régénération des exemples
Les exemples contiennent actuellement tous les fichiers binaires requis, de sorte que la génération n'est pas nécessaire. Si vous souhaitez effectuer des modifications sur les exemples Java ou .NET, vous pouvez les régénérer à l'aide de msbuild ou du script PowerShell inclus.

**Régénération des exemples**

1. Ouvrez la ligne de commande Hadoop.
2. Exécutez la commande suivante :

		powershell -F buildsamples.ps1


##<a name="blobstorage"></a>Connexion au stockage d'objets blob Azure
L'émulateur HDInsight utilise HDFS en tant que système de fichiers par défaut. Toutefois, Azure HDInsight utilise le stockage d'objets blob Azure comme système de fichiers par défaut. Il est possible de configurer l'émulateur HDInsight pour utiliser le stockage d'objets blob Azure plutôt que le stockage local. Suivez les instructions ci-dessous pour créer un conteneur de stockage dans Azure et pour le connecter à l'émulateur HDInsight.

>[AZURE.NOTE] Pour plus d'informations sur la façon dont HDInsight utilise le stockage d'objets blob Azure, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Avant de commencer à suivre les instructions ci-dessous, vous devez créer un compte de stockage. Pour obtenir des instructions, consultez la rubrique [Création d'un compte de stockage][azure-create-storage-account].

**Création d'un conteneur**

1. Connectez-vous au [Portail de gestion][azure-management-portal].
2. Cliquez sur **STOCKAGE** à gauche. Vous devez voir une liste des comptes de stockage sous votre abonnement.
3. Cliquez sur le compte de stockage dans lequel vous souhaitez créer le conteneur dans la liste.
4. Cliquez sur **CONTENEURS** en haut de la page.
5. Cliquez sur **AJOUTER** en bas de la page.
6. Entrez **NOM** et sélectionnez suite **ACCÈS**. Vous pouvez utiliser un des trois niveaux d'accès.  Le niveau par défaut est **Privé**.
7. Cliquez sur **OK** pour enregistrer les modifications. Vous devez voir le nouveau conteneur répertorié sur le portail.

Avant de pouvoir accéder à un compte de stockage Azure, vous devez ajouter le nom de compte et la clé de compte au fichier de configuration.

**Configuration de la connexion à un compte de stockage Azure**

1. Ouvrez **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\etc\hadoop\core-site.xml** dans le Bloc-notes.
2. Ajoutez la balise <property\> suivante à la suite des autres balises <property\> :

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Vous devez remplacer <torageAccountName\> et <StorageAccountKey\> par les valeurs qui correspondent aux informations de votre compte de stockage.

3. Enregistrez la modification.  Il n'est pas nécessaire de redémarrer les services Hadoop.

Utilisez la syntaxe suivante pour accéder au compte de stockage :

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Par exemple :

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Exécution de PowerShell pour HDInsight
Certaines cmdlets PowerShell pour Azure HDInsight sont également prises en charge sur l'émulateur HDInsight, notamment :

- Cmdlets de définition de tâche HDInsight
	
	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition                                                                                          
	- New-AzureHDInsightHiveJobDefinition                                                                                           
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

Voici un exemple d'envoi d'une tâche Hadoop :

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Vous devez voir apparaître une boîte de dialogue lors de l'appel à Get-Credential : vous devez utiliser **hadoop** comme nom d'utilisateur. Le mot de passe peut être une chaîne quelconque. Le nom du cluster est toujours **http://localhost:50111**.

Pour plus d'informations sur l'envoi de tâches Hadoop, consultez la page [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]. Pour plus d'informations sur les cmdlets PowerShell pour HDInsight, consultez la page [Référence des cmdlets HDInsight][hdinsight-powershell-reference].


##<a name="remove"></a> Suppression de l'émulateur HDInsight
Ouvrez le Panneau de configuration sur l'ordinateur où l'émulateur est installé, puis cliquez sur **Désinstaller un programme** sous **Programme**. Dans la liste des programmes installés, cliquez sur **Émulateur Microsoft HDInsight pour Azure**, puis sur **Désinstaller**. 


##<a name="nextsteps"></a> Étapes suivantes
Dans ce didacticiel, vous avez installé un émulateur HDInsight et exécuté certaines tâches Hadoop. Pour en savoir plus, consultez les articles suivants :

- [Prise en main d'Azure HDInsight][hdinsight-get-started]
- [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]
- [Développement de programmes MapReduce de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-deploy-streaming]
- [Notes de publication de l'émulateur HDInsight][hdinsight-emulator-release-notes]
- [Forum MSDN sur HDInsight](http://social.msdn.microsoft.com/Forums/en-US/hdinsight)



[azure-sdk]: http://azure.microsoft.com/fr-fr/downloads/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/fr-fr/library/ff961504.aspx

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: ../hdinsight-emulator-release-notes/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn479228.aspx
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-deploy-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-versions]: ../hdinsight-component-versioning/

[Powershell-install-configure]: ../install-configure-powershell/

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png 

<!--HONumber=42-->
