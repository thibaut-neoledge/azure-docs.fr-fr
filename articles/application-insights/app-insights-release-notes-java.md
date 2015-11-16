<properties
	pageTitle="Notes de publication pour Application Insights pour Java"
	description="Les dernières mises à jour pour le Kit de développement logiciel (SDK) Java."
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
	ms.date="09/21/2015"
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

## Version 1.0.2
- Possibilité d'empêcher le remplacement de la clé d'instrumentation à l'aide de celle spécifiée dans le fichier config lorsqu'elle est fournie explicitement dans le code.
- Traitement de tous les codes d'état HTTP réussis et signalisation de la réussite des requêtes HTTP correspondante.
- Traitement de toutes les exceptions générées par le ConfigurationFileLocator.


## Version 1.0.1
- L'[agent Java](app-insights-java-agent.md) collecte les informations de dépendance concernant les éléments suivants :
	- Appels HTTP passés via HttpClient, OkHttp et RestTemplate (Spring).
	- Appels à Redis passés via le client Jedis. Lorsqu'un seuil configurable est dépassé, le Kit de développement logiciel récupère également les arguments d'appel.
	- Appels JDBC passés avec des clients Oracle DB et Apache Derby DB.
	- Prendre en charge le type de requête « executeBatch » pour les instructions préparées ; le Kit de développement logiciel (SDK) affiche l’instruction avec le nombre de lots.
	- Fournir le plan de requête pour les clients JDBC prenant en charge cela (MySql, PostgreSql) ; le plan de requête est uniquement extrait lorsqu'un seuil configurable est dépassé.

## Version 1.0.0
- Ajout de la prise en charge du plug-in d’écriture Application Insights pour CollectD.
- Ajout de la prise en charge de l'agent Java Application Insights.
- Correction d'un problème de compatibilité avec la prise en charge des versions HttpClient 4.2 et ultérieures.

## Version 0.9.6
- Rendez le Kit de développement logiciel (SDK) Java compatible avec servlet v2.5 et HttpClient pre-v4.3.
- Ajout de la prise en charge des intercepteurs Java EE.
- Suppression des dépendances redondantes de l'appender Logback.

## Version 0.9.5  

- Résolution du problème qui empêchait la corrélation des événements personnalisés avec les utilisateurs/sessions en raison d'erreurs d'analyse de cookie.  
- Amélioration de la logique de résolution de l'emplacement du fichier de configuration ApplicationInsights.xml.
- Les cookies de sessions et d'utilisateurs anonymes ne seront pas générés côté serveur. Pour implémenter l'utilisateur et la session de suivi pour les applications web, instrumentation avec le SDK JavaScript est désormais obligatoire – les cookies provenant du SDK JavaScript sont toujours respectés. Notez que cette modification peut provoquer un changement significatif du nombre d'utilisateurs et de sessions car seules les sessions créées par l'utilisateur sont désormais comptabilisées.

## Version 0.9.4

- Prise en charge de la collecte des compteurs de performances à partir d’ordinateurs Windows 32 bits.
- Prise en charge du suivi manuel des dépendances à l’aide d’une nouvelle API de méthode ```trackDependency```.
- Possibilité de baliser un élément de télémétrie comme synthétique en ajoutant une propriété ```SyntheticSource``` à l’élément de rapport.

<!---HONumber=Nov15_HO2-->