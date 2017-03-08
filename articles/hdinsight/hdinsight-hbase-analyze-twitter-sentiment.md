---
title: "Analyse de sentiments Twitter en temps réel avec HBase | Microsoft Docs"
description: "Apprenez à effectuer une analyse des sentiments des données volumineuses en temps réel à partir de Twitter à l&quot;aide de HBase dans un cluster HDInsight (Hadoop)."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5c798ad3-a20d-4385-a463-f4f7705f9566
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a62cd616f0abd59c83c834bf13b4ba8549a9c73e
ms.openlocfilehash: dd47e9c8f5c440c72acb60877d556476fe187ddf
ms.lasthandoff: 02/23/2017


---
# <a name="analyze-real-time-twitter-sentiment-with-hbase-in-hdinsight"></a>Analyse de sentiments Twitter en temps réel avec HBase dans HDInsight
Apprenez à effectuer une [analyse des sentiments](http://en.wikipedia.org/wiki/Sentiment_analysis) des données volumineuses en temps réel à partir de Twitter à l’aide d’un cluster HBase dans HDInsight.

Les sites web sociaux constituent l'un des principaux motifs de l'utilisation du modèle des données volumineuses. Les API publiques fournies par des sites comme Twitter représentent une source de données utile pour l'analyse et la compréhension des tendances populaires. Ce didacticiel va vous permettre de développer une application console de service de diffusion et une application Web ASP.NET afin d'effectuer les opérations suivantes :

![Analyse de sentiments Twitter avec HBase dans HDInsight.][img-app-arch]

* Application de diffusion en continu

  * recevoir en temps réel des tweets avec emplacement en utilisant une API de diffusion Twitter
  * évaluer les sentiments de ces tweets
  * stocker les informations de sentiments dans HBase à l'aide du Kit de développement logiciel (SDK) Microsoft HBase
* L'application Sites Web Microsoft Azure
  
  * représenter graphiquement les résultats statistiques en temps réel à l'aide d'une application Web ASP.NET. Visuellement, les tweets ressembleront à ceci :
    
    ![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
    
    Vous pourrez effectuer des recherches par mots clés sur des tweets afin d'évaluer si l'opinion qui y est exprimée est positive, négative ou neutre.

Un exemple de solution Visual Studio complète est accessible sur GitHub : [application d’analyse de sentiments sociaux en temps réel](https://github.com/maxluk/tweet-sentiment).

### <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un cluster HBase dans HDInsight**. Pour les instructions sur la création de clusters, voir la section [Prise en main de HBase avec Hadoop dans HDInsight][hbase-get-started]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

    <table border="1">
    <tr><th>Propriété du cluster</th><th>Description</th></tr>
    <tr><td>Nom du cluster HBase</td><td>Le nom de votre cluster HBase dans HDInsight. Par exemple : https://myhbase.azurehdinsight.net/</td></tr>
    <tr><td>Nom d'utilisateur du cluster</td><td>Le nom du compte utilisateur Hadoop. Le nom d’utilisateur Hadoop par défaut est <strong>admin</strong>.</td></tr>
    <tr><td>Mot de passe utilisateur du cluster</td><td>Le mot de passe utilisateur du cluster Hadoop.</td></tr>
    </table>

* **Un poste de travail** où Visual Studio 2013 a été installé. Pour les instructions, voir la section [Installation de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## <a name="create-a-twitter-application-id-and-secrets"></a>Création de l'ID et des secrets d'une application Twitter
Les API de diffusion Twitter utilisent [OAuth](http://oauth.net/) pour autoriser les demandes. Pour utiliser OAuth, la première étape consiste à créer une nouvelle application sur le site du développeur Twitter.

**Pour créer l'ID et les secrets d'une application Twitter**

1. Connectez-vous aux [Applications Twitter](https://apps.twitter.com/). Cliquez sur le lien **Sign up now** si vous ne disposez pas d'un compte Twitter.
2. Cliquez sur **Create New App**.
3. Entrez un **Nom**, une **Description** et un **Site web**. Le nom de l'application Twitter doit être unique. Le champ Website n'est pas réellement utilisé. Il n'est pas nécessaire que ce soit une URL valide. 
4. Cochez la case **Yes, I agree**, puis cliquez **Create your Twitter application**.
5. Cliquez sur l'onglet **Permissions** . L'autorisation par défaut est **Read only**. Ces étapes sont suffisantes pour ce didacticiel. 
6. Cliquez sur l’onglet **Keys and Access Tokens** .
7. Cliquez sur **Create my access token**.
8. Cliquez sur **Test OAuth** dans le coin supérieur droit de la page.
9. Renseignez les valeurs **Clé du client**, **Secret du client**, **Jeton d’accès** et **Secret du jeton d’accès**. Vous en aurez besoin plus loin dans le didacticiel.
   
    ![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]

## <a name="create-twitter-streaming-service"></a>Création d'un service de diffusion Twitter
Vous devez créer une application pour recevoir des tweets, calculer le résultat des sentiments des tweets et envoyer les mots tweets traités à HBase.

**Création de l’application de diffusion en continu**

1. Ouvrez **Visual Studio**, puis créez une application console Visual C# appelée **TweetSentimentStreaming**. 
2. Dans la **Console du gestionnaire de package**, entrez les commandes suivantes :
   
        Install-Package Microsoft.HBase.Client -version 0.4.2.0
        Install-Package TweetinviAPI -version 1.0.0.0
   
    Utilisez les commandes suivantes pour installer le package [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/), qui représente la bibliothèque cliente pour accéder au cluster HBase, et le package [Tweetinvi API](https://www.nuget.org/packages/TweetinviAPI/), utilisé pour accéder à l’API Twitter.
   
   > [!NOTE]
   > L’exemple utilisé dans cet article a été testé à l’aide de la version spécifiée ci-dessus.  Vous pouvez supprimer le commutateur -version pour installer la version la plus récente.
   > 
   > 
3. Dans l’**Explorateur de solutions**, ajoutez **System.Configuration** à la référence.
4. Ajoutez un nouveau fichier de classe au projet nommé **HBaseWriter.cs**, puis remplacez le code par ce qui suit :
   
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling;
        using org.apache.hadoop.hbase.rest.protobuf.generated;
        using Microsoft.HBase.Client;
        using Tweetinvi.Models;
   
        namespace TweetSentimentStreaming
        {
            class HBaseWriter
            {
                // HDinsight HBase cluster and HBase table information
                const string CLUSTERNAME = "https://<Enter Your Cluster Name>.azurehdinsight.net/";
                const string HADOOPUSERNAME = "admin"; //the default name is "admin"
                const string HADOOPUSERPASSWORD = "<Enter the Hadoop User Password>";
   
                const string HBASETABLENAME = "tweets_by_words";
                const string COUNT_ROW_KEY = "~ROWCOUNT";
                const string COUNT_COLUMN_NAME = "d:COUNT";
   
                long rowCount = 0;
   
                // Sentiment dictionary file and the punctuation characters
                const string DICTIONARYFILENAME = @"..\..\dictionary.tsv";
                private static char[] _punctuationChars = new[] {
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.
   
                // For writting to HBase
                HBaseClient client;
   
                // a sentiment dictionary for estimate sentiment. It is loaded from a physical file.
                Dictionary<string, DictionaryItem> dictionary;
   
                // use multithread write
                Thread writerThread;
                Queue<ITweet> queue = new Queue<ITweet>();
                bool threadRunning = true;
   
                // This function connects to HBase, loads the sentiment dictionary, and starts the thread for writting.
                public HBaseWriter()
                {
                    ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
                    client = new HBaseClient(credentials);
   
                    // create the HBase table if it doesn't exist
                    if (!client.ListTablesAsync().Result.name.Contains(HBASETABLENAME))
                    {
                        TableSchema tableSchema = new TableSchema();
                        tableSchema.name = HBASETABLENAME;
                        tableSchema.columns.Add(new ColumnSchema { name = "d" });
                        client.CreateTableAsync(tableSchema).Wait();
                        Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
                    }
   
                    // Read current row count cell
                    rowCount = GetRowCount();
   
                    // Load sentiment dictionary from a file
                    LoadDictionary();
   
                    // Start a thread for writting to HBase
                    writerThread = new Thread(new ThreadStart(WriterThreadFunction));
                    writerThread.Start();
                }
   
                ~HBaseWriter()
                {
                    threadRunning = false;
                }
   
                private long GetRowCount()
                {
                    try
                    {
                        RequestOptions options = RequestOptions.GetDefaultOptions();
                        options.RetryPolicy = RetryPolicy.NoRetry;
                        var cellSet = client.GetCellsAsync(HBASETABLENAME, COUNT_ROW_KEY, null, null, options).Result;
                        if (cellSet.rows.Count != 0)
                        {
                            var countCol = cellSet.rows[0].values.Find(cell => Encoding.UTF8.GetString(cell.column) == COUNT_COLUMN_NAME);
                            if (countCol != null)
                            {
                                return Convert.ToInt64(Encoding.UTF8.GetString(countCol.data));
                            }
                        }
                    }
                    catch(Exception ex)
                    {
                        if (ex.InnerException.Message.Equals("The remote server returned an error: (404) Not Found.", StringComparison.OrdinalIgnoreCase))
                        {
                            return 0;
                        }
                        else
                        {
                            throw ex;
                        }
   
                    }
   
                    return 0;
                }
   
                // Enqueue the Tweets received
                public void WriteTweet(ITweet tweet)
                {
                    lock (queue)
                    {
                        queue.Enqueue(tweet);
                    }
                }
   
                // Load sentiment dictionary from a file
                private void LoadDictionary()
                {
                    List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
                    var items = lines.Select(line =>
                    {
                        var fields = line.Split('\t');
                        var pos = 0;
                        return new DictionaryItem
                        {
                            Type = fields[pos++],
                            Length = Convert.ToInt32(fields[pos++]),
                            Word = fields[pos++],
                            Pos = fields[pos++],
                            Stemmed = fields[pos++],
                            Polarity = fields[pos++]
                        };
                    });
   
                    dictionary = new Dictionary<string, DictionaryItem>();
                    foreach (var item in items)
                    {
                        if (!dictionary.Keys.Contains(item.Word))
                        {
                            dictionary.Add(item.Word, item);
                        }
                    }
                }
   
                // Calculate sentiment score
                private int CalcSentimentScore(string[] words)
                {
                    Int32 total = 0;
                    foreach (string word in words)
                    {
                        if (dictionary.Keys.Contains(word))
                        {
                            switch (dictionary[word].Polarity)
                            {
                                case "negative": total -= 1; break;
                                case "positive": total += 1; break;
                            }
                        }
                    }
                    if (total > 0)
                    {
                        return 1;
                    }
                    else if (total < 0)
                    {
                        return -1;
                    }
                    else
                    {
                        return 0;
                    }
                }
   
                // Popular a CellSet object to be written into HBase
                private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
                {
                    // Split the Tweet into words
                    string[] words = tweet.Text.ToLower().Split(_punctuationChars);
   
                    // Calculate sentiment score base on the words
                    int sentimentScore = CalcSentimentScore(words);
                    var word_pairs = words.Take(words.Length - 1)
                                        .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
                    var all_words = words.Concat(word_pairs).ToList();
   
                    // For each word in the Tweet add a row to the HBase table
                    foreach (string word in all_words)
                    {
                        string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                        string key = word + "_" + time_index;
   
                        // Create a row
                        var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
   
                        // Add columns to the row, including Tweet identifier, language, coordinator(if available), and sentiment 
                        var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                        row.values.Add(value);
   
                        value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                        row.values.Add(value);
   
                        if (tweet.Coordinates != null)
                        {
                            var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                            value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                            row.values.Add(value);
                        }
   
                        value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                        row.values.Add(value);
   
                        set.rows.Add(row);
                    }
                }
   
                // Write a Tweet (CellSet) to HBase
                public void WriterThreadFunction()
                {
                    try
                    {
                        while (threadRunning)
                        {
                            if (queue.Count > 0)
                            {
                                CellSet set = new CellSet();
                                lock (queue)
                                {
                                    do
                                    {
                                        ITweet tweet = queue.Dequeue();
                                        CreateTweetByWordsCells(set, tweet);
                                    } while (queue.Count > 0);
                                }
   
                                // Write the Tweet by words cell set to the HBase table
                                client.StoreCellsAsync(HBASETABLENAME, set).Wait();
                                Console.WriteLine("\tRows written: {0}", set.rows.Count);
                            }
                            Thread.Sleep(100);
                        }
                    }
                    catch (Exception ex)
                    {
                        Console.WriteLine("Exception: " + ex.Message);
                    }
                }
            }
            public class DictionaryItem
            {
                public string Type { get; set; }
                public int Length { get; set; }
                public string Word { get; set; }
                public string Pos { get; set; }
                public string Stemmed { get; set; }
                public string Polarity { get; set; }
            }
        }
5. Définissez les constantes dans le code précédent, notamment **CLUSTERNAME**, **HADOOPUSERNAME**, **HADOOPUSERPASSWORD** et DICTIONARYFILENAME. DICTIONARYFILENAME est le nom du fichier et l'emplacement de direction.tsv.  Le fichier peut être téléchargé à partir de **https://hditutorialdata.blob.core.windows.net/twittersentiment/dictionary.tsv**. Si vous souhaitez modifier le nom de la table HBase, vous devez également modifier le nom de la table dans l'application Web.
6. Ouvrez le fichier **Program.cs**et remplacez le code par le suivant :
   
        using System;
        using System.Diagnostics;
        using Tweetinvi;
        using Tweetinvi.Models;
   
        namespace TweetSentimentStreaming
        {
            class Program
            {
                const string TWITTERAPPACCESSTOKEN = "<Enter Twitter App Access Token>";
                const string TWITTERAPPACCESSTOKENSECRET = "<Enter Twitter Access Token Secret>";
                const string TWITTERAPPAPIKEY = "<Enter Twitter App API Key>";
                const string TWITTERAPPAPISECRET = "<Enter Twitter App API Secret>";
   
                static void Main(string[] args)
                {
                    Auth.SetUserCredentials(TWITTERAPPAPIKEY, TWITTERAPPAPISECRET, TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET);
   
                    Stream_FilteredStreamExample();
                }
   
                private static void Stream_FilteredStreamExample()
                {
                    for (;;)
                    {
                        try
                        {
                            HBaseWriter hbase = new HBaseWriter();
                            var stream = Stream.CreateFilteredStream();
                            stream.AddLocation(new Coordinates(-180, -90), new Coordinates(180, 90)); 
   
                            var tweetCount = 0;
                            var timer = Stopwatch.StartNew();
   
                            stream.MatchingTweetReceived += (sender, args) =>
                            {
                                tweetCount++;
                                var tweet = args.Tweet;
   
                                // Write Tweets to HBase
                                hbase.WriteTweet(tweet);
   
                                if (timer.ElapsedMilliseconds > 1000)
                                {
                                    if (tweet.Coordinates != null)
                                    {
                                        Console.ForegroundColor = ConsoleColor.Green;
                                        Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                        Console.ForegroundColor = ConsoleColor.White;
                                        Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                                    }
   
                                    timer.Restart();
                                    Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                                    tweetCount = 0;
                                }
                            };
   
                            stream.StartStreamMatchingAllConditions();
                        }
                        catch (Exception ex)
                        {
                            Console.WriteLine("Exception: {0}", ex.Message);
                        }
                    }
                }
   
            }
        }
7. Définissez les constantes, notamment **TWITTERAPPACCESSTOKEN**, **TWITTERAPPACCESSTOKENSECRET**, **TWITTERAPPAPIKEY** et **TWITTERAPPAPISECRET**. 

Pour exécuter le service de diffusion, appuyez sur **F5**. La capture d'écran suivante présente l'application console :

![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]

Continuez à exécuter l'application console pendant que vous développez l'application Web, de façon à disposer de davantage de données. Pour examiner les données insérées dans la table, vous pouvez utiliser HBase Shell. Voir [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started.md#create-tables-and-insert-data).

## <a name="visualize-real-time-sentiment"></a>Visualiser les données de sentiments en temps réel
Dans cette section, vous allez créer une application Web ASP.NET MVC afin de lire en temps réel les données de sentiments de HBase et les représenter graphiquement sur Bing Maps.

**Pour créer une application Web ASP.NET MVC**

1. Ouvrez Visual Studio.
2. Cliquez sur **Fichier**, **Nouveau**, puis sur **Projet**.
3. Entrez les informations suivantes :
   
   * Catégorie de modèle : **Visual C#/Web**
   * Modèle : **Application Web ASP.NET**
   * Nom : **TweetSentimentWeb**
   * Emplacement : **C:\Tutorials** 
4. Cliquez sur **OK**.
5. Dans **Sélectionner un modèle**, cliquez sur **MVC**. 
6. Dans **Microsoft Azure**, cliquez sur **Gérer les abonnements**.
7. Dans **Gérer les abonnements Microsoft Azure**, cliquez sur **Se connecter**.
8. Entrez vos informations d’identification Azure. Les informations de votre abonnement Azure s’affichent dans l’onglet **Comptes** .
9. Cliquez sur **Fermer** pour fermer la fenêtre **Gérer les abonnements Microsoft Azure**.
10. Dans **Nouveau projet ASP.NET - TweetSentimentWeb**, cliquez sur **OK**.
11. Dans **Configurer les paramètres du site Microsoft Azure**, sélectionnez la **Région** la plus proche de vous. Il n'est pas nécessaire de spécifier un serveur de base de données. 
12. Cliquez sur **OK**.

**Pour installer les packages Nuget**

1. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**. Le panneau de console est ouvert au bas de la page.
2. Utilisez la commande suivante pour installer le package [HBase .NET SDK](https://www.nuget.org/packages/Microsoft.HBase.Client/) , qui est la bibliothèque cliente permettant d’accéder au cluster HBase :
   
        Install-Package Microsoft.HBase.Client 

**Pour ajouter une classe HBaseReader**

1. Dans l’**Explorateur de solutions**, développez **TweetSentiment**.
2. Cliquez avec le bouton droit sur **Modèles**, puis cliquez sur **Ajouter** et enfin sur **Classe**.
3. Dans le champ **Nom**, entrez **HBaseReader.cs**, puis cliquez sur **Ajouter**.
4. Remplacez le code par ce qui suit :
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
   
        using System.Configuration;
        using System.Threading.Tasks;
        using System.Text;
        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;
   
        namespace TweetSentimentWeb.Models
        {
            public class HBaseReader
            {
                // For reading Tweet sentiment data from HDInsight HBase
                HBaseClient client;
   
                // HDinsight HBase cluster and HBase table information
                const string CLUSTERNAME = "<HBaseClusterName>";
                const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
                const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
                const string HBASETABLENAME = "tweets_by_words";
   
                // The constructor
                public HBaseReader()
                {
                    ClusterCredentials creds = new ClusterCredentials(
                                    new Uri(CLUSTERNAME),
                                    HADOOPUSERNAME,
                                    HADOOPUSERPASSWORD);
                    client = new HBaseClient(creds);
                }
   
                // Query Tweets sentiment data from the HBase table asynchronously 
                public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
                {
                    List<Tweet> list = new List<Tweet>();
   
                    // Demonstrate Filtering the data from the past 6 hours the row key
                    string timeIndex = (ulong.MaxValue -
                        (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
                    string startRow = keyword + "_" + timeIndex;
                    string endRow = keyword + "|";
                    Scanner scanSettings = new Scanner
                    {
                        batch = 100000,
                        startRow = Encoding.UTF8.GetBytes(startRow),
                        endRow = Encoding.UTF8.GetBytes(endRow)
                    };
   
                    // Make async scan call
                    ScannerInformation scannerInfo =
                        await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
   
                    CellSet next;
   
                    while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
                    {
                        foreach (CellSet.Row row in next.rows)
                        {
                            // find the cell with string pattern "d:coor" 
                            var coordinates =
                                row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
   
                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
   
                                var sentimentField =
                                    row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
                                Int32 sentiment = 0;
                                if (sentimentField != null)
                                {
                                    sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
                                }
   
                                list.Add(new Tweet
                                {
                                    Longtitude = Convert.ToDouble(lonlat[0]),
                                    Latitude = Convert.ToDouble(lonlat[1]),
                                    Sentiment = sentiment
                                });
                            }
   
                            if (coordinates != null)
                            {
                                string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
                            }
                        }
                    }
   
                    return list;
                }
            }
   
            public class Tweet
            {
                public string IdStr { get; set; }
                public string Text { get; set; }
                public string Lang { get; set; }
                public double Longtitude { get; set; }
                public double Latitude { get; set; }
                public int Sentiment { get; set; }
            }
        }
5. Dans la classe **HBaseReader** , modifiez les valeurs de constante comme suit :
   
   * **CLUSTERNAME** : nom du cluster HBase, par exemple, *https://<HBaseClusterName>.azurehdinsight.net/*. 
   * **HADOOPUSERNAME**: le nom d'utilisateur Hadoop du cluster HBase. Le nom par défaut est *admin*.
   * **HADOOPUSERPASSWORD**: le mot de passe utilisateur Hadoop du cluster HBase.
   * **HBASETABLENAME** = « tweets_by_words » ;
     
     Le nom de la table HBase est **« tweets_by_words » ;**. Les valeurs doivent correspondre aux valeurs que vous avez envoyées au service de diffusion pour que l'application Web lise les données de la même table HBase.

**Pour ajouter un contrôleur TweetsController**

1. Dans l’**Explorateur de solutions**, développez **TweetSentimentWeb**.
2. Cliquez avec le bouton droit sur **Contrôleurs**, puis cliquez sur **Ajouter** et enfin sur **Contrôleur**.
3. Cliquez sur **Contrôleur API 2 web - Vide**, puis sur **Ajouter**.
4. Dans le champ **Nom du contrôleur**, entrez **TweetsController**, puis cliquez sur **Ajouter**.
5. Dans l' **Explorateur de solutions**, double-cliquez sur TweetsController.cs pour ouvrir le fichier.
6. Modifiez le fichier pour qu'il ressemble à ceci :
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
   
        using System.Threading.Tasks;
        using TweetSentimentWeb.Models;
   
        namespace TweetSentimentWeb.Controllers
        {
            public class TweetsController : ApiController
            {
                HBaseReader hbase = new HBaseReader();
   
                public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
                {
                    return await hbase.QueryTweetsByKeywordAsync(query);
                }
            }
        }

**Pour ajouter heatmap.js**

1. Dans l’**Explorateur de solutions**, développez **TweetSentimentWeb**.
2. Cliquez avec le bouton droit sur **Scripts**, puis cliquez sur **Ajouter** et enfin sur **Fichier JavaScript**.
3. Dans le champ **Nom de l’élément** tapez **heatmap.js**.
4. Collez le code suivant dans le fichier. Le code a été écrit par Alastair Aitchison. Pour plus d'informations, consultez [Bibliothèque Bing Maps AJAX v7 HeatMap](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/).
   
        /*******************************************************************************
        * Author: Alastair Aitchison
        * Website: http://alastaira.wordpress.com
        * Date: 15th April 2011
        * 
        * Description: 
        * This JavaScript file provides an algorithm that can be used to add a heatmap
        * overlay on a Bing Maps v7 control. The intensity and temperature palette
        * of the heatmap are designed to be easily customisable.
        *
        * Requirements:
        * The heatmap layer itself is created dynamically on the client-side using
        * the HTML5 &lt;canvas> element, and therefore requires a browser that supports
        * this element. It has been tested on IE9, Firefox 3.6/4 and 
        * Chrome 10 browsers. If you can confirm whether it works on other browsers or
        * not, I'd love to hear from you!
        *
        * Usage:
        * The HeatMapLayer constructor requires:
        * - A reference to a map object
        * - An array or Microsoft.Maps.Location items
        * - Optional parameters to customise the appearance of the layer
        *  (Radius,, Unit, Intensity, and ColourGradient), and a callback function
        */

        var HeatMapLayer = function (map, locations, options) {

            /* Private Properties */
            var _map = map,
                _canvas,
                _temperaturemap,
                _locations = [],
                _viewchangestarthandler,
                _viewchangeendhandler;

            // Set default options
            var _options = {
                // Opacity at the centre of each heat point
                intensity: 0.5,

                // Affected radius of each heat point
                radius: 1000,

                // Whether the radius is an absolute pixel value or meters
                unit: 'meters',

                // Colour temperature gradient of the map
                colourgradient: {
                    "0.00": 'rgba(255,0,255,20)',  // Magenta
                    "0.25": 'rgba(0,0,255,40)',    // Blue
                    "0.50": 'rgba(0,255,0,80)',    // Green
                    "0.75": 'rgba(255,255,0,120)', // Yellow
                    "1.00": 'rgba(255,0,0,150)'    // Red
                },

                // Callback function to be fired after heatmap layer has been redrawn 
                callback: null
            };

            /* Private Methods */
            function _init() {
                var _mapDiv = _map.getRootElement();

                if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
                    // Create the canvas element
                    _canvas = document.createElement('canvas');
                    _canvas.style.position = 'relative';

                    var container = document.createElement('div');
                    container.style.position = 'absolute';
                    container.style.left = '0px';
                    container.style.top = '0px';
                    container.appendChild(_canvas);

                    _mapDiv.childNodes[2].childNodes[1].appendChild(container);

                    // Override defaults with any options passed in the constructor
                    _setOptions(options);

                    // Load array of location data
                    _setPoints(locations);

                    // Create a colour gradient from the suppied colourstops
                    _temperaturemap = _createColourGradient(_options.colourgradient);

                    // Wire up the event handler to redraw heatmap canvas
                    _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
                    _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);

                    _createHeatMap();

                    delete _init;
                } else {
                    setTimeout(_init, 100);
                }
            }

            // Resets the heat map
            function _clearHeatMap() {
                var ctx = _canvas.getContext("2d");
                ctx.clearRect(0, 0, _canvas.width, _canvas.height);
            }

            // Creates a colour gradient from supplied colour stops on initialisation
            function _createColourGradient(colourstops) {
                var ctx = document.createElement('canvas').getContext('2d');
                var grd = ctx.createLinearGradient(0, 0, 256, 0);
                for (var c in colourstops) {
                    grd.addColorStop(c, colourstops[c]);
                }
                ctx.fillStyle = grd;
                ctx.fillRect(0, 0, 256, 1);
                return ctx.getImageData(0, 0, 256, 1).data;
            }

            // Applies a colour gradient to the intensity map
            function _colouriseHeatMap() {
                var ctx = _canvas.getContext("2d");
                var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
                var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
                for (var p = 0, len = pix.length; p < len;) {
                    var a = pix[p + 3] * 4; // get the alpha of this pixel
                    if (a != 0) { // If there is any data to plot
                        pix[p] = _temperaturemap[a]; // set the red value of the gradient that corresponds to this alpha
                        pix[p + 1] = _temperaturemap[a + 1]; //set the green value based on alpha
                        pix[p + 2] = _temperaturemap[a + 2]; //set the blue value based on alpha
                    }
                    p += 4; // Move on to the next pixel
                }
                ctx.putImageData(dat, 0, 0);
            }

            // Sets any options passed in
            function _setOptions(options) {
                for (attrname in options) {
                    _options[attrname] = options[attrname];
                }
            }

            // Sets the heatmap points from an array of Microsoft.Maps.Locations  
            function _setPoints(locations) {
                _locations = locations;
            }

            // Main method to draw the heatmap
            function _createHeatMap() {
                // Ensure the canvas matches the current dimensions of the map
                // This also has the effect of resetting the canvas
                _canvas.height = _map.getHeight();
                _canvas.width = _map.getWidth();

                _canvas.style.top = -_canvas.height / 2 + 'px';
                _canvas.style.left = -_canvas.width / 2 + 'px';

                // Calculate the pixel radius of each heatpoint at the current map zoom
                if (_options.unit == "pixels") {
                    radiusInPixel = _options.radius;
                } else {
                    radiusInPixel = _options.radius / _map.getMetersPerPixel();
                }

                var ctx = _canvas.getContext("2d");

                // Convert lat/long to pixel location
                var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
                var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
                var mapWidth = 256 * Math.pow(2, _map.getZoom());

                // Create the Intensity Map by looping through each location
                for (var i = 0, len = pixlocs.length; i < len; i++) {
                    var x = pixlocs[i].x;
                    var y = pixlocs[i].y;

                    if (x < 0) {
                        x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
                    }

                    // Create radial gradient centred on this point
                    var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
                    grd.addColorStop(0.0, shadow);
                    grd.addColorStop(1.0, 'transparent');

                    // Draw the heatpoint onto the canvas
                    ctx.fillStyle = grd;
                    ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
                }

                // Apply the specified colour gradient to the intensity map
                _colouriseHeatMap();

                // Call the callback function, if specified
                if (_options.callback) {
                    _options.callback();
                }
            }

            /* Public Methods */

            this.Show = function () {
                if (_canvas) {
                    _canvas.style.display = '';
                }
            };

            this.Hide = function () {
                if (_canvas) {
                    _canvas.style.display = 'none';
                }
            };

            // Sets options for intensity, radius, colourgradient etc.
            this.SetOptions = function (options) {
                _setOptions(options);
            }

            // Sets an array of Microsoft.Maps.Locations from which the heatmap is created
            this.SetPoints = function (locations) {
                // Reset the existing heatmap layer
                _clearHeatMap();
                // Pass in the new set of locations
                _setPoints(locations);
                // Recreate the layer
                _createHeatMap();
            }

            // Removes the heatmap layer from the DOM
            this.Remove = function () {
                _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);

                if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
                if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }

                _locations = null;
                _temperaturemap = null;
                _canvas = null;
                _options = null;
                _viewchangestarthandler = null;
                _viewchangeendhandler = null;
            }

            // Call the initialisation routine
            _init();
        };

        // Call the Module Loaded method
        Microsoft.Maps.moduleLoaded('HeatMapModule');

**Pour ajouter twitterStream.js**

1. Dans l’**Explorateur de solutions**, développez **TweetSentimentWeb**.
2. Cliquez avec le bouton droit sur **Scripts**, puis cliquez sur **Ajouter** et enfin sur **Fichier JavaScript**.
3. Dans le champ **Nom de l’élément**, saisissez **twitterStream.js**.
4. Copiez et collez le code suivant dans le fichier :
   
        var liveTweetsPos = [];
        var liveTweets = [];
        var liveTweetsNeg = [];
        var map;
        var heatmap;
        var heatmapNeg;
        var heatmapPos;
   
        function initialize() {
            // Initialize the map
            var options = {
                credentials: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
                center: new Microsoft.Maps.Location(23.0, 8.0),
                mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
                labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
                zoom: 2.5
            };
            var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
   
            // Heatmap options for positive, neutral and negative layers
   
            var heatmapOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,
   
                // Affected radius of each heat point
                radius: 15,
   
                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels'
            };
   
            var heatmapPosOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,
   
                // Affected radius of each heat point
                radius: 15,
   
                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',
   
                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 255, 191, 1)',
                    0.3: 'rgba(0, 255, 127, 1)',
                    0.4: 'rgba(0, 255, 63, 1)',
                    0.5: 'rgba(0, 127, 0, 1)',
                    0.7: 'rgba(0, 159, 0, 1)',
                    0.8: 'rgba(0, 191, 0, 1)',
                    0.9: 'rgba(0, 223, 0, 1)',
                    1.0: 'rgba(0, 255, 0, 1)'
                }
            };
   
            var heatmapNegOptions = {
                // Opacity at the centre of each heat point
                intensity: 0.5,
   
                // Affected radius of each heat point
                radius: 15,
   
                // Whether the radius is an absolute pixel value or meters
                unit: 'pixels',
   
                colourgradient: {
                    0.0: 'rgba(0, 255, 255, 0)',
                    0.1: 'rgba(0, 255, 255, 1)',
                    0.2: 'rgba(0, 191, 255, 1)',
                    0.3: 'rgba(0, 127, 255, 1)',
                    0.4: 'rgba(0, 63, 255, 1)',
                    0.5: 'rgba(0, 0, 127, 1)',
                    0.7: 'rgba(0, 0, 159, 1)',
                    0.8: 'rgba(0, 0, 191, 1)',
                    0.9: 'rgba(0, 0, 223, 1)',
                    1.0: 'rgba(0, 0, 255, 1)'
                }
            };
   
            // Register and load the Client Side HeatMap Module
            Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
            Microsoft.Maps.loadModule("HeatMapModule", {
                callback: function () {
                    // Create heatmap layers for positive, neutral and negative tweets
                    heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
                    heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
                    heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
                }
            });
   
            $("#searchbox").val("xbox");
            $("#searchBtn").click(onsearch);
            $("#positiveBtn").click(onPositiveBtn);
            $("#negativeBtn").click(onNegativeBtn);
            $("#neutralBtn").click(onNeutralBtn);
            $("#neutralBtn").button("toggle");
        }
   
        function onsearch() {
            var uri = 'api/tweets?query=';
            var query = $('#searchbox').val();
            $.getJSON(uri + query)
                .done(function (data) {
                    liveTweetsPos = [];
                    liveTweets = [];
                    liveTweetsNeg = [];
   
                    // On success, 'data' contains a list of tweets.
                    $.each(data, function (key, item) {
                        addTweet(item);
                    });
   
                    if (!$("#neutralBtn").hasClass('active')) {
                        $("#neutralBtn").button("toggle");
                    }
                    onNeutralBtn();
                })
                .fail(function (jqXHR, textStatus, err) {
                    $('#statustext').text('Error: ' + err);
                });
        }
   
        function addTweet(item) {
            //Add tweet to the heat map arrays.
            var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
            if (item.Sentiment > 0) {
                liveTweetsPos.push(tweetLocation);
            } else if (item.Sentiment < 0) {
                liveTweetsNeg.push(tweetLocation);
            } else {
                liveTweets.push(tweetLocation);
            }
        }
   
        function onPositiveBtn() {
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }
   
            heatmapPos.SetPoints(liveTweetsPos);
            heatmapPos.Show();
            heatmapNeg.Hide();
            heatmap.Hide();
   
            $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
        }
   
        function onNeutralBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#negativeBtn").hasClass('active')) {
                $("#negativeBtn").button("toggle");
            }
   
            heatmap.SetPoints(liveTweets);
            heatmap.Show();
            heatmapNeg.Hide();
            heatmapPos.Hide();
   
            $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
        }
   
        function onNegativeBtn() {
            if ($("#positiveBtn").hasClass('active')) {
                $("#positiveBtn").button("toggle");
            }
            if ($("#neutralBtn").hasClass('active')) {
                $("#neutralBtn").button("toggle");
            }
   
            heatmapNeg.SetPoints(liveTweetsNeg);
            heatmapNeg.Show();
            heatmap.Hide();;
            heatmapPos.Hide();;
   
            $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
        }
   
        function getPosNegRatio() {
            if (liveTweetsNeg.length == 0) {
                return "";
            }
            else {
                var ratio = liveTweetsPos.length / liveTweetsNeg.length;
                var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
                return "Positive/Negative Ratio: " + str;
            }
        }

