<properties
	pageTitle="Notes de publication de l’extension Visual Studio pour Developer Analytics"
	description="Dernières mises à jour des outils Visual Studio pour Developer Analytics"
	services="application-insights"
    documentationCenter=""
	authors="acearun"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2016"
	ms.author="acearun"/>

# Notes de publication - Outils de Developer Analytics
##### Analyses Application Insights et HockeyApp dans Visual Studio
## Version 7.0.1
Application Insights prend désormais en charge les projets ASP.NET Core RC2 dans Visual Studio. Vous pouvez ajouter Application Insights aux nouveaux projets ASP.NET Core RC2 à partir de la boîte de dialogue Nouveau projet, ou à un projet existant en double-cliquant sur le projet dans l’Explorateur de solutions puis en sélectionnant « Ajouter la télémétrie Application Insights... »

![Prise en charge de .NET Core](./media/app-insights-release-notes-vsix/NetCoreSupport.PNG)

Les projets ASP.NET 5 RC1 et ASP.NET Core RC2 sont également pris en charge dans la fenêtre Outils de diagnostic. Vous verrez des événements Application Insights telles que les demandes et les exceptions de votre application ASP.NET pendant le débogage local sur votre PC. Vous pouvez explorer chaque événement afin d’obtenir plus d’informations en cliquant sur « Rechercher ».

![Prise en charge des outils de diagnostic](./media/app-insights-release-notes-vsix/DiagnosticTools.PNG)

Autres nouvelles fonctionnalités :

* Nous avons rendu la fonction de recherche d’Application Insights plus rapide et plus intuitive en appliquant automatiquement des plages horaires et des filtres de détail à mesure que vous les sélectionnez
* Application Insights Search propose désormais une option permettant d’accéder au code à partir de la télémétrie de la demande
* Nous avons amélioré l’expérience de connexion HockeyApp.

## Version 5.2
Nous avons le plaisir d’annoncer l’introduction de scénarios HockeyApp dans Visual Studio. La première intégration que nous avons activée est la distribution bêta des applications Universal Windows et Windows Forms au sein de Visual Studio.

La distribution bêta vous permet de télécharger les premières versions de vos applications vers HockeyApp pour une distribution vers un sous-ensemble choisi de clients ou de testeurs. La distribution bêta, associée aux fonctionnalités HockeyApp de collecte d’incidents et de commentaires d’utilisateurs, peut fournir des informations précieuses sur votre application avant une publication majeure. Vous pouvez utiliser ces informations pour résoudre les problèmes de votre application avant que les choses ne deviennent plus sérieuses (mauvaises évaluations, commentaires négatifs, etc.).

Découvrez combien il est simple de télécharger des builds pour la distribution bêta à partir de Visual Studio…
### Applications Windows universelles
Le menu contextuel d’un nœud de projet UWP inclut désormais une option pour télécharger votre build vers HockeyApp.

