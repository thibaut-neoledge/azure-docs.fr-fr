<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Utilisation de compteurs de performances dans Azure

Vous pouvez utiliser des compteurs de performances dans une
application Azure pour collecter des données qui peuvent vous aider à
repérer les goulots d'étranglement au niveau du système et à ajuster les performances du système et de l'application. Les compteurs de performances accessibles avec Windows Server 2008, Windows Server 2012, IIS et ASP.NET peuvent être collectés et utilisés pour déterminer l'intégrité de votre application Azure.

Vous pouvez configurer les compteurs de performances avant le déploiement ou au moment de l'exécution dans Visual Studio 2012 ou Visual Studio 2013 avec le Kit de développement logiciel (SDK) 2.0 ou une version ultérieure. Pour plus d'informations, consultez la page [Configuration des diagnostics Azure][Configuration des diagnostics Azure]. Pour plus d'informations sur la configuration manuelle des compteurs de performance dans votre application, consultez [Procédure : configurer les compteurs de performance][Procédure : configurer les compteurs de performance].

Pour d'autres instructions détaillées sur la création d'une stratégie de journalisation et de suivi, et sur l'utilisation des diagnostics et des autres techniques pour résoudre les problèmes, consultez la page [Meilleures pratiques de dépannage pour développer des applications Azure][Meilleures pratiques de dépannage pour développer des applications Azure].

## <a name="nextsteps"> </a>Étapes suivantes

Suivez ces liens pour savoir comment implémenter des scénarios de dépannage plus complexes..

-   [Test des performances d'un service cloud][Test des performances d'un service cloud]
-   [Meilleures pratiques de dépannage pour développer des applications Azure][Meilleures pratiques de dépannage pour développer des applications Azure]
-   [Surveillance des services cloud][Surveillance des services cloud]
-   [Utilisation du bloc applicatif de mise à l'échelle automatique][Utilisation du bloc applicatif de mise à l'échelle automatique]
-   [Création d'applications élastiques et résilientes][Création d'applications élastiques et résilientes]

## <a name="additional"> </a>Ressources supplémentaires

-   [Activation des diagnostics dans Azure][Activation des diagnostics dans Azure]
-   [Collecte des données de journalisation avec les diagnostics Azure][Collecte des données de journalisation avec les diagnostics Azure]
-   [Débogage d'une application Azure][Débogage d'une application Azure]

  [Configuration des diagnostics Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn186185.aspx
  [Procédure : configurer les compteurs de performance]: http://msdn.microsoft.com/fr-fr/library/azure/dn535595.aspx
  [Meilleures pratiques de dépannage pour développer des applications Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh771389.aspx
  [Test des performances d'un service cloud]: http://msdn.microsoft.com/fr-fr/library/azure/hh369930.aspx
  [Surveillance des services cloud]: http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-how-to-monitor/
  [Utilisation du bloc applicatif de mise à l'échelle automatique]: http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [Création d'applications élastiques et résilientes]: http://msdn.microsoft.com/fr-fr/library/hh680949(PandP.50).aspx
  [Activation des diagnostics dans Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-dotnet-diagnostics/
  [Collecte des données de journalisation avec les diagnostics Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433048.aspx
  [Débogage d'une application Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee405479.aspx
