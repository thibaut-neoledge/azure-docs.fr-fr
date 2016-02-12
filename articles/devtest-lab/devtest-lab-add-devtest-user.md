<properties
	pageTitle="Ajouter des propriétaires et des utilisateurs à un DevTest Lab | Microsoft Azure"
	description="Ajoutez en toute sécurité un utilisateur non inclus dans votre abonnement à votre DevTest Lab Microsoft Azure."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="tarcher"/>

# Ajouter des propriétaires et des utilisateurs à un DevTest Lab

## Vue d'ensemble

L'accès à un DevTest Lab est contrôlé par le contrôle d'accès en fonction du rôle (RBAC) Azure. Recherchez [Contrôle d'accès en fonction du rôle (RBAC)](https://azure.microsoft.com/searchresults?query=Role%20Based%20Access%20Control%20%28RBAC%29) dans le portail Azure en version préliminaire pour en savoir plus.

L'accès à votre DevTest Lab s'accorde via deux rôles :

 - **Propriétaire** : les utilisateurs affectés au rôle **Propriétaire** au niveau de l'abonnement Azure ont un accès complet au labo, y compris aux fonctions de gestion et de surveillance.

     > [AZURE.NOTE] Les rôles **Propriétaire** qui sont affectés aux niveaux RBAC autres que le niveau de l'abonnement ne sont pas pris en charge dans DevTest Lab. L'affectation d'un utilisateur au rôle **Propriétaire** dans DevTest Lab n'est pas prise en charge.

 -  **Utilisateur DevTest Lab** : les utilisateurs affectés au rôle **Utilisateur DevTest Lab** peuvent créer, mettre à jour et supprimer des machines virtuelles dans le labo spécifié. Les utilisateurs peuvent être *internes* (membres d'Azure Active Directory pour l'abonnement) ou *externes* (utilisateurs non membres d'Azure AD, par exemple membres d'une organisation partenaire).
	-  Un rôle **Utilisateur DevTest Lab** doit être affecté avec les vignettes **Ajouter des utilisateurs** du labo.
	-  Les utilisateurs du rôle **Utilisateur DevTest Lab** ne peuvent effectuer ces opérations qu'au sein du labo auquel ils sont affectés. Par exemple, un **Utilisateur DevTest Lab** ne peut pas créer de machine virtuelle avec le service de machine virtuelle de l'abonnement. La création d'une machine virtuelle n'est autorisée qu'à partir du compte DevTest Lab.
	- Les utilisateurs *externes* doivent posséder un compte dans l'un des domaines de compte Microsoft (@hotmail.com, @live.com, @msn.com, @passport.com, @outlook.com ou une variante spécifique d'un pays).

## Ajouter un propriétaire à votre labo

DevTest Lab considère le(s) propriétaire(s) d'un abonnement Azure qui contient des labos comme propriétaire(s) de ces labos. Si vous pouvez ajouter des propriétaires supplémentaires à un DevTest Lab via le panneau du labo dans le portail Azure en version préliminaire, ceci n'est pas pris en charge actuellement.

Pour ajouter un propriétaire à un abonnement Azure où vous avez déjà créé ou allez créer des labos, procédez comme suit :

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com).

1. Dans la partie de gauche, cliquez sur **Abonnements**.

	![Lien vers les abonnements](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Cliquez sur l'abonnement qui contiendra le(s) labo(s).

1. Cliquez sur l'icône **Accès**.

	![Accéder aux utilisateurs](./media/devtest-lab-add-devtest-user/access-users.png)

1. Sur le panneau **Utilisateurs**, cliquez sur **Ajouter**.

	![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Sur le panneau **Sélectionner un rôle**, cliquez sur **Propriétaire**.

1. Dans la zone de texte **Utilisateur**, tapez l'adresse e-mail de l'utilisateur que vous souhaitez ajouter comme propriétaire. Si l'utilisateur n'est pas trouvé, vous obtiendrez un message d'erreur expliquant le problème. Si l'utilisateur est trouvé, cet utilisateur sera répertorié sous la zone de texte **Utilisateur**.

1. Cliquez sur le nom d'utilisateur trouvé.

1. Cliquez sur **Sélectionner**.

1. Cliquez sur **OK** pour fermer le panneau **Ajouter un accès**.

1. Lorsque vous reviendrez sur le panneau **Utilisateurs**, vous verrez que l'utilisateur a été ajouté comme propriétaire. Cette personne est désormais propriétaire de tous les labos créés sous cet abonnement et pourra par conséquent effectuer des tâches de propriétaire.

## Ajouter un utilisateur DevTest Lab à votre labo

Pour ajouter un utilisateur DevTest Lab à votre labo, procédez comme suit :

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com).

1. Cliquez sur **Parcourir**.

1. Cliquez sur **DevTest Labs**.

1. Dans la liste des labos, sélectionnez le labo souhaité.

1. Cliquez sur l'icône **Accès**.

	![Accès des utilisateurs](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Sur le panneau **Utilisateurs**, cliquez sur **Ajouter**.

	![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Dans le panneau **Sélectionner un rôle**, cliquez sur **Utilisateur DevTest Lab**

1. Dans le panneau **Ajouter des utilisateurs** :

	1. Le panneau **Ajouter des utilisateurs** affiche la liste des utilisateurs intégrés. Si l'utilisateur souhaité est déjà dans la liste, vous pouvez simplement cliquer sur la ligne de l'utilisateur pour le sélectionner. Une coche s'affiche à gauche de l'utilisateur pour indiquer que l'utilisateur a été sélectionné. Pour sélectionner plusieurs utilisateurs, maintenez la touche **&lt;Ctrl>** enfoncée tout en cliquant sur chaque utilisateur. Pour désélectionner un utilisateur, maintenez la touche **&lt;Ctrl>** enfoncée et cliquez sur l'utilisateur. Un compteur en bas du panneau indique le nombre d'utilisateurs sélectionnés.

	1. Si l'utilisateur n'est pas dans la liste, entrez un compte de messagerie Microsoft valide dans la zone de texte **Utilisateurs**. Si l'adresse de messagerie est valide, l'utilisateur s'affiche sous la zone de texte **Utilisateur**. Il vous suffit de cliquer dessus pour le sélectionner.

	1. Une fois que vous avez sélectionné les utilisateurs que vous souhaitez ajouter au labo, cliquez sur **Sélectionner**.

	1. Cliquez sur **OK** pour fermer le panneau **Ajouter un accès**.

	1. Le panneau **Utilisateurs** affiche les rôles et les utilisateurs ajoutés.

<!---HONumber=AcomDC_0204_2016-->