<properties
	pageTitle="Gestion des comptes avec Batch Management .NET | Microsoft Azure"
	description="Créez, supprimez et modifiez des comptes Azure Batch dans vos applications avec la bibliothèque Batch Management .NET."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="11/10/2015"
	ms.author="v-marsma"/>

# Gérer les quotas et les comptes Azure Batch avec Batch Management .NET

> [AZURE.SELECTOR]
- [Azure preview portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Réduisez la surcharge de maintenance dans vos applications Azure Batch avec la bibliothèque [Batch Management .NET][api_mgmt_net] en automatisant la création et la suppression de comptes Batch, la gestion de clés et la détection des quotas.

- **Créez et supprimez des comptes Batch** dans n'importe quelle région. Si, par exemple, en tant qu'éditeur de logiciels indépendant (ISV) vous fournissez un service à vos clients dans lequel un compte Batch distinct est affecté à chacun à des fins de facturation, vous pouvez ajouter des fonctionnalités de création et de suppression de comptes à votre portail client.
- **Récupérez et régénérez des clés de compte** par programme pour tous vos comptes Batch. Cela est particulièrement pratique pour maintenir la conformité aux éventuelles stratégies de sécurité appliquant une substitution ou une expiration périodique des clés de compte. Lorsque vous avez plusieurs comptes Batch dans différentes régions Azure, l'automatisation de ce processus de substitution augmente l'efficacité de votre solution.
- **Vérifiez les quotas des comptes** et déterminez quels comptes Batch possèdent quelles limites sans avoir à tâtonner. En vérifiant les quotas de vos comptes avant de commencer les tâches, de créer des pools ou d'ajouter des nœuds de calcul, vous pouvez ajuster de façon proactive l'endroit et le moment où ces ressources de calcul sont créées. Vous pouvez déterminer quels comptes requièrent des augmentations de quota avant d'allouer des ressources supplémentaires à ces comptes.
- **Combinez les fonctionnalités d'autres services Azure** pour une expérience de gestion complète en tirant parti conjointement de Batch Management .NET, d'[Azure Active Directory][aad_about] et d'[Azure Resource Manager][resman_overview] dans la même application. Avec ces fonctionnalités et leurs API, vous pouvez fournir une expérience d'authentification transparente, la création et la suppression des groupes de ressources et les fonctionnalités décrites ci-dessus, pour une solution de gestion de bout en bout.

> [AZURE.NOTE]Si cet article se concentre sur la gestion par programme de vos comptes, clés et quotas Batch, vous pouvez effectuer la plupart de ces activités avec le [portail Azure en version préliminaire][azure_portal]. Consultez [Créer et gérer un compte Azure Batch dans le portail Azure en version préliminaire](batch-account-create-portal.md) et [Quotas et limites du service Azure Batch](batch-quota-limit.md) pour plus d'informations.

## Créer et supprimer des comptes Batch

Comme mentionné ci-dessus, l'une des principales fonctionnalités de l'API Batch Management consiste à créer et à supprimer des comptes Batch dans une région Azure. Pour ce faire, vous allez utiliser [BatchManagementClient.Accounts.CreateAsync][net_create] et [DeleteAsync][net_delete], ou leurs équivalents synchrones.

L'extrait de code suivant crée un compte, récupère le compte créé à partir du service Batch, puis le supprime. Dans cet extrait de code et les autres extraits de cet article, `batchManagementClient` est une instance entièrement initialisée de [BatchManagementClient][net_mgmt_client].

```
// Create a new Batch account
await batchManagementClient.Accounts.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mynewaccount");
AccountResource account = getResponse.Resource;

// Delete the account
await batchManagementClient.Accounts.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE]Les applications qui utilisent la bibliothèque Batch Management .NET et son BatchManagementClient nécessitent un accès **administrateur de services fédérés** ou **coadministrateur** à l'abonnement qui possède le compte Batch à gérer. Consultez la section [Azure Active Directory](#aad) ci-dessous et l'exemple de code [AccountManagement][acct_mgmt_sample] pour plus d'informations.

## Récupérer et régénérer des clés de compte

Récupérez les clés primaires et secondaires de n'importe quel compte Batch de votre abonnement avec [ListKeysAsync][net_list_keys] et régénérez ces clés avec [RegenerateKeyAsync][net_regenerate_keys].

```
// Get and print the primary and secondary keys
BatchAccountListKeyResponse accountKeys = await batchManagementClient.Accounts.ListKeysAsync("MyResourceGroup", "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.PrimaryKey);
Console.WriteLine("Secondary key: {0}", accountKeys.SecondaryKey);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys = await batchManagementClient.Accounts.RegenerateKeyAsync(
	"MyResourceGroup",
	"mybatchaccount",
	new BatchAccountRegenerateKeyParameters() { KeyName = AccountKeyType.Primary });
```

> [AZURE.TIP]Vous pouvez créer un flux de travail de connexion rationalisé pour vos applications de gestion. Tout d'abord, récupérez une clé du compte Batch que vous voulez gérer avec [ListKeysAsync][net_list_keys], puis utilisez cette clé lors de l'initialisation des [BatchSharedKeyCredentials][net_sharedkeycred] de la bibliothèque Batch .NET utilisés pour l'initialisation d'un [BatchClient][net_batch_client].

## Vérifier les quotas d'un abonnement Azure et d'un compte Batch

Les abonnements Azure et les services Azure, comme Batch, ont tous des quotas par défaut limitant le nombre de certaines entités. Les quotas par défaut des abonnements Azure se trouvent dans [Limites de service, quotas et contraintes des abonnements Azure](./../azure-subscription-service-limits.md), et ceux du service Batch dans [Quotas et limites du service Azure Batch](batch-quota-limit.md). La bibliothèque Batch Management .NET offre la possibilité de vérifier ces quotas au sein de vos applications, ce qui vous permet de prendre des décisions d'allocation avant d'ajouter des comptes ou des ressources de calcul, par exemple des pools et des nœuds de calcul.

### Vérifier les quotas d'un compte Batch dans un abonnement Azure

Avant de créer un compte Batch dans une région, vous pouvez vérifier dans votre abonnement Azure que vous êtes en mesure d'ajouter un compte dans cette région.

Dans l'extrait de code ci-dessous, nous utilisons tout d'abord [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts] pour obtenir la liste de tous les comptes Batch d'un abonnement. Une fois cette liste obtenue, nous déterminons le nombre de comptes dans la région cible, puis nous utilisons [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] pour récupérer le quota du compte Batch et déterminer combien de comptes (le cas échéant) peuvent être créés dans cette région.

```
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse = await batchManagementClient.Accounts.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}", creds.SubscriptionId, accounts.Count);

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

Dans l'extrait ci-dessus, `creds` est une instance de [TokenCloudCredentials][azure_tokencreds]. Pour voir un exemple de création de cet objet, consultez l'exemple de code [AccountManagement][acct_mgmt_sample] sur GitHub.

### Vérifier les quotas de ressources de calcul dans un compte Batch

Avant d'augmenter les ressources de calcul dans votre solution Batch, vous pouvez vous assurer que les ressources que vous souhaitez allouer n'éclipseront pas les quotas de comptes actuellement en place. Dans l'extrait de code ci-dessous, nous affichons simplement les informations de quotas du compte Batch nommé `mybatchaccount`. Dans votre application, vous pouvez utiliser ces informations pour déterminer si le compte peut gérer les ressources supplémentaires que vous souhaitez créer.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT]Bien qu'il y ait des quotas par défaut pour les abonnements et services Azure, la plupart de ces limites peuvent être relevées en émettant une demande dans le [portail Azure en version préliminaire][azure_portal]. Par exemple, consultez [Quotas et limites du service Azure Batch](batch-quota-limit.md) pour obtenir des instructions sur l'augmentation des quotas de vos comptes Batch.

## Batch Management .NET, AAD et Resource Manager

Lorsque vous travaillez avec la bibliothèque Batch Management .NET, vous exploitez généralement à la fois les capacités d'[Azure Active Directory][aad_about] (AAD) et celles d'[Azure Resource Manager][resman_overview]. L'exemple de projet décrit ci-dessous utilise à la fois Azure Active Directory et Resource Manager tout en montrant l'API Batch Management .NET.

### <a name="aad"></a>Azure Active Directory

Azure lui-même utilise Azure Active Directory (AAD) pour l'authentification de ses clients, de ses administrateurs de services fédérés et de ses utilisateurs professionnels. Dans le contexte de Batch Management .NET, vous allez l'utiliser pour authentifier un administrateur ou un coadministrateur d'abonnement qui permettra ensuite à la bibliothèque de gestion d'interroger le service Batch et d'effectuer les opérations décrites dans cet article.

Dans l'exemple de projet présenté ci-dessous, la [bibliothèque d'authentification Azure Active Directory][aad_adal] (ADAL) est utilisée pour inviter l'utilisateur à entrer ses informations d'identification Microsoft. Une fois fournies, les informations d'identification de l'administrateur de services fédérés ou du coadministrateur permettent à l'application de demander à Azure une liste d'abonnements et de créer et de supprimer à la fois des groupes de ressources et des comptes Batch.

