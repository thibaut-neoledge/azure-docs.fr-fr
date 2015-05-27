<properties 
	pageTitle="Test d’un écosystème Hadoop avec l’émulateur HDInsight | Microsoft Azure" 
	description="Utilisez un émulateur sur un ordinateur de bureau et suivez un didacticiel MapReduce pour en savoir plus sur l’utilisation d’un écosystème Hadoop dans HDInsight. L’émulateur HDInsight fonctionne comme un bac à sable (sandbox) Hadoop." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="nitinme"/>

# Prise en main de l'émulateur HDInsight 

Ce didacticiel vous présente l'utilisation de clusters Hadoop dans l'émulateur Microsoft HDInsight pour Azure (anciennement HDInsight Server Developer Preview). L'émulateur HDInsight est fourni avec les mêmes composants de l'écosystème Hadoop qu'Azure HDInsight. Pour plus d'informations, notamment sur les versions déployées, consultez la rubrique [Version de Hadoop dans Azure HDInsight](hdinsight-component-versioning.md).

> [AZURE.NOTE]L’émulateur HDInsight inclut uniquement un cluster Hadoop. Il n’inclut pas HBase ou Storm.

L’émulateur HDInsight fournit un environnement de développement local assez proche d’un bac à sable (sandbox) Hadoop. Si vous connaissez Hadoop, vous pouvez prendre en main l’émulateur HDInsight à l’aide de Hadoop Distributed File System (HDFS). Dans HDInsight, le système de fichiers par défaut est le stockage d’objets blob Azure. Ainsi, vous pouvez développer vos tâches à l’aide du stockage d’objets blob Azure. Pour utiliser le stockage d’objets blob Azure avec l’émulateur HDInsight, vous devez apporter des modifications à la configuration de l’émulateur.

> [AZURE.NOTE]L'émulateur HDInsight peut uniquement utiliser un déploiement à nœud unique.


## Configuration requise	
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- L'émulateur HDInsight requiert une version 64 bits de Windows. L'un des systèmes d'exploitation suivants doit être installé :

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack 1
	- Windows 8 
	- Windows Server 2012

- Installez et configurez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](install-configure-powershell.md).


##<a name="install"></a>Installation de l’émulateur HDInsight

L'émulateur Microsoft HDInsight peut être installé via Microsoft Web Platform Installer.

> [AZURE.NOTE]Actuellement, l’émulateur HDInsight prend uniquement en charge les systèmes d’exploitation en anglais. Si vous avez installé une version antérieure de l’émulateur, vous devez désinstaller les deux composants suivants à partir de Panneau de configuration/Programmes et fonctionnalités avant d’installer la dernière version de l’émulateur : <ul> <li>Émulateur Microsoft HDInsight pour Azure ou HDInsight Developer Preview, selon celui qui est installé</li> <li>Hortonworks Data Platform</li> </ul>


**Installation de l’émulateur HDInsight**

1. Ouvrez Internet Explorer, puis accédez à la [page d'installation de l'émulateur Microsoft HDInsight pour Azure][hdinsight-emulator-install].
2. Cliquez sur **Installer maintenant**. 
3. Cliquez sur **Exécuter** lorsque vous êtes invité à installer HDINSIGHT.exe en bas de la page. 
4. Cliquez sur le bouton **Oui** dans la fenêtre **Contrôle de compte d'utilisateur** qui s'affiche pour terminer l'installation. La fenêtre Web Platform Installer s’affiche.
6. Cliquez sur **Installer** en bas de la page.
7. Cliquez sur **J'accepte** pour accepter les termes du contrat de licence.
8. Vérifiez que Web Platform Installer affiche **Les produits suivants ont été correctement installés**, puis cliquez sur **Terminer**.
9. Cliquez sur **Quitter** pour fermer la fenêtre Web Platform Installer.

**Vérification de l’installation de l’émulateur HDInsight**
	
L'installation doit avoir installé trois icônes sur votre Bureau. Celles-ci sont liées comme suit :
	
- **Hadoop Command Line** :invite de commandes Hadoop à partir de laquelle les tâches MapReduce, Pig et Hive sont exécutées dans l’émulateur HDInsight.

- **Hadoop NameNode Status** : NameNode gère une arborescence de répertoires pour tous les fichiers dans HDFS. NameNode effectue également le suivi de l’emplacement des données conservées dans un cluster Hadoop pour tous les fichiers. Les clients communiquent avec NameNode pour obtenir l'emplacement de stockage des nœuds de données pour tous les fichiers.
	
