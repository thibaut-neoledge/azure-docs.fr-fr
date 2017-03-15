---
title: "Trouver les rapports d’activité sur le Portail Azure | Microsoft Docs"
description: "Découvrez comment trouver les rapports d’activité sur le Portail Azure."
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/17/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: afd48a77faeeab6d5a8730934345cb7000a59831
ms.openlocfilehash: e72e49140aaad55a6e66a9f331ce7fde20b8577c
ms.lasthandoff: 02/23/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Trouver les rapports d’activité sur le Portail Azure

Avec la migration du Portail Azure Classic vers le Portail Azure, nous vous offrons une nouvelle présentation des journaux d’activité Azure Active Directory. Nous avons publié un [billet de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) il y a quelques mois pour expliquer comment nous fournissons des journaux d’activité dans le contexte de la ressource sur laquelle vous travaillez. Cet article décrit la façon dont nous avons transposé les rapports existants du Portail Azure Classic vers le nouveau monde du Portail Azure.

## <a name="what-is-new"></a>Quelles sont les nouveautés ?

Les rapports du Portail Azure Classic sont répartis en différentes catégories :

1.    Rapports de sécurité
2.    Rapports d’activité
3.    Rapports d’application intégrée

### <a name="activity-and-integrated-app-reports"></a>Rapports d’activité et d’application intégrée

Lors du passage aux rapports contextuels du Portail Azure, nous avons fusionné les rapports existants dans un seul et même affichage avec une seule API sous-jacente qui fournit les données affichées. 

Vous pouvez accéder à cette vue dans **Journaux d’audit** dans la section **Activité** du panneau **Azure Active Directory**.


![Journaux d’audit](./media/active-directory-reporting-migration/482.png "Journaux d’Audit")








Voici les rapports qui ont été consolidés dans cet affichage :

-    Rapport d’audit

-     Activité de réinitialisation de mot de passe

-     Activité de l’enregistrement de la réinitialisation de mot de passe

-     Activité de groupes en libre-service

-     Changements de nom de groupe d’Office&365;

-     Activité d’approvisionnement de compte

-     État de substitution de mot de passe
-     Erreurs de configuration de compte


Le rapport d’utilisation de l’application a été amélioré et inclus dans une vue nommée **Connexions**. Vous pouvez accéder à cette vue dans la section **Activité** du panneau **Azure Active Directory**.


![Journaux d’audit](./media/active-directory-reporting-migration/483.png "Journaux d’Audit")

Cette vue répertorie toutes les connexions des utilisateurs, qui, à leur tour, peuvent être exploitées pour obtenir les informations d’utilisation de l’application. Vous trouverez également les informations d’utilisation de l’application dans la vue d’ensemble **Applications d’entreprise**.

![Journaux d’audit](./media/active-directory-reporting-migration/484.png "Journaux d’Audit")



## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>Comment accéder à un rapport en particulier dans cet affichage unique ?

### <a name="audit-logs"></a>Journaux d’audit

L’une des principales demandes de nombreux clients concerne la possibilité d’avoir plusieurs options de filtrage pour accéder aux journaux d’activité au sein d’Azure AD. Au lieu de cela, nous avons fourni un mécanisme de filtrage avancé vous permettant de filtrer les données de votre choix. L’un des filtres que nous avons fournis se nomme **Catégorie d’activité**. Il liste les différents types de journaux d’activité fournis par Azure AD. En choisissant la catégorie souhaitée, vous pouvez limiter les résultats à ce que vous recherchez. 

Par exemple, si vous souhaitez simplement obtenir les activités liées à la **Réinitialisation de mot de passe en libre-service**, vous pouvez choisir la catégorie **Gestion des mots de passe en libre-service**. Les catégories affichées sont dans le contexte de la ressource que vous utilisez.  


![Journaux d’audit](./media/active-directory-reporting-migration/06.png "Journaux d’Audit")

 
Voici les différentes catégories que nous avons à ce jour :

