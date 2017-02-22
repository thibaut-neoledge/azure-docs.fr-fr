---
title: "Forum aux questions sur l’accès conditionnel Azure Active Directory | Microsoft Docs"
description: "Forum Aux Questions concernant l’accès conditionnel  "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3980b99206c9f0c81ba3d03778777878e25db7c8


---
# <a name="azure-active-directory-conditional-access-faq"></a>Forum aux questions sur l’accès conditionnel Azure Active Directory
## <a name="which-applications-work-with-conditional-access-policies"></a>A quelles applications les stratégies d’accès conditionnel s’appliquent-elles ?
**R :** Consultez la rubrique [Accès conditionnel - applications prises en charge](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Les stratégies d’accès conditionnel s’appliquent-elles à la collaboration B2B et aux utilisateurs invités ?
**R :** Les stratégies sont appliquées aux utilisateurs dans le cadre d’une collaboration B2B. Toutefois, dans certains cas, un utilisateur peut ne pas remplir les conditions de la stratégie, par exemple si son organisation ne prend pas en charge l’authentification multifacteur. 

La stratégie ne s’applique actuellement pas aux utilisateurs SharePoint invités. Comme la relation d’invité est conservée au sein de SharePoint, les comptes d’invités ne bénéficient pas des stratégies d’accès au niveau du serveur d’authentification. L’accès invité peut être géré au niveau de SharePoint.

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>La stratégie SharePoint Online s’applique-t-elle également à OneDrive Entreprise ?
**R :** Oui.

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Pourquoi ne puis-je pas définir une stratégie pour les applications clientes telles que Word ou Outlook ?
**R :** La stratégie d’accès conditionnel définit les conditions requises pour accéder à un service et s’applique lorsque l’authentification est effectuée sur ce service. La stratégie n’est donc pas définie directement sur une application cliente, mais appliquée lorsqu’elle est appelée dans un service. Par exemple, une stratégie définie sur SharePoint s’applique aux clients qui appellent SharePoint, et la stratégie définie sur Exchange s’applique à Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>La stratégie d’accès conditionnel s’applique-t-elle aux comptes de service ?
**R :** Les stratégies d’accès conditionnel s’appliquent à tous les comptes d’utilisateurs. Cela inclut les comptes d’utilisateurs utilisés comme comptes de service. Dans de nombreux cas, un compte de service qui s’exécute sans assistance n’est pas en mesure de remplir les conditions d’une stratégie. C’est par exemple le cas lorsque l’authentification multi-facteur est requise. Dans ces cas, les comptes de service peuvent être exclus d’une stratégie à l’aide des paramètres de gestion de stratégie accès conditionnel. Pour en savoir plus sur l’application d’une stratégie à des utilisateurs, rendez-vous ici.




<!--HONumber=Dec16_HO4-->


