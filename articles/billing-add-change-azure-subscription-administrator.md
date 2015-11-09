<properties
	pageTitle="Comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure | Microsoft Azure"
	description="Décrit comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="meerak"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="genli"/>

# Comment ajouter ou modifier un coadministrateur, administrateur de services et administrateur de compte Azure
## Rôles d’administrateur

Il existe trois genres de rôles d’administrateur dans Microsoft Azure :

| Rôle administratif | Limite | Description
| ------------- | ------------- |---------------|
|Administrateur de compte | 1 par compte Azure |Il s’agit de la personne qui a souscrit ou acheté des abonnements Azure et qui est autorisée à accéder au Centre des comptes et à effectuer diverses tâches de gestion. Ces tâches incluent la possibilité de créer des abonnements, d’annuler des abonnements, de modifier la facturation pour un abonnement et de modifier l’administrateur de services.
| Administrateur de services | 1 par abonnement Azure |Cette personne est autorisée à accéder au portail de gestion Azure pour les abonnements dans le compte qui lui sont affectés. Par défaut, pour un nouvel abonnement, l’administrateur de compte est également l’administrateur de services.|
|Coadministrateur|200 par abonnement (outre l’administrateur de services)|Cette personne possède les mêmes privilèges d’accès que l’administrateur de services, mais ne peut pas modifier l’association d’abonnements sur les répertoires Azure.|

## Ajouter un coadministrateur pour un abonnement
1. Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).
2. Dans le volet de navigation, sélectionnez **Paramètres**> **Administrateurs**> **Ajouter**. </br>![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Entrez l’adresse de messagerie de la personne que vous souhaitez ajouter en tant que coadministrateur, puis sélectionnez l’abonnement auquel vous souhaitez que le coadministrateur ait accès.</br> ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

L’adresse de messagerie suivante peut être ajoutée en tant que coadministrateur :

* **Compte Microsoft** (anciennement, Windows Live ID) </br> Vous pouvez utiliser un compte Microsoft pour vous connecter à tous les produits et services cloud Microsoft orientés utilisateur, notamment Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone et Xbox LIVE.
* **Compte de société**</br> Un compte de société est un compte qui est créé sous Azure Active Directory. L’adresse du compte de société est similaire à ceci : utilisateur@<your domain>.onmicrosoft.com

**Remarque** * Si vous vous êtes connecté avec un compte Microsoft, vous ne pouvez ajouter d’autres comptes Microsoft qu’en tant que coadministrateurs. Pour des raisons de sécurité, il importe d’empêcher les comptes autres que les comptes de société d’identifier si certains comptes (par exemple, janedoe@contoso.com) sont des comptes valides. * Si vous vous êtes connecté avec un compte de société, vous pouvez ajouter d’autres comptes de société de votre société en tant que coadministrateur. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’administrateur de services ou coadministrateur, mais ne peut pas ajouter john@notcontoso.com. Les utilisateurs connectés avec des comptes de société peuvent continuer à ajouter des utilisateurs de compte Microsoft en tant qu’administrateur de services ou coadministrateur. * Avec la possibilité nouvelle de se connecter à Azure avec un compte de société, voici les modifications des conditions requises pour les comptes d’administrateur de services et de coadministrateur :

| Méthode de connexion| Ajouter un compte Microsoft en tant que coadministrateur ou administrateur de services ? |Ajouter un compte de société de la même société en tant que coadministrateur ou administrateur de services ? |Ajouter un compte de société d’une autre société en tant que coadministrateur ou administrateur de services ?
| ------------- | ------------- |---------------|---------------|
|Compte Microsoft |Oui|Non|Non|
|Compte de société|Oui|Oui|Non|

## Modifier l’administrateur de services pour un abonnement
Seul l’administrateur de compte peut modifier l’administrateur de services pour un abonnement.

1. Connectez-vous au [portail de gestion des comptes](https://account.windowsazure.com/subscriptions) à l’aide de l’administrateur de compte.
2. Sélectionnez l’abonnement que vous souhaitez modifier.
3. Sur le côté droit, cliquez sur les informations **Modifier l’abonnement**. </br> ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)

4. Dans la zone **ADMINISTRATEUR DE SERVICES**, entrez l’adresse de messagerie du nouvel administrateur de services. ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## Modifier l’administrateur de compte (transfert de la propriété du compte Azure vers un autre compte)

Pour transférer la propriété du compte Azure vers un autre compte, consultez [Transfert d’un abonnement Azure](../billing-subscription-transfer.md).

<!---HONumber=Nov15_HO1-->