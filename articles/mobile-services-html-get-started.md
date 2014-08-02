<properties pageTitle="Get Started with Azure Mobile Services for HTML 5 apps" metaKeywords="" description="Follow this tutorial to get started using Azure Mobile Services for HTML development. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main de Mobile Services
================================

[Windows Store](/en-us/documentation/articles/mobile-services-windows-store-get-started "Windows Store") [Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-get-started "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-ios-get-started "iOS") [Android](/en-us/documentation/articles/mobile-services-android-get-started "Android") [HTML](/en-us/documentation/articles/mobile-services-html-get-started "HTML") [Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-mobile-services-ios-get-started "Xamarin.iOS") [Xamarin.Android](/en-us/documentation/articles/partner-xamarin-mobile-services-android-get-started "Xamarin.Android") [Sencha](/en-us/documentation/articles/partner-sencha-mobile-services-get-started/ "Sencha") [PhoneGap](/en-us/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/ "PhoneGap")

Ce didacticiel vous montre comment ajouter un service principal cloud à une application HTML en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Vous pouvez regarder une version vidéo de ce didacticiel en cliquant sur le clip à droite.

[regarder le didacticiel](http://go.microsoft.com/fwlink/?LinkId=287040) [Lire la vidéo](http://go.microsoft.com/fwlink/?LinkId=287040) 03:51:00

Voici une capture d'écran de l'application terminée :

![](./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png)

Vous devez suivre ce didacticiel avant de pouvoir suivre les autres didacticiels Mobile Services pour les applications HTML.

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F).

### Conditions supplémentaires

-   L'un des serveurs Web suivants doit être exécuté sur votre ordinateur local pour pouvoir suivre ce didacticiel :

    -   **Sur Windows** : IIS Express. IIS Express est installé par le [programme d'installation de la plate-forme Web Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=286333).
    -   **Sur MacOS X** : Python, qui doit déjà être installé.
    -   **Sur Linux** : Python. Vous devez installer la [dernière version de Python](http://go.microsoft.com/fwlink/p/?LinkId=286342).

    Vous pouvez utiliser n'importe quel serveur Web pour héberger l'application, mais les serveurs précédents sont les seuls pris en charge par les scripts téléchargés.

-   Un navigateur Web qui prend en charge le HTML5

Création d'un service mobile
----------------------------

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

Création d'une applicationCréation d'une application HTML
---------------------------------------------------------

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application HTML connectée à votre appareil mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2.  Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application HTML**.

   	![][6]

   	Ceci affiche les trois étapes pour créer et héberger une application HTML connecté à votre service mobile.

   	![](./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png)

1.  Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

2.  Sous **Download and run your app**, cliquez sur **Télécharger**.

   	Ceci entraîne le téléchargement des fichiers de site Web pour l'exemple d'application *To do list* connectée à votre service mobile. Enregistrez le fichier compressé sur votre ordinateur local, et notez où vous l'enregistrez.

1.  Sous l'onglet **Configurer** vérifiez que `localhost` figure déjà dans la liste **Autoriser les demandes à partir des noms d'hôte** sous **Partage des ressources cross-origin (CORS)**. Si cela n'est pas le cas, entrez `localhost` dans le champ **Nom d'hôte**, puis cliquez sur **Enregistrer**.

   	![](./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png)

	<div class="dev-callout"><b>Note</b>
		<p>Si vous déployez l'application de démarrage rapide sur un autre serveur que localhost, vous devez ajouter le nom d'hôte du serveur Web à la liste Autoriser les demandes à partir des noms d'hôte. Pour plus d'informations, consultez l'article [Partage des ressources cross-origin] (CORS).</a>.</p>

Hébergement et exécution de votre application HTML
--------------------------------------------------

La dernière étape de ce didacticiel consiste à héberger et exécuter votre nouvelle application sur votre ordinateur local.

1.  Accédez à l'emplacement sur lequel vous avez enregistré les fichiers du projet compressés, décompressez-les sur votre ordinateur, puis exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **serveur**.

   -   **launch-windows** (pour les ordinateurs Windows)
    -   **launch-mac.command** (pour les ordinateurs Mac OS X)
    -   **launch-linux.sh** (pour les ordinateurs Linux)

    **Remarque**

    Sur un ordinateur Windows, appuyez sur la touche « R » lorsque PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer à charger le script.

    Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

2.  Ouvrez l'URL <http://localhost:8000/> dans un navigateur Web pour démarrer l'application.

3.  Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

   	![][10]

   	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la deuxième colonne de l'application.

    **Remarque**

    Vous pouvez vérifier le code qui se trouve dans le fichier app.js et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

4.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

   	![][11]

   	Cela vous permet de parcourir les données insérées par l'application dans la table.

   	![][12]

Étapes suivantes
----------------

Maintenant que vous avez effectué le démarrage rapide, découvrez comment effectuer d'autres tâches importantes dans Mobile Services :

-	**[Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-html)**
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

-	**[Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-html)**
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* **[Guide de fonctionnement Mobile Services HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)** 
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec le HTML/JavaScript

<!-- Anchors. -->
[Getting started with Mobile Services]:#getting-started
[Create a new mobile service]:#create-new-service
[Define the mobile service instance]:#define-mobile-service-instance
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-html
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-html

[Management Portal]: https://manage.windowsazure.com/
[Microsoft Web Platform Installer]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[latest version of Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client
[Partage des ressources cross-origin]: http://msdn.microsoft.com/en-us/library/windowsazure/dn155871.aspx

