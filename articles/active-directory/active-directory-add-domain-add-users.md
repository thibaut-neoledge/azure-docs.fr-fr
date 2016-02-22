<properties
	pageTitle="Affecter des utilisateurs à un domaine personnalisé dans Azure Active Directory | Microsoft Azure"
	description="Comment remplir un domaine personnalisé dans Azure Active Directory avec des comptes d'utilisateur."
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
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="curtand;jeffsta"/>

# Affecter des utilisateurs à un domaine personnalisé

Après avoir ajouté votre domaine personnalisé à Azure Active Directory, vous devez ajouter les comptes d'utilisateur pour ce domaine afin de pouvoir les authentifier.

## Utilisateurs synchronisés à partir d’un répertoire de votre réseau d'entreprise

Si vous avez déjà configuré une connexion entre un domaine Active Directory local et Azure Active Directory, la synchronisation peut remplir les comptes. Pour plus d'informations sur la synchronisation d’Azure Active Directory avec votre domaine Active Directory local, consultez [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## Utilisateurs ajoutés et gérés dans le cloud

Pour modifier le domaine d'un compte d'utilisateur existant :

1.  Ouvrez le portail Azure Classic à l'aide d'un compte administrateur global ou administrateur d'utilisateurs.

2.  Ouvrez votre annuaire.

3.  Cliquez sur l’onglet **Utilisateurs**.

4.  Sélectionnez l'utilisateur dans la liste.

5.  Modifiez le domaine de l'utilisateur, puis sélectionnez **Enregistrer**.

Vous pouvez également utiliser [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou l’[API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## Sélectionner un domaine personnalisé lors de la création d'un nouvel utilisateur

1.  Ouvrez le portail Azure Classic à l'aide d'un compte administrateur global ou administrateur d'utilisateurs.

2.  Ouvrez votre annuaire.

3.  Cliquez sur l’onglet **Utilisateurs**.

4.  Dans la barre de commandes, sélectionnez **Ajouter**.

5.  Lorsque vous ajoutez le nom d'utilisateur, choisissez le domaine personnalisé dans la liste des domaines.

6.  Sélectionnez **Enregistrer**.

## Étapes suivantes

- [Utilisation de noms de domaine personnalisés pour simplifier l’expérience de connexion pour vos utilisateurs](active-directory-add-domain.md)
- [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)
- [Ajouter et vérifier un nom de domaine personnalisé dans Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Modifier le bureau d’enregistrement DNS pour votre nom de domaine personnalisé](active-directory-add-domain-change-registrar.md)
- [Supprimer un domaine personnalisé dans Azure Active Directory](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->