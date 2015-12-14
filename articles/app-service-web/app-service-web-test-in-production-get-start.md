<properties
	pageTitle="Prise en main de la fonction de test en production pour les applications web"
	description="En savoir plus sur la fonction de test en production dans Azure App Service Web Apps."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="cephalin"/>

# Prise en main de la fonction de test en production pour les applications web

Le test en production, ou test en direct de votre application web à l’aide du trafic client en direct, est une stratégie de test que les développeurs d’applications intègrent de plus en plus à leur méthodologie de [développement agile](https://en.wikipedia.org/wiki/Agile_software_development). Cette fonction vous permet de tester la qualité de vos applications avec le trafic des utilisateurs en direct dans votre environnement de production, par opposition aux données synthétisées dans un environnement de test. En exposant votre nouvelle application auprès d’utilisateurs réels, vous pouvez recevoir des informations sur les problèmes réels qu’elle sera susceptible de rencontrer après son déploiement. Vous pouvez vérifier les fonctionnalités, les performances et la valeur de vos mises à jour de l’application par rapport au volume, à la vitesse et à la diversité du trafic utilisateur réel, ce qui est impossible dans un environnement de test.

## Routage du trafic dans App Service Web Apps

Grâce à la fonctionnalité de routage du trafic dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), vous pouvez diriger une partie du trafic utilisateur direct vers un ou plusieurs [emplacements de déploiement](web-sites-staged-publishing.md), puis analyser votre application avec [Azure Application Insights](/services/application-insights/), [Azure HDInsight](/services/hdinsight/) ou un outil tiers comme [New Relic](/marketplace/partners/newrelic/newrelic/) pour valider vos modifications. Par exemple, vous pouvez implémenter les scénarios suivants avec App Service :

- Découvrir des bogues fonctionnels ou identifier les goulots d’étranglement de performances dans vos mises à jour avant le déploiement à l’échelle du site
- Exécuter des « versions de test contrôlées » de vos modifications en mesurant la facilité d’utilisation sur l’application bêta
- Développer progressivement une nouvelle mise à jour et revenir normalement à la version actuelle si une erreur se produit 
- Optimiser les résultats commerciaux de votre application en exécutant des [tests A/B](https://en.wikipedia.org/wiki/A/B_testing) ou des [tests multivariables](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) dans plusieurs emplacements de déploiement

### Configuration requise pour utiliser le routage du trafic dans Web Apps

- Votre application web doit s’exécuter au niveau **Standard** ou **Premium**, requis pour les emplacements de déploiement multiples.
- Pour fonctionner correctement, le routage du trafic requiert l’activation des cookies dans le navigateur des utilisateurs. Le routage du trafic utilise des cookies pour épingler un navigateur client à un emplacement de déploiement pendant la durée de la session de client.
- Le routage du trafic prend en charge des scénarios avancés de test en production via les applets de commande Azure PowerShell.

## Routage d’un segment de trafic vers un emplacement de déploiement

Au niveau de base, dans chaque scénario de test en production, vous acheminez un pourcentage prédéfini de votre trafic en direct vers un emplacement de déploiement hors production. Pour ce faire, procédez comme suit :

>[AZURE.NOTE]Cette procédure suppose que vous disposez déjà d’un [emplacement de déploiement hors production](web-sites-staged-publishing.md) et que le contenu d’application web souhaité est déjà [déployé](web-sites-publish-source-control.md) sur celui-ci.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le panneau de votre application web, cliquez sur **Paramètres** > **Routage du trafic**. ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Sélectionnez l’emplacement vers lequel vous souhaitez acheminer le trafic, ainsi que le pourcentage de trafic total souhaité, puis cliquez sur **Enregistrer**.

	![](./media/app-service-web-test-in-production/02-select-slot.png)

4. Accédez au panneau de l’emplacement de déploiement. Vous devriez maintenant voir le trafic en direct en cours de routage vers celui-ci.

	![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Une fois le routage du trafic configuré, le pourcentage de clients spécifié sera routé de manière aléatoire vers votre emplacement hors production. Toutefois, il est important de noter qu’une fois qu’un client est automatiquement acheminé vers un emplacement spécifique, il est « épinglé » à cet emplacement pendant la durée de cette session de client. Cette opération est effectuée à l’aide d’un cookie pour épingler la session utilisateur. Si vous inspectez les demandes HTTP, vous trouverez un cookie `TipMix` dans chaque demande ultérieure.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## Forcer les demandes des clients vers un emplacement spécifique

Outre le routage automatique du trafic, App Service peut acheminer les demandes vers un emplacement spécifique. Cela est utile lorsque vous souhaitez que vos utilisateurs puissent choisir d’accepter ou de refuser votre application bêta. Pour ce faire, vous utilisez le paramètre de requête `x-ms-routing-name`.

Pour rediriger les utilisateurs vers un emplacement spécifique à l’aide de `x-ms-routing-name`, vous devez vous assurer que l’emplacement est déjà ajouté à la liste de routage du trafic. Étant donné que vous souhaitez router vers un emplacement explicitement, le pourcentage de routage réel que vous définissez n’a pas d’importance. Si vous le souhaitez, vous pouvez créer un « lien bêta » sur lequel les utilisateurs peuvent cliquer pour accéder à l’application bêta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### Utilisateurs refusant l’application bêta

Par exemple, pour permettre aux utilisateurs de refuser votre application bêta, vous pouvez placer ce lien sur votre page web :

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

La chaîne `x-ms-routing-name=self` spécifie l’emplacement de production. Une fois que le navigateur client accède au lien, non seulement il est redirigé vers l’emplacement de production, mais chaque demande ultérieure contiendra le cookie `x-ms-routing-name=self` qui épingle la session à l’emplacement de production.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### Utilisateurs acceptant l’application bêta

Pour permettre aux utilisateurs d’accepter votre application bêta, définissez le même paramètre de requête pour le nom de l’emplacement hors production, par exemple :

		<webappname>.azurewebsites.net/?x-ms-routing-name=staging

## Autres ressources ##

-   [Configurer des environnements intermédiaires pour les applications web dans Azure App Service](web-sites-staged-publishing.md)
-	[Déployer une application complexe de manière prévisible dans Microsoft Azure](app-service-deploy-complex-application-predictably.md)
-   [Développement logiciel agile avec Azure App Service](app-service-agile-software-development.md)
-	[Utiliser efficacement les environnements DevOps pour vos applications web](app-service-web-staged-publishing-realworld-scenarios.md)

<!---HONumber=AcomDC_1203_2015-->