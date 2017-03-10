---
title: "Référence technique d’Azure Active Directory Conditional Access | Microsoft Docs"
description: "Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 4296bbc7123f6571ad564351612864a6315d8abf
ms.openlocfilehash: 450f3e001a0bc4a45fea4c4f0a81e676e9a80cc4
ms.lasthandoff: 03/02/2017


---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Référence technique d’Azure Active Directory Conditional Access
## <a name="services-enabled-with-conditional-access"></a>Services activés avec accès conditionnel
Les règles d’accès conditionnel sont prises en charge sur différents types d’application Azure AD. Cette liste comprend les éléments suivants :


* Applications inscrites auprès du proxy d’application Azure
* Application distante Azure
* Applications métiers et mutualisées développées inscrites auprès d’Azure AD
* Dynamics CRM
* Applications fédérées dans la galerie d’applications Azure AD
* Microsoft Office 365 Yammer
* Microsoft Office 365 Exchange Online
* Microsoft Office 365 SharePoint Online (y compris OneDrive Entreprise)
* Microsoft Power BI 
* Application d’authentification unique par mot de passe dans la galerie d’applications Azure AD
* Visual Studio Online


## <a name="enable-access-rules"></a>Activer les règles d’accès
Chaque règle peut être activée ou désactivée sur la base de l’application. Lorsque les règles sont activées ( **ON** ), elles s’appliquent à tous les utilisateurs qui accèdent à l’application. Lorsqu’elles sont désactivées ( **OFF** ), elles ne sont pas utilisées et n’ont aucun impact sur l’expérience de connexion de l’utilisateur.

## <a name="applying-rules-to-specific-users"></a>Application de règles à des utilisateurs spécifiques
Des règles peuvent être appliquées à des ensembles spécifiques d’utilisateurs basés sur le groupe de sécurité en définissant l’option **Appliquer à**. L’option **Appliquer à** peut être définie sur **Tous les utilisateurs** ou **Groupes**. Lorsqu’elle est définie sur **Tous les utilisateurs** , les règles s’appliquent à n’importe quel utilisateur ayant accès à l’application. L’option **Groupes** permet de sélectionner des groupes de sécurité et de distribution spécifiques, et d’appliquer les règles à ces groupes uniquement.

Lorsque vous déployez une règle, il est courant de commencer par l’appliquer à un ensemble limité d’utilisateurs, qui sont membres d’un groupe pilote. Une fois cette opération terminée, la règle peut être appliquée à **tous les utilisateurs**. Cela entraînera l’application de la règle à tous les utilisateurs de l'organisation.

Des groupes sélectionnés peuvent également être exclus de la stratégie à l’aide de l’option **Sauf** . Tous les membres de ces groupes seront exclus et ce, même s’ils apparaissent dans un groupe inclus.

## <a name="at-work-networks"></a>Réseaux Au bureau
Les règles d’accès conditionnel qui utilisent un réseau Au bureau s’appuient sur des plages d’adresses IP approuvées qui ont été configurées dans Azure AD, ou utilisent la revendication Au sein du réseau d'entreprise d’AD FS. Ces règles incluent les éléments suivants :

* Exiger l’authentification multifacteur à l’extérieur de l’entreprise
* Bloquer l’accès quand l’utilisateur n’est pas au bureau

Options pour la spécification des réseaux Au bureau

1. Configurez les plages d’adresses IP approuvées sur la [page des paramètres de l’authentification multifacteur](../multi-factor-authentication/multi-factor-authentication-whats-next.md). La stratégie d’accès conditionnel utilisera les plages configurées sur chaque demande d’authentification et l’émission de jetons pour l’évaluation des règles. 
2. Configurez l’utilisation de la revendication Au sein du réseau d’entreprise (cette option peut être utilisée avec des répertoires fédérés) à l’aide d’AD FS. Pour en savoir plus sur les revendications au sein d’un réseau d’entreprise, consultez [Adresses IP approuvées](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="rules-based-on-application-sensitivity"></a>Règles basées sur le critère de diffusion des applications
Les règles sont configurées par application, permettant ainsi aux services à valeur élevée d’être sécurisé sans affecter l’accès à d’autres services. Les règles d’accès conditionnel peuvent être configurées dans l’onglet **Configurer** de l’application. 

Règles actuellement proposées :

* **Imposer l’authentification multifacteur**
  
  * Tous les utilisateurs auxquels cette stratégie est appliquée devront s’identifier au moins une fois via l’authentification multifacteur.
* **Exiger l’authentification multifacteur à l’extérieur de l’entreprise**
  
  * Si cette stratégie est appliquée, tous les utilisateurs devront avoir effectué au moins une fois l’authentification multifacteur s’ils accèdent au service à partir d’un emplacement distant non professionnel. S’ils se déplacent du bureau vers un emplacement distant, ils devront effectuer l’authentification multifacteur lors de l’accès au service.
* **Bloquer l’accès quand l’utilisateur n’est pas au bureau** 
  
  * Lorsque les utilisateurs se déplacent de leur travail vers un emplacement distant, ils seront bloqués si la stratégie "Bloquer l'accès quand l'utilisateur n'est pas au travail" leur est appliquée.  Ils seront de nouveau autorisés à y accéder lorsqu’ils se trouvent au bureau.

## <a name="related-topics"></a>Rubriques connexes
* [Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure AD](active-directory-conditional-access.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)


