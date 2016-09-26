<properties
	pageTitle="Changer d’offre pour votre abonnement Azure | Microsoft Azure"
	description="Découvrez comment changer votre abonnement Azure et basculer vers une autre offre à l’aide du portail de gestion des abonnements"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="genli"/>

# Changer d’offre pour votre abonnement Azure

## Bon à savoir

Vous [pouvez](#where-is-the-button) changer d’abonnement et choisir une offre plus avantageuse, sans aucune interruption de service. Vous trouverez ci-après les changements actuellement pris en charge dans le [Centre des comptes](https://account.windowsazure.com/Subscriptions). Cliquez sur les liens pour en savoir plus sur la configuration requise.

| À partir d'une base | À |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Prépaiement de 12 mois](https://azure.microsoft.com/offers/ms-azr-0026p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |
> [AZURE.NOTE] Si vous utilisez une version d’évaluation gratuite, découvrez comment [passer à une offre de paiement à l’utilisation](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go).

> Si vous avez souscrit votre abonnement récemment, vous devez attendre l’expiration de votre première période de facturation avant de pouvoir changer d’offre.

> Pour les autres changements d’offre, [contactez le support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
	
## Guide pour changer d’offre pour votre abonnement

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.	Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/Subscriptions).
2.	Sélectionnez l’abonnement que vous souhaitez changer.
3.	Cliquez sur **Changer d’offre**. Découvrez pourquoi [vous ne voyez pas le bouton](#where-is-the-button).

	![Bouton de changement d’offre](./media/billing-how-to-switch-azure-offer/switchbutton.png)
	
4.	Toutes les offres auxquelles votre abonnement vous donne droit s’affichent. Cette liste varie selon les adhésions auxquelles votre compte est associé et selon l’offre actuelle. Vous pouvez cliquer sur le lien **En savoir plus** sous chaque offre pour obtenir des informations détaillées la concernant. Cliquez sur l’offre souhaitée pour passer à l’étape suivante.

	![Sélectionner une offre](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5.	Selon l’offre que vous envisagez d’adopter, une note peut apparaître sur l’impact du changement. Parcourez cette liste soigneusement et suivez les instructions avant de continuer.

	![Points à noter](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6.	Vous pouvez renommer votre abonnement. Par défaut, nous le définissons sur le nom de la nouvelle offre. Cliquez sur **Changer d’offre** pour terminer le processus.

	![Page de confirmation](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7.	C’est terminé ! Votre abonnement est désormais associé à la nouvelle offre.

## Forum Aux Questions (FAQ)

### Quels changements d’offre prenez-vous en charge ?

Consultez la liste des [offres pour lesquelles vous pouvez changer](#what-you-need-to-know).

### Où est le bouton ?

Il est possible que le bouton **Changer pour une autre offre** ne s’affiche pas si :

- Vous êtes toujours dans votre première période de facturation ; vous devez attendre l’expiration de votre première période de facturation avant de pouvoir changer d’offre.

- Vous n’êtes pas éligible pour changer d’offre. Vérifiez la liste des [offres pour lesquelles vous pouvez changer](#what-you-need-to-know) pour vous assurer que le changement d’offre souhaité est pris en charge.

### Un changement d’offre a-t-il un impact sur mon service ?

Il n’y a aucune interruption de service. Toutefois, l’offre pour laquelle vous changez peut avoir des restrictions. Par exemple, certaines offres interdisent une utilisation en production. Vous devrez donc déplacer les ressources de production dans un autre abonnement.

### Un changement d’offre a-t-il un impact sur les administrateurs de service ? 

Il n’y a aucun impact sur les utilisateurs associés à l’abonnement.

### Quel impact un changement d’offre peut-il avoir sur la facturation ?

Le jour où vous changez d’offre, une facture est générée pour tous les frais dus. Par la suite, votre abonnement est facturé en fonction des conditions de tarification de la nouvelle offre. La date de facturation de votre abonnement devient la date à laquelle vous avez changé d’offre. Les données d’utilisation et de facturation antérieures au changement d’offre n’étant pas conservées, nous vous recommandons de les télécharger avant de procéder au changement.

> [AZURE.NOTE] En raison de contraintes liées à la facturation, vous ne pouvez pas changer d’offre pendant le premier cycle de facturation qui suit la création d’un abonnement.

### Puis-je utiliser ce mécanisme pour migrer d’une offre web directe vers une offre [Fournisseur de solutions cloud](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) ou [contrat Entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/) ?

Ce mécanisme ne peut être utilisé que pour passer de et à une offre directe web. Pour inclure votre abonnement actuel dans un contrat Entreprise, demandez à votre administrateur d’inscription d’ajouter votre compte au contrat Entreprise. Vous recevez ensuite un e-mail d’invitation. Quand vous suivez les instructions pour accepter l’invitation, votre abonnement est automatiquement rattaché au contrat Entreprise. À ce jour, aucune option ne permet d’inclure un abonnement direct web existant dans une offre CSP.

## Étapes suivantes

- Découvrez comment [gérer les rôles d’administrateur](billing-add-change-azure-subscription-administrator.md) pour votre abonnement

- Effectuez le suivi de votre utilisation en [téléchargeant les données d’utilisation et la facture](billing-download-azure-invoice-daily-usage-date.md)

> [AZURE.NOTE] Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

<!---HONumber=AcomDC_0914_2016-->