- Annuaire principal

- Gestion des mots de passe en libre-service

- Gestion des groupes en libre-service

- Approvisionnement des comptes

### <a name="application-usage"></a>Utilisation des applications

Vous pouvez voir l’utilisation de toutes les applications ou d’une seule application dans l’affichage **Activités -> Connexions**. Comme le montre l’illustration ci-dessous, cet affichage est présent pour toutes les applications ou pour une seule application. Si vous souhaitez restreindre les résultats, vous pouvez filtrer par **nom d’utilisateur** ou par **nom d’application**.
 

![Journaux d’audit](./media/active-directory-reporting-migration/07.png "Journaux d’Audit")


### <a name="security-reports"></a>Rapports de sécurité

Les rapports de sécurité ont été consolidés pour montrer une vue d’ensemble exhaustive de tous les événements à risque liés à la sécurité qu’Azure Active Directory peut détecter et signaler. Pour bénéficier d’une présentation complète, consultez [Azure Active Directory risk events](active-directory-identity-protection-risk-events.md) (Événements à risque dans Azure Active Directory).  
Dans cette rubrique, vous trouverez une présentation de la manière dont les rapports d’activité anormale dans Azure Active Directory sont mappés aux événements à risque dans Azure AD dans la section relative aux [rapports d’activité anormale Azure AD](active-directory-identity-protection-risk-events.md#azure-ad-anomalous-activity-reports).

Dans le portail Azure, vous pouvez accéder aux rapports sur les événements à risque détectés dans la section **Sécurité** du panneau **Azure Active Directory**. Les événements à risque détectés sont suivis à l’aide des rapports suivants :   

- les utilisateurs à risque ;
- les connexions risquées. 


![Journaux d’audit](./media/active-directory-reporting-migration/04.png "Journaux d’Audit")

Pour plus de détails sur ces rapports, consultez les ressources suivantes :

- [Rapport sur la sécurité des utilisateurs courant un risque dans le portail Azure Active Directory - version préliminaire](active-directory-reporting-security-user-at-risk.md)
- [Rapport de connexions risquées dans le portail Azure Active Directory - version préliminaire](active-directory-reporting-security-risky-sign-ins.md)






## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Comparaison des rapports d’activité entre le Portail Azure Classic et le Portail Azure

Cette section liste les rapports existants sur le Portail Azure Classic et explique comment obtenir ces informations sur le Portail Azure.


Les **Journaux d’audit** dans la section **Activité** du panneau **Azure Active Directory** constituent votre point d’entrée pour toutes les données d’audit.

![Journaux d’audit](./media/active-directory-reporting-migration/61.png "Journaux d’Audit")


| Portail Azure Classic                 | Étapes dans le portail Azure                                                         |
| ---                                  | ---                                                                        |
| Journaux d’audit                           | Dans **Catégorie d’activité**, sélectionnez **Annuaire principal**.                       |
| Activité de réinitialisation de mot de passe              | Dans **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**. | 
| Activité de l’enregistrement de la réinitialisation de mot de passe | Dans **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**.     |
| Activité de groupes en libre-service         | Dans **Catégorie d’activité**, sélectionnez **Gestion des groupes en libre-service**.        |
| Activité d’approvisionnement de compte        | Dans **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.         |
| État de substitution de mot de passe             | Dans **Catégorie d’activité**, sélectionnez **Substitution automatique de mot de passe d’application**.      |
| Erreurs de configuration de compte          | Dans **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.        |
| Changements de nom de groupe d’Office&365;         | Dans **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**, dans **Type de ressource d’activité**, sélectionnez **Groupe** et dans **Source de l’activité**, sélectionnez **Groupe O365**.|

 

Votre point d’entrée pour le rapport **Utilisation des applications** est **Azure Active Directory > Applications d’entreprise > Connexions**. 


![Journaux d’audit](./media/active-directory-reporting-migration/199.png "Journaux d’Audit")


