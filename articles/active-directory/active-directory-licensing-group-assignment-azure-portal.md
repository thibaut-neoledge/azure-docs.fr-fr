---

title: "Affecter des licences à un groupe dans Azure Active Directory | Microsoft Docs"
description: "Comment affecter des licences à l’aide de la gestion des licences basée sur le groupe Azure Active Directory"
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: cd2c8b8bd006d3172cb75f12f4a16fef9b698974
ms.lasthandoff: 03/10/2017


---

# <a name="assign-licenses-to-a-group-of-users-in-azure-active-directory"></a>Affecter des licences à un groupe d’utilisateurs dans Azure Active Directory

Dans cet article, nous allons suivre un scénario de base d’affectation de licences du produit à un groupe d’utilisateurs dans Azure Active Directory (Azure AD), puis vérifier que tous les membres du groupe disposent d’une licence appropriée.

Dans cet exemple, le locataire contient un groupe de sécurité appelé *HR Department*. Ce groupe inclut tous les membres du département des ressources humaines (dans ce cas, environ 1 000 utilisateurs). L’administrateur souhaite attribuer des licences Office 365 Entreprise E3 à l’ensemble du département. Le service Yammer Enterprise inclus dans le produit doit être temporairement désactivé jusqu’à une date ultérieure, lorsque le département sera prêt à l’utiliser. L’administrateur souhaite également déployer des licences Enterprise Mobility + Security sur le même groupe d’utilisateurs.

## <a name="step-1-assign-the-required-licenses"></a>Étape 1 : Affecter les licences requises

