<properties 
	writer="ricksal" 
	pageTitle="Appel d'une API personnalisée à partir d'un client Android | Centre de développement mobile" 
	description="Découvrez comment définir une API personnalisée et l'appeler depuis une application Android qui utilise Microsoft Azure Mobile Services." 
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
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="ricksal,glenga"/>

# Appel d'une API personnalisée à partir du client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique montre comment appeler une API personnalisée à partir d'une application Android. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique vous permet d'envoyer une seule demande POST qui définit l'indicateur *completed* sur `true` pour toutes les tâches (éléments todo) de la table de votre service mobile. En l'absence de cette API personnalisée, le client doit envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données]. 

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez suivre le didacticiel [Prise en main de Mobile Services]. 

## <a name="define-custom-api"></a>Définition de l'API personnalisée

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

## Étapes suivantes

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application Android, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur la création d'API personnalisées.

* [Stockage de scripts serveur dans un contrôle de code source]
  <br/> Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.

<!-- Anchors. -->
[Définition de l'API personnalisée]: #define-custom-api
[Mise à jour de l'application pour appeler l'API personnalisée]: #update-app
[Test de l'application]: #test-app
[Étapes suivantes]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Kit de développement logiciel (SDK) Mobile Services pour Android]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Tableau de bord Mes applications]: http://go.microsoft.com/fwlink/?LinkId=262039
[Prise en main de Mobile Services]: /documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Prise en main des données]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[Prise en main de l'authentification]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Prise en main des notifications Push]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push/

[Stockage de scripts serveur dans un contrôle de code source]: /documentation/articles/mobile-services-store-scripts-source-control

<!--HONumber=47-->
