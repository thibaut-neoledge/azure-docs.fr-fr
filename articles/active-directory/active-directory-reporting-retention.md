---
title: "Stratégies de rétention des rapports Azure Active Directory | Microsoft Docs"
description: "Stratégies de rétention des données de rapport dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 8a7d1bd1db8abdac5b741d79b10c9b363afecd24
ms.openlocfilehash: 9f8d9c845c391ec24aa3b265aa3078840fe3240a


---
# <a name="azure-active-directory-report-retention-policies"></a>Stratégies de rétention des rapports Azure Active Directory
*Cette documentation fait partie du guide [Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).*


Cette rubrique fournit des réponses aux questions plus courantes en rapport avec la conservation des données pour les différents rapports d’activité dans Azure Active Directory. 

Comment obtenir la collection des données d’activité démarrées ?

| Édition d’Azure AD | Début de la collection |
| :--              | :--   |
|Premium et Premium 2 | Lorsque vous vous inscrivez pour une licence |
| Gratuit | La première fois que vous ouvrez le [panneau Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) ou utilisez les [API de création de rapports](https://aka.ms/aadreports)  |


Quand vos données d’activité sont-elles disponibles dans le portail Azure ?

- **Immédiatement** : si vous avez déjà travaillé avec des rapports dans le portail Azure Classic
- **Dans les 2 heures** : si vous n’avez pas encore activé la création de rapports dans le portail Azure Classic

Comment obtenir la collection des signaux de sécurité démarrés ?  
Pour les signaux de sécurité, le processus de collection démarre lorsque vous choisissez d’utiliser Identity Protection Center. 

Pendant combien de temps les données collectées sont stockées ?

**Rapports d’activité**    

| Rapport | Azure AD Gratuit | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Audit de répertoire | 7 jours | 30 jours | 30 jours |
| Activité de connexion |    7 jours | 30 jours | 30 jours |

**Signaux de sécurité**

| Rapport | Azure AD Gratuit | Azure AD Premium 1 | Azure AD Premium 2 |
| :--    | :--           | :--                | :--                |
| Connexions risquées | 7 jours | 30 jours | 90 jours |





<!--HONumber=Dec16_HO4-->


