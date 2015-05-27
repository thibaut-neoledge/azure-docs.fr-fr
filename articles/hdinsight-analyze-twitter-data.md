<properties 
	pageTitle="Analyse des données Twitter avec Hadoop dans HDInsight | Microsoft Azure" 
	description="Découvrez comment utiliser Hive pour analyser des données Twitter sur Hadoop dans HDInsight afin de déterminer la fréquence d'utilisation d'un mot particulier." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="jgao"/>

# Analyse des données Twitter avec Hadoop dans HDInsight

##Vue d'ensemble
Les sites web sociaux constituent l’un des principaux motifs de l’utilisation du modèle « Big Data ». Les API publiques fournies par des sites comme Twitter représentent une source de données utile pour l'analyse et la compréhension des tendances populaires. Dans ce didacticiel, vous allez recevoir des tweets à l’aide de l’API de diffusion Twitter, puis utiliser Apache Hive sur Azure HDInsight pour récupérer une liste des utilisateurs de Twitter ayant envoyé le plus de tweets contenant un mot donné.

> [AZURE.NOTE]Un exemple du même type est présent dans la galerie d’exemples de HDInsight. Regardez la vidéo de Channel 9 : <a href="http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Analyze-Twitter-trend-using-Apache-Hive-in-HDInsight" target="_blank">Analyse des tendances de Twitter à l’aide d’Apache Hive dans HDInsight</a>.

##Configuration requise
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- Un **poste de travail** sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install]. Pour exécuter des scripts Windows PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et définir la stratégie d’exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][powershell-script].

	Avant d’exécuter vos scripts Windows PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l’aide de l’applet de commande suivante :

		Add-AzureAccount

	Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

		Select-AzureSubscription <AzureSubscriptionName>



- Un **cluster Azure HDInsight**. Pour obtenir des instructions sur l’approvisionnement des clusters, consultez la rubrique [Prise en main de HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight][hdinsight-provision]. Vous en aurez besoin plus loin dans le didacticiel.

**Présentation du stockage HDInsight**

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Le stockage d’objets blob Azure est l’implémentation par Microsoft de Hadoop Distributed File System (HDFS). Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Lorsque vous approvisionnez un cluster HDInsight, un conteneur de stockage d'objets blob est désigné comme système de fichiers par défaut, comme dans HDFS. Vous pouvez ajouter à ce conteneur d’autres conteneurs à partir du même compte de stockage Azure ou à partir de différents comptes de stockage Azure au cours du processus d’approvisionnement. Pour plus d’instructions sur l’ajout de comptes de stockage, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].

> [AZURE.NOTE]Pour simplifier le script Windows PowerShell utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur du système de fichiers par défaut, situé à l’emplacement */tutorials/twitter*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight. Si vous choisissez d'utiliser un conteneur différent pour stocker ces fichiers, mettez à jour le script en conséquence.

La syntaxe du stockage d’objets blob Azure est la suivante :

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]Seule la syntaxe *wasb://* est prise en charge dans le cluster HDInsight version 3.0. L’ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais n’est pas prise en charge dans les clusters HDInsight 3.0 et ne sera plus prise en charge dans les versions ultérieures.

> Le chemin d’accès au stockage d’objets blob Azure est virtuel. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

HDInsight peut accéder aux fichiers stockés dans le conteneur du système de fichiers par défaut via n’importe lequel des URI (Uniform Resource Identifier) suivants. Ces URI utilisent tweets.txt comme exemple.

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

	tutorials/twitter/tweets.txt

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

<table border="1">
<tr><th>Fichiers</th><th>Description</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Données source pour la tâche Hive.</td></tr>
<tr><td>/tutorials/twitter/output</td><td>Données de sortie pour la tâche Hive. Par défaut, le nom du fichier de sortie de la tâche Hive est <strong>000000_0</strong>. </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>Fichier de script HiveQL.</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>État de la tâche Hadoop.</td></tr>
</table>

##Obtention du flux Twitter

Dans ce didacticiel, vous allez utiliser les [API de diffusion Twitter][twitter-streaming-api]. L’API de diffusion Twitter spécifique que vous allez utiliser est [statuses/filter][twitter-statuses-filter].

>[AZURE.NOTE]Un fichier contenant 10 000 tweets et le fichier de script Hive (traité dans la section suivante) ont été téléchargés dans un conteneur d'objets blob public. Vous pouvez ignorer cette section si vous souhaitez utiliser les fichiers téléchargés.

