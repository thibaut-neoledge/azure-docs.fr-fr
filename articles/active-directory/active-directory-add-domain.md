<properties
	pageTitle="Ajoutez votre nom de domaine personnalisé pour simplifier la connexion à l’aide d’Azure Active Directory | Microsoft Azure"
	description="Comment ajouter les noms de domaine de votre société à Azure Active Directory et comment vérifier le nom de domaine."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/19/2016"
	ms.author="curtand;jeffsta"/>

# Ajoutez votre nom de domaine personnalisé pour simplifier la connexion à l’aide d’Azure Active Directory

Vous pouvez utiliser vos propres noms de domaine pour améliorer et simplifier l’expérience de connexion et d’autres expériences utilisateur dans Azure Active Directory (Azure AD). Par exemple, si votre organisation a pour nom de domaine « contoso.com », vos utilisateurs peuvent se connecter à l’aide de noms d’utilisateur auxquels ils sont habitués, tels que « joe@contoso.com ». Vous devez avoir accès à un compte d’administrateur général de votre annuaire Azure AD.

Lorsque vous obtenez votre annuaire client dans Azure Active Directory, vos utilisateurs se connectent avec des noms d’utilisateur tels qu’« alice@contoso.onmicrosoft.com ». Dans cet exemple, contoso.onmicrosoft.com est un nom de domaine intégré initial que vous pouvez utiliser jusqu’à ce que vous vérifiiez votre nom de domaine personnalisé. L’une des étapes suivantes consisterait ensuite à ajouter un nom de domaine personnalisé qui appartient à votre organisation tel que « contoso.com ». Cela vous permet d’attribuer des noms qui sont familiers à vos utilisateurs tels qu’« alice@contoso.com ».

Pour plus de détails sur l’utilisation des noms de domaine dans Azure AD, référez-vous à l’article sur les [concepts de gestion des domaines dans Azure AD](active-directory-add-domain-concepts.md). La plupart des administrateurs effectuent des tâches de gestion de nom de domaine dans Azure AD à l’aide du portail Azure Classic. Toutefois, vous pouvez utiliser [PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou [l’API Graph en version préliminaire](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations) pour effectuer vos tâches de gestion si vous préférez.

## Ajouter un nom de domaine personnalisé à votre annuaire

Pour ajouter un nom de domaine personnalisé à votre annuaire :

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) avec un compte d’administrateur général pour votre annuaire Azure AD.

2. Sélectionnez **Active Directory** dans le volet de navigation gauche.

3. Ouvrez votre annuaire.

4. Sélectionnez l’onglet **Domaines**.

5. Dans la barre de commandes, sélectionnez **Ajouter**.

6. Entrez le nom de votre domaine personnalisé, par exemple « contoso.com ». Veillez à inclure .com, .net ou une autre extension de niveau supérieur.

7. Si vous envisagez de configurer ce domaine pour la [connexion fédérée](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) avec votre Active Directory local, activez la case à cocher.

8. Sélectionnez **Ajouter**.

Avant de pouvoir attribuer des noms d’utilisateur qui incluent votre nom de domaine personnalisé, Azure AD doit vérifier que votre organisation possède le nom de domaine. Pour effectuer cette vérification, vous devez mettre à jour les entrées DNS du domaine dans le Bureau d’enregistrement des noms de domaine.

## Obtenir les entrées DNS qu’Azure AD utilise pour vérifier le nom de domaine

Si vous avez sélectionné l’option pour configurer le domaine pour la fédération lorsque vous avez ajouté le domaine, des instructions vous indiquant comment télécharger l’outil Azure AD Connect s’afficheront. Exécutez l’outil Azure AD Connect pour [obtenir les entrées DNS que vous devez ajouter à votre Bureau d’enregistrement de noms de domaine](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si vous n’avez pas sélectionné l’option pour la connexion fédérée avec Windows Server AD, vous verrez les entrées DNS sur la deuxième page de l’Assistant **Ajouter un domaine**.

## Ajouter les entrées DNS pour le fichier de zone DNS pour le domaine

Pour ajouter les entrées DNS dont Azure AD a besoin :

1.  Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Si vous ne disposez pas d’autorisations suffisantes pour mettre à jour le fichier de zone DNS pour le domaine, partagez les entrées DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui de les mettre à jour.

2.  Mettez à jour le fichier de zone DNS pour le domaine en ajoutant des entrées DNS fournies par Azure AD. Ces entrées DNS permettent à Azure AD de vérifier que vous êtes le propriétaire du domaine. Elles ne modifieront aucun comportement comme le routage du courrier ou l’hébergement web.

[Instructions pour ajouter des entrées DNS à des bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Vérifier le domaine avec Azure AD.

Une fois que vous avez ajouté les entrées DNS, vous pouvez vérifier le domaine avec Azure AD.

Si vous avez sélectionné l’option de fédération de votre nom de domaine personnalisé, la vérification se fera automatiquement à l’aide de l’outil Azure AD Connect. Exécutez l’outil une fois les conditions préalables remplies. Sinon, vérifiez le domaine dans le portail Azure Classic. Si l’Assistant **Ajouter un domaine** est toujours ouvert, vous pouvez cliquer sur le bouton Vérifier sur sa troisième page. L'opération de propagation des enregistrements DNS peut prendre quelques minutes.

Si l’Assistant **Ajouter un domaine** n’est plus ouvert, vous pouvez vérifier le domaine dans le [portail Azure Classic](https://manage.windowsazure.com/) :

1.  Connectez-vous à l’aide d’un compte d’administrateur général de votre annuaire Azure AD.

2.  Sélectionnez **Active Directory** dans le volet de navigation gauche.

3.  Ouvrez votre annuaire.

4.  Sélectionnez l’onglet **Domaines**.

5.  Dans la liste des domaines, sélectionnez le domaine que vous souhaitez vérifier.

6.  Sélectionnez **Vérifier** dans la barre de commande.

7.  Sélectionnez **Vérifiez** dans la boîte de dialogue.

Maintenant, vous pouvez affecter des noms d’utilisateur qui incluent votre nom de domaine personnalisé.

## Ajouter des noms de domaines personnalisés

Si votre organisation utilise plusieurs noms de domaine personnalisés tels que « contoso.com » et « contosobank.com », vous pouvez les ajouter chacun à votre annuaire Azure AD (900 domaines au maximum). Suivez les étapes ci-dessus pour ajouter chaque nom de domaine supplémentaire.

Étapes suivantes

-   [Affecter des noms d’utilisateur avec un nom de domaine personnalisé](active-directory-add-domain-add-users.md)

-   [Gérer les noms de domaine personnalisés](active-directory-add-manage-domain-names.md)

-   [Afficher la marque de votre société lorsque vos utilisateurs se connectent](active-directory-add-company-branding.md)

-   [Concepts de gestion de domaine dans Azure AD](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0420_2016-->