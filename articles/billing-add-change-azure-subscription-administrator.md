<properties
	pageTitle="Ajout ou modification de rôles d’administrateur Azure | Microsoft Azure"
	description="Décrit comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor="meerak"
	tags="billing"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="genli"/>

# Ajout ou modification de rôles d’administrateur Azure

Il existe trois genres de rôles d’administrateur dans Microsoft Azure :

| Rôle administratif | Limite | Description
| ------------- | ------------- |---------------|
|Administrateur de compte | 1 par compte Azure |Il s’agit de la personne qui a souscrit ou acheté des abonnements Azure et qui est autorisée à accéder au [Centre des comptes](https://account.windowsazure.com/Home/Index) et à effectuer diverses tâches de gestion. Ces tâches incluent la possibilité de créer des abonnements, d’annuler des abonnements, de modifier la facturation pour un abonnement et de modifier l’administrateur de services.
| Administrateur de services fédérés | 1 par abonnement Azure |Cette personne est autorisée à gérer les services sur le [portail Azure](https://manage.windowsazure.com/). Par défaut, pour un nouvel abonnement, l’administrateur de compte est également l’administrateur de services fédérés.|
|Coadministrateur|200 par abonnement|Cette personne possède les mêmes privilèges d’accès que l’administrateur de services, mais ne peut pas modifier l’association d’abonnements sur les répertoires Azure.|

> [AZURE.NOTE] Le contrôle d’accès en fonction du rôle (RBAC) Azure Active Directory permet d’ajouter les utilisateurs à plusieurs rôles. Pour plus d’informations, consultez la rubrique [Contrôle d’accès en fonction du rôle Azure Active Directory](./active-directory/role-based-access-control-configure.md).

## Comment ajouter un administrateur pour un abonnement

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).

2. Dans le volet de navigation, sélectionnez **Paramètres**> **Administrateurs**> **Ajouter**. </br>

	![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)

3. Entrez l’adresse de messagerie de la personne que vous souhaitez ajouter en tant que coadministrateur, puis sélectionnez l’abonnement auquel vous souhaitez que le coadministrateur ait accès.</br>

	![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L’adresse de messagerie suivante peut être ajoutée en tant que coadministrateur :

* **Compte Microsoft** (anciennement, Windows Live ID) </br> Vous pouvez utiliser un compte Microsoft pour vous connecter à tous les produits et services cloud Microsoft orientés utilisateur, notamment Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone et Xbox LIVE.
* **Compte professionnel**</br> Un compte professionnel est un compte créé sous Azure Active Directory. L’adresse du compte professionnel est similaire à ceci : user@&lt;your domain&gt;.onmicrosoft.com

### Limitations et restrictions

 * Chaque abonnement est associé à un annuaire Azure AD (également appelé « annuaire par défaut »). Pour identifier l’annuaire par défaut associé à l’abonnement, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/), sélectionnez **Paramètres** > **Abonnements**. Vérifiez l'ID d'abonnement pour trouver l’annuaire par défaut.

 * Si vous vous êtes connecté avec un compte Microsoft, vous ne pouvez ajouter d’autres comptes ou utilisateurs Microsoft dans l’annuaire par défaut qu’en tant que coadministrateur.

 * Si vous vous êtes connecté avec un compte professionnel, vous pouvez ajouter d’autres comptes professionnels de votre organisation en tant que coadministrateur. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’administrateur de services fédérés ou coadministrateur, mais ne peut pas ajouter john@notcontoso.com, sauf si john@noncontoso.com est l’utilisateur de l’annuaire par défaut. Les utilisateurs connectés avec des comptes professionnels peuvent continuer à ajouter des utilisateurs de compte Microsoft en tant qu’administrateur de services fédérés ou coadministrateur.

 * Il est désormais possible de se connecter à Azure avec un compte professionnel. Voici les modifications apportées aux exigences liées aux comptes Administrateur de services fédérés et Coadministrateur :

	Méthode de connexion| Ajouter un compte ou des utilisateurs Microsoft dans l’annuaire par défaut en tant que coadministrateur ou administrateur de services fédérés ? |Ajouter un compte de société dans la même organisation que le coadministrateur ou administrateur de services fédérés ? |Ajouter un compte de société dans une autre organisation que le coadministrateur ou administrateur de services fédérés ?
	------------- | ------------- |---------------|---------------
	Compte Microsoft |Oui|Non|Non
	Compte de société|Oui|Oui|Non

## Modification de l’administrateur de services fédérés pour un abonnement

Portail Azure Classic Seul l’administrateur de compte peut modifier l’administrateur de services pour un abonnement.

1. Connectez-vous au [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) à l’aide de l’administrateur de compte.

2. Sélectionnez l’abonnement que vous souhaitez modifier.

3. Sur le côté droit, cliquez sur les informations **Modifier l’abonnement**. </br>

	![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Dans la zone **ADMINISTRATEUR DE SERVICES**, entrez l’adresse de messagerie du nouvel administrateur de services fédérés. </br>

	![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Modification de l’administrateur de compte

Pour transférer la propriété du compte Azure vers un autre, consultez [Transfert d’un abonnement Azure](billing-subscription-transfer.md).

## Étapes suivantes

* Pour plus d’informations sur la façon dont l’accès aux ressources est contrôlé dans Microsoft Azure, voir [Présentation de l’accès aux ressources dans Azure](./active-directory/active-directory-understanding-resource-access.md)

* Pour plus d’informations sur la façon dont le service Azure Active Directory est lié à votre abonnement Azure, voir [Association des abonnements Azure avec Azure Active Directory] (./active-directory/active-directory-how-subscriptions-associated directory.md)

* Pour plus d’informations sur le lien entre Azure Active Directory et votre abonnement Azure, voir [Attribution de rôles d’administrateur dans Azure Active Directory](./active-directory/active-directory-assign-admin-roles.md).

<!---HONumber=AcomDC_0309_2016-->