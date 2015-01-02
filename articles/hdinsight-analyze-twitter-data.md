<properties urlDisplayName="Analyze Twitter data with HDInsight Hadoop" pageTitle="Analyse des données Twitter avec Hadoop dans HDInsight | Azure" metaKeywords="" description="Learn how to use Hive to analyze Twitter data on Hadoop in HDInsight to find the usage frequency of a particular word." metaCanonical="" services="HDInsight" documentationCenter="" title="Analyze Twitter data with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="jgao" />

# Analyse des données Twitter avec Hadoop dans HDInsight

Découvrez comment analyser les données Twitter en utilisant Hive dans HDInsight.

Les sites web sociaux constituent l'un des principaux motifs de l'utilisation du modèle " Big Data ". Les API publiques fournies par des sites comme Twitter représentent une source de données utile pour l'analyse et la compréhension des tendances populaires. Dans ce didacticiel, vous allez vous connecter au service web Twitter pour obtenir quelques Tweets à l'aide de l'API de diffusion Twitter, puis utiliser Hive pour récupérer une liste d'utilisateurs Twitter qui ont envoyé des Tweets contenant un mot donné.

**Durée de réalisation estimée :** 30 minutes

##Dans cet article

- [Configuration requise](#prerequisites)
- [Obtention du flux Twitter](#feed)
- [Création d'un script HiveQL](#script)
- [Traitement des données à l'aide de Hive](#process)
- [Nettoyage du didacticiel](#cleanup)
- [Étapes suivantes](#nextsteps)

##<a id="prerequisites"></a>Conditions préalables
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail** sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install]. Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][powershell-script].

	Avant d'exécuter vos scripts PowerShell, assurez-vous que vous êtes connecté à votre abonnement Azure à l'aide de la cmdlet suivante :

		Add-AzureAccount

	Si vous possédez plusieurs abonnements Azure, utilisez la cmdlet suivante pour définir l'abonnement en cours :

		Select-AzureSubscription <AzureSubscriptionName>



- **Un cluster Azure HDInsight**. Pour obtenir des instructions sur l'approvisionnement des clusters, consultez les rubriques [Prise en main de HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight][hdinsight-provision]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

	<table border="1">
	<tr><th>Propriété du cluster</th><th>Nom de la variable PowerShell</th><th>Valeur</th><th>Description</th></tr>
	<tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Nom du cluster HDInsight.</td></tr>
	<tr><td>Nom du compte de stockage Azure</td><td>$storageAccountName</td><td></td><td>Il s'agit du compte de stockage Azure disponible sur le cluster HDInsight. Pour ce didacticiel, utilisez le compte de stockage par défaut indiqué au cours du processus d'approvisionnement du cluster.</td></tr>
	<tr><td>Nom du conteneur d'objets blob Azure</td><td>$containerName</td><td></td><td>Dans cet exemple, utilisez le conteneur de stockage d'objets blob Azure utilisé pour le système de fichiers de cluster HDInsight par défaut. Par défaut, il porte le même nom que le cluster HDInsight.</td></tr>
	</table>

**Présentation du stockage HDInsight**

HDInsight utilise le stockage d'objets blob Azure pour stocker des données.  Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage]. 

Lorsque vous approvisionnez un cluster HDInsight, un conteneur de stockage d'objets blob est désigné comme système de fichiers par défaut, comme dans HDFS. Outre ce conteneur, vous pouvez ajouter des conteneurs supplémentaires à partir du même compte de stockage Azure ou de différents comptes de stockage Azure au cours du processus d'approvisionnement. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision]. 

> [WACOM.NOTE] Pour simplifier le script PowerShell utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur du système de fichiers par défaut, situé à l'emplacement */tutorials/twitter*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight. Si vous choisissez d'utiliser un conteneur différent pour stocker ces fichiers, mettez à jour le script en conséquence.

La syntaxe WASB est :

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Seule la syntaxe *wasb://* est prise en charge dans le cluster HDInsight version 3.0. L'ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais pas dans les clusters HDInsight 3.0, et elle ne sera pas prise en charge dans les versions ultérieures.

> Le chemin d'accès WASB est le chemin d'accès virtuel.  Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage]. 

Prenons un fichier stocké dans le conteneur du système de fichiers par défaut : tous les URI suivants permettent d'y accéder à partir de HDInsight (utiliser tweets.txt comme exemple) :

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/twitter/tweets.txt
	wasb:///tutorials/twitter/tweets.txt
	/tutorials/twitter/tweets.txt

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

	tutorials/twitter/tweets.txt

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

<table border="1">
<tr><th>Fichiers</th><th>Description</th></tr>
<tr><td>/tutorials/twitter/data/tweets.txt</td><td>Données source pour la tâche Hive.</td></tr>
<tr><td>/tutorials/twitter/output</td><td>Données de sortie pour la tâche Hive. Le nom de fichier de sortie de la tâche Hive du fichier par défaut est <strong>000000_0</strong>. </td></tr>
<tr><td>tutorials/twitter/twitter.hql</td><td>Fichier de script HiveQL.</td></tr>
<tr><td>/tutorials/twitter/jobstatus</td><td>État de la tâche Hadoop.</td></tr>
</table>

##<a id="feed"></a>Obtention du flux Twitter

Dans ce didacticiel, vous allez utiliser les [API de diffusion Twitter][twitter-streaming-api]. L'API de diffusion Twitter spécifique que vous allez utiliser est [statuses/filter][twitter-statuses-filter].

[Les données Tweets](https://dev.twitter.com/docs/platform-objects/tweets) sont stockées au format JSon qui contient une structure imbriquée complexe. Au lieu d'écrire de nombreuses lignes de code à l'aide d'un langage de programmation classique, vous pouvez transformer cette structure imbriquée en une table Hive, de sorte qu'un langage de type SQL appelé HiveQL puisse effectuer une requête sur celle-ci. 

Twitter utilise OAuth pour fournir un accès autorisé à son API. OAuth est un protocole d'authentification qui permet aux utilisateurs d'approuver l'application afin qu'elle agisse à leur place sans partager leur mot de passe. Des informations supplémentaires sont disponibles sur [oauth.net](http://oauth.net/) ou dans l'excellent [Guide du débutant sur OAuth](http://hueniverse.com/oauth/) de Hueniverse.

Pour utiliser OAuth, la première étape consiste à créer une nouvelle application sur le site du développeur Twitter.

**Pour créer une application Twitter**

1. Connectez-vous à [https://apps.twitter.com/](https://apps.twitter.com/). Cliquez sur le lien **Sign up now** si vous ne disposez pas d'un compte Twitter.
2. Cliquez sur **Create New App**.
3. Entrez un **Nom**, une **Description** et un **Site web**. Vous pouvez créer une URL pour le champ du site Web. Le tableau suivant affiche quelques exemples de valeurs à utiliser :

	<table border="1">
	<tr><th>Champ</th><th>Valeur</th></tr>
	<tr><td>Nom</td><td>MyHDInsightApp</td></tr>
	<tr><td>Description</td><td>MyHDInsightApp</td></tr>
	<tr><td>Site Web</td><td>http://www.myhdinsightapp.com</td></tr>
	</table>
4. Activez la case à cocher **Yes, I agree**, puis cliquez sur **Create your Twitter application**.
5. Cliquez sur l'onglet **Permissions**. L'autorisation par défaut est **Read only**. Ces étapes sont suffisantes pour ce didacticiel. 
6. Cliquez sur l'onglet **API Keys**.
7. Cliquez sur **Create my access token**.
8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.
9. Notez les valeurs **consumer key**, **Consumer secret**, **Access token** et **Access token secret**. Vous en aurez besoin plus loin dans le didacticiel.

Dans ce didacticiel, vous allez utiliser PowerShell pour effectuer un appel de service Web. L'autre outil connu permettant d'effectuer des appels de service web est [*Curl*][curl]. Vous pouvez le télécharger [ici][curl-download].

>[WACOM.NOTE] Lorsque vous utilisez la commande curl sur Windows, remplacez les guillemets doubles par des guillemets simples pour exprimer la valeur des options.

**Pour récupérer des Tweets**

1. Ouvrez Windows PowerShell ISE (dans l'écran d'accueil Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).

3. Copiez le script suivant dans le volet du script :

		Write-Host "Set variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$destBlobName = "tutorials/twitter/data/tweets.txt"
		
		$trackString = "Azure, Cloud, HDInsight"
		$track = [System.Uri]::EscapeDataString($trackString);
		$lineMax = 100  #about 3 minutes every 100 lines
		
		$oauth_consumer_key = "<TwitterAppConsumerKey>";
		$oauth_consumer_secret = "<TwitterAppConsumerSecret>";
		$oauth_token = "<TwitterAppAccessToken>";
		$oauth_token_secret = "<TwitterAppAccessTokenSecret>";
		
		Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		Write-Host "Create block blob object ..." -ForegroundColor Green
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
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
		
		$sReader = New-Object System.IO.StreamReader($response.GetResponseStream())
		
		$inrec = $sReader.ReadLine()
		$count = 0
		while (($inrec -ne $null) -and ($count -le $lineMax))
		{
		    if ($inrec -ne "")
		    {
		        Write-Host $count.ToString() ", " -NoNewline -ForegroundColor Green
		        if ($count%10 -eq 0){
		            write-host " --- " -NoNewline
		            Get-Date -Format hh:mm:sstt
		        }
		
		        $writeStream.WriteLine($inrec)
		        $count ++
		    }
		
		    $inrec=$sReader.ReadLine()
		}
		
		Write-Host "Write to the destination blob ..." -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream) 
		
		$sReader.close()
		Write-Host "Complete!" -ForegroundColor Green

4. Définissez les neuf premières variables du script :	

	<table border="1">
	<tr><th>Variable</th><th>Description</th></tr>
	<tr><td>$storageAccountName</td><td>Compte de stockage Azure utilisé en tant que système de fichiers du cluster HDInsight par défaut. Il s'agit du compte de stockage spécifié lors de l'approvisionnement. Consultez <a href="#prerequisites">Conditions préalables</a>.</td></tr>
	<tr><td>$containerName</td><td>Conteneur d'objets blob utilisé en tant que système de fichiers du cluster HDInsight par défaut. Il s'agit du conteneur spécifié lors de l'approvisionnement. Consultez <a href="#prerequisites">Conditions préalables</a>.</td></tr>
	<tr><td>$destBlobName</td><td>Nom de l'objet blob de sortie.  La valeur par défaut est <strong>tutorials/twitter/data/tweets.txt</strong>. Si vous modifiez la valeur par défaut, vous devez mettre à jour les scripts PowerShell en conséquence.</td></tr>
	<tr><td>$trackString</td><td>Le service Web renvoie les tweets liés à ces mots clés.  La valeur par défaut est <strong>Azure, Cloud, HDInsight</strong>. Si vous modifiez la valeur par défaut, vous devez mettre à jour les scripts PowerShell en conséquence.</td></tr>
	<tr><td>$lineMax</td><td>La valeur détermine le nombre de tweets lus par le script. La lecture de 100 tweets prend environ trois minutes. Vous pouvez définir un nombre plus important, mais le téléchargement prendra plus de temps.</td></tr>
	<tr><td>$oauth_consumer_key</td><td>Il s'agit de la valeur <strong>consumer key</strong> que vous avez notée lorsque vous avez créé l'application Twitter.</td></tr>
	<tr><td>$oauth_consumer_secret</td><td>Il s'agit de la valeur <strong>consumer secret</strong> que vous avez notée précédemment.</td></tr>
	<tr><td>$oauth_token</td><td>Il s'agit de la valeur <strong>access token</strong> que vous avez notée précédemment.</td></tr>
	<tr><td>$oauth_token_secret</td><td>Il s'agit de la valeur <strong>access token secret</strong> que vous avez notée précédemment.</td></tr>
	</table>

5. Appuyez sur **F5** pour exécuter le script. Si vous êtes confronté à des problèmes, vous pouvez, pour les contourner, sélectionner toutes les lignes et appuyer ensuite sur **F8**.
6. Le message " Complete! " doit normalement s'afficher à la fin de la sortie. S'il y a un message d'erreur, il s'affiche en rouge.

Dans le cadre d'une procédure de validation, vous pouvez vérifier le fichier de sortie, **/tutorials/twitter/data/tweets.txt**, sur votre stockage d'objets blob Azure à l'aide d'un explorateur de stockage Azure ou d'Azure PowerShell.  Pour obtenir un exemple de script PowerShell pour répertorier les fichiers, consultez la rubrique [Utilisation du stockage d'objets blob avec HDInsight][hdinsight-storage-powershell]. 



##<a id="script"></a>Création d'un script HiveQL

À l'aide d'Azure PowerShell, vous pouvez exécuter plusieurs instructions HiveQL, une par une, ou empaqueter l'instruction HiveQL dans un fichier de script. Dans ce didacticiel, vous allez créer un script HiveQL. Le fichier de script doit être téléchargé sur WASB. Dans la section suivante, vous allez exécuter le fichier de script à l'aide d'Azure PowerShell.

Le script HiveQL exécutera les opérations suivantes :

1. **Suppression de la table tweets_raw** le cas échéant.
2. **Création de la table tweets_raw Hive**. Cette table structurée Hive temporaire conserve les données en vue d'un traitement ETL ultérieur.  Pour plus d'informations sur la partition, consultez la page [Didacticiel Hive][apache-hive-tutorial].  
3. **Chargement des données** à partir du dossier source, /tutorials/twitter/data. Le jeu de données Tweets volumineux imbriqué dans le format JSon a été transformé en une structure de table Hive temporaire.
3. **Suppression de la table tweets** le cas échéant.
4. **Création de la table tweets**. Avant de pouvoir effectuer une requête sur les jeux de données Tweets à l'aide de Hive, vous devez exécuter un autre processus ETL. Ce dernier définit un schéma de table plus détaillé pour les données que vous avez stockées dans la table " twitter_raw ".  
5. **Insertion de la table overwrite**. Ce script Hive complexe démarre un ensemble de tâches MapReduce sur le cluster Hadoop.  Selon votre ensemble de données et la taille de votre cluster, cela peut prendre environ 10 minutes.
6. **Insertion du répertoire overwrite**. Exécutez une requête et sortez le jeu de données dans un fichier. Cette requête renvoie une liste d'utilisateurs Twitter qui ont envoyé la plupart des tweets contenant le mot " Azure ".

**Pour créer un script Hive et le télécharger sur Azure**

1. Ouvrez Windows PowerShell ISE.
2. Copiez le script suivant dans le volet du script :

		Write-Host "Define variables ..." -ForegroundColor Green
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceDataPath = "/tutorials/twitter/data"
		$outputPath = "/tutorials/twitter/output"
		
		$hqlScriptFile = "tutorials/twitter/twitter.hql"
		
		$hqlStatements = @"
		set hive.exec.dynamic.partition = true;
		set hive.exec.dynamic.partition.mode = nonstrict;
		
		DROP TABLE tweets_raw;
		CREATE TABLE tweets_raw (
		    json_response STRING
		) 
		PARTITIONED BY (filesequence INT);
		
		LOAD DATA INPATH '$sourceDataPath'
		INTO TABLE tweets_raw
		PARTITION (filesequence = 1);
		
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
		)
		PARTITIONED BY (filesequence INT);
		
		FROM tweets_raw
		INSERT OVERWRITE TABLE tweets
		PARTITION (filesequence)
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
			json_response,
			filesequence
		WHERE (length(json_response) > 500);
		
		INSERT OVERWRITE DIRECTORY '$outputPath'
		SELECT name, screen_name, count(1) as cc 
			FROM tweets 
			WHERE text like "%Azure%" 
			GROUP BY name,screen_name 
			ORDER BY cc DESC LIMIT 10;
		"@
		
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
		
		Write-Host "Write to the destination blob ... " -ForegroundColor Green
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$hqlScriptBlob.UploadFromStream($memStream)
		
		Write-Host "Complete!" -ForegroundColor Green


4. Définissez les deux premières variables du script :	

	<table border="1">
	<tr><th>Variable</th><th>Description</th></tr>
	<tr><td>$storageAccountName</td><td>Compte de stockage Azure utilisé en tant que système de fichiers du cluster HDInsight par défaut. Il s'agit du compte de stockage spécifié lors de l'approvisionnement. Consultez <a href="#prerequisites">Conditions préalables</a>.</td></tr>
	<tr><td>$containerName</td><td>Conteneur d'objets blob utilisé en tant que système de fichiers du cluster HDInsight par défaut. Il s'agit du conteneur spécifié lors de l'approvisionnement. Consultez <a href="#prerequisites">Conditions préalables</a>.</td></tr>
	<tr><td>$sourceDataPath</td><td>Emplacement WASB où les requêtes Hive lisent les données. Vous n'avez pas besoin de modifier cette variable.</td></tr>
	<tr><td>$outputPath</td><td>Emplacement WASB où les requêtes Hive sortent les résultats. Vous n'avez pas besoin de modifier cette variable.</td></tr>
	<tr><td>$hqlScriptFile</td><td>Emplacement et nom du fichier de script HiveQL.</td></tr>
	</table>

5. Appuyez sur **F5** pour exécuter le script. Si vous êtes confronté à des problèmes, vous pouvez, pour les contourner, sélectionner toutes les lignes et appuyer ensuite sur **F8**.
6. Le message " Complete! " doit normalement s'afficher à la fin de la sortie. S'il y a un message d'erreur, il s'affiche en rouge.

Dans le cadre d'une procédure de validation, vous pouvez vérifier le fichier de sortie, **/tutorials/twitter/twitter.hql**, sur votre stockage d'objets blob Azure à l'aide d'un explorateur de stockage Azure ou d'Azure PowerShell.  Pour obtenir un exemple de script PowerShell pour répertorier les fichiers, consultez la rubrique [Utilisation du stockage d'objets blob avec HDInsight][hdinsight-storage-powershell].  


##<a name="process"></a> Traitement des données à l'aide de Hive

Vous avez terminé tout le travail de préparation. Vous pouvez à présent appeler le script Hive et vérifier les résultats.

### Envoi d'une tâche Hive

Utilisez le script PowerShell suivant pour exécuter le script Hive. Vous devez définir la première variable.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$clusterName = "<HDInsightClusterName>"
	
	$hqlScriptFile = "/tutorials/twitter/twitter.hql"
	$statusFolder = "/tutorials/twitter/jobstatus"
	
	Write-Host "Invoke Hive ... " -ForegroundColor Green
	Use-AzureHDInsightCluster $clusterName
	$response = Invoke-Hive -file $hqlScriptFile -StatusFolder $statusFolder -OutVariable $outVariable
	
	Write-Host "Display the standard error log ... " -ForegroundColor Green
	$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace '\s*$' -replace '.*\s'
	Get-AzureHDInsightJobOutput -cluster $clusterName -JobId $jobID -StandardError 

### Vérification des résultats

Exécutez le script PowerShell suivant pour vérifier la sortie de la tâche Hive. Vous devez définir les deux premières variables.

	Write-Host "Set the variables ... " -ForegroundColor Green
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "Display the output ..." -ForegroundColor Green
	cat "./$blob"

> [WACOM.NOTE] La table Hive utilise \001 comme délimiteur de champ. Le délimiteur n'est pas visible dans la sortie. 

Une fois que les résultats d'analyse ont été placés sur WASB, vous pouvez exporter les données dans la base de données Azure SQL/SQL Server, exporter les données dans Excel à l'aide de Power Query, ou connecter votre application aux données à l'aide du pilote ODBC Hive.  Pour plus d'informations, consultez les pages [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop], [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-delay-data], [Connexion d'Excel à HDInsight à l'aide de Power Query][hdinsight-power-query] et [Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive][hdinsight-hive-odbc].
   

##<a id="cleanup"></a>Nettoyage du didacticiel

Si vous voulez réexécuter le didacticiel, vous devez :

- **Recréer le fichier de données Tweets**. Le fichier de données Tweets source a été supprimé par la tâche Hive.  Vous devez en générer un autre.  Le nom de fichier est **tutorials/twitter/data/tweets.txt**. Consultez la page [Obtention du flux Twitter](#feed).

##<a id="nextsteps"></a>Étapes suivantes

Dans ce didacticiel, nous avons vu comment transformer le jeu de données Json non structuré en une table Hive structurée pour effectuer une requête sur les données, les explorer et les analyser à partir de Twitter à l'aide de HDInsight sur Azure. Pour plus d'informations, consultez les rubriques suivantes :

- [Prise en main de HDInsight][hdinsight-get-started]
- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-delay-data]
- [Connexion d'Excel à HDInsight à l'aide de Power Query][hdinsight-power-query]
- [Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive][hdinsight-hive-odbc]
- [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/fr-fr/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/fr-fr/library/ee176961.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/


<!--HONumber=35_1-->
