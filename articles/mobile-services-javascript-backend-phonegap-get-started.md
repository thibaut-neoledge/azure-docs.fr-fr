<properties pageTitle="Prise en main de Mobile Services avec PhoneGap | Centre de développement mobile" metaKeywords="" description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement de PhoneGap pour iOS, Android et Windows Phone." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-phonegap" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

# <a name="getting-started"> </a>Prise en main de Mobile Services

Ce didacticiel vous montre comment ajouter un service principal cloud à une application en utilisant Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application _To do list_ qui stocke les données d'application dans le nouveau service mobile. 

Voici une capture d'écran de l'application terminée :

![][3]

### <a name="additional-requirements"></a>Conditions supplémentaires

Pour suivre ce didacticiel, vous devez disposer des outils PhoneGap (v3.2+ requise pour les projets Windows Phone 8). 

PhoneGap prend en charge le développement de plusieurs plateformes. Outre les outils PhoneGap, vous devez installer les outils de chacune des plateformes requises :

- Windows Phone : Installez [Visual Studio 2012 Express pour Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
- iOS : installez [Xcode] (version 4.4+ requise)
- Android : installez les [outils de développement Android][Android SDK]
	<br/>(le Kit de développement logiciel (SDK) Mobile Services pour Android prend en charge les applications destinées à Android 2.2 (et versions ultérieures). Android 4.2 (ou version ultérieure) est requis pour exécuter l'application de démarrage rapide).

## <a name="create-new-service"> </a>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Création d'une application PhoneGap</h2>

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile. 

Dans cette section, vous allez créer une application PhoneGap connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Services mobiles**, puis sur le service mobile que vous venez de créer.

2. Dans l'onglet de démarrage rapide, cliquez sur **PhoneGap** sous **Choisissez une plateforme** et développez **Créer une application PhoneGap**.

   	![][0]

   	Cette opération affiche les trois étapes faciles permettant de créer une application PhoneGap connectée à votre service mobile.

  	![][1]

3. Si ce n'est déjà fait, téléchargez et installez PhoneGap et au moins un des outils de développement de plateforme (Windows Phone, iOS ou Android).

4. Cliquez sur **Create TodoItems table** pour créer une table permettant de stocker les données d'application.

5. Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**. 

	Cette opération télécharge le projet de votre exemple d'application _To do list_ qui est connectée à votre service mobile, ainsi que le Kit de développement logiciel (SDK) Mobile Services JavaScript. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre nouvelle application PhoneGap

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application. 

1.	Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur. 

2.	Ouvrez et exécutez le projet conformément aux instructions disponibles sous chaque plateforme.

	+ **Windows Phone 8**

	1. Windows Phone 8 : ouvrez le fichier .sln situé dans le dossier **platforms\wp8** dans Visual Studio 2012 Express pour Windows Phone.
	
	2. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.
	
	  	![][2]

	+ **iOS**

	1. Ouvrez le projet du dossier **platforms/ios** dans Xcode.
	
	2. Appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone (configuration par défaut pour ce projet).
	
	  	![][3]

	+ **Android**

		1. Dans Eclipse, cliquez sur **Fichier**, puis sur **Importer**, développez **Android**, cliquez sur **Code Android existant dans l'espace de travail**, puis sur **Suivant.** 
		
		2. Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, veillez à ce que le projet TodoActivity soit sélectionné, puis cliquez sur **Terminer**. <p>Cette opération importe les fichiers projet dans l'espace de travail actuel.</p>
		
		3. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.
		
			![][4]
	
		>[WACOM.NOTE]Afin d'exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.
			
	
3. Après avoir lancé l'application dans un des émulateurs mobiles ci-dessus, tapez du texte dans la zone de texte, puis cliquez sur **Ajouter**.

	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la demande sont insérées dans la table **TodoItem**. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	<div class="dev-callout"><strong>Important</strong> <p>Les modifications apportées à ce projet de plateforme seront écrasées si le projet principal est régénéré à l'aide des outils PhoneGap. Par conséquent, il est impératif d'apporter ces modifications dans le répertoire www racine du projet, comme décrit à la section suivante.</p></div>

4. Dans le portail de gestion, cliquez sur l'onglet <strong>Données</strong> et sur la table <strong>TodoItems</strong> .

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

	Cela vous permet de parcourir les données insérées par l'application dans la table.

	![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)
	

## Mise à jour de l'application et régénération des projets pour chaque plateforme

1. Modifiez les fichiers de code du répertoire " www ". Il s'agit ici de " todolist/www ".

2. Vérifiez que les outils de toutes les plateformes cibles sont accessibles dans le chemin d'accès système. 

2. Ouvrez une invite de commandes dans le répertoire projet racine, puis exécutez une des commandes spécifiques de la plateforme : 

	+ **Windows Phone**

		Exécutez la commande suivante depuis une invite de commandes de développement Visual Studio :

    		phonegap local build wp8

	+ **iOS**
 
		Ouvrez le terminal et exécutez la commande suivante :

    		phonegap local build ios

	+ **Android**

		Ouvrez une invite de commandes ou une fenêtre terminal et exécutez la commande suivante : 

		    phonegap local build android


4. Ouvrez chaque projet dans l'environnement de développement approprié, comme décrit à la section précédente.

>[WACOM.NOTE]Dans le fichier js/index.js, vous pouvez vérifier le code qui permet d'accéder à votre service mobile pour exécuter une requête et insérer des données.

## <a name="next-steps"> </a>Étapes suivantes
Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services : 

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.
  
<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-html-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-html-get-started-users
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/mobile-services-html-get-started-push
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Portail de gestion]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express pour Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374


<!--HONumber=35.1-->
