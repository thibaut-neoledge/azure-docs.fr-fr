---
title: Application Extensions - Azure AD B2C | Microsoft Docs
description: "Restauration de l’application b2c-extensions-app"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: f0392e32-0771-473c-a799-81438ca2bcff
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/17/2017
ms.author: parja
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 17500b572a0e92c1c233c6967840a5b6d96e21cb
ms.contentlocale: fr-fr
ms.lasthandoff: 08/17/2017

---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C : application Extensions

Lors de la création d’un annuaire Azure AD B2C, une application appelée `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` est automatiquement créée dans celui-ci. L’application **b2c-extensions-app** est visible sous *Inscriptions des applications*. Elle est utilisée par le service Azure AD B2C pour stocker des informations sur les utilisateurs et les attributs personnalisés. Si l’application est supprimée, Azure AD B2C ne pourra plus fonctionner correctement et votre environnement de production en sera affecté.

> [!IMPORTANT]
> Ne supprimez pas l’application b2c-extensions-app, sauf si vous prévoyez de supprimer immédiatement votre locataire. Si l’application reste supprimée pendant plus de 30 jours, les informations utilisateur sont définitivement perdues.

## <a name="verifying-that-the-extensions-app-is-present"></a>Vérification de la présence de l’application Extensions

Pour vérifier que l’application b2c-extensions-app est présente :

1. Dans votre locataire Azure AD B2C, cliquez sur **Autres services** dans le menu de navigation de gauche.
1. Recherchez et ouvrez **Inscriptions des applications**.
1. Recherchez une application dont le nom commence par **b2c-extensions-app**

## <a name="recover-the-extensions-app"></a>Récupérer l’application Extensions

Si vous avez supprimé accidentellement b2c-extensions-app, vous avez 30 jours pour la récupérer. Vous pouvez restaurer l’application à l’aide de l’API Graph :

1. Accédez à [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Ouvrez une session en tant qu’administrateur général pour l’annuaire Azure AD B2C dans lequel restaurer l’application supprimée.
1. Exécutez une requête HTTP GET sur l’URL `https://graph.windows.net/{tenantName}.onmicrosoft.com/deletedApplications` avec les paramètres suivants : api-version=1.6. Remplacez `{tenantName}` par le nom de votre locataire. Cette opération liste toutes les applications qui ont été supprimées au cours des 30 derniers jours.
1. Dans la liste, recherchez l’application dont le nom commence par « b2c-extension-app », puis copiez sa valeur de propriété `objectid`.
1. Exécutez une requête HTTP POST sur l’URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`. Remplacez la partie `{OBJECTID}` de l’URL par le `objectid` de l’étape précédente. 

Vous devez maintenant être en mesure de [voir l’application restaurée](#verifying-that-the-extensions-app-is-present) dans le portail Azure.

> [!NOTE]
> Une application ne peut être restaurée que dans les 30 jours suivant sa suppression. Après ce délai, les données sont définitivement perdues. Pour obtenir de l’aide, soumettez un ticket de support.
