<properties 
	pageTitle="Notes de publication pour Application Insights" 
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
	ms.date="06/18/2015" 
	ms.author="awills"/>
 
# Notes de publication pour le Kit de développement logiciel (SDK) Application Insights pour Java

Le [Kit de développement logiciel (SDK) Application Insights pour Java](app-insights-java-get-started.md) envoie la télémétrie de votre application en direct à [Application Insights](http://azure.microsoft.com/services/application-insights/) afin que vous puissiez en analyser les performances et l’utilisation.

#### Pour installer le Kit de développement logiciel (SDK) dans votre application

Consultez la rubrique [Prise en main du Kit de développement logiciel (SDK) pour Java](app-insights-java-get-started.md).

#### Pour passer à la dernière version du Kit de développement logiciel (SDK) 

Une fois la mise à jour effectuée, vous devrez fusionner toutes les personnalisations qui ont été apportées à ApplicationInsights.xml. Conservez-en une copie pour effectuer une comparaison avec le nouveau fichier.

*Si vous utilisez Maven ou Gradle*

1. Si vous avez spécifié un numéro de version dans pom.xml ou build.gradle, mettez-le à jour.
2. Actualisez les dépendances de votre projet.

*Sinon*

* Téléchargez la dernière version des [bibliothèques Azure pour Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) et remplacez les anciennes. 
 
Comparez les anciennes et nouvelles versions d’ApplicationInsights.xml. La plupart des modifications que vous apercevez sont dues aux ajouts et suppressions de modules. Rétablissez toutes les personnalisations que vous avez apportées.

## Version 0.9.6
- Rendez le Kit de développement logiciel (SDK) Java compatible avec servlet v2.5 et HttpClient pre-v4.3
- Ajout de la prise en charge des intercepteurs Java EE
- Suppression des dépendances redondantes de l'appender Logback

## Version 0.9.5  

- Résolution du problème qui empêchait la corrélation des événements personnalisés avec les utilisateurs/sessions en raison d'erreurs d'analyse de cookie.  
- Amélioration de la logique de résolution de l'emplacement du fichier de configuration ApplicationInsights.xml.
- Suppression du suivi des sessions et des utilisateurs (il sera uniquement effectué par les Kits de développement logiciel (SDK) côté client).

## Version 0.9.4

- Prise en charge de la collecte des compteurs de performances à partir d’ordinateurs Windows 32 bits.
- Prise en charge du suivi manuel des dépendances à l’aide d’une nouvelle API de méthode ```trackDependency```.
- Possibilité de baliser un élément de télémétrie comme synthétique en ajoutant une propriété ```SyntheticSource``` à l’élément de rapport.
 

<!---HONumber=62-->