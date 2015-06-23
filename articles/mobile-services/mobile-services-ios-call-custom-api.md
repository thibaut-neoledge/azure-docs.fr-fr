<properties 
	pageTitle="Appel d'une API personnalisée à partir d'une application iOS | Mobile Services" 
	description="Découvrez comment définir une API personnalisée et l'appeler depuis une application iOS qui utilise Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	Writer="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Appel d'une API personnalisée à partir du client

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

Cette rubrique explique comment appeler une API personnalisée à partir d'une application iOS. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur  `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client doit envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services] ou [Prise en main des données]. À cet effet, vous allez exécuter la procédure suivante :

1. [Définition de l'API personnalisée]
2. [Mise à jour de l'application pour appeler l'API personnalisée]
3. [Test de l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services]. Ce didacticiel requiert le [Kit de développement logiciel (SDK) Mobile Services iOS](https://go.microsoft.com/fwLink/p/?LinkID=266533) et [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) et iOS 5.0 ou une version ultérieure.

## <a name="define-custom-api"></a>Définition de l'API personnalisée

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](mobile-services-ios-call-custom-api.md)]

## Étapes suivantes

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application iOS, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur la création d'API personnalisées.

* [Stockage de scripts serveur dans un contrôle de code source]
  <br/> Découvrez comment utiliser la fonctionnalité de contrôle de code source pour développer et publier plus facilement et de manière plus sécurisée un code de script d'API personnalisé.

<!-- Anchors. -->
[Définition de l'API personnalisée]: #define-custom-api
[Mise à jour de l'application pour appeler l'API personnalisée]: #update-app
[Test de l'application]: #test-app
[Étapes suivantes]: #next-steps

<!-- URLs. -->
[Notifications Push de Windows & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Tableau de bord Mes applications]: http://go.microsoft.com/fwlink/?LinkId=262039
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-ios-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-ios-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-ios-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-ios-get-started-push/
[Stockage de scripts serveur dans un contrôle de code source]: /fr-fr/documentation/articles/mobile-services-store-scripts-source-control



<!--HONumber=42-->
 