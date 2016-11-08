---
title: Déploiement avec distribution d’une version d’évaluation (test bêta) dans Azure App Service
description: Apprenez à déployer de nouvelles fonctionnalités dans votre application ou à tester vos mises à jour en mode bêta grâce à ce didacticiel de bout en bout. Il réunit les fonctionnalités App Service comme la publication continue, les emplacements, le routage du trafic et l’intégration d’Application Insights.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: wpickett
editor: ''

ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cephalin

---
# Déploiement avec distribution d’une version d’évaluation (test bêta) dans Azure App Service
Ce didacticiel vous montre comment procéder à des *déploiements avec distribution d’une version d’évaluation* en intégrant les différentes fonctionnalités d’[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) et d’[Azure Application Insights](/services/application-insights/).

La *distribution d’une version d’évaluation* est un processus de déploiement qui valide une nouvelle fonctionnalité ou une modification auprès d’un nombre limité de clients réels. Il s’agit d’un test majeur dans un scénario de production. Cette opération est comparable à un test bêta et est parfois appelée « version de test contrôlée ». Beaucoup de grandes entreprises avec une présence web utilisent cette approche pour obtenir les premières validations de leurs mises à jour d’application dans leur pratique de [développement agile](https://en.wikipedia.org/wiki/Agile_software_development). Azure App Service vous permet d’intégrer le test en production à la publication continue et à Application Insights pour implémenter le même scénario d’opérations de développement. Avantages de cette approche :

* **Obtenir des commentaires réels *avant* la sortie des mises à jour en production** : qu’y a-t-il de mieux qu’obtenir des commentaires dès la sortie de votre application ? Les obtenir avant ! Vous pouvez tester les mises à jour avec le trafic et les comportements des utilisateurs réels dès que vous le souhaitez dans le cycle de vie du produit.
* **Améliorer [le développement continu piloté par les tests ](https://en.wikipedia.org/wiki/Continuous_test-driven_development)** : en intégrant le test en production à l’intégration et l’instrumentation continues avec Application Insights, la validation par les utilisateurs se produit automatiquement et tôt dans le cycle de vie de votre produit. Cela contribue à réduire les investissements en temps dans l’exécution de tests manuels.
* **Optimiser le workflow de test** : grâce à l’automatisation du test en production avec une instrumentation de surveillance continue, vous pouvez potentiellement atteindre les objectifs de différents genres de tests dans un processus unique, notamment l’[intégration](https://en.wikipedia.org/wiki/Integration_testing), la [régression](https://en.wikipedia.org/wiki/Regression_testing), la [convivialité](https://en.wikipedia.org/wiki/Usability_testing), l’accessibilité, la localisation, les [performances](https://en.wikipedia.org/wiki/Software_performance_testing), la [sécurité](https://en.wikipedia.org/wiki/Security_testing) et l’[acceptation](https://en.wikipedia.org/wiki/Acceptance_testing).

Un déploiement avec distribution d’une version d’évaluation ne se limite pas au routage du trafic en direct. Dans un tel déploiement, vous souhaitez bénéficier d’une visibilité aussi rapidement que possible, en cas de bogues inattendus, de dégradation des performances ou de problèmes d’expérience utilisateur. N’oubliez pas : vous êtes confronté à des clients réels. Pour faire les choses correctement, vous devez donc vous assurer que vous avez configuré votre déploiement avec distribution d’une version d’évaluation de manière à collecter toutes les données requises afin de prendre une décision éclairée à l’étape suivante. Ce didacticiel vous montre comment collecter des données avec Application Insights. Toutefois, vous pouvez utiliser New Relic ou d’autres technologies adaptées à votre scénario.

## Procédure à suivre
Dans ce didacticiel, vous allez apprendre à rassembler les scénarios suivants pour tester votre application App Service en production :

* [Acheminer le trafic de production](app-service-web-test-in-production-get-start.md) vers votre application bêta
* [Instrumenter votre application](../application-insights/app-insights-web-track-usage.md) pour obtenir des mesures utiles
* Déployer votre application bêta et suivre les mesures de l’application en direct en continu
* Comparer des mesures entre l’application de production et l’application bêta pour voir comment les modifications de code se traduisent en résultats

## Éléments requis
* Un compte Azure
* Un compte [GitHub](https://github.com/)
* Visual Studio 2015 : vous pouvez télécharger l’[édition Community](https://www.visualstudio.com/fr-FR/products/visual-studio-express-vs.aspx)
* Git Shell (installé avec [GitHub for Windows](https://windows.github.com/)) : cela permet d’exécuter des commandes PowerShell et Git dans la même session
* Dernières informations [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi)
* Compréhension élémentaire des concepts et outils suivants :
  * Déploiement de modèles [Azure Resource Manager](../resource-group-overview.md) (voir [Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-deploy-complex-application-predictably.md))
  * [Git](http://git-scm.com/documentation)
  * [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure :
> 
> * Vous pouvez [ouvrir un compte Azure gratuitement](/pricing/free-trial/) : vous obtenez alors des crédits dont vous pouvez vous servir pour tester les services Azure payants, et même quand ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Web Apps.
> * Vous pouvez [activer les avantages de votre abonnement Visual Studio](/pricing/member-offers/msdn-benefits-details/) : votre abonnement Visual Studio vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.
> 
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## Configurer votre application web de production
> [!NOTE]
> Le script utilisé dans ce didacticiel configure automatiquement la publication continue à partir de votre référentiel GitHub. Pour ce faire, vos informations d’identification GitHub doivent déjà être stockées dans Azure, sinon les scripts de déploiement échoueront lorsque vous tenterez de configurer les paramètres de contrôle de code source pour les applications web.
> 
> Pour stocker vos informations d’identification GitHub dans Azure, créez une application web dans le [portail Azure](https://portal.azure.com/) et [configurez le déploiement GitHub](app-service-continuous-deployment.md#Step7). Cette opération est unique.
> 
> 

Dans un scénario classique d’opérations de développement, vous disposez d’une application qui s’exécute dans Azure et vous souhaitez lui apporter des modifications par le biais de la publication continue. Dans ce scénario, vous allez déployer en production un modèle que vous avez développé et testé.

1. Créez votre branchement dans le référentiel [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Pour plus d’informations sur la création de votre branchement, consultez [Branchement dans un référentiel](https://help.github.com/articles/fork-a-repo/). Une fois votre branchement créé, il est visible dans votre navigateur.
   
   ![](./media/app-service-agile-software-development/production-1-private-repo.png)
2. Ouvrez une session Git Shell. Si vous n’avez pas encore Git Shell, installez [GitHub for Windows](https://windows.github.com/).
3. Créez un clone local de votre branchement en exécutant la commande suivante :
   
     git clone https://github.com/<your_fork>/ToDoApp.git
4. Lorsque le clone local est créé, accédez à *&lt;racine\_référentiel>*\\ARMTemplates, puis exécutez le script deploy.ps1 avec un suffixe unique, comme indiqué ci-dessous :
   
     .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>
5. Lorsque vous y êtes invité, tapez le nom d’utilisateur et le mot de passe souhaités pour l’accès à la base de données. N’oubliez pas vos informations d’identification à la base de données, car vous devrez les spécifier de nouveau lors de la mise à jour du groupe de ressources.
   
   Vous devez voir l’avancement de la configuration des différentes ressources Azure. Lorsque le déploiement est terminé, le script lance l’application dans le navigateur et émet un signal sonore convivial. ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)
6. De retour dans votre session Git Shell, exécutez :
   
     .\swap –Name ToDoApp<your_suffix>
   
   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)
7. Lorsque le script se termine, revenez en arrière pour accéder à l’adresse du serveur frontal (http://ToDoApp*&lt;your_suffix>*.azurewebsites.net/) afin d’afficher l’application qui s’exécute en production.
8. Connectez-vous au [portail Azure](https://portal.azure.com/) et observez ce qui est créé.
   
   Les deux applications web doivent figurer dans le même groupe de ressources, et le nom de l’une d’elles doit comporter le suffixe `Api`. Si vous examinez l’affichage de groupe de ressources, vous pouvez voir également la base de données et le serveur SQL, le plan App Service et les emplacements intermédiaires pour les applications web. Parcourez les différentes ressources et comparez-les à *&lt;racine\_référentiel>*\\ARMTemplates\\ProdAndStage.json pour voir comment elles sont configurées dans le modèle.
   
   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Vous avez configuré l’application de production. À présent, imaginons que vous receviez des commentaires indiquant que la facilité d’utilisation de l’application est médiocre. Vous décidez alors de mener l’enquête. Vous allez instrumenter votre application de sorte qu’elle vous fournisse des commentaires.

## Enquête : Instrumenter votre application cliente aux fins de surveillance/mesures
1. Ouvrez *&lt;racine\_référentiel>*\\src\\MultiChannelToDo.sln dans Visual Studio.
2. Restaurez tous les packages Nuget en cliquant avec le bouton droit sur la solution > **Gérer les packages NuGet pour la solution** > **Restaurer**.
3. Cliquez avec le bouton droit sur **MultiChannelToDo.Web** > **Ajouter Application Insights Telemetry** > **Configurer les paramètres** > Modifier le groupe de ressources en ToDoApp*&lt;votre\_suffixe>* > **Ajouter Application Insights au projet**.
4. Dans le portail Azure, ouvrez le panneau de la ressource Application Insights **MultiChannelToDo.Web**. Ensuite, dans la partie **Intégrité des applications**, cliquez sur **Apprendre à collecter les données de chargement de page de navigateur** > Copier le code.
5. Ajoutez le code d’instrumentation JS copié à *&lt;racine\_référentiel>*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml, juste avant la balise de fermeture `<heading>`. Il doit contenir la clé d’instrumentation unique de votre ressource Application Insights.
   
        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });
   
        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>
6. Envoyez des événements personnalisés à Application Insights pour les clics de souris en ajoutant le code suivant en bas du corps :
   
       <script>
           $(document.body).find("*").click(function(event) {
   
               appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
           });
       </script>
   
   Cet extrait de code JavaScript envoie un événement personnalisé à Application Insights chaque fois qu’un utilisateur clique dans l’application web.
7. Dans Git Shell, validez et envoyez vos modifications à votre branchement dans GitHub. Ensuite, attendez que les clients actualisent le navigateur.
   
       git add -A :/
       git commit -m "add AI configuration for client app"
       git push origin master
8. Échangez les modifications de l’application déployée en production :
   
     .\swap –Name ToDoApp<your_suffix>
9. Accédez à la ressource Application Insights que vous avez configurée. Cliquez sur Événements personnalisés.
   
   ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)
   
   Si vous ne voyez pas les mesures pour les événements personnalisés, attendez quelques minutes et cliquez sur **Actualiser**.

Supposons que vous voyiez un graphique comme ci-dessous :

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

Et la grille d’événements ci-dessous :

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

En fonction de votre code d’application ToDoApp, l’événement **BUTTON** correspond au bouton d’envoi et l’événement **INPUT** à la zone de texte. Jusqu’à présent, tout est logique. Cependant, il semble y avoir une grande quantité de clics, mais très peu de clics sur les tâches à effectuer (événements **LI**).

En vous appuyant sur ces informations, vous formulez votre hypothèse : certains utilisateurs ne savent pas bien quelle partie de l’interface utilisateur est interactive, et c’est parce que le curseur est stylisé pour la sélection de texte lorsqu’il survole les éléments de liste et leurs icônes.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Il peut s’agir d’un exemple fictif. Néanmoins, vous allez apporter une amélioration à votre application, puis effectuer un déploiement avec distribution d’une version d’évaluation pour obtenir des commentaires sur la facilité d’utilisation de la part des clients en direct.

### Instrumenter votre application serveur aux fins de surveillance/mesures
Il s’agit d’une digression étant donné que le scénario présenté dans ce didacticiel traite uniquement de l’application cliente. Toutefois, par souci d’exhaustivité, vous allez configurer l’application côté serveur.

1. Cliquez avec le bouton droit sur **MultiChannelToDo** > **Ajouter Application Insights Telemetry** > **Configurer les paramètres** > Modifier le groupe de ressources en ToDoApp*&lt;votre\_suffixe>* > **Ajouter Application Insights au projet**.
2. Dans Git Shell, validez et envoyez vos modifications à votre branchement dans GitHub. Ensuite, attendez que les clients actualisent le navigateur.
   
       git add -A :/
       git commit -m "add AI configuration for server app"
       git push origin master
3. Échangez les modifications de l’application déployée en production :
   
     .\swap –Name ToDoApp<your_suffix>

Et voilà !

## Enquête : Ajouter des balises spécifiques aux emplacements à vos mesures d’application cliente
Dans cette section, vous allez configurer différents emplacements de déploiement pour envoyer des données de télémétrie spécifiques aux emplacements à la même ressource Application Insights. De cette façon, vous pouvez comparer les données de télémétrie entre le trafic à partir de différents emplacements (environnements de déploiement) pour facilement voir l’effet des modifications de votre application. En même temps, vous pouvez séparer le trafic de production du reste, de manière à pouvoir continuer à surveiller votre application de production si nécessaire.

Dans la mesure où vous collectez des données sur le comportement des clients, vous allez [ajouter un initialiseur de télémétrie à votre code JavaScript](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer) dans index.cshtml. Si vous souhaitez tester les performances côté serveur, par exemple, vous pouvez également faire de même dans votre code serveur (voir [API Application Insights pour les événements et les mesures personnalisés](../application-insights/app-insights-api-custom-events-metrics.md).

1. Tout d’abord, ajoutez le code entre les deux commentaires `//` ci-dessous, dans le bloc JavaScript que vous avez ajouté à la balise `<heading>` précédemment.
   
        window.appInsights = appInsights;
   
        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code
   
        appInsights.trackPageView();
   
    Ce code d’initialiseur pousse l’objet `appInsights` à ajouter une propriété personnalisée appelée `Environment` à chaque élément de télémétrie qu’il envoie.
2. Ensuite, ajoutez cette propriété personnalisée en tant que [paramètre d’emplacement](web-sites-staged-publishing.md#AboutConfiguration) pour votre application web dans Azure. Pour ce faire, exécutez les commandes suivantes dans votre session Git Shell :
   
        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production
   
    Le fichier Web.config dans votre projet définit déjà le paramètre d’application `environment`. Avec ce paramètre, lorsque vous testez l’application localement, vos mesures sont marquées avec `VS Debugger`. Toutefois, lorsque vous envoyez vos modifications à Azure, Azure trouve et utilise le paramètre d’application `environment` dans la configuration de l’application web à la place, et vos mesures sont marquées avec `Production`.
3. Validez et envoyez vos modifications de code à votre branchement sur GitHub, puis attendez que vos utilisateurs utilisent la nouvelle application (nécessité d’actualiser le navigateur). Il faut environ 15 minutes pour que la nouvelle propriété s’affiche dans votre ressource Application Insights `MultiChannelToDo.Web`.
   
        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master
4. À présent, accédez de nouveau au panneau **Événements personnalisés** et filtrez les mesures sur `Environment=Production`. Vous devriez maintenant être en mesure de voir tous les nouveaux événements personnalisés dans l’emplacement de production avec ce filtre.
   
    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)
5. Cliquez sur le bouton **Favoris** pour enregistrer les paramètres Metrics Explorer actuels dans un dossier similaire à **Événements personnalisés : Production**. Vous pourrez facilement basculer entre cette vue et une vue d’emplacement de déploiement ultérieurement.
   
   > [!TIP]
   > Pour une analyse encore plus puissante, envisagez d’[intégrer votre ressource Application Insights avec Power BI](../application-insights/app-insights-export-power-bi.md).
   > 
   > 

### Ajouter des balises spécifiques aux emplacements à vos mesures d’application serveur
De nouveau, par souci d’exhaustivité, vous allez configurer l’application côté serveur. Contrairement à l’application cliente qui est instrumentée dans JavaScript, les balises spécifiques aux emplacements pour l’application serveur sont instrumentées avec le code .NET.

1. Ouvrez *&lt;racine\_référentiel>*\\src\\MultiChannelToDo\\Global.asax.cs. Ajoutez le bloc de code ci-dessous, juste avant l’accolade fermante d’espace de noms.
   
        namespace MultiChannelToDo
        {
                ...
   
                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }
2. Corrigez les erreurs de résolution de nom en ajoutant les instructions `using` ci-dessous au début du fichier :
   
        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;
3. Ajoutez le code ci-dessous au début de la méthode `Application_Start()` :
   
        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());
4. Validez et envoyez vos modifications de code à votre branchement sur GitHub, puis attendez que vos utilisateurs utilisent la nouvelle application (nécessité d’actualiser le navigateur). Il faut environ 15 minutes pour que la nouvelle propriété s’affiche dans votre ressource Application Insights `MultiChannelToDo`.
   
        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## Mise à jour : Configurer votre branche bêta
1. Ouvrez *&lt;racine\_référentiel>*\\ARMTemplates\\ProdAndStagetest.json et trouvez les ressources `appsettings` (recherchez `"name": "appsettings"`). Elles sont au nombre de 4, une pour chaque emplacement.
2. Pour chaque ressource `appsettings`, ajoutez un paramètre d’application `"environment": "[parameters('slotName')]"` à la fin du tableau `properties`. N’oubliez pas de terminer la ligne précédente par une virgule.
   
    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
   
    Vous venez d’ajouter le paramètre d’application `environment` à tous les emplacements dans le modèle.
3. Dans le même fichier, trouvez les ressources `slotconfignames` (recherchez `"name": "slotconfignames"`). Elles sont au nombre de 2, une pour chaque application.
4. Pour chaque ressource `slotconfignames`, ajoutez `"environment"` à la fin du tableau `appSettingNames`. N’oubliez pas de terminer la ligne précédente par une virgule.
   
    Vous venez de faire en sorte que le paramètre d’application `environment` reste dans son emplacement de déploiement respectif pour les deux applications.
5. Dans votre session Git Shell, exécutez les commandes suivantes avec le même suffixe de groupe de ressources que celui utilisé auparavant.
   
        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta
6. Lorsque vous y êtes invité, spécifiez les mêmes informations d’identification à la base de données SQL que précédemment. Ensuite, lorsque vous êtes invité à mettre à jour le groupe de ressources, tapez `Y`, puis `ENTER`.
   
    Une fois que le script se termine, toutes vos ressources dans le groupe de ressources d’origine sont conservées, mais un nouvel emplacement nommé « beta » est créé dans celui-ci, avec la même configuration que l’emplacement intermédiaire créé au début.
   
   > [!NOTE]
   > Cette méthode de création de différents environnements de déploiement est différente de la méthode présentée dans [Développement logiciel agile avec Azure App Service](app-service-agile-software-development.md). Ici, vous créez des environnements de déploiement avec des emplacements de déploiement, alors qu’avec l’autre méthode, vous créez des environnements de déploiement avec des groupes de ressources. La gestion des environnements de déploiement avec les groupes de ressources vous permet de maintenir l’environnement de production hors d’atteinte pour les développeurs. Toutefois, il n’est pas facile de réaliser des tests en production, ce que vous pouvez facilement faire avec les emplacements.
   > 
   > 

Si vous le souhaitez, vous pouvez également créer une application alpha en exécutant la commande suivante :

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Pour ce didacticiel, vous continuerez simplement à utiliser votre application bêta.

## Mise à jour : Envoyer vos mises à jour à l’application bêta
Revenons à votre application que vous souhaitez améliorer.

1. Vérifiez que vous êtes à présent dans votre branche bêta
   
        git checkout beta
2. Dans *&lt;racine\_référentiel>*\\src\\MultiChannelToDo.Web\\app\\Index.cshtml, trouvez la balise `<li>` et ajoutez l’attribut `style="cursor:pointer"`, comme indiqué ci-dessous.
   
    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)
3. Validez et envoyez vers Azure.
4. Vérifiez que la modification est désormais reflétée dans l’emplacement bêta en accédant à http://todoapp*&lt;your_suffix>*-beta.azurewebsites.net/. Si vous ne voyez pas encore la modification, actualisez votre navigateur pour obtenir le nouveau code JavaScript.
   
    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Maintenant que votre modification est exécutée dans l’emplacement bêta, vous êtes prêt à effectuer un déploiement avec distribution d’une version d’évaluation.

## Validation : Acheminer le trafic vers l’application bêta
Dans cette section, vous allez acheminer le trafic vers l’application bêta. Par souci de clarté de démonstration, vous allez acheminer une partie significative du trafic utilisateur vers celle-ci. En réalité, la quantité de trafic que vous souhaitez acheminer dépendra de votre situation particulière. Par exemple, si votre site est à l’échelle de microsoft.com, vous aurez peut-être besoin de moins d’un pour cent de votre trafic total pour obtenir des données utiles.

1. Dans votre session Git Shell, exécutez les commandes suivantes pour acheminer la moitié du trafic de production vers l’emplacement bêta :
   
        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule
   
   La propriété `ReroutePercentage=50` spécifie que 50 % du trafic de production sera routé vers l’URL de l’application bêta (spécifiée par la propriété `ActionHostName`).
2. Accédez maintenant à http://ToDoApp*&lt;your_suffix>*.azurewebsites.net. 50 % du trafic doit maintenant être redirigé vers l’emplacement bêta.
3. Dans votre ressource Application Insights, filtrez les mesures sur environnement="beta".
   
   > [!NOTE]
   > Si vous enregistrez cette vue filtrée comme un autre favori, vous pouvez facilement retourner les vues Metrics Explorer entre les vues de production et bêta.
   > 
   > 

Supposons que dans Application Insights vous voyiez quelque chose de similaire à ce qui suit :

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Non seulement cela indique qu’il y a beaucoup plus de clics sur les balises `<li>`, mais il semble qu’il y a un nombre important de clics sur les balises `<li>`. Vous pouvez en conclure que les utilisateurs ont découvert que les nouvelles balises `<li>` sont interactives et qu’ils effacent maintenant toutes leurs tâches terminées précédemment dans l’application.

En vous appuyant sur les données de votre déploiement avec distribution d’une version d’évaluation, vous décidez que votre nouvelle interface utilisateur est prête pour la production.

## Mise en ligne : Mettre votre nouveau code en production
Vous êtes maintenant prêt à mettre votre mise à jour en production. Ce qui est super, c’est que vous savez que votre mise à jour a déjà été validée *avant* d’être envoyée en production. Vous pouvez donc la déployer en toute confiance. Étant donné que vous avez effectué une mise à jour de l’application cliente AngularJS, vous avez uniquement validé le code côté client. Si vous deviez apporter des modifications à l’application API web principale, vous pourriez valider vos modifications de la même façon et facilement.

1. Dans Git Shell, supprimez la règle de routage du trafic en exécutant la commande suivante :
   
        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()
2. Exécutez les commandes Git :
   
        git checkout master
        git pull origin master
        git merge beta
        git push origin master
3. Attendez quelques minutes que le nouveau code soit déployé dans l’emplacement intermédiaire, puis lancez http://ToDoApp*&lt;your_suffix>*-staging.azurewebsites.net pour vérifier que la nouvelle mise à jour est initialisée dans l’emplacement intermédiaire. N’oubliez pas que la branche principale de votre branchement est liée à l’emplacement intermédiaire de votre application.
4. À présent, échangez l’emplacement intermédiaire en production.
   
        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## Résumé
Azure App Service facilite le test en production des applications destinées aux clients pour les PME, possibilité qui historiquement était réservée aux grandes entreprises. Nous espérons que ce didacticiel vous a apporté les connaissances nécessaires pour rassembler App Service et Application Insights, afin de rendre possible le déploiement avec distribution d’une version d’évaluation, voire d’autres scénarios de test en production, dans votre monde d’opérations de développement.

## Autres ressources
* [Développement logiciel agile avec Azure App Service](app-service-agile-software-development.md)
* [Configurer des environnements intermédiaires pour les applications web dans Azure App Service](web-sites-staged-publishing.md)
* [Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-deploy-complex-application-predictably.md)
* [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md)
* [JSONLint - Validateur JSON](http://jsonlint.com/)
* [Création de branches Git – Branchement et fusion basiques](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* [Azure PowerShell](../powershell-install-configure.md)
* [Projet Wiki Kudu](https://github.com/projectkudu/kudu/wiki)

<!---HONumber=AcomDC_0803_2016-->