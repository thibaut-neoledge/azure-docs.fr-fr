<properties
	pageTitle="Obtention d’un client Azure AD | Microsoft Azure"
	description="Obtention d’un client Azure Active Directory pour l'inscription et la génération d'applications."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/02/2015"
	ms.author="dastrock"/>

# Obtention d’un client Azure Active Directory

Dans Azure Active Directory (Azure AD), un [client](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) est représentatif d'une organisation. Il s’agit d’une instance dédiée du service Azure AD qu’une organisation reçoit et possède lorsqu’elle s'inscrit à un service cloud de Microsoft tel qu’Azure, Microsoft Intune ou Office 365. Chaque client Azure AD est distinct et indépendant des autres clients Azure AD.

Un client héberge les utilisateurs d'une entreprise et leurs informations, c’est-à-dire les mots de passe, les données de profil utilisateur, les autorisations, etc. Il contient également des groupes, des applications et d’autres informations relatives à une organisation et à sa sécurité.

Pour permettre aux utilisateurs d'Azure AD de se connecter à votre application, vous devez enregistrer votre application dans un de vos clients. La publication d'une application dans un client Azure AD est **absolument gratuite**. En fait, la plupart des développeurs créent plusieurs clients et applications pour expérimenter, développer, organiser et tester. Les organisations qui s’inscrivent et utilisent votre application peuvent éventuellement choisir d'acheter des licences s'ils souhaitent bénéficier des fonctionnalités de répertoire avancées.

Mais comment faire pour obtenir un client Azure AD ? Le processus peut être un peu différent si :

- [Vous avez un abonnement Office 365 en cours](#use-an-existing-office-365-subscription)
- [Vous avez un abonnement Azure en cours associé à un compte Microsoft](#use-an-msa-azure-subscription)
- [Vous avez un abonnement Azure en cours associé à un compte professionnel](#use-an-organizational-azure-subscription)
- [Vous n’avez aucun des éléments ci-dessus et vous souhaitez commencer à partir de zéro](#start-from-scratch)

## Utilisation d’un abonnement Office 365 en cours
Si vous avez un abonnement Office 365 en cours mais que vous n'avez pas d'abonnement Azure (et ne pouvez pas vous connecter au [Portail de gestion Azure](https://manage.windowsazure.com)), veuillez suivre [ces instructions](https://technet.microsoft.com/library/dn832618.aspx) pour accéder à votre client Azure AD.

## Utilisation d’un abonnement Microsoft Azure
Si vous vous êtes déjà inscrit à un abonnement Azure avec votre compte individuel Microsoft, vous avez déjà un client ! Dans le [Portail de gestion Azure](https://manage.windowsazure.com), vous trouverez un client nommé « Client par défaut » répertorié sous « Tous les éléments » et « Active Directory ». Vous êtes libre d'utiliser ce client selon vos besoins, mais vous avez la possibilité de créer un compte d'administrateur professionnel.

Pour cela, procédez comme suit : Autrement, vous pouvez créer un nouveau client et créer un administrateur dans ce client suivant un processus similaire.

1.	Connectez-vous au [Portail de gestion Azure](https://manage.windowsazure.com) avec votre compte individuel
2.	Accédez à la section « Active Directory » du portail (situé dans la barre de navigation de gauche)
3.	Sélectionnez l'entrée « Répertoire par défaut » dans la liste des répertoires disponibles
4.	Cliquez sur le lien Utilisateurs en haut de la page. Vous verrez un seul utilisateur dans la liste avec la valeur « compte Microsoft » dans la colonne Provenance
5.	Cliquez sur « Ajouter un utilisateur » en bas de la page.
6.	Dans le formulaire Ajouter un utilisateur, fournissez les informations suivantes :
    - Type d'utilisateur : nouvel utilisateur dans votre organisation
    - Nom d'utilisateur : (choisissez un nom d'utilisateur pour cet administrateur)
    - Prénom/nom de famille/nom d'affichage : (sélectionnez les valeurs qui conviennent)
    - Rôle : administrateur général
    - Autre adresse e-mail : (saisissez les valeurs qui conviennent)
    - Facultatif : activez Multi-Factor Authentication
    - Enfin, cliquez sur le bouton vert « CRÉER » pour finaliser la création de l'utilisateur (et afficher le mot de passe temporaire).
7.	Lorsque vous avez rempli le formulaire Ajouter un utilisateur et que vous recevez le mot de passe temporaire pour le nouvel utilisateur d'administration, veillez à enregistrer ce mot de passe que vous utiliserez pour vous connecter avec ce nouvel utilisateur afin de modifier le mot de passe. Vous pouvez également envoyer le mot de passe directement à l'utilisateur, à l'aide d'un autre e-mail.
8.	Pour modifier le mot de passe temporaire, connectez-vous à https://login.microsoftonline.com avec ce nouveau compte d'utilisateur et modifiez le mot de passe lorsque cela vous sera demandé.


## Utilisation d’un abonnement Azure professionnel
Si vous vous êtes déjà inscrit à un abonnement Azure avec votre compte professionnel, vous avez déjà un client ! Dans le [Portail de gestion Azure](https://manage.windowsazure.com), vous trouverez un client répertorié sous « Tous les éléments » et « Active Directory ». Vous êtes libre d'utiliser ce client selon vos besoins. Vous pouvez également créer un client à l'aide du bouton « Nouveau » situé en bas à gauche dans le portail.


## Commencer à partir de zéro
Si tous les éléments ci-dessus vous sont incompréhensibles, ne vous inquiétez pas. Visitez simplement [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) pour vous inscrire à Azure avec une nouvelle organisation. Une fois le processus terminé, vous aurez votre propre client Azure AD avec le nom de domaine que vous avez choisi lors de votre inscription. Dans le [Portail de gestion Azure](https://manage.windowsazure.com), vous pouvez trouver votre client en accédant à « Active Directory » dans la barre de navigation de gauche.

Dans le cadre du processus d'inscription à Azure, vous devrez fournir les informations d’une carte de crédit. Vous pouvez procéder en toute confiance. Vous ne serez pas facturé pour la publication d'applications dans Azure AD ou la création de nouveaux clients.

<!---HONumber=July15_HO4-->