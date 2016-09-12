<properties
	pageTitle="Quotas et limites du service Batch | Microsoft Azure"
	description="En savoir plus sur les quotas, les limites et les contraintes liés à l’utilisation du service Azure Batch"
	services="batch"
	documentationCenter=""
	authors="mmacy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="marsma"/>

# Quotas et limites pour le service Azure Batch

Comme avec d’autres services Azure, il existe des limites concernant certaines ressources associées au service Batch. La plupart de ces limites représentent des quotas par défaut appliqués par Azure au niveau de l’abonnement ou du compte. Cet article décrit ces paramètres par défaut, et comment vous pouvez demander une augmentation de ces quotas.

Si vous envisagez d’exécuter des charges de travail de production dans Batch, vous devrez peut-être affecter à un ou plusieurs des quotas une valeur supérieure à la valeur par défaut. Si vous souhaitez augmenter un quota, vous pouvez ouvrir une [demande de service clientèle](#increase-a-quota) en ligne gratuitement.

>[AZURE.NOTE] Un quota est une limite de crédit, pas une garantie de capacité. Si vous avez des besoins de capacité à grande échelle, contactez le support Azure.

## Quotas d’abonnement
**Ressource**|**Limite par défaut**|**Limite maximale**
---|---|---
Comptes Batch par région et par abonnement | 1 | 50

## Quotas de compte Batch
[AZURE.INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## Autres limites
**Ressource**|**Limite maximale**
---|---
[Tâches simultanées](batch-parallel-node-tasks.md) par nœud de calcul | 4 x nombre de cœurs de nœud
[Applications](batch-application-packages.md) par compte Batch | 20
Packages d’applications par application | 40
Taille de package d’application (individuel) | Environ 195 Go<sup>1</sup>

<sup>1</sup> Limite Azure Storage pour la taille d’objet blob de blocs maximale

## Afficher les quotas Batch

Affichez vos quotas de compte Batch dans le [portail Azure][portal].

1. Dans le portail, cliquez sur **Comptes Batch**, puis sur le nom de votre compte Batch.

2. Dans le panneau associé au compte, cliquez sur **Tous les paramètres** > **Propriétés**.

	![Quotas de compte Batch][account_quotas]

3. Le panneau **Propriétés** affiche les quotas actuellement appliqués au compte Batch.

## Augmenter un quota

Suivez les étapes ci-dessous pour demander une augmentation du quota à l’aide du [portail Azure][portal].

1. Sélectionnez la mosaïque **Aide + Support** sur le tableau de bord du portail, ou le point d’interrogation (**?**) dans le coin supérieur droit du portail.

2. Sélectionnez **Nouvelle demande de support** > **De base**.

3. Sur le panneau **De base** :

	a. **Type de problème** > **Quota**

	b. Sélectionnez votre abonnement.

	c. **Type de quota** > **Batch**

	d. **Plan de support** > **Support quota - Inclus**

	Cliquez sur **Next**.

4. Sur le panneau **Problème** :

	a. Sélectionnez un **niveau de gravité** en fonction de [l’impact sur votre activité][support_sev].

	b. Dans **Détails**, spécifiez chaque quota que vous souhaitez modifier, le nom du compte Batch et la nouvelle limite.

	Cliquez sur **Next**.

5. Sur le panneau **Informations de contact** :

	a. Sélectionnez une **méthode de contact préférée**.

	b. Vérifiez et entrez les informations de contact requises.

	Cliquez sur **Créer** pour envoyer la demande de support.

Une fois que vous avez envoyé votre demande de support, le support Azure vous contactera. Notez que le traitement de la demande peut prendre jusqu’à 2 jours ouvrés.

## Rubriques connexes

* [Création et gestion d’un compte Azure Batch dans le portail Azure](batch-account-create-portal.md)

* [Vue d'ensemble des fonctionnalités d'Azure Batch](batch-api-basics.md)

* [Abonnement Azure et limites, quotas et contraintes de service](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

<!---HONumber=AcomDC_0831_2016-->