<properties 
	pageTitle="Notes de publication : Émulateur Microsoft HDInsight pour Azure | Azure" 
	description="Accédez à des informations de dernière minute sur les versions les plus récentes de l'émulateur HDInsight Hadoop." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/03/2014" 
	ms.author="jgao"/>




# Notes de publication : émulateur Microsoft HDInsight pour Azure 



> [AZURE.NOTE] 
> La méthode la plus simple pour connaître votre numéro de version consiste à vérifier dans Ajout/Suppression de programmes, à l'entrée " Émulateur Microsoft HDInsight pour Azure " (pour la version 1.0.0.0 ou supérieure) ou " Microsoft HDInsight Developer Preview " (pour les versions inférieures à 1.0.0.0). 

## v2.0.0.0, publiée le 29/08/2014

* Cette version met à jour l'émulateur HDInsight afin de cibler le même jeu de projets Hadoop actuellement actifs dans le service dans la version 3.1.    

* Comme pour les pré-versions du produit, cette version continue d'être conçue à destination des développeurs pour les scénarios de déploiement. En conséquence, seuls les déploiements sur un nœud unique sont pris en charge. 

### Nouveautés 
 
* [Versions de composants Hadoop mises à jour](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/) correspondant à la version 3.1 du service.  Cela inclut la prise en charge de Hive 0.13 et de Tez.

## v1.0.0.0, publiée le 28/10/2013

* Il s'agit de la version mise à disposition du grand public de l'émulateur Microsoft HDInsight Emulator pour Azure, précédemment connu sous le nom de Microsoft HDInsight Developer Preview. 

* Comme pour les pré-versions du produit, cette version continue d'être conçue à destination des développeurs pour les scénarios de déploiement. En conséquence, seuls les déploiements sur un nœud unique sont pris en charge. 

### Nouveautés 
 
* Des scripts ont été ajoutés pour simplifier la configuration de tous les services Apache Hadoop sur un démarrage automatique ou manuel. Le paramètre par défaut reste automatique, comme auparavant, mais tous les services peuvent désormais être modifiés à l'aide des scripts set-onebox-manualstart.cmd ou set-onebox-autostart.cmd, installés dans C:\Hadoop. 

* Le nombre de dépendances d'installation requises a été nettement réduit, ce qui permet d'effectuer des installations plus rapides. 

* Des erreurs ont été corrigées dans la commande pour exécuter des exemples Pig dans le script RunSamples.ps1installé dans le dossier GettingStarted. 

* Cette version comporte une mise à jour de la plateforme de données Hortonworks version 1.1 qui correspond aux services de plateforme de données Hortonworks disponibles avec Azure HDInsight cluster version 1.6. 

## v0.11.0.0, publiée le 30/09/2013

### Nouveautés 
		
* Le tableau de bord HDInsight a été supprimé. 

* Cette version comporte une mise à jour de la plateforme de données Hortonworks version " Developer Preview " qui correspond aux services de la pré-version de plateforme de données Hortonworks sur Azure HDInsight. 

## v0.10.0.0, publiée le 09/08/2013

### Nouveautés 
	
* Cette version comporte une mise à jour de la plateforme de données Hortonworks version " Developer Preview " qui correspond aux services de la pré-version de plateforme de données Hortonworks sur Azure HDInsight.

## v0.8.0.0, publiée le 07/06/2013

### Nouveautés 

* Cette version comporte une mise à jour de la plateforme de données Hortonworks version " Developer Preview " qui correspond aux services de la pré-version de plateforme de données Hortonworks sur Azure HDInsight.

## v0.6.0.0, publiée le 13/05/2013

### Nouveautés 

* Hive Server 2 est désormais installé. Cet élément est requis pour la nouvelle version (0.9.2.0) du pilote ODBC Microsoft pour Hive, publiée en même temps que cette mise à jour. 

* Tous les services sont définis en démarrage automatique pour ne plus avoir à relancer tous les éléments après redémarrage de la machine. 

## v0.4.0.0, publiée le 25/03/2013

### Nouveautés 

* Nouvelle publication de la pré-version de Microsoft HDInsight Developer ainsi que de la plateforme de données Hortonworks pour Windows Developer Preview. 

* Inclut Apache Hadoop, Hive, Pig, Sqoop, Oozie, HCatalog et Templeton. 

* Nouveau tableau de bord Microsoft HDInsight comprenant les fonctionnalités suivantes : 
 
* Connexion à plusieurs clusters, notamment l'installation locale et celles à distance utilisant le service Azure HDInsight. 
 
* Pour plus d'informations sur le service HDInsight, consultez la page [http://azure.microsoft.com/fr-fr/documentation/services/hdinsight/](http://azure.microsoft.com/fr-fr/documentation/services/hdinsight/).

* Configuration de WASB sur un cluster local

* Voir les instructions détaillées ci-dessous.

* Nouvelle console interactive Hive avec auteur et modification de requêtes Hive.

* Affichage et téléchargement de l'historique des tâches et des résultats.

### Notes de publication 

Numéros de ports 