**Pour modifier layout.cshtml**

1. Dans l’**Explorateur de solutions**, développez **TweetSentimentWeb**, **Affichages**, **Partagé**, puis double-cliquez sur _**Layout.cshtml**.
2. Remplacez le contenu par ce qui suit :
   
        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="utf-8" />
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>@ViewBag.Title</title>
            @Styles.Render("~/Content/css")
            @Scripts.Render("~/bundles/modernizr")
            <!-- Bing Maps -->
            <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
            <!-- Spatial Dashboard JavaScript -->
            <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
        </head>
        <body onload="initialize()">
            <div class="navbar navbar-inverse navbar-fixed-top">
                <div class="container">
                    <div class="navbar-header">
                        <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                            <span class="icon-bar"></span>
                        </button>
                    </div>
                    <div class="navbar-collapse collapse">
                        <div class="row">
                            <ul class="nav navbar-nav col-lg-5">
                                <li class="col-lg-12">
                                    <div class="navbar-form">
                                        <input id="searchbox" type="search" class="form-control">
                                        <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
                                    </div>
                                </li>
                            </ul>
                            <ul class="nav navbar-nav col-lg-7">
                                <li>
                                    <div class="navbar-form">
                                        <div class="btn-group" data-toggle="buttons-radio">
                                            <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
                                            <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
                                            <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
                                        </div>
                                    </div>
                                </li>
                                <li><span id="statustext" class="navbar-text"></span></li>
                            </ul>
                        </div>
                    </div>
                </div>
            </div>
            <div class="map_container">
                @RenderBody()
            </div>
            @Scripts.Render("~/bundles/jquery")
            @Scripts.Render("~/bundles/bootstrap")
            @RenderSection("scripts", required: false)
        </body>
        </html>

