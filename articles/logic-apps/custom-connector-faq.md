---
title: "FAQ sur le connecteur personnalisé - Azure Logic Apps | Microsoft Docs"
description: "FAQ sur la configuration requise, les déclencheurs et autres concernant la création de connecteurs personnalisés."
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>FAQ : connecteurs personnalisés

## <a name="requirements"></a>Configuration requise

**Q :** Puis-je créer un connecteur sans API REST ? </br>
**R :** Non. Pour créer un connecteur, vous devez prendre en charge des API HTTP REST stables pour votre service. 

**Q :** Quels outils puis-je utiliser pour créer un connecteur ? </br>
**R :** Azure dispose de fonctionnalités et de services qui permettent d’exposer n’importe quel service comme une API, notamment Azure App Service pour l’hébergement, la Gestion des API et bien plus encore.

**Q :** Quels sont les types d’authentification pris en charge ? </br>
**R :** Vous pouvez utiliser ces standards d’authentification pris en charge :

* [OAuth 2.0](https://oauth.net/2/), y compris [Azure Active Directory](https://azure.microsoft.com/develop/identity/) ou des services spécifiques, notamment Dropbox, GitHub et Salesforce
* Generic OAuth 2.0
* [Authentification de base](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [Clé API](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>Déclencheurs

**Q :** Puis-je générer des déclencheurs sans Webhooks ? </br>
**R :** Non. Les connecteurs personnalisés pour Azure Logic Apps et Microsoft Flow ne prennent en charge que les déclencheurs par Webhooks. Si vous souhaitez demander d’autres modèles de mise en œuvre, contactez [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) en décrivant votre API.

## <a name="certification"></a>Certification

**Q :** Je ne suis ni partenaire Microsoft, ni éditeur de logiciels indépendant (ISV). Puis-je quand même créer des connecteurs ? </br>
**R :** Oui. Vous pouvez inscrire ces connecteurs en vue d’un usage interne dans votre organisation. En revanche, si vous souhaitez certifier et publier un connecteur, vous devez soit posséder le service sous-jacent, soit présenter des droits explicites d’utilisation de l’API.

## <a name="other"></a>Autres

**Q :** Mes API utilisent un hôte dynamique. Comment les implémenter avec OpenAPI ? </br>
**R :** Les connecteurs personnalisés ne prennent pas en charge les hôtes dynamiques. Utilisez plutôt un hôte statique pour le développement et les tests. Si vous voulez certifier votre connecteur, demandez des informations sur l’implémentation dynamique à votre contact Microsoft.

**Q :** Prenez-vous en charge Postman Collection V2 ? </br>
**R :** Non. Seul Postman Collection V1 est pris en charge pour le moment.

**Q :** Prenez-vous en charge OpenAPI 3.0 ? </br>
**R :** Non, seul OpenAPI 2.0 est pris en charge pour le moment.