* Les points de terminaison API REST sur une installation HDInsight locale sont accessibles via différents numéros de ports pour les mêmes services : 

	Local: 
	Oozie: http://localhost:11000/oozie/v1/admin/status 
	Templeton : http://localhost:50111/templeton/v1/status 
	ODBC : utilisez le port 10000 en configuration DSN ou la chaîne de connexion. 

	HDInsight Service: 
	Oozie: http://ServerFQDN:563/oozie/v1/admin/status 
	Templeton : http://ServerFQDN:563/templeton/v1/status 
	ODBC : utilisez le port 563 en configuration DSN ou la chaîne de connexion. 


* Configuration d'ASV sur le cluster local : 

	Dans le tableau de bord, un cluster local par défaut nommé " local (hdfs) " s'affiche. Procédez comme suit pour utiliser ASV comme stockage pour votre installation locale : 

	1. Ajoutez la balise de compte dans le fichier core-site.xml qui se trouve sous C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf:       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		Exemple :       

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. Ouvrez l'interface de commande Hadoop sur votre bureau en mode d'exécution élevé et exécutez la commande suivante :
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. Accédez à tout fichier présent sur ce compte via l'URI suivante : asv://{container}@{account}/{path} (ou asvs:// si vous souhaitez utiliser une connexion HTTPS pour accéder aux données). Exemple :
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Supprimez le cluster local actuellement enregistré, puis enregistrez-le à nouveau avec les nouvelles informations d'identification ASV. 


## v0.3.0.0, publiée le 13/12/2012 

* Le site Web du tableau de bord est passé en authentification anonyme (en remplacement des identifiants Windows). Ceci élimine le problème relatif à l'invite de connexion mentionné dans les notes de publication de la version précédente. 

* Correction de certaines erreurs Sqoop lors des exportations et de certains types d'importations. 

### Problèmes liés à cette version 

* Échec de chargement de la console JavaScript (voir les notes de publication de la version 0.2.0.0 pour plus d'informations). 
* La ligne de commande Sqoop affiche l'avertissement ci-dessous. Ces problèmes seront résolus lors des prochaines mises à jour. Ils peuvent être ignorés en toute sécurité. 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## v0.2.0.0, publiée le 03/12/2012

* Introduction du contrôle de version sémantique dans MSI 

* Correction de diverses erreurs d'installation signalées sur les forums MSDN, notamment concernant l'installation du tableau de bord HDInsight 

* Ajout des éléments du menu Démarrer pour une meilleure visibilité 

* Correction de l'entrée sur plusieurs lignes pour console Hive 

* Mises à jour mineures concernant le contenu de démarrage 

### Problèmes liés à cette version 

* Échec du chargement de la console JavaScript 

	* Sur certaines installations, la console JavaScript échoue, signalée sur la page par une erreur HTTP 404. Pour contourner ce problème, accédez directement à l'adresse http://localhost:8080 afin d'utiliser la console. 

* Une invite de connexion s'affiche lors de l'accès au tableau de bord HDInsight 

	* Cet affichage d'une boîte de dialogue de connexion lors de l'accès au tableau de bord HDInsight nous a été signalé. Dans ce cas, vous pouvez entrer les informations d'identification de votre utilisateur actuel, ce qui doit vous permettre d'accéder au tableau de bord. 


## v1.0.0.0, publiée le 23/10/2012

* Version initiale 

### Problèmes liés à cette version 

* Console Hive 

	* Si un renvoi à la ligne est ajouté à la commande Hive envoyée, cela engendre une erreur de syntaxe. Supprimez les renvois à la ligne ; la requête doit alors s'exécuter normalement. 



## Problèmes généraux connus

* Expiration du mot de passe utilisateur Hadoop 

	Le mot de passe pour l'utilisateur Hadoop peut arriver à expiration, en fonction des règles AD appliquées sur la machine. Le script PowerShell suivant indique l'absence d'expiration du mot de passe. Il faut l'exécuter depuis une invite de commandes en tant qu'administrateur. 	

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* Répertoire Temp> 
	
	hadoop.tmp.dir indique un emplacement incorrect : au lieu de pointer vers C:\hadoop\hdfs, il pointe vers c:\hdfs. Cette erreur sera corrigée lors de la prochaine mise à jour des portions HDP. 

* Restrictions de système d'exploitation 

	HDInsight Server doit être installé sur un système d'exploitation 64 bits. 

* HDInsight n'apparait pas dans les résultats de recherche WebPI. 

	Il s'agit normalement d'une restriction de système d'exploitation. HDInsight nécessite un système d'exploitation 64 bits avec les versions minimum suivantes : Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack1, Windows 8 ou Windows Server 2012.

* Documentation relative à l'invite de commandes administrative 

	* Pour exécuter certaines commandes, telles que **hadoop mradmin** ou **hadoop defadmin**, vous devez être connecté en tant qu'utilisateur hadoop. 

	* Pour créer facilement une commande exécutée sous cet utilisateur, ouvrez une invite de commandes Hadoop, puis exécutez la commande suivante : 
	 
	 		start-hadoopadminshell

	* Ceci ouvre une nouvelle commande exécutée avec les privilèges administrateur Hadoop. 

##<a name="nextsteps"></a> Étapes suivantes

- [Prise en main de l'émulateur HDInsight][hdinsight-get-started-emulator]


[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/


<!--HONumber=42-->