1. Connectez-vous au [**portail Azure**](https://portal.azure.com) avec un compte administrateur. Pour pouvoir gérer les licences, le compte doit disposer du rôle Administrateur général ou du rôle Administrateur de compte d’utilisateur.

2. Sélectionnez **Plus de services** dans le volet de navigation de gauche, puis sélectionnez **Azure Active Directory**. Vous pouvez définir ce panneau comme « favori » en cliquant sur l’icône en forme d’étoile ou en l’épinglant au tableau de bord du portail.

3. Dans le panneau **Azure Active Directory**, sélectionnez **Licences**. Cela ouvre un panneau dans lequel vous pouvez afficher et gérer tous les produits sous licence dans le locataire.

4. Sous **Tous les produits**, sélectionnez Office 365 Enterprise E3 et Enterprise Mobility + Security en sélectionnant les noms de produits. Sélectionnez **Attribuer** en haut du panneau pour démarrer l’affectation.

  ![Tous les produits, affecter des licences](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. Dans le panneau **Affecter une licence**, cliquez sur **Utilisateurs et groupes** pour ouvrir le panneau **Utilisateurs et groupes**. Recherchez le nom du groupe *HR Department*, sélectionnez le groupe, puis veillez à confirmer en cliquant sur **Sélectionner** en bas du panneau.

  ![Sélectionner un groupe](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. Dans le panneau **Affecter une licence**, cliquez sur **Options d’affectation (facultatif)** qui affiche tous les plans de service comprenant les deux produits que nous avons sélectionnés précédemment. Recherchez **Yammer Enterprise** et définissez-le sur **Désactivé** pour désactiver ce service à partir de la licence du produit. Confirmez en cliquant sur **OK** en bas des **options d’affectation**.

  ![Options d’affectation](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Pour finir, dans le panneau **Affecter une licence**, cliquez sur **Attribuer** en bas du panneau pour terminer l’affectation.

8. Une notification indiquant l’état et le résultat du processus s’affiche dans le coin supérieur droit. Si l’affectation au groupe n’a pas pu être effectuée (par exemple en raison de licences existantes dans le groupe), cliquez sur la notification pour afficher les détails de l’échec.

Nous avons maintenant spécifié un modèle de licence sur le groupe HR Department. Un processus en arrière-plan dans Azure AD a été démarré pour traiter tous les membres existants de ce groupe. Cette opération initiale peut prendre un certain temps en fonction de la taille du groupe. Dans l’étape suivante, nous allons voir comment vérifier que le processus est terminé et si une attention supplémentaire est requise pour résoudre les problèmes.

> [!NOTE]
> Vous pouvez démarrer la même affectation à partir d’un autre emplacement : **Utilisateurs et groupes** dans Azure AD. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les groupes**. Recherchez le groupe, sélectionnez-le et cliquez sur l’onglet **Licences**. Le bouton **Attribuer** en haut du panneau permet d’ouvrir le panneau d’affectation de licences.

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>Étape 2 : Vérifier que l’affectation initiale est terminée

1. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les groupes**. Recherchez le groupe *HR Department* auquel ont été affectées les licences.

2. Dans le panneau du groupe *HR Department*, sélectionnez **Licences** pour vérifier rapidement si les licences ont été entièrement affectées aux utilisateurs et si des erreurs doivent être étudiées. Les informations suivantes sont disponibles :

  - Liste des licences du produit qui sont actuellement affectées au groupe. Sélectionnez une entrée pour afficher les services spécifiques qui ont été activés et pour apporter des modifications.

  - État des dernières modifications de licences effectuées sur le groupe (si les modifications sont en cours de traitement ou si le traitement a été effectué sur tous les membres de l’utilisateur).

  - Informations sur les utilisateurs qui se trouvent à l’état d’erreur (impossible de leur affecter des licences).

  ![Options d’affectation](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Pour des informations plus détaillées sur le traitement des licences, consultez **Azure Active Directory** > **Utilisateurs et groupes** > *nom du groupe* > **Journaux d’audit**. Notez les activités suivants :

  - Activité : **Commencer à appliquer une licence basée sur le groupe aux utilisateurs**. Elle est consignée lorsque le système sélectionne la modification d’affectation de licences sur le groupe et commence à l’appliquer à tous les membres de l’utilisateur. Elle contient des informations sur la modification qui a été apportée.

  - Activité : **Terminer l’application d’une licence basée sur le groupe aux utilisateurs**. Elle est consignée lorsque le système termine le traitement de tous les utilisateurs du groupe. Elle contient un résumé du nombre d’utilisateurs qui ont été traités avec succès et du nombre d’utilisateurs auxquels les licences du groupe n’ont pas pu être affectées.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Étape 3 : Rechercher des problèmes de licences et les résoudre

1. Accédez à **Azure Active Directory** > **Utilisateurs et groupes** > **Tous les groupes** et recherchez le groupe *HR Department* auquel des licences ont été affectées.
2. Dans le panneau **HR Department**, sélectionnez **Licences**. La notification en haut du panneau indique que des licences n’ont pas pu être affectées à 10 utilisateurs. En cliquant sur cette notification, vous accédez à une liste de tous les utilisateurs associés à une erreur de licence pour ce groupe.
3. La colonne **Échec d’affectations** indique que les deux licences de produit n’ont pas pu être affectées aux utilisateurs. La colonne **Raison principale de l’échec** contient la cause de l’échec. Dans ce cas, il s’agit de **Plans de service en conflit**.

  ![Échec d’affectations](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Sélectionnez un utilisateur pour ouvrir le panneau **Licences**. Ce panneau répertorie toutes les licences qui sont actuellement affectées à l’utilisateur. Dans cet exemple, nous pouvons voir que l’utilisateur a hérité de la licence Office 365 Enterprise E1 du groupe **Kiosk users**. Cette licence est en conflit avec la licence E3 que le système a tenté d’appliquer à partir du groupe **HR Department**. Par conséquent, aucune des licences de ce groupe n’a été attribuée à l’utilisateur.

  ![Afficher les licences pour un utilisateur](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Pour résoudre ce problème, nous supprimons l’utilisateur du groupe **Kiosk users**. Lorsqu’Azure AD a traité la modification, les licences **HR Department** sont affectées correctement.

  ![Licence affectée correctement](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’ensemble de fonctionnalités de gestion des licences par le biais des groupes, consultez les articles suivants :

* [What is group-based licensing in Azure Active Directory? (Présentation des licences basées sur le groupe dans Azure Active Directory)](active-directory-licensing-whatis-azure-portal.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Migration des utilisateurs individuels sous licence vers les licences basées sur les groupes dans Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Autres scénarios de licences basées sur les groupes Azure Active Directory](active-directory-licensing-group-advanced.md)

