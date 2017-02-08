---
title: "Exemple de code : analyser les données exportées depuis Application Insights | Microsoft Docs"
description: "Codez votre propre analyse des données de télémétrie dans Application Insights à l’aide de la fonctionnalité d’exportation continue. Enregistrez les données dans SQL."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 3ffb62b6-3fe9-455d-a260-b2a0201b5ecd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 7a9c40081f52b2ffe918f4612f790f7fd08acc5a
ms.openlocfilehash: b0782ed5675e5256694f7b9f4e98750e57d23e0a


---
# <a name="code-sample-parse-data-exported-from-application-insights"></a>Exemple de code : analyser les données exportées depuis Application Insights
Cet article explique comment écrire du code pour traiter les données exportées depuis [Azure Application Insights][start] à l’aide de l'[exportation continue][export]. Cette fonctionnalité permet de déplacer vos données de télémétrie vers un stockage Azure au format JSON et nécessite l’écriture de code pour analyser les objets JSON et créer des lignes dans une table de base de données.

À titre d'exemple, nous allons écrire du code pour déplacer vos données de télémétrie d'Application Insights vers une base de données SQL.

Avant de commencer, notez les points suivants :

* Une méthode plus efficace de transférer des données exportées dans une base de données consiste [à utiliser Stream Analytics](app-insights-code-sample-export-sql-stream-analytics.md), mais ici notre objectif est de vous montrer un code permettant de traiter les données exportées. Vous pourriez adapter cet exemple de code pour effectuer d'autres actions à l'aide des données de télémétrie exportées.
* Dans cet exemple, nous déplaçons les données vers une base de données Azure en exécutant le code dans un rôle de travail Azure. Mais vous pouvez adapter ce code pour l'exécute sur un serveur local afin d'extraire les données vers un serveur SQL local.
* Vous pouvez [écrire du code pour accéder directement à vos données de télémétrie](http://dev.applicationinsights.io/) dans Application Insights, sans les exporter.

Si vous n’avez pas encore commencé à surveiller votre application web avec Application Insights, [faites-le maintenant][start].



## <a name="create-storage-in-azure"></a>Création d’un stockage dans Azure
Les données d'Application Insights sont toujours exportées vers un compte de stockage Azure au format JSON. C'est à partir de ce système de stockage que votre code lit les données.

1. Créez un compte de stockage « classique » dans votre abonnement sur le [portail Azure][portal].
   
    ![Sur le portail Azure, choisissez Nouveau, Données, Stockage.](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)
2. Créez un conteneur.
   
    ![Dans le nouvel emplacement de stockage, sélectionnez Conteneurs, cliquez sur la mosaïque Conteneurs puis sur Ajouter.](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)

## <a name="start-continuous-export-to-azure-storage"></a>Démarrer l’exportation continue vers le stockage Azure
1. Sur le portail Azure, accédez à la ressource Application Insights que vous avez créée pour votre application.
   
    ![Sélectionnez Parcourir, Application Insights, puis votre application.](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)
2. Créez une exportation continue.
   
    ![Choisissez Paramètres, Exportation continue, Ajouter.](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)

    Sélectionnez le compte de stockage que vous avez préalablement créé.

    ![Définissez la destination de l’exportation.](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)

    Définissez les types d’événements que vous souhaitez afficher :

    ![Choisissez les types d’événements.](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

1. Laissez les données s'accumuler. Installez-vous confortablement et laissez les utilisateurs utiliser votre application pendant un certain temps. Les données de télémétrie vont vous être transmises et vous permettre d’afficher des graphiques statistiques dans [Metrics explorer](app-insights-metrics-explorer.md) et des événements dans [Recherche de diagnostic](app-insights-diagnostic-search.md). 
   
    Les données seront également exportées vers votre stockage. 
2. Inspectez les données exportées. Dans Visual Studio, sélectionnez **Afficher / Cloud Explorer**, puis ouvrez Azure / Stockage. (Si vous n'avez pas cette option, vous devez installer le SDK Azure : Ouvrez la boîte de dialogue Nouveau projet et ouvrez Visual C# / Cloud / Obtenir Microsoft Azure SDK pour .NET.)
   
    ![Dans Visual Studio, ouvrez Explorateur de serveurs, Azure, Stockage](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)
   
    Prenez note de la partie commune du nom du chemin d'accès, qui est dérivée du nom de l'application et de la clé d'instrumentation. 

Les événements sont écrits dans des fichiers blob au format JSON. Chaque fichier peut contenir un ou plusieurs événements. Donc, nous devons lire les données d’événement et filtrer les champs voulus. Nous pourrions faire toutes sortes de choses avec les données, mais notre objectif aujourd’hui est d’écrire du code pour déplacer les données vers une base de données SQL. Cette action va simplifier l’exécution d’un grand nombre de requêtes intéressantes.

## <a name="create-an-azure-sql-database"></a>Création d’une base de données SQL Azure
Pour cet exemple, nous allons écrire du code pour envoyer les données vers une base de données.

Une fois encore, à partir de votre abonnement dans le [portail Azure][portal], créez la base de données (et un nouveau serveur le cas échéant) dans laquelle vous allez écrire les données.

![Nouveau, Données, SQL](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)

Assurez-vous que le serveur de base de données permet d’accéder aux services Azure :

![Parcourir, Serveurs, votre serveur, Paramètres, Pare-feu, Autoriser l’accès à Azure](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)

## <a name="create-a-worker-role"></a>Créer un rôle de travail
Nous pouvons maintenant écrire du [code](https://sesitai.codeplex.com/) pour analyser le JSON dans les objets blob exportés et créer des enregistrements dans la base de données. Étant donné que la banque d’exportation et la base de données se trouvent toutes deux dans Azure, nous allons exécuter le code dans un rôle de travail Azure.

Ce code extrait automatiquement toutes les propriétés présentes dans le JSON. Pour obtenir une description des propriétés, consultez [Modèle d'exportation de données](app-insights-export-data-model.md).

#### <a name="create-worker-role-project"></a>Créer un projet de rôle de travail
Dans Visual Studio, créez un projet pour le rôle de travail :

![Nouveau projet, Visual C#, Cloud, Azure Cloud Service](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![Dans la boîte de dialogue du nouveau service cloud, choisissez Visual C#, Rôle de travail.](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)

#### <a name="connect-to-the-storage-account"></a>Se connecter au compte de stockage
Dans Azure, obtenez la chaîne de connexion à partir de votre compte de stockage :

![Dans le Compte de stockage, sélectionnez Clés, et copiez la chaîne de connexion principale.](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

Dans Visual Studio, configurez les paramètres du rôle de travail à l’aide de la chaîne de connexion de compte de stockage :

![Dans l’Explorateur de solutions, sous le projet Service cloud, développez Rôles et ouvrez votre rôle de travail. Ouvrez l’onglet des paramètres, choisissez Ajouter un paramètre et définissez name=StorageConnectionString, type=connection string, et cliquez pour définir la valeur. Définissez le paramètre manuellement et collez la chaîne de connexion.](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)

#### <a name="packages"></a>Packages
Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet Rôle de travail, puis sélectionnez Gérer les packages NuGet.
Recherchez et installez ces packages : 

* EntityFramework 6.1.2 ou version ultérieure : nous utiliserons cet outil pour générer le schéma de table de base de données à la volée, en fonction du contenu du JSON de l’objet blob.
* JsonFx : nous utiliserons cet outil pour aplatir le JSON sur les propriétés de classe C#.

Utilisez cet outil pour générer la classe C# à partir de notre document JSON unique. Il nécessite des modifications mineures, telles que l’aplatissage des tableaux JSON en propriété C# unique, puis à son tour en colonne unique dans la table de base de données (par exemple, urlData_port) 

* [Générateur de classes C# JSON](http://jsonclassgenerator.codeplex.com/)

## <a name="code"></a>Code
Vous pouvez placer ce code dans `WorkerRole.cs`.

#### <a name="imports"></a>Importations
    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### <a name="retrieve-the-storage-connection-string"></a>Récupérer la chaîne de connexion de stockage
    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### <a name="run-the-worker-at-regular-intervals"></a>Exécuter le travail à intervalles réguliers
Remplacez la méthode d’exécution existante, puis choisissez l’intervalle que vous préférez. Il doit être au moins d’une heure, car c’est le temps nécessaire à la fonctionnalité d’exportation pour terminer un objet JSON.

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000

        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### <a name="insert-each-json-object-as-a-table-row"></a>Insérer chaque objet JSON sous forme de ligne de table
    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
             {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
        //handle exception
      }
    }

#### <a name="parse-each-blob"></a>Analyser chaque objet blob
    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());

        string json;

        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());

          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));

          recCount = entities.Count();
          failureCount = 0; //resetting failure count

          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);

            Dictionary<string, object> dict = new Dictionary<string, object>();

            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");

            switch (FilterType)
            {
              case "PageViewPerformance":

              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
                }

              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }

            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);

            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;

            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### <a name="prepare-a-dictionary-for-each-json-document"></a>Préparer un dictionnaire pour chaque document JSON
    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }
        }

