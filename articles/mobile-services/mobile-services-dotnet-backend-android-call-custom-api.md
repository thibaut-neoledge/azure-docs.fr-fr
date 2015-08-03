<properties 
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
	ms.date="06/03/2015" 
	ms.author="ricksal"/>

# Appel d'une API personnalisée à partir du client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique montre comment appeler une API personnalisée à partir d'une application Android. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON.

L'API personnalisée créée dans cette rubrique vous permet d'envoyer une seule requête POST qui définit l'indicateur *completed* sur `true` pour toutes les tâches (éléments todo) de la table de votre service mobile. En l'absence de cette API personnalisée, le client devrait envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données].

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services].

## <a name="define-custom-api"></a>Définition de l'API personnalisée

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]

## Étapes suivantes

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application Android, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

* [Référence des scripts serveur Mobile Services] <br/>Familiarisez-vous avec la création des API personnalisées.

* [Stockage des scripts serveur dans le contrôle du code source ] <br/> Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[Référence des scripts serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Prise en main de Mobile Services]: mobile-services-dotnet-backend-android-get-started.md
[Prise en main des données]: mobile-services-dotnet-backend-android-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md

[Stockage des scripts serveur dans le contrôle du code source ]: mobile-services-store-scripts-source-control.md
 

<!---HONumber=July15_HO4-->