Les [données des tweets](https://dev.twitter.com/docs/platform-objects/tweets) sont stockées au format JSON (JavaScript Object Notation) qui contient une structure imbriquée complexe. Au lieu d’écrire de nombreuses lignes de code à l’aide d’un langage de programmation classique, vous pouvez transformer cette structure imbriquée en une table Hive, de sorte qu’un langage de type SQL, appelé HiveQL, puisse effectuer une requête sur celle-ci.

Twitter utilise OAuth pour fournir un accès autorisé à son API. OAuth est un protocole d’authentification qui permet aux utilisateurs d’autoriser des applications à agir à leur place sans partager leur mot de passe. Pour plus d'informations, consultez la page [oauth.net](http://oauth.net/) ou l'excellent [Guide du débutant sur OAuth](http://hueniverse.com/oauth/) de Hueniverse.

Pour utiliser OAuth, la première étape consiste à créer une nouvelle application sur le site du développeur Twitter.

**Pour créer une application Twitter**

1. Connectez-vous à [https://apps.twitter.com/](https://apps.twitter.com/). Cliquez sur le lien **Sign up now** si vous ne possédez pas de compte Twitter.
2. Cliquez sur **Create New App**.
3. Renseignez les champs **Name**, **Description** et **Website**. Vous pouvez créer une URL pour le champ **Website**. Le tableau suivant affiche quelques exemples de valeurs à utiliser :

	<table border="1">
<tr><th>Champ</th><th>Valeur</th></tr>
<tr><td>Nom</td><td>MyHDInsightApp</td></tr>
<tr><td>Description</td><td>MyHDInsightApp</td></tr>
<tr><td>Site Web</td><td>http://www.myhdinsightapp.com</td></tr>
</table>
4. Activez la case à cocher **Yes, I agree**, puis cliquez **Create your Twitter application**.
5. Cliquez sur l'onglet **Permissions**. L'autorisation par défaut est **Read only**. Ces étapes sont suffisantes pour ce didacticiel. 
6. Cliquez sur l’onglet **Keys and Access Tokens**.
7. Cliquez sur **Create my access token**.
8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.
9. Renseignez les valeurs **consumer key**, **Consumer secret**, **Access token** et **Access token secret**. Vous en aurez besoin plus loin dans le didacticiel.

Dans ce didacticiel, vous allez utiliser Windows PowerShell pour effectuer un appel de service web. Pour obtenir un exemple .NET en C#, consultez la rubrique [Analyse des sentiments Twitter en temps réel avec HBase dans HDInsight][hdinsight-hbase-twitter-sentiment]. L'autre outil connu permettant d'effectuer des appels de service Web est [*Curl*][curl]. Vous pouvez le télécharger [ici][curl-download].

>[AZURE.NOTE]Lorsque vous utilisez la commande Curl sous Windows, remplacez les guillemets simples par des guillemets doubles pour exprimer la valeur des options.

**Pour récupérer des tweets**

1. Ouvrez l’environnement d’écriture de scripts intégré de Windows PowerShell (ISE). (Sur l’écran d’accueil Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start].)

2. Copiez le script suivant dans le volet du script :

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name
		
		# Enter the OAuth information for your Twitter application
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		$destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob. 
		
		$trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
		#endregion
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writing tweets into Blob storage
		Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		#end region
				
		# region - Format OAuth strings	
		Write-Host "Format oauth strings ..." -ForegroundColor Green
		$oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
		$ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
		$oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();
				
		$signature = "POST&";
		$signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
		$signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&"); 
		$signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
		$signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
		$signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
		$signature += [System.Uri]::EscapeDataString("track=" + $track);
				
		$signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);
				
		$hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
		$hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
		$oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));
				
		$oauth_authorization = 'OAuth ';
		$oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
		$oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
		$oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
		$oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
		$oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
		$oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
		$oauth_authorization += 'oauth_version="1.0"';
				
		$post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track); 
		#endregion 
					
		#region - Read tweets	
		Write-Host "Create HTTP web request ..." -ForegroundColor Green
		[System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
		$request.Method = "POST";
		$request.Headers.Add("Authorization", $oauth_authorization);
		$request.ContentType = "application/x-www-form-urlencoded";
		$body = $request.GetRequestStream();
				
		$body.write($post_body, 0, $post_body.length);
		$body.flush();
		$body.close();
		$response = $request.GetResponse() ;
				
		Write-Host "Start stream reading ..." -ForegroundColor Green
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
				
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
				
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
			if ($inrec -ne "")
			{
				Write-Host "`n`t $count tweets received." -ForegroundColor Yellow
				
				$writeStream.WriteLine($inrec)
				$count ++
			}
				
			$inrec=$sReader.ReadLine()
		}
		#endregion
				
		#region - Write tweets to Blob storage
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
				
		$sReader.close()
		#endregion
		
		Write-Host "Completed!" -ForegroundColor Green