### Gestionnaire de ressources

Lorsque vous créez des comptes Batch avec la bibliothèque Batch Management .NET, vous le faites généralement dans un [groupe de ressources][resman_overview]. Vous pouvez créer le groupe de ressources par programme à l'aide du [ResourceManagementClient][resman_client] trouvé dans la bibliothèque [Resource Manager .NET][resman_api], ou bien ajouter un compte à un groupe de ressources créé précédemment avec le [portail Azure en version préliminaire][azure_portal].

## <a name="sample"></a>Exemple de projet sur GitHub

Découvrez l'exemple de projet [AccountManagment][acct_mgmt_sample] sur GitHub pour voir la bibliothèque Batch Management .NET en pratique. Cette application de console illustre la création et l'utilisation de [BatchManagementClient][net_mgmt_client] et de [ResourceManagementClient][resman_client], ainsi que l'utilisation de la [bibliothèque d'authentification Azure Active Directory][aad_adal] (ADAL) requise par les deux clients.

> [AZURE.IMPORTANT]Pour exécuter avec succès l'exemple d'application, vous devez tout d'abord l'enregistrer dans Azure Active Directory avec le portail de gestion Azure. Lisez **Ajout d'une application** dans [Intégration d'applications à Azure Active Directory][aad_integrate], puis suivez les étapes décrites dans l'article pour enregistrer l'exemple d'application dans votre propre compte.

