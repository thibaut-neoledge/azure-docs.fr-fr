<properties
	pageTitle="Ajout de votre nom de domaine personnalisé à Azure Active Directory | Microsoft Azure"
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

# Ajout de votre nom de domaine personnalisé à Azure Active Directory

Votre organisation utilise un ou plusieurs noms de domaine pour mener ses activités, et vos utilisateurs se connectent au réseau d’entreprise en utilisant votre nom de domaine d’entreprise. Maintenant que vous utilisez le logiciel Azure Active Directory (Azure AD), vous pouvez également ajouter votre nom de domaine d’entreprise à ce dernier. Cette tâche vous permet d’attribuer des noms de répertoire qui sont familiers à vos utilisateurs, comme « alice@contoso.com ». Le processus est simple :

- Ajoutez votre nom de domaine dans notre Assistant **Ajouter un domaine**, sur le portail Azure Classic.

- Obtenez l’entrée DNS via le portail Azure AD Classic ou l’outil Azure AD Connect.

- Ajoutez cette entrée pour le nom de domaine dans le fichier de zone DNS, sur le site web associé au bureau d’enregistrement de noms de domaine DNS.

- Vérifiez le nom de domaine via le portail Azure AD Classic ou l’outil Azure AD Connect.


Jusqu’à ce que votre nom de domaine personnalisé soit vérifié, vos utilisateurs doivent se connecter en recourant à des noms d’utilisateur comme « alice@contoso.onmicrosoft.com », qui utilisent le nom de domaine initial de votre répertoire. Si votre organisation utilise plusieurs noms de domaine personnalisés, tels que « contoso.com» et « contosobank.com », vous pouvez les ajouter (le nombre maximum autorisé est de 900). Suivez la procédure décrite dans cet article pour ajouter chaque nom de domaine.

## Ajouter un nom de domaine personnalisé à votre annuaire

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) en utilisant le compte d’un administrateur général d’Azure AD.

2. Dans **Active Directory**, ouvrez le répertoire et sélectionnez l’onglet **Domaines**.

3. Dans la barre de commandes, sélectionnez **Ajouter**, puis indiquez le nom de votre domaine personnalisé, par exemple « contoso.com ». Veillez à inclure .com, .net ou une autre extension de niveau supérieur.

4. Si vous envisagez de configurer ce domaine pour la [connexion fédérée](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) avec votre instance Active Directory locale, activez la case à cocher.

5. Sélectionnez **Ajouter**.

Maintenant que vous avez ajouté le nom de domaine, Azure AD doit vérifier que votre organisation possède le nom de domaine. Pour qu’Azure AD puisse effectuer cette vérification, vous devez ajouter une entrée DNS dans le fichier de zone DNS pour le nom de domaine. Cette tâche peut être effectuée sur le site web pour le bureau d’enregistrement de noms de domaine associé au nom de domaine.

## Obtenir les entrées DNS pour le nom de domaine

Si vous n’effectuez pas la fédération avec une instance Windows Server Active Directory locale, les entrées DNS figurent sur la deuxième page de l’Assistant **Ajouter un domaine**.

Si vous avez configuré le domaine pour la fédération, vous allez être redirigé vers le téléchargement de l’outil Azure AD Connect. Exécutez l’outil Azure AD Connect pour [obtenir les entrées DNS que vous devez ajouter à votre bureau d’enregistrement de noms de domaine](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Azure AD Connect vérifie également le nom de domaine pour Azure AD.

## Ajouter l’entrée DNS au fichier de zone DNS

1.  Connectez-vous au Bureau d’enregistrement des noms de domaine pour le domaine. Si vous n’avez pas les autorisations suffisantes pour mettre à jour l’entrée DNS, demandez à la personne ou l’équipe qui dispose de cet accès d’ajouter l’entrée DNS.

2.  Mettez à jour le fichier de zone DNS pour le domaine en ajoutant l’entrée DNS fournie par Azure AD. Cette entrée DNS permet à Azure AD de vérifier que vous êtes le propriétaire du domaine. Elle ne modifiera aucun comportement comme le routage du courrier ou l’hébergement web. La propagation des enregistrements DNS peut prendre jusqu’à une heure.

[Instructions pour ajouter une entrée DNS à des Bureaux d’enregistrement DNS courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Vérifier le nom de domaine avec Azure AD.

Une fois que vous avez ajouté l’entrée DNS, vous devez vous assurer que le nom de domaine est vérifié par Azure AD. Il s’agit de l’étape finale avant la réussite.

Si l’Assistant **Ajouter un domaine** est toujours ouvert, cliquez sur le bouton **Vérifier** sur sa troisième page. Avant de procéder à la vérification, vous devez attendre la propagation de l’entrée DNS pendant un maximum d’une heure.

Si l’Assistant **Ajouter un domaine** n’est plus ouvert, vous pouvez vérifier le domaine sur le [portail Azure Classic](https://manage.windowsazure.com/) :

1.  Connectez-vous à l’aide d’un compte d’administrateur général de votre annuaire Azure AD.

2.  Ouvrez votre répertoire, puis sélectionnez l’onglet **Domaines**.

3.  Choisissez le domaine que vous souhaitez vérifier.

4.  Sélectionnez **Vérifier** sur la barre de commandes et choisissez **Vérifier** dans la boîte de dialogue.

Bravo, vous avez réussi ! Maintenant, vous pouvez [affecter des noms d’utilisateur qui incluent votre nom de domaine personnalisé](active-directory-add-domain-add-users.md). Si vous avez des difficultés à vérifier le nom de domaine, consultez notre section [Résolution de problèmes](#troubleshooting).

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
-   [En savoir plus sur les concepts de gestion de domaine dans Azure AD](active-directory-add-domain-concepts.md)
-   [Afficher la marque de votre société lorsque vos utilisateurs se connectent](active-directory-add-company-branding.md)
-   [Utiliser PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

<!---HONumber=AcomDC_0504_2016-->