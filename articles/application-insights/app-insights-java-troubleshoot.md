<properties 
	pageTitle="Résolution des problèmes d'Application Insights dans un projet web Java" 
	description="Guide de dépannage et questions-réponses." 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="awills"/>
 
# Guide de dépannage et questions-réponses concernant Application Insights pour Java

Vous avez des questions concernant [Visual Studio Application Insights dans Java][java] ou vous rencontrez des problèmes ? Voici quelques conseils.


## Erreurs de build

*Dans Eclipse, quand j’ajoute le kit de développement logiciel (SDK) Application Insights via Maven ou Gradle, j’obtiens des erreurs de validation de build ou de somme de contrôle.*

* Si l’élément <version>de dépendance utilise un modèle avec des caractères génériques (par exemple, (Maven) `<version>[0.9,)</version>` or (Gradle) `version:'0.9.+'`), essayez de spécifier une version spécifique, comme par exemple `0.9.3`.

## Absence de données 

*J’ai ajouté Application Insights sans problème et exécuté mon application, mais je ne vois aucune donnée dans le portail.*

* Attendez une minute, puis cliquez sur Actualiser. L'actualisation n'est pas automatique pour le moment.
* Vérifiez que vous disposez d'une clé d'instrumentation définie dans le fichier ApplicationInsights.xml (situé dans le dossier de ressources de votre projet)
* Vérifiez qu’aucun nœud `<DisableTelemetry>true</DisableTelemetry>` ne se trouve dans le fichier .xml.
* Vous devrez ouvrir les ports TCP 80 et 443 de votre pare-feu pour le trafic sortant vers dc.services.visualstudio.com et f5.services.visualstudio.com.
* Dans le panneau de démarrage Microsoft Azure, examinez la carte d'état du service. Si des alertes sont indiquées, attendez qu'elles soient corrigées (OK), puis fermez et rouvrez le volet de votre application Application Insights.
* Activez la journalisation dans la fenêtre de console IDE en ajoutant un élément `<SDKLogger />` sous le nœud racine dans le fichier ApplicationInsights.xml (situé dans le dossier de ressources de votre projet), puis vérifiez les entrées précédées du mot [Error].
* Assurez-vous que le fichier ApplicationInsights.xml approprié a été correctement chargé par le Kit de développement logiciel (SDK) Java, en vérifiant que le message de sortie de la console « Le fichier de configuration a été trouvé » s’affiche.
* Si le fichier de configuration est introuvable, vérifiez les messages de sortie pour voir où cette recherche a été effectuée et vous assurer que le fichier ApplicationInsights.xml se trouve dans l’un des emplacements de recherche. En règle générale, vous pouvez placer le fichier de configuration près du JAR du Kit de développement logiciel (SDK) Application Insights. Par exemple, il s’agit du dossier WEB-INF/lib dans Tomcat.



#### Je pouvais voir les données, mais plus maintenant

* Vérifiez le [blog d'état](http://blogs.msdn.com/b/applicationinsights-status/).
* Vous souhaitez savoir si vous avez atteint votre quota mensuel de points de données ? Ouvrez Paramètres/Quota et tarification pour le savoir. Le cas échéant, vous pouvez mettre à niveau votre forfait ou payer pour disposer d’une capacité supplémentaire. Consultez le [mécanisme de tarification](http://azure.microsoft.com/pricing/details/application-insights/).



## Absence de données d'utilisation

*Je vois des données concernant les requêtes et les temps de réponse, mais rien concernant les affichages de pages, le navigateur ou les données utilisateur.*

Assurez-vous d'avoir configuré correctement votre application de façon à envoyer la télémétrie depuis le serveur. L’étape suivante consiste à [configurer vos pages web pour l’envoi de la télémétrie depuis le navigateur web][usage].

Si votre client est une application d’un [téléphone ou d’un autre appareil][platforms], vous pouvez également envoyer la télémétrie à partir de ce dernier.

Utilisez la même clé d'instrumentation pour configurer la télémétrie de votre client et de votre serveur. Les données apparaîtront dans la même ressource Application Insights, et vous pourrez mettre en corrélation les événements du serveur et du client.



## Désactivation de la télémétrie

*Comment puis-je désactiver la collecte télémétrique ?*

Dans le code :

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**Ou**

Mettez à jour ApplicationInsights.xml (situé dans le dossier de ressources de votre projet). Ajoutez le code suivant sous le nœud racine :

    <DisableTelemetry>true</DisableTelemetry>

À l'aide de la méthode XML, vous devrez redémarrer l'application si vous modifiez la valeur.

## Modification de la cible

*Comment puis-je changer la ressource Azure à laquelle mon projet envoie des données ?*

* [Obtenez la clé d’instrumentation de la nouvelle ressource.][java]
* Si vous avez ajouté Application Insights à votre projet à l’aide du kit de ressources Azure pour Eclipse, cliquez avec le bouton droit sur votre projet web, sélectionnez **Azure**, **Configurer Application Insights**, puis modifiez la clé.
* Sinon, mettez à jour la clé du fichier ApplicationInsights.xml (situé dans le dossier de ressources de votre projet).


## L'écran d'accueil Azure

*J’ai ouvert [le portail Azure](http://portal.azure.com). Est-ce que la carte fournit des informations concernant mon application ?*

Non, elle montre l'intégrité des serveurs Azure dans le monde entier.

*Depuis le panneau de démarrage Azure (l’écran d’accueil), comment trouver les données relatives à mon application ?*

En supposant que vous ayez [configuré votre application pour Application Insights][java], cliquez sur Parcourir, sélectionnez Application Insights, puis sélectionnez la ressource d’application que vous avez créée pour votre application. Pour aller plus vite la prochaine fois, vous pouvez épingler votre application au panneau de démarrage.

## Serveurs intranet

*Puis-je surveiller un serveur sur mon intranet ?*

Oui, à condition que votre serveur puisse envoyer la télémétrie vers le portail Application Insights via l'Internet public.

Vous devrez ouvrir les ports TCP 80 et 443 de votre pare-feu pour le trafic sortant vers dc.services.visualstudio.com et f5.services.visualstudio.com.

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
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=August15_HO6-->