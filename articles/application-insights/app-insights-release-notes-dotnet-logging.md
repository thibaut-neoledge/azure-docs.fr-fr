<properties 
	pageTitle="Notes de publication pour les adaptateurs de journalisation Application Insights" 
	description="Les dernières mises à jour." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/21/2015" 
	ms.author="abaranch"/>
 
# Notes de publication pour les adaptateurs de journalisation .NET

Si vous utilisez déjà un framework de journalisation comme log4net, NLog ou System.Diagnostics.Trace, vous pouvez capturer ces journaux et les envoyer à [Application Insights](http://azure.microsoft.com/services/application-insights/), où vous pouvez associer des suivis du journal à des actions de l'utilisateur, des exceptions et autres événements.


#### Pour commencer

Voir [Journaux, exceptions et diagnostics personnalisés pour ASP.NET dans Application Insights](app-insights-search-diagnostic-logs.md).

## Version 1.2.6

- Résolution des bogues
- log4Net : collecter les propriétés log4net en tant que propriétés personnalisées. UserName n'est plus une propriété personnalisée (collectée comme telemetry.Context.User.Id). Timestamp n'est plus une propriété personnalisée.
- NLog : collecter les propriétés NLog en tant que propriétés personnalisées. SequenceID n’est plus une propriété personnalisée (collectée comme telemetry.Sequence). Timestamp n'est plus une propriété personnalisée. 

## Version 1.2.5
- Ouvrez d’abord la version source : référence Application Insights API version 1.2.3 ou ultérieure.

<!---HONumber=AcomDC_0107_2016-->