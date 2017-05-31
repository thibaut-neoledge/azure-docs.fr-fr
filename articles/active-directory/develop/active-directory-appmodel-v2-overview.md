---
title: Point de terminaison Azure Active Directory v2.0 | Microsoft Docs
description: "Introduction à la création d’applications avec une connexion de compte Microsoft et Azure Active Directory."
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 4a9bf4193017313f532b398880a84966367f0b58
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017


---
# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Connecter les utilisateurs de compte Microsoft et d’Azure AD dans une même application
Auparavant, un développeur d’application qui souhaitait prendre en charge à la fois les comptes Microsoft personnels et les comptes professionnels d’Azure Active Directory devait opérer une intégration avec deux systèmes distincts.  Le **point de terminaison Azure Active Directory v2.0** met en place une nouvelle version d’API d’authentification qui vous permet de connecter les deux types de comptes en utilisant une seule intégration.  Les applications qui utilisent le point de terminaison v2.0 peuvent également consommer des API REST à partir de [Microsoft Graph](https://graph.microsoft.io) à l’aide d’un des deux types de compte.

## <a name="getting-started"></a>Mise en route
Choisissez votre plateforme préférée dans la liste ci-dessous pour créer une application à l’aide de nos bibliothèques et infrastructures open source.  Vous pouvez également utiliser notre documentation du protocole OAuth 2.0 et OpenID Connect pour envoyer et recevoir les messages de protocole directement sans utiliser une bibliothèque d’authentification.

<br />

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Nouveautés
Les informations ici seront utiles pour comprendre ce qui est et ce qui n’est pas possible avec le point de terminaison v2.0.

* Découvrez les [différents types d’application que vous pouvez créer avec le point de terminaison v2.0](active-directory-v2-flows.md).
* Familiarisez-vous avec les [limites, restrictions et contraintes](active-directory-v2-limitations.md) du point de terminaison v2.0.
* Consultez cette présentation vidéo du point de terminaison v2.0 :

>[!VIDÉO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="reference"></a>Référence
Les liens ci-dessous vous seront utiles pour explorer la plateforme en profondeur :

* [Référence sur le protocole v2.0](active-directory-v2-protocols.md)
* [Référence sur le jeton v2.0](active-directory-v2-tokens.md)
* [Référence de la bibliothèque v2.0](active-directory-v2-libraries.md)
* [Étendues et consentement dans le point de terminaison v2.0](active-directory-v2-scopes.md)
* [Microsoft Graph](https://graph.microsoft.io)

## <a name="help--support"></a>Aide et support
Ce sont les ressources les plus pertinentes pour obtenir de l’aide concernant le développement sur Azure Active Directory.

* [Balises `azure-active-directory` et `adal` de Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+or+adal)
* [Commentaires sur Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)


> [!NOTE]
> Si vous ne devez connecter que des comptes professionnels et scolaires à partir d’Azure Active Directory, commencez par notre [Guide du développeur Azure AD](active-directory-developers-guide.md).  Le point de terminaison v2.0 est destiné aux développeurs qui doivent explicitement connecter des comptes personnels Microsoft.


