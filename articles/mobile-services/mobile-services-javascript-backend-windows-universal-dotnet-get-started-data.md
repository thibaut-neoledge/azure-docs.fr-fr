<properties 
	pageTitle="Ajouter Mobile Services à une application Windows universelle existante | Azure Mobile Services" 
	description="Découvrez comment connecter votre application Windows universelle existante à Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="glenga"/>

# Ajout de services mobiles à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

##Vue d'ensemble

Cette rubrique explique comment utiliser Azure Mobile Services pour exploiter les données dans une application Windows universelle. Les solutions d'application Windows universelles incluent des projets pour Windows Store 8.1 et Windows Phone Store 8.1, et un projet partagé commun. Pour plus d'informations, consultez la page [Créer des applications Windows universelles qui ciblent Windows et Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application Windows universelle qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

>[AZURE.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour connecter un nouveau service mobile à une application Windows universelle. La même procédure peut être utilisée pour connecter un service mobile à une application Windows Store ou Windows Phone Store 8.1. Pour connecter un service mobile à une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, voir la section [Prise en main de données pour Windows Phone](mobile-services-windows-phone-get-started-data.md).

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F).
* [Visual Studio Express 2013 pour Windows](https://go.microsoft.com/fwLink/p/?LinkID=257546) (Update 2 ou une version ultérieure). 

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../../includes/mobile-services-windows-universal-dotnet-download-project.md)]
 

##<a name="create-service"></a>Création d'un service mobile à partir de Visual Studio

[AZURE.INCLUDE [mobile-services-create-new-service-vs2013](../../includes/mobile-services-create-new-service-vs2013.md)]

&nbsp;&nbsp;7. Dans l'Explorateur de solutions, ouvrez le fichier de code App.xaml.cs dans le dossier de projet GetStartedWithData.Shared, et voyez comment le nouveau champ statique a été ajouté à la classe **App** dans un bloc de compilation conditionnelle de l'application Windows Store, pour ressembler à l'exemple suivant :

	public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
	    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
	        "https://todolist.azure-mobile.net/",
	        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

&nbsp;&nbsp;Ce code fournit l'accès à votre nouveau service mobile dans votre application à l'aide d'une instance de la classe [MobileServiceClient]. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Ce champ statique est disponible pour toutes les pages de votre application.

&nbsp;&nbsp;8. Cliquez avec le bouton droit de la souris sur le projet d'application Windows Phone, cliquez sur **Ajouter**, puis sur **Service connecté...**, sélectionnez le service mobile que vous venez de créer, puis cliquez sur **OK**. Le même code est ajouté au fichier App.xaml.cs partagé, mais dans un bloc de compilation conditionnelle de l'application Windows Phone.

Les deux applications Windows Store et Windows Phone Store sont maintenant connectées au nouveau service mobile. L'étape suivante consiste à créer une table TodoItem dans le service mobile.

##<a name="add-table"></a>Ajout d'une nouvelle table au service mobile

[AZURE.INCLUDE [mobile-services-create-new-table-vs2013](../../includes/mobile-services-create-new-table-vs2013.md)]

##<a name="update-app"></a>Mise à jour de l'application pour utiliser le service mobile

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../../includes/mobile-services-windows-dotnet-update-data-app.md)]

##<a name="test-azure-hosted"></a>Test du service mobile hébergé dans Azure

Nous pouvons maintenant tester les deux versions de l'application Windows universelle sur le service mobile hébergé dans Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]

&nbsp;&nbsp;4. Dans le [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur le service mobile.

&nbsp;&nbsp;5. Cliquez sur **Données** > **Parcourir** et notez que la table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.
     
Cela conclut le didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows universelle pour utiliser les données dans Mobile Services. Vous pouvez ensuite consulter une de ces autres rubriques :

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push]
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement Mobile Services C#](mobile-services-windows-dotnet-how-to-use-client-library.md)
  <br/>En savoir plus sur l'utilisation de Mobile Services avec .NET.
  
<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use the mobile service]: #update-app
[Test the app against Mobile Services]: #test-app
[View uploaded data in the Azure Management Portal]: #view-data
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Validate and modify data with scripts]: ../mobile-services-windows-store-dotnet-validate-modify-data-server-scripts.md
[Refine queries with paging]: ../mobile-services-windows-store-dotnet-add-paging-data.md
[Get started with Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Prise en main de l'authentification]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Prise en main des notifications Push]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md

[portail de gestion Azure]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[site d'exemples de code développeur]: http://go.microsoft.com/fwlink/p/?LinkID=510826

[MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!-----HONumber=August15_HO6-->