---
title: "Trouver les rapports d’activité dans le Portail Azure | Microsoft Docs"
description: "Découvrez comment trouver les rapports d’activité Azure Active Directory sur le Portail Azure."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7f6d82d211e9b3d25b0efe62ab8dd32f827b08ef
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---
# Trouver les rapports d’activité sur le Portail Azure
<a id="find-activity-reports-in-the-azure-portal" class="xliff"></a>

Si vous passez du portail Azure Classic au portail Azure, vous voyez un nouvel aperçu des journaux d’activité Azure Active Directory (Azure AD). Dans un [billet de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) récent, nous expliquons comment afficher les journaux d’activité dans le contexte de la ressource que vous utilisez dans le portail Azure. Dans cet article, nous expliquons comment trouver les rapports que vous avez utilisés dans le portail Azure Classic sur le portail Azure.

## Nouveautés
<a id="whats-new" class="xliff"></a>

Dans le portail Azure Classic, les rapports sont répartis dans des catégories distinctes :

1.  Rapports de sécurité
2.  Rapports d’activité
3.  Rapports d’application intégrée

### Rapports d’activité et d’application intégrée
<a id="activity-and-integrated-app-reports" class="xliff"></a>

Dans le portail Azure, pour la création de rapports basés sur le contexte, les rapports existants sont fusionnés en une seule vue. Une seule API sous-jacente fournit les données de la vue.

Pour afficher cette vue, dans le panneau **Azure Active Directory**, sous **Activité**, sélectionnez **Journaux d’audit**.

![Journaux d’audit](./media/active-directory-reporting-migration/482.png "Journaux d’Audit")

Les rapports suivants sont consolidés dans cette vue :

-   Rapport d’audit
-   Activité de réinitialisation de mot de passe
-   Activité de l’enregistrement de la réinitialisation de mot de passe
-   Activité de groupes en libre-service
-   Changements de nom de groupe d’Office 365
-   Activité d’approvisionnement de compte
-   État de substitution de mot de passe
-   Erreurs de configuration de compte


Le rapport d’utilisation de l’application a été amélioré et inclus dans la vue **Connexions**. Pour afficher cette vue, sur le panneau **Azure Active Directory**, sous **Activité**, sélectionnez **Connexions**.

![Vue Connexions](./media/active-directory-reporting-migration/483.png "Vue Connexions")

La vue **Connexions** inclut toutes les connexions des utilisateurs. Vous pouvez utiliser ces informations pour obtenir des informations sur l’utilisation de l’application. Vous pouvez également afficher des informations sur l’utilisation de l’application dans la vue d’ensemble **Applications d’entreprise** sous la section **Gérer**.

![Applications d’entreprise](./media/active-directory-reporting-migration/484.png "Applications d’entreprise")

## Accéder à un rapport spécifique
<a id="access-a-specific-report" class="xliff"></a>

Bien que le portail Azure offre une vue unique, vous pouvez également consulter des rapports spécifiques.

### Journaux d’audit
<a id="audit-logs" class="xliff"></a>

En réponse aux commentaires des clients, dans le portail Azure, vous pouvez utiliser un filtrage avancé pour accéder aux données de votre choix. Vous pouvez utiliser le filtre *Catégorie de l’activité*, qui répertorie les différents types de journaux d’activité dans Azure AD. Pour restreindre les résultats à ce que vous recherchez, vous pouvez sélectionner une catégorie.

Par exemple, si vous souhaitez simplement obtenir les activités liées aux réinitialisations de mot de passe libre-service, vous pouvez choisir la catégorie **Gestion des mots de passe en libre-service**. Les catégories affichées sont basées sur la ressource que vous utilisez.  

![Options de catégories dans la page Filtrer les journaux d’audit](./media/active-directory-reporting-migration/06.png "Options de catégories dans la page Filtrer les journaux d’audit")

Les catégories d’activités sont les suivantes :

- Annuaire principal
- Gestion des mots de passe en libre-service
- Gestion des groupes en libre-service
- Approvisionnement des comptes

### Utilisation des applications
<a id="application-usage" class="xliff"></a>

Pour afficher plus d’informations sur l’utilisation des applications (pour toutes les applications ou une seule application), sous **Activité**, sélectionnez **Connexions**. Pour restreindre les résultats, vous pouvez filtrer par nom d’utilisateur ou par nom d’application.

![Page Filtrer les événements de connexion](./media/active-directory-reporting-migration/07.png "Page Filtrer les événements de connexion")

