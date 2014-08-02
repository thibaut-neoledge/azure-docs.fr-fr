<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="" />

Inscription de vos applications à des fins d'utilisation d'une connexion via un compte Azure Active Directory
=============================================================================================================

Cette rubrique montre comment inscrire vos applications afin d'utiliser Azure Active Directory comme fournisseur d'authentification pour Azure Mobile Services.

**Remarque**

Lorsque vous prévoyez de fournir également une authentification pilotée par le client pour l'authentification unique (SSO) ou les notifications Push à partir d'une application Windows Store, pensez aussi à inscrire votre application auprès du Windows Store. Pour plus d'informations, consultez la page [Inscription de vos applications Windows Store pour l'authentification Windows Live Connect](/en-us/develop/mobile/how-to-guides/register-for-single-sign-on).

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).

2.  Dans le portail de gestion, accédez à **Active Directory**, puis cliquez sur votre annuaire.

    ![](./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png)

3.  Cliquez sur l'onglet **Applications**, puis sur **Ajouter une application**.

    ![](./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png)

4.  Suivez les instructions de l'Assistant Ajout d'application en choisissant **Application Web et/ou API Web** pour le XXX. Activez l'authentification unique. Lorsque vous êtes invité à entrer l'**URL de l'application**, collez l'URL de l'application des services mobiles.

5.  *\*\* TEXTE À VENIR \*\**

Vous pouvez désormais utiliser un compte Azure Active Directory pour l'authentification dans votre application en fournissant les valeurs de l'ID client et de la clé secrète client à Mobile Services.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png

<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
