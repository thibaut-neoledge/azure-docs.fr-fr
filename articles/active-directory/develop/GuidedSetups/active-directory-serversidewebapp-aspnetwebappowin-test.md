---
title: "Prise en main d’Azure AD v2 avec les applications de serveur web ASP.NET - Test | Microsoft Docs"
description: "Implémentation de la connexion Microsoft dans une solution ASP.NET avec une application basée sur un navigateur web traditionnel utilisant le standard OpenID Connect"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 00cb963e85111274c36c3a84489894811ad2dabd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
## <a name="test-your-code"></a>Test de votre code

Appuyez sur `F5` pour exécuter votre projet dans Visual Studio. Le navigateur s’ouvre et vous redirige vers *http://localhost:{port}* où vous pouvez voir le bouton *Se connecter avec Microsoft*. Continuez et cliquez dessus pour vous connecter.

Lorsque vous êtes prêt à procéder au test, utilisez un compte professionnel ou scolaire (Azure Active Directory) ou un compte personnel (live.com, outlook.com) pour vous connecter. 

![Connexion à l’aide de la fenêtre de navigateur Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![Connexion à l’aide de la fenêtre de navigateur Microsoft](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>Résultats attendus
Après la connexion, l’utilisateur est redirigé vers la page d’accueil de votre site web qui est l’URL HTTPS spécifiée dans les informations d’inscription de votre application sur le portail d’inscription des applications de Microsoft. Cette page affiche désormais *Bonjour {utilisateur}*, ainsi qu’un lien de déconnexion et un lien pour afficher les revendications de l’utilisateur, qui est un lien vers le contrôleur d’autorisation créé précédemment.

### <a name="see-users-claims"></a>Afficher les revendications de l’utilisateur
Sélectionnez le lien hypertexte pour afficher les revendications de l’utilisateur. Cela vous permet d’accéder au contrôleur et à la vue qui est uniquement disponible pour les utilisateurs authentifiés.

#### <a name="expected-results"></a>Résultats attendus
 Vous devez voir une table contenant les propriétés de base de l’utilisateur connecté :

| Propriété | Valeur | Description|
|---|---|---|
| Nom | {Nom complet de l’utilisateur} | Prénom et nom de l’utilisateur.
|Nom d’utilisateur | <span>user@domain.com</span>| Nom d’utilisateur employé pour identifier l’utilisateur connecté.
| Objet| {Objet}|Une chaîne pour identifier de façon unique l’ouverture de session utilisateur sur le web.|
| ID client| {Guid}| Un *GUID* qui identifie l’organisation Azure Active Directory de l’utilisateur.|

En outre, vous verrez une table contenant toutes les revendications de l’utilisateur incluses dans la demande d’authentification. Pour obtenir la liste des revendications dans un jeton d’ID et des informations complémentaires, consultez l’[article](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "Liste des revendications dans un jeton d’ID").


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>Test de l’accès à une méthode disposant d’un attribut *[Authorize]* (facultatif)
Dans cette étape, vous allez tester l’accès au contrôleur authentifié en tant qu’utilisateur anonyme :<br/>
Sélectionnez le lien de déconnexion de l’utilisateur et terminez le processus de déconnexion.<br/>
À présent, dans votre navigateur, saisissez http://localhost:{port}/authenticated pour accéder à votre contrôleur qui est protégé par l’attribut `[Authorize]`.

#### <a name="expected-results"></a>Résultats attendus
Vous devriez recevoir l’invite vous demandant de vous authentifier pour afficher la vue.

## <a name="additional-information"></a>Informations supplémentaires

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Protéger l’ensemble de votre site web
Pour protéger l’ensemble de votre site web, ajoutez `AuthorizeAttribute` à `GlobalFilters` dans la méthode `Global.asax` `Application_Start` :

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **Autoriser uniquement les utilisateurs d’une seule organisation à se connecter à votre application**

> Votre application accepte par défaut des connexions de comptes personnels (y compris outlook.com, live.com et autres), ainsi que des comptes professionnels et scolaires de n’importe quelle société ou organisation ayant intégré Azure Active Directory. 

> Si vous souhaitez que votre application accepte uniquement les connexions à partir d’une seule organisation Azure Active Directory, remplacez le paramètre `Tenant` dans *web.config* défini sur `Common` par le nom du client de l’organisation, par exemple *contoso.onmicrosoft.com*. Ensuite, définissez l’argument `ValidateIssuer` dans votre *classe de démarrage OWIN* sur `true`.

> Pour autoriser uniquement les utilisateurs de certaines organisations à se connecter, définissez `ValidateIssuer` sur true et utilisez le paramètre `ValidIssuers` pour créer une liste d’organisations.

> Une autre option consiste à implémenter une méthode personnalisée pour valider les émetteurs à l’aide du paramètre IssuerValidator. Pour en savoir plus sur `TokenValidationParameters`, consultez [l’](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "article MSDN sur la propriété TokenValidationParameters").

