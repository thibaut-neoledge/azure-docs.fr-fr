---
title: Gestion des ressources de compte avec Batch Management .NET | Microsoft Docs
description: "Créez, supprimez et modifiez des ressources de compte Azure Batch avec la bibliothèque Batch Management .NET."
services: batch
documentationcenter: .net
author: mmacy
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 10/19/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d2a2bf6c6046aa1de67d85b23b0c11bf7f9d4400


---
# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Gérer les quotas et les comptes Azure Batch avec Batch Management .NET
> [!div class="op_single_selector"]
> * [Portail Azure](batch-account-create-portal.md)
> * [Gestion de lots .NET](batch-management-dotnet.md)
> 
> 

Vous pouvez réduire la surcharge de maintenance dans vos applications Azure Batch avec la bibliothèque [Batch Management .NET][api_mgmt_net] en automatisant la création et la suppression de comptes Batch, la gestion de clés et la détection des quotas.

* **Créez et supprimez des comptes Batch** dans n'importe quelle région. Si, par exemple, en tant qu'éditeur de logiciels indépendant (ISV) vous fournissez un service à vos clients dans lequel un compte Batch distinct est affecté à chacun à des fins de facturation, vous pouvez ajouter des fonctionnalités de création et de suppression de comptes à votre portail client.
* **Récupérez et régénérez des clés de compte** par programme pour tous vos comptes Batch. Cela est pratique pour maintenir la conformité aux stratégies de sécurité appliquant une substitution ou une expiration périodique des clés de compte. Lorsque vous avez plusieurs comptes Batch dans différentes régions Azure, l’automatisation de ce processus de substitution augmente l’efficacité de votre solution.
* **Vérifiez les quotas des comptes** et déterminez quels comptes Batch possèdent quelles limites sans avoir à tâtonner. En vérifiant les quotas de vos comptes avant de commencer les travaux, de créer des pools ou d’ajouter des nœuds de calcul, vous pouvez ajuster de façon proactive l’endroit et le moment où ces ressources de calcul sont créées. Vous pouvez déterminer quels comptes requièrent des augmentations de quota avant d’allouer des ressources supplémentaires à ces comptes.
* **Combinez les fonctionnalités d’autres services Azure** pour une expérience de gestion complète en utilisant conjointement Batch Management .NET, [Azure Active Directory][aad_about] et [Azure Resource Manager][resman_overview] dans la même application. En utilisant ces fonctionnalités et leurs API, vous pouvez fournir une expérience d’authentification transparente, une fonction de création et de suppression des groupes de ressources ainsi que les fonctionnalités décrites ci-dessus, pour une solution de gestion de bout en bout.

