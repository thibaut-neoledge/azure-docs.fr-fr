---

title: "Prise en main de l’API de création de rapports Azure AD | Microsoft Docs"
description: "Prise en main de l&quot;API de création de rapports Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 414e6b0f23616f8f3d059f250335b0e530729741
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Prise en main de l’API de création de rapports Azure Active Directory
*Cette rubrique fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*

Azure Active Directory vous fournit plusieurs rapports. Les données de ces rapports peuvent être très utiles pour vos applications, telles que les systèmes SIEM, l’audit et les outils d’analyse décisionnelle. Les API de création de rapports Azure AD fournissent un accès par programme aux données via un ensemble d’API REST. Vous pouvez appeler ces API à partir de divers outils et langages de programmation.

Cet article vous fournit les informations dont vous avez besoin pour vous familiariser avec l’API de création de rapports Azure AD.
Dans la section suivante, vous trouverez plus d’informations sur l’utilisation des API d’audit et de connexion. Pour toutes les autres API, consultez l’article [Rapports et événements Azure AD (préversion)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview).

Si vous avez des questions, des problèmes ou des commentaires, veuillez contacter [Aide à la création de rapports AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="learning-map"></a>Carte d’apprentissage
1. **Préparer** : avant de pouvoir tester les exemples d’API, vous devez respecter la [configuration requise pour accéder à l’API de création de rapports Azure AD](active-directory-reporting-api-prerequisites.md).
2. **Explorer** : faites-vous une première impression des API de création de rapports :
   
   * [Utilisation des exemples pour l’API d’audit](active-directory-reporting-api-audit-samples.md) 
   * [Utilisation des exemples pour l’API de création de rapports sur l’activité de connexion](active-directory-reporting-api-sign-in-activity-samples.md)
3. **Personnaliser** : créez votre propre solution : 
   
   * [Utilisation de la référence d’API d’audit](active-directory-reporting-api-audit-reference.md) 
   * [Utilisation de la référence d’API de création de rapports sur l’activité de connexion](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>Étapes suivantes
Si vous voulez consulter l’ensemble des points de terminaison de l’API Azure AD Graph disponibles, accédez à [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta).


