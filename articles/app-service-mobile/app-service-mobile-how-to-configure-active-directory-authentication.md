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
	ms.date="11/20/2015" 
	ms.author="mahender"/>

# Configurer votre application App Service pour utiliser la connexion Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Azure App Services pour utiliser Azure Active Directory comme fournisseur d'authentification.

> [AZURE.NOTE]Cette rubrique décrit l'utilisation de la fonctionnalité Authentification/autorisation d'App Service. Elle remplace la passerelle App Service pour la plupart des applications. Si vous utilisez la passerelle, consultez la [méthode alternative]. Les différences qui s'appliquent à l'utilisation de la passerelle sont signalées dans des notes tout au long de cette section.


## <a name="express"></a>Configuration d'Azure Active Directory à l'aide de la configuration rapide

13. Dans le [portail de gestion Azure], accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification / Autorisation**.

14. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.

15. Cliquez sur **Azure Active Directory**, puis sur **Rapide** sous **Mode de gestion**.

16. Cliquez sur **OK** pour inscrire l'application dans Azure Active Directory. Une nouvelle inscription est alors créée. Si vous choisissez une inscription existante à la place, cliquez sur **Sélectionner une application existante** et recherchez le nom d'une inscription créée précédemment au sein de votre locataire. Cliquez sur l'inscription pour la sélectionner, puis sur **OK**. Cliquez ensuite sur **OK** dans le panneau des paramètres Azure Active Directory.

    ![][0]
	
	Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

17. (Facultatif) Pour restreindre l'accès à votre site aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Action à exécuter lorsque la demande n'est pas authentifiée** sur **Azure Active Directory**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour être authentifiées.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application.

## <a name="advanced"> </a>(Méthode alternative) Configurer manuellement Azure Active Directory avec des paramètres avancés
Vous pouvez également choisir de fournir des paramètres de configuration manuellement. Il s’agit de la solution préférée si le locataire AAD que vous voulez utiliser diffère de celui avec lequel vous vous connectez à Azure. Pour terminer la configuration, vous devez d’abord créer une inscription dans Azure Active Directory, puis fournir des informations d’inscription à App Service.

### <a name="register"> </a>Inscription de votre application auprès d’Azure Active Directory

1. Connectez-vous au [portail de gestion Azure en version préliminaire] et accédez à votre application. Copiez votre **URL**. Elle vous permettra de configurer votre application Azure Active Directory.

3. Connectez-vous au [portail de gestion Azure] et accédez à **Active Directory**.

    ![][2]

4. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut de la page. Cliquez sur **AJOUTER** en bas de la page pour créer une inscription d’application.

5. Cliquez sur **Ajouter une application développée par mon organisation**.

6. Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application Web et/ou API Web**. Ensuite, cliquez pour continuer.

7. Dans la zone **URL DE CONNEXION**, collez l'URL de l'application que vous avez copiée précédemment. Entrez cette URL dans la zone **URI ID d’application**. Ensuite, cliquez pour continuer.

8. Une fois que l'application a été ajoutée, cliquez sur l'onglet **Configurer**. Remplacez l'**URL de réponse** sous **Authentification unique** par l’URL de votre application en y ajoutant le chemin d’accès _/.auth/login/aad/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assurez-vous d'utiliser le schéma HTTPS.

    ![][3]
	
	
	> [AZURE.NOTE]Si vous utilisez la passerelle App Service au lieu de la fonction d’authentification/autorisation d'App Service, votre URL de réponse utilise à la place l'URL de la passerelle avec le chemin d’accès _/signin-aad_.


9. Cliquez sur **Save**. Copiez ensuite l’**ID client** pour l’application. Vous configurerez l’application pour utiliser cet ID plus tard.

10. Dans la barre de commandes située en bas, cliquez sur **Afficher les points de terminaison**, puis copiez l'URL du **document de métadonnées de fédération** et téléchargez ce document ou ouvrez-le dans un navigateur.

11. L'élément racine **EntityDescriptor**, doit contenir un attribut **entityID** au format `https://sts.windows.net/` suivi d’un GUID propre à votre client (appelé « ID client »). Copiez cette valeur qui servira d'**URL de l'émetteur**. Vous configurerez l’application pour utiliser cet ID plus tard.

### <a name="secrets"> </a>Ajout d'informations Azure Active Directory à votre application

> [AZURE.NOTE]Si vous utilisez la passerelle App Service, ignorez cette section et accédez à votre passerelle dans le portail. Sélectionnez **Paramètres**, **Identité**, puis choisissez **Azure Active Directory**. Collez le ClientID et ajoutez l'ID client à la liste des **locataires autorisés**. Cliquez sur **Save**.


13. Revenez au [portail de gestion Azure en version préliminaire] et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification / Autorisation**.

14. Si la fonctionnalité Authentification / Autorisation n’est pas activée, positionnez le commutateur sur **On**.

15. Cliquez sur **Azure Active Directory**, puis sur **Avancé** sous **Mode de gestion**. Collez-y les valeurs d’ID locataire et d’URL de l’émetteur que vous avez obtenues précédemment. Cliquez ensuite sur **OK**.

    ![][1]
	
	Par défaut, App Service fournit une authentification, mais ne restreint pas l'accès autorisé à votre contenu et aux API de votre site. Vous devez autoriser les utilisateurs dans votre code d'application.

17. (Facultatif) Pour restreindre l'accès à votre site aux seuls utilisateurs authentifiés par Azure Active Directory, définissez **Action à exécuter lorsque la demande n'est pas authentifiée** sur **Azure Active Directory**. Cela implique que toutes les demandes soient authentifiées. Toutes les demandes non authentifiées sont redirigées vers Azure Active Directory pour être authentifiées.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application.

## <a name="related-content"> </a>Contenu connexe

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[portail de gestion Azure en version préliminaire]: https://portal.azure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[méthode alternative]: #advanced

<!---HONumber=AcomDC_1125_2015-->