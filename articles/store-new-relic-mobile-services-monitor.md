<properties urlDisplayName="Use New Relic to monitor Mobile Services" pageTitle="Stockage des scripts serveur dans le contrôle du code source - Azure Mobile Services" metaKeywords="" description="Découvrez comment utiliser le module New Relic pour analyser votre service mobile." metaCanonical="" disqusComments="1" umbracoNaviHide="0" documentationCenter="Mobile" title="Use New Relic to monitor Mobile Services" authors="new relic" manager="carolz" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/25/2014" ms.author="stepsic" />

# Utilisation de New Relic pour la surveillance de Mobile Services

Cette rubrique montre comment configurer le module tiers New Relic, qui fonctionnera avec Azure Mobile Services pour offrir une surveillance améliorée de votre service mobile. 

Ce didacticiel vous accompagne tout au long des étapes suivantes :

1. [Inscription à New Relic à l'aide de l'Azure Store].
2. [Installation du module New Relic].
3. [Activation de New Relic Developer Analytics pour le service mobile].
4. [Surveillance du service mobile dans le tableau de bord New Relic].

Pour suivre ce didacticiel, vous devez avoir créé un service mobile au moyen du didacticiel [Prise en main de Mobile Services] ou [Prise en main des données].

##<a name="sign-up"></a>Inscription à New Relic à l'aide de l'Azure Store

La première étape consiste à acheter le service New Relic. Ce didacticiel vous montre comment acheter ce service dans l'Azure Store. Mobile Services prend en charge les abonnements New Relic achetés en dehors de l'Azure Store.

1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com).

2. Dans le volet inférieur du portail de gestion, cliquez sur **Nouveau**.

3. Cliquez sur **Store**.

4. Dans la boîte de dialogue **Choisir un module complémentaire**, sélectionnez **New Relic**, puis cliquez sur **Suivant**.

5. Dans la boîte de dialogue **Personnaliser un module complémentaire**, sélectionnez le plan New Relic de votre choix.

7. Entrez un nom pour le service New Relic, qui apparaîtra dans vos paramètres Azure ou utilisez la valeur par défaut **NewRelic**. Le nom doit être unique dans la liste des éléments de l'Azure Store auxquels vous êtes abonné.

8. Choisissez une valeur pour la région ; par exemple **Bretagne**.

9. Cliquez sur **Suivant**.

10. Dans la boîte de dialogue **Revoir l'achat**, passez en revue le plan et les informations tarifaires, ainsi que les conditions juridiques. Si vous acceptez ces dernières, cliquez sur **Acheter**.

11. Une fois que vous avez cliqué sur **Acheter**, votre compte New Relic commence le processus de création. Vous pouvez surveiller le statut dans le portail de gestion Azure.

##<a name="install-module"></a>Installation du module New Relic

Après votre inscription au service New Relic, vous devez installer le module Node.js New Relic sur votre service mobile. Le contrôle de code source doit être activé pour que votre service mobile puisse télécharger ce module.

1. Si ce n'est pas déjà fait, suivez les étapes indiquées dans le didacticiel [Stockage de scripts serveur dans un contrôle de code source] pour activer le contrôle de code source pour votre service mobile, cloner le référentiel et installer <a href="http://nodejs.org/" target="_blank">Node Package Manager (NPM)</a>.

2. Accédez au dossier `.\service` de votre référentiel Git local, puis, à partir de l'invite de commandes, exécutez la commande suivante :

		npm install newrelic

	NPM installe le [module New Relic][newrelic] dans le sous-répertoire `\newrelic`. 

3. Ouvrez un outil en ligne de commande Git, tel que **GitBash** (Windows) ou **Bash** (Unix Shell), puis tapez la commande suivante dans l'invite de commandes Git : 

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	Cela charge le nouveau module `newrelic` module vers votre service mobile. 

Ensuite, vous allez activer la surveillance New Relic de votre service mobile dans le [portail de gestion][Azure Management Portal]. 

##<a name="enable-service"></a>Activation de New Relic Developer Analytics pour le service mobile

1. Dans le [portail de gestion][Azure Management Portal], sélectionnez votre service mobile, puis cliquez sur l'onglet **Configurer**.

	![][0]

2. Faites défiler jusqu'à **Analyse développeur** et effectuez l'une des actions suivantes, selon le mode d'achat de votre abonnement New Relic :

	+ Achat dans l'Azure Store :

		Cliquez sur **Module complémentaire**, sélectionnez le module New Relic dans **Choisir un module complémentaire**, puis cliquez sur **Enregistrer**.

		![][1]

	+ Achat directement auprès de New Relic : 

		Cliquez sur **Personnalisé**, sélectionnez New Relic dans**Fournisseur**, entrez votre clé, puis cliquez sur **Enregistrer**.

		![][2]

		Vous pouvez obtenir la clé dans votre tableau de bord New Relic.

3. Une fois l'enregistrement terminé, vous verrez une nouvelle valeur dans **Paramètres de l'application** :

	![][3] 

##<a name="monitor"></a>Surveillance du service mobile dans le tableau de bord New Relic

1. Exécutez votre application cliente pour générer les demandes de lecture, de création, de mise à jour et de suppression sur votre service mobile.

2. Attendez quelques minutes que les données soient traitées, puis accédez au tableau de bord New Relic.

	Une fois votre abonnement New Relic acheté en tant que module complémentaire, sélectionnez-le dans le [portail de gestion][Azure Management Portal], puis cliquez sur **Gérer**.

3. Dans New Relic, cliquez sur **Applications**, puis sur votre service mobile.

	![][4]

4. Cliquez sur **Web transactions** pour afficher les demandes récentes que vous avez effectuées sur votre service mobile :

	![][5]

##<a name="next-steps"> </a>Étapes suivantes

+ Pour optimiser les performances de votre application mobile **iOS**/**Android**, consultez [New Relic Mobile].
+ Pour obtenir des informations de tarification, consultez la [page New Relic dans l'Azure Store].
+ Pour plus d'informations sur l'utilisation de New Relic, consultez la page [Vue d'ensemble des applications] dans la documentation de New Relic. 

<!-- Anchors. -->
[Inscription à New Relic à l'aide de l'Azure Store]: #sign-up
[Installation du module New Relic]: #install-module
[Activation de New Relic Developer Analytics pour le service mobile]: #enable-service
[Surveillance du service mobile dans le tableau de bord New Relic]: #monitor
[Étapes suivantes]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[Contrôle de code source]: http://msdn.microsoft.com/fr-fr/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Utilisation des scripts serveur dans Mobile Services]: /fr-fr/develop/mobile/how-to-guides/work-with-server-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Documentation d'API Node.js : Modules]: http://nodejs.org/api/modules.html
[Stockage de scripts serveur dans un contrôle de code source]: /fr-fr/develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[Page New Relic dans l'Azure Store]: /fr-fr/gallery/store/new-relic/new-relic/
[Vue d'ensemble des applications]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring


<!--HONumber=35.1-->
