<properties
	pageTitle="Création d'un compte Azure Batch | Microsoft Azure"
	description="Apprenez à créer un compte Azure Batch dans le portail Azure pour exécuter des charges de travail parallèles à grande échelle dans le cloud"
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
	ms.topic="get-started-article"
	ms.date="09/21/2016"
	ms.author="marsma"/>

# Création d’un compte Azure Batch à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](batch-account-create-portal.md)
- [Gestion de lots .NET](batch-management-dotnet.md)

Découvrez comment créer un compte Azure Batch dans le [portail Azure][azure_portal] et où trouver les propriétés importantes du compte, comme les clés d’accès et les URL de compte. Nous aborderons également la tarification Azure et la liaison d’un compte Azure Storage à votre compte Batch, afin que vous puissiez utiliser les [packages d’applications](batch-application-packages.md) et [conserver une sortie de tâche et de travail](batch-task-output.md).

## Création d’un compte Batch

1. Connectez-vous au [portail Azure][azure_portal].

2. Cliquez sur **Nouveau** > **Calcul** > **Service Batch**.

	![Batch dans Marketplace][marketplace_portal]

3. Le panneau **Nouveau compte Batch** s’affiche. Consultez les points *a* à *e* ci-après pour obtenir une description de chaque élément du panneau.

    ![Création d’un compte Batch][account_portal]

	a. **Nom de compte** : nom unique de votre compte Batch. Ce nom doit être unique dans la région Azure où le compte est créé (voir *Emplacement* ci-dessous). Il peut contenir uniquement des caractères minuscules, des chiffres et doit comporter 3 à 24 caractères.

	b. **Abonnement** : abonnement dans lequel créer le compte Batch. Si vous n’avez qu’un seul abonnement, il est sélectionné par défaut.

	c. **Groupe de ressources** : groupe de ressources de votre nouveau compte Batch (vous pouvez également en créer un).

	d. **Emplacement** : région Azure dans laquelle créer le compte Batch. Seules les régions prises en charge par votre abonnement et votre groupe de ressources sont affichées.

    e. **Compte de stockage** (facultatif) : compte de stockage **à usage général** à associer à votre nouveau compte Batch. Pour plus d’informations, consultez la section [Compte Azure Storage lié](#linked-azure-storage-account) ci-dessous.

4. Cliquez sur **Créer** pour créer le compte.

  Le portail indique qu’il procède au **déploiement** du compte. À la fin du processus, le message **Déploiements réussis** s’affiche dans les *Notifications*.

## Afficher les propriétés du compte Batch

Une fois le compte créé, vous pouvez ouvrir le **Panneau du compte Batch** pour accéder à ses propriétés et paramètres. Vous avez accès à tous les paramètres et propriétés du compte dans le menu de gauche du panneau du compte Batch.

![Panneau de compte Batch dans le portail Azure][account_blade]

* **Batch account URL (URL du compte Batch)** : les applications créées avec les [API de développement Batch](batch-technical-overview.md#batch-development-apis) ont besoin d’une URL de compte pour gérer les ressources et exécuter des tâches dans le compte. Une URL de compte Batch a le format suivant :

    `https://<account_name>.<region>.batch.azure.com`

![URL de compte Batch dans le portail][account_url]

* **Clés d’accès** : vos applications doivent également avoir une clé d’accès pour utiliser des ressources dans votre compte Batch. Pour afficher ou régénérer les clés d’accès de votre compte Batch, entrez `keys` dans la zone **Recherche** du menu de gauche dans le volet du compte Batch, puis sélectionnez **Clés**.

    ![Clés de compte Batch dans le portail Azure][account_keys]

## Tarification

Les comptes Batch ne sont proposés qu’au « niveau gratuit », ce qui signifie que ce compte ne vous est pas facturé. En revanche, vous êtes facturé pour les ressources de calcul Azure que vos solutions Batch consomment, ainsi que pour les ressources utilisées par d’autres services pendant l’exécution de vos charges de travail. Par exemple, vous êtes facturé pour les nœuds de calcul dans vos pools et pour les données que vous stockez dans Azure Storage comme entrées ou sorties de vos tâches. De même, si vous utilisez la fonctionnalité des [packages d’application](batch-application-packages.md), vous êtes facturé pour les ressources Azure Storage utilisées pour stocker les versions de vos packages d’application. Pour plus d’informations, consultez [Tarification Batch][batch_pricing].

## Compte Azure Storage lié

Comme nous l’avons déjà vu, vous pouvez (si vous le souhaitez) lier un compte de stockage **à usage général** à votre compte Batch. La fonctionnalité des [packages d’application](batch-application-packages.md) de Batch utilise le stockage d’objets blob dans un compte de stockage général lié, comme la bibliothèque [Batch File Conventions .NET](batch-task-output.md). Ces fonctionnalités facultatives vous aideront à déployer les applications exécutées par vos tâches Batch et à conserver les données qu’elles produisent.

Le service Batch ne prend actuellement en charge *que* le type de compte de stockage à **usage général**, comme décrit à l’étape 5, [Création d’un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), dans [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Quand vous liez un compte Azure Storage à votre compte Batch, liez *uniquement* un compte de stockage **à usage général**.

![Création d’un compte de stockage « à usage général »][storage_account]

Nous vous recommandons de créer un compte de stockage dédié à votre compte Batch.

>[AZURE.WARNING] Soyez prudent lorsque vous régénérez les clés d’accès d’un compte de stockage lié. Ne régénérez qu’une clé du compte de stockage et cliquez sur **Synchroniser les clés** dans le panneau du compte de stockage lié. Attendez cinq minutes que les clés se propagent aux nœuds de calcul de vos pools, puis régénérez et synchronisez l’autre clé si nécessaire. Si vous régénérez les deux clés en même temps, les nœuds de calcul ne pourront pas les synchroniser, car elles perdront l’accès au compte de stockage.

  ![Régénération de clés de compte de stockage][4]

## Quotas et limites du service Batch

Sachez qu’à l’image de votre abonnement Azure et d’autres services Azure, certains [quotas et limites](batch-quota-limit.md) s’appliquent aux comptes Batch. Les quotas actuels d’un compte Batch sont indiqués dans les **propriétés** du compte sur le portail.

![Quotas de compte Batch dans le portail Azure][quotas]

Gardez ces quotas à l’esprit lorsque vous concevez et mettez à l’échelle vos charges de travail Batch. Par exemple, si votre pool n’atteint pas le nombre de nœuds de calcul que vous avez spécifiés, il se peut que vous ayez atteint le quota limite de votre compte Batch.

Notez également que vous n’êtes pas limité à un compte Batch par abonnement Azure. Vous pouvez exécuter plusieurs charges de travail Batch dans un compte Batch ou répartir vos charges de travail entre plusieurs comptes Batch dans le même abonnement mais dans différentes régions Azure.

Il est possible de relever la plupart de ces quotas simplement en envoyant une demande de support produit gratuite dans le portail Azure. Pour plus d’informations sur les demandes d’augmentation de quotas, consultez [Quotas et limites pour le service Azure Batch](batch-quota-limit.md).

## Autres options de gestion de comptes Batch

Vous pouvez créer et gérer des comptes Batch à l’aide du portail Azure, mais aussi avec les éléments suivants :

* [Applets de commande PowerShell pour Batch](batch-powershell-cmdlets-get-started.md)
* [Interface de ligne de commande Azure](../xplat-cli-install.md)
* [Gestion de lots .NET](batch-management-dotnet.md)

## Étapes suivantes

* Pour en savoir plus sur les concepts et fonctionnalités du service Batch, consultez [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). L’article traite des principales ressources Batch, notamment des pools, des nœuds de calcul, des travaux et des tâches et fournit une vue d’ensemble des fonctionnalités du service qui permettent l’exécution de la charge de travail de calcul à grande échelle.

* Découvrez les bases du développement d’une application Batch à l’aide de la [bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md). Cette [présentation](batch-dotnet-get-started.md) vous décrit la création d’une application opérationnelle qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul, et inclut l’utilisation d’Azure Storage pour la mise en attente et la récupération de fichiers de la charge de travail.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Régénération de clés de compte de stockage"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png

<!---HONumber=AcomDC_0921_2016-->