#### <a name="cast-the-json-document-into-c-class-telemetry-object-properties"></a>Effectuer une conversion de type du document JSON en propriétés d’objet de télémétrie de classe C#
     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }

            return res;
        }

#### <a name="pageviewperformance-class-file-generated-out-of-json-document"></a>Fichier de classe PageViewPerformance généré à partir du document JSON
    public class PageViewPerformance
    {
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### <a name="dbcontext-for-sql-interaction-by-entity-framework"></a>DBcontext pour l’interaction SQL par Entity Framework
    public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

Ajoutez votre chaîne de connexion de base de données au nom `TelemetryContext` dans `app.config`.

## <a name="schema-information-only"></a>Schéma (informations uniquement)
Voici le schéma de la table qui sera générée pour PageView.

> [!NOTE]
> Vous n’êtes pas obligé d’exécuter ce script. Les attributs du JSON déterminent les colonnes de la table.
> 
> 

    CREATE TABLE [dbo].[PageViewPerformances](
    [Id] [uniqueidentifier] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlData_port] [int] NOT NULL,
    [urlData_protocol] [nvarchar](max) NULL,
    [urlData_host] [nvarchar](max) NULL,
    [urlData_base] [nvarchar](max) NULL,
    [urlData_hashTag] [nvarchar](max) NULL,
    [total_value] [float] NOT NULL,
    [networkConnection_value] [float] NOT NULL,
    [sendRequest_value] [float] NOT NULL,
    [receiveRequest_value] [float] NOT NULL,
    [clientProcess_value] [float] NOT NULL,
    [name] [nvarchar](max) NULL,
    [User] [nvarchar](max) NULL,
    [internal_data_id] [nvarchar](max) NULL,
    [internal_data_documentVersion] [nvarchar](max) NULL,
    [context_data_eventTime] [datetime] NULL,
    [context_device_id] [nvarchar](max) NULL,
    [context_device_type] [nvarchar](max) NULL,
    [context_device_os] [nvarchar](max) NULL,
    [context_device_osVersion] [nvarchar](max) NULL,
    [context_device_locale] [nvarchar](max) NULL,
    [context_device_userAgent] [nvarchar](max) NULL,
    [context_device_browser] [nvarchar](max) NULL,
    [context_device_browserVersion] [nvarchar](max) NULL,
    [context_device_screenResolution_value] [nvarchar](max) NULL,
    [context_user_anonId] [nvarchar](max) NULL,
    [context_user_anonAcquisitionDate] [nvarchar](max) NULL,
    [context_user_authAcquisitionDate] [nvarchar](max) NULL,
    [context_user_accountAcquisitionDate] [nvarchar](max) NULL,
    [context_session_id] [nvarchar](max) NULL,
    [context_session_isFirst] [bit] NOT NULL,
    [context_operation_id] [nvarchar](max) NULL,
    [context_location_point_lat] [float] NOT NULL,
    [context_location_point_lon] [float] NOT NULL,
    [context_location_clientip] [nvarchar](max) NULL,
    [context_location_continent] [nvarchar](max) NULL,
    [context_location_country] [nvarchar](max) NULL,
    [context_location_province] [nvarchar](max) NULL,
    [context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


Pour voir cet exemple en action, [téléchargez](https://sesitai.codeplex.com/) le code de travail complet, modifiez les paramètres `app.config` et publiez le rôle de travail dans Azure.

## <a name="next-steps"></a>Étapes suivantes

* [Écriture du code pour accéder directement à vos données de télémétrie](http://dev.applicationinsights.io/)
* [Exportation vers SQL à l’aide d’un rôle de travail](app-insights-code-sample-export-telemetry-sql-database.md)
* [Exportation continue dans Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [Modèle d’exportation de données](app-insights-export-data-model.md)
* [Plus d'exemples et de procédures pas à pas](app-insights-code-samples.md)


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md





<!--HONumber=Nov16_HO3-->


