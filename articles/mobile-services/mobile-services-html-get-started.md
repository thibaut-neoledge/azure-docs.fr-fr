<properties
	pageTitle="Prise en main d'Azure Mobile Services pour les applications HTML 5"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement HTML."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="ggailey777"/>


# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel vous montre comment ajouter un service principal cloud à une application HTML en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application <em>To do list</em> qui stocke les données d'application dans le nouveau service mobile. Vous pouvez regarder une version vidéo de ce didacticiel en cliquant sur le clip à droite.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-html-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=287040" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">03:51:00</span></div>
</div>

Voici une capture d’écran de l’application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre les autres didacticiels Mobile Services pour les applications HTML.

> [AZURE.IMPORTANT]Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank).

###Conditions supplémentaires

+ L'un des serveurs web suivants doit être exécuté sur votre ordinateur local pour pouvoir suivre ce didacticiel :

	+  **Sur Windows** : IIS Express. IIS Express est installé par le [programme d'installation de la plate-forme Web Microsoft].
	+  **Sur MacOS X** : Python, qui doit déjà être installé.
	+  **Sur Linux** : Python. Vous devez installer la [dernière version de Python].

	Vous pouvez utiliser n'importe quel serveur Web pour héberger l'application, mais les serveurs précédents sont les seuls pris en charge par les scripts téléchargés.

+ Un navigateur web qui prend en charge le HTML5


## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Création d'une application HTML

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application HTML connectée à votre appareil mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.


2. Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application HTML**.

   	![][6]

   	Ceci affiche les trois étapes pour créer et héberger une application HTML connectée à votre service mobile.

  	![][7]

3. Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

4. Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**.

  	Ceci entraîne le téléchargement des fichiers de site Web pour l'exemple d'application _To do list_ connectée à votre service mobile. Enregistrez le fichier compressé sur votre ordinateur local, et notez où vous l'enregistrez.

5. Sous l'onglet **Configurer** vérifiez que `localhost` figure déjà dans la liste **Autoriser les demandes à partir des noms d'hôte** sous **Partage des ressources cross-origin (CORS)**. Si cela n'est pas le cas, entrez `localhost` dans le champ **Nom d'hôte**, puis cliquez sur **Enregistrer**.

  	![][9]

	> [AZURE.IMPORTANT]Si vous déployez l'application de démarrage rapide sur un autre serveur que localhost, vous devez ajouter le nom d'hôte du serveur Web à la liste **Autoriser les demandes à partir des noms d'hôte**. Pour plus d'informations, consultez la page [Partage des ressources cross-origin](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx"%20target="_blank).

## Hébergement et exécution de votre application HTML

La dernière étape de ce didacticiel consiste à héberger et exécuter votre nouvelle application sur votre ordinateur local.

1. Accédez à l'emplacement sur lequel vous avez enregistré les fichiers du projet compressés, décompressez-les sur votre ordinateur, puis exécutez l'un des fichiers de commandes suivants à partir du sous-dossier **serveur**.

	+ **launch-windows** (pour les ordinateurs Windows)
	+ **launch-mac.command** (pour les ordinateurs Mac OS X)
	+ **launch-linux.sh** (pour les ordinateurs Linux)

	> [AZURE.NOTE]Sur un ordinateur Windows, appuyez sur la touche `R` quand PowerShell vous demande de confirmer l'exécution du script. Vous pouvez recevoir un avertissement de votre navigateur Web vous recommandant de ne pas exécuter le script, car il a été téléchargé depuis Internet. Lorsque cela se produit, vous devez demander au navigateur de continuer à charger le script.

	Ceci démarre un serveur Web sur votre ordinateur local pour pouvoir héberger la nouvelle application.

2. Ouvrez l'URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> dans un navigateur web pour démarrer l'application.

3. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, dans **Entrer une nouvelle tâche**, puis cliquez sur **Ajouter**.

   	![][10]

   	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la deuxième colonne de l’application.

	> [AZURE.NOTE]Vous pouvez vérifier le code qui se trouve dans le fichier app.js et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

4. De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

   	![][11]

   	Cela vous permet de parcourir les données insérées par l'application dans la table.

   	![][12]

## <a name="next-steps"> </a>Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d’autres tâches importantes dans Mobile Services :

* **[Prise en main des données]** <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* **[Appel d'une API personnalisée à partir d'une application HTML]** <br/>Connectez votre application HTML à l'aide d'une API personnalisée hébergée sur Mobile Services.

* **[Prise en main de l'authentification]** <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* **[Guide de fonctionnement Mobile Services HTML/JavaScript]** <br/>: découvrez plus en détail comment utiliser Mobile Services avec le HTML/JavaScript

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png





[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Prise en main des données]: /develop/mobile/tutorials/get-started-with-data-html
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-html
[Appel d'une API personnalisée à partir d'une application HTML]: mobile-services-html-call-custom-api.md

[Management Portal]: https://manage.windowsazure.com/
[programme d'installation de la plate-forme Web Microsoft]: http://go.microsoft.com/fwlink/p/?LinkId=286333
[dernière version de Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Guide de fonctionnement Mobile Services HTML/JavaScript]: /develop/mobile/how-to-guides/work-with-html-js-client
[Cross-origin resource sharing]: http://msdn.microsoft.com/library/windowsazure/dn155871.aspx
 

<!---HONumber=58_postMigration-->