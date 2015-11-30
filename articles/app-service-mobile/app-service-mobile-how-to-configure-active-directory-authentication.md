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

# Configurer votre application App Service pour utiliser la connexion Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Cette rubrique montre comment configurer Azure App Services pour utiliser Azure Active Directory comme fournisseur d'authentification.


	> [AZURE.NOTE] This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. If using the gateway, please see the [alternative method]. Differences that apply to using the gateway are called out in notes throughout that section.


## <a name="express"></a>Configuration d’Azure Active Directory à l’aide de la configuration rapide

13. Dans le [portail de gestion Azure], accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification/Autorisation**.

14. Si la fonction Authentification/Autorisation n’est pas activée, positionnez le commutateur sur **Activer**.

15. Cliquez sur **Azure Active Directory**, puis sur **Rapide** sous **Mode de gestion**.

16. Cliquez sur **OK** pour inscrire l’application dans Azure Active Directory. Une nouvelle inscription est alors créée. Si vous choisissez une inscription existante à la place, cliquez sur **Sélectionner une application existante** et recherchez le nom d’une inscription précédemment créée au sein de votre locataire. Cliquez sur l’inscription pour la sélectionner, puis sur **OK**. Cliquez ensuite sur **OK** sur le panneau de paramètres Azure Active Directory.

    ![][0]
	
16. Par défaut, App Service fournit la connexion, mais ne restreint pas l’accès au contenu de votre site et des API : c’est la responsabilité de votre code d’application. Si vous souhaitez que le site soit complètement protégé par une connexion via Azure Active Directory, modifiez le choix dans le menu déroulant **Action à entreprendre lorsque la demande n’est pas authentifiée** et choisissez l’option **Azure Active Directory**. Ainsi, toutes les demandes devront être authentifiées ; les demandes non authentifiées seront redirigées vers l’écran de connexion avec Azure Active Directory.

17. Cliquez sur **Enregistrer**.

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application.

## <a name="advanced"> </a>(Méthode alternative) Configurer manuellement Azure Active Directory avec des paramètres avancés
Vous pouvez également choisir de fournir des paramètres de configuration manuellement. Il s’agit de la solution préférée si le locataire AAD que vous voulez utiliser diffère de celui avec lequel vous vous connectez à Azure. Pour terminer la configuration, vous devez d’abord créer une inscription dans Azure Active Directory, puis fournir des informations d’inscription à App Service.

### <a name="register"> </a>Inscription de votre application auprès d’Azure Active Directory

1. Connectez-vous à la [version préliminaire du portail de gestion Azure] et accédez à votre application. Copiez votre **URL**. Elle vous permettra de configurer votre application Azure Active Directory.

3. Connectez-vous au [portail de gestion Azure] et accédez à **Active Directory**.

    ![][2]

4. Sélectionnez votre annuaire, puis l’onglet **Applications** en haut de la page. Cliquez sur **AJOUTER** en bas de la page pour créer une inscription d’application.

5. Cliquez sur **Ajouter une application développée par mon organisation**.

6. Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application Web et/ou API Web**. Ensuite, cliquez pour continuer.

7. Dans la zone **URL de connexion**, collez la valeur de l’URL de l’application que vous avez copiée précédemment. Entrez cette URL dans la zone **URI ID d’application**. Ensuite, cliquez pour continuer.

8. Une fois que l'application a été ajoutée, cliquez sur l'onglet **Configurer**. Remplacez l’**URL de réponse** sous **Authentification unique** par l’URL de votre application en y ajoutant le chemin d’accès _/.auth/login/aad/callback_. Par exemple : `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Assurez-vous d'utiliser le schéma HTTPS.

    ![][3]
	
	
	> [AZURE.NOTE]Si vous utilisez la passerelle App Service au lieu de la fonction d’authentification/autorisation d’App Service, votre URL de réponse utilise à la place l’URL de la passerelle avec le chemin d’accès _/signin-aad_.


9. Cliquez sur **Save**. Copiez ensuite l’**ID client** pour l’application. Vous configurerez l’application pour utiliser cet ID plus tard.

10. Dans la barre de commandes située en bas, cliquez sur **Afficher les points de terminaison**, puis copiez l’URL du **document de métadonnées de fédération** et téléchargez ce document ou ouvrez-le dans un navigateur.

11. Dans l’élément racine **EntityDescriptor**, il doit y avoir un attribut **entityID** au format `https://sts.windows.net/` suivi d’un GUID propre à votre locataire (appelé « ID locataire »). Copiez cette valeur qui servira d’**URL de l’émetteur**. Vous configurerez l’application pour utiliser cet ID plus tard.

### <a name="secrets"> </a>Ajout des informations Azure Active Directory à votre application


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Azure Active Directory**. Paste in the ClientID and add the tenant ID to the **Allowed Tenants** list. Click **Save**.


13. Revenez au [portail de gestion Azure en version préliminaire] et accédez à votre application. Cliquez sur **Paramètres**, puis sur **Authentification/Autorisation**.

14. Si la fonction Authentification/Autorisation n’est pas activée, positionnez le commutateur sur **Activer**.

15. Cliquez sur **Azure Active Directory**, puis sur **Avancé** sous **Mode de gestion**. Collez-y les valeurs d’ID locataire et d’URL de l’émetteur que vous avez obtenues précédemment. Cliquez ensuite sur **OK**.

    ![][1]
	
16. Par défaut, App Service fournit la connexion, mais ne restreint pas l’accès au contenu de votre site et des API : c’est la responsabilité de votre code d’application. Si vous souhaitez que le site soit complètement protégé par une connexion via Azure Active Directory, modifiez le choix dans le menu déroulant **Action à entreprendre lorsque la demande n’est pas authentifiée** et choisissez l’option **Azure Active Directory**. Ainsi, toutes les demandes devront être authentifiées ; les demandes non authentifiées seront redirigées vers l’écran de connexion avec Azure Active Directory.

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
[version préliminaire du portail de gestion Azure]: https://portal.azure.com/
[portail de gestion Azure]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[alternative method]: #advanced

<!---HONumber=Nov15_HO4-->