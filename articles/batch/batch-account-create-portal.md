<properties
	pageTitle="Création d'un compte Azure Batch | Microsoft Azure"
	description="Apprenez à créer un compte Azure Batch dans le portail Azure pour exécuter des charges de travail parallèles à grande échelle dans le cloud"
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
	ms.topic="get-started-article"
	ms.date="01/28/2016"
	ms.author="marsma"/>

# Création et gestion d'un compte Azure Batch dans le portail Azure

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Cet article vous montre comment utiliser le [portail Azure][azure_portal] pour créer et gérer un compte Azure Batch, et vous indique où trouver des paramètres comme l’URL et les clés de compte. Vous avez besoin d'une URL de compte Batch et d'une clé d'accès associée pour authentifier toutes les requêtes API Batch. Et vous associez toutes vos ressources Batch (comme les pools, les travaux et les tâches) de votre charge de travail de calcul à un compte Batch spécifique.

>[AZURE.NOTE] Actuellement, le portail Azure prend en charge des fonctionnalités de gestion de compte Batch et l’affichage des ressources de compte. L’ensemble complet des fonctionnalités Batch est disponible pour les développeurs via les API Batch.

## Création d’un compte Batch

1. Connectez-vous au [portail Azure][azure_portal].

2. Cliquez sur **Nouveau** > **Calcul** > **Service Batch**.

	![Batch dans Marketplace][marketplace_portal]

3. Passez en revue les informations, puis cliquez sur **Créer**.

4. Dans le panneau **Nouveau compte Batch**, entrez les informations suivantes :

	a. Dans **Nom de compte**, saisissez un nom unique à utiliser dans l'URL du compte Batch.

	>[AZURE.NOTE] Le nom du compte Batch doit être unique dans Azure, contenir entre 3 et 24 caractères, et utiliser des minuscules et des chiffres uniquement.

	b. Si vous avez plusieurs abonnements, cliquez sur **Abonnement** pour sélectionner un abonnement disponible où créer le compte.

	c. Cliquez sur **Groupe de ressources** pour sélectionner un groupe de ressources existant pour le compte, ou créez-en un.

	d. Dans **Emplacement**, sélectionnez une région Azure dans laquelle Batch est disponible.

	![Création d’un compte Batch][account_portal]

5. Cliquez sur **Créer** pour terminer la création du compte.

## Gestion des clés d'accès et des paramètres de compte
Une fois le compte créé, il apparaît dans le portail et vous pouvez gérer les clés d'accès, les utilisateurs autorisés et d'autres paramètres.

L’URL du compte Batch apparaît dans **Essentials**. Il s’agit d’une URL sous la forme `https://<account_name>.<region>.batch.azure.com`.

Pour afficher et gérer les clés d'accès, cliquez sur l'icône de clé.

![Clés de compte de Batch][account_keys]

## Autres choses à savoir sur le compte Batch

* D’autres façons de créer et de gérer des comptes Batch incluent les[applets de commande PowerShell Batch](batch-powershell-cmdlets-get-started.md) et la [bibliothèque .NET de gestion Batch](batch-management-dotnet.md).

* Azure ne vous facture pas si vous avez un compte Batch. Vous êtes uniquement facturé pour l’utilisation des ressources de calcul et autres services Azure lors de l’exécution de vos charges de travail (voir [Tarification Batch][batch_pricing]).

* Vous pouvez exécuter plusieurs charges de travail Batch dans un seul compte Batch ou distribuer vos charges de travail entre plusieurs comptes Batch dans différentes régions Azure.

* Si vous exécutez plusieurs charges de travail Batch à grande échelle, tenez compte des [limites et quotas du service Batch](batch-quota-limit.md) qui s’appliquent à votre abonnement Azure et à chaque compte Batch. Les quotas actuels sur le compte Batch apparaissent sur le portail dans les propriétés du compte.

## Étapes suivantes

* Consultez les [Concepts de base concernant les API dans Azure Batch](batch-api-basics.md) pour en savoir plus sur les concepts de Batch.

* Prise en main du développement de votre première application avec la [bibliothèque cliente .NET Batch](batch-dotnet-get-started.md).

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0218_2016-->