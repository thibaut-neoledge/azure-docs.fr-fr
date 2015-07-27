<properties 
	pageTitle="Appel d'une API personnalisée à partir d'un client JS Windows Store - Mobile Services" 
	description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." 
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
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Appel d'une API personnalisée à partir du client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique explique comment appeler une API personnalisée à partir d'une application Windows Store. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client devrait envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez suivre [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante].

## <a name="define-custom-api"></a>Définir l'API personnalisée

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]


[AZURE.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../../includes/mobile-services-windows-store-javascript-call-custom-api.md)]

## Étapes suivantes

Cette rubrique vous a montré comment utiliser la fonction **invokeApi** pour appeler une API personnalisée relativement simple à partir de votre application Windows. Pour en savoir plus sur l'utilisation de la fonction **invokeApi**, consultez le billet [API personnalisée dans Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Pour plus d'informations sur les rubriques Mobile Services suivantes, consultez :

* [Référence de script serveur Mobile Services] <br/>Familiarisez-vous avec la création des API personnalisées.

* [Stockage des scripts serveur dans le contrôle du code source ] <br/> Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Prise en main de Mobile Services]: ../mobile-services-windows-store-get-started.md
[Ajout de Mobile Services à une application existante]: mobile-services-windows-store-javascript-get-started-data.md
[Get started with authentication]: mobile-services-windows-store-javascript-get-started-users.md
[Get started with push notifications]: mobile-services-javascript-backend-windows-store-javascript-get-started-push.md

[Define a custom API that supports periodic notifications]: mobile-services-windows-store-javascript-create-pull-notifications.md
[Stockage des scripts serveur dans le contrôle du code source ]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO3-->