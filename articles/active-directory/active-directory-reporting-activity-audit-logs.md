---

title: "Rapports d’activité d’audit dans le portail Azure Active Directory | Microsoft Docs"
description: "Présentation des rapports d’activité d’audit dans le portail Azure Active Directory"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 4065682658bdd99066266b8b4e5e4c4605ff3db9
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité d’audit dans le portail Azure Active Directory 

Dans Azure Active Directory (Azure AD), vous pouvez obtenir toutes les informations dont vous avez besoin pour déterminer l’état de votre environnement.

L’architecture de création de rapports dans Azure AD comprend les composants suivants :

- **Activité** 
    - **Activités de connexion** – Informations sur l’utilisation des applications gérées et les activités de connexion des utilisateurs
    - **Activités du système** – Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, à vos applications gérées et aux activités de répertoire.
- **Sécurité** 
    - **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour en savoir plus, consultez Connexions risquées.
    - **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour en savoir plus, consultez Utilisateurs avec indicateur de risque.

Cette rubrique vous donne une vue d’ensemble des activités d’audit.
 


## <a name="audit-logs"></a>Journaux d’audit

Les journaux d’audit dans Azure Active Directory fournissent des enregistrements des activités du système pour la conformité.  
Les **Journaux d’audit** dans la section **Activité** **d’Azure Active Directory** constituent votre premier point d’entrée pour toutes les données d’audit.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/61.png "Journaux d’Audit")

Un journal d’audit a une vue de liste par défaut qui indique :

- la date et l’heure de l’occurrence
- l’initiateur / intervenant (*qui*) d’une activité 
- l’activité (*quoi*) 
- la cible

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/18.png "Journaux d’Audit")

Vous pouvez personnaliser la vue sous forme de liste en cliquant sur **Colonnes** dans la barre d’outils.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/19.png "Journaux d’Audit")

Cela vous permet d’afficher des champs supplémentaires ou de supprimer des champs qui sont déjà affichés.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/21.png "Journaux d’Audit")


En cliquant sur un élément dans la vue sous forme de liste, vous pouvez obtenir plus d’informations sur cet élément.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/22.png "Journaux d’Audit")


## <a name="filtering-audit-logs"></a>Filtrage des journaux d’audit

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données d’audit à l’aide des champs suivants :

- Plage de dates
- Initié par (intervenant)
- Catégorie
- Type de ressource d’activité
- Activité

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/23.png "Journaux d’Audit")


Le filtre **Plage de dates** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 24 heures
- Personnalisée

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Le filtre **Initié par** vous permet de définir le nom d’un intervenant ou son nom principal universel (UPN).

Le filtre **Catégorie** vous permet de sélectionner un des filtres suivants :

- Tout
- Catégorie principale
- Répertoire principal
- Gestion des mots de passe en libre-service
- Gestion des groupes en libre service
- Approvisionnement des comptes
- Substitution de mot de passe automatique
- utilisateurs invités
- Service MIM

Le filtre **Type de ressource d’activité** vous permet de sélectionner un des filtres suivants :

- Tout 
- Groupe
- Répertoire
- Utilisateur
- Application
- Stratégie
- Appareil
- Autres

Lorsque vous sélectionnez le **type de ressource d’activité** **Groupe**, vous obtenez une catégorie de filtre supplémentaire qui vous permet de fournir également une **source** :

- Azure AD
- O365




Le filtre **Activité** est basé sur la catégorie et le type de ressource d’activité que vous choisissez. Vous pouvez sélectionner une activité spécifique que vous souhaitez voir ou toutes les choisir. 

