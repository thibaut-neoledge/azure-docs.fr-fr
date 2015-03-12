<properties 
	pageTitle="Appel d'une API personnalisée à partir d'une application Windows Phone - Mobile Services" 
	description="Découvrez comment définir une API personnalisée et l'appeler depuis une application Windows Phone qui utilise Microsoft Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	Writer="glenga" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Appel d'une API personnalisée à partir du client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique explique comment appeler une API personnalisée à partir d'une application Windows Phone. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client doit envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Ajout de Mobile Services à une application existante](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) . À cet effet, vous allez exécuter la procédure suivante:

1. [Définition de l'API personnalisée]
2. [Mise à jour de l'application pour appeler l'API personnalisée]
3. [Test de l'application] 

Ce didacticiel se base sur l'exemple GetStartedWithData, une application TodoList simple. Avant de commencer, vous devez suivre le didacticiel [Ajout de Mobile Services à une application existante](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/).

## <a name="define-custom-api"></a>Définition de l'API personnalisée

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-windows-phone-call-custom-api](../includes/mobile-services-windows-phone-call-custom-api.md)]


## Étapes suivantes

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application Windows Phone, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations:

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur la création d'API personnalisées.

* [Stockage de scripts serveur dans un contrôle de code source]
  <br/> Apprenez à utiliser la fonctionnalité de contrôle de code source pour développer et publier plus facilement et de manière plus sécurisée le code de script d'API personnalisées.

<!-- Anchors. -->
[Définition de l'API personnalisée]: #define-custom-api
[Mise à jour de l'application pour appeler l'API personnalisée]: #update-app
[Test de l'application]: #test-app
[Étapes suivantes]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/

[Stockage de scripts serveur dans un contrôle de code source]: /fr-fr/documentation/articles/mobile-services-store-scripts-source-control


<!--HONumber=42-->
