<properties linkid="manage-services-hdinsight-howto-social-data" urlDisplayName="Analyze Data with HDInsight" pageTitle="Analyzing Twitter data with Hive - Windows Azure Services" metaKeywords="" description="In this tutorial you will query, explore, and analyze data from Twitter using the HDInsight Service for Windows Azure and a complex Hive example." metaCanonical="" services="" documentationCenter="" title="Analyzing Twitter Data with Hive" authors="" solutions="" writer="wenming" manager="" editor="" />

Analyse des données Twitter avec Hive
=====================================

Dans ce didacticiel, vous allez interroger, explorer et analyser des données de Twitter en utilisant le service HDInsight pour Azure basé sur Hadoop Apache, ainsi qu'un exemple Hive complexe. Les sites Web sociaux constituent l'un des principaux motifs de l'utilisation du modèle « Big Data ». Les API publiques fournies par des sites comme Twitter représentent une source de données utile pour l'analyse et la compréhension des tendances populaires. Ce didacticiel part du principe que vous avez déjà créé un cluster HDInsight via le [portail Azure](http://www.windowsazure.com).

Ce didacticiel se compose des parties suivantes :

1.  [Obtention du flux Twitter avec cURL et l'API de diffusion Twitter](#segment1)
2.  [Traitement interactif des données Twitter avec Hive](#segment2)

## Obtention du flux Twitter avec cURL et l'API de diffusion Twitter

1.  Cette procédure requiert curl.exe. Téléchargez le fichier curl adapté à votre SE (comme le SSL binaire Win64) sur la page <http://curl.haxx.se/download.html>.

    ![Figure3](./media/hdinsight-analyze-twitter-date-hive/Figure3.png)

2.  Décompressez **curl.exe** dans l'emplacement adéquat (tel que **C:\\twitterdata**).

3.  Avec le Bloc-notes, créez les fichiers **get\_twitter\_stream.cmd** et **twitter\_params.txt** dans le même dossier que **curl.exe**, comme ceci :

    ![Figure4](./media/hdinsight-analyze-twitter-date-hive/Figure4.png)

4.  Modifiez le fichier **twitter\_params.txt** pour suivre vos tweets comme ceci :

         track=weather,Azure,WindowsAzure,cloud

    Vous pouvez utiliser les sujets ou les hashtags de votre choix.

5.  Modifiez le script de commande de la fenêtre **get\_twitter\_stream.cmd**, ajoutez votre nom d'utilisateur Twitter à la place de **USER** et votre mot de passe à la place de **PASSWORD** sur la ligne suivante :

         curl -d @twitter_parameters.txt -k https://stream.twitter.com/1/statuses/filter.json -u user:{password} >>twitter_stream_seq2.txt

6.  Dans une invite de commandes, exécutez le script get\_twitter\_stream.cmd comme ceci :

    ![Figure5](./media/hdinsight-analyze-twitter-date-hive/Figure5.png)

    Le résultat suivant devrait s'afficher :

    ![Figure6](./media/hdinsight-analyze-twitter-date-hive/Figure6.png)

7.  Vous pouvez arrêter la tâche en appuyant sur **Ctrl+C**. Vous pouvez ensuite renommer le fichier, puis redémarrer le script. La durée de ce processus peut varier entre 10 minutes et plusieurs heures. Dans le cadre de ce didacticiel, veuillez limiter le volume des données à quelques centaines de mégaoctets.

    Les données Twitter sont stockées au format JSon qui contient une structure imbriquée complexe. Dans la procédure suivante, au lieu d'écrire de nombreuses lignes de code en utilisant un langage de programmation conventionnel, nous allons transformer cette structure imbriquée en une table Hive, pour pouvoir l'interroger de façon interactive grâce à un langage similaire au SQL, nommé HQL.

## Traitement interactif des données Twitter avec Hive

1.  Durant la collecte des Tweets, créez une session RDP sur le cluster HDInsight créé via le portail Azure.

    **Accédez** à HDInsight, **sélectionnez** le cluster créé. En bas de l'écran, cliquez sur l'icône **Se connecter à RDP**. Connectez-vous à votre session RDP en entrant votre mot de passe. Une fois connecté, **ouvrez** une fenêtre de l'Explorateur, puis accédez au répertoire C:.

    ![Se connecter avec l'icône RDP](./media/hdinsight-analyze-twitter-date-hive/twitter-RDPconnect.PNG)

2.  Appuyez sur les touches **Ctrl+C** pour terminer la collecte de Tweets Curl, puis utilisez l'Explorateur Windows pour accéder à l'emplacement du fichier Twitter. **Cliquez avec le bouton droit** pour compresser le dossier (dossier zippé). Ceci réduit le temps de téléchargement.

3.  Sur votre ordinateur local, cliquez sur le fichier .zip et appuyez sur **Ctrl+C**, puis, sur votre session de Bureau à distance, accédez au répertoire C:\\. Cliquez sur la fenêtre de l'Explorateur (C:), puis appuyez sur **Ctrl+V** pour télécharger le fichier .zip via la session RDP.

    ![Télécharger les Tweets via la session RDP](./media/hdinsight-analyze-twitter-date-hive/twitter-uploadingzip.PNG)

4.  Une fois le fichier téléchargé, **cliquez avec le bouton droit**, **sélectionnez** Extraire tout... vers le répertoire C:\\ pour récupérer le fichier texte d'origine. **Ouvrez** une fenêtre de ligne de commande Hadoop pour commencer à utiliser HIVE et les commandes Hadoop.

    ![Figure26](./media/hdinsight-analyze-twitter-date-hive/Figure26.png)

5.  Pour commencer, **tapez** C:\\ et **appuyez sur Entrée**. Vous arrivez dans le répertoire C:\\ contenant les données Twitter.

6.  Ensuite, créez un dossier dans HDFS (Hadoop Distributed File System), puis copiez les données Twitter dans ce dossier, en utilisant le commutateur -copyFromLocal :

         hadoop fs -mkdir /example/data
         hadoop fs -copyFromLocal c:\twitter_stream_seq2.txt /example/data/

7.  Â présent, les données Twitter brutes sont copiées dans HDFS sur votre cluster HDInsight. L'étape suivante consiste à créer une simple structure de table pour les données téléchargées. Cette table Hive structurée temporaire nous permet de conserver les données et de procéder à un traitement ETL avancé. Ouvrez le Bloc-notes et collez le code suivant dans le fichier de Bloc-notes, puis enregistrez-le sous le nom suivant : « C:\\load\_twitter\_raw.hql »

         drop table twitter_raw;

         create table twitter_raw (
             json_response string
         ) 
         partitioned by (filesequence int);

         load data inpath '/example/data/twitter_stream_seq2.txt'
         into table twitter_raw
         partition (filesequence = 1);

8.  Une fois le fichier enregistré, exécutez Hive dans la fenêtre de commande :

         C:\apps\dist\hive-0.9.0\bin\hive -f load_twitter_raw.hql

9.  Le processus dure quelques secondes :

    ![Charger les résultats bruts de Twitter](./media/hdinsight-analyze-twitter-date-hive/twitter-load-raw-results.PNG)

10. L'ensemble de données Twitter volumineux au format JSon imbriqué est transformé en une structure de table Hive temporaire.

11. Avant de pouvoir interroger l'ensemble de données Twitter avec Hive, nous devons exécuter un autre processus ETL. Nous allons définir un schéma de table plus détaillé pour les données stockées dans la table « twitter\_raw ». Ce processus ETL est plus complexe et dure donc plus longtemps. À l'invite de commandes, démarrez de nouveau le Bloc-notes pour y coller le code de requête Hive suivant, puis enregistrez le fichier en le nommant « C:\\twitter\_etl.hql.txt ».

        set hive.exec.dynamic.partition = true;
        set hive.exec.dynamic.partition.mode=nonstrict;

        drop table twitter_temp;

        create table twitter_temp
        (
            id bigint,
            created_at string,
            created_at_date string,
            created_at_year string,
            created_at_month string,
            created_at_day string,
            created_at_time string,
            in_reply_to_user_id_str string,
            text string,
            contributors string,
            retweeted string,
            truncated string,
            coordinates string,
            source string,
            retweet_count int,
            url string,
            hashtags array<string>,
            user_mentions array<string>,
            first_hashtag string,
            first_user_mention string,
            screen_name string,
            name string,
            followers_count int,
            listed_count int,
            friends_count int,
            lang string,
            user_location string,
            time_zone string,
            profile_image_url string,
            json_response string
        )
        partitioned by (filesequence int);

        from twitter_raw
        insert overwrite table twitter_temp
        partition (filesequence)
        select
            cast(get_json_object(json_response, '$.id_str') as bigint),

            get_json_object(json_response, '$.created_at'),
            concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
                substr (get_json_object(json_response, '$.created_at'),27,4)),

            substr (get_json_object(json_response, '$.created_at'),27,4),

            case substr (get_json_object(json_response,     '$.created_at'),5,3)
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
            cast (get_json_object(json_response, '$.retweet_count') as int),
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
            cast (get_json_object(json_response, '$.user.followers_count') as int),
            cast (get_json_object(json_response, '$.user.listed_count') as int),
            cast (get_json_object(json_response, '$.user.friends_count') as int),
            get_json_object(json_response, '$.user.lang'),
            get_json_object(json_response, '$.user.location'),
            get_json_object(json_response, '$.user.time_zone'),
            get_json_object(json_response, '$.user.profile_image_url'),
            json_response,
            filesequence
        where (length(json_response) > 500);

12. Puis tapez :

        C:\apps\dist\hive-0.9.0\bin\hive -f twitter_etl.hql.txt

13. Ce script Hive complexe démarre un ensemble de tâches MapReduce sur le cluster Hadoop. Selon votre ensemble de données et la taille de votre cluster, cela peut prendre environ 10 minutes.

    ![ETL complexe Twitter](./media/hdinsight-analyze-twitter-date-hive/twitter-etl-complex.PNG)

14. Vous pouvez également surveiller la progression de votre tâche via la page de suivi de la tâche sur le nœud principal en effectuant un double clic sur le raccourci « Hadoop MapReduce Status » sur le Bureau.

    ![Tâches de surveillance et de suivi](./media/hdinsight-analyze-twitter-date-hive/twitter_longjob_browser.PNG)

15. Une fois le processus ETL terminé, démarrez Hive :

        C:\apps\dist\hive-0.9.0\bin\hive

16. Vous pouvez tester des requêtes Hive simples telles que :

        select name, screen\_name, count(1) as cc from twitter\_temp where text like "%Azure%" group by name,screen\_name order by cc desc limit 10;

1.  Cette requête renvoie une liste des utilisateurs de Twitter qui envoient le plus de Tweets contenant le mot « Azure ».

    ![Résultat final](./media/hdinsight-analyze-twitter-date-hive/twitter_final_query_result.PNG)

## Résumé

Dans ce didacticiel, nous avons vu comment transformer un ensemble de données Json non structuré en une table Hive structurée pour interroger, explorer et analyser des données de Twitter en utilisant HDInsight sur Azure. Pour obtenir des mises à jour et des fichiers de support, recherchez-les sur le référentiel GitHub, [ici](https://github.com/wenming/BigDataSamples/tree/master/twittersample).

Étapes suivantes
----------------

Bien que cet article présente l'utilisation de la ligne de commande Hadoop, vous pouvez également réaliser des tâches avec la console interactive HDInsight Service. Pour plus d'informations, consultez [Recommandations : HDInsight Interactive JavaScript et consoles Hive][interactive-console].