- **Hadoop Yarn Status** : dispositif de suivi des tâches qui alloue des tâches MapReduce à des nœuds dans un cluster.

L'installation doit également avoir installé plusieurs services locaux. La capture d'écran suivante présente la fenêtre Services :

![HDI.Emulator.Services][image-hdi-emulator-services]

Les services liés à l’émulateur HDInsight ne sont pas démarrés par défaut. Pour lancer ces services, à partir de la ligne de commande Hadoop, exécutez **start_local_hdp_services.cmd** sous C:\\hdp (emplacement par défaut). Pour démarrer automatiquement les services après le redémarrage de l'ordinateur, exécutez **set-onebox-autostart.cmd**.

Pour plus d’informations sur les problèmes connus lors de l’installation et de l’exécution de l’émulateur HDInsight, consultez la page [Notes de publication de l’émulateur HDInsight](hdinsight-emulator-release-notes.md). Le journal d'installation se trouve à l'emplacement **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

##<a name="vstools"></a>Utilisation des outils HDInsight pour Visual Studio

Vous pouvez utiliser les outils HDInsight pour Visual Studio pour vous connecter à l’émulateur HDInsight. Pour plus d’informations sur la façon d’utiliser les outils de Visual Studio avec les clusters HDInsight sur Azure, consultez la page [Prise en main de HDInsight Hadoop Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

### Installation des outils HDInsight pour l’émulateur

Pour obtenir des instructions sur la façon d’installer les outils Visual Studio HDInsight, cliquez [ici](hdinsight-hadoop-visual-studio-tools-get-started.md#installation).

### Connexion à l’émulateur HDInsight

1. Ouvrez Visual Studio.
2. Dans le menu **Affichage**, cliquez sur **Explorateur de serveurs** pour ouvrir la fenêtre du même nom.
3. Développez **Azure**, cliquez avec le bouton droit sur **HDInsight**, puis cliquez sur **Connexion à l’émulateur HDInsight**.

	 ![HDI.Emulator.Connect.VS](./media/hdinsight-get-started-emulator/hdi.emulator.connect.vs.png)

4. Dans la boîte de dialogue Connexion à l’émulateur HDInsight, vérifiez les valeurs des points de terminaison WebHCat, HiveServer2 et WebHDFS, puis cliquez sur **Suivant**. Les valeurs par défaut doivent fonctionner si vous n’avez pas modifié la configuration par défaut de l’émulateur. Si vous avez modifié la configuration, mettez à jour les valeurs dans la boîte de dialogue, puis cliquez sur Suivant.

	![HDI.Emulator.Connect.VS.dialog](./media/hdinsight-get-started-emulator/hdi.emulator.connect.vs.dialog.png)

5. Une fois la connexion établie, cliquez sur **Terminer**. L’émulateur HDInsight doit être affiché dans l’Explorateur de serveurs.

	![HDI.Emulator.Connect.VS.dialog](./media/hdinsight-get-started-emulator/hdi.emulator.vs.connected.png)

Une fois la connexion établie, vous pouvez utiliser les outils Visual Studio HDInsight avec l’émulateur, comme avec un cluster Azure HDInsight. Pour obtenir des instructions sur l’utilisation des outils Visual Studio avec les clusters Azure HDInsight, consultez la page [Utilisation des outils HDInsight Hadoop pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

### Résolution des problèmes : connexion d’outils HDInsight à l’émulateur HDInsight

1. Lors de la connexion à l’émulateur HDInsight, même si la boîte de dialogue indique que HiveServer2 est connecté avec succès, vous devez définir manuellement **hive.security.authorization.enabled property** sur **false** dans le fichier de configuration Hive situé sur C:\\hdp\\hive-*version*\\conf\\hive-site.xml, puis redémarrer l’émulateur local. Les outils HDInsight pour Visual Studio se connectent uniquement à HiveServer2 lorsque vous affichez un aperçu des 100 premières lignes de votre table. Si vous ne souhaitez pas utiliser une telle requête, ne modifiez pas la configuration du fichier Hive.

2. Si vous utilisez l’allocation d’adresse IP dynamique (DHCP) sur l’ordinateur exécutant l’émulateur HDInsight, vous devrez peut-être mettre à jour C:\\hdp\\hadoop-*version*\\etc\\hadoop\\core-site.xml et remplacer la valeur de la propriété **hadoop.proxyuser.hadoop.hosts** par (*). Cela permet à l’utilisateur Hadoop de se connecter à tous les hôtes pour emprunter l’identité de l’utilisateur que vous avez entrée dans Visual Studio.

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. Il est possible qu’une erreur survienne lorsque Visual Studio tente de se connecter au service WebHCat (« Erreur » : « Impossible de trouver le travail job_XXXX_0001 »). Dans ce cas, vous devez redémarrer le service WebHCat, puis réessayer. Pour redémarrer le service WebHCat, démarrez la console MMC **Services**, cliquez avec le bouton droit sur **Apache Hadoop Templeton** (il s’agit de l’ancien nom du service WebHCat), puis cliquez sur **Redémarrer**.

##<a name="runwordcount"></a>Exécution d’une tâche MapReduce de comptage des mots

Maintenant que l’émulateur HDInsight est configuré sur votre poste de travail, vous pouvez exécuter une tâche MapReduce pour tester l’installation. D'abord, vous allez charger quelques fichiers de données vers HDFS, puis vous exécuterez une tâche MapReduce de comptage des mots pour compter la fréquence de mots spécifiques dans ces fichiers.

Le programme MapReduce de comptage des mots a été empaqueté dans le fichier *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*. Celui-ci se situe dans le dossier *C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\hadoop\\mapreduce*.

La tâche MapReduce de comptage des mots prend deux arguments :

- Un dossier d'entrée. Vous utiliserez *hdfs://localhost/user/HDIUser* comme dossier d’entrée.- Un dossier de sortie. Vous utiliserez *hdfs://localhost/user/HDIUser/WordCount_Output* comme dossier de sortie. Le dossier de sortie ne peut pas être un dossier existant, sinon la tâche MapReduce échoue. Si vous souhaitez exécuter la tâche MapReduce une deuxième fois, vous devez soit spécifier un autre dossier de sortie soit supprimer le dossier de sortie existant. 
**Exécution d’une tâche MapReduce de comptage des mots**

1. À partir du Bureau, double-cliquez sur **Hadoop Command Line** pour ouvrir la fenêtre de ligne de commande Hadoop. Le dossier actif doit être :

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	Si ce n'est pas le cas, exécutez la commande suivante :

		cd %hadoop_home%

2. Exécutez les commandes Hadoop suivantes afin de créer un dossier HDFS pour le stockage des fichiers d'entrée et de sortie :

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser
	
3. Exécutez la commande Hadoop suivante pour copier certains fichiers texte locaux vers HDFS :

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. Exécutez la commande suivante pour répertorier les fichiers du dossier /user/HDIUser :

		hadoop fs -ls /user/HDIUser

	Les fichiers suivants doivent s'afficher :

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Exécutez la commande suivante pour exécuter la tâche MapReduce de comptage des mots :

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Exécutez la commande suivante pour répertorier le nombre de mots comprenant « windows » à partir du fichier de sortie :

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	La sortie doit être :

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Pour plus d'informations sur les commandes Hadoop, consultez la page [Manuel des commandes Hadoop][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Exécution de l’exemple de prise en main

L’installation de l’émulateur HDInsight fournit quelques exemples offrant un apprentissage rapide des services Apache Hadoop sur Windows aux utilisateurs. Ces exemples présentent quelques tâches généralement nécessaires lors du traitement d’un jeu de données volumineux. En parcourant les exemples, vous vous familiariserez avec les concepts associés au modèle de programmation MapReduce et à son écosystème.

L’exemple tourne autour du traitement des données de journal IIS World Wide Web Consortium (W3C). Un outil de génération de données est fourni pour créer et importer les ensembles de données de plusieurs tailles vers un fichier Hadoop (HDFS) ou un stockage d’objets blob Azure. Pour plus d’informations, consultez la page [Utilisation du stockage d’objets blob Azure pour HDInsight](hdinsight-use-blob-storage.md). Ensuite, les tâches MapReduce, Pig ou Hive peuvent être exécutées sur les pages de données générées par le script Azure PowerShell. Notez que les scripts Pig et Hive forment une couche d'abstraction par-dessus MapReduce, et peuvent éventuellement être compilés en programmes MapReduce. Les utilisateurs peuvent exécuter une série de tâches afin d'observer les effets de l'utilisation de ces différentes technologies et les effets de la taille des données sur l'exécution des tâches de traitement.

### Dans cette section

- [Scénario de données du journal IIS W3C](#scenarios)
- [Chargement de l’exemple des données du journal W3C](#loaddata)
- [Exécution du travail MapReduce en Java](#javamapreduce)
- [Exécution d’un travail Hive](#hive)
- [Exécution d’un travail Pig](#pig)
- [Régénération des exemples](#rebuild)

###<a name="scenarios"></a>Scénario de données du journal IIS W3C

Le scénario W3C génère et importe les données du journal IIS W3C vers un fichier Hadoop (HDFS) ou un stockage d’objets blob Azure en trois tailles : petite (1 Mo), moyenne (500 Mo) et grande (2 Go). Il fournit trois types de tâches et implémente chacune d'elles en C#, Java, Pig et Hive.

- **totalhits** : calcule le nombre total de requêtes pour une page donnée. 
- **avgtime** : calcule la durée moyenne prise (en secondes) par une requête par page. 
- **errors** : calcule le nombre d’erreurs par page, par heure, pour les requêtes avec un état 404 ou 500. 

Ces exemples et leur documentation ne fournissent pas une étude approfondie ou une implémentation complète des principales technologies Hadoop. Le cluster utilisé possède seulement un nœud unique, par conséquent, les effets de l'ajout de plusieurs nœuds ne peuvent pas être observés avec cette version.

###<a name="loaddata"></a>Chargement de l’exemple des données du journal W3C

La génération et l’importation des données vers un fichier Hadoop (HDFS) sont effectuées à l’aide du script PowerShell importdata.ps1.

**Pour importer l’exemple des données du journal W3C**

1. Ouvrez la ligne de commande Hadoop à partir du Bureau.
2. Accédez au répertoire **C:\\hdp\\GettingStarted**.
3. Exécutez la commande suivante pour générer et importer les données vers HDFS :

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

	Si vous souhaitez plutôt charger les données vers un stockage d’objets blob Azure, consultez la rubrique [Connexion au stockage d’objets blob Azure](#blobstorage).

4. Exécutez la commande suivante à partir de la ligne de commande Hadoop pour répertorier les fichiers importés vers le fichier Hadoop (HDFS) :

		hadoop fs -ls -R /w3c

	Le résultat doit ressembler à ce qui suit :

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Si vous voulez vérifier le contenu du fichier, exécutez la commande suivante pour afficher un des fichiers de données dans la fenêtre de la console :

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Les fichier de données sont à présent créés et importés dans HDFS. Vous pouvez commencer à exécuter différentes tâches Hadoop.

###<a name="javamapreduce"></a> Exécution de tâches MapReduce en Java

MapReduce est le moteur de calcul de base de Hadoop. Par défaut, il est implémenté en Java, mais des exemples utilisent aussi .NET et Hadoop Streaming qui utilisent C#. La syntaxe pour l'exécution d'une tâche MapReduce est la suivante :

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Le fichier .jar et les fichiers sources sont situés dans le dossier C:\\Hadoop\\GettingStarted\\Java.

**Pour exécuter une tâche MapReduce pour le calcul des accès à une page Web**

1. Ouvrez la ligne de commande Hadoop.
2. Accédez au répertoire **C:\\hdp\\GettingStarted**.
3. Exécutez la commande suivante pour supprimer le dossier de sortie dans le cas où ce dossier existerait. La tâche MapReduce échoue si le dossier de sortie existe déjà.

		hadoop fs -rm -r /w3c/output

3. Exécutez la commande suivante :

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	Le tableau suivant décrit les éléments de la commande : <table border="1"> <tr><td>Paramètre</td><td>Remarque</td></tr> <tr><td>w3c_scenarios.jar</td><td>Le fichier jar est situé dans le dossier C:\\hdp\\GettingStarted\\Java.</td></tr> <tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>Le type peut être remplacé par l’un des suivants : <ul> <li>microsoft.hadoop.w3c.AverageTimeTaken</li> <li>microsoft.hadoop.w3c.ErrorsByPage</li> </ul></td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td>Le fichier d’entrée peut être remplacé par ce qui suit : <ul> <li>/w3c/input/medium/data_w3c_medium.txt</li> <li>/w3c/input/large/data_w3c_large.txt</li> </ul></td></tr> <tr><td>/w3c/output</td><td>Il s’agit du nom du dossier de sortie.</td></tr> </table>

4. Exécutez la commande suivante pour afficher le fichier de sortie :

		hadoop fs -cat /w3c/output/part-00000

	Le résultat doit ressembler à ceci :

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	Le nombre d'accès à la page Default.aspx s'élève à 3 360, et ainsi de suite. Essayez de réexécuter les commandes en remplaçant les valeurs de la façon suggérée dans le tableau ci-dessus et observez la façon dont le résultat change en fonction du type de tâche et de la taille des données.

### <a name="hive"></a>Exécution de tâches Hive
Il ne faut pas beaucoup de temps aux analystes expérimentés en SQL (Structured Query Language) pour être à l’aise avec le moteur de requête Hive. Il fournit une interface semblable à celle de SQL et un modèle de base de données relationnelle pour HDFS. Hive utilise un langage nommé HiveQL, qui est très similaire à SQL. Hive fournit une couche d’abstraction par-dessus l’infrastructure MapReduce en Java et les requêtes Hive sont compilées dans MapReduce au moment de l’exécution.

**Exécution d’une tâche Hive**

1. Ouvrez une ligne de commande Hadoop.
2. Accédez au répertoire **C:\\hdp\\GettingStarted**.
3. Exécutez la commande suivante pour supprimer le dossier **/w3c/hive/input** s'il existe déjà. La tâche Hive échoue si le dossier existe.

		hadoop fs -rmr /w3c/hive/input

4. Exécutez la commande suivante pour créer les dossiers **/w3c/hive/input**, puis copiez les fichiers de données dans le dossier /hive/input :

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input
        
		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Exécutez la commande suivante pour exécuter le fichier de script **w3ccreate.hql**. Le script crée une table Hive et charge les données dans la table Hive :

	> [AZURE.NOTE]À ce stade, vous pouvez également utiliser les outils Visual Studio HDInsight pour exécuter la requête Hive. Ouvrez Visual Studio, créez un projet, puis à partir du modèle HDInsight, sélectionnez **Application Hive**. Une fois le projet ouvert, ajoutez la requête en tant que nouvel élément. La requête est disponible à l’adresse **C:/hdp/GettingStarted/Hive/w3c**. Une fois la requête ajoutée au projet, remplacez **${hiveconf:input}** par **/w3c/hive/input**, puis cliquez sur **Envoyer**.
        
		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	Le résultat doit ressembler à ce qui suit :

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

6. Exécutez la commande suivante pour exécuter le fichier de script HiveQL **w3ctotalhitsbypage.hql** :

	> [AZURE.NOTE]Comme expliqué précédemment, vous pouvez aussi exécuter cette requête en utilisant les outils Visual Studio HDInsight.

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	Le tableau suivant décrit les éléments de la commande : <table border="1"> <tr><td>Fichier</td><td>Description</td></tr> <tr><td>C:\\hdp\\hive-0.13.0.2.1.3.0-1981\\bin\\hive.cmd</td><td>Le script de commande Hive.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql</td><td> Vous pouvez remplacer le fichier de script Hive par l’un des éléments suivants : <ul> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li> </ul> </td></tr>

	</table>

	Le script HiveQL w3ctotalhitsbypage.hql est :

		SELECT filtered.cs_uri_stem,COUNT(*) 
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	La fin du résultat doit ressembler à ce qui suit :
		
		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Notez qu'en tant que première étape pour chaque tâche, une table est créée et les données sont chargées dans la table à partir du fichier créé précédemment. Vous pouvez parcourir le fichier qui a été créé en regardant sous le nœud /Hive dans HDFS à l’aide de la commande suivante :

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Exécution de travaux Pig

Le traitement Pig utilise un langage de flux de données, nommé *Pig Latin*. Les abstractions Pig Latin offrent des structures de données plus riches que MapReduce et sont pour Hadoop ce qu’est SQL pour les systèmes de gestion de base de données relationnelle.


**Pour exécuter les tâches Pig :**

1. Ouvrez une ligne de commande Hadoop.
2. Accédez au dossier **C:\\hdp\\GettingStarted**.
3. Exécutez la commande suivante pour soumettre la tâche Pig :

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	Le tableau suivant indique les éléments de la commande : <table border="1"> <tr><td>Fichier</td><td>Description</td></tr> <tr><td>C:\\hdp\\pig-0.12.1.2.1.3.0-1981\\bin\\pig.cmd</td><td>Le script de commande Pig.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig</td><td> Vous pouvez remplacer le fichier de script Pig Latin par l’un des éléments suivants : <ul> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li> </ul> </td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Vous pouvez remplacer le paramètre par un fichier plus volumineux :
	
	<ul>
<li>/w3c/input/medium/data_w3c_medium.txt</li>
<li>/w3c/input/large/data_w3c_large.txt</li>
</ul></td></tr> </table>

	Le résultat doit ressembler à ce qui suit :

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)
		
Notez que puisque les scripts Pig se compilent en tâches MapReduce, et éventuellement en plusieurs tâches de ce genre, vous verrez peut-être plusieurs tâches MapReduce s'exécuter lors du traitement d'une tâche Pig.

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>Connexion au stockage d’objets blob Azure
L'émulateur HDInsight utilise HDFS en tant que système de fichiers par défaut. Toutefois, Azure HDInsight utilise le stockage d'objets blob Azure comme système de fichiers par défaut. Il est possible de configurer l’émulateur HDInsight pour utiliser le stockage d’objets blob Azure plutôt que le stockage local. Suivez les instructions ci-dessous pour créer un conteneur de stockage dans Azure et pour le connecter à l'émulateur HDInsight.

>[AZURE.NOTE]Pour plus d’informations sur la façon dont HDInsight utilise le stockage d’objets blob Azure, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

Avant de commencer à suivre les instructions ci-dessous, vous devez créer un compte de stockage. Pour obtenir des instructions, consultez la rubrique [Création d'un compte de stockage](storage-create-storage-account.md).

**Création d’un conteneur**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **STORAGE** à gauche. Une liste des comptes de stockage s’affiche sous votre abonnement.
3. Cliquez sur le compte de stockage dans lequel vous souhaitez créer le conteneur dans la liste.
4. Cliquez sur **CONTAINERS** en haut de la page.
5. Cliquez sur **ADD** en bas de la page.
6. Entrez **NAME** et sélectionnez **ACCESS**. Vous pouvez utiliser un des trois niveaux d’accès. Le niveau par défaut est **Private**.
7. Cliquez sur **OK** pour enregistrer les modifications. Le nouveau conteneur est à présent répertorié sur le portail.

Avant de pouvoir accéder à un compte de stockage Azure, vous devez ajouter le nom de compte et la clé de compte au fichier de configuration.

**Pour configurer la connexion à un compte de stockage Azure**

1. Ouvrez **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\etc\\hadoop\\core-site.xml** dans le Bloc-notes.
2. Ajoutez la balise <property> suivante à la suite des autres balises <property> :

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Vous devez remplacer <StorageAccountName> et <StorageAccountKey> par les valeurs qui correspondent aux informations de votre compte de stockage.

3. Enregistrez la modification. Il n'est pas nécessaire de redémarrer les services Hadoop.

Utilisez la syntaxe suivante pour accéder au compte de stockage :

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Par exemple :

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Exécution d’Azure PowerShell
Certaines applets de commande Azure PowerShell sont également prises en charge sur l’émulateur HDInsight, notamment :

- Cmdlets de définition de tâche HDInsight
	
	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition                                                                                          
	- New-AzureHDInsightHiveJobDefinition                                                                                           
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

Voici un exemple d'envoi d'une tâche Hadoop :

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Vous devez voir apparaître une boîte de dialogue lors de l'appel à Get-Credential : Vous devez utiliser **hadoop** comme nom d’utilisateur. Le mot de passe peut être une chaîne quelconque. Le nom du cluster est toujours **http://localhost:50111**.

Pour plus d’informations sur l’envoi de tâches Hadoop, consultez la page [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md). Pour plus d’informations sur les applets de commande Azure PowerShell pour HDInsight, consultez la page [Documentation de référence des applets de commande HDInsight PowerShell][hdinsight-powershell-reference].


##<a name="remove"></a> Suppression de l’émulateur HDInsight
Ouvrez le Panneau de configuration sur l’ordinateur où l’émulateur est installé, puis, sous **Programme**, cliquez sur **Désinstaller un programme**. Dans la liste des programmes installés, cliquez sur **Émulateur Microsoft HDInsight pour Azure**, puis sur **Désinstaller**.


##<a name="nextsteps"></a>Étapes suivantes
Dans ce didacticiel, vous avez installé un émulateur HDInsight et exécuté certaines tâches Hadoop. Pour en savoir plus, consultez les articles suivants :

- [Prise en main d’Azure HDInsight](hdinsight-get-started.md)
- [Développement de programmes MapReduce en Java pour HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
- [Développement de programmes MapReduce de diffusion en continu Hadoop en C# pour HDInsight](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [Notes de publication de l’émulateur HDInsight](hdinsight-emulator-release-notes.md)
- [Forum MSDN sur HDInsight](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: install-configure-powershell.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png

<!--HONumber=54-->