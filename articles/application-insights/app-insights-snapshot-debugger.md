---
title: "Débogueur de captures instantanées Azure Application Insights pour les applications .NET | Microsoft Docs"
description: "Des captures instantanées de débogage sont collectées automatiquement lorsque des exceptions sont levées dans des applications .NET de production"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: dcc5cc0be4c03ad661cf1539cb98a7d4fc94e778
ms.contentlocale: fr-fr
ms.lasthandoff: 07/19/2017

---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Captures instantanées de débogage sur exceptions levées dans des applications .NET

Quand une exception se produit, vous pouvez collecter automatiquement une capture instantanée de débogage à partir de votre application web dynamique. La capture instantanée indique l’état du code source et des variables au moment où l’exception a été levée. Le Débogueur de capture instantanée (préversion) dans [Azure Application Insights](app-insights-overview.md) analyse la télémétrie des exceptions à partir de votre application web. Il collecte des captures instantanées sur les principales exceptions levées afin que vous disposiez des informations dont vous avez besoin pour diagnostiquer des problèmes de production. Incluez le [package NuGet de collecte des captures instantanées](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application, et configurez éventuellement les paramètres de collecte dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Les captures instantanées apparaissent sur les [exceptions](app-insights-asp-net-exceptions.md) dans le portail Application Insights.

Vous pouvez afficher les captures instantanées de débogage dans le portail pour consulter la pile des appels et inspecter les variables à chaque frame de pile des appels. Pour améliorer la puissance de débogage du code source, ouvrez les captures instantanées à l’aide de Visual Studio 2017 Enterprise en [téléchargeant l’extension du Débogueur de capture instantanée pour Visual Studio](https://aka.ms/snapshotdebugger).

La collecte de captures instantanées est disponible pour :
* les applications .NET framework et ASP.NET exécutant .NET Framework 4.5 ou version ultérieure ;
* les applications .NET core 2.0 et ASP.NET Core 2.0 s’exécutant sous Windows.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web](app-insights-asp-net.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application. 

3. Examinez les options par défaut que le package a ajoutées à [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in one minute. -->
        <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Dans certains cas (par exemple, des versions plus anciennes de la plateforme .NET), il se peut que vous deviez [configurer la collecte des exceptions](app-insights-asp-net-exceptions.md#exceptions) afin de voir celles-ci avec des captures instantanées dans le portail.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET Core 2.0

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web ASP.NET Core](app-insights-asp-net-core.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Modifiez la méthode `ConfigureServices` dans la classe `Startup` votre application pour ajouter le processeur de télémétrie du collecteur de captures instantanées. Le code que vous devez ajouter dépend de la version référencée du package NuGet de Microsoft.ApplicationInsights.ASPNETCore.

   Pour Microsoft.ApplicationInsights.AspNetCore 2.1.0, ajoutez :
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddSingleton<Func<ITelemetryProcessor, ITelemetryProcessor>>(next => new SnapshotCollectorTelemetryProcessor(next));
           // TODO: Add any other services your application needs here.
       }
   }
   ```

   Pour Microsoft.ApplicationInsights.AspNetCore 2.1.1, ajoutez :
   ```C#
   using Microsoft.ApplicationInsights.SnapshotCollector;
   ...
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           public ITelemetryProcessor Create(ITelemetryProcessor next) =>
               new SnapshotCollectorTelemetryProcessor(next);
       }

       // This method is called by the runtime. Use it to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
            services.AddSingleton<ITelemetryProcessorFactory>(new SnapshotCollectorTelemetryProcessorFactory());
           // TODO: Add any other services your application needs here.
       }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurer la collecte de captures instantanées pour d’autres applications .NET

