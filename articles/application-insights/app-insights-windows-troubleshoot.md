<properties 
	pageTitle="Résolution des problèmes Application Insights avec des appareils Windows" 
	description="Guide de dépannage et questions-réponses." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="awills"/>
 
# Guide de dépannage et questions-réponses concernant Application Insights pour les appareils Windows

Vous avez des questions concernant [Visual Studio Application Insights dans Windows][windows] ou vous rencontrez des problèmes ? Voici quelques conseils.



## Absence de données 

*J’ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.*

* Attendez une minute, puis cliquez sur Actualiser. L'actualisation n'est pas automatique pour le moment.
* Vérifiez que vous disposez d’une clé d’instrumentation définie dans le fichier ApplicationInsights.config, et que celle-ci est identique à la clé du portail Application Insights. Pour afficher la clé, cliquez sur Essentials sur le panneau Vue d’ensemble.
* Assurez-vous que votre application [demande un accès au réseau sortant](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx).
* Existe-t-il un pare-feu entre votre émulateur ou l’appareil test et le portail Application Insights ? Vous devrez peut-être ouvrir les ports TCP 80 et 443 pour le trafic sortant vers dc.services.visualstudio.com et f5.services.visualstudio.com.
* Dans le panneau de démarrage Microsoft Azure, examinez la carte d'état du service. Si des alertes sont indiquées, attendez qu'elles soient corrigées (OK), puis fermez et rouvrez le volet de votre application Application Insights.


#### Je pouvais voir les données, mais plus maintenant

* Vérifiez le [status blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous souhaitez savoir si vous avez atteint votre quota mensuel de points de données ? Ouvrez Paramètres/Quota et tarification pour le savoir. Le cas échéant, vous pouvez mettre à niveau votre forfait ou payer pour disposer d’une capacité supplémentaire. Consultez le [mécanisme de tarification](http://azure.microsoft.com/pricing/details/application-insights/).


## Comment puis-je ajouter Application Insights à une application universelle ?

Ajoutez les packages NuGet manuellement à chaque projet de vos appareils dans votre solution. Consultez l’article [Prise en main : applications universelles][universal].

## Désactivation de la télémétrie

*Comment puis-je désactiver la collecte télémétrique ?*

Dans le code :

    TelemetryConfiguration config = TelemetryConfiguration.Active;
    config.TrackingIsDisabled = true;

## Modification de la ressource cible

*Comment puis-je changer la ressource Application Insights à laquelle mon projet envoie des données ?*

Dans le nouveau panneau Vue d’ensemble d’Application Insights, ouvrez Essentials et copiez la clé d’instrumentation.

Collez la clé dans votre fichier ApplicationInsights.config, dans le nœud `<InstrumentationKey>`.

Ou, si vous souhaitez modifier les cibles au moment de l’exécution, utilisez :

     var telemetry = new TelemetryClient();
     telemetry.Context.InstrumentationKey = newKey;
    
## Comment puis-je surveiller une application client-serveur ?

Il existe deux façons de procéder :

* Créez deux ressources Application Insights (avec des clés d’instrumentation différentes) : une pour le client et une pour le serveur. Vous devez cependant les créer dans le même groupe de ressources Azure. Vous pourrez ainsi basculer facilement de l’une à l’autre.
* Utilisez une ressource Application Insights et placez votre clé d’instrumentation dans les projets client et serveur. Vous pourrez dès lors corréler les métriques et les événements des deux sources.

Pour vous aider à mettre en corrélation les événements dans le client et dans le serveur, générez un ID d’opération pour chaque requête. Transmettez-le entre le client et le serveur, ajoutez-le à la télémétrie aux deux extrémités :

    telemetry.Context.OperationId = opid;


## L'écran d'accueil Azure

*J’ai ouvert [le portail Azure](http://portal.azure.com). Est-ce que la carte fournit des informations concernant mon application ?*

Non, elle montre l'intégrité des serveurs Azure dans le monde entier.

*Depuis le panneau de démarrage Azure (l’écran d’accueil), comment trouver les données relatives à mon application ?*

En supposant [que vous ayez déjà configuré votre application pour Application Insights][windows], cliquez sur Parcourir, sélectionnez Application Insights, puis sélectionnez la ressource que vous avez créée pour votre application. Pour aller plus vite la prochaine fois, vous pouvez épingler la ressource au panneau de démarrage.

## Conservation des données 

*Combien de temps les données sont-elles conservées dans le portail ? Sont-elles sécurisées ?*

Consultez [Rétention de données et confidentialité][data].

## Étapes suivantes

*J’ai configuré Application Insights pour mon application serveur Java. Que puis-je faire d’autre ?*

* [Surveiller la disponibilité de vos pages web][availability]
* [Surveiller l’utilisation des pages web][usage]
* [Suivre l’utilisation de vos applications et diagnostiquer les problèmes des applications de vos appareils][platforms]
* [Écrire du code pour suivre l’utilisation de votre application][track]
* [Capture des journaux de diagnostic][javalogs]


## Obtenir de l'aide

* [Dépassement de capacité de la pile](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[universal]: app-insights-windows-get-started.md#universal
[usage]: app-insights-web-track-usage.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=58_postMigration-->