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
	ms.date="05/12/2016"
	ms.author="marsma"/>

# Création et gestion d'un compte Azure Batch dans le portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](batch-account-create-portal.md)
- [Gestion de lots .NET](batch-management-dotnet.md)

Le [portail Azure][azure_portal] vous fournit les outils dont vous avez besoin pour créer et gérer un compte Azure Batch que vous pouvez utiliser pour le traitement de la charge de travail parallèle à grande échelle. Dans cet article, nous allons parcourir la création du compte Batch en utilisant le portail, et nous traitons les propriétés et les paramètres les plus importants du compte Batch. Par exemple, les applications et les services que vous développez avec Batch ont besoin de l’URL de votre compte et d’une clé d’accès pour communiquer avec les API du service Batch, et les deux se trouvent dans le portail Azure.

>[AZURE.NOTE] Le portail Azure prend actuellement en charge un sous-ensemble de fonctionnalités disponibles dans le service Batch, et notamment la création de compte et la gestion des paramètres et des propriétés du compte. L’ensemble complet des fonctionnalités de Batch, y compris la création et l’exécution des travaux et des tâches, est disponible pour les développeurs via les API de Batch.

## Création d’un compte Batch

1. Connectez-vous au [portail Azure][azure_portal].

2. Cliquez sur **Nouveau** > **Calcul** > **Service Batch**.

	![Batch dans Marketplace][marketplace_portal]

3. Consultez les informations sur le panneau du **Service Batch**, puis cliquez sur **Créer**. Notez que la sélection du modèle de déploiement est désactivée. C’est pour cette raison que Batch utilise le modèle de déploiement du groupe de ressources uniquement.

	![Panneau de création du service Batch dans le portail Azure][3]

