<properties
   pageTitle="Utiliser les compteurs de Performance dans Azure Diagnostics | Microsoft Azure"
   description="Les compteurs de performance dans les services ou les machines virtuelles de cloud Azure permettent de trouver les goulots d’étranglement et d’ajuster les performances."
   services="cloud-services"
   documentationCenter=".net"
   authors="rboucher"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="robb" />

# Créer et utiliser des compteurs de performances dans une application Azure

Cet article décrit les avantages et la façon de placer des compteurs de performance dans votre application Microsoft Azure. Vous pouvez les utiliser pour collecter des données, détecter les goulots d’étranglement et régler les performances système et des applications.

Les compteurs de performances disponibles pour Windows Server, IIS et ASP.NET peuvent être recueillis et utilisés pour déterminer l’état de vos rôles Azure web, worker et machines virtuelles. Vous pouvez également créer et utiliser des compteurs de performance personnalisés.

Vous pouvez examiner les données du compteur de performances
1. Directement sur l’hôte d’application avec l’outil d’analyse des performances auquel on accède à l’aide de Bureau à distance
2. Avec System Center Operations Manager en utilisant le Pack d’administration Azure
3. Avec d’autres outils d’analyse ayant accès aux données de diagnostic transférées vers le stockage Azure. Voir [Stocker et afficher des données de diagnostic dans Azure Storage](https://msdn.microsoft.com/library/azure/hh411534.aspx) pour plus d’informations.  

Pour plus d’informations sur la configuration des performances de votre application dans le [portail Azure Classic](http://manage.azure.com/), consultez la page [Surveillance des services cloud](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

Pour obtenir d'autres instructions détaillées sur la création d'une stratégie de journalisation et de suivi, et sur l'utilisation des diagnostics et des autres techniques pour résoudre les problèmes et optimiser les applications Azure, consultez la page [Meilleures pratiques de dépannage pour développer des applications Azure](https://msdn.microsoft.com/library/azure/hh771389.aspx).


## Activer la surveillance du compteur de performance

Les compteurs de performance ne sont pas activés par défaut. Votre application ou une tâche de démarrage doit modifier la configuration de l’agent de diagnostics par défaut pour y inclure les compteurs de performances spécifiques que vous souhaitez surveiller pour chaque instance de rôle.

### Compteurs de performances disponibles pour Microsoft Azure

Azure fournit un sous-ensemble des compteurs de performances disponibles pour Windows Server, IIS et la pile ASP.NET. Le tableau suivant répertorie certains des compteurs de performance présentant un intérêt particulier pour les applications Azure.

|Catégorie de compteur : Objet (Instance)|Nom de compteur |Référence|
|---|---|---|
|Exceptions CLR .NET (_Global_)|# Nombre d’exceptions levées/s |Compteurs de performance des exceptions|
|Mémoire CLR .NET (_Global_) |% de temps dans GC |Compteurs de performance mémoire|
|ASP.NET |L’application redémarre |Compteurs de performances pour ASP.NET|
|ASP.NET |Durée d’exécution de la demande |Compteurs de performances pour ASP.NET|
|ASP.NET |Requêtes déconnectées |Compteurs de performances pour ASP.NET|
|ASP.NET |Redémarrage du processus Worker |Compteurs de performances pour ASP.NET|
|Application ASP.NET (__Total__)|Nombre Total de demandes |Compteurs de performances pour ASP.NET|
|Application ASP.NET (__Total__)|Demandes/s |Compteurs de performances pour ASP.NET|
|ASP.NET v4.0.30319 |Durée d’exécution de la demande |Compteurs de performances pour ASP.NET|
|ASP.NET v4.0.30319 |Délai d’attente de demande |Compteurs de performances pour ASP.NET|
|ASP.NET v4.0.30319 |Demandes actuelles |Compteurs de performances pour ASP.NET|
|ASP.NET v4.0.30319 |Demandes en file d’attente |Compteurs de performances pour ASP.NET|
|ASP.NET v4.0.30319 |Demandes rejetées |Compteurs de performances pour ASP.NET|
|Mémoire |Nombre d’octets disponibles |Compteurs de performance mémoire|
|Mémoire |Octets dédiés |Compteurs de performance mémoire|
|Processeur(\_Total) |% temps processeur |Compteurs de performances pour ASP.NET| |TCPv4 |Échecs de connexion |Objet TCP| |TCPv4 |Connexions établies |Objet TCP| |TCPv4 |Réinitialisation des connexions |Objet TCP| |TCPv4 |Segments envoyés/s |Objet TCP| |Interface réseau(*) |Octets reçus/s |Objet d’interface réseau| |Interface réseau(*) |Octets envoyés/s |Objet d’interface réseau| |Interface réseau(Microsoft Virtual Machine Bus Network Adapter \_2)|Octets reçus/s|Objet d’interface réseau| |Interface réseau (Microsoft Virtual Machine Bus Network Adapter \_2)|Octets envoyés/s|Objet d’interface réseau | |Interface réseau (Microsoft Virtual Machine Bus Network Adapter \_2)|Nombre total d’octets/s|Objet d’interface réseau|

## Créer et ajouter des compteurs de performance personnalisés à votre application

Azure assure la prise en charge pour créer et modifier des compteurs de performance personnalisés pour les rôles web et worker. Les compteurs peuvent être utilisés pour suivre et surveiller le comportement spécifique à l’application. Vous pouvez créer et supprimer des catégories de compteurs de performance personnalisés et spécificateurs depuis une tâche de démarrage, un rôle web ou un rôle de travail avec des autorisations de niveau élevé.

>[AZURE.NOTE] Les codes qui modifient des compteurs de performance personnalisés doivent avoir des autorisations élevées pour s’exécuter. Si le code est dans un rôle web ou un rôle de travail, le rôle doit inclure la balise <Runtime executionContext="elevated" /> dans le fichier ServiceDefinition.csdef pour que le rôle s’initialise correctement.

Vous pouvez envoyer des données de compteurs de performance personnalisés vers le stockage Azure grâce à l’agent de diagnostics.

Les données de compteur de performance standard sont générées par les processus Azure. Les données du compteur de performance personnalisé doivent être créées par votre rôle web ou votre application de travail. Voir [Types de compteurs de performance](https://msdn.microsoft.com/library/z573042h.aspx) pour plus d’informations sur les types de données pouvant être stockées dans des compteurs de performance personnalisés. Voir [Exemple de compteurs de performance](http://code.msdn.microsoft.com/azure/) pour obtenir un exemple créant et définissant les données de compteurs de performance personnalisés dans un rôle web.

## Stockez et affichez les données de compteur de performance

Azure met en mémoire cache les données de compteur de performance avec d’autres informations de diagnostic. Ces données sont disponibles pour la surveillance à distance pendant l’exécution de l’instance de rôle à l’aide d’un accès du Bureau à distance pour afficher les outils de l’Analyseur de performances. Pour conserver les données en dehors de l’instance de rôle, l’agent de diagnostic doit transférer les données vers le stockage Azure. La limite de taille des données du compteur de performances mises en cache peut être configurée dans l’agent de diagnostics, ou il peut être configuré pour faire partie d’une limite partagée pour toutes les données de diagnostic. Pour plus d’informations sur la définition de la taille de la mémoire tampon, consultez [OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) et [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx). Voir [Stocker et afficher les données de diagnostic dans Azure Storage](https://msdn.microsoft.com/library/azure/hh411534.aspx) pour une vue d’ensemble de la configuration de l’agent de diagnostic pour transférer des données vers un compte de stockage.

Chaque instance de compteur de performance configurée est enregistrée à un taux d’échantillonnage spécifié et les données échantillonnées sont transférées au compte de stockage par demande de transfert planifiée ou demande de transfert à la demande. Les transferts automatiques peuvent être planifiés au maximum une fois par minute. Les données de compteur de performance transférées par l’agent de diagnostics sont stockées dans un tableau, WADPerformanceCountersTable, dans le compte de stockage. Cette table peut-être accessible et interrogée avec les méthodes de l’API standard de stockage Azure. Voir [Exemple de compteur de performance Microsoft Azure](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) pour obtenir un exemple d’interrogation et d’affichage des données de compteur de performances du tableau WADPerformanceCountersTable.

>[AZURE.NOTE] Selon la fréquence de transfert de l’agent de diagnostics et de la latence de la file d’attente, les données de compteur de performance les plus récentes du compte de stockage peuvent être obsolètes pendant plusieurs minutes.

## Activer les compteurs de performance à l’aide du fichier de configuration de diagnostics

Utilisez la procédure suivante pour activer les compteurs de performances dans votre application Azure.

## Configuration requise

Cette section part du principe que vous avez importé le moniteur de diagnostics dans votre application et que vous avez ajouté le fichier de configuration de diagnostic à votre solution Visual Studio (diagnostics.wadcfg dans le kit de développement logiciel 2.4 et version antérieure ou diagnostics.wadcfgx dans le kit de développement logiciel 2.5 et ultérieur). Voir les étapes 1 et 2 dans [Activation de Diagnostics dans Azure Cloud Services et Virtual Machines](./cloud-services-dotnet-diagnostics.md) pour plus d’informations.

## Étape 1 : collecte et stockage de données de compteurs de performances

Après avoir ajouté le fichier diagnostics à votre solution Visual Studio, vous pouvez configurer la collecte et le stockage de données de compteurs de performances dans une application Azure. Pour cela, vous devez ajouter des compteurs de performances au fichier diagnostics. Dans un premier temps, les données de diagnostic, y compris les compteurs de performances, sont collectées au niveau de l'instance. Sachant que les données sont conservées dans la table WADPerformanceCountersTable du service de Table Azure, vous serez également amené à spécifier le compte de stockage dans votre application. Si vous testez votre application en local dans l'émulateur de calcul, vous pouvez également stocker les données de diagnostic en local dans l'émulateur de stockage. Avant de stocker les données de diagnostic, vous devez accéder au [portail Azure Classic](http://manage.windowsazure.com/) et créer un compte de stockage. Pour éviter de payer des frais de bande passante externes et réduire le temps de réponse, il est recommandé de situer votre compte de stockage dans la même région que votre application Azure.

### Ajouter des compteurs de performances au fichier de diagnostics

Il existe de nombreux compteurs que vous pouvez utiliser. L’exemple suivant montre plusieurs compteurs de performances qui sont recommandés pour la surveillance des rôles web et worker.

Ouvrez le fichier de diagnostics (diagnostics.wadcfg dans le Kit de développement logiciel 2.4 et antérieur ou diagnostics.wadcfgx dans le kit de développement logiciel 2.5 et versions ultérieures) et ajoutez le code suivant à l’élément DiagnosticMonitorConfiguration :

```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
       <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)# of marshalling" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)# Bytes in all Heaps" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
       <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
    </PerformanceCounters>
```

L'attribut bufferQuotaInMB spécifie la capacité maximale de stockage du système du fichiers disponible pour le type de collecte de données (journaux Azure, journaux IIS, etc.). La valeur par défaut est 0. Lorsque le quota est atteint, les données les plus anciennes sont supprimées à mesure que de nouvelles données sont ajoutées. La somme de toutes les propriétés bufferQuotaInMB doit être supérieure à la valeur de l'attribut OverallQuotaInMB. Pour plus d'informations sur la façon de déterminer la quantité de stockage nécessaire à la collecte de données de diagnostic, consultez la section Configuration de WAD de la page [Meilleures pratiques de dépannage pour développer des applications Azure](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx).

L'attribut scheduledTransferPeriod spécifie le délai entre les transferts de données planifiés, arrondi à la minute supérieure. Dans les exemples suivants, sa valeur est définie sur PT30M (30 minutes). Si la définition d'une période de transfert plus courte, par exemple 1 minute, peut avoir des conséquences néfastes sur les performances de l'application en production, cela peut s'avérer utile en phase de test pour obtenir des diagnostics rapides. La période de transfert planifiée doit être suffisamment courte pour éviter que les données de diagnostic soient remplacées au niveau de l'instance, mais suffisamment longue pour qu'elle n'ait pas d'incidence sur les performances de votre application.

L’attribut counterSpecifier spécifie le compteur de performances à relever. L’attribut sampleRate spécifie le taux auquel le compteur de performance est échantillonné, dans ce cas, 30 secondes.

Une fois que vous avez ajouté les compteurs de performances à collecter, enregistrez vos modifications dans le fichier diagnostics. Ensuite, vous devez spécifier le compte de stockage dans lequel les données de diagnostic seront conservées.

### Spécification du compte de stockage

Pour conserver vos informations de diagnostic dans votre compte Azure Storage, vous devez spécifier une chaîne de connexion dans le fichier de configuration (ServiceConfiguration.cscfg) de votre service.

Dans le kit de développement logiciel (SDK) Azure 2.5, le compte de stockage peut être spécifié dans le fichier diagnostics.wadcfgx.

>[AZURE.NOTE] Ces instructions s’appliquent uniquement au kit de développement logiciel Azure 2.4 et versions antérieures. Dans le kit de développement logiciel (SDK) Azure 2.5, le compte de stockage peut être spécifié dans le fichier diagnostics.wadcfgx.

Pour définir les chaînes de connexion :

1. Ouvrez le fichier ServiceConfiguration.Cloud.cscfg à l’aide de l’éditeur de texte de votre choix, puis définissez la chaîne de connexion de votre stockage. Les valeurs de *AccountName* et de *AccountKey* se trouvent dans le portail Azure Classic, dans le tableau de bord du compte de stockage, sous Gérer les clés.

    ```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. Enregistrez le fichier ServiceConfiguration.Cloud.cscfg.

3. Ouvrez le fichier ServiceConfiguration.Local.cscfg et vérifiez que la valeur de UseDevelopmentStorage est définie sur true.

    ```
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
Dès lors que les chaînes de connexion sont définies, une fois déployée, votre application conserve les données de diagnostic dans votre compte de stockage.
4. Enregistrez et générez votre projet, puis déployez votre application.

## Étape 2 : (facultatif) création de compteurs de performances personnalisés

Outre les compteurs de performances prédéfinis, vous pouvez ajouter les vôtres pour surveiller les rôles Web ou de travail. Les compteurs de performances personnalisés permettent de suivre et de surveiller le comportement propre à une application. Vous pouvez les créer ou les supprimer dans une tâche de démarrage, un rôle Web ou un rôle de travail avec des autorisations élevées.

Étape 2 : L’agent de diagnostics Azure actualise la configuration du compteur de performance à partir du fichier .wadcfg une minute après le démarrage. Si vous créez des compteurs de performance personnalisés dans la méthode OnStart et vos tâches de démarrage prennent plus d’une minute pour s’exécuter, vos compteurs de performance personnalisés ne seront pas créés lorsque l’agent de Diagnostics Microsoft Azure tente de les charger. Dans ce scénario, vous verrez qu’Azure Diagnostics capture correctement toutes les données de diagnostic, excepté les compteurs de performance personnalisés. Pour résoudre ce problème, créez les compteurs de performance dans une tâche de démarrage ou déplacez certaines de vos tâches de démarrage vers la méthode OnStart après avoir créé les compteurs de performances.

Pour créer un simple compteur de performances personnalisé nommé « \\MyCustomCounterCategory\\MyButton1Counter », procédez comme suit :

1. Ouvrez le fichier de définition de service (CSDEF) de votre application.
2. Ajoutez l'élément Runtime à l'élément WebRole ou WorkerRole pour permettre une exécution avec des privilèges élevés :

    ```
    <runtime executioncontext="elevated"/>
    ```
3. Enregistrez le fichier .
4. Ouvrez le fichier de diagnostics (diagnostics.wadcfg dans le Kit de développement logiciel 2.4 et antérieur ou diagnostics.wadcfgx dans le kit de développement logiciel 2.5 et versions ultérieures) et ajoutez le code suivant à DiagnosticMonitorConfiguration : 

    ```
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
     <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. Enregistrez le fichier .
6. Créez la catégorie de compteur de performances personnalisée dans la méthode OnStart de votre rôle avant d'appeler base.OnStart. L'exemple C# suivant permet de créer une catégorie personnalisée, si elle n'existe pas déjà :

    ```
    public override bool OnStart()
    {
    if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
    {
       CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

       // add a counter tracking user button1 clicks
       CounterCreationData operationTotal1 = new CounterCreationData();
       operationTotal1.CounterName = "MyButton1Counter";
       operationTotal1.CounterHelp = "My Custom Counter for Button1";
       operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
       counterCollection.Add(operationTotal1);

       PerformanceCounterCategory.Create(
         "MyCustomCounterCategory",
         "My Custom Counter Category",
         PerformanceCounterCategoryType.SingleInstance, counterCollection);

       Trace.WriteLine("Custom counter category created.");
    }
    else{
       Trace.WriteLine("Custom counter category already exists.");
    }

    return base.OnStart();
    }
    ```
7. Mettez à jour les compteurs dans l'application. Dans l'exemple suivant, un compteur de performances personnalisé est mis à jour lors d'événements Button1\_Click :

    ```
    protected void Button1_Click(object sender, EventArgs e)
        {
         PerformanceCounter button1Counter = new PerformanceCounter(
           "MyCustomCounterCategory",
           "MyButton1Counter",
           string.Empty,
           false);
         button1Counter.Increment();
        this.Button1.Text = "Button 1 count: " +
           button1Counter.RawValue.ToString();
        }
    ```
8. Enregistrez le fichier .  

Les données du compteur de performances personnalisé vont être collectées par le moniteur de diagnostics Azure.

## Étape 3 : interrogation des données de compteurs de performances

Une fois votre application déployée et exécutée, le moniteur de diagnostics commence à collecter les compteurs de performances et à conserver ces données dans le stockage Azure. Pour examiner les données des compteurs de performances dans la table WADPerformanceCountersTable, vous pouvez utiliser des outils tels que l’Explorateur de serveurs de Visual Studio, [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/) ou [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) de Cerebrata. Vous pouvez également interroger le service de Table par programme en [C#](../storage/storage-dotnet-how-to-use-tables.d), [Java](../storage/storage-java-how-to-use-table-storage.md), [Node.js](../storage/storage-nodejs-how-to-use-table-storage.md), [Python](../storage/storage-python-how-to-use-table-storage.md), [Ruby](../storage/storage-ruby-how-to-use-table-storage.md) ou [PHP](../storage/storage-php-how-to-use-table-storage.md).

Dans l'exemple C# suivant, une requête simple est exécutée sur la table WADPerformanceCountersTable et les données de diagnostic sont enregistrées dans un fichier CSV. Une fois que les compteurs de performances sont enregistrés dans un fichier CSV, vous pouvez visualiser les données à l'aide des fonctionnalités de création de graphiques de Microsoft Excel ou d'un autre outil. Veillez à ajouter une référence à Microsoft.WindowsAzure.Storage.dll, qui figure dans le Kit de développement logiciel (SDK) Azure pour .NET d'octobre 2012 et les versions ultérieures. L’assembly est installé dans le répertoire %Program Files%\\Microsoft SDKs\\Microsoft Azure.NET SDK\\version-num\\ref\\.

```
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ...

    // Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
    // you store your connection string using the Microsoft Azure service configuration
    // system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
    // to retrieve your storage connection string.  If you're not using Cloud Services, it's
    // recommended that you store the connection string in your web.config or app.config file.
    // Use the ConfigurationManager type to retrieve your storage connection string.

    string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

    // Get a reference to the storage account using the connection string.  You can also use the development
    // storage account (Storage Emulator) for local debugging.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
    CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

    // Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
    TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
       .Where(
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
          TableOperators.And,
          TableQuery.CombineFilters(
          TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
          TableOperators.And,
          TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
          )
       )
    );

    // Execute the table query.
    IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

    // Process the query results and build a CSV file.
    StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

    foreach (PerformanceCountersEntity entity in result)
    {
       sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
          + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
    }

    StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
    sw.Write(sb.ToString());
    sw.Close();
```

Les entités mappent vers les objets C# en utilisant une classe personnalisée dérivée d’une **TableEntity**. Le code suivant permet de définir une classe d'entité qui représente un compteur de performances dans la table **WADPerformanceCountersTable**.


    public class PerformanceCountersEntity : TableEntity
    {
       public long EventTickCount { get; set; }
       public string DeploymentId { get; set; }
       public string Role { get; set; }
       public string RoleInstance { get; set; }
       public string CounterName { get; set; }
       public double CounterValue { get; set; }
    }


## Étapes suivantes
[Afficher d’autres articles sur les diagnostics Azure](../azure-diagnostics.md)

<!---HONumber=AcomDC_0302_2016-->