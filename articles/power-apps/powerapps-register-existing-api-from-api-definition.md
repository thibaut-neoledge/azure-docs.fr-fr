<properties
	pageTitle="Créer une définition d’API Swagger 2.0 à partir d’une API dans PowerApps Enterprise | Microsoft Azure"
	description="Découvrez comment inscrire une API à partir d’une définition d’API Swagger 2.0 créée à partir d’une API existante"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Inscription d’une API à partir de la définition d’API Swagger 2.0  
De nombreuses organisations disposent déjà d’API que les utilisateurs peuvent utiliser et consommer dans leurs applications. Pour utiliser ces API dans vos applications, vous devez « enregistrer » les API dans le portail Azure à l'aide d'une API gérée, d’API existantes dans votre environnement App Service, ou en créant une API à l'aide de Swagger.

> [AZURE.SELECTOR]
- [API gérées](../articles/power-apps/powerapps-register-from-available-apis.md)
- [API dans votre ASE](../articles/power-apps/powerapps-register-api-hosted-in-app-service.md)
- [API Swagger](../articles/power-apps/powerapps-register-existing-api-from-api-definition.md)

Dans cette rubrique, nous nous concentrons sur la troisième option : **inscrire l’une de vos propres API à l’aide d’une définition d’API Swagger 2.0** créée à partir d’une API existante.

#### Conditions préalables

- Inscrivez-vous à [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Créez un [environnement App Service](powerapps-get-started-azure-portal.md)

## Inscription d’une API existante à l’aide de la définition d’API Swagger 2.0

Cette inscription est très facile. Procédez comme suit :

1. Créez la définition d’API [Swagger 2.0](http://swagger.io) de votre API existante. PowerApps utilise Swagger 2.0 en tant que format de définition d’API. Vous pouvez utiliser les outils référencés dans les [sites Web de Swagger 2.0](http://swagger.io) pour créer facilement une définition d’API Swagger 2.0. Quelques points à noter :  

	- La propriété ``host`` doit pointer vers le point de terminaison de votre API existante. N’utilisez pas de schéma ou de sous-chemins. Par exemple, entrez ``api.contoso.com``. <br/><br/>
	- La propriété ``basePath`` doit répertorier les sous-chemins d’accès de votre point de terminaison API existant, le cas échéant. Démarrez avec une barre oblique ``/``. Par exemple, entrez : ``/purchaseorderapi``.

2. Assurez-vous que votre API existante est accessible via votre environnement App Service en toute sécurité :

	1. Si vous savez comment rendre votre API accessible via Internet, vous pouvez configurer l’authentification de base HTTP entre votre environnement App Service et votre API existante. Pour plus d’informations, découvrez comment [Mettre à jour une API existante](powerapps-configure-apis.md). <br/><br/>
	2. Si vous souhaitez conserver votre API au sein du réseau de votre organisation, vous pouvez configurer un réseau virtuel dans l’environnement App Service pour accéder au réseau de votre entreprise en toute sécurité. En savoir plus sur les [environnements App Service](../app-service-web/app-service-app-service-environment-intro.md).

3. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **PowerApps**, puis sélectionnez **Gérer les API** : ![][11]
4. Dans Gérer les API, sélectionnez **Ajouter** : ![][12]
5. Dans **Ajouter une API**, entrez les propriétés de l’API :  

	- Dans **Nom**, entrez un nom pour votre API. Notez que le nom que vous entrez est inclus dans l’URL d’exécution de l’API. Utilisez un nom descriptif qui soit unique au sein de votre organisation.
	- Dans **Source**, sélectionnez **Import from Swagger 2.0 (Importer à partir de Swagger 2.0)**.

6. Dans **Définition d’API (Swagger 2.0)**, téléchargez votre fichier de définition d’API Swagger 2.0 : ![][13]
7. Sélectionnez **ADD (Ajouter)** pour terminer ces étapes.

> [AZURE.TIP] Quand vous inscrivez une API, vous l’inscrivez dans votre environnement App Service. Une fois dans l’environnement App Service, elle peut être utilisée par d’autres applications de l’environnement.

## Résumé et étapes suivantes

Dans cette rubrique, vous avez vu comment inscrire une API à partir de la définition d’API Swagger 2.0. Pour plus d’informations sur PowerApps, consultez les rubriques et ressources connexes suivantes :

- [Configurer les propriétés de l’API](powerapps-configure-apis.md)
- [Octroyer aux utilisateurs l’accès aux API](powerapps-manage-api-connection-user-access.md)
- [Créer des applications dans PowerApps](https://powerapps.microsoft.com/tutorials/)

<!--References-->
[11]: ./media/powerapps-register-existing-api-from-api-definition/registered-apis-part.png
[12]: ./media/powerapps-register-existing-api-from-api-definition/add-api-button.png
[13]: ./media/powerapps-register-existing-api-from-api-definition/add-api-blade.png

<!------HONumber=AcomDC_0309_2016-->