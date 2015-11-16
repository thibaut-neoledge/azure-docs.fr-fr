<properties 
	pageTitle="Comment configurer l'authentification Azure Active Directory pour votre application App Services" 
	description="Découvrez comment configurer l'authentification Azure Active Directory pour votre application App Services." 
	authors="mattchenderson" 
	services="app-service\mobile" 
	documentationCenter="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="10/29/2015" 
	ms.author="mahender"/>

# Comment configurer votre application pour utiliser la connexion Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Azure App Services pour utiliser Azure Active Directory comme fournisseur d'authentification.

## <a name="register"> </a>Inscription de votre application auprès d’Azure Active Directory

1. Connectez-vous à la [version préliminaire du portail de gestion Azure] et accédez à votre application Mobile App.

2. Sous **Paramètres**, cliquez sur **Authentification mobile**, puis cliquez sur **Azure Active Directory**. Copiez l’**URL de l’application** et l’**URL de réponse** qui y sont répertoriées. Vous les utiliserez ultérieurement. Assurez-vous que l’**URL de l’application** et l’**URL de réponse** utilisent le schéma HTTPS.

    ![][1]

3. Connectez-vous au [portail de gestion Azure] et accédez à **Active Directory**.

    ![][2]

4. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut de la page. Cliquez sur **AJOUTER** en bas de la page pour créer une inscription d’application.

5. Cliquez sur **Ajouter une application développée par mon organisation**.

6. Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application Web et/ou API Web**. Ensuite, cliquez pour continuer.

7. Dans la zone **URL de connexion**, collez l’ID d’application que vous avez copié dans les paramètres du fournisseur d’identité Active Directory de votre application Mobile App. Entrez cet identificateur de ressource unique dans la zone **URI ID d’application**. Ensuite, cliquez pour continuer.

8. Une fois que l'application a été ajoutée, cliquez sur l'onglet **Configurer**. Sous **Authentification unique**, définissez **URL de réponse** sur l’URL de réponse d’application Mobile App que vous avez copiée précédemment. Il s’agit normalement de la passerelle d’application Mobile App suivie de _/signin-aad_. Par exemple : `https://contosogateway.azurewebsites.net/signin-aad`. Assurez-vous d'utiliser le schéma HTTPS.

    ![][3]

9. Cliquez sur **Save**. Copiez ensuite l’**ID client** pour l’application.

## <a name="secrets"> </a>Ajout des informations Azure Active Directory à votre application Mobile App

1. Revenez à la version préliminaire du portail de gestion et au panneau de paramètres **Azure Active Directory** pour votre application Mobile App. Collez-y le paramètre **ID client** pour le fournisseur d’identité Azure Active Directory.
  
2. Dans la liste **Locataires autorisés**, vous devez ajouter le domaine du répertoire dans lequel vous avez inscrit l'application (ex. : contoso.onmicrosoft.com). Vous trouverez votre nom de domaine par défaut en cliquant sur l’onglet **Domaines** de votre client Azure Active Directory. Ajoutez votre nom de domaine à la liste **Locataires autorisés**, puis cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Authentification des utilisateurs de votre application Mobile App à l’aide de l’authentification unique Azure Active Directory : [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[version préliminaire du portail de gestion Azure]: https://portal.azure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md

<!---HONumber=Nov15_HO2-->