L'exemple d'application illustre les opérations suivantes :

1. Acquérir un jeton de sécurité dans Azure Active Directory (AAD) avec [ADAL][aad_adal]. Si l'utilisateur n'est pas encore connecté, il sera invité à entrer ses informations d'identification Azure.
2. Avec le jeton de sécurité obtenu avec AAD, créez un [SubscriptionClient][resman_subclient] qui demande à Azure la liste des abonnements associés au compte, permettant à l'utilisateur d'en sélectionner un dans le cas où plusieurs ont été trouvés.
3. Créer un nouvel objet d'informations d'identification associé à l'abonnement sélectionné
4. Créer un [ResourceManagementClient][resman_client] avec les nouvelles informations d'identification
5. Utiliser le [ResourceManagementClient][resman_client] pour créer un nouveau groupe de ressources
6. Utiliser le [BatchManagementClient][net_mgmt_client] pour effectuer un certain nombre d'opérations de compte Batch :
  - Créer un nouveau compte Batch dans le groupe de ressources créé
  - Récupérer le compte créé dans le service Batch
  - Afficher les clés du nouveau compte
  - Régénérer une nouvelle clé primaire pour le compte
  - Afficher les informations de quota du compte
  - Afficher les informations de quota de l'abonnement
  - Afficher tous les comptes de l'abonnement
  - Supprimer le compte créé
7. Supprimer le groupe de ressources

Avant de supprimer le compte Batch et le groupe de ressources créés, vous pouvez les examiner tous les deux dans le [portail Azure en version préliminaire][azure_portal] :

![Portail Azure en version préliminaire affichant le groupe de ressources et le compte Batch][1] <br /> *Portail Azure en version préliminaire affichant le nouveau groupe de ressources et le nouveau compte Batch*

[aad_about]: ../active-directory/active-directory-whatis.md "Qu’est-ce qu’Azure Active Directory ?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénarios d’authentification pour Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Intégration d'applications dans Azure Active Directory"
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
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png

<!---HONumber=Nov15_HO4-->