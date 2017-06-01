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
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: ad4160abfef748033eeb84a2f07a37124ce3b4b1
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
<!-- Workaround for Docs conversion bug -->
<ol start="9">
<li>
Remplacez `ClientId` par l’ID d’application que vous venez d’enregistrer
</li>
<li>
Remplacez `redirectUri` par l’URL SSL de votre projet
</li>
</ol>
<!-- End Docs -->

