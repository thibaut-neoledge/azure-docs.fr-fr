<properties
	pageTitle="Analyse des données Twitter avec Apache Hive dans HDInsight | Microsoft Azure"
	description="Découvrez comment utiliser Python pour stocker des tweets qui contiennent des mots clés spécifiques, puis utiliser Hive et Hadoop dans HDInsight pour transformer les données TWitter brutes en une table Hive pouvant faire l’objet d’une recherche."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="larryfr"/>

# Analyse des données Twitter avec Hive dans HDInsight

Dans ce document, vous allez recevoir des tweets à l'aide de l'API de diffusion Twitter, puis utiliser Apache Hive sur un cluster HDInsight sous Linux pour traiter les données au format JSON. Une liste des utilisateurs de Twitter ayant envoyé le plus de tweets contenant un mot donné vous sera ensuite retournée.

> [AZURE.NOTE] Bien que des éléments individuels de ce document puissent être utilisés avec des clusters HDInsight Windows (Python, par exemple), plusieurs étapes de ce document sont basées sur les clusters HDInsight Linux. Pour les étapes spécifiques à un cluster Windows, consultez la rubrique [Analyse des données Twitter avec Hive dans HDInsight](hdinsight-analyze-twitter-data.md).

###Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- Un __cluster HDInsight sous Linux__ Pour plus d'informations sur la création d'un cluster, consultez la rubrique [Prise en main de HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