3. Définissez les cinq premières variables du script :

	<table border="1">
<tr><th>Variable</th><th>Description</th></tr>
<tr><td>$clusterName</td><td>Nom du cluster HDInsight où vous souhaitez exécuter l’application.</td></tr><tr><td>$oauth_consumer_key</td><td><strong>Clé</strong> que vous avez notée auparavant en créant l’application Twitter.</td></tr>
<tr><td>$oauth_consumer_secret</td><td><strong>Secret</strong> que vous avez écrit auparavant pour l'application Twitter.</td></tr>
<tr><td>$oauth_token</td><td><strong>Jeton d'accès</strong> que vous avez écrit auparavant pour l'application Twitter.</td></tr>
<tr><td>$oauth_token_secret</td><td><strong>Secret de jeton d'accès</strong> que vous avez écrit auparavant pour l'application Twitter.</td></tr>	
<tr><td>$destBlobName</td><td>Nom de l'objet blob de sortie. La valeur par défaut est <strong>tutorials/twitter/data/tweets.txt</strong>. Si vous modifiez la valeur par défaut, vous devez mettre à jour les scripts Windows PowerShell en conséquence.</td></tr>
<tr><td>$trackString</td><td>Le service Web renvoie les tweets liés à ces mots clés. La valeur par défaut est <strong>Azure, Cloud, HDInsight</strong>. Si vous modifiez la valeur par défaut, vous devez mettre à jour les scripts Windows PowerShell en conséquence.</td></tr>
<tr><td>$lineMax</td><td>La valeur détermine le nombre de tweets lus par le script. La lecture de 100&#160;tweets prend environ trois minutes. Vous pouvez définir un nombre plus important, mais le téléchargement prendra plus de temps.</td></tr>

</table>

5. Appuyez sur **F5** pour exécuter le script. Si vous êtes confronté à des problèmes, vous pouvez, pour les contourner, sélectionner toutes les lignes et appuyer ensuite sur **F8**.
6. Le message « Complete! » doit normalement s'afficher à la fin de la sortie. S’il y a un message d’erreur, il s’affiche en rouge.

Dans le cadre d’une procédure de validation, vous pouvez vérifier le fichier de sortie, **/tutorials/twitter/data/tweets.txt**, sur votre stockage d’objets blob Azure à l’aide d’un explorateur de stockage Azure ou d’Azure PowerShell. Pour obtenir un exemple de script Windows PowerShell pour répertorier les fichiers, consultez la rubrique [Utilisation du stockage d’objets blob avec HDInsight][hdinsight-storage-powershell].



##Création d'un script HiveQL

À l'aide d'Azure PowerShell, vous pouvez exécuter plusieurs instructions HiveQL, une par une, ou empaqueter l'instruction HiveQL dans un fichier de script. Dans ce didacticiel, vous allez créer un script HiveQL. Le fichier de script doit être téléchargé dans le stockage d’objets blob Azure. Dans la section suivante, vous allez exécuter le fichier de script à l’aide d’Azure PowerShell.

>[AZURE.NOTE]Le fichier de script Hive et un fichier contenant 10 000 tweets ont été téléchargés dans un conteneur d'objets Blob public. Vous pouvez ignorer cette section si vous souhaitez utiliser les fichiers téléchargés.

Le script HiveQL exécutera les opérations suivantes :

1. **Suppression de la table tweets_raw** au cas où la table existe déjà.
2. **Création de la table tweets_raw Hive**. Cette table Hive structurée et temporaire conserve les données en vue d’un traitement ETL ultérieur (extraction, modification et chargement). Pour plus d’informations sur les partitions, consultez la rubrique [Didacticiel Hive][apache-hive-tutorial].  
3. **Chargement des données** à partir du dossier source, /tutorials/twitter/data. Le volumineux jeu de données des tweets imbriqué dans le format JSON a été transformé en une structure de table Hive temporaire.
3. **Suppression de la table tweets**, le cas échéant.
4. **Création de la table tweets**. Avant de pouvoir effectuer une requête sur le jeu de données des tweets à l’aide de Hive, vous devez exécuter un autre processus ETL. Ce dernier définit un schéma de table plus détaillé pour les données que vous avez stockées dans la table « twitter_raw ».  
5. **Insertion de la table overwrite**. Ce script Hive complexe démarre un ensemble de longues tâches MapReduce sur le cluster Hadoop. Selon votre jeu de données et la taille de votre cluster, cela peut prendre environ 10 minutes.
6. **Insertion du répertoire overwrite**. Exécutez une requête et sortez le jeu de données dans un fichier. Cette requête renvoie une liste d’utilisateurs de Twitter dont la majorité des tweets envoyés contenaient le mot « Azure ».

