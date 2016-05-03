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
	ms.date="04/20/2016"
	ms.author="curtand;jeffsta"/>

# Ajoutez votre nom de domaine personnalisé pour simplifier la connexion à l’aide d’Azure Active Directory

Lorsque vous obtenez votre répertoire dans Azure Active Directory (Azure AD) pour la première fois, l’une des tâches principales consiste à vérifier un nom de domaine personnalisé utilisé par votre organisation, tel que « contoso.com ». Cette tâche vous permet d’attribuer des noms qui sont familiers à vos utilisateurs tels qu’« alice@contoso.com ». Jusqu’à ce que le nom de domaine personnalisé soit vérifié, vos utilisateurs doivent utiliser des noms d’utilisateur, comme « alice@contoso.onmicrosoft.com » qui utilisent le nom de domaine initial de votre répertoire.

## Ajouter un nom de domaine personnalisé à votre annuaire

Pour ajouter un nom de domaine personnalisé à votre annuaire :

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) avec un compte d’administrateur général pour votre répertoire Azure AD.

2. Sélectionnez **Active Directory** dans le volet de navigation gauche et ouvrez votre répertoire.

4. Sélectionnez l’onglet **Domaines**.

5. Dans la barre de commandes, sélectionnez **Ajouter**.

6. Entrez le nom de votre domaine personnalisé, par exemple « contoso.com ». Veillez à inclure .com, .net ou une autre extension de niveau supérieur.

7. Si vous envisagez de configurer ce domaine pour la [connexion fédérée](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) avec votre Active Directory local, activez la case à cocher.

8. Sélectionnez **Ajouter**.

Avant de pouvoir attribuer des noms d’utilisateur qui incluent votre nom de domaine personnalisé, Azure AD doit vérifier que votre organisation possède le nom de domaine. Pour effectuer cette vérification, vous devez ajouter une entrée DNS dans le fichier de zone DNS pour le nom de domaine. Cette tâche peut être effectuée sur le site web pour le Bureau d’enregistrement de noms de domaine pour le nom de domaine.

## Obtenir les entrées DNS pour le nom de domaine

Si vous avez choisi de configurer le domaine pour la fédération, vous allez être redirigé vers le téléchargement de l’outil Azure AD Connect. Exécutez l’outil Azure AD Connect pour [obtenir les entrées DNS que vous devez ajouter à votre Bureau d’enregistrement de noms de domaine](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Si vous n’avez pas choisi de configurer le domaine pour la fédération, les entrées DNS se trouvent sur la deuxième page de l’Assistant **Ajouter un domaine**.

## Ajouter l’entrée DNS au fichier de zone DNS

Pour ajouter l’entrée DNS dont Azure AD a besoin :

1.  Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Si vous n’avez pas les autorisations suffisantes pour mettre à jour l’entrée DNS, demandez à la personne ou l’équipe qui dispose de cet accès d’ajouter l’entrée DNS.

2.  Mettez à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS permet à Azure AD de vérifier que vous êtes le propriétaire du domaine. Elle ne modifiera aucun comportement comme le routage du courrier ou l’hébergement web.

[Instructions pour ajouter une entrée DNS à des Bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Vérifier le nom de domaine avec Azure AD.

Une fois que vous avez ajouté l’entrée DNS, vous pouvez vérifier le nom de domaine avec Azure AD.

Si vous avez choisi de fédérer votre nom de domaine personnalisé, l’outil Azure AD Connect effectue automatiquement la vérification. Exécutez l’outil une fois les conditions préalables remplies. Sinon, vérifiez le domaine dans le portail Azure Classic. Si l’Assistant **Ajouter un domaine** est toujours ouvert, vous pouvez cliquer sur le bouton **Vérifier** sur sa troisième page. La propagation des enregistrements DNS peut prendre jusqu’à une heure.

Si l’Assistant **Ajouter un domaine** n’est plus ouvert, vous pouvez vérifier le domaine dans le [portail Azure Classic](https://manage.windowsazure.com/) :

1.  Connectez-vous à l’aide d’un compte d’administrateur général de votre annuaire Azure AD.

2.  Sélectionnez **Active Directory** dans le volet de navigation gauche.

3.  Ouvrez votre annuaire.

4.  Sélectionnez l’onglet **Domaines**.

5.  Dans la liste des domaines, sélectionnez le domaine que vous souhaitez vérifier.

6.  Sélectionnez **Vérifier** dans la barre de commande.

7.  Sélectionnez **Vérifier** dans la boîte de dialogue.

Maintenant, vous pouvez [affecter des noms d’utilisateur qui incluent votre nom de domaine personnalisé](active-directory-add-domain-add-users.md).

## Ajouter des noms de domaines personnalisés

Si votre organisation utilise plusieurs noms de domaine personnalisés tels que « contoso.com» et « contosobank.com », vous pouvez les ajouter à votre répertoire Azure AD. La valeur maximale est de 900 noms de domaine. Suivez les mêmes étapes pour ajouter chaque nom de domaine supplémentaire.

## Résolution de problèmes
Si vous ne pouvez pas vérifier un nom de domaine personnalisé, plusieurs causes sont possibles. Nous irons de la plus courante à la moins courante.

- Vous avez essayé de vérifier le nom de domaine avant que l’entrée DNS n’ait pu se propager. Attendez un moment et réessayez.

- L’enregistrement DNS n’a pas été entré du tout. Vérifiez l’entrée DNS et attendez qu’elle se propage, puis réessayez.

- Le nom de domaine a déjà été vérifié dans un autre répertoire. Recherchez le nom de domaine et supprimez-le de l’autre répertoire, puis réessayez.

- L’enregistrement DNS contient une erreur. Corrigez l’erreur, puis réessayez.

- Vous n’avez pas les autorisations suffisantes pour mettre à jour les enregistrements DNS. Partagez les entrées DNS avec la personne ou l’équipe de votre organisation qui possède cet accès et demandez-lui d’ajouter l’entrée DNS.


## Étapes suivantes

-   [Affecter des noms d’utilisateur qui incluent votre nom de domaine personnalisé](active-directory-add-domain-add-users.md)

-   [Gérer les noms de domaine personnalisés](active-directory-add-manage-domain-names.md)

-   [Afficher la marque de votre société lorsque vos utilisateurs se connectent](active-directory-add-company-branding.md)

-   [Utiliser PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [En savoir plus sur les concepts de gestion de domaine dans Azure AD](active-directory-add-domain-concepts.md)

<!---HONumber=AcomDC_0427_2016-->