<properties
	pageTitle="Impossible de me connecter pour gérer mon abonnement Azure | Microsoft Azure"
	description="Fournit des informations sur la résolution de certains problèmes de connexion à l’abonnement Azure"
	services="billing"
	documentationCenter=""
	authors="genlin"
	manager="jarrettr"
	editor="na"
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="genli"/>

# Impossible de me connecter pour gérer mon abonnement Azure

Cet article vous aidera à résoudre certains problèmes de connexion courants.

## À quel portail tentez-vous d’accéder ?

Les ADministrateurs de comptes ne peuvent accéder qu’au [Centre des comptes](https://account.windowsazure.com/), tandis que les ADministrateurs de services (SA) et les co-administrateurs (CA) bénéficient uniquement d’un accès au [portail Azure](https://portal.azure.com) ou au [portail Azure Classic](https://manage.windowsazure.com/).

Pour plus d’informations sur les rôles d’administrateur Azure, consultez [Ajout ou modification de rôles d’administrateur Azure](billing-add-change-azure-subscription-administrator.md).

## Votre abonnement est-il associé à un compte Microsoft ou à un compte professionnel ?

Votre compte Microsoft correspond à l’adresse de messagerie et au mot de passe que vous utilisez pour vous connecter à n’importe quel programme ou service Windows Live (par exemple, Outlook, Hotmail, MSN ou OneDrive). Vous pouvez configurer un compte Microsoft en utilisant l’une de vos adresses de messagerie personnelles, y compris la messagerie de votre entreprise. Pour plus d’informations, consultez [www.microsoft.com/account](http://www.microsoft.com/account).

Si votre compte est associé à un compte professionnel, sélectionnez l’option de connexion appropriée, comme indiqué ci-dessous. Pour plus d’informations sur l’utilisation des comptes professionnels, consultez la page [Inscription à Azure en tant qu’organisation](./active-directory/sign-up-organization.md).

![page de connexion](./media/billing-cannot-login-subscription/signin.png)

## Coadministrateurs : utilisez-vous le type de compte approprié pour gérer d’autres comptes ?

- Si vous vous êtes connecté avec un compte Microsoft, vous pouvez ajouter d’autres comptes Microsoft uniquement en tant que coadministrateur. Pour des raisons de sécurité, il importe d'empêcher les comptes autres que les comptes professionnels d’identifier si certains comptes (par exemple janedoe@contoso.com) sont des comptes valides.
- Si vous vous êtes connecté avec un compte professionnel, vous pouvez ajouter d’autres comptes de société de votre société en tant que coadministrateur. Par exemple, abby@contoso.com peut ajouter bob@contoso.com en tant qu’administrateur de services ou coadministrateur, mais ne peut pas ajouter john@notcontoso.com. Les utilisateurs connectés avec des comptes professionnels peuvent également ajouter des utilisateurs de compte Microsoft en tant qu’administrateur de services ou coadministrateur.

Il est désormais possible de se connecter à Azure avec un compte professionnel. Voici les modifications apportées aux exigences liées aux comptes Administrateur de services (SA) et Coadministrateur (CA) :

| Méthode de connexion| Ajouter un compte Microsoft en tant que coadministrateur ou administrateur de services ? |Ajouter un compte de société de la même société en tant que coadministrateur ou administrateur de services ? |Ajouter un compte de société d’une autre société en tant que coadministrateur ou administrateur de services ?
| ------------- | ------------- |---------------|---------------|
|Compte Microsoft |Oui|Non|Non|
|Compte de société|Oui|Oui|Non|

## Essayez de supprimer le cache/les cookies en utilisant IE en mode de navigation InPrivate et en utilisant également un autre navigateur

Si vous avez besoin d’aide supplémentaire concernant n’importe quel point de cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez la [FAQ du support Microsoft Azure](https://azure.microsoft.com/support/faq/).

<!---HONumber=AcomDC_0330_2016-->