**Pour créer un script Hive et le télécharger sur Azure**

1. Ouvrez Windows PowerShell ISE.
2. Copiez le script suivant dans le volet du script :

		#region - variables and constants
		$clusterName = "<HDInsightClusterName>" # Enter your HDInsight cluster name
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
				
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
				
		DROP TABLE tweets_raw;
		CREATE EXTERNAL TABLE tweets_raw (
			json_response STRING
		) 
		STORED AS TEXTFILE LOCATION '$sourceDataPath';
				
		DROP TABLE tweets;
		CREATE TABLE tweets
		(
			id BIGINT,
			created_at STRING,
			created_at_date STRING,
			created_at_year STRING,
			created_at_month STRING,
			created_at_day STRING,
			created_at_time STRING,
			in_reply_to_user_id_str STRING,
			text STRING,
			contributors STRING,
			retweeted STRING,
			truncated STRING,
			coordinates STRING,
			source STRING,
			retweet_count INT,
			url STRING,
			hashtags array<STRING>,
			user_mentions array<STRING>,
			first_hashtag STRING,
			first_user_mention STRING,
			screen_name STRING,
			name STRING,
			followers_count INT,
			listed_count INT,
			friends_count INT,
			lang STRING,
			user_location STRING,
			time_zone STRING,
			profile_image_url STRING,
			json_response STRING
		);
				
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		SELECT
			cast(get_json_object(json_response, '$.id_str') as BIGINT),
			get_json_object(json_response, '$.created_at'),
			concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
			substr (get_json_object(json_response, '$.created_at'),27,4)),
			substr (get_json_object(json_response, '$.created_at'),27,4),
			case substr (get_json_object(json_response,	'$.created_at'),5,3)
				when "Jan" then "01"
				when "Feb" then "02"
				when "Mar" then "03"
				when "Apr" then "04"
				when "May" then "05"
				when "Jun" then "06"
				when "Jul" then "07"
				when "Aug" then "08"
				when "Sep" then "09"
				when "Oct" then "10"
				when "Nov" then "11"
				when "Dec" then "12" end,
			substr (get_json_object(json_response, '$.created_at'),9,2),
			substr (get_json_object(json_response, '$.created_at'),12,8),
			get_json_object(json_response, '$.in_reply_to_user_id_str'),
			get_json_object(json_response, '$.text'),
			get_json_object(json_response, '$.contributors'),
			get_json_object(json_response, '$.retweeted'),
			get_json_object(json_response, '$.truncated'),
			get_json_object(json_response, '$.coordinates'),
			get_json_object(json_response, '$.source'),
			cast (get_json_object(json_response, '$.retweet_count') as INT),
			get_json_object(json_response, '$.entities.display_url'),
			array(	
				trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
				trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
			array(
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
				trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
			trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
			trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
			get_json_object(json_response, '$.user.screen_name'),
			get_json_object(json_response, '$.user.name'),
			cast (get_json_object(json_response, '$.user.followers_count') as INT),
			cast (get_json_object(json_response, '$.user.listed_count') as INT),
			cast (get_json_object(json_response, '$.user.friends_count') as INT),
			get_json_object(json_response, '$.user.lang'),
			get_json_object(json_response, '$.user.location'),
			get_json_object(json_response, '$.user.time_zone'),
			get_json_object(json_response, '$.user.profile_image_url'),
			json_response
		WHERE (length(json_response) > 500);
				
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		#endregion		
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Add-AzureAccount
		#endregion
			
		#region - Create a block blob object for writing the Hive script file
		Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
		$myCluster = Get-AzureHDInsightCluster -Name $clusterName
		$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
		$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
		Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
		Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
				
		Write-Host "Define the connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
				
		Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)
				
		Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		$writeStream.Writeline($hqlStatements)
		#endregion
		
		#region - Write the Hive script file to Blob storage		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		#endregion
				
		Write-Host "Completed!" -ForegroundColor Green


4. Définissez les deux premières variables du script :

	<table border="1">
<tr><th>Variable</th><th>Description</th></tr>
<tr><td>$clusterName</td><td>Entrez le nom du cluster HDInsight où vous souhaitez exécuter l'application.</td></tr>
<tr><td>$sourceDataPath</td><td>Emplacement de stockage d’objets blob Azure où les requêtes Hive lisent les données. Vous n'avez pas besoin de modifier cette variable.</td></tr>
<tr><td>$outputPath</td><td>Emplacement de stockage d’objets blob Azure où les requêtes Hive envoient les résultats. Vous n'avez pas besoin de modifier cette variable.</td></tr>
<tr><td>$hqlScriptFile</td><td>Emplacement et nom du fichier de script HiveQL. Vous n'avez pas besoin de modifier cette variable.</td></tr>
</table>

5. Appuyez sur **F5** pour exécuter le script. Si vous êtes confronté à des problèmes, vous pouvez, pour les contourner, sélectionner toutes les lignes et appuyer ensuite sur **F8**.
6. Le message « Complete! » doit normalement s'afficher à la fin de la sortie. S’il y a un message d’erreur, il s’affiche en rouge.

Dans le cadre d’une procédure de validation, vous pouvez vérifier le fichier de sortie, **/tutorials/twitter/twitter.hql**, sur votre stockage d’objets blob Azure à l’aide d’un explorateur de stockage Azure ou d’Azure PowerShell. Pour obtenir un exemple de script Windows PowerShell pour répertorier les fichiers, consultez la rubrique [Utilisation du stockage d’objets blob avec HDInsight][hdinsight-storage-powershell].


##Traitement des données Twitter à l’aide de Hive

Vous avez terminé tout le travail de préparation. Vous pouvez à présent appeler le script Hive et vérifier les résultats.

### Soumettre un travail Hive

Utilisez le script Windows PowerShell suivant pour exécuter le script Hive. Vous devez définir la première variable.

>[AZURE.NOTE]Pour utiliser les tweets et le script HiveQL que vous avez téléchargé dans les deux dernières sections, définissez la valeur $hqlScriptFile sur "/ tutorials/twitter/twitter.hql". Pour utiliser ceux qui ont été téléchargés pour vous, vers un objet blob public, définissez $hqlScriptFile sur "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	#use one of the following
	$hqlScriptFile = "wasbs://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"

	$statusFolder = "/tutorials/twitter/jobstatus"
	#endregion
	
	#region - Invoke Hive
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 
	#endregion

### Vérification des résultats

Exécutez le script Windows PowerShell suivant pour vérifier la sortie de la tâche Hive. Vous devez définir les deux premières variables.

	#region variables and constants
	$clusterName = "<HDInsightClusterName>"
	
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	#engregion
	
	#region - Create an Azure storage context object
	Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
	$myCluster = Get-AzureHDInsightCluster -Name $clusterName
	$storageAccountName = $myCluster.DefaultStorageAccount.StorageAccountName.Replace(".blob.core.windows.net", "")	
	$containerName = $myCluster.DefaultStorageAccount.StorageContainerName
	Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
	Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	#endregion 
	
	#region - Download blob and display blob
	Write-Host "Download the blob ..." -ForegroundColor Green
	cd $HOME
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	Write-Host "==================================" -ForegroundColor Green
	cat "./$blob"
	Write-Host "==================================" -ForegroundColor Green
	#end region

> [AZURE.NOTE]La table Hive utilise \\001 comme délimiteur de champ. Le délimiteur n'est pas visible dans la sortie.

Une fois que les résultats d’analyse ont été placés dans le stockage d’objets blob Azure, vous pouvez exporter les données dans la base de données Azure SQL/le serveur SQL, exporter les données dans Excel à l’aide de Power Query ou connecter votre application aux données à l’aide du pilote ODBC Hive. Pour plus d’informations, consultez les rubriques [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop], [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-delay-data], [Connexion d’Excel à HDInsight à l’aide de Power Query][hdinsight-power-query] et [Connexion d’Excel à HDInsight à l’aide du pilote ODBC Microsoft Hive][hdinsight-hive-odbc].

##Étapes suivantes

Dans ce didacticiel, nous avons vu comment transformer le jeu de données JSON non structuré en une table Hive structurée pour effectuer une requête sur les données, les explorer et les analyser à partir de Twitter à l’aide de HDInsight sur Azure. Pour plus d'informations, consultez les rubriques suivantes :

- [Prise en main de HDInsight][hdinsight-get-started]
- [Analyse des sentiments Twitter en temps réel avec HBase dans HDInsight][hdinsight-hbase-twitter-sentiment]
- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-delay-data].
- [Connexion d’Excel à HDInsight à l’aide de Power Query][hdinsight-power-query]
- [Connexion d’Excel à HDInsight à l’aide du pilote ODBC Microsoft Hive][hdinsight-hive-odbc]
- [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-storage-powershell]: hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

<!--HONumber=54-->