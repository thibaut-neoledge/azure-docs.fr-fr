<properties
	pageTitle="Prendre en main Azure Mobile Services pour les applications Android (backend JavaScript)"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Android (backend JavaScript)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="11/05/2015"
	ms.author="ricksal"/>

# Prendre en main Mobile Services pour Android (backend JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Ce didacticiel présente l'ajout d'un service backend cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application **To do list** qui stocke les données d'application dans le nouveau service mobile.

> [AZURE.VIDEO mobile-get-started-android]

Voici une capture d’écran de l’application terminée :

![](./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png)

## Composants requis

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android](https://developer.android.com/sdk/index.html), qui incluent l’environnement de développement intégré Android Studio et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Azure Mobile Services pour Android.

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28).


## Création d’un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Création d’une application Android

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail Azure Classic pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le portail Azure Classic, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

   	![](./media/mobile-services-android-get-started/mobile-portal-quickstart-android1.png)

   	Cette opération affiche les trois étapes faciles permettant de créer une application Android connectée à votre service mobile.

  	![](./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png)

3. Si ce n'est pas déjà fait, téléchargez et installez les [outils de développement Android](https://go.microsoft.com/fwLink/p/?LinkID=280125) sur votre ordinateur local ou sur votre machine virtuelle.

4. Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.


5. Téléchargez alors votre application en appuyant sur le bouton **Télécharger**.

## Exécution de votre application Android

[AZURE.INCLUDE [mobile-services-run-your-app](../../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>Étapes suivantes
Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

* [Prise en main des données] <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- URLs. -->
[Get started (Eclipse)]: mobile-services-android-get-started-ec.md
[Prise en main des données]: mobile-services-android-get-started-data.md
[Prise en main de l'authentification]: mobile-services-android-get-started-users.md
[Prise en main des notifications Push]: mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

<!---HONumber=AcomDC_0128_2016-->