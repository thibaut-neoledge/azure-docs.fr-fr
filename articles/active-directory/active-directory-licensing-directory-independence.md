---
title: "Ajouter et gérer plusieurs annuaires Azure Active Directory | Microsoft Docs"
description: "Instructions et meilleures pratiques concernant l’ajout et la gestion de vos annuaires Azure Active Directory, les annuaires étant considérés comme une ressource entièrement indépendante"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 2b862b75-14df-45f2-a8ab-2a3ff1e2eb08
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: a4a78b92c8bb9e8aff25fd724ed78283de8f2fd8
ms.openlocfilehash: 23aad0421088b620f32bbf3f6b3f0b11e001e3ae


---
# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Ajouter et gérer plusieurs annuaires Azure Active Directory
Dans Azure Active Directory (Azure AD), chaque annuaire est une ressource entièrement indépendante : un homologue complet et logiquement indépendant des autres annuaires que vous gérez. Il n’existe aucune relation parent-enfant entre les annuaires. Cette indépendance entre les annuaires vaut pour les ressources, l’administration et la synchronisation.

## <a name="resource-independence"></a>Indépendance des ressources
Si vous créez ou supprimez une ressource dans un annuaire, cela n’a aucun effet sur les ressources d’un autre annuaire, si l’on excepte le cas des utilisateurs externes, comme décrit ci-dessous. Si vous utilisez un domaine personnalisé (par exemple, « contoso.com ») pour un annuaire, il ne peut être utilisé avec aucun autre annuaire.

## <a name="administrative-independence"></a>Indépendance de l’administration
Si un utilisateur non administrateur de l’annuaire « Contoso » crée l’annuaire de test « Test », alors : 

* Par défaut, l’utilisateur qui crée un annuaire est ajouté comme utilisateur externe dans le nouvel annuaire et se voit attribuer rôle d’administrateur global dans cet annuaire.
* Les administrateurs de l’annuaire « Contoso » n’ont pas de privilèges d’administration directs sur l’annuaire « Test », à moins qu’un administrateur de « Test » leur ait spécifiquement accordé ces privilèges. Les administrateurs de « Contoso » peuvent contrôler l’accès à l’annuaire « Test » s’ils contrôlent le compte d’utilisateur qui a créé « Test ».
* Si vous modifiez le rôle d’administrateur d’un utilisateur dans un annuaire (ou que vous l’ajoutez ou le modifiez), la modification n’affecte pas le rôle d’administrateur que l’utilisateur peut avoir dans un autre annuaire.

## <a name="synchronization-independence"></a>Indépendance de la synchronisation
Vous pouvez configurer chaque annuaire Azure AD de manière indépendante pour obtenir la synchronisation des données à partir d’une même instance de l’un des éléments suivants :

* l’outil de synchronisation d’annuaire (DirSync), pour synchroniser les données avec une seule forêt AD ;
* le connecteur Azure Active Directory pour Forefront Identity Manager, pour synchroniser les données avec une ou plusieurs forêts locales et/ou des sources de données non Azure AD.

## <a name="add-an-azure-ad-directory"></a>Ajouter un annuaire Azure AD
Pour ajouter un annuaire Azure AD dans le portail Azure Classic, sélectionnez l’extension Azure Active Directory sur la gauche, puis appuyez sur **Ajouter**.

> [!NOTE]
> Contrairement aux autres ressources Azure, vos annuaires ne sont pas des ressources enfants d’un abonnement Azure. Si vous annulez votre abonnement ou que vous le laissez arriver à expiration, vous pourrez toujours accéder à vos données d’annuaire à l’aide d’Azure PowerShell, de l’API Graph Azure ou d’autres interfaces, telles que le Centre d’administration Office 365. Vous pouvez également associer un autre abonnement à l’annuaire.
>
>

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une vue d’ensemble des problèmes de licence Azure AD et pour découvrir les meilleures pratiques, voir l’article [Qu’est-ce que la gestion des licences Azure Active Directory ?](active-directory-licensing-what-is.md).



<!--HONumber=Feb17_HO2-->


