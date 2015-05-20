<properties 
	pageTitle="Prise en main d’Application Insights avec Java dans Eclipse" 
	description="Le plug-in Eclipse permet d’ajouter la surveillance des performances et de l’utilisation de votre site web Java avec Application Insights" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="awills"/>
 
# Prise en main d’Application Insights avec Java dans Eclipse

Le Kit de développement logiciel \(SDK\) Application Insights envoie la télémétrie de votre application web Java afin que vous puissiez en analyser les performances et l’utilisation. Le plug-in Eclipse pour Application Insights installe automatiquement le Kit de développement logiciel \(SDK\) dans votre projet pour pouvoir bénéficier de la télémétrie de base, plus une API avec laquelle vous pouvez écrire des éléments de télémétrie personnalisés.


## Configuration requise

Actuellement, le plug-in fonctionne pour les projets web dynamiques dans Eclipse. \([Ajout d’Application Insights à d’autres types de projets Java][java].\)

Vous devez disposer des éléments suivants :

* Oracle JRE 1.6 ou version ultérieure
* Un abonnement [Microsoft Azure](http://azure.microsoft.com/). \(Vous pouvez commencer par l’[essai gratuit](http://azure.microsoft.com/pricing/free-trial/).\)
* [IDE Eclipse pour développeurs Java EE](http://www.eclipse.org/downloads/), Indigo ou version ultérieure.
* Windows 7 ou version ultérieure ou Windows Server 2008 ou version ultérieure

## Installer le Kit de développement logiciel \(SDK\) sur Eclipse \(opération unique\)

Vous ne devez effectuer cette opération qu’une seule fois par machine. Cette étape installe un kit de ressources qui peut ensuite ajouter le Kit de développement logiciel \(SDK\) pour chaque projet web dynamique.

1. Dans Eclipse, cliquez sur Aide, Installer un nouveau logiciel.

    ![Aide, installation de nouveaux logiciels](./media/app-insights-java-eclipse/0-plugin.png)

2. Le Kit de développement logiciel \(SDK\) se trouve dans http://dl.msopentech.com/eclipse, sous Azure Toolkit.
3. Désactivez l’option **Contacter tous les sites de mise à jour...**.

    ![Pour le Kit de développement logiciel \(SDK\) Application Insights, désactivez Contacter tous les sites de mise à jour.](./media/app-insights-java-eclipse/1-plugin.png)

Suivez les étapes restantes pour chaque projet Java.

## Obtenir une clé d’instrumentation Application Insights

Votre analyse d’utilisation et de performances sera affichée dans une ressource Azure sur le portail web Azure. Dans cette étape, vous définissez une ressource Azure pour votre application.

1. Connectez-vous au [portail Microsoft Azure](https://portal.azure.com). \(Vous avez besoin d’un [abonnement Azure](http://azure.microsoft.com/).\)
2. Créer une ressource Application Insights dans Azure

    ![Cliquez sur + et choisissez Ajouter Application Insights.](./media/app-insights-java-get-started/01-create.png)
3. Définissez le type d’application sur Application web Java.

    ![Indiquez le nom, choisissez l’application web Java, puis cliquez sur Créer.](./media/app-insights-java-get-started/02-create.png)
4. Obtenez la clé d’instrumentation de la nouvelle ressource. Vous devez la coller dans votre projet dans Eclipse.

    ![Dans la nouvelle vue d’ensemble des ressources, cliquez sur Propriétés et copiez la clé d’instrumentation.](./media/app-insights-java-get-started/03-key.png)

## Ajouter le Kit de développement logiciel \(SDK\) à votre projet Java

1. Ajoutez Application Insights à partir du menu contextuel de votre projet web.

    ![Dans la nouvelle vue d’ensemble des ressources, cliquez sur Propriétés et copiez la clé d’instrumentation.](./media/app-insights-java-eclipse/4-addai.png)
2. Collez la clé d’instrumentation que vous avez obtenue sur le portail Azure.

    ![Dans la nouvelle vue d’ensemble des ressources, cliquez sur Propriétés et copiez la clé d’instrumentation.](./media/app-insights-java-eclipse/5-config.png)


La clé est envoyée avec chaque élément de télémétrie et indique à Application Insights de l’afficher dans votre ressource.

## Exécuter l’application et consulter les mesures

Exécutez votre application.

Revenez à votre ressource Application Insights dans Microsoft Azure.

Les données des demandes HTTP apparaissent dans le panneau Vue d’ensemble. \(Si elles n’y sont pas, attendez quelques secondes et cliquez sur Actualiser\).

![Réponse du serveur, nombre de demandes et échecs](./media/app-insights-java-track-http-requests/5-results.png)
 

Cliquez sur un des graphiques pour afficher des mesures plus détaillées.

![Nombres de demandes par nom](./media/app-insights-java-track-http-requests/6-barchart.png)


[En savoir plus sur les mesures.][metrics]

 

Lorsque vous affichez les propriétés d’une demande, vous voyez les événements de télémétrie associés, par exemple les demandes et les exceptions.
 
![Tous le suivi pour cette demande](./media/app-insights-java-track-http-requests/7-instance.png)


## Télémétrie côté client

Dans la vignette de démarrage rapide du panneau Vue d’ensemble, vous pouvez obtenir un script à ajouter à vos pages web.

Les mesures liées au nombre de consultations de la page, à l’utilisateur et à la session s’affichent dans le panneau de la vue d’ensemble :

![Sessions, utilisateurs et affichages de pages](./media/appinsights/appinsights-47usage-2.png)

[En savoir plus sur la configuration de la télémétrie côté client.][usage]

## Tests web de disponibilité

Application Insights peut tester votre site web à intervalles réguliers pour vérifier qu’il fonctionne et répond correctement. Cliquez sur les graphiques de tests web vides dans le panneau Vue d’ensemble et indiquez votre URL publique.

Vous obtenez des graphiques du temps de réponse, ainsi que des notifications par courrier électronique si votre site ne fonctionne plus.

![Exemple de test web](./media/appinsights/appinsights-10webtestresult.png)

[En savoir plus sur les tests de disponibilité web.][availability]

## Journaux de diagnostic

Si vous utilisez Logback ou Log4J \(v1.2 ou v2.0\) pour le suivi, vous pouvez faire en sorte que vos journaux de suivi soient envoyés automatiquement à Application Insights, où vous pouvez les explorer et effectuer des recherches.

[En savoir plus sur les journaux de diagnostic][javalogs]

## Télémétrie personnalisée 

Insérez quelques lignes de code dans votre application web Java pour découvrir ce qu’en font les utilisateurs ou pour faciliter le diagnostic des problèmes.

Vous pouvez insérer le code dans le JavaScript de la page web et dans le Java côté serveur.

[En savoir plus sur la télémétrie personnalisée][track]



## Étapes suivantes

#### Détecter et diagnostiquer les problèmes

* [Ajoutez la télémétrie de client web][usage] pour obtenir la télémétrie des performances du client web.
* [Configurez les tests web][availability] pour vous assurer que votre application est bien active.
* [Recherchez les événements et les journaux][diagnostic] pour diagnostiquer les problèmes.
* [Capturez le suivi Log4J ou Logback][javalogs].

#### Suivi de l'utilisation

* [Ajoutez la télémétrie de client web][usage] pour surveiller les affichages de page et autres mesures utilisateur de base.
* [Suivez des événements et des mesures personnalisés][track] pour en savoir plus sur la façon dont votre application est utilisée, à la fois sur le client et sur le serveur.


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md


<!--HONumber=54-->