<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="" editor="" />

Utilisation de compteurs de performances dans Azure
===================================================

Vous pouvez utiliser des compteurs de performances dans une application Azure pour collecter des données qui peuvent vous aider à repérer les goulots d'étranglement au niveau du système et à ajuster les performances du système et de l'application. Les compteurs de performances accessibles avec Windows Server 2008, Windows Server 2012, IIS et ASP.NET peuvent être collectés et utilisés pour déterminer l'intégrité de votre application Azure.

Cette rubrique explique comment activer les compteurs de performances dans votre application à l'aide du fichier de configuration diagnostics.wadcfg. Pour plus d'informations sur la configuration des performances de votre application dans le [portail de gestion Azure](http://manage.windowsazure.com), consultez la page [Surveillance des services cloud](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/). Pour obtenir d'autres instructions détaillées sur la création d'une stratégie de journalisation et de suivi, et sur l'utilisation des diagnostics et des autres techniques pour résoudre les problèmes et optimiser les applications Azure, consultez la page [Meilleures pratiques de dépannage pour développer des applications Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh771389.aspx).

Cette procédure comprend les étapes suivantes :

-   [Configuration requise](#prereqs)
-   [Étape 1 : collecte et stockage de données de compteurs de performances](#step1)
-   [Étape 2 : (facultatif) création de compteurs de performances personnalisés](#step2)
-   [Étape 3 : interrogation des données de compteurs de performances](#step3)
-   [Étapes suivantes](#nextsteps)
-   [Ressources supplémentaires](#additional)

Conditions préalables
---------------------

Cet article part du principe que vous avez importé le moniteur de diagnostics dans votre application et que vous avez ajouté le fichier de configuration diagnostics.wadcfg à votre solution Visual Studio. Pour plus d'informations, consultez les étapes 1 et 2 de la page [Activation des diagnostics dans Azure](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/).

Étape 1 : collecte et stockage de données de compteurs de performances
----------------------------------------------------------------------

Après avoir ajouté le fichier diagnostics.wadcfg à votre solution Visual Studio, vous pouvez configurer la collecte et le stockage de données de compteurs de performances dans une application Azure. Pour cela, vous devez ajouter des compteurs de performances au fichier diagnostics.wadcfg. Dans un premier temps, les données de diagnostic, y compris les compteurs de performances, sont collectées au niveau de l'instance. Sachant que les données sont conservées dans la table **WADPerformanceCountersTable** du service de Table Azure, vous serez également amené à spécifier le compte de stockage dans votre application. Si vous testez votre application en local dans l'émulateur de calcul, vous pouvez également stocker les données de diagnostic en local dans l'émulateur de stockage. Avant de stocker les données de diagnostic, vous devez accéder au [portail de gestion Azure](http://manage.windowsazure.com) et créer un compte de stockage. Pour éviter de payer des frais de bande passante externes et réduire le temps de réponse, il est recommandé de situer votre compte de stockage dans la même région que votre application Azure.

### Ajout de compteurs de performances au fichier diagnostics.wadcfg

Les compteurs de performances qu'il vous est possible de collecter sont nombreux. L'exemple suivant fait référence à plusieurs compteurs de performances qui sont recommandés dans le cadre d'une surveillance des rôles Web et de travail.

Ouvrez le fichier diagnostics.wadcfg et ajoutez ce qui suit à l'élément **DiagnosticMonitorConfiguration** :

     	<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />
        <!-- Utilisez les compteurs de catégorie Process(w3wp) dans un rôle Web -->
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />
        <!-- Utilisez les compteurs de catégorie Process(w3wp) dans un rôle de travail.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" /> 
		-->
     	<PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
        </PerformanceCounters>    

L'attribut **bufferQuotaInMB** spécifie la capacité maximale de stockage du système du fichiers disponible pour le type de collecte de données (journaux Azure, journaux IIS, etc.). La valeur par défaut est 0. Lorsque le quota est atteint, les données les plus anciennes sont supprimées à mesure que de nouvelles données sont ajoutées. La somme de toutes les propriétés **bufferQuotaInMB** doit être supérieure à la valeur de l'attribut **OverallQuotaInMB**. Pour plus d'informations sur la façon de déterminer la quantité de stockage nécessaire à la collecte de données de diagnostic, consultez la section Configuration de WAD de la page [Meilleures pratiques de dépannage pour développer des applications Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh771389.aspx).

L'attribut **scheduledTransferPeriod** spécifie le délai entre les transferts de données planifiés, arrondi à la minute supérieure. Dans les exemples suivants, sa valeur est définie sur PT30M (30 minutes). Si la définition d'une période de transfert plus courte, par exemple 1 minute, peut avoir des conséquences néfastes sur les performances de l'application en production, cela peut s'avérer utile en phase de test pour obtenir des diagnostics rapides. La période de transfert planifiée doit être suffisamment courte pour éviter que les données de diagnostic soient remplacées au niveau de l'instance, mais suffisamment longue pour qu'elle n'ait pas d'incidence sur les performances de votre application.

L'attribut **counterSpecifier** spécifie le compteur de performances à collecter.

L'attribut **sampleRate** spécifie la vitesse d'échantillonnage du compteur de performances, dans ce cas, 30 secondes.

Une fois que vous avez ajouté les compteurs de performances à collecter, enregistrez vos modifications dans le fichier diagnostics.wadcfg. Ensuite, vous devez spécifier le compte de stockage dans lequel les données de diagnostic seront conservées.

### Spécification du compte de stockage

Pour conserver vos informations de diagnostic dans votre compte Azure Storage, vous devez spécifier une chaîne de connexion dans le fichier de configuration (ServiceConfiguration.cscfg) de votre service. Les outils Azure pour Visual Studio version 1.4 (août 2011) et les versions ultérieures vous permettent d'avoir différents fichiers de configuration (ServiceConfiguration.cscfg), locaux et cloud. Pour le diagnostic, il est utile d'avoir plusieurs configurations de service. Vous pouvez ainsi effectuer des tests locaux à l'aide de l'émulateur de stockage tout en conservant un fichier de configuration distinct pour la production.

Pour définir les chaînes de connexion :

1.  Ouvrez le fichier ServiceConfiguration.Cloud.cscfg à l'aide de l'éditeur de texte de votre choix, puis définissez la chaîne de connexion pour votre compte de stockage :

        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
        </ConfigurationSettings>

    Les valeurs de **AccountName** et de **AccountKey** se trouvent dans le portail de gestion, dans le tableau de bord du compte de stockage, sous **Manage Keys**.

2.  Enregistrez le fichier ServiceConfiguration.Cloud.cscfg.
3.  Ouvrez le fichier ServiceConfiguration.Local.cscfg et vérifiez que la valeur de **UseDevelopmentStorage** est définie sur true (valeur par défaut).

        <ConfigurationSettings>

        </ConfigurationSettings>

    Dès lors que les chaînes de connexion sont définies, une fois déployée, votre application conserve les données de diagnostic dans votre compte de stockage.

4.  Enregistrez et générez votre projet, puis déployez votre application.

Étape 2 : (facultatif) création de compteurs de performances personnalisés
--------------------------------------------------------------------------

Outre les compteurs de performances prédéfinis, vous pouvez ajouter les vôtres pour surveiller les rôles Web ou de travail. Les compteurs de performances personnalisés permettent de suivre et de surveiller le comportement propre à une application. Vous pouvez les créer ou les supprimer dans une tâche de démarrage, un rôle Web ou un rôle de travail avec des autorisations élevées.

Pour créer un simple compteur de performances personnalisé nommé « \\MyCustomCounterCategory\\MyButton1Counter », procédez comme suit :

1.  Ouvrez le fichier de définition de service (CSDEF) de votre application.
2.  Ajoutez l'élément **Runtime** à l'élément **WebRole** ou **WorkerRole** pour permettre une exécution avec des privilèges élevés :

        <Runtime executionContext="elevated" />

3.  Enregistrez le fichier.
4.  Ouvrez le fichier diagnostics.wadcfg et ajoutez ce qui suit à l'élément **DiagnosticMonitorConfiguration** :

        <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
        <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
        </PerformanceCounters>        

5.  Enregistrez le fichier.
6.  Créez la catégorie de compteur de performances personnalisée dans la méthode **OnStart** de votre rôle avant d'appeler **base.OnStart**. L'exemple C\# suivant permet de créer une catégorie personnalisée, si elle n'existe pas déjà :

        public override bool OnStart()
        {
        if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
        {
           CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();

           // Ajoutez un compteur de suivi des clics utilisateur sur button1
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

7.  Mettez à jour les compteurs dans l'application. Dans l'exemple suivant, un compteur de performances personnalisé est mis à jour lors d'événements **Button1\_Click** :

        protected void Button1_Click(object sender, EventArgs e)
        {
           PerformanceCounter button1Counter = new PerformanceCounter(
              "MyCustomCounterCategory",
              "MyButton1Counter",
              string.Empty,
              false)

           button1Counter.Increment();
           this.Button1.Text = "Button 1 count: " +
              button1Counter.RawValue.ToString();
        }

8.  Enregistrez le fichier.

Une fois que cette procédure est exécutée, les données du compteur de performances personnalisé sont collectées par le moniteur de diagnostics Azure.

Étape 3 : interrogation des données de compteurs de performances
----------------------------------------------------------------

Une fois votre application déployée et exécutée, le moniteur de diagnostics commence à collecter les compteurs de performances et à conserver ces données dans le stockage Azure. Pour examiner les données des compteurs de performances dans la table **WADPerformanceCountersTable**, vous pouvez utiliser des outils tels que l'**Explorateur de serveurs de Visual Studio**, [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/) ou [Azure Diagnostics Manager](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) de Cerebrata. Vous pouvez également interroger le service de Table par programme en [C\#][], [Java](http://www.windowsazure.com/fr-fr/develop/java/how-to-guides/table-service/), [Node.js][], [Python](http://www.windowsazure.com/fr-fr/develop/python/how-to-guides/table-service/) ou [PHP](http://www.windowsazure.com/fr-fr/develop/php/how-to-guides/table-service/).

Dans l'exemple C\# suivant, une requête simple est exécutée sur la table **WADPerformanceCountersTable** et les données de diagnostic sont enregistrées dans un fichier CSV. Une fois que les compteurs de performances sont enregistrés dans un fichier CSV, vous pouvez visualiser les données à l'aide des fonctionnalités de création de graphiques de Microsoft Excel ou d'un autre outil. Veillez à ajouter une référence à Microsoft.WindowsAzure.Storage.dll, qui figure dans le Kit de développement logiciel (SDK) Azure pour .NET d'octobre 2012 et les versions ultérieures. L'assembly est installé dans le répertoire %Program Files%\\Microsoft SDKs\\Windows Azure.NET SDK\\version-num\\ref\\.

    	using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Table;

        ...

        // Obtenez la chaîne de connexion. Lorsque Azure Cloud Services est utilisé, il est recommandé 
        // de stocker la chaîne de connexion à l'aide du système de configuration de service
        // Azure (fichiers *.csdef et *.cscfg). Vous pouvez utiliser le type CloudConfigurationManager 
        // pour récupérer votre chaîne de connexion de stockage.  Si vous n'utilisez pas Cloud Services, il est
        // préférable de stocker la chaîne de connexion dans votre fichier web.config ou app.config.
        // Utilisez le type ConfigurationManager pour extraire votre chaîne de connexion de stockage.
        
        string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
        //string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;
            
        // Obtenez une référence au compte de stockage à l'aide de la chaîne de connexion.  Vous pouvez également utiliser le compte de stockage de développement (émulateur de stockage)
        // pour le débogage local.              
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
        //CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

        // Créez le client de la table.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

        // Créez l'objet CloudTable qui représente la table "WADPerformanceCountersTable".
        CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

        // Créez la requête de table, filtrez sur un CounterName, un DeploymentId et un RoleInstance spécifiques.
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

        // Exécutez la requête de table.
        IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

        // Traitez les résultats de la requête et générez un fichier CSV.
        StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");
            
        foreach (PerformanceCountersEntity entity in result)
        {
            sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + "," 
                + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
        }

        StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
        sw.Write(sb.ToString());
        sw.Close();

Les entités mappent vers les objets C\# en utilisant une classe personnalisée dérivée d’une **TableEntity**. Le code suivant permet de définir une classe d'entité qui représente un compteur de performances dans la table **WADPerformanceCountersTable**.

     	public class PerformanceCountersEntity : TableEntity
        {
            public long EventTickCount { get; set; }
            public string DeploymentId { get; set; }
            public string Role { get; set; }
            public string RoleInstance { get; set; }
            public string CounterName { get; set; }
            public double CounterValue { get; set; }                
        }

Étapes suivantes
----------------

Maintenant que vous avez appris les principes de base de la collecte de compteurs de performances, suivez ces liens pour savoir comment implémenter des scénarios de dépannage plus complexes.

-   [Meilleures pratiques de dépannage pour développer des applications Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh771389.aspx)
-   [Surveillance des services cloud](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/)
-   [Utilisation du bloc applicatif de mise à l'échelle automatique](http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/autoscaling/)
-   [Création d'applications élastiques et résilientes](http://msdn.microsoft.com/fr-fr/library/hh680949(PandP.50).aspx)

Ressources supplémentaires
--------------------------

-   [Activation des diagnostics dans Azure](https://www.windowsazure.com/en-us/develop/net/common-tasks/diagnostics/)
-   [Collecte des données de journalisation avec les diagnostics Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433048.aspx)
-   [Débogage d'une application Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405479.aspx)


