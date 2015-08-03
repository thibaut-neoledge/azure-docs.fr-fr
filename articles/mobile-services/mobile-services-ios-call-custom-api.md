<properties
	pageTitle="Comment appeler une API personnalisée à partir d'un client iOS"
	description="Découvrez comment définir une API personnalisée et l'appeler depuis une application iOS qui utilise Azure Mobile Services."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/04/2015"
	ms.author="krisragh"/>

# Comment appeler une API personnalisée à partir d'un client iOS (backend JavaScript)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique explique comment appeler une API personnalisée à partir d'une application iOS. Une API personnalisée vous permet de définir des points de terminaison avec une fonctionnalité de serveur, mais elle ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture de base de données. En utilisant une API personnalisée, vous avez plus de contrôle sur la messagerie, notamment sur les en-têtes HTTP et le format du corps.

## <a name="define-custom-api"></a>Définition d'une API personnalisée

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

## Étapes suivantes

Cette rubrique vous a montré comment utiliser la méthode **invokeApi** pour appeler une API personnalisée relativement simple à partir de votre application iOS. Pour en savoir plus sur l'utilisation de la méthode **invokeApi**, consultez le billet [API personnalisée dans Azure Mobile Services](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

Pour plus d'informations sur les rubriques Mobile Services suivantes, consultez :

* [Référence de script serveur Mobile Services] <br/>Familiarisez-vous avec la création des API personnalisées.

* [Stockage des scripts serveur dans le contrôle du code source ] <br/> Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services Quick Start]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: ../mobile-services-ios-get-started-push.md
[Stockage des scripts serveur dans le contrôle du code source ]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO4-->