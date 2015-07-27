<properties 
	pageTitle="Appel d'une API personnalisée à partir d'un client Windows Phone - Mobile Services" 
	description="Learn how to define a custom API and then call it from a Windows Phone app that use Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="glenga"/>

# Appel d'une API personnalisée à partir du client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique montre comment appeler une API personnalisée à partir d'une application Windows Phone. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client devrait envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée en suivant le didacticiel [Ajout de Mobile Services à une application existante](mobile-services-windows-phone-get-started-data.md). Ce didacticiel se base sur l'exemple GetStartedWithData, une application TodoList simple. Avant de commencer ce didacticiel, vous devez terminer celui intitulé : [Ajout de Mobile Services à une application existante](mobile-services-windows-phone-get-started-data.md).

## <a name="define-custom-api"></a>Définir l'API personnalisée

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../../includes/mobile-services-windows-phone-call-custom-api.md)]

## Étapes suivantes

Cette rubrique vous a montré comment utiliser la méthode **InvokeApiAsync** pour appeler une API personnalisée relativement simple à partir de votre application Windows Phone. Pour en savoir plus sur l'utilisation de la méthode **InvokeApiAsync**, consultez le billet [API personnalisée dans Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Pour plus d'informations sur les rubriques Mobile Services suivantes, consultez :

* [Référence de script serveur Mobile Services] <br/>Familiarisez-vous avec la création des API personnalisées.

* [Stockage des scripts serveur dans le contrôle du code source ] <br/> Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md
[Get started with data]: mobile-services-windows-phone-get-started-data.md
[Get started with authentication]: mobile-services-windows-phone-get-started-users.md
[Get started with push notifications]: ../mobile-services-windows-phone-get-started-push.md

[Stockage des scripts serveur dans le contrôle du code source ]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO3-->