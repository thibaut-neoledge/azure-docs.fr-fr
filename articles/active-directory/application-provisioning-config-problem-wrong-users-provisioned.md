---
title: "Un mauvais ensemble d’utilisateurs est affecté à une application de la galerie Azure AD | Microsoft Docs"
description: "Découvrez pourquoi un autre ensemble d’utilisateurs que celui que vous attendiez est affecté à une application"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 5ccb9e920ce8e1c95b9ce0ffc8626f2dcf3783d8
ms.contentlocale: fr-fr
ms.lasthandoff: 04/11/2017

---

<a id="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application" class="xliff"></a>

# Un mauvais ensemble d’utilisateurs est affecté à une application de la galerie Azure AD

Le choix des utilisateurs affectés à l’application dépend principalement des utilisateurs et groupes qui ont été **affectés** à l’application.

Utilisez les ressources ci-dessous pour déterminer quels utilisateurs et groupes ont été affectés à une application dans Azure Active Directory.

<a id="assign-a-user-directly-as-an-administrator" class="xliff"></a>

## Affecter un utilisateur directement en tant qu’administrateur

Pour affecter un ou plusieurs utilisateurs directement à une application, effectuez les étapes suivantes :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général.**

2.  Ouvrez l’**extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher une liste de toutes vos applications.

  * Si l’application que vous recherchez n’apparaît pas, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7.  Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le panneau **Ajouter une attribution**.

9.  Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du panneau **Ajouter une attribution**.

10. Tapez **le nom complet** ou **l’adresse de messagerie** de l’utilisateur souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur **l’utilisateur** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo de l’utilisateur pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs utilisateurs**, entrez un autre **nom complet** ou une autre **adresse de messagerie** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter cet utilisateur à la liste **Sélectionné**.

13. Lorsque vous avez fini de sélectionner les utilisateurs, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le panneau **Ajouter une attribution** pour sélectionner un rôle à affecter aux utilisateurs que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux utilisateurs sélectionnés.

Si l’approvisionnement est configuré et déjà en cours d’exécution pour une application, les nouveaux utilisateurs doivent être affectés à une application en 10 minutes environ. Consultez **les journaux d’audit** pour plus d’informations.

<a id="assign-a-group-directly-to-an-application-as-an-administrator" class="xliff"></a>

## Affecter un groupe directement à une application en tant qu’administrateur

Pour affecter un ou plusieurs groupes directement à une application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant qu’**administrateur général.**

2.  Ouvrez l’**extension Azure Active Directory** en cliquant sur **Plus de services** en bas du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche du filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher une liste de toutes vos applications.

  * Si l’application que vous recherchez n’apparaît pas, utilisez la commande **Filtre** en haut de la **liste de toutes les applications** et définissez l’option **Afficher** sur **Toutes les applications.**

6.  Dans la liste qui s’affiche, sélectionnez l’application à laquelle vous souhaitez affecter un utilisateur.

7.  Une fois l’application chargée, cliquez sur **Utilisateurs et groupes** dans le menu de navigation de gauche de l’application.

8.  Cliquez sur le bouton **Ajouter** en haut de la liste **Utilisateurs et groupes** pour ouvrir le panneau **Ajouter une attribution**.

9.  Cliquez sur le sélecteur **Utilisateurs et groupes** à partir du panneau **Ajouter une attribution**.

10. Tapez le **nom de groupe complet** du groupe souhaité pour l’attribution dans la zone de recherche **Rechercher par nom ou adresse de messagerie**.

11. Pointez sur le **groupe** dans la liste pour afficher une **case à cocher**. Cliquez sur la case à cocher en regard de la photo de profil ou du logo du groupe pour ajouter ce dernier à la liste **Sélectionné**.

12. **Facultatif :** si vous souhaitez **ajouter plusieurs groupes**, entrez un autre **nom de groupe complet** dans la zone de recherche **Rechercher par nom ou adresse de messagerie**, puis cliquez sur la case à cocher pour ajouter ce groupe à la liste **Sélectionné**.

13. Lorsque vous avez fini de sélectionner les groupes, cliquez sur le bouton **Sélectionner** pour les ajouter à la liste des utilisateurs et des groupes à affecter à l’application.

14. **Facultatif :** cliquez sur le sélecteur **Sélectionner un rôle** dans le panneau **Ajouter une attribution** pour sélectionner un rôle à affecter aux groupes que vous avez sélectionnés.

15. Cliquez sur le bouton **Attribuer** pour affecter l’application aux groupes sélectionnés.

Si l’approvisionnement est configuré et déjà en cours d’exécution pour une application, les nouveaux utilisateurs de ce groupe doivent être affectés à une application en 10 minutes environ. Consultez **les journaux d’audit** pour plus d’informations.

>[!IMPORTANT]
>Approvisionnement du nom du groupe et des détails du groupe, en plus des membres, si la prise en charge est effective pour certaines applications. Vous pouvez activer ou désactiver cette fonctionnalité en activant ou désactivant la **mappage** pour les objets de groupe affichés dans l’onglet **Approvisionnement**. 
>
>

Si les groupes de configuration sont activés, veillez à passer en revue les mappages d’attributs afin de vous assurer qu'un champ approprié est utilisé pour l’« ID correspondant ». Il peut s’agir du nom d’affichage ou de l’alias de courrier électronique, comme le groupe et ses membres qui ne sont pas approvisionnés si la propriété correspondante est vide ou n’est pas remplie pour un groupe dans Azure AD.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
[Automatisation de l’approvisionnement et de la l’annulation de l’approvisionnement des utilisateurs pour les applications SaaS avec Azure Active Directory](active-directory-saas-app-provisioning.md)

