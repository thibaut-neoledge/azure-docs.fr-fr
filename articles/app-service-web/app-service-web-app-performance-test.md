<properties
   pageTitle="Tester les performances de votre application web Azure | Microsoft Azure"
   description="Exécuter les tests de performances d’application web Azure pour contrôler de quelle façon votre application gère la charge utilisateur. Mesurer le temps de réponse et rechercher les défaillances pouvant indiquer des problèmes."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/11/2015"
   ms.author="estfan; manasma"/>

# test de performance de votre application web Azure sous charge

Vérifiez le fonctionnement de votre application web avant de lancer ou de déployer des mises à jour en production. Ainsi, vous pouvez mieux vous rendre compte si votre application est prête à être publiée. Il est probable que votre application puisse gérer le trafic pendant les pics d’utilisation ou à votre prochaine publication marketing.

Pendant la présentation publique, vous pouvez effectuer un test de performance de votre application dans la version préliminaire du portail Azure. Ces tests simulent la charge utilisateur sur votre application sur une période spécifique et mesurent la réponse de votre application. Par exemple, les résultats des tests affichent la vitesse à laquelle votre application répond à un nombre spécifique d’utilisateurs. Ils indiquent également le nombre de demandes ayant échoué, qui peuvent être symptomatiques des problèmes de votre application.

![Rechercher des problèmes de performances dans votre application web][TestOverview]

## Avant de commencer

*	Vous aurez besoin d’un [abonnement Azure][AzureSubscription] si vous n’en avez pas encore. Apprenez comment [ouvrir gratuitement un compte Azure][AzureFreeTrial].

*	Vous allez associer un compte [Visual Studio Online (VSO)][WhatIsVSO] pour conserver l’historique des performances de votre test. Créez votre nouveau compte lorsque vous configurez votre test de performance, ou utilisez un compte existant si vous êtes le propriétaire du compte. [Que puis-je faire d’autre avec un compte Visual Studio Online ?](#VSOAccount)

*	Déployez votre application dans un environnement de test. Demandez votre application d’utiliser un plan de Service d’application autre que le plan utilisé en production. De cette façon, vous n’affectez aucun client existant et ne ralentissez pas votre application en production.

## Configurer et exécuter votre test de performance

0.	Connectez-vous à la [version préliminaire du portail Azure][AzurePortal]. Pour utiliser un compte Visual Studio Online que vous possédez, connectez-vous en tant que propriétaire du compte.

0.	Accédez à votre application web.

	![Accéder à Parcourir tout, Applications web , votre application web][WebApp]

0.	Accédez à **Test de performance**.

	![Accéder à Outils, Test de performance][ExpandedTools]
 
0.	Maintenant vous allez associer un compte [Visual Studio Online (VSO)][WhatIsVSO] pour conserver l’historique des performances de votre test.

	Si vous avez un compte Visual Studio Online à utiliser, sélectionnez-le. Dans le cas contraire, créez un nouveau compte.

	![Sélectionner le compte de VSO existant ou créer un nouveau compte][ExistingNewVSOAccount]

0.	Créer votre test de performance. Définir les détails et exécuter le test. Vous pouvez regarder les résultats en temps réel pendant l’exécution du test.

	Par exemple, supposons que nous avons une application qui a distribué des coupons d’achat lors de la saison des fêtes. Cet événement a duré de 15 minutes avec une charge maximale de 100 clients simultanés. Nous souhaitons doubler le nombre de clients cette année. Nous souhaitons également améliorer la satisfaction du client en ramenant le délai de chargement de page de 5 à 2 secondes. Ainsi, nous allons tester les performances de notre application mise à jour avec 250 utilisateurs pendant 15 minutes.

	Nous allons simuler la charge sur notre application en générant des utilisateurs virtuels (clients) qui visitent notre site web en même temps. Cela permet de visualiser le nombre de requêtes ayant échoué ou répondant lentement.

	![Créer, configurer et exécuter votre test de performance][NewTest]

	 *	L’URL par défaut de votre application web est ajoutée automatiquement. Vous pouvez modifier l’URL pour tester d’autres pages (demandes HTTP GET uniquement).

	 *	Pour simuler des conditions locales et de réduire le temps de latence, sélectionnez l’emplacement le plus proche de vos utilisateurs pour générer la charge.

	Voici le test en cours. Au cours de la première minute, notre page se charge plus lentement que ce que nous souhaitons.

	![Test de performance en cours avec des données en temps réel][TestRunning]

	Une fois le test terminé, nous apprenons que la page se charge plus rapidement après la première minute. Cela permet d’identifier l’endroit où nous pouvons commencer la résolution des problèmes.

	![Le test de performance terminé affiche les résultats, notamment les demandes ayant échoué][TestDone]
	
N’hésitez pas à nous faire part de vos commentaires. Si vous avez des questions ou des problèmes, veuillez nous contacter au <vsoloadtest@microsoft.com>

##	Questions et réponses

####Q : Existe-t-il une limite de la durée d’exécution d’un test ? 

R : Oui, vous pouvez exécuter votre test pendant une heure sur la version préliminaire du portail Azure.

####Q : Combien de temps ai-je pour exécuter des tests de performances ? 

R : Après la version d’évaluation, vous pouvez obtenir 20 000 minutes d’utilisateur virtuel (VUM) gratuites chaque mois avec votre compte Visual Studio Online. Un VUM correspond au nombre d’utilisateurs virtuels multiplié par le nombre de minutes dans votre test. Si vos besoins dépassent la limite gratuite, vous pouvez acheter du temps supplémentaire et payer uniquement ce que vous utilisez.

####Q : Où puis-je vérifier combien de VUM ont été utilisées jusqu’ici ?

R : Vous pouvez vérifier ce montant dans la version préliminaire du portail Azure.

![Accéder à votre compte Visual Studio Online][VSOAccount]

![Vérifier les VUM utilisées][CheckTestTime]

<a name="VSOAccount"></a>
####Q : Que puis-je faire d’autre avec un compte Visual Studio Online ?

R : Pour votre nouveau compte, accédez à ```https://{accountname}.visualstudio.com```. Partager votre code, générer, tester, effectuer le suivi du travail et distribuer les logiciels – dans le cloud à l’aide de n’importe quel outil ou un langage. Pour en savoir plus sur la façon dont les fonctions et les services [Visual Studio Online][WhatIsVSO] permettent à votre équipe de collaborer plus facilement et de se déployer en continu.

<!--Image references-->
[WebApp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png
[TestOverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png
[ExpandedTools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png
[ExistingNewVSOAccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png
[NewTest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png
[TestRunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png
[TestDone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png
[VSOAccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png
[CheckTestTime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png

<!--Reference links -->
[AzurePortal]: https://portal.azure.com
[AzureSubscription]: https://account.windowsazure.com/subscriptions
[AzureFreeTrial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[WhatIsVSO]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs

<!---HONumber=Sept15_HO3-->