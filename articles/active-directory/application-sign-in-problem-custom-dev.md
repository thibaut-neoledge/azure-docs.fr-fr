---
title: "Problèmes de connexion à une application personnalisée | Microsoft Docs"
description: "Erreurs courantes pouvant vous empêcher de vous connecter à une application développée avec Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 51fac8adc2fe11aac005b4c0c0e9b67bbca8fa6e
ms.contentlocale: fr-fr
ms.lasthandoff: 04/17/2017

---

# <a name="problems-signing-in-to-an-custom-developed-application"></a>Problèmes de connexion à une application personnalisée

Plusieurs erreurs peuvent vous empêcher de vous connecter à une application. La raison principale vient du fait que les applications sont mal configurées.

## <a name="errors-related-to--misconfigured-apps"></a>Erreurs liées à des applications mal configurées

* Vérifiez que les deux configurations dans le portail correspondent à ce que vous avez dans votre application. Plus précisément, comparez l’ID de client/d’application, les URL de réponse, les clés secrètes client/clés et l’URI ID d’application.

* Comparez la ressource à laquelle vous souhaitez accéder dans le code aux autorisations configurées sous l’onglet **Ressources nécessaires** pour vérifier que vous demandez uniquement les ressources que vous avez configurées.

* Pour obtenir des informations sur des erreurs ou des problèmes similaires, consultez [Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory).

## <a name="next-steps"></a>Étapes suivantes

[Guide de développement Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Consentement et intégration d’applications à Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications>)<br>

[Consentement et octroi d’autorisations pour les applications convergées Azure AD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory>)

