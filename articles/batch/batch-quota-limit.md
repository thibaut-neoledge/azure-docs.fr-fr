<properties
	pageTitle="Quotas et limites du service Batch | Microsoft Azure"
	description="En savoir plus sur les quotas, les limites et les contraintes liés à l’utilisation du service Azure Batch"
	services="batch"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="danlep"/>

# Quotas et limites pour le service Azure Batch

Cet article répertorie les limites par défaut et maximales de certaines ressources que vous pouvez utiliser avec le service Azure Batch. La plupart de ces limites sont des quotas qu’Azure applique à votre abonnement ou à vos comptes Batch.

Si vous envisagez d’exécuter des charges de travail Batch dans un environnement de production, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut. Si vous souhaitez augmenter un quota, ouvrez une demande de service clientèle en ligne gratuitement.

>[AZURE.NOTE] Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.

## Quotas d’abonnement
Ressource|Limite par défaut|Limite maximale
---|---|---
Comptes Batch par région et par abonnement|1|50

## Quotas de compte Batch
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Autres limites
Ressource|Limite maximale
---|---
Tâches par nœud de calcul | 4 x nombre de cœurs de nœud
Applications par compte Batch | 20
Packages d’applications par application | 40
Taille de package d’application (individuel) | Environ 195 Go<sup>1</sup>

<sup>1</sup> Limite Azure Storage pour la taille d’objet blob de blocs maximale

## Afficher les quotas Batch

Affichez vos quotas de compte Batch dans le [portail Azure](https://portal.azure.com).

1. Dans le portail, cliquez sur **Comptes Batch**, puis sur le nom de votre compte Batch.

2. Dans le panneau associé au compte, cliquez sur **Paramètres** > **Propriétés**.

	![Quotas de compte Batch][account_quotas]

3. Dans le panneau **Propriétés**, passez en revue les quotas qui s’appliquent actuellement au compte Batch.

## Augmenter un quota

Procédez comme suit pour demander une augmentation de quota dans le portail Azure (vous pouvez également demander une augmentation dans le [portail Azure Classic](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)).

1. Dans le tableau de bord du portail, cliquez sur **Aide + Support**.

2. Cliquez sur **Nouvelle demande de support > De base**.

3. Dans le panneau **De base**, procédez comme suit :

	a. Dans **Type de problème**, sélectionnez **Quota**.

	b. Sélectionnez votre abonnement.

	c. Dans **Type de quota**, sélectionnez **Batch**.

	d. Dans **Formule d’assistance**, sélectionnez **Formule d’assistance Azure - Développeur**.

	Cliquez sur **Next**.

4. Dans le panneau **Problème**, procédez comme suit :

	a. Sélectionnez un **niveau de gravité** en fonction de l’impact sur votre activité.

	b. Dans **Détails**, répertoriez le quota ou les quotas à modifier dans un compte spécifique et les nouvelles limites que vous souhaitez.

	Cliquez sur **Next**.

5. Dans le panneau **Informations de contact**, entrez vos coordonnées et cliquez sur **Suivant**.

6. Cliquez sur **Créer** pour envoyer la demande de support.

Le support Azure vous contactera. Le traitement de la demande peut prendre jusqu’à 2 jours ouvrés.

## Rubriques connexes

* [Créer et gérer un compte Azure Batch](batch-account-create-portal.md)

* [Vue d'ensemble des fonctionnalités d'Azure Batch](batch-api-basics.md)

* [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0316_2016-->