| Catégorie d’activité| Type de ressource d’activité| Activité |
| :-- | :-: | :-- |
| Annuaire principal| Groupe| Suppression de paramètres de groupe|
| Annuaire principal| Répertoire| Mise à jour d’un domaine|
| Annuaire principal| Répertoire| Suppression d’un partenaire d’une entreprise|
| Annuaire principal| Utilisateur| Mise à jour d’un rôle|
| Annuaire principal| Utilisateur| Ajout d’un rôle à partir d’un modèle|
| Annuaire principal| Groupe| Ajout d’une affectation de rôle d’application à un groupe|
| Annuaire principal| Groupe| Commencer à appliquer une licence basée sur un groupe à des utilisateurs|
| Annuaire principal| Application| Ajout d’un principal de service|
| Annuaire principal| Stratégie| Mise à jour d’une stratégie|
| Annuaire principal| Stratégie| Ajout d’une stratégie à un principal de service|
| Annuaire principal| Appareil| Ajout d’un propriétaire enregistré à un appareil|
| Annuaire principal| Appareil| Ajout d’utilisateurs enregistrés à un appareil|
| Annuaire principal| Appareil| Mise à jour d’une configuration d’appareil|
| Gestion des mots de passe en libre-service| Utilisateur| Réinitialisation d’un mot de passe (libre-service)|
| Gestion des mots de passe en libre-service| Utilisateur| Déverrouillage d’un compte d’utilisateur (libre-service)|
| Gestion des mots de passe en libre-service| Utilisateur| Réinitialisation d’un mot de passe (par l’administrateur)|
| Gestion des groupes en libre-service| Groupe| Suppression d’une demande en attente pour rejoindre un groupe|
| Approvisionnement des comptes| Application| Dépôt de processus|
| Substitution de mot de passe automatique| Application| Substitution de mot de passe automatique|
| Utilisateurs invités| Autres| Lot d’invitations traité|
| Annuaire principal| Répertoire| Suppression d’un domaine vérifié|
| Annuaire principal| Répertoire| Ajout d’un domaine non vérifié|
| Annuaire principal| Répertoire| Ajout d’un domaine vérifié|
| Annuaire principal| Répertoire| Définition de la fonctionnalité de répertoire sur un client|
| Annuaire principal| Répertoire| Définition de l’indicateur Dirsyncenabled|
| Annuaire principal| Répertoire| Création de paramètres d’entreprise|
| Annuaire principal| Répertoire| Mise à jour de paramètres d’entreprise|
| Annuaire principal| Répertoire| Suppression de paramètres d’entreprise|
| Annuaire principal| Répertoire| Définition d’un emplacement de données autorisé pour une entreprise|
| Annuaire principal| Répertoire| Définition de l’activation de la fonctionnalité multinationale|
| Annuaire principal| Utilisateur| Mise à jour d’un utilisateur|
| Annuaire principal| Utilisateur| Supprimer l'utilisateur|
| Annuaire principal| Groupe| Suppression d’un membre d’un groupe|
| Annuaire principal| Groupe| Définition d’une licence de groupe|
| Annuaire principal| Groupe| Création de paramètres de groupe|
| Annuaire principal| Application| Mise à jour d’un principal de service|
| Annuaire principal| Application| Supprimer l’application|
| Annuaire principal| Application| Mise à jour d’une application|
| Annuaire principal| Application| Suppression d’un principal de service|
| Annuaire principal| Application| Ajout des informations d’identification d’un principal de service|
| Annuaire principal| Application| Suppression d’une affectation de rôle d’application d’un principal de service|
| Annuaire principal| Application| Suppression d’un propriétaire d’une application|
| Annuaire principal| Appareil| Suppression d’un propriétaire enregistré d’un appareil|
| Gestion des mots de passe en libre-service| Utilisateur| Progression de l’activité du flux de réinitialisation du mot de passe en libre-service|
| Approvisionnement des comptes| Application| Administration|
| Approvisionnement des comptes| Application| Opération sur le répertoire|
| Service MIM| Groupe| Suppression d’un membre|
| Annuaire principal| Stratégie| Suppression d’une stratégie|
| Utilisateurs invités| Utilisateur| Création d’un locataire viral|
| Annuaire principal| Répertoire| Mise à jour de clés secrètes externes|
| Annuaire principal| Répertoire| Définition de propriétés Rights Management|
| Annuaire principal| Répertoire| Mise à jour d’une entreprise|
| Annuaire principal| Utilisateur| Ajouter un utilisateur|
| Annuaire principal| Utilisateur| Conversion d’un utilisateur fédéré en utilisateur géré|
| Annuaire principal| Utilisateur| Création d’un mot de passe d’application pour un utilisateur|
| Annuaire principal| Groupe| Ajout d’un membre à un groupe|
| Annuaire principal| Groupe| Ajout d’un groupe|
| Annuaire principal| Application| Consentement pour une application|
| Annuaire principal| Application| Ajout d’une application|
| Annuaire principal| Application| Ajout d’un propriétaire à un principal de service|
| Annuaire principal| Application| Suppression d’Oauth2Permissiongrant|
| Annuaire principal| Stratégie| Suppression des informations d’identification d’une stratégie|
| Annuaire principal| Appareil| Suppression d’une configuration d’appareil|
| Gestion des groupes en libre-service| Groupe| Définition de propriétés de groupe dynamiques|
| Gestion des groupes en libre-service| Groupe| Mise à jour d’une stratégie de gestion du cycle de vie|
| Approvisionnement des comptes| Application| Action de règles de synchronisation|
| Utilisateurs invités| Autres| Lot d’invitations chargé|
| Service MIM| Groupe| Ajout d’un membre|
| Annuaire principal| Utilisateur| Définition de propriétés de licence|
| Annuaire principal| Utilisateur| Restauration d’un utilisateur|
| Annuaire principal| Utilisateur| Suppression d’un membre d’un rôle|
| Annuaire principal| Utilisateur| Suppression d’une affectation de rôle d’application d’un utilisateur|
| Annuaire principal| Utilisateur| Suppression d’un membre inclus dans une étendue d’un rôle|
| Annuaire principal| Groupe| Mise à jour d’un groupe|
| Annuaire principal| Groupe| Ajout d’un propriétaire à un groupe|
| Annuaire principal| Groupe| Terminer l’application d’une licence basée sur le groupe à des utilisateurs|
| Annuaire principal| Groupe| Suppression d’une affectation de rôle d’application d’un groupe|
| Annuaire principal| Groupe| Définition d’un groupe à gérer par un utilisateur|
| Annuaire principal| Application| Ajout d’Oauth2Permissiongrant|
| Annuaire principal| Application| Ajout d’une affectation de rôle d’application d’un principal de service|
| Annuaire principal| Application| Suppression des informations d’identification d’un principal de service|
| Annuaire principal| Stratégie| Suppression d’une stratégie d’un principal de service|
| Annuaire principal| Appareil| Mise à jour d’un appareil|
| Annuaire principal| Appareil| Ajout d’un appareil|
| Annuaire principal| Appareil| Ajout d’une configuration d’un appareil|
| Gestion des mots de passe en libre-service| Utilisateur| Modification d’un mot de passe (libre-service)|
| Gestion des mots de passe en libre-service| Utilisateur| Utilisateur inscrit pour la réinitialisation du mot de passe en libre-service|
| Gestion des groupes en libre-service| Groupe| Approbation d’une demande en attente pour rejoindre un groupe|
| Annuaire principal| Répertoire| Suppression d’un domaine non vérifié|
| Annuaire principal| Répertoire| Vérification d’un domaine|
| Annuaire principal| Répertoire| Définition d’une authentification de domaine|
| Annuaire principal| Répertoire| Définir la stratégie de mot de passe|
| Annuaire principal| Répertoire| Ajout d’un partenaire à une entreprise|
| Annuaire principal| Répertoire| Promotion d’une entreprise auprès d’un partenaire|
| Annuaire principal| Répertoire| Définition d’un partenariat|
| Annuaire principal| Répertoire| Définition d’un seuil de suppression accidentelle|
| Annuaire principal| Répertoire| Rétrogradation d’un partenaire|
| Utilisateurs invités| Utilisateur| Invitation d’un utilisateur externe|
| Approvisionnement des comptes| Application| Importer|
| Annuaire principal| Application| Suppression d’un propriétaire d’un principal de service|
| Annuaire principal| Appareil| Suppression d’utilisateurs enregistrés d’un appareil|
| Annuaire principal| Répertoire| Définir les informations de l'entreprise|
| Annuaire principal| Répertoire| Définition de paramètres de fédération d’un domaine|
| Annuaire principal| Répertoire| Création d’une entreprise|
| Annuaire principal| Répertoire| Vidage de propriétés Rights Management|
| Annuaire principal| Répertoire| Définition de la fonctionnalité DirSync|
| Annuaire principal| Répertoire| Vérification d’un domaine vérifié par courrier électronique|
| Annuaire principal| Utilisateur| Modification de la licence d’un utilisateur|
| Annuaire principal| Utilisateur| Modification du mot de passe d’un utilisateur|
| Annuaire principal| Utilisateur| Réinitialisation du mot de passe d’un utilisateur|
| Annuaire principal| Utilisateur| Ajout d’une affectation de rôle d’application à un utilisateur|
| Annuaire principal| Utilisateur| Ajout d’un membre à un rôle|
| Annuaire principal| Utilisateur| Suppression d’un mot de passe d’application pour un utilisateur|
| Annuaire principal| Utilisateur| Mise à jour des informations d’identification d’un utilisateur|
| Annuaire principal| Utilisateur| Définition d’un gestionnaire d’utilisateurs|
| Annuaire principal| Utilisateur| Ajout d’un membre inclus dans une étendue à un rôle|
| Annuaire principal| Groupe| Suppression d’un groupe|
| Annuaire principal| Groupe| Suppression d’un propriétaire d’un groupe|
| Annuaire principal| Groupe| Mise à jour des paramètres d’un groupe|
| Annuaire principal| Application| Ajout d’un propriétaire à une application|
| Annuaire principal| Application| Annulation d’un consentement|
| Annuaire principal| Stratégie| Ajout d’une stratégie|
| Annuaire principal| Appareil| Suppression d’un appareil|
| Gestion des mots de passe en libre-service| Utilisateur| Blocage suite à la réinitialisation du mot de passe en libre-service|
| Gestion des groupes en libre-service| Groupe| Demande pour rejoindre un groupe|
| Gestion des groupes en libre-service| Groupe| Création d’une stratégie de gestion du cycle de vie|
| Gestion des groupes en libre-service| Groupe| Rejet d’une demande en attente pour rejoindre un groupe|
| Gestion des groupes en libre-service| Groupe| Annulation d’une demande en attente pour rejoindre un groupe|
| Gestion des groupes en libre-service| Groupe| Renouvellement d’un groupe|
| Approvisionnement des comptes| Application| Exportation|
| Approvisionnement des comptes| Application| Autres|
| Utilisateurs invités| Utilisateur| Utilisation de l’invitation d’un utilisateur externe|
| Utilisateurs invités| Utilisateur| Création d’un utilisateur viral|
| Utilisateurs invités| Utilisateur| Affectation d’un utilisateur externe à une application|




