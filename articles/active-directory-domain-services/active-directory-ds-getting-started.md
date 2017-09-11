---
title: "Bien démarrer avec Azure Active Directory Domain Services | Microsoft Docs"
description: "Activer Azure Active Directory Domain Services à l’aide du portail Azure (préversion)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: d2d570bc2bab3bfd28282cd95b0a1be65e96743d
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Activer Azure Active Directory Domain Services à l’aide du portail Azure (préversion)
Cet article explique comment activer Azure Active Directory Domain Services (Azure AD DS) au moyen du portail Azure.

Pour lancer l’Assistant **Activer Azure AD Domain Services**, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans le volet gauche, cliquez sur **Nouveau**.
3. Dans le panneau **Nouveau**, tapez **Domain Services** dans la barre de recherche.

    ![Rechercher Domain Services](./media/getting-started/search-domain-services.png)

4. Dans la liste des suggestions de recherche, cliquez pour sélectionner **Azure AD Domain Services**. Dans le panneau **Azure AD Domain Services**, cliquez sur le bouton **Créer**.

    ![Panneau Domain Services](./media/getting-started/domain-services-blade.png)

5. L’Assistant **Activer Azure AD Domain Services** est lancé.


## <a name="task-1-configure-basic-settings"></a>Tâche 1 : Configurer les paramètres de base
Dans la page **Fonctions de base** de l’Assistant, vous pouvez spécifier le nom de domaine DNS pour le domaine managé. Vous pouvez également choisir le groupe de ressources et l’emplacement Azure sur lequel le domaine managé doit être déployé.

![Configurer les fonctions de base](./media/getting-started/domain-services-blade-basics.png)

1. Choisissez le **Nom de domaine DNS** pour votre domaine managé.

   * Le nom de domaine par défaut du répertoire (avec un suffixe **.onmicrosoft.com**) est spécifié par défaut.

   * Vous pouvez également entrer un nom de domaine personnalisé. Dans cet exemple, le nom de domaine personnalisé est *contoso100.com*.

     > [!WARNING]
     > Le préfixe du nom de domaine spécifié (par exemple, *contoso100* dans le nom de domaine *contoso100.com*) doit contenir au maximum 15 caractères. Vous ne pouvez pas créer de domaine managé avec un préfixe de plus de 15 caractères.
     >
     >

2. Vérifiez que le nom de domaine DNS choisi pour le domaine géré n’existe pas au sein du réseau virtuel. Plus spécifiquement, vérifiez les poins suivants :

   * Vous disposez d’un domaine présentant le nom de domaine DNS au sein du réseau virtuel.

   * Le réseau virtuel dans lequel vous envisagez d’activer le domaine managé a une connexion VPN avec votre réseau local. Dans ce scénario, veillez à ne pas avoir de domaine portant le même nom de domaine DNS sur votre réseau local.

   * Il existe un service cloud portant ce nom sur le réseau virtuel.

3. Choisissez le **type de réseau virtuel**. Le type de réseau virtuel **Resource Manager** est sélectionné par défaut. Nous vous recommandons d’utiliser ce type de réseau virtuel pour tous les nouveaux domaines managés.

4. Sélectionnez l’**Abonnement** Azure dans lequel vous souhaitez créer le domaine managé.

5. Sélectionnez le **Groupe de ressources** auquel le domaine managé doit appartenir. Vous avez le choix entre les options **Créer** ou **Utiliser l’existant** pour sélectionner le groupe de ressources.

6. Choisissez l’**Emplacement** Azure dans lequel créer le domaine managé. Dans la page **Réseau** de l’Assistant, vous voyez uniquement les réseaux virtuels appartenant à l’emplacement que vous avez sélectionné.

7. Lorsque vous avez terminé, cliquez sur **OK** pour accéder à la page **Réseau** de l’Assistant.


## <a name="next-step"></a>Étape suivante
[Tâche 2 : Configurer les paramètres réseau](active-directory-ds-getting-started-network.md)