> [!NOTE]
> Si cet article se concentre sur la gestion par programme de vos comptes, clés et quotas Batch, vous pouvez effectuer la plupart de ces activités avec le [portail Azure][azure_portal]. Pour plus d’informations, consultez [Création d’un compte Azure Batch à l’aide du portail Azure](batch-account-create-portal.md) et [Quotas et limites pour le service Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Créez et supprimez des comptes Batch
Comme mentionné ci-dessus, l’une des principales fonctionnalités de l’API Batch Management consiste à créer et à supprimer des comptes Batch dans une région Azure. Pour ce faire, utilisez [BatchManagementClient.Account.CreateAsync][net_create] et [DeleteAsync][net_delete] ou leurs équivalents synchrones.

L’extrait de code suivant crée un compte, récupère le compte créé à partir du service Batch, puis le supprime. Dans cet extrait de code et les autres extraits de cet article, `batchManagementClient` est une instance entièrement initialisée de [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Les applications qui utilisent la bibliothèque Batch Management .NET et sa classe BatchManagementClient nécessitent un accès **administrateur de services fédérés** ou **coadministrateur** à l’abonnement qui possède le compte Batch à gérer. Pour plus d’informations, consultez la section [Azure Active Directory](#azure-active-directory) et l’exemple de code [AccountManagement][acct_mgmt_sample].
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Récupérez et régénérez des clés de compte
Récupérez les clés primaires et secondaires de n’importe quel compte Batch de votre abonnement avec [ListKeysAsync][net_list_keys]. Vous pouvez régénérer ces clés à l’aide de [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Vous pouvez créer un flux de travail de connexion rationalisé pour vos applications de gestion. Commencez par récupérer une clé de compte pour le compte Batch que vous souhaitez gérer avec [ListKeysAsync][net_list_keys]. Utilisez ensuite cette clé lors de l’initialisation de la classe [BatchSharedKeyCredentials][net_sharedkeycred] de la bibliothèque .NET de Batch, qui est utilisée lors de l’initialisation de [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Vérifier les quotas d'un abonnement Azure et d'un compte Batch
Les abonnements Azure et les services Azure, comme Batch, ont tous des quotas par défaut qui limitent le nombre de certaines entités. Pour connaître les quotas par défaut des abonnements Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md). Pour obtenir les quotas par défaut du service Batch, consultez [Quotas et limites pour le service Azure Batch](batch-quota-limit.md). La bibliothèque Batch Management .NET vous permet de vérifier ces quotas dans vos applications. Vous pouvez ainsi plus facilement décider des allocations avant d’ajouter des comptes ou des ressources de calcul comme les pools et nœuds de calcul.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Vérifier les quotas d'un compte Batch dans un abonnement Azure
Avant de créer un compte Batch dans une région, vous pouvez vérifier dans votre abonnement Azure que vous êtes en mesure d'ajouter un compte dans cette région.

Dans l’extrait de code ci-dessous, nous utilisons tout d’abord [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] pour obtenir la liste de tous les comptes Batch d’un abonnement. Une fois que nous avons obtenu cette collection, nous pouvons déterminer le nombre de comptes qui se trouvent dans la région cible. Nous utilisons ensuite [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] pour obtenir le quota de compte Batch et déterminer le nombre de comptes (le cas échéant) pouvant être créés dans cette région.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Dans l’extrait ci-dessus, `creds` est une instance de [TokenCloudCredentials][azure_tokencreds]. Pour voir un exemple de création de cet objet, consultez l’exemple de code [AccountManagement][acct_mgmt_sample] sur GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Vérifier les quotas de ressources de calcul dans un compte Batch
Avant d’augmenter les ressources de calcul dans votre solution Batch, vous pouvez vous assurer que les ressources que vous souhaitez allouer ne dépasseront pas les quotas de comptes. Dans l’extrait de code ci-dessous, nous imprimons les informations de quota pour le compte Batch nommé `mybatchaccount`. Dans votre application, vous pouvez utiliser ces informations pour déterminer si le compte peut ou non gérer les ressources supplémentaires à créer.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Bien qu’il y ait des quotas par défaut pour les abonnements et services Azure, la plupart de ces limites peuvent être relevées en émettant une demande dans le [portail Azure][azure_portal]. Par exemple, consultez [Quotas et limites du service Azure Batch](batch-quota-limit.md) pour obtenir des instructions sur l’augmentation des quotas de vos comptes Batch.
> 
> 

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Batch Management .NET, Azure AD et Resource Manager
Lorsque vous travaillez avec la bibliothèque Batch Management .NET, vous utilisez également [Azure Active Directory][aad_about] (Azure AD) et [Azure Resource Manager][resman_overview]. L’exemple de projet décrit ci-dessous utilise à la fois Azure Active Directory et Resource Manager tout en illustrant l’API Batch Management .NET.

### <a name="azure-active-directory"></a>Azure Active Directory
Azure lui-même utilise Azure AD pour l’authentification de ses clients, de ses administrateurs de services fédérés et de ses utilisateurs professionnels. Dans le contexte de Batch Management .NET, utilisez Azure AD pour authentifier un administrateur ou coadministrateur d’abonnement. La bibliothèque de gestion peut ainsi interroger le service Batch et effectuer les opérations décrites dans cet article.

Dans l’exemple de projet présenté ci-dessous, la [bibliothèque d’authentification Azure Active Directory][aad_adal] (ADAL) est utilisée pour inviter l’utilisateur à entrer ses informations d’identification Microsoft. Une fois fournies, les informations d’identification de l’administrateur ou du coadministrateur de services permettent à l’application de demander à Azure une liste d’abonnements et de créer et supprimer à la fois des groupes de ressources et des comptes Batch.

### <a name="resource-manager"></a>Gestionnaire de ressources
Lorsque vous créez des comptes Batch avec la bibliothèque Batch Management .NET, vous le faites généralement dans un [groupe de ressources][resman_overview]. Vous pouvez créer le groupe de ressources au moyen d’un programme, en utilisant la classe [ResourceManagementClient][resman_client] dans la bibliothèque [Resource Manager .NET][resman_api]. Vous pouvez aussi ajouter un compte à un groupe de ressources existant que vous avez créé précédemment à l’aide du [portail Azure][azure_portal].

## <a name="sample-project-on-github"></a>Exemple de projet sur GitHub
Pour voir la bibliothèque Batch Management .NET en pratique, découvrez l’exemple de projet [AccountManagment][acct_mgmt_sample] sur GitHub. Cette application de console illustre la création et l’utilisation de [BatchManagementClient][net_mgmt_client] et [ResourceManagementClient][resman_client]. Elle montre également l’utilisation de la [bibliothèque d’authentification Azure Active Directory][aad_adal] (ADAL) dont ont besoin les deux clients.

Pour exécuter avec succès cet exemple d’application, vous devez tout d’abord l’enregistrer dans Azure AD à l’aide du portail Azure. Suivez les étapes de la section [Ajout d’une application](../active-directory/active-directory-integrating-applications.md#adding-an-application) dans [Intégration d’applications à Azure Active Directory][aad_integrate] pour enregistrer l’exemple d’application dans le répertoire par défaut de votre propre compte. Veillez à sélectionner **Application cliente native** pour le type d’application. Vous pouvez également spécifier tout URI valide (tel que `http://myaccountmanagementsample`) comme **URI de redirection** : inutile qu’il s’agisse d’un point de terminaison réel.

Après avoir ajouté votre application, déléguez l’autorisation **Accéder à la gestion des services Azure en tant qu’organisation** à l’application *API de gestion des services Windows Azure* dans les paramètres de l’application, dans le portail :

![Autorisations sur les applications dans le portail Azure][2]

> [!TIP]
> Si l’**API Gestion des services Microsoft Azure** n’apparaît pas sous *Autorisations pour d’autres applications*, cliquez sur **Ajout d’une application**, sélectionnez **API Gestion des services Microsoft Azure**, puis cliquez sur la coche. Ensuite, déléguez des autorisations comme indiqué ci-dessus.
> 
> 

Une fois que vous avez ajouté l’application conformément à la description ci-dessus, mettez à jour `Program.cs` dans l’exemple de projet [AccountManagment][acct_mgmt_sample] avec l’URI de redirection et l’ID client de votre application. Vous pouvez trouver ces valeurs dans l’onglet **Configurer** de votre application :

![Configuration des applications dans le portail Azure][3]

L’exemple d’application [AccountManagment][acct_mgmt_sample] illustre les opérations suivantes :

1. Acquérir un jeton de sécurité d’Azure AD avec [ADAL][aad_adal]. Si l’utilisateur n’est pas encore connecté, il est invité à entrer ses informations d’identification Azure.
2. À l’aide du jeton de sécurité obtenu à partir d’Azure AD, créez une classe [SubscriptionClient][resman_subclient] pour demander à Azure la liste des abonnements associés au compte. Cela permet de sélectionner un abonnement lorsque plusieurs abonnements sont identifiés.
3. Créez un objet d’informations d’identification associé à l’abonnement sélectionné.
4. Créez une classe [ResourceManagementClient][resman_client] avec les nouvelles informations d’identification.
5. Utilisez [ResourceManagementClient][resman_client] pour créer un groupe de ressources.
6. Utilisez [BatchManagementClient][net_mgmt_client] pour effectuer un certain nombre d’opérations de compte Batch :
   * Créez un compte Batch dans le nouveau groupe de ressources.
   * Récupérer le compte créé dans le service Batch
   * Imprimer les clés du nouveau compte
   * Régénérer une nouvelle clé primaire pour le compte
   * Imprimer les informations de quota du compte
   * Imprimer les informations de quota de l’abonnement
   * Imprimer tous les comptes de l’abonnement
   * Supprimer le compte créé
7. Supprimez le groupe de ressources.

Avant de supprimer le compte Batch et le groupe de ressources créés, vous pouvez les examiner tous les deux dans le [portail Azure][azure_portal] :

![Portail Azure affichant le groupe de ressources et le compte Batch][1]
<br />
*Portail Azure affichant le nouveau groupe de ressources et le nouveau compte Batch*

[aad_about]: ../active-directory/active-directory-whatis.md "Qu’est-ce qu’Azure Active Directory ?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénarios d’authentification pour Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Intégration d’applications dans Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png



<!--HONumber=Nov16_HO3-->


