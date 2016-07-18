<properties
	pageTitle="Ajouter des propriétaires et des utilisateurs à un laboratoire | Microsoft Azure"
	description="Ajouter en toute sécurité à Azure DevTest Labs un utilisateur non inclus dans votre abonnement"
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
	ms.date="07/05/2016"
	ms.author="tarcher"/>

# Ajouter des propriétaires et des utilisateurs à un laboratoire

> [AZURE.NOTE] Regardez la vidéo d’accompagnement de cet article : [Comment configurer la sécurité dans DevTest Labs](/documentation/videos/how-to-set-security-in-your-devtest-lab) (How to set security in DevTest Labs).

## Vue d'ensemble
L’accès à DevTest Labs est contrôlé par le contrôle d’accès en fonction du rôle (RBAC) d’Azure. Pour plus d’informations, recherchez [Contrôle d’accès en fonction du rôle (RBAC)](https://azure.microsoft.com/search/?q=role%20based%20access%20control) dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).


L'accès à votre labo s'accorde selon deux rôles :

- **Propriétaire** : les utilisateurs affectés au rôle **Propriétaire** au niveau du laboratoire ont un accès complet au laboratoire, notamment aux fonctions de gestion et de surveillance. Le rôle **Propriétaire** affecté au niveau du laboratoire n’accorde pas aux utilisateurs les autorisations d’accès aux ressources de l’abonnement en dehors de l’étendue du laboratoire. Les utilisateurs affectés au rôle **Propriétaire** au niveau de l’abonnement Azure ont automatiquement des droits d’accès **Propriétaire** aux ressources créées dans cet abonnement, y compris aux laboratoires et aux machines virtuelles.

-  **Utilisateur DevTest Labs** : les utilisateurs affectés au rôle **Utilisateur DevTest Labs** peuvent créer des machines virtuelles dans le laboratoire spécifié, et consulter toutes les ressources du laboratoire, telles que les machines virtuelles, les stratégies ou les réseaux virtuels. Les utilisateurs peuvent être *internes* (membres d'Azure Active Directory pour l'abonnement) ou *externes* (utilisateurs non membres d'Azure AD, par exemple membres d'une organisation partenaire).
	-  Un rôle **Utilisateur DevTest Labs** doit être affecté via les vignettes **Ajouter des utilisateurs** du laboratoire.
	-  Les utilisateurs du rôle **Utilisateur DevTest Labs** peuvent effectuer ces opérations seulement au sein du laboratoire auquel ils sont affectés. Par exemple, un **Utilisateur DevTest Labs** ne peut pas créer une machine virtuelle avec le service Virtual Machines de l’abonnement. La création d’une machine virtuelle est autorisée seulement à partir du compte DevTest Labs.
	- Les utilisateurs *externes* sont des utilisateurs avec un compte Microsoft (MSA).
 
Une fois qu’une machine virtuelle est créée, l’utilisateur qui l’a créée est automatiquement affecté au rôle **Propriétaire** sur la machine virtuelle créée, ce qui lui permet d’effectuer toutes les actions proposées dans le laboratoire.

## Ajouter un propriétaire à votre labo

En raison de la façon dont les autorisations sont propagées de l’étendue parent à l’étendue enfant dans Azure, les propriétaires d’un abonnement Azure contenant des laboratoires sont automatiquement propriétaires de ces laboratoires, ainsi que de toutes les machines virtuelles et autres ressources créées par les utilisateurs du laboratoire et par le service DevTest Lab. Bien que vous puissiez ajouter des propriétaires à un laboratoire via le panneau du laboratoire dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040), leur étendue d’administration sera plus restreinte que celle des propriétaires d’abonnement, car ils n’auront pas accès à certaines ressources créées dans l’abonnement par le service DevTest Labs.

Pour ajouter un propriétaire à un abonnement Azure où vous avez déjà créé ou allez créer des labos, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Dans la partie de gauche, sélectionnez **Abonnements**.

	![Lien vers les abonnements](./media/devtest-lab-add-devtest-user/subscriptions.png)
	
1. Sélectionnez l'abonnement qui contiendra le(s) labo(s).  


1. Sélectionnez l’icône **Accès**.

	![Accéder aux utilisateurs](./media/devtest-lab-add-devtest-user/access-users.png)

1. Dans le panneau **Utilisateurs**, sélectionnez **Ajouter**.

	![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Sur le panneau **Sélectionner un rôle**, sélectionnez **Propriétaire**.

1. Dans la zone de texte **Utilisateur**, tapez l'adresse e-mail de l'utilisateur que vous souhaitez ajouter comme propriétaire. Si l'utilisateur n'est pas trouvé, vous obtiendrez un message d'erreur expliquant le problème. Si l'utilisateur est trouvé, cet utilisateur sera répertorié sous la zone de texte **Utilisateur**.

1. Sélectionnez le nom d'utilisateur trouvé.

1. Sélectionnez **Sélectionner**.

1. Sélectionnez **OK** pour fermer le panneau **Ajouter un accès**.

1. Lorsque vous reviendrez sur le panneau **Utilisateurs**, vous verrez que l'utilisateur a été ajouté comme propriétaire. Cette personne est désormais propriétaire de tous les labos créés sous cet abonnement et pourra par conséquent effectuer des tâches de propriétaire.

## Ajouter un utilisateur DevTest Labs à votre laboratoire

Pour ajouter un utilisateur DevTest Labs à votre laboratoire, procédez comme suit :

1. Connectez-vous au [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Sélectionnez **Parcourir**.

1. Sélectionnez **DevTest Labs**.

1. Sélectionnez le laboratoire souhaité dans la liste des laboratoires.

1. Sélectionnez l’icône **Accès**.

	![Accès des utilisateurs](./media/devtest-lab-add-devtest-user/devtest-lab-home-blade.png)

1. Dans le panneau **Utilisateurs**, sélectionnez **Ajouter**.

	![Ajouter un utilisateur](./media/devtest-lab-add-devtest-user/devtest-users-blade.png)

1. Dans le panneau **Sélectionner un rôle**, sélectionnez **Utilisateur DevTest Labs**

1. Dans le panneau **Ajouter des utilisateurs** :

	1. Le panneau **Ajouter des utilisateurs** affiche la liste des utilisateurs intégrés. Si l'utilisateur souhaité est déjà dans la liste, vous pouvez simplement sélectionner sa ligne pour le sélectionner. Une coche s'affiche à gauche de l'utilisateur pour indiquer que l'utilisateur a été sélectionné. Pour sélectionner plusieurs utilisateurs, maintenez la touche **<Ctrl>** enfoncée tout en sélectionnant chaque utilisateur. Pour désélectionner un utilisateur, maintenez la touche **<Ctrl>** enfoncée et sélectionnez l’utilisateur. Un compteur en bas du panneau indique le nombre d'utilisateurs sélectionnés.

	1. Si l'utilisateur n'est pas dans la liste, entrez un compte de messagerie Microsoft valide dans la zone de texte **Utilisateurs**. Si l'adresse de messagerie est valide, l'utilisateur s'affiche sous la zone de texte **Utilisateur**.

	1. Une fois que vous avez sélectionné les utilisateurs que vous voulez ajouter au laboratoire, cliquez sur **Sélectionner**.

	1. Cliquez sur **OK** pour fermer le panneau **Ajouter un accès**.

1. Le panneau **Utilisateurs** affiche les rôles et les utilisateurs ajoutés.

<!---HONumber=AcomDC_0706_2016-->