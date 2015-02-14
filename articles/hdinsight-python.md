<properties 
	pageTitle="Utilisation de Python avec Hive et Pig dans Azure HDInsight" 
	description="Découvrez comment utiliser des fonctions définies par l'utilisateur Python à partir de Hive et Pig dans Azure HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>

#Utilisation de Python avec Hive et Pig dans HDInsight

Les langages Hive et Pig sont parfaits pour traiter des données dans HDInsight, mais vous avez parfois besoin d'un langage plus généraliste. Hive et Pig vous permettent de créer des fonctions définies par l'utilisateur (UDF) avec différents langages de programmation. Dans cet article, vous allez apprendre à utiliser une fonction définie par l'utilisateur Python à partir de Hive et Pig.

> [AZURE.NOTE] Les étapes décrites dans cet article s'appliquent aux versions de cluster HDInsight 2.1, 3.0 et 3.1.

##Sommaire

* [Python sur HDInsight](#python)
*   [Hive et Python](#hivepython)
*   [Pig et Python](#pigpython)
* [Exécution des exemples](#running)
* [Résolution des problèmes](#troubleshooting)
* [Étapes suivantes](#next)

##<a name="python"></a>Python sur HDInsight

Python 2.7 est installé par défaut sur des clusters HDInsight 3.0. L'installation se trouve dans le dossier D:\Python. Hive peut être utilisé avec cette version de Python pour le traitement par flux (les données sont transmises entre Hive et Python avec STDOUT/STDIN).

HDInsight inclut également Jython, une implémentation de Python écrite en Java. Vous avez intérêt à l'utiliser avec Pig, car ce dernier sait comment communiquer avec Jython sans devoir recourir à la diffusion en continu.

###<a name="hivepython"></a>Hive et Python

Python peut être utilisé en tant que fonction définie par l'utilisateur à partir de Hive via l'instruction HiveQL **TRANSFORM**. Par exemple, le code HiveQL suivant appelle un script Python stocké dans le fichier **streaming.py**.

	add file wasb:///streaming.py;
	
	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

Cet exemple effectue ce qui suit :

1. L'instruction **add file** au début du fichier ajoute le fichier **streaming.py** au cache distribué ; il est donc accessible à tous les nœuds du cluster.

2. L'instruction  **SELECT TRANSFORM ... USING 'D:\Python27\python.exe streaming.py'** sélectionne les données de la table **hivesampletable** et transmet clientid, devicemake et devicemodel au script **streaming.py**.

	> [AZURE.NOTE] La clause **USING** spécifie le chemin complet de python.exe, car ce dernier n'est pas dans le chemin.

3. La clause **AS** décrit les champs renvoyés par **streaming.py**.


<a name="streamingpy"></a>
Voici le fichier **streaming.py** utilisé par l'exemple HiveQL :

	import sys
	import string
	import hashlib
	
	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break
	
	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Comme nous utilisons la diffusion en continu, ce script doit effectuer ce qui suit :

1. Lire les données de STDIN. Cette opération est réalisée avec `sys.stdin.readline()` dans cet exemple.

2. Le caractère de renvoi à la ligne est supprimé avec `string.strip(line, "\n ")`, car nous voulons simplement les données texte, et pas l'indicateur de fin de ligne.

2. Lors du traitement par flux, une seule ligne contient toutes les valeurs séparées par un caractère de tabulation.  `string.split(line, "\t")` peut donc être utilisé pour fractionner l'entrée à chaque tabulation et retourner uniquement les champs.

3. Une fois le traitement terminé, la sortie doit être écrite dans STDOUT sur une seule ligne, chaque champ étant séparé par une tabulation. Cette opération est réalisée avec `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Tout cela a lieu dans une boucle `while`, qui se répète jusqu'à ce qu'aucun `line` ne soit lu ; `break` interrompt alors la boucle et le script se termine.

Par ailleurs, le script concatène simplement les valeurs d'entrée pour `devicemake` et `devicemodel`, et calcule un hachage de la valeur concaténée. Ce processus, plutôt simple, décrit les bases du fonctionnement normal de tout script Python appelé à partir de Hive : boucle, lire les données d'entrée jusqu'à la fin, fractionner chaque ligne d'entrée au niveau des tabulations, traiter, écrire une seule ligne de sortie délimitée par des tabulations.

Consultez la rubrique [Exécution des exemples](#running) pour savoir comment exécuter cet exemple sur votre cluster HDInsight.

###<a name="pigpython"></a>Pig et Python

Un script Python peut être utilisé comme une fonction définie par l'utilisateur à partir de Pig via l'instruction **GENERATE**. Par exemple, l'exemple suivant utilise un script Python stocké dans le fichier **jython.py**.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Cet exemple fonctionne de la manière suivante :

1. Il enregistre le fichier contenant le script Python (**jython.py**) avec **Jython** et l'expose dans Pig en tant que **myfuncs**. Jython est une implémentation de Python en Java et s'exécute dans la même machine virtuelle Java que Pig. Cela nous permet de traiter le script Python comme un appel de fonction classique, alors que Hive utilise une approche basée sur la diffusion en continu.

2. La ligne suivante charge l'exemple de fichier de données **sample.log** dans **LOGS**. Comme ce fichier journal n'a pas de schéma cohérent, il définit également chaque enregistrement (**LINE** dans le cas présent) comme un tableau de caractères (**chararray**), qui correspond essentiellement à une chaîne.

3. La troisième ligne élimine par filtrage les valeurs null et stocke le résultat de l'opération dans **LOG**.

4. La ligne suivante itère sur les enregistrements dans **LOG** et utilise **GENERATE** pour appeler la méthode **create_structure** contenue dans le script **jython.py** chargé en tant que **myfuncs**.  **LINE** est utilisé pour transmettre l'enregistrement actuel à la fonction.

5. Enfin, les sorties sont vidées dans STDOUT avec la commande **DUMP**. Il s'agit simplement d'afficher immédiatement les résultats une fois l'opération terminée ; dans un script réel, vous stockeriez normalement les données dans un nouveau fichier avec **STORE**.

<a name="jythonpy"></a>
Voici le fichier **jython.py** utilisé par l'exemple Pig :

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

Souvenez-vous : auparavant, nous avons simplement défini l'entrée **LINE** comme un tableau de caractères car il n'existait pas de schéma cohérent pour l'entrée ! Le rôle de **jython.py** est de transformer les données en un schéma cohérent pour la sortie. Cela fonctionne de la manière suivante :

1. L'instruction **@outputSchema** définit le format des données qui seront renvoyées à Pig. Dans le cas présent, il s'agit d'un **data bag**, qui est un type de données Pig. Ce conteneur contient les champs suivants, qui sont tous des tableaux de caractères (chaînes) :

	* date : date de création de l'entrée du journal
	* time : heure de création de l'entrée du journal
	* classname : nom de la classe pour laquelle l'entrée a été créée
	* level : niveau du journal
	* detail : détails pour l'entrée du journal

2. Ensuite, **def create_structure(input)** définit la fonction à laquelle Pig transmettra les lignes.

3. L'exemple de données **sample.log** respecte pour l'essentiel le schéma basé sur les champs date, time, classname, level et detail que nous voulons renvoyer. Mais il contient également quelques lignes commençant par la chaîne *java.lang.Exception* qui doivent être modifiées pour correspondre au schéma. L'instruction **if** recherche ces dernières, puis manipule les données d'entrée pour déplacer la chaîne *java.lang.Exception* à la fin, harmonisant les données avec notre schéma de sortie prévu.

4. La commande **split** est ensuite utilisée pour fractionner les données aux quatre premiers caractères d'espacement. Il en résulte cinq valeurs qui sont affectées aux champs **date**, **time**, **classname**, **level** et **detail**.

5. Enfin, les valeurs sont renvoyées à Pig.

Lorsque les données sont renvoyées à Pig, elles utilisent un schéma cohérent comme défini dans l'instruction **@outputSchema**.

Consultez la rubrique [Exécution des exemples](#running) pour savoir comment exécuter cet exemple sur votre cluster HDInsight.

##<a name="running"></a>Exécution des exemples

Ces étapes utilisent Windows Azure PowerShell. Si ce dernier n'est pas déjà installé et configuré sur votre ordinateur de développement, consultez la page [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/) avant d'exécuter la procédure suivante.


1. À l'aide des exemples Python [streaming.py](#streamingpy) et [jython.py](#jythonpy), créez des copies locales des fichiers sur votre ordinateur de développement.

2. Utilisez  le script PowerShell suivant pour télécharger les fichiers **streaming.py** et **jython.py** sur le serveur. Remplacez le nom par celui de votre cluster Azure HDInsight et le chemin par celui de vos fichiers **streaming.py** et **jython.py** sur les trois premières lignes du script.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$hdiStore = get-azurehdinsightcluster -name $clusterName
		$storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		$storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
		$defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName
		
		$destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
		set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
		set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

	Ce script récupère des informations concernant votre cluster HDInsight, puis extrait le compte et la clé pour le compte de stockage par défaut et télécharge les fichiers vers la racine du conteneur.

	> [AZURE.NOTE] D'autres méthodes de téléchargement des scripts sont décrites dans le document [Téléchargement de données pour des tâches Hadoop dans HDInsight](/fr-fr/documentation/articles/hdinsight-upload-data/).

###Utilisation du tableau de bord Hive (exemple Hive uniquement)

1. Après le téléchargement du fichier, ouvrez un navigateur et accédez à la page https://NomDeVotreCluster.azurehdinsight.net/. Lorsque les informations d'identification vous sont demandées, entrez le nom d'utilisateur admin et le mot de passe pour votre cluster.

	> [AZURE.NOTE] Vous pouvez également utiliser le lien **Gérer le cluster** au bas du **tableau de bord** HDInsight dans le portail de gestion Azure pour lancer le tableau de bord Hive.

2. À l'aide de l'**Éditeur Hive**, remplacez la ligne `select * from hivesampletable` par le code HiveQL suivant.

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'D:\Python27\python.exe streaming.py' AS
		  (clientid string, phoneLable string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Cliquez sur le bouton **Envoyer** pour envoyer la tâche. Selon la version du cluster HDInsight, vous pouvez être redirigé vers la page Détails de la tâche. Sinon, sélectionnez **Afficher les détails** dans la zone **Session de la tâche** au bas de la page.

4. La page **Détails de la tâche** est actualisée jusqu'à ce que la tâche soit terminée. Une fois qu'elle est terminée, les informations sur la tâche ainsi que le résultat s'affichent.

###Utilisation de PowerShell (exemples Hive et Pig)

Après avoir téléchargé les fichiers, utilisez les scripts PowerShell suivant pour démarrer les tâches. Lorsque la tâche s'achève, le résultat est normalement écrit sur la console PowerShell.

**Exécution de la tâche Hive**
    
    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLable string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"
	
	$jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

Le résultat de la tâche **Hive** doit ressembler à ceci :

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

**Exécution de la tâche Pig**

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"
	
	$jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

Le résultat de la tâche **Pig** doit ressembler à ceci :

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Résolution des problèmes

Les deux exemples de script PowerShell utilisés pour l'exécution des exemples contiennent une ligne mise en commentaire qui affiche les erreurs de sortie pour la tâche. Si la tâche ne donne pas le résultat prévu, supprimez les marques de commentaire sur la ligne suivante pour voir si les informations sur l'erreur signalent un problème.

	# Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

Les informations sur l'erreur (STDERR) et le résultat de la tâche (STDOUT) sont également enregistrés dans le conteneur d'objets blob par défaut pour vos clusters aux emplacements suivants.

<table>
<tr>
<td>Pour cette tâche...</td><td>Examinez ces fichiers dans le conteneur d'objets blob</td>
</tr>
<tr>
<td>Hive</td><td>/HivePython/stderr<br />/HivePython/stdout</td>
</tr>
<td>Pig</td><td>/PigPython/stderr<br />/PigPython/stdout</td>
</tr>
</table>

##<a name="next"></a>Étapes suivantes

Si vous devez charger des modules Python qui ne sont pas fournis par défaut, consultez la page [Déploiement d'un module vers Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) pour un exemple de procédure à suivre.

Si vous souhaitez exécuter des tâches sur HDInsight à distance sans utiliser PowerShell, consultez la page [Utilisation d'Azure HDInsight à partir de Linux](http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx), qui propose un exemple d'utilisation de Python pour exécuter des tâches par l'intermédiaire de l'API REST WebHCat.
<!--HONumber=42-->