![Menu contextuel du projet pour les applications Universal](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Sélectionnez l’élément et observez la boîte de dialogue de téléchargement HockeyApp. Vous devez avoir un compte HockeyApp pour télécharger votre build. Si vous êtes un nouvel utilisateur, il vous suffit de créer un compte en toute simplicité.

Une fois que vous êtes connecté, le formulaire de téléchargement apparaît dans la boîte de dialogue.

![Boîte de dialogue Téléchargement pour les applications Universal](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Sélectionnez le contenu à télécharger (appxbundle ou appx) et sélectionnez les options de version de l’Assistant. Vous pouvez éventuellement ajouter des notes de publication sur la page suivante. Cliquez sur Terminer pour commencer le téléchargement.

Lorsque le téléchargement est terminé, une notification toast HockeyApp apparaît avec une confirmation et un lien vers l’application dans le portail HockeyApp.

![Notification toast de téléchargement terminé](./media/app-insights-release-notes-vsix/UploadComplete.png)

C’est tout ! Vous venez de télécharger une build pour la distribution bêta en quelques clics.

Le portail HockeyApp vous permet de gérer votre application de différentes manières (inviter des utilisateurs, consulter les rapports d’incidents et les commentaires, modifier des détails, etc.).

![Portail HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Pour plus d’informations sur la gestion des applications, consultez la [Base de connaissances Hockey App](http://support.hockeyapp.net/kb/app-management-2).

### Applications Windows Forms
Le menu contextuel d’un nœud de projet Windows Form inclut une option pour télécharger votre build vers HockeyApp.

![Menu contextuel du projet pour les applications Windows Forms](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Une boîte de dialogue de téléchargement HockeyApp similaire à celle des applications Universal s’ouvre.

![Boîte de dialogue Téléchargement pour les applications Windows Form](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

Vous remarquez un champ supplémentaire dans cet Assistant, qui permet de spécifier la version de l’application. Pour les applications Universal, les informations sont renseignées à partir du fichier manifeste. Windows Forms n’a malheureusement pas d’équivalent, et les informations doivent être spécifiées manuellement.

Le reste du flux est similaire aux applications Universal : sélection d’une build, ajout d’options de version et de notes de publication, téléchargement et gestion dans le portail HockeyApp.

C’est aussi simple que cela. Faites le test et dites-nous ce que vous en pensez.
## Version 4.3
### Recherche de données de télémétrie des sessions de débogage local
Avec cette version, nous ajoutons la capacité de rechercher de la télémétrie Application Insights générée pendant la session de débogage de Visual Studio. Auparavant, la recherche n’était possible que si vous aviez inscrit votre application auprès d’Application Insights. Avec cette version, il suffit que le Kit de développement logiciel (SDK) Application Insights soit installé dans votre application pour rechercher les données de télémétrie locales.

#### Si vous avez une application ASP.NET avec le Kit de développement logiciel (SDK) Application Insights

- Déboguez votre application.
- Ouvrez la Recherche Application Insights à l’aide de l’une des méthodes suivantes
	- Menu Affichage -> Autres fenêtres -> Recherche Application Insights
	- Cliquez sur le bouton de la barre d’outils Application Insights
	- Dans l’Explorateur de solutions, développez ApplicationInsights.config -> Recherche de la télémétrie de la session de débogage
- Si vous n’êtes pas encore inscrit auprès du service Application Insights, la fenêtre de recherche s’ouvre en mode « Télémétrie de la session de débogage ».
- Cliquez sur l’icône de recherche pour afficher vos données de télémétrie locales.

![Chargement terminé](./media/app-insights-release-notes-vsix/LocalSearch.png)



##Version 4.2
Dans cette version, nous avons ajouté des fonctionnalités qui facilitent la recherche de données dans le contexte des événements, la possibilité d'accéder au code à partir de plusieurs événements de données, et une expérience sans effort pour envoyer vos données de journalisation à Application Insights. Cette extension est mise à jour tous les mois. Si vous avez des commentaires ou des demandes de fonctionnalités, envoyez-les à aidevtools@microsoft.com
###- Expérience de journalisation 0 clic
Si vous utilisez déjà un suivi NLog, Log4Net ou System.Diagnostics, vous n'avez pas à vous soucier du transfert de toutes vos traces vers AI : nous intégrons désormais les adaptateurs de journalisation Application Insights avec l'expérience de configuration normale. Si vous disposez déjà d'une de ces infrastructures de journalisation configurées, voici comment procéder :
####Si Application Insights a déjà été ajouté
- Cliquez avec le bouton droit sur le nœud du projet, puis sélectionnez Application Insights, Configurer Application Insights. Vérifiez que l’option d'ajout de l'adaptateur approprié apparaît dans la fenêtre de configuration.
- Ou lorsque vous générez la solution, observez la fenêtre contextuelle qui apparaît en haut à droite de l’écran et cliquez sur Configurer. ![Toast de connexion](./media/app-insights-release-notes-vsix/LoggingToast.png)

Une fois l’adaptateur d’enregistrement installé, vous pouvez exécuter votre application et vous assurer que les données apparaissent comme suit sous l’onglet Outils de diagnostic : ![Traces](./media/app-insights-release-notes-vsix/Traces.png)
###- L’utilisateur peut accéder/rechercher le code où la propriété d'événement de télémétrie est émise
Avec la nouvelle version, l’utilisateur peut cliquer sur n'importe quel détail de l'événement et rechercher ainsi une chaîne correspondante dans la solution actuellement ouverte. Les résultats apparaissent dans la liste « Résultats de la recherche » de Visual Studio, comme illustré ci-dessous : ![Rechercher une correspondance](./media/app-insights-release-notes-vsix/FindMatch.png)
###- Nouvel écran pour utilisateur non connecté dans la fenêtre de recherche
Nous avons amélioré l’apparence de la fenêtre de recherche pour guider les utilisateurs dans la recherche de leurs données en production. ![Fenêtre Recherche](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- L’utilisateur peut voir tous les événements de télémétrie associés à l'événement
Un nouvel onglet en regard des détails de l'événement a été ajouté, contenant des requêtes prédéfinies pour afficher toutes les données associées à l'événement de télémétrie que l'utilisateur examine. Par exemple : chaque demande comporte un champ appelé ID d'opération, et chaque événement associé à cette demande aura le même ID d'opération. Par conséquent, si une exception s'est produite lors du traitement de la demande, elle affichera le même ID d'opération que la demande pour faciliter sa localisation, et ainsi de suite. Un utilisateur qui observe une requête peut donc désormais cliquer sur « Toute la télémétrie pour cette opération » pour ouvrir un nouvel onglet présentant les nouveaux résultats de la recherche. ![Éléments connexes](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - Ajout d’un historique avant/après dans la recherche
L’utilisateur peut maintenant aller et venir dans les résultats de la recherche. ![Retour](./media/app-insights-release-notes-vsix/GoBAck.png)

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

Lors de *Connect();* 2015, nous avons [annoncé](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que notre expérience DevOps mobile pour les appareils était HockeyApp. HockeyApp vous permet de distribuer des versions bêta à vos testeurs, de recueillir, de collecter et d’analyser tous les incidents à partir de votre application et de recueillir les commentaires directement de vos clients. HockeyApp vous assiste, quelle que soit la plateforme sur laquelle vous générez votre application mobile, qu’il s’agisse d’iOS, Android ou Windows ou d’une solution multiplateforme comme Xamarin, Cordova ou Unity.

Dans les versions futures de l’extension Application Insights,nous allons présenter de nouvelles fonctionnalités pour permettre une expérience davantage intégrée entre HockeyApp et Visual Studio. Pour le moment, vous pouvez commencer à utiliser HockeyApp en ajoutant simplement la référence NuGet : consultez la [documentation](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) pour plus d’informations.

<!---HONumber=AcomDC_0615_2016-->