- Un __client SSH__ Pour plus d’informations sur l’utilisation de SSH avec HDInsight sous Linux, consultez les articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- __Python__ et [pip](https://pypi.python.org/pypi/pip)

##Obtention du flux Twitter

Twitter vous permet de récupérer les [données de chaque tweet](https://dev.twitter.com/docs/platform-objects/tweets) sous la forme d’un document JavaScript Object Notation (JSON) via une API REST. [OAuth](http://oauth.net) est requis pour l'authentification de l'API. Vous devez également créer une _Application Twitter_ qui contient les paramètres utilisés pour accéder à l'API.

###Création d'une application Twitter

1. Depuis un navigateur Web, connectez-vous à [https://apps.twitter.com/](https://apps.twitter.com/). Cliquez sur le lien **Sign up now** si vous ne possédez pas de compte Twitter.
2. Cliquez sur **Create New App**.
3. Renseignez les champs **Name**, **Description** et **Website**. Vous pouvez créer une URL pour le champ **Website**. Le tableau suivant affiche quelques exemples de valeurs à utiliser :

	| Champ | Valeur |
	|:----- |:----- |
	| Nom | MyHDInsightApp |
	| Description | MyHDInsightApp |
	| Site Web | http://www.myhdinsightapp.com |
	
4. Activez la case à cocher **Yes, I agree**, puis cliquez **Create your Twitter application**.
5. Cliquez sur l'onglet **Permissions**. L'autorisation par défaut est **Read only**. Ces étapes sont suffisantes pour ce didacticiel.
6. Cliquez sur l’onglet **Keys and Access Tokens**.
7. Cliquez sur **Create my access token**.
8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.
9. Renseignez les valeurs **consumer key**, **Consumer secret**, **Access token** et **Access token secret**. Vous en aurez besoin un peu plus tard.

>[AZURE.NOTE] Lorsque vous utilisez la commande Curl sous Windows, remplacez les guillemets simples par des guillemets doubles pour exprimer la valeur des options.

###Téléchargement de tweets

Le code Python suivant téléchargera 10 000 tweets à partir de Twitter et les enregistrera dans un fichier nommé __tweets.txt__.

> [AZURE.NOTE] Les étapes suivantes sont effectuées sur le cluster HDInsight, puisque Python a déjà été installé.

1. Connectez-vous au cluster HDInsight à l’aide de SSH :

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple : `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Pour plus d’informations sur l’utilisation de SSH avec HDInsight sous Linux, consultez les articles suivants :
	
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows)
	
2. Par défaut, l’utilitaire __pip__ n'est pas installé sur le nœud principal HDInsight. Utilisez la commande suivante pour l’installer, puis mettez-le à jour :

		sudo apt-get install python-pip
		sudo pip install --upgrade pip

3. Le code vous permettant de télécharger des tweets s'appuie sur [Tweepy](http://www.tweepy.org/) et [Progressbar](https://pypi.python.org/pypi/progressbar/2.2). Utilisez la commande suivante pour procéder à l’installation :

		sudo apt-get install python-dev libffi-dev libssl-dev
		sudo apt-get remove python-openssl
		sudo pip install tweepy progressbar pyOpenSSL requests[security]
		
	> [AZURE.NOTE] Les bits concernant la suppression de python-openssl, l'installation de python-dev, libffi-dev, libssl-dev, pyOpenSSL et requests[security] permettent d’éviter qu’un avertissement InsecurePlatform survienne lors de la connexion à Twitter via SSL à partir de Python.
	>
	> Tweepy v3.2.0 vous permet d’éviter qu’[une erreur](https://github.com/tweepy/tweepy/issues/576) se produise lors du traitement des tweets.

4. Utilisez la commande suivante pour créer un fichier nommé __gettweets.py__ :

		nano gettweets.py

5. Utilisez les données suivantes comme contenu du fichier __gettweets.py__ : Remplacez les informations d’espace réservé de __consumer/_secret__, __consumer/_key_\_, __access/_token__ et __access/_token/_secret__ par les informations de votre application Twitter.

        #!/usr/bin/python

        from tweepy import Stream, OAuthHandler
        from tweepy.streaming import StreamListener
        from progressbar import ProgressBar, Percentage, Bar
        import json
        import sys

        #Twitter app information
        consumer_secret='Your consumer secret'
        consumer_key='Your consumer key'
        access_token='Your access token'
        access_token_secret='Your access token secret'

        #The number of tweets we want to get
        max_tweets=10000

        #Create the listener class that will receive and save tweets
        class listener(StreamListener):
            #On init, set the counter to zero and create a progress bar
            def __init__(self, api=None):
                self.num_tweets = 0
                self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

            #When data is received, do this
            def on_data(self, data):
                #Append the tweet to the 'tweets.txt' file
                with open('tweets.txt', 'a') as tweet_file:
                    tweet_file.write(data)
                    #Increment the number of tweets
                    self.num_tweets += 1
                    #Check to see if we have hit max_tweets and exit if so
                    if self.num_tweets >= max_tweets:
                        self.pbar.finish()
                        sys.exit(0)
                    else:
                        #increment the progress bar
                        self.pbar.update(self.num_tweets)
                return True

            #Handle any errors that may occur
            def on_error(self, status):
                print status

        #Get the OAuth token
        auth = OAuthHandler(consumer_key, consumer_secret)
        auth.set_access_token(access_token, access_token_secret)
        #Use the listener class for stream processing
        twitterStream = Stream(auth, listener())
        #Filter for these topics
        twitterStream.filter(track=["azure","cloud","hdinsight"])

6. Appuyez sur __Ctrl + X__, puis sur __Y__ pour enregistrer le fichier.

7. Utilisez la commande suivante pour exécuter le fichier et télécharger des tweets :

		python gettweets.py

	Un indicateur de progression doit apparaître et afficher « 100 % » au fur et à mesure que des tweets sont téléchargés et enregistrés dans le fichier.

    > [AZURE.NOTE] Si la barre de progression n’avance pas, vous devez modifier le filtre pour effectuer le suivi des rubriques tendances. Lorsqu’il existe un grand nombre de tweets concernant la rubrique que vous filtrez, vous pouvez obtenir rapidement les 10000 tweets nécessaires.

###Téléchargement des données

Pour télécharger les données vers WASB (le système de fichiers distribués utilisé par HDInsight), utilisez les commandes suivantes :

	hdfs dfs -mkdir -p /tutorials/twitter/data
	hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt

Ceci permet de stocker les données dans un emplacement accessible à tous les nœuds du cluster.

##Exécutez la tâche HiveQL


1. Utilisez la commande suivante pour créer un fichier contenant des instructions HiveQL :

		nano twitter.hql
	
	Utilisez les données suivantes comme contenu du fichier :

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode = nonstrict;
        -- Drop table, if it exists
        DROP TABLE tweets_raw;
        -- Create it, pointing toward the tweets logged from Twitter
        CREATE EXTERNAL TABLE tweets_raw (
            json_response STRING
        )
        STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
        -- Drop and recreate the destination table
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
        -- Select tweets from the imported data, parse the JSON,
        -- and insert into the tweets table
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
		
		
3. Appuyez sur __Ctrl + X__, puis sur __Y__ pour enregistrer le fichier.

4. Utilisez la commande suivante pour exécuter le HiveQL contenu dans le fichier :

		beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i twitter.hql		
		
	Ceci chargera le shell Hive, exécutera le HiveQL dans le fichier __twitter.hql__, et renverra ensuite une invite `jdbc:hive2//localhost:10001/>`.
	
5. À partir de l’invite Beeline, utilisez les commandes suivantes pour vérifier que les données peuvent être sélectionnées à partir de la table __tweets__, qui a été créée par le HiveQL dans le fichier __twitter.hql__ :
		
		SELECT name, screen_name, count(1) as cc
			FROM tweets
			WHERE text like "%Azure%"
			GROUP BY name,screen_name
			ORDER BY cc DESC LIMIT 10;

	Ceci renverra un maximum de 10 tweets contenant le mot __Azure__ dans le corps du message.

##Étapes suivantes

Dans ce didacticiel, nous avons vu comment transformer le jeu de données JSON non structuré en une table Hive structurée pour effectuer une requête sur les données, les explorer et les analyser à partir de Twitter à l’aide de HDInsight sur Azure. Pour plus d'informations, consultez les rubriques suivantes :

- [Prise en main de HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Analyse des données sur les retards de vol avec HDInsight](hdinsight-analyze-flight-delay-data-linux.md)

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

<!---HONumber=AcomDC_0713_2016-->