---
title: "Prise en main d’Azure AD v2 avec les applications de serveur web ASP.NET - Configuration | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6f50ced708cb79a39e190657e4720c515c09990f
ms.contentlocale: fr-fr


---

## <a name="create-an-application-express"></a>Créer une application (Express)
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :
1. Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2.    Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3.    Assurez-vous que la case de l’option Guided Setup (Installation guidée) est activée.
4.    Suivez les instructions pour ajouter une URL de redirection à votre application.

## <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Ajoutez les informations d’inscription de l’application à votre solution (Avancé).
Maintenant, vous devez inscrire votre application dans le *portail d’inscription des applications de Microsoft* :
1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail. 
3.    Assurez-vous que la case de l’option Guided Setup (Installation guidée) est désactivée.
4.    Cliquez sur `Add Platforms`, puis sélectionnez `Web`.
5.    Retournez dans Visual Studio, puis, dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre Propriétés (si vous ne la voyez pa, appuyez sur F4).
6.    Définissez SSL activé sur `True` :<br/><br/>![Propriétés du projet](media/active-directory-serversidewebapp-aspnetwebappowin-configure/vsprojectproperties.png)<br />

7.    Copiez l’URL SSL et ajoutez cette URL à la liste d’URL de redirection dans la liste du portail d’inscription d’URL de redirection
8.    Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

```xml
<add key="ClientId" value="Enter_the_Application_Id_here" />
<add key="redirectUri" value="Enter_the_Redirect_URL_here" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
9. Remplacez `ClientId` par l’ID d’application que vous venez d’enregistrer
10. Remplacez `redirectUri` par l’URL SSL de votre projet 
<!-- End Docs -->

> Remarque
> ### <a name="restricting-users-from-only-one-organization-to-sign-in-to-your-application"></a>Autoriser uniquement les utilisateurs d’une seule organisation à se connecter à votre application
> Votre application accepte par défaut des connexions de comptes personnels (y compris outlook.com, live.com et autres), ainsi que des comptes professionnels et scolaires de n’importe quelle société ou organisation ayant intégré Azure Active Directory. Si vous souhaitez que votre application accepte uniquement les connexions à partir d’une seule organisation, remplacez le paramètre `Tenant` dans `web.config` défini sur `Common` par le nom du client de l’organisation, par exemple `contoso.onmicrosoft.com`. Ensuite, définissez l’argument *ValidateIssuer* dans votre classe de démarrage OWIN sur `true`.
Pour autoriser uniquement les utilisateurs de certaines organisations à se connecter, définissez `ValidateIssuer` sur `true` et utilisez le paramètre `ValidIssuers` pour créer une liste d’organisations.
Une autre option consiste à implémenter une méthode personnalisée pour valider les émetteurs à l’aide de `IssuerValidator parameter`. Pour plus d’informations sur `TokenValidationParameters`, consultez [cet article MSDN](https://msdn.microsoft.com/en-us/library/system.identitymodel.tokens.tokenvalidationparameters(v=vs.114).aspx).


