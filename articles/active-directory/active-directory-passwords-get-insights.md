---
title: "Obtention d’informations grâce aux rapports sur la gestion des mots de passe Azure AD | Microsoft Docs"
description: "Cet article explique comment utiliser les rapports pour obtenir des informations sur les opérations de gestion des mots de passe dans votre organisation."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 1472b51d-53f4-4b0f-b1be-57f6fa88fa65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 7375d2d3c237c3b1c2dcdab44b2fcb0000ff961c
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017


---
# <a name="how-to-get-operational-insights-with-password-management-reports"></a>Obtention d’informations grâce aux rapports sur la gestion des mots de passe
> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account).
>
>

Cette section explique comment utiliser les rapports de gestion des mots de passe Azure Active Directory pour savoir comment les utilisateurs utilisent la réinitialisation et la modification des mots de passe dans votre organisation.

* [**Vue d’ensemble des rapports de gestion des mots de passe**](#overview-of-password-management-reports)
* [**Comment visualiser les rapports de gestion des mots de passe dans le nouveau portail Azure**](#how-to-view-password-management-reports)
 * [Rôles d’annuaire autorisés à lire les rapports](#directory-roles-allowed-to-read-reports)
* [**Types d’activités de gestion des mots de passe en libre-service dans le nouveau portail Azure**](#self-service-password-management-activity-types)
 * [Blocage suite à la réinitialisation du mot de passe en libre-service](#activity-type-blocked-from-self-service-password-reset)
 * [Modification du mot de passe (libre-service)](#activity-type-change-password-self-service)
 * [Réinitialisation du mot de passe (par l’administrateur)](#activity-type-reset-password-by-admin)
 * [Modification du mot de passe (libre-service)](#activity-type-reset-password-self-service)
 * [Progression de l’activité du flux de réinitialisation du mot de passe en libre-service](#activity-type-self-serve-password-reset-flow-activity-progress)
 * [Déverrouillage du compte d’utilisateur (libre-service)](#activity-type-unlock-user-account-self-service)
 * [Utilisateur inscrit pour la réinitialisation du mot de passe en libre-service](#activity-type-user-registered-for-self-service-password-reset)
* [**Comment récupérer les événements de gestion des mots de passe à partir de l’API de rapports et d’événements Azure AD**](#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
 * [Création de rapport sur les limitations de récupération des données API](#reporting-api-data-retrieval-limitations)
* [**Comment télécharger rapidement les événements d’inscription de réinitialisation de mot de passe avec PowerShell**](#how-to-download-password-reset-registration-events-quickly-with-powershell)
* [**Comment visualiser les rapports de gestion des mots de passe dans le portail Classic**](#how-to-view-password-management-reports-in-the-classic-portal)
* [**Visualiser l’activité d’inscription de réinitialisation de mot de passe dans le portail Classic**](#view-password-reset-registration-activity-in-the-classic-portal)
* [**Visualiser l’activité de réinitialisation de mot de passe dans le portail Classic**](#view-password-reset-activity-in-the-classic-portal)


## <a name="overview-of-password-management-reports"></a>Vue d’ensemble des rapports de gestion des mots de passe
Une fois la réinitialisation des mots de passe déployée, l’une des étapes les plus courantes consiste à vérifier comment elle est utilisée dans votre organisation.  Par exemple, vous souhaiterez peut-être savoir comment les utilisateurs s’inscrivent pour la réinitialisation des mots de passe ou combien de réinitialisations de mots de passe ont été effectuées au cours des derniers jours.  Voici quelques-unes des questions courantes auxquelles vous serez en mesure de répondre grâce aux rapports de gestion des mots de passe accessibles à l’heure actuelle dans le [portail de gestion Azure](https://manage.windowsazure.com) :

* Combien de personnes se sont inscrites pour la réinitialisation des mots de passe ?
* Qui s’est inscrit pour la réinitialisation des mots de passe ?
* Quelles sont les données inscrites par ces personnes ?
* Combien de personnes ont réinitialisé leurs mots de passe au cours des 7 derniers jours ?
* Quelles sont les méthodes les plus courantes employées par les utilisateurs ou les administrateurs pour réinitialiser leurs mots de passe ?
* Quels sont les problèmes courants rencontrés par les utilisateurs ou les administrateurs lors des tentatives d’utilisation de la fonctionnalité de réinitialisation des mots de passe ?
* Quels sont les administrateurs qui réinitialisent fréquemment leurs mots de passe ?
* Y a-t-il des activités suspectes en rapport avec la réinitialisation des mots de passe ?

## <a name="how-to-view-password-management-reports"></a>Comment visualiser les rapports de gestion des mots de passe
Dans la nouvelle interface du [portail Azure](https://portal.azure.com), nous avons amélioré l’affichage de l’activité de réinitialisation de mot de passe et d’inscription de réinitialisation de mot de passe.  Suivez les étapes ci-dessous pour trouver les événements de réinitialisation de mot de passe et d’inscription de réinitialisation de mot de passe dans le nouveau [portail Azure](https://portal.azure.com) :

1. Accédez à [**portal.azure.com**](https://portal.azure.com)
2. Cliquez sur le menu **Autres services** dans la barre de navigation principale gauche du portail Azure.
3. Recherchez **Azure Active Directory** dans la liste des services, puis sélectionnez-le.
4. Cliquez sur **Utilisateurs et groupes** dans le menu de navigation d’Azure Active Directory.
5. Cliquez sur l’élément de navigation **Journaux d’audit** dans le menu de navigation Utilisateurs et groupes. Vous affichez ainsi tous les événements d’audit concernant tous les utilisateurs de votre annuaire. Vous pouvez filtrer cette vue pour voir également tous les événements associés aux mots de passe.
6. Pour filtrer cette vue et afficher uniquement les événements associés à la gestion des mots de passe, cliquez sur le bouton **Filtrer** en haut du panneau.
7. À partir du menu **Filtrer**, sélectionnez la liste déroulante **Catégorie** et changez pour le type de catégorie **Self-service Password Management** (Gestion des mots de passe en libre-service).
8. Si vous le souhaitez, vous pouvez affiner le filtre de la liste en choisissant **l’activité** spécifique qui vous intéresse.
### <a name="direct-link-to-user-audit-blade"></a>Lien direct vers le panneau User Audit (Audit d’utilisateur)
Si vous êtes connecté à votre portail, cliquez sur ce lien pour accéder directement au panneau d’audit d’utilisateur dans lequel sont affichés ces événements :

* [Accéder directement à la vue de l’audit de gestion des utilisateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit)

### <a name="directory-roles-allowed-to-read-reports"></a>Rôles d’annuaire autorisés à lire les rapports
Actuellement, les rôles d’annuaire suivants peuvent lire les rapports de gestion des mots de passe Azure AD dans le portail Azure Classic :

* Administrateur général

Avant de pouvoir lire ces rapports, un administrateur global de la société doit avoir choisi de récupérer ces données pour le compte de l’entreprise en accédant à l’onglet de création de rapports ou aux journaux d’audit au moins une fois. Sans cela, les données ne sont pas collectées pour votre entreprise.

Pour plus d’informations sur les rôles d’annuaire et leurs autorisations, consultez [Attribution de rôles d’administrateur dans Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles).

## <a name="self-service-password-management-activity-types"></a>Types d’activités de gestion des mots de passe en libre-service
Les types d’activités suivants s’affichent dans la catégorie d’événement d’audit **Self-Service Password Management** (Gestion des mots de passe en libre service).  Ces types d’activité sont décrits ci-dessous.

* [**Blocked from self-service password reset**](#activity-type-blocked-from-self-service-password-reset) (Blocage de réinitialisation de mot de passe en libre-service) : indique qu’un utilisateur a tenté de réinitialiser un mot de passe, d’utiliser une porte spécifique ou de valider un numéro de téléphone plus de 5 fois au total au cours des dernières 24 heures.
* [**Change password (self-service)**](#activity-type-change-password-self-service) (Modification du mot de passe (libre-service)) : indique qu’un utilisateur a effectué une modification de mot de passe volontaire ou forcée (en raison d’une expiration).
* [**Reset password (by admin)**](#activity-type-reset-password-by-admin) (Réinitialisation du mot de passe (par l’administrateur)) : indique qu’un administrateur a effectué une réinitialisation du mot de passe pour le compte d’un utilisateur à partir du portail Azure.
* [**Reset password (self-service)**](#activity-type-reset-password-self-service) (Réinitialisation du mot de passe (libre-service)) : indique qu’un utilisateur a correctement réinitialisé son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com).
* [**Self serve password reset flow activity progress**](#activity-type-self-serve-password-reset-flow-activity-progress) (Progression de l’activité du flux de réinitialisation du mot de passe en libre-service) : indique chaque étape spécifique effectuée par un utilisateur (comme le passage d’une porte d’authentification de réinitialisation de mot de passe spécifique) dans le cadre du processus de réinitialisation du mot de passe.
* [**Unlock user account (self-service)**](#activity-type-unlock-user-account-self-service) (Déverrouillage du compte d’utilisateur (libre-service)) : indique qu’un utilisateur a correctement déverrouillé son compte Active Directory sans réinitialiser son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com) à l’aide de la fonctionnalité [Déverrouillage de compte Active Directory sans réinitialisation](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password).
* [**User registered for self-service password reset**](#activity-type-user-registered-for-self-service-password-reset) (Utilisateur inscrit pour la réinitialisation de mot de passe en libre-service) : indique qu’un utilisateur a inscrit toutes les informations requises pour pouvoir réinitialiser son mot de passe conformément à la stratégie de réinitialisation de mot de passe du client actuellement spécifiée.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Type d’activité : Blocked from self-service password reset (Blocage suite à la réinitialisation du mot de passe en libre-service)
La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique qu’un utilisateur a tenté de réinitialiser un mot de passe, d’utiliser une porte spécifique ou de valider un numéro de téléphone plus de 5 fois au total au cours des dernières 24 heures.
* **Activity Actor** (Acteur de l’activité) : l’utilisateur a été empêché d’effectuer des opérations supplémentaires de réinitialisation du mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur a été empêché d’effectuer des opérations supplémentaires de réinitialisation du mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **États d’activité autorisés**
 * _Success_ : indique qu’un utilisateur a été empêché d’effectuer des réinitialisations supplémentaires, d’essayer des méthodes d’authentification supplémentaires ou de valider des numéros de téléphone supplémentaires pendant les prochaines 24 heures.
* **Activity Status Failure Reason** (Motif de l’état d’activité Failure) : non applicable

### <a name="activity-type-change-password-self-service"></a>Type d’activité : Change password (self-service) (Modification du mot de passe (libre-service))
La liste suivante explique cette activité en détail :

* **Activity Description)** (Description de l’activité) : indique qu’un utilisateur a effectué une modification de mot de passe volontaire ou forcée (en raison d’une expiration).
* **Activity Actor** (Acteur de l’activité) : l’utilisateur qui a modifié son mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur qui a modifié son mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **États d’activité autorisés**
 * _Success_ : indique qu’un utilisateur a modifié son mot de passe avec succès
 * _Failure_ : indique qu’un utilisateur n’a pas réussi à modifier son mot de passe. Cliquez sur la ligne pour voir la catégorie **Activity Status Reason** (Motif de l’état d’activité) et en savoir plus sur la cause de l’échec.
* **Activity Status Failure Reason** - (Motif de l’état d’activité Failure)
 * _FuzzyPolicyViolationInvalidPassword_ : l’utilisateur a sélectionné un mot de passe qui a été automatiquement interdit car les fonctionnalités de détection de mot de passe interdit de Microsoft l’ont trouvé trop banal ou trop faible.

### <a name="activity-type-reset-password-by-admin"></a>Type d’activité : Reset password (by admin) (Réinitialisation du mot de passe (par l’administrateur))
La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique qu’un administrateur a effectué une réinitialisation du mot de passe pour le compte d’un utilisateur à partir du portail Azure.
* **Activity Actor** (Acteur de l’activité) : l’administrateur qui a effectué la réinitialisation du mot de passe pour le compte d’un autre utilisateur ou d’un administrateur. Doit être un administrateur global, un administrateur de mot de passe, administrateur de l’utilisateur ou un administrateur du support technique.
* **Activity Target** (Cible de l’activité) : l’utilisateur dont le mot de passe a été réinitialisé. Il peut s’agir d’un utilisateur final ou d’un autre administrateur.
* **États d’activité autorisés**
 * _Success_ : indique qu’un administrateur a correctement réinitialisé un mot de passe d’utilisateur
 * _Failure_ : indique qu’un administrateur n’a pas réussi à modifier un mot de passe d’utilisateur. Cliquez sur la ligne pour voir la catégorie **Activity Status Reason** (Motif de l’état d’activité) et en savoir plus sur la cause de l’échec.

### <a name="activity-type-reset-password-self-service"></a>Type d’activité : Reset password (self-service) (Réinitialisation du mot de passe (libre-service))
La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique qu’un utilisateur a correctement réinitialisé son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com).
* **Activity Actor** (Acteur de l’activité) : l’utilisateur qui a réinitialisé son mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur qui a réinitialisé son mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **États d’activité autorisés**
 * _Success_ : indique qu’un utilisateur a correctement réinitialisé son propre mot de passe
 * _Failure_ : indique qu’un utilisateur n’a pas réussi à réinitialiser son propre mot de passe. Cliquez sur la ligne pour voir la catégorie **Activity Status Reason** (Motif de l’état d’activité) et en savoir plus sur la cause de l’échec.
* **Activity Status Failure Reason** - (Motif de l’état d’activité Failure)
 * _FuzzyPolicyViolationInvalidPassword_ : l’administrateur a sélectionné un mot de passe qui a été automatiquement interdit car les fonctionnalités de détection de mot de passe interdit de Microsoft l’ont trouvé trop banal ou trop faible.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Type d’activité : Self serve password reset flow activity progress (Progression de l’activité du flux de réinitialisation du mot de passe en libre-service)
La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique chaque étape spécifique effectuée par un utilisateur (comme le passage d’une porte d’authentification de réinitialisation de mot de passe spécifique) dans le cadre du processus de réinitialisation du mot de passe.
* **Activity Actor** (Acteur de l’activité) : l’utilisateur qui a effectué une partie du flux de réinitialisation du mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur qui a effectué une partie du flux de réinitialisation du mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **États d’activité autorisés**
 * _Success_ : indique qu’un utilisateur a correctement effectué une étape spécifique du flux de réinitialisation du mot de passe.
 * _Failure_ : indique qu’une étape spécifique du flux de réinitialisation du mot de passe a échoué. Cliquez sur la ligne pour voir la catégorie **Activity Status Reason** (Motif de l’état d’activité) et en savoir plus sur la cause de l’échec.
* **Motifs des états d’activité autorisés**
 * Voir le tableau ci-dessous pour [tous les motifs d’état d’activité de réinitialisation du mot de passe autorisés](#allowed-values-for-details-column)

### <a name="activity-type-unlock-user-account-self-service"></a>Type d’activité : Unlock user account (self-service) (Déverrouillage du compte d’utilisateur (libre-service))
La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique qu’un utilisateur a correctement déverrouillé son compte Active Directory sans réinitialiser son mot de passe à partir du [portail de réinitialisation de mot de passe Azure AD](https://passwordreset.microsoftonline.com) à l’aide de la fonctionnalité [Déverrouillage de compte Active Directory sans réinitialisation](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password).
* **Activity Actor** (Acteur de l’activité) : l’utilisateur qui a déverrouillé son compte sans réinitialiser son mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur qui a déverrouillé son compte sans réinitialiser son mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **États d’activité autorisés**
 * _Success_ : indique qu’un utilisateur a correctement déverrouillé son propre compte
 * _Failure_ : indique qu’un utilisateur n’a pas réussi à déverrouiller son propre compte. Cliquez sur la ligne pour voir la catégorie **Activity Status Reason** (Motif de l’état d’activité) et en savoir plus sur la cause de l’échec.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Type d’activité : User registered for self-service password reset (Utilisateur inscrit pour la réinitialisation du mot de passe libre-service)
La liste suivante explique cette activité en détail :

* **Activity Description** (Description de l’activité) : indique qu’un utilisateur a inscrit toutes les informations requises pour pouvoir réinitialiser son mot de passe conformément à la stratégie de réinitialisation de mot de passe du client actuellement spécifiée.
* **Activity Actor** (Acteur de l’activité) : l’utilisateur qui s’est inscrit pour la réinitialisation du mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **Activity Target** (Cible de l’activité) : l’utilisateur qui s’est inscrit pour la réinitialisation du mot de passe. Il peut s’agir d’un utilisateur final ou d’un administrateur.
* **États d’activité autorisés**
 * _Success_ : indique qu’un utilisateur s’est inscrit pour la réinitialisation de mot de passe conformément à la stratégie actuelle.
 * _Failure_ : indique qu’un utilisateur n’a pas réussi à s’inscrire pour la réinitialisation de mot de passe. Cliquez sur la ligne pour voir la catégorie **Activity Status Reason** (Motif de l’état d’activité) et en savoir plus sur la cause de l’échec. Remarque : cela ne signifie pas qu’un utilisateur n’est pas en mesure de réinitialiser son propre mot de passe, simplement qu’il n’a pas terminé le processus d’inscription. Si leur compte contient des données correctes non vérifiées (par exemple, un numéro de téléphone non validé), même s’il n’a pas vérifié ce numéro de téléphone, il peut toujours l’utiliser pour réinitialiser son mot de passe. Pour plus d’informations, consultez [Que se passe-t-il lorsqu’un utilisateur s’inscrit ?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Comment récupérer les événements de gestion des mots de passe à partir de l’API de rapports et d’événements Azure AD
Depuis août 2015, l’API de rapports et d’événements Azure AD prennent désormais en charge la récupération de toutes les informations incluses dans les rapports de réinitialisation des mots de passe et d’inscription de réinitialisation des mots de passe. En utilisant cette API, vous pouvez télécharger les événements de réinitialisation de mot de passe individuels et les événements d’inscription de réinitialisation de mot de passe pour les intégrer à la technologie de création de rapports de votre choix.

### <a name="how-to-get-started-with-the-reporting-api"></a>Prise en main de l’API de création de rapports
Pour accéder à ces données, vous devrez écrire une petite application ou un petit script permettant de les extraire de nos serveurs. [Procédure de prise en main de l'API de création de rapports Azure AD](active-directory-reporting-api-getting-started.md).

Une fois que votre script est opérationnel, vous devrez examiner les événements d’inscription et de réinitialisation des mots de passe que vous pouvez récupérer pour répondre à vos scénarios.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): Répertorie les colonnes disponibles pour les événements de réinitialisation de mot de passe
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): Répertorie les colonnes disponibles pour les événements d’inscription de réinitialisation de mot de passe

### <a name="reporting-api-data-retrieval-limitations"></a>Création de rapport sur les limitations de récupération des données API
Actuellement, l’API de rapports et d’événements Azure AD récupère jusqu'à **75 000 événements individuels** des types [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) et [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent), couvrant les **30 derniers jours**.

Si vous avez besoin de récupérer ou de stocker des données au-delà de cette période, nous vous suggérons de les conserver dans une base de données externe et d’utiliser l’API pour interroger les deltas résultants. La meilleure solution est de commencer à récupérer ces données lorsque vous démarrez votre processus d’inscription de réinitialisation de mot de passe dans votre entreprise, à les conserver en externe, puis à continuer à suivre les deltas à partir de ce point.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Comment télécharger rapidement les événements d’inscription de réinitialisation de mot de passe avec PowerShell
En plus d’utiliser directement l’API de rapports et d’événements Azure AD, vous pouvez également utiliser le script PowerShell ci-dessous pour les événements d’inscription récents dans votre annuaire. C’est utile si vous souhaitez voir qui s’est inscrit récemment ou souhaitez vous assurer que votre déploiement de réinitialisation de mot de passe se produit conformément à votre attente.

* [Script PowerShell d’activité d’inscription SSPR Azure AD](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

## <a name="how-to-view-password-management-reports-in-the-classic-portal"></a>Comment visualiser les rapports de gestion des mots de passe dans le portail Classic
Pour accéder aux rapports de gestion des mots de passe, procédez comme suit :

1. Cliquez sur l’extension **Active Directory** dans le [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez votre annuaire dans la liste qui apparaît dans le portail.
3. Cliquez sur l’onglet **Rapports** .
4. Regardez sous la section **Journaux d’activité** .
5. Sélectionnez le rapport **Activité de réinitialisation de mot de passe** ou **Activité d’inscription à la réinitialisation de mot de passe**.

## <a name="view-password-reset-registration-activity-in-the-classic-portal"></a>Visualiser l’activité d’inscription de réinitialisation de mot de passe dans le portail Classic
Le rapport Activité d’inscription de réinitialisation de mot de passe montre toutes les inscriptions à la réinitialisation de mot de passe qui ont eu lieu dans votre organisation.  Une inscription à la réinitialisation de mot de passe est affichée dans ce rapport pour tout utilisateur qui a inscrit des informations d’authentification dans le portail d’inscription à la réinitialisation de mot de passe ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

* **Période max** : 30 jours
* **Nombre max de lignes** : 75 000
* **Téléchargeable**: Oui, via un fichier CSV

### <a name="description-of-report-columns"></a>Description des colonnes du rapport
La liste suivante décrit chacune des colonnes du rapport en détail :

* **Utilisateur** : utilisateur ayant tenté d’effectuer une opération d’inscription à la réinitialisation de mot de passe.
* **Rôle** : rôle de l’utilisateur dans l’annuaire.
* **Date et heure** : date et heure de la tentative.
* **Données inscrites** : données d’authentification fournies par l’utilisateur lors de l’inscription à la réinitialisation de mot de passe.

### <a name="description-of-report-values"></a>Description des valeurs du rapport
Le tableau suivant décrit les différentes valeurs autorisées pour chaque colonne :

| des colonnes | Valeurs autorisées et leur signification |
| --- | --- |
| Données inscrites |**Adresse de messagerie de secours** : l’utilisateur a utilisé une autre adresse de messagerie électronique ou une adresse électronique d’authentification pour s’authentifier.<p><p>**Téléphone professionnel** : l’utilisateur a utilisé un téléphone professionnel pour s’authentifier.<p>**Téléphone mobile** : l’utilisateur a utilisé un téléphone portable ou un téléphone d’authentification pour s’authentifier.<p>**Questions de sécurité** : l’utilisateur a utilisé des questions de sécurité pour s’authentifier.<p>**Toute combinaison des éléments ci-dessus (par exemple, Adresse de messagerie de secours + Téléphone mobile)** : se produit lorsqu’une stratégie à 2 voies d’accès est spécifiée et indique les deux méthodes employées par l’utilisateur pour authentifier sa demande de réinitialisation de mot de passe. |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>Visualiser l’activité de réinitialisation de mot de passe dans le portail Classic
Ce rapport montre toutes les tentatives de réinitialisation de mot de passe qui se sont produites dans votre organisation.

* **Période max** : 30 jours
* **Nombre max de lignes** : 75 000
* **Téléchargeable**: Oui, via un fichier CSV

### <a name="description-of-report-columns"></a>Description des colonnes du rapport
La liste suivante décrit chacune des colonnes du rapport en détail :

1. **Utilisateur** : utilisateur ayant tenté d’effectuer une opération de réinitialisation de mot de passe (basé sur le champ ID utilisateur fourni quand l’utilisateur demande à réinitialiser un mot de passe).
2. **Rôle** : rôle de l’utilisateur dans l’annuaire.
3. **Date et heure** : date et heure de la tentative.
4. **Méthode(s) utilisée(s)** : méthodes d’authentification employées par l’utilisateur pour cette opération de réinitialisation.
5. **Résultat** : résultat final de l’opération de réinitialisation de mot de passe.
6. **Détails** : détails de la raison pour laquelle la réinitialisation de mot de passe a généré ce résultat.  Inclut également les étapes d’atténuation que vous pouvez suivre pour résoudre une erreur inattendue.

### <a name="description-of-report-values"></a>Description des valeurs du rapport
Le tableau suivant décrit les différentes valeurs autorisées pour chaque colonne :

| des colonnes | Valeurs autorisées et leur signification |
| --- | --- |
| Méthodes utilisées |**Adresse de messagerie de secours** : l’utilisateur a utilisé une autre adresse de messagerie électronique ou une adresse électronique d’authentification pour s’authentifier.<p>**Téléphone professionnel** : l’utilisateur a utilisé un téléphone professionnel pour s’authentifier.<p>**Téléphone mobile** : l’utilisateur a utilisé un téléphone portable ou un téléphone d’authentification pour s’authentifier.<p>**Questions de sécurité** : l’utilisateur a utilisé des questions de sécurité pour s’authentifier.<p>**Toute combinaison des éléments ci-dessus (par exemple, Adresse de messagerie de secours + Téléphone mobile)** : se produit lorsqu’une stratégie à 2 voies d’accès est spécifiée et indique les deux méthodes employées par l’utilisateur pour authentifier sa demande de réinitialisation de mot de passe. |
| Résultat |**Abandonné** : l’utilisateur a démarré la réinitialisation de mot de passe, puis s’est arrêté à mi-chemin sans terminer<p>**Bloqué** : le compte d’utilisateur n’a pas été autorisé à utiliser la réinitialisation de mot de passe, car il a tenté d’utiliser la page de réinitialisation de mot de passe ou une même méthode de réinitialisation de mot de passe un trop grand nombre de fois durant une période de 24 heures.<p>**Annulé** : l’utilisateur a démarré la réinitialisation de mot de passe, puis il a cliqué sur le bouton Annuler pour annuler la session en cours. <p>**Administrateur contacté** : l’utilisateur a rencontré un problème insoluble lors de sa session et il a cliqué sur le lien « Contactez votre administrateur » au lieu de terminer le flux de réinitialisation de mot de passe.<p>**Échec** : l’utilisateur n’a pas pu réinitialiser un mot de passe, probablement car il n’était pas configuré pour utiliser la fonctionnalité (par exemple, aucune licence, informations d’authentification manquantes, mot de passe géré localement mais écriture différée désactivée).<p>**Réussi** : la réinitialisation du mot de passe a réussi. |
| Détails |Voir le tableau ci-dessous |

### <a name="allowed-values-for-details-column"></a>Valeurs autorisées pour la colonne Détails
Voici la liste des types de résultats que vous pouvez attendre lors de l’utilisation du rapport d’activité de réinitialisation du mot de passe :

| Détails | Type de résultat |
| --- | --- |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par e-mail. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par SMS sur mobile. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par appel vocal sur mobile. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par appel vocal au bureau. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de questions de sécurité. |Abandonné |
| L’utilisateur a abandonné après avoir entré son ID utilisateur. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par e-mail. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par SMS sur mobile. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par appel vocal sur mobile. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par appel vocal au bureau. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par questions de sécurité. |Abandonné |
| L’utilisateur a abandonné avant de choisir un nouveau mot de passe. |Abandonné |
| L’utilisateur a abandonné lors du choix d’un nouveau mot de passe. |Abandonné |
| L’utilisateur a entré un trop grand nombre de codes de vérification par SMS non valides et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois la vérification de la voix par appel sur mobile et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois la vérification de la voix par appel au bureau et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois de répondre à des questions de sécurité et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois de vérifier un numéro de téléphone et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a abandonné avant de se soumettre aux méthodes d’authentification requises. |Annulé |
| L’utilisateur a abandonné avant de soumettre un nouveau mot de passe. |Annulé |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par e-mail. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par SMS sur mobile. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par appel vocal sur mobile. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par appel vocal au bureau. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par question de sécurité. |Administrateur contacté |
| La réinitialisation du mot de passe n’est pas activée pour cet utilisateur. Activez-la sous l’onglet Configuration pour résoudre ce problème. |Échec |
| L’utilisateur n’a pas de licence. Vous pouvez ajouter une licence à l’utilisateur pour résoudre ce problème. |Échec |
| L’utilisateur a tenté de réinitialiser depuis un appareil où les cookies ne sont pas activés. |Échec |
| Un nombre insuffisant de méthodes d’authentification sont définies pour le compte de l’utilisateur. Ajoutez des informations d’authentification pour résoudre ce problème. |Échec |
| Le mot de passe de l’utilisateur est géré localement. Vous pouvez activer l’écriture en différé du mot de passe pour résoudre ce problème. |Échec |
| Nous n’avons pas pu accéder au service de réinitialisation de votre mot de passe local. Vérifiez le journal des événements de votre ordinateur synchronisé. |Échec |
| Nous avons rencontré un problème lors de la réinitialisation du mot de passe local de l’utilisateur. Vérifiez le journal des événements de votre ordinateur synchronisé. |Échec |
| Cet utilisateur n’est pas membre du groupe utilisateurs de réinitialisation de mot de passe. Ajoutez cet utilisateur à ce groupe pour résoudre ce problème. |Échec |
| La réinitialisation du mot de passe a été désactivée pour ce locataire. Voir [ici](http://aka.ms/ssprtroubleshoot) pour résoudre ce problème. |Échec |
| L’utilisateur a réinitialisé le mot de passe. |Réussi |

## <a name="next-steps"></a>Étapes suivantes
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**Forum Aux Questions**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.