## <a name="audit-logs-shortcuts"></a>Raccourcis de journaux d’audit

En plus d’**Azure Active Directory**, le portail Azure vous offre deux autres points d’entrée pour les données d’audit :

- Utilisateurs et groupes
- Applications d’entreprise

Pour obtenir une liste complète des activités de rapport d’audit, consultez la [liste des événements de rapport d’audit](active-directory-reporting-audit-events.md#list-of-audit-report-events).


### <a name="users-and-groups-audit-logs"></a>Journaux d’audit des utilisateurs et des groupes

Les rapports d’audit basés sur les utilisateurs et les groupes vous permettent d’obtenir des réponses aux questions telles que :

- Quels types de mises à jour ont été appliquées aux utilisateurs ?

- Combien d’utilisateurs ont été modifiés ?

- Combien de mots de passe ont été modifiés ?

- Qu’a fait un administrateur dans un répertoire ?

- Quels sont les groupes qui ont été ajoutés ?

- Existe-t-il des groupes comportant des modifications d’adhésion ?

- Les propriétaires de groupe ont-ils été modifiés ?

- Quelles licences ont été attribuées à un groupe ou un utilisateur ?

Si vous souhaitez simplement consulter les données d’audit connexes aux utilisateurs et aux groupes, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** de **Utilisateurs et groupes**. Dans ce point d’entrée, **Utilisateurs et groupes** est présélectionné comme **Type de ressource d’activité**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/93.png "Journaux d’Audit")

### <a name="enterprise-applications-audit-logs"></a>Journaux d’audit d’applications d’entreprise

Les rapports d’audit basés sur les applications vous permettent d’obtenir des réponses aux questions telles que :

* Quelles applications ont été ajoutées ou mises à jour ?
* Quelles applications ont été supprimées ?
* Le principal du service d’une application a-t-il été modifié ?
* Les noms des applications ont-ils été modifiés ?
* Qui a donné son consentement à une application ?

Si vous souhaitez simplement consulter les données d’audit connexes à vos applications, vous pouvez trouver une vue filtrée sous **Journaux d’audit** dans la section **Activité** du panneau **Applications d’entreprise**. Dans ce point d’entrée, **Applications d’entreprise** est présélectionné comme **Type de ressource d’activité**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/134.png "Journaux d’Audit")

Vous pouvez filtrer davantage cette vue pour afficher uniquement les **groupes** ou les **utilisateurs**.

![Journaux d’audit](./media/active-directory-reporting-activity-audit-logs/25.png "Journaux d’Audit")


## <a name="next-steps"></a>Étapes suivantes
Consultez le [Guide Azure Active Directory Reporting Guide](active-directory-reporting-guide.md).