**Pour modifier Index.cshtml**

1. Dans l’**Explorateur de solutions**, développez **TweetSentimentWeb**, **Affichages**, **Accueil**, puis double-cliquez sur **Index.cshtml**.
2. Remplacez le contenu par ce qui suit :
   
        @{
            ViewBag.Title = "Tweet Sentiment";
        }

        <div class="map_container">
            <div id="map_canvas"/>
        </div>

**Pour modifier le fichier site.css**

1. Dans l’**Explorateur de solutions**, développez **TweetSentimentWeb** et **Contenu**, puis double-cliquez sur **Site.css**.
2. Ajoutez le code suivant au fichier :
   
        /* make container, and thus map, 100% width */
        .map_container {
            width: 100%;
            height: 100%;
        }
   
        #map_canvas{
          height:100%;
        }
   
        #tweets{
          position: absolute;
          top: 60px;
          left: 75px;
          z-index:1000;
          font-size: 30px;
        }

**Pour modifier le fichier global.asax**

1. Dans l’**Explorateur de solutions**, développez **TweetSentimentWeb**, puis double-cliquez sur **Global.asax**.
2. Ajoutez les instructions **using** suivantes :
   
        using System.Web.Http;
3. Ajoutez les lignes suivantes dans la fonction **Application_Start()** :
   
        // Register API routes
        GlobalConfiguration.Configure(WebApiConfig.Register);
   
    Modifiez l'enregistrement des itinéraires API pour que le contrôleur API Web fonctionne à l'intérieur de l'application MVC.

