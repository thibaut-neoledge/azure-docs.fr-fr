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
ms.sourcegitcommit: 800397efdf2e3e3cb95c77bc90f45bb8852e1e42
ms.openlocfilehash: d05cf7dc62518ea5519f319a2502e1d261b148f3
ms.lasthandoff: 02/17/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Trouver les rapports d’activité sur le Portail Azure

Avec la migration du Portail Azure Classic vers le Portail Azure, nous vous offrons une nouvelle présentation des journaux d’activité Azure Active Directory. Nous avons publié un [billet de blog](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/) il y a quelques mois pour expliquer comment nous fournissons des journaux d’activité dans le contexte de la ressource sur laquelle vous travaillez. Cet article décrit la façon dont nous avons transposé les rapports existants du Portail Azure Classic vers le nouveau monde du Portail Azure.

## <a name="what-is-new"></a>Quelles sont les nouveautés ?

Les rapports du Portail Azure Classic sont répartis en différentes catégories :

1.    Rapports de sécurité
2.    Rapports d’activité
3.    Rapports d’application intégrée

### <a name="activity-and-integrated-app-reports"></a>Rapports d’activité et d’application intégrée

Lors du passage aux rapports contextuels du Portail Azure, nous avons fusionné les rapports existants dans un seul et même affichage avec une seule API sous-jacente qui fournit les données affichées. Vous pouvez trouver cet affichage sous « *Activités* » > Journaux d’audit sur le Portail Azure (volet de navigation gauche). Voici les rapports qui ont été consolidés dans cet affichage :

-    Rapport d’audit

-     Activité de réinitialisation de mot de passe

-     Activité de l’enregistrement de la réinitialisation de mot de passe

-     Activité de groupes en libre-service

-     Changements de nom de groupe d’Office&365;

-     Activité d’approvisionnement de compte

-     État de substitution de mot de passe
-     Erreurs de configuration de compte


Le rapport d’utilisation de l’application a été amélioré et inclus dans un affichage appelé «*Activités* » -> Connexions (volet de navigation de gauche) ; il comprend les connexions de tous les utilisateurs qui, à leur tour, peuvent être dérivées pour obtenir des informations d’utilisation de l’application. Vous trouverez les informations d’utilisation de l’application sur l’écran de vue d’ensemble des « Applications d’entreprise ».

## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>Comment accéder à un rapport en particulier dans cet affichage unique ?

### <a name="audit-logs"></a>Journaux d’audit

L’une des principales demandes de nombreux clients concerne la possibilité d’avoir plusieurs options de filtrage pour accéder aux journaux d’activité au sein d’Azure AD. Au lieu de cela, nous avons fourni un mécanisme de filtrage avancé vous permettant de filtrer les données de votre choix. L’un des filtres que nous avons fournis se nomme «*Catégorie d’activité*» ; il liste les différents types de journaux d’activité fournis par Azure AD. En sélectionnant la catégorie souhaitée, vous pouvez restreindre les résultats des journaux d’activité à ceux que vous souhaitez voir. 

Par exemple, si vous souhaitez simplement obtenir les activités liées à la **Réinitialisation de mot de passe en libre-service**, vous pouvez choisir la catégorie **Gestion des mots de passe en libre-service**. Les catégories affichées sont dans le contexte de la ressource que vous utilisez.  


![Journaux d’audit](./media/active-directory-reporting-migration/06.png "Journaux d’Audit")

 
Voici les différentes catégories que nous avons à ce jour :

- Annuaire principal

- Gestion des mots de passe en libre-service

- Gestion des groupes en libre-service

- Approvisionnement des comptes

### <a name="application-usage"></a>Utilisation des applications

Vous pouvez voir l’utilisation de toutes les applications ou d’une seule application dans l’affichage Activités -> Connexions. Comme le montre l’illustration ci-dessous, cet affichage est présent pour toutes les applications ou pour une seule application. Si vous souhaitez restreindre les résultats, vous pouvez filtrer par nom d’utilisateur ou par nom d’application.
 

![Journaux d’audit](./media/active-directory-reporting-migration/07.png "Journaux d’Audit")


### <a name="security-reports"></a>Rapports de sécurité

Les rapports de sécurité ont été consolidés pour montrer tous les signaux de risque qu’Azure Active Directory peut détecter et signaler.

Cet affichage consolidé vous fournit des données sur :

- les utilisateurs à risque ;
- les connexions risquées. 


![Journaux d’audit](./media/active-directory-reporting-migration/04.png "Journaux d’Audit")



## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Comparaison des rapports d’activité entre le Portail Azure Classic et le Portail Azure

Cette section liste les rapports existants sur le Portail Azure Classic et explique comment obtenir ces informations sur le Portail Azure.

**Journaux d’audit :**

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Annuaire principal**. 

**Activité de réinitialisation de mot de passe :**

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**. 

**Activité d’inscription à la réinitialisation de mot de passe :**    

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**.

**Activité de groupes en libre-service :**    

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Gestion des groupes en libre-service**.

**Changements de nom de groupe d’Office&365; :**

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Gestion des mots de passe en libre-service**.
3. Dans **Type de ressource activité**, sélectionnez **Groupe**. 
4. Dans **Source de l’activité**, sélectionnez **Groupes O365**. 

**Activité d’approvisionnement des comptes :**    

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.

**État de la substitution de mot de passe :**    

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Substitution automatique de mot de passe d’application**.

**Erreurs d’approvisionnement de compte :**

1. Dans le volet de navigation gauche, cliquez sur **Activités**, puis sur **Journaux d’audit**.
2. Dans **Catégorie d’activité**, sélectionnez **Approvisionnement des utilisateurs de comptes**.

**Utilisation des applications :**

- Dans le volet de navigation de gauche, cliquez sur **Applications d’entreprise**, puis sur **Connexions**



