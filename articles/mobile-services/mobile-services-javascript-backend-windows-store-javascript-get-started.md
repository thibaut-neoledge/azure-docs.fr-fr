<properties
	pageTitle="Prise en main de Mobile Services pour les applications Windows Store en JavaScript | Azure Mobile Services"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement d’applications Windows Store en JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="glenga"/>

# Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Ce didacticiel montre comment ajouter un service de backend cloud à une application Windows Store en JavaScript à l’aide d’Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur.

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* [Visual Studio 2013 Express pour Windows]

## Création d’un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Création d'une application Windows Store

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail Azure Classic pour créer une application Windows Store 8.1 en JavaScript qui se connecte à votre service mobile.

1.  Dans le [portail Azure Classic], cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.


2. Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application Windows Store**.

3. Si ce n'est déjà fait, téléchargez et installez [Visual Studio 2013][Visual Studio 2013 Express for Windows] sur votre ordinateur local ou sur votre machine virtuelle.

4. Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.

5. Sous **Download and run your app**, sélectionnez une langue pour votre application, puis cliquez sur **Télécharger**.

  	Cette opération télécharge le projet de votre exemple d'application *To do list* qui est connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Windows

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, développez les fichiers sur votre ordinateur et ouvrez le fichier solution dans Visual Studio.

2. Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

3. Dans l'application, tapez un texte explicite, comme *Suivre le didacticiel*, dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

   	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la deuxième colonne de l'application.

4. (Facultatif) Réexécutez l’application. Notez que les données enregistrées à l’étape précédente sont chargées à partir du service mobile après le démarrage de l’application.
 
4. De retour dans le [portail Azure Classic], cliquez sur l’onglet **Données**, puis cliquez sur la table **TodoItems**.

   	Cela vous permet de parcourir les données insérées par l'application dans la table.

>[AZURE.NOTE] Vous pouvez vérifier le code qui se trouve dans le fichier default.js et permet d’accéder au service mobile pour exécuter une requête et insérer des données.

## Étapes suivantes
Le démarrage rapide étant terminé, découvrez comment utiliser le [client Mobile Services pour HTML/JavaScript](mobile-services-html-how-to-use-client-library.md).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express for Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Visual Studio 2013 Express pour Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portail Azure Classic]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0211_2016-->