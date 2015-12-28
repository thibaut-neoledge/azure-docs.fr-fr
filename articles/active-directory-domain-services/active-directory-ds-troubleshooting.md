<properties
	pageTitle="Version préliminaire des services Azure Active Directory Domain : guide de dépannage | Microsoft Azure"
	description="Guide de dépannage pour les services de domaine Azure Active Directory"
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
	ms.date="12/16/2015"
	ms.author="maheshu"/>

# Services de domaine Azure AD *(version préliminaire)* - Guide de dépannage
Cet article fournit des conseils de dépannage pour les problèmes que vous pouvez rencontrer pendant la configuration ou l’administration des services de domaine Azure Active Directory (AD).


### Les utilisateurs sont incapables de se connecter aux services de domaine Asure AD gérés
Si vous rencontrez une situation dans laquelle un ou plusieurs utilisateurs de votre locataire Azure AD sont incapables de se connecter au domaine géré nouvellement créé, effectuez les étapes de dépannage suivantes :

- Assurez-vous d'avoir [activé la synchronisation du mot de passe](active-directory-ds-getting-started-password-sync.md) selon les étapes décrites dans le guide de mise en route.

- Assurez-vous que le compte d'utilisateur affecté n'est pas un compte externe dans le locataire Azure AD. Les exemples de comptes externes incluent les comptes Microsoft (par exemple, 'joe@live.com') ou les comptes d'utilisateurs d'un annuaire Azure AD externe. Dans la mesure où les services de domaine Azure AD n’ont pas d'informations d'identification pour ces comptes d'utilisateurs, ces utilisateurs ne peuvent pas se connecter au domaine géré.

- Assurez-vous que le préfixe UPN du compte utilisateur affecté (par exemple, la première partie de l’UPN) dans votre client Azure AD comporte moins de 20 caractères. Par exemple, pour l’UPN « joereallylongnameuser@contoso.com », le préfixe (« joereallylongnameuser ») est composé de plus de 20 caractères et ce compte ne sera pas disponible dans le domaine géré de services de domaine Azure AD.

- **Comptes synchronisés :** si les comptes d’utilisateurs affectés sont synchronisés à partir d’un répertoire local, assurez-vous de respecter la procédure suivante :
    - Vous avez déployé ou effectué la mise à jour vers la dernière version recommandée d’Azure AD Connect.
    - Vous avez créé la clé de registre nécessaire pour activer la synchronisation des informations d’identification héritées avec Azure AD.
    - Après avoir créé la clé de registre susmentionnée sur le serveur exécutant Azure AD Connect, vous avez forcé Azure AD à effectuer une synchronisation complète, comme indiqué dans le document.
    - Selon la taille de votre annuaire, la mise à disposition des comptes d'utilisateurs et hachages d’informations d’identification dans les services de domaine Azure AD peut prendre du temps. Patientez suffisamment avant de tenter à nouveau de vous authentifier (de quelques heures, voire même un ou deux jours pour les annuaires volumineux).

- **Comptes cloud uniquement** : si le compte d’utilisateur affecté est un compte d’utilisateur dans le cloud uniquement, assurez-vous que l’utilisateur a modifié son mot de passe après avoir activé les services de domaine Azure AD. Cette étape permet de générer les hachures d’informations d'identification requises pour les services de domaine Azure AD.


### Nous contacter
Si vous rencontrez des problèmes avec votre domaine géré, vérifiez si les étapes décrites dans ce guide de dépannage apportent une solution. Si les problèmes persistent, n'hésitez pas à nous contacter sur :

- Le [canal Azure Active Directory User Voice](http://feedback.azure.com/forums/169401-azure-active-directory). Veillez à ajouter les termes **« AADDS »** avant votre question lorsque vous nous contactez.
- Vous pouvez également nous envoyer un e-mail à l’adresse dédiée aux [commentaires sur les services de domaine Azure AD](mailto:aaddsfb@microsoft.com)

<!---HONumber=AcomDC_1217_2015-->