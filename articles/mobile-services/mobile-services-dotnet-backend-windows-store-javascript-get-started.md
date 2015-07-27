<properties
	pageTitle="Prise en main de Mobile Services pour les applications Windows Store | Centre de développement mobile"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Windows Store en C#, VB ou JavaScript."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.date="04/24/2015"
	ms.author="glenga"/>


# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

Ce didacticiel présente l’ajout d’un service principal cloud à une application Windows universelle à l’aide d’Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application *To do list* en HTML et JavaScript qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la version principale JavaScript de cette rubrique.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Une version d’évaluation gratuite est disponible.


## Création d’un service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Création d’une application Windows universelle

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Windows universelle connectée à votre service mobile.

1. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Dans l'onglet de démarrage rapide, cliquez sur **Windows** sous **Choisissez une plateforme** et développez **Créer une application Windows Store**.

   	![][6]

   	Cette opération affiche les trois étapes faciles permettant de créer une application Windows Store connectée à votre service mobile.

  	![][7]

3. Si ce n'est déjà fait, téléchargez et installez <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> sur votre ordinateur local ou sur votre machine virtuelle.

4. Sous **Télécharger et exécuter votre application et votre service en local**, sélectionnez la langue de votre application Windows Store, puis cliquez sur **Télécharger**.

  	Ceci entraîne le téléchargement d'une solution contenant les projets du service mobile et de l'exemple d'application _To do list_ connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.


## Test de l’application avec le service mobile local

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]Vous pouvez vérifier le code qui se trouve dans le fichier default.js et permet d’accéder au service mobile pour exécuter une requête et insérer des données.

## Publication de votre service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

<ol start="4">
<li><p>Dans le projet de code partagé, ouvrez le fichier default.js, recherchez le code de création d'une instance <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx" target="_blank">WindowsAzure.MobileServiceClient</a> placez en commentaire le code de création de ce client à l'aide de <em>localhost</em> et annulez le commentaire du code de création du client à l'aide de l'URL du service mobile distant, lequel est semblable à ce qui suit&#160;:</p>

        <pre><code>var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXXXX-APPLICATION-KEY-XXXXXX"
        );</code></pre>

	<p>Le client a désormais accès au service mobile publié sur Azure.</p></li>

<li><p>Appuyez sur la touche <strong>F5</strong> pour régénérer le projet et démarrer l'application.</p></li>

<li><p>Dans l'application, tapez un texte explicite, comme <em>Suivre le didacticiel</em>, dans <strong>Insert a TodoItem</strong>, puis cliquez sur <strong>Enregistrer</strong>.</p>

<p>Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure.</p>
</li>
<li><p>(Facultatif) Dans une solution Windows universelle, remplacez le projet de démarrage par défaut par l'autre application et appuyez de nouveau sur <strong>F5</strong>.</p>

	<p>Notez que les données enregistrées à l'étape précédente sont chargées à partir du service mobile après le démarrage de l'application.</p></li>
</ol>

Pour plus d'informations sur les applications Windows universelles, consultez la page [Prise en charge de plusieurs plateformes d'appareil à partir d'un service mobile unique](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-completed.png

[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-portal-quickstart.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-steps.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-service-startup.png

[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-startup.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-quickstart-publish.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started/mobile-data-browse.png


<!-- URLs. -->
[Get started with data]: ../mobile-services-dotnet-backend-windows-universal-javascript-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Management Portal]: https://manage.windowsazure.com/
[JavaScript version]: ../mobile-services-windows-store-get-started.md
[Get started with data in Mobile Services using Visual Studio 2012]: ../mobile-services-windows-store-dotnet-get-started-data-vs2012.md
 

<!---HONumber=July15_HO3-->