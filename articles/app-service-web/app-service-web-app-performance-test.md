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
   ms.date="05/25/2016"
   ms.author="estfan; manasma; ahomer"/>

# test de performance de votre application web Azure sous charge

Vérifiez le fonctionnement de votre application web avant de lancer ou de déployer des mises à jour en production. Ainsi, vous pouvez mieux vous rendre compte si votre application est prête à être publiée. Il est probable que votre application puisse gérer le trafic pendant les pics d’utilisation ou à votre prochaine publication marketing.

Pendant la présentation publique, vous pouvez effectuer un test de performance de votre application dans le portail Azure. Ces tests simulent la charge utilisateur sur votre application sur une période spécifique et mesurent la réponse de votre application. Par exemple, les résultats des tests affichent la vitesse à laquelle votre application répond à un nombre spécifique d’utilisateurs. Ils indiquent également le nombre de demandes ayant échoué, qui peuvent être symptomatiques des problèmes de votre application.

![Rechercher des problèmes de performances dans votre application web](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## Avant de commencer

* Vous aurez besoin d’un [abonnement Azure](https://account.windowsazure.com/subscriptions) si vous n’en avez pas encore. Apprenez comment [ouvrir gratuitement un compte Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).

* Vous allez associer un compte [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) pour conserver l’historique des performances de votre test. Un compte approprié est automatiquement créé quand vous configurez votre test de performances. Vous pouvez également créer un compte ou utiliser un compte existant dont vous êtes propriétaire.

* Déployez votre application dans un environnement de test. Demandez votre application d’utiliser un plan de Service d’application autre que le plan utilisé en production. De cette façon, vous n’affectez aucun client existant et ne ralentissez pas votre application en production.

## Configurer et exécuter votre test de performance

0.  Connectez-vous au [portail Azure](https://portal.azure.com). Pour utiliser un compte Visual Studio Team Services que vous possédez, connectez-vous en tant que propriétaire du compte.

0.  Accédez à votre application web.

    ![Accéder à Parcourir tout, Applications web , votre application web](./media/app-service-web-app-performance-test/azure-np-web-apps.png)

0.  Accédez à **Test de performance**.

    ![Accéder à Outils, Test de performance](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
 
0. Vous allez maintenant associer un compte [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) pour conserver l’historique des performances de votre test.

    Si vous possédez un compte Team Services, sélectionnez-le. Dans le cas contraire, créez un nouveau compte.

    ![Sélectionner le compte Team Services existant ou créer un nouveau compte](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)

0.  Créer votre test de performance. Définir les détails et exécuter le test.

Vous pouvez regarder les résultats en temps réel pendant l’exécution du test.

Par exemple, supposons que nous avons une application qui a distribué des coupons d’achat lors de la saison des fêtes. Cet événement a duré de 15 minutes avec une charge maximale de 100 clients simultanés. Nous souhaitons doubler le nombre de clients cette année. Nous souhaitons également améliorer la satisfaction du client en ramenant le délai de chargement de page de 5 à 2 secondes. Ainsi, nous allons tester les performances de notre application mise à jour avec 250 utilisateurs pendant 15 minutes.

Nous allons simuler la charge sur notre application en générant des utilisateurs virtuels (clients) qui visitent notre site web en même temps. Cela permet de visualiser le nombre de requêtes ayant échoué ou répondant lentement.

  ![Créer, configurer et exécuter votre test de performance](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

   *  L’URL par défaut de votre application web est ajoutée automatiquement. Vous pouvez modifier l’URL pour tester d’autres pages (demandes HTTP GET uniquement).

   *  Pour simuler des conditions locales et de réduire le temps de latence, sélectionnez l’emplacement le plus proche de vos utilisateurs pour générer la charge.

  Voici le test en cours. Au cours de la première minute, notre page se charge plus lentement que ce que nous souhaitons.

  ![Test de performance en cours avec des données en temps réel](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)

  Une fois le test terminé, nous apprenons que la page se charge plus rapidement après la première minute. Cela permet d’identifier l’endroit où nous pouvons commencer la résolution des problèmes.

  ![Le test de performance terminé affiche les résultats, notamment les demandes ayant échoué](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## Tester plusieurs URL

Vous pouvez également exécuter des tests de performance comprenant plusieurs URL qui constituent un scénario utilisateur de bout en bout en chargeant un fichier de test web Visual Studio. Voici deux méthodes permettant de créer un fichier de test web Visual Studio :

* [Capturer le trafic à l’aide de Fiddler et l’exporter dans un fichier de test web Visual Studio](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Créer un fichier de test de charge dans Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Pour charger et exécuter un fichier de test web Visual Studio :
 
0. Suivez les étapes ci-dessus pour ouvrir le panneau **Nouveau test de performance**. Dans ce panneau, choisissez l’option CONFIGURE TEST USING (CONFIGURER LE TEST AVEC) pour ouvrir le panneau **Configure test using** (Configurer le test avec).  

    ![Ouverture du panneau Configure load testing (Configurer les tests de charge)](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)

0. Vérifiez que le type de test est défini sur **Visual Studio Web Test** (Test web Visual Studio) et sélectionnez votre fichier d’archive HTTP. Utilisez l’icône de dossier pour ouvrir la boîte de dialogue de sélection de fichier.

    ![Chargement d’un fichier de test web Visual Studio avec plusieurs URL](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)

    Une fois le fichier chargé, la liste des URL à tester apparaît dans la section DÉTAILS DES URL.
 
0. Spécifiez la charge utilisateur et la durée du test, puis choisissez **Exécuter le test**.

    ![Sélection de la charge utilisateur et de la durée](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)

    Une fois le test terminé, les résultats apparaissent dans deux volets. Le volet gauche affiche les informations de performance sous la forme d’une série de graphiques.

    ![Volet des résultats des performances](./media/app-service-web-app-performance-test/multiple-01a-results.png)

    Le volet droit affiche la liste des demandes ayant échoué, avec le type d’erreur et le nombre d’occurrences.

    ![Volet des échecs de demande](./media/app-service-web-app-performance-test/multiple-01b-results.png)

0. Réexécutez le test en choisissant l’icône **Réexécuter** en haut du volet droit.

    ![Réexécution du test](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

##  Questions et réponses

#### Q : Existe-t-il une limite de la durée d’exécution d’un test ? 

**R** : Oui, vous pouvez exécuter votre test pendant une heure dans le portail Azure.

#### Q : Combien de temps ai-je pour exécuter des tests de performances ? 

**R** : Après la version d’évaluation, vous pouvez obtenir 20 000 minutes d’utilisateur virtuel (VUM) gratuites chaque mois avec votre compte Visual Studio Team Services. Un VUM correspond au nombre d’utilisateurs virtuels multiplié par le nombre de minutes dans votre test. Si vos besoins dépassent la limite gratuite, vous pouvez acheter du temps supplémentaire et payer uniquement ce que vous utilisez.

#### Q : Où puis-je vérifier combien de VUM ont été utilisées jusqu’ici ?

**R** : Vous pouvez vérifier ce montant dans le portail Azure.

![Accéder à votre compte Team Services](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![Vérifier les VUM utilisées](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### Q : Quelle est l’option par défaut ? Mes tests sont-ils affectés ?

**R** : L’option par défaut pour les tests de charge de performance est un test manuel, ce qui était déjà le cas avant que l’option de test de plusieurs URL ne soit ajoutée au portail. Vos tests continuent d’utiliser l’URL configurée et fonctionnent comme avant.

#### Q : Quelles sont les fonctionnalités non prises en charge dans le fichier de test web Visual Studio ?

**R** : Actuellement, cette fonctionnalité ne prend pas en charge les plug-ins de test web, les sources de données et les règles d’extraction. Vous devez modifier votre fichier de test web pour les supprimer. Nous espérons ajouter la prise en charge de ces fonctionnalités dans les futures mises à jour.

#### Q : Prend-elle en charge d’autres formats de fichier de test web ?
  
**R** : Actuellement, seuls les fichiers de format test web Visual Studio sont pris en charge. N’hésitez pas à nous contacter si vous souhaitez que d’autres formats de fichier soient pris en charge. Envoyez-nous un e-mail à l’adresse [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com).

#### Q : Que puis-je faire d’autre avec un compte Visual Studio Team Services ?

**R** : Pour votre nouveau compte, accédez à ```https://{accountname}.visualstudio.com```. Partager votre code, générer, tester, effectuer le suivi du travail et distribuer les logiciels – dans le cloud à l’aide de n’importe quel outil ou un langage. Pour en savoir plus sur la façon dont les fonctions et les services [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) permettent à votre équipe de collaborer plus facilement et de se déployer en continu.

## Voir aussi

* [Run simple cloud performance tests (Exécuter des tests de performance cloud simples)](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Run Apache Jmeter performance tests](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test) (Exécuter des tests de performance Apache Jmeter)
* [Record and replay cloud-based load tests](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests) (Enregistrer et lire des tests de charge basés sur le cloud)
* [Performance test your app in the cloud](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) (Tester les performances de votre application dans le cloud)

<!---HONumber=AcomDC_0525_2016-->