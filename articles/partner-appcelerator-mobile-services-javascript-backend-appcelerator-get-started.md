<properties 
	pageTitle="Prise en main d'Azure Mobile Services pour Appcelerator Titanium" 
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Appcelerator." 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="hero-article" 
	ms.date="11/24/2014" 
	ms.author="mahender"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Ce didacticiel vous montre comment exploiter Azure Mobile Services dans vos applications générées avec Appcelerator.

Dans ce didacticiel, vous allez créer un service mobile et une application simple (_To do list_) qui stocke les données d'application dans le nouveau service mobile. Le service mobile à créer utilise du code JavaScript pour la logique métier côté serveur.

Pour suivre ce didacticiel, vous avez besoin d'[Appcelerator Titanium].

Si vous créez pour iOS, vous avez également besoin de Xcode 5.1 et du Kit de développement logiciel (SDK) iOS 7.1 ou de versions ultérieures de ces produits. 

Si vous créez pour Android, vous avez également besoin du Kit de développement logiciel (SDK) Android 4.3 ou d'une version ultérieure.

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## Création d'une application Appcelerator

1. Dans le portail Mobile Services, sélectionnez l'onglet **Données** de votre service mobile.

2. Cliquez sur **Ajouter une table** et créez une table nommée **TodoItem**.

3. Téléchargez une nouvelle [application Todolist] Appcelerator et décompressez le projet.

4. Si vous ne l'avez pas déjà fait, téléchargez et installez [Appcelerator Titanium Studio][Appcelerator Titanium] et le Kit de développement logiciel (SDK) v3.2.1 ou une version supérieure. Vous avez également besoin de Xcode (v 5.0 +) et/ou du Kit de développement logiciel (SDK) Android (v 4.3+) pour exécuter le projet.

5. De retour dans le portail Mobile Services, sous **Tableau de bord**, sélectionnez **Gérer les clés** et faites une copie de la **Clé de l'application**.

5. Dans le fichier index.js de l'application, remplacez `<---App Name---->` et `<------------APP KEY------------>` par les valeurs de votre service mobile.

## Exécution de votre nouvelle application Appcelerator ##

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Ouvrez Titanium Studio et accédez à **File -> Import** (Fichier -> Importer) pour importer le projet que vous avez téléchargé précédemment.

    ![][0]

2.	Dans l'écran suivant, sélectionnez **Existing Projects into Workspace** (Projets existants dans l'espace de travail) et cliquez sur **Next** (Suivant).

    ![][1]

3.	Dans l'écran de sélection des projets, utilisez l'option Browse (Parcourir) et recherchez le projet Titanium Azure que vous avez téléchargé sur le portail de gestion Azure.

    ![][2]

4.	Le projet que vous avez sélectionné dans la section Projects (Projets) s'affiche. Vous pouvez également cocher l'option &quot;Copy projects into workspace&quot; (Copier les projets dans l'espace de travail) pour copier le projet téléchargé dans votre espace de travail. Pour finir, cliquez sur Finish (Terminer) pour ouvrir le projet dans Titanium Studio.

    ![][3]

5.	Sélectionnez la plateforme (iOS/Android) sur laquelle vous voulez exécuter le projet.

    ![][4]

6.	Appuyez sur le bouton Run (Exécuter) pour générer le projet et démarrer l'application dans le simulateur iPhone ou l'émulateur Android.

7.	Faites votre choix en vous basant sur les paramètres du portail de gestion Azure.

    ![][5]

8.	Dans l'écran suivant, cliquez sur l'icône représentant un signe plus (+), tapez un texte pertinent tel que &quot;Suivre ce didacticiel&quot; et cliquez sur le bouton Save (Enregistrer).<br />

    ![][6]

    ![][7]

Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Microsoft Azure. Les données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

![][8]


>[AZURE.NOTE] Dans le fichier TodoService.m, vous pouvez vérifier le code qui permet d'accéder à votre service mobile pour exécuter une requête et insérer des données.

4. De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

   	![][9]

   	Cela vous permet de parcourir les données insérées par l'application dans la table.

   	![][10]


## <a name="next-steps"> </a>Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d'autres tâches importantes dans Mobile Services : 

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] 
  <br/>Découvrez comment envoyer une notification Push très simple à votre application.



<!-- Images. -->
[0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image007.png
[1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image008.png
[2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image009.png
[3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image010.png
[4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image011.png
[5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image012.png
[6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image013.png
[7]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image014.png
[8]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/image015.png
[9]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-tab.png
[10]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started/mobile-data-browse.png

<!-- URLs. -->
[Application Todolist]: http://go.microsoft.com/fwlink/p/?LinkId=506859
[Appcelerator Titanium]: http://go.microsoft.com/fwlink/p/?LinkID=509987
[Prise en main de l'authentification]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-users.md
[Prise en main des notifications Push]: partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push.md


<!--HONumber=52-->