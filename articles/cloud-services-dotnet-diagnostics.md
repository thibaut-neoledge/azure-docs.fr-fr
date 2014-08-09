<properties linkid="dev-net-commons-tasks-diagnostics" urlDisplayName="Diagnostics" pageTitle="How to use diagnostics (.NET) - Azure feature guide" metaKeywords="Azure diagnostics monitoring,logs crash dumps C#" description="Learn how to use diagnostic data in Azure for debugging, measuring performance, monitoring, traffic analysis, and more." metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Enabling Diagnostics in Azure" authors="ryanwi" solutions="" manager="" editor="" />

Activation des diagnostics dans Azure
=====================================

Les diagnostics Azure vous permettent de collecter des données de diagnostic à partir d'un rôle de travail ou d'un rôle Web exécuté dans Azure. Ces données peuvent servir au débogage et à la résolution des problèmes, à mesurer les performances, à suivre l'utilisation des ressources, à analyser le trafic et à prévoir et analyser la capacité.

Vous pouvez configurer les diagnostics avant le déploiement ou au moment de l'exécution dans Visual Studio 2012 ou Visual Studio 2013 avec le Kit de développement logiciel (SDK) 2.0 ou une version ultérieure. Pour plus d'informations, consultez la page [Configuration des diagnostics Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn186185.aspx).

Pour d'autres instructions détaillées sur la création d'une stratégie de journalisation et de suivi, et sur l'utilisation des diagnostics et des autres techniques pour résoudre les problèmes, consultez la page [Meilleures pratiques de dépannage pour développer des applications Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh771389.aspx).

Pour des informations sur la configuration manuelle des diagnostics dans votre application ou sur la modification à distance de la configuration du moniteur de diagnostics, consultez la page [Collecte des données de journalisation avec les diagnostics Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433048.aspx).

Étapes suivantes
----------------

-   [Modification à distance de la configuration du moniteur de diagnostics](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg432992.aspx) - Une fois que vous avez déployé votre application, vous pouvez modifier la configuration des diagnostics.
-   [Surveillance des services cloud](../cloud-services-how-to-monitor/) - Vous pouvez surveiller les principales mesures de performances clés de vos services cloud dans le [portail de gestion Azure](http://manage.windowsazure.com).

Ressources supplémentaires
--------------------------

-   [Collecte des données de journalisation avec les diagnostics Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433048.aspx)
-   [Débogage d'une application Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405479.aspx)
-   [Configuration des diagnostics Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn186185.aspx)


  [Troubleshooting Best Practices for Developing Azure Applications]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh771389.aspx
  

  [Using performance counters in Azure]: ../cloud-services-performance-testing-visual-studio-profiler/
  [How to monitor cloud services]: ../cloud-services-how-to-monitor/
  [DiagnosticMonitorTraceListener Class]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx
  [How to Use the Azure Diagnostics Configuration File]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh411551.aspx
  [Adding Trace Failed Requests in the IIS 7.0 Configuration Reference]: http://www.iis.net/ConfigReference/system.webServer/tracing/traceFailedRequests/add
  [Using performance counters in Azure]: /en-us/develop/net/common-tasks/performance-profiling/
  [How to Configure Local Storage Resources]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758708.aspx
  [Browsing Storage Resources with Server Explorer]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff683677.aspx
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  [Azure Diagnostics Manager]: http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx
  [Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433048.aspx
  [Debugging an Azure Application]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405479.aspx
  [Use the Azure Diagnostics Configuration File]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh411551.aspx
  [LogLevel Enumeration]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.diagnostics.loglevel.aspx
  [OnStart method]: http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
  [Azure Diagnostics Configuration Schema]: http://msdn.microsoft.com/fr-fr/library/gg593185.aspx
  [How to use the Table Storage Service]: http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/table-services/
  [How to use the Azure Blob Storage Service]: http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/blob-storage/
  [Azure Management Portal]: http://manage.windowsazure.com
  [Remotely Change the Diagnostic Monitor Configuration]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg432992.aspx
  [Configuring Azure Diagnostics]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn186185.aspx  