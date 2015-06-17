<properties 
	pageTitle="Analyse de la disponibilité et de la réactivité d'un site web" 
	description="Configurez des tests web dans Application Insights. Recevez des alertes si un site web devient indisponible ou répond lentement." 
	services="application-insights" 
documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/27/2015" 
	ms.author="awills"/>
 
# Analyse de la disponibilité et de la réactivité d'un site web

Après avoir déployé votre application web, vous pouvez configurer des tests web pour analyser sa disponibilité et sa réactivité. Application Insights envoie des demandes web à intervalles réguliers à partir de différents points dans le monde et vous alerte si votre application réagit lentement ou pas du tout.

![Web test example](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

Vous pouvez configurer des tests web pour n'importe quel point de terminaison HTTP accessible à partir du réseau Internet public.

*S'agit-il d'un site web Azure ? Il vous suffit [de créer le test web dans le panneau ][azurewebtest] du site web.*


1. [Création d'une nouvelle ressource ?](#create)
1. [Configuration d'un test web](#setup)
1. [Affichage des résultats](#monitor)
2. [Si vous constatez des erreurs...](#failures)
2. [Tests web à plusieurs étapes](#multistep)
1. [Modification ou désactivation d'un test](#edit)


 [Vidéo](#video)
 [Étapes suivantes](#next)

## Configuration d'un test web

### <a name="create"></a>1. Création d'une nouvelle ressource ?

Ignorez cette étape si vous avez déjà [configuré une ressource Application Insights][start] pour cette application et que vous souhaitez afficher les données de disponibilité au même endroit.

Inscrivez-vous à [Microsoft Azure](http://azure.com), accédez à la [version préliminaire du portail](https://portal.azure.com) et créez une ressource Application Insights. 

![New > Application Insights](./media/app-insights-monitor-web-app-availability/appinsights-11newApp.png)

### <a name="setup"></a>2. Création d'un test web

Dans le panneau de vue d'ensemble de votre application, cliquez sur la vignette de tests web. 

![Click the empty availability test](./media/app-insights-monitor-web-app-availability/appinsights-12avail.png)

*Vous avez déjà des tests web ? Cliquez sur la vignette des tests web et sélectionnez Ajouter un test web.*

Configurez les détails du test.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/appinsights-13availChoices.png)

- **L'URL** doit être visible à partir de l'Internet public. Elle peut inclure une chaîne de requête, par exemple pour vous permettre de tester un peu votre base de données. Si l'URL correspond à une redirection, nous allons la suivre, jusqu'à 10 redirections.

- **Les emplacements de test** sont les lieux d'où nos serveurs envoient des demandes web à votre URL. Choisissez-en deux ou trois de façon à distinguer les problèmes de votre site web des problèmes de réseau. Vous ne pouvez pas en sélectionner plus de trois.

- **Critères de réussite** :
    **Codes de retour HTTP** : 200 est courant. 

    **Chaîne de correspondance de contenu**, par exemple " Bienvenue ! " Nous allons vérifier qu'elle est présente dans chaque réponse. Il doit s'agir d'une chaîne standard sans caractère générique. N'oubliez pas que si votre contenu change, vous devrez peut-être l'actualiser.

- **Alertes** : par défaut, des alertes vous sont envoyées en cas d'erreurs répétées pendant plus de 15 minutes. Cependant, vous pouvez modifier cette fonctionnalité de manière à ce qu'elle soit plus ou moins sensible. Vous pouvez également modifier les adresses de messagerie notifiées.

#### Test d'autres URL

Vous pouvez ajouter d'autres tests pour autant d'URL que vous le souhaitez. Exemple : outre le test de votre page d'accueil, vous pouvez vérifier que votre base de données fonctionne correctement en testant une recherche sur l'URL.

![On the web tests blade, choose Add](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)


### <a name="monitor"></a>3. Affichage des rapports de disponibilité

Après 1 à 2 minutes, cliquez sur Actualiser dans le panneau de vue d'ensemble Il n'est pas automatiquement actualisé dans cette version.

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/appinsights-14availSummary.png)

Le graphique du panneau de vue d'ensemble combine les résultats de tous les tests web réalisés sur cette application.

#### Composants de page

Les images, les feuilles de style, les scripts et les autres composants statiques sont demandés dans le cadre du test.  

Le temps de réponse enregistré est la durée que prend le chargement complet de tous les composants.

Si le chargement d'un composant échoue, le test est marqué comme ayant échoué.

## <a name="failures"></a>Si vous constatez des erreurs...

Accédez au panneau des tests web pour afficher les résultats distincts de chaque test.

Ouvrez un test web.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-15webTestList.png)

Faites défiler l'écran jusqu'à **Échecs de tests** et choisissez un résultat.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/appinsights-17-availViewDetails.png)

Le résultat indique la raison de l'échec.

![Webtest run result](./media/app-insights-monitor-web-app-availability/appinsights-18-availDetails.png)

Pour plus d'informations, téléchargez le fichier de résultats et examinez-le dans Visual Studio.



##<a name="multistep"></a>Tests web à plusieurs étapes

Vous pouvez analyser un scénario qui implique une séquence d'URL. Par exemple, si vous analysez un site web commercial, vous pouvez vérifier que l'ajout d'articles au panier d'achat fonctionne correctement. 

Pour créer un test à plusieurs étapes, vous enregistrez le scénario à l'aide de Visual Studio et téléchargez ensuite l'enregistrement dans Application Insights. Application Insights relit le scénario à intervalles réguliers et vérifie les réponses.

#### 1. Enregistrement d'un scénario

Utilisez Visual Studio Ultimate pour enregistrer une session web.

1. Créez un projet de test de performances web.
    ![In Visual Studio, create a new project from the Web Performance and Load Test template.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. Ouvrez le fichier .webtest et lancez l'enregistrement.
    ![Open the .webtest file and click Record.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Effectuez les actions utilisateur à simuler dans votre test : ouvrez votre site web, ajoutez un produit au panier d'achat, et ainsi de suite. Ensuite, arrêtez le test. 
    ![The web test recorder runs in Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)
    Ne créez pas de scénario long. La limite est de 100 étapes et 2 minutes.
4. Exécutez le test dans Visual Studio pour vérifier qu'il fonctionne.
    Le test runner web ouvre un navigateur web et répète les actions enregistrées. Assurez-vous qu'il fonctionne comme prévu. 
    ![In Visual Studio, open the .webtest file and click Run.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)
 

(N'insérez pas de boucles dans votre code de test web.)

#### 2. Téléchargement du test web dans Application Insights

Dans le portail Application Insights, créez un test web.

![On the web tests blade, choose Add.](./media/app-insights-monitor-web-app-availability/appinsights-16anotherWebtest.png)

Sélectionnez le test à plusieurs étapes et téléchargez le fichier .webtest.

![Select multi-step webtest.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

Affichez les résultats de votre test et les échecs éventuels de la même manière que pour les tests d'URL unique. 

Un échec est souvent dû à un test trop long. Le test ne doit pas durer plus de deux minutes.


### Ajout de plug-ins de temps et de nombres aléatoires à votre test à plusieurs étapes

Supposons que vous testiez un outil qui obtient des données dépendant de l'heure, telles que des actions, à partir d'un flux externe. Lorsque vous enregistrez votre test web, vous devez utiliser des heures spécifiques, mais vous les définissez en tant que paramètres de test, à savoir StartTime et EndTime.

![A web test with parameters.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Lorsque vous exécutez le test, vous souhaitez que le paramètre EndTime corresponde systématiquement à l'heure actuelle et le paramètre StartTime à l'heure il y a 15 minutes.

Les plug-ins de test web vous permettent d'y parvenir.

1. Ajoutez un plug-in de test web pour chaque valeur de paramètre variable souhaitée. Dans la barre d'outils de test web, sélectionnez **Ajouter un plug-in de test web**.

    ![Choose Add Web Test Plugin and select a type.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    Dans cet exemple, nous allons utiliser deux instances du plug-in Date et Heure, une pour " il y a 15 minutes " et l'autre pour " maintenant ". 

2. Ouvrez les propriétés de chaque plug-in. Donnez-lui un nom et configurez-le pour qu'il utilise l'heure actuelle. Pour l'un d'eux, définissez Ajouter des minutes = -15.

    ![Set name, Use Current Time, and Add Minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. Dans les paramètres de test web, utilisez {{nom du plug-in}} pour référencer un nom de plug-in.

    ![In the test parameter, use {{plug-in name}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Maintenant, téléchargez votre test sur le portail. Il va utiliser les valeurs dynamiques à chaque exécution du test.

## <a name="edit"></a> Modification ou désactivation d'un test

Ouvrez un test à modifier ou à désactiver.

![Edit or disable a web test](./media/app-insights-monitor-web-app-availability/appinsights-19-availEdit.png)

Vous pouvez par exemple désactiver des tests web lorsque vous effectuez des opérations de maintenance sur votre service.

## <a name="video"></a>Vidéo

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Étapes suivantes

[Recherche dans les journaux de diagnostic][diagnostic]

[Résolution des problèmes][QnA]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




[azurewebtest]: ../insights-create-web-tests/

<!--HONumber=46--> 
 