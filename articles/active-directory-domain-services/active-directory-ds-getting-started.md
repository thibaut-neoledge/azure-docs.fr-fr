<properties
	pageTitle="Version préliminaire des services de domaine Azure Active Directory : prise en main | Microsoft Azure"
	description="Prise en main des services de domaine Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Services de domaine Azure AD *(version préliminaire)* : prise en main

Cet article décrit les étapes de configuration nécessaires pour activer les services de domaine Azure AD pour votre client Azure AD.

## Étape 1 : créer le groupe « AAD DC Administrators »
La première étape consiste à créer un groupe d’administration dans votre client Azure Active Directory. Ce groupe d’administration spécial est appelé **AAD DC Administrators**. Les membres de ce groupe se voient accorder des privilèges d’administrateur sur les ordinateurs joints au domaine des services de domaine Azure AD que vous configurez. Une fois effectuée la jonction au domaine, ce groupe est ajouté au groupe « Administrateurs » sur ces ordinateurs joints au domaine. En outre, les membres de ce groupe sont également autorisés à utiliser le Bureau à distance pour se connecter à distance aux ordinateurs joints au domaine.

> [AZURE.NOTE] Vous ne pouvez pas exercer de privilèges d’administrateur de domaine ou d’administrateur d’entreprise au sein du domaine créé à l’aide des services de domaine Azure AD. Dans la mesure où il s’agit d’un domaine géré, ces privilèges sont réservés par le service et ne sont pas accessibles aux utilisateurs au sein du client. Toutefois, vous pouvez utiliser ce groupe d’administrateurs spécial créé au cours de cette étape de configuration pour effectuer certaines opérations privilégiées, telles que joindre des ordinateurs au domaine, devenir membre du groupe Administrateurs sur les ordinateurs joints au domaine ou configurer une stratégie de groupe.

Au cours de cette étape de configuration, vous créez le groupe et y ajoutez un ou plusieurs utilisateurs de votre client. Pour créer le groupe d’administration pour les services de domaine Azure AD, procédez comme suit :

1. Accédez au **portail de gestion Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Sélectionnez le nœud **Active Directory** dans le volet gauche.
3. Sélectionnez le client Azure AD (annuaire) pour lequel vous souhaitez activer les services de domaine Azure AD. Notez que vous ne pouvez créer qu’un seul domaine par annuaire Azure AD.

    ![Sélectionner un annuaire Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Cliquez sur l’onglet **Groupes**.
5. Cliquez sur **Ajouter un groupe** à partir du volet de tâches en bas de la page pour ajouter un groupe à votre annuaire.
6. Créez un groupe nommé **AAD DC Administrators**.

    > [AZURE.WARNING] Vous devez créer un groupe portant exactement ce nom pour activer l’accès au sein des services de domaine Azure AD.

	![Créer un groupe d’administrateurs](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Vous pouvez ajouter une description pour ce groupe, afin que les autres utilisateurs dans votre client Azure AD comprennent que ce groupe permet d’accorder des privilèges d’administrateur au sein des services de domaine Azure AD.
8. Après avoir créé le groupe, cliquez sur le nom du groupe pour afficher ses propriétés. Cliquez sur le bouton **Ajouter des membres** dans le panneau inférieur, pour ajouter des utilisateurs en tant que membres de ce groupe.
9. Dans la boîte de dialogue **Ajouter des membres**, sélectionnez les utilisateurs qui doivent être membres de ce groupe et cochez la case quand vous avez terminé.

    ![Ajouter des utilisateurs au groupe d’administrateurs](./media/active-directory-domain-services-getting-started/add-group-members.png)

---
[**Étape suivante : créer ou sélectionner un réseau virtuel Azure.**](active-directory-ds-getting-started-vnet.md)

<!---HONumber=AcomDC_0128_2016-->