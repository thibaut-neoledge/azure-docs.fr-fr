<properties 
	pageTitle="Notes de publication pour Application Insights" 
	description="Les dernières mises à jour." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="awills"/>
 
# Notes de publication pour le Kit de développement logiciel (SDK) Application Insights pour Java

[Utilisation du Kit de développement logiciel (SDK) Azure pour Java](app-insights-java-get-started.md)

## Version 0.9.4

- Prise en charge de la collecte des compteurs de performances à partir d’ordinateurs Windows 32 bits.
- Prise en charge du suivi manuel des dépendances à l’aide d’une nouvelle API de méthode ```trackDependency```.
- Possibilité de baliser un élément de télémétrie comme synthétique en ajoutant une propriété ```SyntheticSource``` à l’élément de rapport.

<!---HONumber=58-->