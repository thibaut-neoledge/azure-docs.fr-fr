<properties 
	pageTitle="Notes de publication de l’extension Visual Studio pour Application Insights" 
	description="Les dernières mises à jour de l’extension Visual Studio pour Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Notes de publication d’Outils Application Insights pour Visual Studio v 4.1

##Version 4.1
Cette version est fournie avec un certain nombre de nouvelles fonctionnalités et d’améliorations de fonctionnalités existantes. Pour obtenir cette version, vous devez disposer de la mise à jour Update 1 sur votre machine.

### Passage d’une exception à la méthode dans le code source
Désormais, les utilisateurs qui visualisent les exceptions de leurs applications de production dans la fenêtre Recherche d’Application Insights peuvent accéder directement à la méthode dans le code qui génère l’exception. Il suffit vous suffit de charger le bon projet, et nous nous chargerons du reste ! (Pour en savoir plus sur la fenêtre Recherche, consultez les notes de publication de 4.0 ci-dessous)

#### Comment cela fonctionne-t-il ?

Lorsqu’une solution n’est pas ouverte, il est possible d’utiliser AI Search sans ouvrir la solution. Dans ce cas, la zone d’arborescence des appels de procédure affiche un message d’information, et tous les éléments de l’arborescence des appels de procédure sont affichés en grisé.


Si les informations de fichier sont disponibles, certains éléments peuvent être des liens, mais l’élément d’information de la solution reste visible.

Un clic sur le lien hypertexte vous dirigera vers l’emplacement de la méthode sélectionnée dans votre code. Il se peut qu’il y ait une différence de numéro de version, mais cette fonction sera mise à jour dans les versions ultérieures : accédez à la version de code adéquate.

![Clic sur une exception](./media/app-insights-release-notes-vsix/jumptocode.png)

###Nouveaux points d’entrée vers l’expérience de recherche dans l’Explorateur de solutions. 

![Point d’entrée dans Explorateur de solutions](./media/app-insights-release-notes-vsix/searchentry.png)


###Afficher une notification toast si la publication est terminée
Une fenêtre contextuelle apparaîtra une fois le projet publié en ligne, afin que vous puissiez consulter vos données d’Application Insights en production.

![Fenêtre contextuelle](./media/app-insights-release-notes-vsix/publishtoast.png)

## Version 4.0

###Données Search Application Insights dans Visual Studio
Comme pour la recherche dans le portail Application Insights, il est possible de filtrer et d’effectuer des recherches sur différents types d’événements, valeurs et textes de propriété et d’inspecter des événements individuels.

![Fenêtre Recherche](./media/app-insights-release-notes-vsix/search.png)

###Consultez les données en provenance de votre boîte locale dans la fenêtre Outils de diagnostic

La télémétrie apparaîtra, avec d’autres données de débogage dans le concentrateur de diagnostic Visual Studio. Seul ASP.NET 4.5 est pris en charge. La prise en charge d’ASP.NET 5 sera disponible dans les versions à venir.

![Fenêtre de concentrateur de diagnostic](./media/app-insights-release-notes-vsix/diagtools.png)

###Ajoutez le kit de développement logiciel à votre projet sans être connecté à Azure

Vous n’avez plus à vous connecter à Azure pour ajouter des packages Application Insights à votre projet dans la boîte de dialogue Nouveau projet ou dans le menu contextuel du projet. Si vous vous connectez, le kit de développement logiciel est installé et configuré pour envoyer des données de télémétrie au portail, comme par le passé. Si vous ne vous connectez pas, le kit de développement logiciel est ajouté à votre projet et génère des données de télémétrie pour le concentrateur de diagnostic. Il vous est alors possible de le configurer ultérieurement si vous le souhaitez.

![Boîte de dialogue Nouveau projet](./media/app-insights-release-notes-vsix/newproject.png)

###Prise en charge des périphériques

Dans *Connect() ;* 2015, nous avons [annoncé](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que notre expérience Mobile DevOpsdes à des périphériques était HockeyApp. HockeyApp vous permet de distribuer des versions bêta à vos testeurs, de recueillir, de collecter et d’analyser tous les incidents à partir de votre application et de recueillir les commentaires directement de vos clients. HockeyApp vous assiste, quelle que soit la plateforme sur laquelle vous générez votre application mobile, qu’il s’agisse d’iOS, Android ou Windows ou d’une solution multiplateforme comme Xamarin, Cordova ou Unity.

Dans les versions futures de l’extension Application Insights,nous allons présenter de nouvelles fonctionnalités pour permettre une expérience davantage intégrée entre HockeyApp et Visual Studio. Pour l’instant, vous pouvez démarrer avec HockeyApp simplement en ajoutant la référence NuGet : consultez la [documentation](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) pour plus d’informations.

 

<!---HONumber=AcomDC_0121_2016-->