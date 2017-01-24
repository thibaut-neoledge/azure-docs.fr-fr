---
title: "Activation d&quot;applications clientes natives de manière à ce qu&quot;elles interagissent avec des applications proxy | Microsoft Docs"
description: "Explique comment activer des applications clientes natives de manière à communiquer avec le connecteur de proxy d&quot;application Azure AD pour fournir un accès à distance sécurisé à vos applications locales."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: f0cae145-e346-4126-948f-3f699747b96e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e65393c9582056f84530a32804e0d82fd451b688
ms.openlocfilehash: 34cacff4e8b13fa9d91387ca8762439908ed01fd


---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Activation d'applications clientes natives de manière à ce qu'elles interagissent avec des applications proxy
Le proxy d'application Azure Active Directory est largement utilisé pour publier des applications de navigateur telles que SharePoint, Outlook Web Access et des applications métier personnalisées. Il peut également être utilisé pour publier des applications clientes natives, qui diffèrent des applications web, car celles-ci sont installées sur un appareil. Pour cela, la prise en charge de jetons générés par Azure AD qui sont envoyés dans des en-têtes d'autorisation HTTP standard est nécessaire.

![Relation entre les utilisateurs finaux, Azure Active Directory et les applications publiées](./media/active-directory-application-proxy-native-client/richclientflow.png)

La méthode recommandée pour publier de telles applications consiste à utiliser la bibliothèque d’authentification Azure AD, qui prend en charge toutes les contraintes liées à l’authentification pour de nombreux environnements client différents. Le proxy d'application est conforme au [scénario Application Native vers API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api). Pour ce faire, procédez comme suit :

## <a name="step-1-publish-your-application"></a>Étape 1 : publier votre application
Publiez votre application proxy comme vous le feriez pour toute autre application, affectez des utilisateurs et affectez-leur des licences premium ou de base. Pour plus d'informations, consultez [Publier des applications avec le proxy d'application](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Étape 2 : configurer votre application
Configurez votre application native comme suit :

1. Connectez-vous à la version classique du portail Azure.
2. Cliquez sur l’icône d’Active Directory dans le menu de gauche, puis cliquez sur le répertoire souhaité.
3. Dans le menu supérieur, cliquez sur **Applications**. Si aucune application n’a été ajoutée à votre répertoire, cette page affiche uniquement le lien **Ajouter une application** . Cliquez sur le lien ou cliquez sur le bouton **Ajouter** dans la barre de commandes.
4. Sur la page **Que voulez-vous faire**, cliquez sur le lien vers **Ajouter une application développée par mon organisation**.
5. Dans la page **Parlez-nous de votre application**, spécifiez un nom pour votre application et sélectionnez **Application cliente native**. Cliquez sur l’icône en forme de flèche pour continuer.
6. Sur la page **Informations de l’application**, fournissez **l’URI de redirection** pour l’application cliente native, puis cliquez sur la coche pour terminer.

Votre application a été ajoutée, et vous allez être redirigé vers la page de démarrage rapide pour votre application.

## <a name="step-3-grant-access-to-other-applications"></a>Étape 3: accorder l’accès à d’autres applications
Activez l'application native à exposer à d'autres applications dans votre répertoire :

1. Dans le menu principal, cliquez sur **Applications**, sélectionnez la nouvelle application native, puis cliquez sur **Configurer**.
2. Faites défiler la page jusqu'à la section **Autorisations pour d'autres applications** . Cliquez sur le bouton **Ajouter une application** , puis sélectionnez l'application de proxy à laquelle vous souhaitez accorder l'accès à l'application native et cochez la case dans le coin inférieur droit. Dans la liste déroulante **Autorisations déléguées** , sélectionnez la nouvelle autorisation.

![Capture d’écran Autorisations pour d’autres applications - ajouter une application](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Étape 4 : modifier la bibliothèque d’authentification Active Directory
Modifiez le code de l'application native dans le contexte de l'authentification de l'Active Directory Authentication Library (ADAL) de manière à inclure les éléments suivants :

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Les variables doivent être remplacées comme suit :

* Le **TenantId** se trouve dans le GUID dans l'URL de la page de **configuration** de l'application, juste après « /Directory/ ».
* **L'URL de front-end** est l'URL de front-end entré dans l'application de Proxy et de se trouve sur la page de **Configuration** de l'application proxy.
* **L’ID client** de l'application native est indiqué sur la page de **configuration** page de l'application native.
* **L’URI de redirection de l'application native** est indiqué sur la page de **configuration** de l'application native.

![Capture d’écran de la page de configuration d’une nouvelle application native](./media/active-directory-application-proxy-native-client/new_native_app.png)

Pour plus d’informations sur le flux d’application native, consultez [Application native vers API Web](develop/active-directory-authentication-scenarios.md#native-application-to-web-api).

## <a name="see-also"></a>Voir aussi
* [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
* [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
* [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)

Pour les dernières nouvelles et mises à jour, visitez [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Jan17_HO3-->


