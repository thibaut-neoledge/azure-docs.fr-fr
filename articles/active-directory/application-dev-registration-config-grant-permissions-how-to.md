---
title: "Octroyer des autorisations à une application personnalisée | Microsoft Docs"
description: "Comment octroyer des autorisations à votre application personnalisée à l’aide du portail Azure AD ou d’un paramètre d’URL"
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
ms.openlocfilehash: 336b945929f80e1a566f7cf71b40fd799a98c12d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Octroyer des autorisations à une application personnalisée

Si vous souhaitez octroyer un consentement de façon préventive sur votre application ou que vous rencontrez une erreur indiquant que vous n’avez pas donné votre consentement pour une application, essayez les étapes ci-dessous.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Donner un consentement d’administrateur pour une application

Cette opération a pour effet d’accorder un consentement à l’application pour tous les utilisateurs de votre organisation.

1. Accédez au panneau **Inscriptions d’applications** en tant qu’**administrateur général**, puis sélectionnez l’application.

2. Sélectionnez **Autorisations requises** et appuyez sur le bouton **Accorder des autorisations** en haut du panneau.

Vous pouvez également créer une demande que vous envoyez à *login.microsoftonline.com* avec les configurations de votre application, à laquelle vous ajoutez *&prompt=admin\_consent*. Une fois que la connexion a été effectuée avec les informations d’identification d’administrateur, l’application reçoit le consentement pour tous les utilisateurs.

## <a name="how-to-force-user-consent-for-your-application"></a>Forcer le consentement d’utilisateur pour votre application

* Ajoutez des demandes d’authentification *&prompt=consent* qui nécessitent que les utilisateurs finaux donnent leur consentement chaque fois qu’ils s’authentifient.

## <a name="next-steps"></a>Étapes suivantes

[Consentement et intégration d’applications pour Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

[Consentement et octroi d’autorisations pour les applications convergées Azure AD v2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
