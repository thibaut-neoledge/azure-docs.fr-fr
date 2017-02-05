---
title: "Dépannage et questions : Application Insights"
description: "Un aspect d’Azure Application Insights vous semble abscons ou ne fonctionne pas correctement ? Essayez ici."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 0d860856b4c871c2c8e3622597ba3753bf62cf4f


---
# <a name="questions---application-insights-for-aspnet"></a>Questions : Application Insights pour ASP.NET
## <a name="configuration-problems"></a>Problèmes de configuration
*J’ai des difficultés à configurer :*

* [Application .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Analyse d’une application déjà en cours d’exécution](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnostics Azure](app-insights-azure-diagnostics.md)
* [Applications web Java](app-insights-java-troubleshoot.md)
* [autres plateformes](app-insights-platforms.md)

*Je ne reçois aucune donnée de mon serveur*

* [Définir les exceptions de pare-feu](app-insights-ip-addresses.md)
* [Configurer un serveur ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurer un serveur Java](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Puis-je utiliser Application Insights avec... ?
[Voir Plateformes][platforms]

## <a name="is-it-free"></a>Est-ce gratuit ?
* Oui, si vous choisissez le [niveau de tarification](app-insights-pricing.md)gratuit. Vous obtenez la plupart des fonctionnalités et un quota important de données.
* Vous devez fournir les données de votre carte de crédit pour vous enregistrer auprès de Microsoft Azure, mais aucun service ne vous sera facturé, sauf si vous utilisez un autre service Azure payant, ou si vous mettez explicitement à niveau vers un niveau payant.
* Si votre application envoie plus de données que le quota mensuel autorisé pour le niveau gratuit, les données ne sont plus consignées. Si cela se produit, vous pouvez choisir de commencer à payer ou d'attendre jusqu'à ce que le quota soit réinitialisé à la fin du mois.
* Les données de session et l'utilisation de base ne sont pas soumises à un quota.
* Il existe aussi une version d’évaluation gratuite qui vous permet d’obtenir les fonctionnalités payantes gratuitement pendant 30 jours.
* Chaque ressource d'application possède un quota distinct, et vous définissez son niveau de tarification indépendamment des autres.

#### <a name="what-do-i-get-if-i-pay"></a>Que vais-je obtenir si je paye ?
* Un [quota de données mensuel](https://azure.microsoft.com/pricing/details/application-insights/)plus important.
* L'option consistant à payer le « dépassement » pour poursuivre la collecte des données une fois le quota mensuel dépassé. Si vos données dépassent le quota, vous êtes facturé par Mo.
* [Exportation continue](app-insights-export-telemetry.md).

## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Que modifie Application Insights dans mon projet ?
Cela dépend du type de projet. Pour une application web :

* Ajoute ces fichiers à votre projet :
  
  * ApplicationInsights.config.
  * ai.js
* Installe ces packages NuGet :
  
  * *Application Insights API* - API de base
  * *Application Insights API for Web Applications* - permet d'envoyer des données de télémétrie depuis le serveur
  * *Application Insights API for JavaScript Applications* - permet d'envoyer des données de télémétrie depuis le client
    
    Ces packages comprennent les assemblys suivants :
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Insère des éléments dans :
  
  * Web.config
  * packages.config
* (Nouveaux projets uniquement : si vous [ajoutez Application Insights à un projet existant][start], vous devez le faire manuellement.) Insère des extraits de code dans le code du client et du serveur, afin de les initialiser avec l'ID de la ressource Application Insights. Par exemple, dans une application MVC, le code est inséré dans la page maître Views/Shared/_Layout.cshtml.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Comment mettre à niveau à partir d'anciennes versions du Kit de développement logiciel (SDK) ?
Consultez les [notes de publication](app-insights-release-notes.md) du Kit de développement logiciel (SDK) adapté à votre type d'application.

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Comment puis-je changer la ressource Azure à laquelle mon projet envoie des données ?
Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config` , puis sélectionnez **Mettre à jour Application Insights**. Vous pouvez envoyer les données à une ressource existante ou à une nouvelle ressource dans Azure. L'Assistant Mise à jour modifie la clé d'instrumentation dans ApplicationInsights.config, qui détermine où le Kit de développement logiciel (SDK) du serveur envoie vos données. Cela modifie également la clé où de l'affichage dans vos pages web, sauf si vous désactivez l'option « Tout mettre à jour ».

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Combien de temps les données sont-elles conservées dans le portail ? Sont-elles sécurisées ?
Voir [Rétention de données et confidentialité][données].

## <a name="logging"></a>Journalisation
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Comment consulter les données POST dans la fonction Recherche de diagnostic ?
Les données POST ne sont pas automatiquement consignées, mais vous pouvez utiliser un appel TrackTrace ; placez les données dans le paramètre message. Ce dernier présente une limite de taille plus longue que les limites relatives aux propriétés de type chaîne, bien que vous ne puissiez pas lui appliquer de filtres.

## <a name="security"></a>Sécurité
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Mes données sont-elles sécurisées sur le portail ? Pendant combien de temps sont-elles conservées ?
Consultez [Rétention de données et confidentialité][données].

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Comment savoir si j'ai activé tout ce qu'il faut pour utiliser Application Insights ?
| Ce qui suit doit s'afficher | Comment y accéder | Utilité |
| --- | --- | --- |
| Graphiques de disponibilité |[Tests web](app-insights-monitor-web-app-availability.md) |Savoir si votre application web est active |
| Performances des applications de serveur (temps de réponse, etc.) |[Ajout d’Application Insights à votre projet](app-insights-asp-net.md) ou [Installation d’AI Status Monitor sur le serveur](app-insights-monitor-performance-live-website-now.md) (ou écrivez votre propre code pour [suivre des dépendances](app-insights-api-custom-events-metrics.md#track-dependency)) |Détecter les problèmes de performances |
| Télémétrie des dépendances |[Installation d’AI Status Monitor sur le serveur](app-insights-monitor-performance-live-website-now.md) |Diagnostiquer les problèmes relatifs à des bases de données ou à d'autres composants externes |
| Obtention de l'arborescence des appels de procédure à partir des exceptions |[Insertion d’appels TrackException dans votre code](app-insights-search-diagnostic-logs.md#exceptions) (certains d’entre eux sont cependant signalés automatiquement) |Détecter et diagnostiquer les exceptions |
| Recherche des données de suivi des journaux |[Ajout d’un enregistreur de données](app-insights-search-diagnostic-logs.md) |Diagnostiquer les exceptions et problèmes de performances |
| Principes fondamentaux d'utilisation des clients (vues de page, sessions, etc.) |[Initialiseur JavaScript dans les pages web](app-insights-javascript.md) |Analyse de l'utilisation |
| Mesures personnalisées des clients |[Appels de suivi dans les pages web](app-insights-api-custom-events-metrics.md) |Améliorer l'expérience utilisateur |
| Mesures personnalisées des serveurs |[Appels de suivi dans le serveur](app-insights-api-custom-events-metrics.md) |Décisionnel |

## <a name="automation"></a>Automatisation
Vous pouvez [écrire des scripts PowerShell](app-insights-powershell.md) pour créer et mettre à jour des ressources Application Insights.

## <a name="more-answers"></a>Réponses supplémentaires
* [Forum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[données]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Nov16_HO3-->


