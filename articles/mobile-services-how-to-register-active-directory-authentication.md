<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Inscription de vos applications à des fins d'utilisation d'une connexion via un compte Azure Active Directory

Cette rubrique montre comment inscrire vos applications afin d'utiliser Azure Active Directory comme fournisseur d'authentification pour Azure Mobile Services.

> [WACOM.NOTE] If you want to provide client-driven authentication for single sign-on (SSO) with Azure Active Directory, see the [Authenticate your app with Active Directory Authentication Library Single Sign-On][] tutorial.

1.  Connectez-vous au [portail de gestion Azure][], cliquez sur **Mobile Services**, puis sur le service mobile.

    ![][]

2.  Cliquez sur l'onglet **Identité** de votre service mobile.

    ![][1]

3.  Faites défiler jusqu'à la section du fournisseur d'identité **Azure active directory** et copiez l'**URL de l'application** qui y est répertoriée.

    ![][2]

4.  Dans le portail de gestion, accédez à **Active Directory**, puis cliquez sur votre annuaire.

    ![][3]

5.  Cliquez en haut sur l'onglet **Applications**, puis cliquez pour **AJOUTER** une application.

    ![][4]

6.  Cliquez sur **Ajouter une application développée par mon organisation**.

7.  Dans l'Assistant Ajout d'application, entrez un **Nom** pour votre application et cliquez sur le type **Application Web et/ou API Web**. Ensuite, cliquez pour continuer.

    ![][5]

8.  Dans la zone **URL de connexion**, collez l'ID d'application que vous avez copié dans les paramètres du fournisseur d'identité Active Directory de votre service mobile. Entrez le même identificateur de ressource unique dans la zone **URI ID d'application**. Ensuite, cliquez pour continuer.

    ![][6]

9.  Une fois que l'application a été ajoutée, cliquez sur l'onglet **Configurer**. puis cliquez sur l'**ID client** de l'application.

    Si vous avez créé le service mobile pour utiliser le service principal .Net sur votre service mobile, remplacez également l'**URL de réponse** sous **Authentification unique** par l'URL de votre service mobile, suivie du chemin *signin-aad*. Par exemple, `https://todolist.azure-mobile.net/signin-aad`

    ![][7]

10. Revenez sous l'onglet **Identité** de votre service mobile. En bas, collez le paramètre **ID client** du fournisseur d'identité Azure Active Directory.

11. Dans la liste **Locataires autorisés**, vous devez ajouter le domaine du répertoire dans lequel vous avez inscrit l'application (ex. : contoso.onmicrosoft.com). Vous pouvez trouver votre nom de domaine par défaut en cliquant sur l'onglet **Domaines** de votre domaine Active Directory.

    ![][8]

    Ajoutez votre nom de domaine à la liste **Locataires autorisés**, puis cliquez sur **Enregistrer**.

    ![][9]

Vous êtes maintenant prêt à utiliser Azure Active Directory pour l'authentification dans votre application.

  [Authenticate your app with Active Directory Authentication Library Single Sign-On]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