4. Le panneau **Nouveau compte Batch** s’affiche. Consultez les articles *a* à *e* ci-après pour obtenir des descriptions de chaque élément du panneau.

    ![Création d’un compte Batch][account_portal]

	a. **Nom de compte** : spécifiez un nom unique pour votre compte Batch. Ce nom doit être unique au sein de la région Azure, le compte est créé (voir l’élément *Emplacement* ci-dessous). Il peut contenir uniquement des caractères minuscules, des chiffres et doit comporter 3 à 24 caractères.

	b. **Abonnement** : sélectionnez un abonnement dans lequel créer le compte Batch. Si vous n’avez qu’un seul abonnement, il est sélectionné par défaut.

	c. **Groupe de ressources** : sélectionnez un groupe de ressources pour votre nouveau compte Batch, ou créez-en un autre s’il n’existe aucun groupe de ressources dans votre abonnement.

	d. **Emplacement** : sélectionnez une région Azure dans laquelle créer le compte Batch. Seules les régions prises en charge par votre abonnement et le groupe de ressources seront affichés en tant qu’option.

    e. **Compte de stockage** (facultatif) : vous pouvez associer (lier) un compte de stockage **à usage général** à votre nouveau compte Batch. La fonctionnalité [Packages des applications](batch-application-packages.md) de Batch utilise le compte de stockage lié pour le stockage et l’extraction des packages d’applications. Consultez [Déploiement d’applications avec des packages d’applications Azure Batch](batch-application-packages.md) pour plus d’informations sur cette fonctionnalité.

     > [AZURE.TIP] La régénération de clés dans un compte de stockage lié exige des considérations spéciales. Consultez [Éléments à prendre en compte pour les comptes Batch](#considerations-for-batch-accounts) ci-dessous pour plus de détails.

5. Cliquez sur **Créer** pour créer le compte.

  Le portail indique qu’il **déploie** le compte, et à l’achèvement, le panneau Compte Batch sera affiché.

## Afficher les propriétés du compte Batch

Le panneau du compte Batch affiche plusieurs propriétés du compte, et fournit un accès à des paramètres supplémentaires tels que les clés d’accès, les quotas, les utilisateurs et l’association au compte de stockage.

* **URL de compte Batch** : cette URL fournit un accès à votre compte Batch lors de l’utilisation des API en tant qu’API [Batch REST][api_rest] ou la bibliothèque client [Batch .NET][api_net] et respecte le format suivant :

  `https://<account_name>.<region>.batch.azure.com`

* **Clés d’accès** : pour afficher et gérer les clés d’accès de votre compte Batch, cliquez sur l’icône de clé pour ouvrir le panneau **Gérer les clés** ou cliquez sur **Tous les paramètres** > **Clés**. Une clé d’accès est requise en cas de communication avec les API de service Batch, telles que la bibliothèque client [Batch REST][api_rest] ou [Batch .NET][api_net].

 ![Clés de compte de Batch][account_keys]

* **Tous les paramètres** : pour gérer tous les paramètres du compte Batch ou pour afficher ses propriétés, cliquez sur **Tous les paramètres** pour ouvrir le panneau **Paramètres**. Ce panneau permet d’accéder à tous les paramètres et propriétés du compte, y compris l’affichage des quotas de compte, sélection d’un compte de stockage Azure pour créer un lien vers le compte Batch et la gestion des utilisateurs.

 ![Panneaux de paramètres et de propriétés de compte batch][5]

## Éléments à prendre en compte pour les comptes Batch

* Vous pouvez également créer et gérer des comptes Batch avec des [applications de commande PowerShell Batch](batch-powershell-cmdlets-get-started.md) et la bibliothèque [Gestion de Batch .NET](batch-management-dotnet.md).

* Vous n’êtes pas facturé pour le compte Batch lui-même. Vous êtes facturé pour toutes les ressources de calcul Azure que vos solutions Batch consomment, ainsi que pour les ressources utilisées par d’autres services pendant l’exécution de vos charges de travail. Par exemple, vous êtes facturé pour les nœuds de calcul présents dans vos pools, et si vous utilisez la fonctionnalité des [packages d’applications](batch-application-packages.md), vous êtes facturé pour les ressources de stockage Azure utilisées pour stocker les versions de package de votre application. Pour plus d’informations, consultez [Tarification de Batch][batch_pricing].

* Vous pouvez exécuter plusieurs charges de travail Batch dans un seul compte Batch ou distribuer vos charges de travail entre plusieurs comptes Batch dans différentes régions Azure.

* Si vous exécutez plusieurs charges de travail Batch à grande échelle, tenez compte des [limites et quotas du service Batch](batch-quota-limit.md) qui s’appliquent à votre abonnement Azure et à chaque compte Batch. Les quotas actuels sur le compte Batch apparaissent sur le portail dans les propriétés du compte.

* Si vous associez (liez) un compte de stockage à votre compte Batch, veillez à régénérer les clés d’accès au compte de stockage. Vous devez régénérer une seule clé de compte de stockage, cliquez sur **Clés de synchronisation** dans le panneau de compte de stockage associé, patientez 5 minutes pour permettre la propagation des clés aux nœuds de calcul dans vos pools, puis régénérez et synchronisez l’autre clé si nécessaire. Si vous régénérez les clés en même temps, les nœuds de calcul ne seront pas en mesure de synchroniser une clé, et ils perdront l’accès au compte de stockage.

  ![Régénération de clés de compte de stockage][4]

> [AZURE.IMPORTANT] Le service Batch prend actuellement en charge *uniquement* le type de compte de stockage à **usage général**, comme décrit à l’étape 5, [Créez un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md). Lorsque vous liez un compte Azure Storage à votre compte Batch, liez *uniquement* un compte de stockage à **usage général** .

## Étapes suivantes

* Consultez la [Vue d’ensemble des fonctionnalités d’Azure Batch](batch-api-basics.md) pour en savoir plus sur les concepts du service Batch. L’article traite des principales ressources Batch, notamment des pools, des nœuds de calcul, des travaux et des tâches et fournit une vue d’ensemble des fonctionnalités du service qui permettent l’exécution de la charge de travail de calcul à grande échelle.

* Apprenez les bases du développement d’une application prenant en charge Batch en utilisant la [bibliothèque cliente .NET Batch](batch-dotnet-get-started.md). L’[article de présentation](batch-dotnet-get-started.md) vous guide dans une application opérationnelle qui utilise le service Batch pour exécuter une charge de travail sur plusieurs nœuds de calcul et inclut l’utilisation d’un stockage Azure pour la mise en attente et la récupération du fichier de charge de travail.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[3]: ./media/batch-account-create-portal/batch_acct_03.png "Panneau de création du service Batch dans le portail Azure"
[4]: ./media/batch-account-create-portal/batch_acct_04.png "Régénération de clés de compte de stockage"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "Panneaux de paramètres et de propriétés de compte batch"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG

<!---HONumber=AcomDC_0518_2016-->