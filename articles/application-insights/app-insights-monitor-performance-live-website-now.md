---
title: "Surveiller une application web ASP.NET active avec Azure Application Insights | Microsoft Docs"
description: "Analysez les performances d&quot;un site web sans le redéployer. Fonctionne avec les applications web ASP.NET hébergées localement, dans des machines virtuelles ou sur Azure."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 917f54248f4c9277caa3cf09d92f78593a901e89
ms.openlocfilehash: fd76f40f5a34b6adf9c6ec3bded604d59b6baa72


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumenter des applications web lors de l’exécution avec Application Insights


Vous pouvez instrumenter une application web dynamique avec Azure Application Insights, sans avoir à modifier ou à redéployer votre code. Si vos applications sont hébergées par un serveur IIS local, vous pouvez installer Status Monitor ; s’il s’agit d’applications web Azure ou d’applications exécutées sur une machine virtuelle Azure, vous pouvez installer l’extension Application Insights. (Des articles distincts sont également consacrés à l’instrumentation des [applications web J2EE actives](app-insights-java-live.md) et [d’Azure Cloud Services](app-insights-cloudservices.md).) Cette opération nécessite un abonnement [Microsoft Azure](http://azure.com) .

![Exemples de graphiques](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Vous avez le choix entre trois façons d’appliquer Application Insights à vos applications web .NET :

* **En cours de création :** [ajoutez le Kit de développement logiciel (SDK) Application Insights][greenbrown] au code de votre application web.
* **En cours d’exécution :** instrumentez votre application web sur le serveur, comme décrit ci-dessous, sans régénérer ni redéployer le code.
* **Les deux :** intégrez le Kit de développement logiciel (SDK) à votre code d’application web et appliquez également les extensions à l’exécution. Profitez des avantages des deux options.

Voici un résumé de ce que vous apporte chaque méthode :

|  | En cours de création | En cours d’exécution |
| --- | --- | --- |
| Requêtes et exceptions |Oui |Oui |
| [Exceptions plus détaillées](app-insights-asp-net-exceptions.md) | |Oui |
| [Diagnostics de dépendance](app-insights-asp-net-dependencies.md) |Sur .NET 4.6 +, mais moins détaillé |Oui, tous les détails : codes de résultat, texte de commande SQL, verbe HTTP|
| [Compteurs de performances système](app-insights-performance-counters.md) |Oui |Oui |
| [API pour la télémétrie personnalisée][api] |Oui | |
| [Intégration des journaux de suivi](app-insights-asp-net-trace-logs.md) |Oui | |
| [Mode Page et données utilisateur](app-insights-javascript.md) |Oui | |
| Régénération du code inutile |Non | |


## <a name="monitor-a-live-azure-web-app"></a>Surveiller une application web Azure active

Si votre application s’exécute en tant que service web Azure, voici comment activer la surveillance :

* Sélectionnez Application Insights sur le panneau de configuration de l’application dans Azure.

    ![Configurer Application Insights pour une application web Azure](./media/app-insights-monitor-performance-live-website-now/azure-web-setup.png)
* Lorsque la page de résumé d’Application Insights s’ouvre, cliquez sur le lien en bas pour ouvrir la ressource Application Insights complète.

    ![Cliquer pour accéder à Application Insights](./media/app-insights-monitor-performance-live-website-now/azure-web-view-more.png)

[Surveillance des applications de machine virtuelle et cloud](app-insights-azure.md).

## <a name="monitor-a-live-iis-web-app"></a>Surveiller une application web IIS active

Si votre application est hébergée sur un serveur IIS, activez Application Insights à l’aide de Status Monitor.

1. Sur votre serveur web IIS, connectez-vous avec vos informations d’identification d’administrateur.
2. Si Application Insights Status Monitor n’est pas encore installé, téléchargez et exécutez le [programme d’installation Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).
3. Dans Status Monitor, sélectionnez l’application web installée ou le site Web à surveiller. Connectez-vous avec vos informations d’identification Azure.

    Configurez la ressource où vous souhaitez afficher les résultats dans le portail Application Insights. (En règle générale, il est préférable de créer une ressource. Sélectionnez une ressource existante si vous avez déjà configuré des [tests web][availability] ou une [Surveillance du client][client] pour cette application.) 

    ![Choisissez une application et une ressource.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

4. Redémarrez IIS.

    ![Cliquez sur Redémarrer en haut de la boîte de dialogue.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    Votre service web sera interrompu pendant une courte période.

## <a name="customize-monitoring-options"></a>Personnaliser les options de surveillance

L’activation d’Application Insights ajoute des DLL et ApplicationInsights.config à votre application web. Vous pouvez [modifier le fichier .config](app-insights-configuration-with-applicationinsights-config.md) pour changer certaines options.

## <a name="when-you-re-publish-your-app-re-enable-application-insights"></a>Lorsque vous republiez votre application, réactivez Application Insights

Avant de republier votre application, vous pouvez [ajouter Application Insights au code dans Visual Studio][greenbrown]. Vous obtiendrez des données de télémétrie plus détaillées et aurez la possibilité de personnaliser la télémétrie à l’aide de code.

Si vous souhaitez procéder à la republication sans ajouter Application Insights au code, sachez que le processus de déploiement peut supprimer les DLL et ApplicationInsights.config du site web publié. Par conséquent :

1. Si vous avez modifié le fichier ApplicationInsights.config, copiez-le avant de republier votre application.
2. Republiez votre application.
3. Réactivez la surveillance d’Application Insights. (Utilisez la méthode appropriée : le panneau de configuration de l’application web Azure ou Status Monitor sur un hôte IIS.)
4. Rétablissez les éventuelles modifications que vous avez effectuées dans le fichier .config.


## <a name="troubleshooting-runtime-configuration-of-application-insights"></a>Résolution des problèmes de configuration d’exécution d’Application Insights

### <a name="cant-connect-no-telemetry"></a>Vous n’arrivez pas à vous connecter ? Vous n’obtenez aucune donnée de télémétrie ?

* Vous devez ouvrir [certains ports sortants](app-insights-ip-addresses.md#outgoing-ports) dans le pare-feu de votre serveur pour permettre à Status Monitor de fonctionner.

* Ouvrez Status Monitor et sélectionnez votre application dans le volet gauche. Vérifiez la présence de messages de diagnostic pour cette application dans la section « Notifications de configuration » :

  ![Ouvrez le panneau des performances pour afficher la demande, le temps de réponse, la dépendance et d’autres données.](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Si un message relatif à des « autorisations insuffisantes » s’affiche sur le serveur, procédez comme suit :
  * Dans le Gestionnaire des services Internet, sélectionnez votre pool d’applications, ouvrez **Paramètres avancés** puis, sous **Modèle de processus**, notez l’identité.
  * Dans le panneau de configuration relatif à la gestion de l’ordinateur, ajoutez cette identité au groupe Utilisateurs de l’Analyseur de performances.
* Si les services MMA/SCOM sont installés sur votre serveur, certaines versions peuvent entrer en conflit. Désinstallez à la fois SCOM et Moniteur d’état, puis réinstallez des versions les plus récentes.
* Consultez la rubrique [Résolution des problèmes][qna].

## <a name="system-requirements"></a>Configuration requise
Prise en charge du système d’exploitation pour Application Insights Status Monitor sur le serveur :

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

avec le dernier Service Pack et .NET Framework 4.5

Windows 7, 8, 8.1 et 10 côté client, avec également .NET Framework 4.5

Prise en charge d’IIS : IIS 7, 7.5, 8, 8.5 (IIS requis)

## <a name="automation-with-powershell"></a>Automation avec PowerShell
Vous pouvez démarrer et arrêter la surveillance à l’aide de PowerShell sur votre serveur IIS.

Tout d’abord, importez le module Application Insights :

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Identifiez les applications qui sont surveillées :

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Facultatif) Nom d’une application web.
* Affiche l’état de la surveillance Application Insights pour chaque application web (ou l’application nommée) dans ce serveur IIS.
* Retourne `ApplicationInsightsApplication` pour chaque application :

  * `SdkState==EnabledAfterDeployment` : l’application est surveillée et a été instrumentée lors de l’exécution par l’outil Status Monitor ou par `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: l’application n’est pas instrumentée pour Application Insights. Soit elle n’a jamais été instrumentée, soit la surveillance lors de l’exécution a été désactivée avec l’outil Status Monitor ou avec `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: l’application a été instrumentée en ajoutant le Kit de développement logiciel (SDK) au code source. Son Kit de développement logiciel (SDK) ne peut pas être mis à jour ou arrêté.
  * `SdkVersion` affiche la version utilisée pour surveiller cette application.
  * `LatestAvailableSdkVersion`affiche la version actuellement disponible dans la galerie NuGet. Pour mettre à niveau l’application vers cette version, utilisez `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` Nom de l’application dans IIS
* `-InstrumentationKey` Ikey de la ressource Application Insights où vous souhaitez afficher les résultats.
* Cette applet de commande affecte uniquement les applications qui ne sont pas déjà instrumentées, c’est-à-dire SdkState==NotInstrumented.

    L’applet de commande n’affecte pas une application qui est déjà instrumentée, que ce soit au moment de la création en ajoutant le Kit de développement logiciel (SDK) au code ou au moment de l’exécution via une utilisation préalable de cette applet de commande.

    La version du Kit de développement logiciel (SDK) utilisée pour instrumenter l’application est la version la plus récente téléchargée sur ce serveur.

    Pour télécharger la version la plus récente, utilisez Update-ApplicationInsightsVersion.
* Retourne `ApplicationInsightsApplication` en cas de réussite. En cas d’échec, il consigne un suivi sur stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` Nom d’une application dans IIS
* `-All` Arrête la surveillance de toutes les applications de ce serveur IIS pour lequel `SdkState==EnabledAfterDeployment`
* Arrête la surveillance des applications spécifiées et supprime l’instrumentation. Cela ne fonctionne que pour les applications qui ont été instrumentées lors de l’exécution avec l’outil Status Monitor ou avec Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Renvoie ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name` : nom d’une application web dans IIS.
* `-InstrumentationKey` (Facultatif.) Permet de modifier la ressource à laquelle la télémétrie de l’application est envoyée.
* Cette applet de commande :
  * Met à niveau l’application nommée vers la version la plus récente du Kit de développement logiciel (SDK) téléchargée sur cette machine. (Ne fonctionne que si `SdkState==EnabledAfterDeployment`)
  * Si vous fournissez une clé d’instrumentation, l’application nommée est reconfigurée pour envoyer la télémétrie à la ressource avec cette clé. (Fonctionne si `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Télécharge la version la plus récente du Kit de développement logiciel (SDK) Application Insights sur le serveur.

## <a name="a-namenextanext-steps"></a><a name="next"></a>Étapes suivantes

Affichez vos données de télémétrie :

* [Explorez les mesures](app-insights-metrics-explorer.md) pour surveiller les performances et l’utilisation
* [Effectuez des recherches dans les événements et les journaux][diagnostic] pour diagnostiquer les problèmes
* [Utilisez la fonctionnalité Analytics](app-insights-analytics.md) pour des requêtes plus élaborées
* [Créez des tableaux de bord](app-insights-dashboards.md)

Ajoutez des données de télémétrie :

* [Créez des tests web][availability] pour vous assurer que votre site reste actif.
* [Ajoutez la télémétrie de client web][usage] pour afficher les exceptions à partir du code de la page web et vous permettre d’insérer un suivi des appels.
* [Ajoutez le Kit de développement logiciel (SDK) Application Insights à votre code][greenbrown] afin de pouvoir insérer un suivi et des appels de journaux

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=Feb17_HO2-->


