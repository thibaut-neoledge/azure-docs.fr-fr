<properties
   pageTitle="Ajouter et gérer plusieurs annuaires Azure AD | Microsoft Azure"
   description="Instructions et meilleures pratiques concernant l’ajout et la gestion de vos annuaires Azure AD, les annuaires étant considérés comme une ressource entièrement indépendante"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/16/2015"
   ms.author="curtand"/>

# Ajouter et gérer plusieurs annuaires Azure AD

Dans Azure AD, chaque annuaire est une ressource entièrement indépendante : un homologue complet et logiquement indépendant des autres annuaires que vous gérez. Il n’existe aucune relation parent-enfant entre les annuaires. Cette indépendance entre les annuaires vaut pour les ressources, l’administration et la synchronisation.

##Indépendance des ressources

Si vous créez ou supprimez une ressource dans un annuaire, cela n’a aucun effet sur les ressources d’un autre annuaire, si l’on excepte le cas des utilisateurs externes, comme décrit ci-dessous. Si vous utilisez un domaine personnalisé (par exemple, « contoso.com ») pour un annuaire, il ne peut être utilisé avec aucun autre annuaire.

##Indépendance de l’administration

Si un utilisateur non administrateur de l’annuaire « Contoso » crée l’annuaire de test « Test », alors : - Par défaut, l’utilisateur qui crée un annuaire est ajouté comme utilisateur externe dans ce nouvel annuaire et se voit attribuer le rôle d’administrateur global dans cet annuaire. - Les administrateurs de l’annuaire « Contoso » ne disposent pas de privilèges administratifs directs sur l’annuaire « Test », à moins qu’un administrateur de « Test » leur ait spécifiquement accordé ces privilèges. Les administrateurs de « Contoso » peuvent contrôler l’accès à l’annuaire « Test » s’ils contrôlent le compte d’utilisateur qui a créé « Test ». - Si vous modifiez (ajoutez ou supprimez) le rôle d’administrateur d’un utilisateur dans un annuaire, cette modification n’affecte pas le rôle d’administrateur que l’utilisateur peut avoir dans un autre annuaire.

##Indépendance de la synchronisation

Vous pouvez configurer chaque annuaire Azure AD de manière indépendante pour déclencher la synchronisation des données à partir d’une même instance de l’un des éléments suivants : - L’outil de synchronisation d’annuaires (DirSync), afin de synchroniser les données avec une seule forêt AD. - Le connecteur Azure Active Directory pour Forefront Identity Manager, afin de synchroniser les données avec une ou plusieurs forêts locales et/ou des sources de données non Azure AD.

##Ajouter un annuaire Azure AD

Pour ajouter un annuaire Azure AD dans le Portail de gestion Azure, sélectionnez l’extension Active Directory sur la gauche, puis appuyez sur **Ajouter**.

> [AZURE.NOTE]Contrairement aux autres ressources Azure, vos annuaires ne sont pas des ressources enfants d’un abonnement Azure. Si vous annulez votre abonnement ou que vous le laissez arriver à expiration, vous pourrez toujours accéder à vos données d’annuaire à l’aide d’Azure PowerShell, de l’API Graph Azure ou d’autres interfaces, telles que le Centre d’administration Office 365. Vous pouvez également associer un autre abonnement à l’annuaire.

Pour obtenir une vue d’ensemble des problèmes de licence Azure AD et pour découvrir les meilleures pratiques, voir l’article [Qu’est-ce que la gestion des licences Azure Active Directory ?](active-directory-licensing-what-is.md).

<!---HONumber=July15_HO4-->