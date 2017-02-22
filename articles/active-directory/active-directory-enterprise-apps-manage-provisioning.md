---
title: "Gestion de l’approvisionnement d’utilisateurs pour les applications d’entreprise dans la version préliminaire d’Azure Active Directory | Microsoft Docs"
description: "Découvrez comment gérer l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise à l’aide de la version préliminaire d’Azure Active Directory"
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/12/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 103eade46452d979705e06dd77441f42d7a514b8


---
# <a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Version préliminaire : Gestion de l’approvisionnement de comptes d’utilisateur pour les applications d’entreprise dans le nouveau portail Azure
Cet article explique comment utiliser le [portail Azure](https://portal.azure.com) pour gérer l’approvisionnement et l’annulation de l’approvisionnement automatiques de comptes d’utilisateur pour les applications qui les prennent en charge, en particulier celles qui ont été ajoutées depuis la catégorie « Sélection » de la [galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Cette expérience de gestion du nouveau portail Azure est actuellement en version préliminaire publique. Cet article décrit les nouvelles fonctionnalités, ainsi que les quelques limitations temporaires appliquées pendant la durée de la version préliminaire. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)

Pour en savoir plus sur l’approvisionnement automatique de comptes d’utilisateur, consultez [Automatisation de l’approvisionnement et de l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-new-portal"></a>Recherche de vos applications dans le nouveau portail
Depuis septembre 2016, toutes les applications qui ont été configurées pour l’authentification unique dans un annuaire par un administrateur d’annuaire à l’aide de la [galerie d’applications Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) du [portail Azure Classic](https://manage.windowsazure.com) peuvent être affichées et gérées dans le nouveau portail Azure.

Ces applications se trouvent dans la section **Applications d’entreprise** du nouveau portail Azure, qui est accessible via le menu **Plus de services** dans la zone de navigation de gauche. Les applications d’entreprise sont des applications qui ont été déployées et sont utilisées au sein de votre organisation.

![Panneau Applications d’entreprise][0]

La sélection du lien **Toutes les applications** à gauche permet d’afficher une liste de toutes les applications qui ont été configurées, y compris les applications ajoutées depuis la galerie. La sélection d’une application charge le panneau de ressources de cette application, où vous pouvez consulter des rapports pour cette application et gérer un large éventail de paramètres.

Les paramètres d’approvisionnement de comptes d’utilisateur peuvent être gérés en sélectionnant **Approvisionnement** sur la gauche.

![Panneau de ressources d’application][1]

## <a name="provisioning-modes"></a>Modes d’approvisionnement
Le panneau **Approvisionnement** commence par un menu **Mode**, qui indique les modes d’approvisionnement pris en charge pour une application d’entreprise et permet de les configurer. Les options disponibles incluent :

* **Automatique** : cette option est affichée si Azure AD prend en charge l’approvisionnement ou l’annulation de l’approvisionnement automatiques basés sur une API de comptes d’utilisateur pour cette application. La sélection de ce mode affiche une interface qui guide les administrateurs à travers la configuration d’Azure AD pour se connecter à l’API de gestion des utilisateurs de l’application, la création de mappages et de flux de travail de comptes définissant le mode de circulation des données de comptes d’utilisateur entre Azure AD et l’application, et la gestion du service d’approvisionnement d’Azure AD.
* **Manuel** : cette option est affichée si Azure AD ne prend pas en charge l’approvisionnement automatique de comptes d’utilisateur pour cette application. Avec cette option, les enregistrements de comptes d’utilisateur stockés dans l’application doivent être gérés à l’aide d’un processus externe qui dépend des fonctionnalités de gestion et d’approvisionnement des utilisateurs fournies par cette application (par exemple l’approvisionnement SAML juste-à-temps).

## <a name="configuring-automatic-user-account-provisioning"></a>Configuration de l’approvisionnement automatique de comptes d’utilisateur
La sélection de l’option **Automatique** affiche un écran divisé en quatre sections :

### <a name="admin-credentials"></a>Admin Credentials (Informations d’identification de l’administrateur)
C’est là que les informations d’identification requises par Azure AD pour se connecter à l’API de gestion des utilisateurs de l’application doivent être saisies. Les entrées requises dépendent de l’application. Pour en savoir plus sur les types d’informations d’identification requis pour une application spécifique, consultez le [didacticiel de configuration de cette application](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

La sélection du bouton **Tester la connexion** vous permet de tester les informations d’identification. Azure AD essaie alors de se connecter à l’API de configuration de l’application à l’aide des informations d’identification fournies.

### <a name="mappings"></a>Mappages
C’est là que les administrateurs peuvent afficher et modifier les attributs utilisateur qui circulent entre Azure AD et l’application cible lorsque des comptes d’utilisateur sont configurés ou mis à jour.

Il existe un ensemble préconfiguré de mappages entre les objets utilisateur Azure AD et les objets utilisateur de chaque application SaaS. Certaines applications gèrent d’autres types d’objets, tels que des groupes ou des contacts. La sélection d’un de ces mappages dans le tableau affiche l’éditeur de mappage, qui permet de le visualiser et de le personnaliser.

![Panneau de ressources d’application][2]

Les personnalisations prises en charge pendant la période de la première version préliminaire incluent :

* L’activation et la désactivation des mappages d’objets spécifiques, par exemple entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application SaaS.
* La modification des attributs qui circulent entre l’objet utilisateur Azure AD et l’objet utilisateur de l’application. Pour plus d’informations sur le mappage d’attributs, voir [Présentation des types de mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Le filtrage des actions d’approvisionnement qui doivent être exécutées par Azure AD sur l’application cible, ce qui est une nouvelle fonctionnalité du portail Azure. Plutôt qu’Azure AD synchronise entièrement les objets, vous pouvez limiter les actions exécutées. Par exemple, si vous sélectionnez uniquement **Mettre à jour**, Azure AD met à jour les comptes d’utilisateur d’une application et n’en crée pas de nouveaux. Si vous sélectionnez uniquement **Créer**, Azure crée des comptes d’utilisateur, mais ne met pas à jour les comptes existants. Cette fonctionnalité permet aux administrateurs de créer des mappages différents pour les flux de travail de création et de mise à jour de comptes. La pleine possibilité de créer plusieurs mappages par application est prévue ultérieurement au cours de la période de la version préliminaire.

### <a name="settings"></a>Settings
Cette section permet aux administrateurs de démarrer et d’arrêter le service d’approvisionnement d’Azure AD pour l’application sélectionnée, et au besoin d’effacer le cache d’approvisionnement et de redémarrer le service.

Si c’est la première fois que l’approvisionnement est activé pour une application, activez le service en définissant le paramètre **État de la configuration** sur **Activé**. Le service d’approvisionnement d’Azure AD exécute alors une synchronisation initiale, durant laquelle il lit les utilisateurs affectés dans la section **Utilisateurs et groupes**, interroge l’application cible à leur sujet, puis effectue les actions d’approvisionnement définies dans la section **Mappages** d’Azure AD. Au cours de ce processus, le service d’approvisionnement stocke des données en cache sur les comptes d’utilisateur qu’il gère, de sorte que les opérations d’annulation de l’approvisionnement n’ont aucun effet sur les comptes non gérés des applications cibles qui n’ont jamais été concernés par l’affectation. Après la synchronisation initiale, le service d’approvisionnement synchronise automatiquement les objets utilisateur et groupe toutes les dix minutes.

La définition du paramètre **État de la configuration** sur **Désactivé** suspend simplement le service d’approvisionnement. Dans cet état, Azure ne crée, met à jour ou supprime aucun objet utilisateur ou groupe dans l’application. Si l’état est de nouveau défini sur Activé, le service reprend là où il s’était arrêté.

Si la case à cocher **Clear current state and restart synchronization** (Effacer l’état en cours et redémarrer la synchronisation) est activée et la configuration enregistrée, le service d’approvisionnement s’arrête, vide les données en cache sur les comptes d’utilisateur gérés par Azure AD, redémarre les services et exécute à nouveau la synchronisation initiale. Cette option permet aux administrateurs de recommencer le processus de déploiement d’approvisionnement.

### <a name="synchronization-details"></a>Détails de la synchronisation
Cette section fournit des informations supplémentaires sur le fonctionnement du service d’approvisionnement, y compris la première et la dernière exécution du service d’approvisionnement pour l’application et le nombre d’objets utilisateur et groupe gérés.

Elle contient également des liens vers le **rapport d’activité d’approvisionnement**, qui fournit un journal de tous les utilisateurs et les groupes créés, mis à jour et supprimés entre Azure AD et l’application cible, et vers le **rapport d’erreurs d’approvisionnement**, qui fournit des messages d’erreur plus détaillés pour les objets utilisateur et groupe dont la lecture, la création, la mise à jour ou la suppression a échoué. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG



<!--HONumber=Feb17_HO3-->


