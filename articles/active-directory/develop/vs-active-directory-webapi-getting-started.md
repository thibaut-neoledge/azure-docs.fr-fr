---
title: "Prise en main d’Azure AD dans les projets Visual Studio WebApi | Microsoft Docs"
description: "Comment prendre en main Azure Active Directory dans les projets WebApi après s’être connecté à un annuaire Azure AD ou avoir créé un annuaire Azure AD à l’aide des services connectés de Visual Studio"
services: active-directory
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f3f8292eb505c73b5fda86499581fe85ad3f8e47
ms.openlocfilehash: b42bd57c8a7dde854208c65f4477327fbf1108a4


---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Prendre en main Azure Active Directory et les services connectés de Visual Studio (projets WebApi)
> [!div class="op_single_selector"]
> * [Prise en main](vs-active-directory-webapi-getting-started.md)
> * [Que s'est-il passé ?](vs-active-directory-webapi-what-happened.md)
> 
> 

### <a name="requiring-authentication-to-access-controllers"></a>Demander une authentification pour l'accès aux contrôleurs
Tous les contrôleurs de votre projet ont été dotés de l'attribut **Authorize** . Cet attribut permet de demander à l’utilisateur de s’authentifier avant d’accéder aux API définies par ces contrôleurs. Pour autoriser un accès anonyme au contrôleur, cet attribut doit être supprimé du contrôleur. Pour définir plus précisément les autorisations, appliquez cet attribut à chaque méthode nécessitant une autorisation, au lieu de l’appliquer à la classe de contrôleur.

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/)




<!--HONumber=Jan17_HO5-->