1. Si votre application n’est pas instrumentée avec Application Insights, commencez par [activer Application Insights et définir la clé d’instrumentation](app-insights-windows-desktop.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Il se peut que vous deviez modifier votre code pour les signaler. Le code de gestion des exceptions dépend de la structure de votre application, mais en voici un exemple :
   ```C#
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }

## Grant permissions

Owners of the Azure subscription can inspect snapshots. Other users must be granted permission by an owner.

To grant permission, assign the `Application Insights Snapshot Debugger` role to users who will inspect snapshots. This role can be assigned to individual users or groups by subscription owners for the target Application Insights resource or its resource group or subscription.

1. Open the Access Control (IAM) blade.
1. Click the +Add button.
1. Select Application Insights Snapshot Debugger from the Roles drop-down list.
1. Search for and enter a name for the user to add.
1. Click the Save button to add the user to the role.


[!IMPORTANT]
    Snapshots can potentially contain personal and other sensitive information in variable and parameter values.

## Debug snapshots in the Application Insights portal

If a snapshot is available for a given exception or a problem ID, an **Open Debug Snapshot** button appears on the [exception](app-insights-asp-net-exceptions.md) in the Application Insights portal.

![Open Debug Snapshot button on exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

In the Debug Snapshot view, you see a call stack and a variables pane. When you select frames of the call stack in the call stack pane, you can view local variables and parameters for that function call in the variables pane.

![View Debug Snapshot in the portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Snapshots might contain sensitive information, and by default they are not viewable. To view snapshots, you must have the `Application Insights Snapshot Debugger` role assigned to you.

## Debug snapshots with Visual Studio 2017 Enterprise
1. Click the **Download Snapshot** button to download a `.diagsession` file, which can be opened by Visual Studio 2017 Enterprise. 

2. To open the `.diagsession` file, you must first [download and install the Snapshot Debugger extension for Visual Studio](https://aka.ms/snapshotdebugger).

3. After you open the snapshot file, the Minidump Debugging page in Visual Studio appears. Click **Debug Managed Code** to start debugging the snapshot. The snapshot opens to the line of code where the exception was thrown so that you can debug the current state of the process.

    ![View debug snapshot in Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

The downloaded snapshot contains any symbol files that were found on your web application server. These symbol files are required to associate snapshot data with source code. For App Service apps, make sure to enable symbol deployment when you publish your web apps.

## How snapshots work

When your application starts, a separate snapshot uploader process is created that monitors your application for snapshot requests. When a snapshot is requested, a shadow copy of the running process is made in about 10 to 20 minutes. The shadow process is then analyzed, and a snapshot is created while the main process continues to run and serve traffic to users. The snapshot is then uploaded to Application Insights along with any relevant symbol (.pdb) files that are needed to view the snapshot.

## Current limitations

### Publish symbols
The Snapshot Debugger requires symbol files on the production server to decode variables and to provide a debugging experience in Visual Studio. The 15.2 release of Visual Studio 2017 publishes symbols for release builds by default when it publishes to App Service. In prior versions, you need to add the following line to your publish profile `.pubxml` file so that symbols are published in release mode:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pour Calcul Azure et d’autres types, vérifiez que les fichiers de symboles se trouvent dans le même dossier que le fichier .dll de l’application principale (généralement `wwwroot/bin`), ou sont disponibles dans le chemin d’accès actuel.

### <a name="optimized-builds"></a>Optimisation des versions
Dans certains cas, des variables locales ne sont pas visibles dans les builds de mise en production en raison d’optimisations appliquées pendant le processus de génération.

## <a name="troubleshooting"></a>Résolution des problèmes

Les conseils suivants peuvent vous aider à résoudre des problèmes rencontrés avec le Débogueur de capture instantanée.

### <a name="verify-the-instrumentation-key"></a>Vérifier la clé d’instrumentation

Assurez-vous que vous utilisez la clé d’instrumentation correcte dans votre application publiée. En règle générale, Application Insights lit la clé d’instrumentation à partir du fichier ApplicationInsights.config. Vérifiez que la valeur est identique à la clé d’instrumentation de la ressource Application Insights que vous voyez dans le portail.

### <a name="check-the-uploader-logs"></a>Vérifier les journaux du chargeur

Une fois une capture instantanée créée, un fichier minidump (.dmp) est créé sur le disque. Un processus distinct du chargeur prend ce fichier minidump et le charge, ainsi que tous les fichiers PDB associés, vers le stockage du Débogueur de capture instantanée d’Application Insights. Une fois le fichier minidump correctement chargé, il est supprimé du disque. Les fichiers journaux du chargeur de fichier minidump sont conservés sur le disque. Dans un environnement App Service, vous pouvez trouver ces fichiers journaux dans `D:\Home\LogFiles\Uploader_*.log`. Le site de gestion Kudu pour App Service permet de rechercher ces fichiers journaux.

1. Ouvrez votre application App Service dans le portail Azure.

2. Sélectionnez le panneau **Outils avancés**, ou recherchez **Kudu**.
3. Cliquez sur **Atteindre**.
4. Dans le zone de liste déroulante de la **Console de débogage**, sélectionnez **CMD**.
5. Cliquez sur **LogFiles**.

Vous devriez voir au moins un fichier dont le nom commence par `Uploader_` et dont l’extension est `.log`. Cliquez sur l’icône appropriée pour télécharger tous les fichiers journaux ou les ouvrir dans un navigateur.
Le nom de fichier inclut le nom de ma machine. Si votre instance App Service est hébergée sur plusieurs machines, il existe des fichiers journaux distincts pour chacune d’elles. Lorsque le chargeur détecte la présence d’un nouveau fichier minidump, celui-ci est enregistré dans le fichier journal. Voici un exemple chargement réussi :

```
MinidumpUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:08.0349846Z
MinidumpUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 329.12 MB
    DateTime=2017-05-25T14:25:16.0145444Z
MinidumpUploader.exe Information: 0 : Upload successful.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2017-05-25T14:25:42.9164120Z
MinidumpUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2017-05-25T14:25:44.2310982Z
MinidumpUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2017-05-25T14:25:44.5435948Z
MinidumpUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2017-05-25T14:25:44.6095821Z
```

Dans l’exemple précédent, la clé d’instrumentation est `c12a605e73c44346a984e00000000000`. Cette valeur doit correspondre à la clé d’instrumentation de votre application.
Le fichier minidump est associé à une capture instantanée dont l’ID est `139e411a23934dc0b9ea08a626db16c5`. Vous pouvez utiliser cet ID ultérieurement pour rechercher la télémétrie d’exception associée dans Application Insights - Analyses.

Le chargeur analyse les nouveaux fichiers PDB environ toutes les 15 minutes. Voici un exemple :

```
MinidumpUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\home\site\wwwroot\ for local PDBs.
    DateTime=2017-05-25T15:11:38.8003886Z
MinidumpUploader.exe Information: 0 : Scanning D:\local\Temporary ASP.NET Files\root\a6554c94\e3ad6f22\assembly\dl3\81d5008b\00b93cc8_dec5d201 for local PDBs.
    DateTime=2017-05-25T15:11:38.8160276Z
MinidumpUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2017-05-25T15:11:38.8316450Z
MinidumpUploader.exe Information: 0 : Deleted PDB scan marker D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\.pdbscan.
    DateTime=2017-05-25T15:11:38.8316450Z
```

Pour les applications qui ne sont _pas_ hébergées dans App Service, les fichiers journaux du chargeur figurent dans le même dossier que les fichiers minidump : `%TEMP%\Dumps\<ikey>` (où `<ikey>` est votre clé d’instrumentation).

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Utilisez une recherche Application Insights pour trouver des exceptions avec des captures instantanées

Quand un instantané est créé, l’exception levée est marquée avec un ID d’instantané. Lorsque la télémétrie d’exception est signalée à Application Insights, cet ID d’instantané est inclus en tant que propriété personnalisée. Le panneau Rechercher dans Application Insights permet de trouver toute télémétrie avec la propriété personnalisée Search `ai.snapshot.id`.

1. Parcourez votre ressource Application Insights dans le portail Azure.
2. Cliquez sur **Rechercher**.
3. Tapez `ai.snapshot.id` dans la zone de texte Rechercher, puis appuyez sur Entrée.

![Rechercher une télémétrie avec un ID d’instantané dans le portail](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Si cette recherche ne retourne aucun résultat, cela signifie qu’aucun instantané n’a été signalé à Application Insights pour votre application dans l’intervalle de temps sélectionné.

Pour rechercher un ID d’instantané spécifique dans les fichiers journaux du chargeur, tapez cet ID dans la zone de recherche. Si vous ne trouvez la télémétrie d’une capture instantanée dont vous savez qu’elle a été chargée, procédez comme suit :

1. Vérifiez soigneusement que vous examinez la ressource Application Insights appropriée en vérifiant la clé d’instrumentation.

2. À l’aide de l’horodateur du fichier journal du chargeur, ajustez le filtre Intervalle de temps de la recherche pour couvrir cet intervalle.

Si vous ne voyez toujours pas d’exception avec cet ID d’instantané, cela signifie que la télémétrie de l’exception n’a pas été signalée à Application Insights. Cette situation peut se produire si votre application s’est arrêtée anormalement après avoir pris la capture instantanée, mais avant d’avoir signalé la télémétrie de l’exception. Dans ce cas, vérifiez les journaux d’App Service sous `Diagnose and solve problems` pour voir si des redémarrages intempestifs ou des exceptions non prises en charge se sont produits.

## <a name="next-steps"></a>Étapes suivantes

* [Définir des points de capture instantanée dans votre code](https://azure.microsoft.com/blog/snapshot-debugger-for-azure/) afin obtenir des captures instantanées sans attendre la levée d’une exception.
* [Diagnostiquer des exceptions dans vos applications web](app-insights-asp-net-exceptions.md) explique comment rendre visible à Application Insights un plus grand nombre d’exceptions. 
* [Détection intelligente](app-insights-proactive-diagnostics.md) permet de détecter automatiquement les anomalies relatives aux performances.

