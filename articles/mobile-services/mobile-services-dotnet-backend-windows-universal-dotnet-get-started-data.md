<properties 
	pageTitle="Ajout de Mobile Services à une application existante (Windows Store) | Centre de développement mobile" 
	description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/02/2015" 
	ms.author="wesmc"/>

# Ajout de services mobiles à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Vue d'ensemble

Cette rubrique explique comment utiliser Azure Mobile Services en tant que source de données principale pour une application Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer dans ce didacticiel est un service mobile de backend .NET. Vous pouvez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile, et vous pouvez exécuter et déboguer votre service mobile sur votre ordinateur local. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la version du backend JavaScript de cette rubrique.

>[AZURE.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour connecter un nouveau service mobile à une application Windows universelle. La même procédure peut être utilisée pour connecter un service mobile à une application Windows Store ou Windows Phone Store 8.1. Pour connecter un service mobile à une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, voir la section [Prise en main de données pour Windows Phone](mobile-services-dotnet-backend-windows-phone-get-started-data.md).

> Si vous ne pouvez pas procéder à la mise à niveau Visual Studio Professional 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application Windows Store, consultez [cette version](../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md) de la rubrique.

##Configuration requise

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 ou une version ultérieure). 

##Téléchargement du projet GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##Création d'un service mobile à partir de Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>Dans l'Explorateur de solutions, ouvrez le fichier de code App.xaml.cs dans le dossier de projet GetStartedWithData.Shared, et voyez comment le nouveau champ statique a été ajouté à la classe <strong>App</strong> dans un bloc de compilation conditionnelle de l'application Windows Store, pour ressembler à l'exemple suivant&#160;:</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Ce code fournit un accès à votre nouveau service mobile dans votre application à l'aide d'une instance de la <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">classe MobileServiceClient</a>. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Ce champ statique est disponible pour toutes les pages de votre application.</p>
</li>
<li><p>Cliquez avec le bouton droit de la souris sur le projet d'application Windows Phone, cliquez sur <strong>Ajouter</strong>, puis sur <strong>Service connecté...</strong>, sélectionnez le service mobile que vous venez de créer, puis cliquez sur <strong>OK</strong>. </p>
<p>Le même code est ajouté au fichier App.xaml.cs partagé, mais dans un bloc de compilation conditionnelle de l'application Windows Phone.</p></li>
</ol>

Les deux applications Windows Store et Windows Phone Store sont maintenant connectées au nouveau service mobile. L'étape suivante consiste à tester le nouveau projet de service mobile.


##Test local d'un projet de service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##Mise à jour de l'application pour utiliser le service mobile

Dans cette section, vous allez mettre à jour l'application Windows universelle pour utiliser le service mobile en tant que service principal de l'application. Vous devrez uniquement modifier le fichier de projet MainPage.xaml.cs dans le dossier de projet GetStartedWithData.Shared.

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]


##Publication du service mobile sur Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##Test du service mobile hébergé dans Azure

Nous pouvons maintenant tester les deux versions de l'application Windows universelle sur le service mobile hébergé dans Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

##Affichage des données stockées dans la base de données SQL

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
Cela conclut le didacticiel.

##Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'un projet d'application Windows universelle pour utiliser les données dans Mobile Services. Vous pouvez ensuite consulter une de ces autres rubriques :

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement Mobile Services C#](mobile-services-windows-dotnet-how-to-use-client-library.md) <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.


<!-- Images. -->



<!-- URLs. -->
[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Prise en main de l'authentification]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Prise en main des notifications Push]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md

[Get started with offline data sync]: mobile-services-windows-store-dotnet-get-started-offline-data.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkID=510826
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!---HONumber=July15_HO4-->