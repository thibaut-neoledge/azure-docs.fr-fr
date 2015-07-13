<properties 
	pageTitle="Configurer des alertes de facturation pour vos abonnements Microsoft Azure" 
	description="Describes how you can set up alerts on your Azure bill so you can avoid billing surprises." 
	services="" 
	documentationCenter="" 
	authors="vikdesai" 
	manager="msmbaldwin" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="vikdesai"/>

# Configurer des alertes de facturation pour vos abonnements Microsoft Azure

Voulez-vous connaître le montant de vos dépenses mensuelles pour votre abonnement Azure ? Si vous êtes l'administrateur de compte d'un abonnement Azure, vous pouvez utiliser le service d'alerte de facturation Azure pour créer des alertes de facturation personnalisées qui vous aident à surveiller et à gérer l'activité de facturation de vos comptes Azure.

Ce service étant en version préliminaire, la première chose que vous avez à faire est de vous y inscrire. Pour ce faire, visitez la page <a href="https://account.windowsazure.com/PreviewFeatures">des fonctionnalités en version préliminaire</a> dans le portail de gestion des comptes Azure.

## Définir le seuil d'alerte et les destinataires des messages électroniques

Après avoir reçu par courrier électronique la confirmation que le service de facturation est activé pour votre abonnement, <a href="https://account.windowsazure.com/Subscriptions">visitez la page Abonnements</a> du portail des comptes. Cliquez sur l’abonnement à surveiller, puis sur **Alertes**.

![][Image1]

Cliquez ensuite sur **Ajouter une alerte** pour créer votre première alerte. Vous pouvez configurer un total de cinq alertes de facturation par abonnement, avec un seuil distinct, et jusqu’à deux destinataires de courrier électroniques pour chaque alerte.

![][Image2]

Quand vous ajoutez une alerte, vous lui donnez un nom unique, vous choisissez un seuil de dépenses, et vous choisissez également les adresses électroniques auxquelles les alertes sont envoyées. Quand vous configurez le seuil, vous pouvez choisir un **Total facturé** ou un **Crédit monétaire** dans la liste **Alerte pour**. Pour un total facturé, une alerte est envoyée quand les dépenses d'abonnement dépassent le seuil. Pour un crédit monétaire, une alerte est envoyée quand les crédits monétaires passent sous la limite. Les crédits monétaires s'appliquent habituellement aux essais et abonnements gratuits associés à des comptes MSDN.

![][Image3]

Azure prend en charge toutes les adresses de messagerie mais ne vérifie pas si elles sont valides. Vous devez donc vous assurer qu'il n'y a aucune faute de frappe.

## Vérifier vos alertes

Une fois que vous avez configuré des alertes, le Centre des comptes les répertorie et vous montre le nombre d'alertes que vous pouvez encore configurer. Pour chaque alerte, vous voyez la date et l'heure d'envoi, s'il s'agit d'une alerte relative au total facturé ou au crédit monétaire, ainsi que la limite que vous avez configurée. L'heure est au format 24 heures UTC (Universal Time Coordinate) et la date est au format aaaa-mm-jj. Cliquez sur le signe plus pour modifier une alerte listée, ou cliquez sur la poubelle pour la supprimer.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png

<!---HONumber=July15_HO1-->