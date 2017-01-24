---
title: "Vue d’ensemble du protocole Azure AD .NET | Documents Microsoft"
description: "Comment utiliser des messages HTTP pour autoriser l’accès aux applications web et API web dans votre client à l’aide d’Azure AD."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: f1e4b86a04a76513a2f0d9a9f89e49611c0447d5
ms.openlocfilehash: b31fa50a62d5b26a7346f212076ec3a2b0386f5e


---
<!--TODO: Introduction -->

## <a name="register-your-application-with-your-ad-tenant"></a>Inscrire votre application avec votre client AD
Tout d’abord, vous devez inscrire votre application avec votre client Azure Active Directory (Azure AD). Ceci vous fournira un ID d’application pour votre application et lui permettra de recevoir des jetons.

* Connectez-vous au [portail Azure](https://portal.azure.com).
* Sélectionnez votre client Azure AD en cliquant sur votre compte dans le coin supérieur droit de la page.
* Dans le volet de navigation gauche, cliquez sur **Azure Active Directory**.
* Cliquez sur **Inscriptions des applications**, puis sur **Ajouter**.
* Suivez les invites et créez une application. Pour ce didacticiel, il peut s’agir d’une application web ou d’une application native. Cependant, si vous souhaitez obtenir des exemples spécifiques pour les applications web ou les applications natives, consultez nos rubriques de [démarrage rapide](../articles/active-directory/develop/active-directory-developers-guide.md).
  * Pour les applications web, indiquez l’**URL de connexion**, c’est-à-dire l’URL de base de votre application, à laquelle les utilisateurs peuvent se connecter, par exemple `http://localhost:12345`.
<!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->
  * Pour les applications natives, indiquez un **URI de redirection**, qui sera utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique à votre application, par exemple, `http://MyFirstAADApp`
* Une fois l’inscription terminée, Azure AD affecte un identificateur client unique à votre application, l’ID d’application. Copiez cette valeur à partir de la page de l’application, car vous en aurez besoin dans les sections suivantes.



<!--HONumber=Jan17_HO3-->