**Pour exécuter l'application Web**

1. Vérifiez que l'application console de service de diffusion est en cours d'exécution de façon à pouvoir observer les modifications en temps réel.
2. Appuyez sur la touche **F5** pour exécuter l'application Web.
   
    ![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
3. Dans la zone de texte, entrez un mot clé, puis cliquez sur **OK**.  En fonction des données collectées dans la table HBase, il se peut que certains mots clés soient introuvables. Essayez quelques mots clés communs tels que « amour », « xbox », « playstation ». 
4. Basculez entre **Positif**, **Neutre** et **Négatif** pour comparer les sentiments sur le sujet.
5. Laissez le service de diffusion s'exécuter une heure de plus, puis effectuez une recherche sur le même mot clé et comparez les résultats.

Vous pouvez également déployer l'application sur les Sites Web Microsoft Azure. Pour les instructions, consultez la page [Prise en main des Sites Web Azure et de ASP.NET][website-get-started].

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à recevoir des tweets, analyser les sentiments des tweets, enregistrer les données de sentiments dans HBase et présenter les données de sentiments Twitter en temps réel sur Bing Maps. Pour plus d'informations, consultez les rubriques suivantes :

* [Prise en main de HDInsight][hdinsight-get-started]
* [Configuration de la géo-réplication HBase dans HDInsigtht](hdinsight-hbase-replication.md) 
* [Analyse des données Twitter avec Hadoop dans HDInsight][hdinsight-analyze-twitter-data]
* [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-delay-data]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]

[hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[website-get-started]: ../app-service-web/web-sites-dotnet-get-started.md



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-analyze-twitter-data]: hdinsight-analyze-twitter-data.md




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md


