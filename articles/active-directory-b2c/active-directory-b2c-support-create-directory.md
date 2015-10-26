<properties
	pageTitle="Azure Active Directory : rubrique de prise en charge Création d'un client | Microsoft Azure"
	description="Création d'un client Azure Active Directory ou d'un client Azure Active Directory B2C - Problèmes et résolutions"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/24/2015"
	ms.author="swkrish"/>

# Création d'un client Azure AD ou d'un client Azure AD B2C - Problèmes et résolutions

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Création d'un client Azure AD

Si vous ne pouvez pas créer un client Azure AD la première fois, veuillez essayer à nouveau. Si le problème persiste, contactez le support technique.

## Création d'un client Azure AD B2C (version préliminaire)

Il existe des problèmes connus que vous pourriez rencontrer lors de la [création d'un client Azure AD B2C](active-directory-b2c-get-started.md).

- Si le client Azure AD B2C n'apparaît pas dans votre liste de clients, essayez à nouveau.
- Si le client Azure AD B2C ne s'affiche pas dans la liste des clients, et si vous voyez s'afficher un message d'erreur similaire à « Impossible de terminer la création du client B2C "contosob2c". Veuillez visitez ce [lien](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) pour obtenir des instructions supplémentaires », effectuez l’une des opérations suivantes :
    - Accédez au panneau de fonctionnalités B2C sur le portail Azure en version préliminaire via le lien [https://portal.azure.com/{tenant}.onmicrosoft.com/?Microsoft\_AAD\_B2CAdmin=true#blade/Microsoft\_AAD\_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com) où **{tenant}** est remplacé par le nom utilisé au moment de la création du client (par exemple, contosob2c), puis connectez-vous en tant qu'administrateur général. Cliquez sur **Activer les fonctionnalités B2C** en haut du volet, puis cliquez sur **OK**. Votre client Azure AD B2C devrait être prêt à l'emploi.
	- Supprimez le client que vous venez de créer, puis réessayez.
- Si aucune des solutions ci-dessus ne fonctionne pour vous, contactez le support technique. Lisez [cet article](active-directory-b2c-support.md) sur l’archivage des demandes de support pour Azure AD B2C.

<!---HONumber=Oct15_HO3-->