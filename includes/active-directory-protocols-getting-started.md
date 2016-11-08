---
title: Vue d’ensemble du protocole Azure AD .NET | Microsoft Docs
description: Comment utiliser des messages HTTP pour autoriser l’accès aux applications web et API web dans votre client à l’aide d’Azure AD.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/21/2016
ms.author: priyamo

---
<!--TODO: Introduction -->

## Inscrire votre application avec votre client AD
Tout d’abord, vous devez inscrire votre application avec votre client Active Directory. Ceci vous fournira un ID client pour votre application et lui permettra de recevoir des jetons.

* Connectez-vous au portail de gestion Azure.
* Dans le volet de navigation gauche, cliquez sur **Active Directory**.
* Sélectionnez un client dans lequel inscrire l’application.
* Cliquez sur l’onglet **Applications**, puis sur **Ajouter** dans le menu déroulant inférieur.
* Suivez les invites et créez une application. Pour ce didacticiel, il peut s’agir d’une application web ou d’une application native. Cependant, si vous souhaitez obtenir des exemples spécifiques pour les applications web ou les applications natives, consultez nos rubriques de démarrage rapide [ici](../articles/active-directory/active-directory-developers-guide.md).
* Pour les applications web, indiquez **l’URL de connexion**, qui est l’URL de base de votre application, à laquelle les utilisateurs peuvent se connecter, par exemple `http://localhost:12345`. Un **URI ID d’application** est un identificateur unique pour votre application. L’usage est d’utiliser `https://<tenant-domain>/<app-name>`, par exemple `https://contoso.onmicrosoft.com/my-first-aad-app`.
* Pour les applications natives, indiquez un **URI de redirection**, qui sera utilisé par Azure AD pour retourner les réponses de jeton. Entrez une valeur spécifique à votre application, par exemple, `http://MyFirstAADApp`.
* Une fois l’inscription terminée, AAD affecte un identificateur client unique à votre application. Copiez cette valeur à partir de l’onglet **Configurer** de votre application, car vous en aurez besoin dans les sections suivantes.

<!---HONumber=AcomDC_0601_2016-->