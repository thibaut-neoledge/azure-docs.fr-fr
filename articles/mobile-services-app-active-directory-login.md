<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="dwrede" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="dwrede"></tags>

# Inscription de vos applications à des fins d'utilisation d'une connexion via un compte Azure Active Directory

Cette rubrique montre comment inscrire vos applications afin d'utiliser Azure Active Directory comme fournisseur d'authentification pour Azure Mobile Services.

<div class="dev-callout"><b>Remarque</b>
<p>Lorsque vous pr&eacute;voyez de fournir &eacute;galement une authentification pilot&eacute;e par le client pour l'authentification unique (SSO) ou les notifications Push &agrave; partir d'une application Windows Store, pensez aussi &agrave; inscrire votre application aupr&egrave;s du Windows Store. Pour plus d'informations, consultez la page <a href="/fr-fr/develop/mobile/how-to-guides/register-for-single-sign-on">Inscription de vos applications Windows Store pour l'authentification Windows Live Connect</a>.</p>
</div>

1.  Connectez-vous au [portail de gestion Azure][].

2.  Dans le portail de gestion, accédez à **Active Directory**, puis cliquez sur votre annuaire.

	![][]

1.  Cliquez sur l'onglet **Applications**, puis sur **Ajouter une application**.

	![][1]

1.  Suivez les instructions de l'Assistant Ajout d'application en choisissant **Application Web et/ou API Web** pour le XXX. Activez l'authentification unique. Lorsque vous êtes invité à entrer l'**URL de l'application**, collez l'URL de l'application des services mobiles.

2.  \*\*\* TEXTE À VENIR \*\*\*

Vous pouvez désormais utiliser un compte Azure Active Directory pour l'authentification dans votre application en fournissant les valeurs de l'ID client et de la clé secrète client à Mobile Services.

  [Inscription de vos applications Windows Store pour l'authentification Windows Live Connect]: /fr-fr/develop/mobile/how-to-guides/register-for-single-sign-on
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png