### Rapports de sécurité
<a id="security-reports" class="xliff"></a>

#### Rapports d’activités anormales Azure AD
<a id="azure-ad-anomalous-activity-reports" class="xliff"></a>

Les rapports de sécurité sur les activités anormales d’Azure Active Directory issus du portail Azure Classic ont été consolidés pour vous fournir une seule vue centralisée. Cette vue affiche tous les événements liés à la sécurité qu’Azure AD peut détecter et signaler dans des rapports.

Le tableau suivant répertorie les rapports de sécurité sur les activités anormales d’Azure AD et les types d’événements à risque correspondants dans le portail Azure.

| Rapport d’activités anormales Azure AD |  Type d’événement à risque signalé par Identity Protection|
| :--- | :--- |
| Utilisateurs avec des informations d’identification volées | Informations d’identification divulguées |
| Activité de connexion anormale | Voyage impossible vers des emplacements inhabituels |
| Connexions à partir d’appareils potentiellement infectés | Connexions depuis des appareils infectés|
| Connexions à partir de sources inconnues | Connexions depuis des adresses IP anonymes |
| Connexions depuis des adresses IP avec des activités suspectes | Connexions depuis des adresses IP avec des activités suspectes |
| - | Connexions depuis des emplacements non connus |

Les rapports de sécurité sur les activités anormales d’Azure AD suivants ne sont pas inclus en tant qu’événements à risque dans le portail Azure :

* Connexions après plusieurs échecs
* Connexions depuis plusieurs zones géographiques

Ces rapports sont toujours disponibles dans le portail Azure Classic, mais ils seront déconseillés ultérieurement.

Pour plus d’informations, consultez [Événements à risque dans Azure Active Directory](active-directory-identity-protection-risk-events.md).  


#### Événements à risque détectés
<a id="detected-risk-events" class="xliff"></a>

Dans le portail Azure, vous pouvez accéder aux rapports sur les événements à risque détectés dans le panneau **Azure Active Directory** sous **Sécurité**. Les événements à risque détectés sont suivis dans les rapports suivants :   

- les utilisateurs à risque ;
- les connexions risquées.

![Rapports de sécurité](./media/active-directory-reporting-migration/04.png "Rapports de sécurité")

Pour plus d’informations sur les rapports de sécurité, consultez :

- [Rapport sur la sécurité des utilisateurs courant un risque dans le portail Azure Active Directory](active-directory-reporting-security-user-at-risk.md)
- [Rapports sur les connexions risquées dans le portail Azure Active Directory](active-directory-reporting-security-risky-sign-ins.md)


## Comparaison des rapports d’activité entre le Portail Azure Classic et le Portail Azure
<a id="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal" class="xliff"></a>

Le tableau de cette section répertorie les rapports existants dans le portail Azure Classic. Il explique également comment obtenir les mêmes informations dans le portail Azure.

Pour afficher toutes les données d’audit, dans le panneau **Azure Active Directory**, sous **Activité**, accédez à **Journaux d’audit**.

![Journaux d’audit](./media/active-directory-reporting-migration/61.png "Journaux d’Audit")

| Portail Azure Classic                 | Pour rechercher dans le portail Azure                                                         |
| ---                                  | ---                                                                        |
| Journaux d’audit                           | Pour **Catégorie d’activité**, sélectionnez **Annuaire principal**.                       |
| Activité de réinitialisation de mot de passe              | Pour **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**. |
| Activité de l’enregistrement de la réinitialisation de mot de passe | Pour **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**.     |
| Activité de groupes en libre-service         | Pour **Catégorie d’activité**, sélectionnez **Gestion des groupes en libre-service**.        |
| Activité d’approvisionnement de compte        | Pour **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.         |
| État de substitution de mot de passe             | Pour **Catégorie d’activité**, sélectionnez **Substitution automatique de mot de passe d’application**.      |
| Erreurs de configuration de compte          | Pour **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.        |
| Changements de nom de groupe d’Office 365         | Pour **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**. Pour **Type de ressource activité**, sélectionnez **Groupe**. Pour **Source de l’activité**, sélectionnez **Groupes O365**.|

Pour afficher le rapport **d’utilisation des applications**, dans le panneau **Azure Active Directory**, sous **Gérer**, sélectionnez **Applications d’entreprise**, puis **Connexions**.


![Rapport sur les connexions d’applications d’entreprise](./media/active-directory-reporting-migration/199.png "Rapport sur les connexions d’applications d’entreprise")

