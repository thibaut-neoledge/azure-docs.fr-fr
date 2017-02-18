---
title: "Azure Active Directory B2C : appel d’une API web depuis une application Android | Microsoft Docs"
description: "Cet article vous expliquera comment créer une application Android de type « liste de tâches » qui appelle une API web Node.js en utilisant les jetons du porteur OAuth 2.0. L’application Android et l’API web utilisent toutes les deux Azure Active Directory B2C pour gérer les identités des utilisateurs et les authentifier."
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C : appel d’une API web depuis une application Android
> [!WARNING]
> Nous avons publié un exemple de code Android mis à jour qui se trouve [ici](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi).  Cet exemple utilise une bibliothèque tierce dont la compatibilité a été testée dans des scénarios de base avec Azure AD B2C.  Cette bibliothèque utilise des vues web incorporées au lieu du navigateur du système.  Google [a annoncé](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) qu’à partir du 20 avril 2017, la connexion via un compte Google avec des vues web incorporées ne sera plus prise en charge : la prise en charge des comptes Google nécessitera dès lors la mise à jour des bibliothèques.  

>Microsoft ne fournit pas de correctifs pour les bibliothèques tierces et ne les a pas vérifiées. Les problèmes et les demandes de fonctionnalités doivent être soumis au projet open source de la bibliothèque. Pour plus d’informations, consultez [cet article](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>




<!--HONumber=Feb17_HO1-->


