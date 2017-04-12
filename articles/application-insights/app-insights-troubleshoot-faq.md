---
title: "Dépannage et questions : Azure Application Insights | Microsoft Docs"
description: "Un aspect d’Azure Application Insights vous semble abscons ou ne fonctionne pas correctement ? Essayez ici."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 35f57e1046597d30adb65b81b1e73226cb08c05f
ms.lasthandoff: 04/12/2017


---
# <a name="questions---application-insights-for-aspnet"></a>Questions : Application Insights pour ASP.NET
## <a name="configuration-problems"></a>Problèmes de configuration
*J’ai des difficultés à configurer :*

* [Application .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Analyse d’une application déjà en cours d’exécution](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
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

Oui, pour une utilisation expérimentale. Dans le plan de tarification de base, votre application peut envoyer un certain quota de données chaque mois sans frais. Le quota gratuit est suffisamment grand pour couvrir le développement et la publication d’une application pour un petit nombre d’utilisateurs. Vous pouvez définir un plafond pour empêcher le traitement des données au-delà d’un certain seuil.

Vous devez utiliser le plan Entreprise pour bénéficier de certaines fonctionnalités, comme l’exportation continue. Cela entraîne des frais quotidiens.

[Lire le plan de tarification](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="q14"></a>Que modifie Application Insights dans mon projet ?
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
* (Nouveaux projets uniquement - si vous [ajoutez Application Insights à un projet existant][start], vous devez le faire manuellement). Insère des extraits de code dans le code du client et du serveur, afin de les initialiser avec l'ID de la ressource Application Insights. Par exemple, dans une application MVC, le code est inséré dans la page maître Views/Shared/_Layout.cshtml.

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Comment mettre à niveau à partir d'anciennes versions du Kit de développement logiciel (SDK) ?
Consultez les [notes de publication](app-insights-release-notes.md) du Kit de développement logiciel (SDK) adapté à votre type d'application.

## <a name="update"></a>Comment puis-je changer la ressource Azure à laquelle mon projet envoie des données ?
Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config` , puis sélectionnez **Mettre à jour Application Insights**. Vous pouvez envoyer les données à une ressource existante ou à une nouvelle ressource dans Azure. L'Assistant Mise à jour modifie la clé d'instrumentation dans ApplicationInsights.config, qui détermine où le Kit de développement logiciel (SDK) du serveur envoie vos données. Cela modifie également la clé où de l'affichage dans vos pages web, sauf si vous désactivez l'option « Tout mettre à jour ».

#### <a name="data"></a>Combien de temps les données sont-elles conservées dans le portail ? Sont-elles sécurisées ?
Voir [Rétention de données et confidentialité][data].

## <a name="logging"></a>Journalisation
#### <a name="post"></a>Comment consulter les données POST dans la fonction Recherche de diagnostic ?
Les données POST ne sont pas automatiquement consignées, mais vous pouvez utiliser un appel TrackTrace ; placez les données dans le paramètre message. Ce dernier présente une limite de taille plus longue que les limites relatives aux propriétés de type chaîne, bien que vous ne puissiez pas lui appliquer de filtres.

## <a name="security"></a>Sécurité
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Mes données sont-elles sécurisées sur le portail ? Pendant combien de temps sont-elles conservées ?
Consultez [Rétention de données et confidentialité][data].

## <a name="q17"></a> Comment savoir si j'ai activé tout ce qu'il faut pour utiliser Application Insights ?
| Ce qui suit doit s'afficher | Comment y accéder | Utilité |
| --- | --- | --- |
| Graphiques de disponibilité |[Tests web](app-insights-monitor-web-app-availability.md) |Savoir si votre application web est active |
| Performances des applications de serveur (temps de réponse, etc.) |[Ajout d’Application Insights à votre projet](app-insights-asp-net.md) ou [Installation d’AI Status Monitor sur le serveur](app-insights-monitor-performance-live-website-now.md) (ou écrivez votre propre code pour [suivre des dépendances](app-insights-api-custom-events-metrics.md#trackdependency)) |Détecter les problèmes de performances |
| Télémétrie des dépendances |[Installation d’AI Status Monitor sur le serveur](app-insights-monitor-performance-live-website-now.md) |Diagnostiquer les problèmes relatifs à des bases de données ou à d'autres composants externes |
| Obtention de l'arborescence des appels de procédure à partir des exceptions |[Insertion d’appels TrackException dans votre code](app-insights-asp-net-exceptions.md) (certains d’entre eux sont cependant signalés automatiquement) |Détecter et diagnostiquer les exceptions |
| Recherche des données de suivi des journaux |[Ajout d’un enregistreur de données](app-insights-asp-net-trace-logs.md) |Diagnostiquer les exceptions et problèmes de performances |
| Principes fondamentaux d'utilisation des clients (vues de page, sessions, etc.) |[Initialiseur JavaScript dans les pages web](app-insights-javascript.md) |Analyse de l'utilisation |
| Mesures personnalisées des clients |[Appels de suivi dans les pages web](app-insights-api-custom-events-metrics.md) |Améliorer l'expérience utilisateur |
| Mesures personnalisées des serveurs |[Appels de suivi dans le serveur](app-insights-api-custom-events-metrics.md) |Décisionnel |

## <a name="automation"></a>Automatisation
Vous pouvez [écrire des scripts PowerShell](app-insights-powershell.md) pour créer et mettre à jour des ressources Application Insights.

## <a name="more-answers"></a>Réponses supplémentaires
* [Forum Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

