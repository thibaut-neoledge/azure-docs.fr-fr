---
ms.assetid: 
title: "Clés de compte de stockage Azure Key Vault"
description: "Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés."
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: c7b20c83b356dd698e66919483c9ff6f0e8a36ef
ms.contentlocale: fr-fr
ms.lasthandoff: 08/03/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Clés de compte de stockage Azure Key Vault

Avant l’apparition des clés de compte de stockage Azure Key Vault, les développeurs devaient gérer leurs propres clés de compte de stockage Azure (ASA) et les faire tourner manuellement ou par le biais d’une automatisation externe. À présent, les clés de compte de stockage Key Vault sont implémentées sous forme de [secrets Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) pour l’authentification avec un compte de stockage Azure. 

La fonctionnalité de clé ASA gère le roulement des secrets à votre place et vous évite d’avoir à établir un contact direct avec une clé ASA en proposant les signatures d’accès partagé (SAP) sous forme de méthode. 

Pour plus d’informations générales sur les comptes de stockage Azure, consultez la page [À propos des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Prise en charge des interfaces

La fonctionnalité de clés de compte de stockage Azure est pour le moment disponible sur les interfaces REST, .NET/C# et PowerShell. Pour plus d’informations, consultez la page [Informations de référence Key Vault](https://docs.microsoft.com/azure/key-vault/).

## <a name="storage-account-keys-behavior"></a>Comportement des clés de compte de stockage

### <a name="what-key-vault-manages"></a>Ce que gère Key Vault

Key Vault exécute plusieurs fonctions de gestion interne à votre place lorsque vous utilisez des clés de compte de stockage.

1. Azure Key Vault gère les clés d’un compte de stockage Azure (SAP). 
    - En interne, Azure Key Vault peut lister (synchroniser) les clés avec un compte de stockage Azure.  
    - Azure Key Vault régénère (fait tourner) les clés régulièrement. 
    - Les valeurs de clés ne sont jamais retournées en réponse à l’appelant. 
    - Azure Key Vault gère les clés des comptes de stockage ainsi que des comptes de stockage Classic. 
2. Azure Key Vault permet au propriétaire du coffre / de l’objet (vous) de créer des définitions de SAP (de compte ou de service). 
    - La valeur de la SAP, créée à l’aide de sa définition, est retournée sous forme de secret par le biais du chemin d’accès de l’URI REST. Pour plus d’informations, consultez la page [Opérations du compte de stockage Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations).

### <a name="naming-guidance"></a>Aide pour l’affectation de noms

Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.  
 
Un nom de définition de SAP doit avoir une longueur comprise entre 1 et 102 caractères, composés uniquement de 0-9, a-z et A-Z.

## <a name="developer-experience"></a>Expérience de développement 

### <a name="before-azure-key-vault-storage-keys"></a>Avant l’apparition des clés de stockage Azure Key Vault 

Les développeurs avaient besoin d’appliquer les pratiques suivantes avec une clé de compte de stockage pour obtenir un accès au stockage Azure. 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Après l’apparition des clés de stockage Azure Key Vault 

```
//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);

  ```
 
 ### <a name="developer-best-practices"></a>Meilleures pratiques de développement 

- Autorisez uniquement Key Vault à gérer vos clés ASA. N’essayez pas de les gérer vous-même, vous interféreriez avec les processus de Key Vault. 
- Ne permettez pas que les clés ASA soient gérées par plusieurs objets Key Vault. 
- Si vous devez régénérer manuellement vos clés ASA, nous vous recommandons de le faire avec Key Vault. 

## <a name="getting-started"></a>Prise en main

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configuration des autorisations de contrôle d’accès en fonction des permissions (RBAC)

Key Vault a besoin d’autorisations pour *répertorier* et *régénérer* les clés d’un compte de stockage. Configurez-les en suivant les étapes ci-dessous :

- Récupérez la valeur ObjectId de Key Vault : 

    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- Attribuez le rôle d’opérateur de clés de stockage à l’identité Azure Key Vault : 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Pour un type de compte classique, réglez le paramètre de rôle sur *« Rôle de service d’opérateur de clés du compte de stockage Classic »*.

### <a name="storage-account-onboarding"></a>Intégration des comptes de stockage 

Exemple : en tant que propriétaire d’un objet Key Vault, vous ajoutez un objet de compte de stockage à votre coffre Azure Key Vault pour intégrer un compte de stockage.

Lors de l’intégration, Key Vault doit vérifier que l’identité du compte en cours d’intégration dispose des autorisations adéquates pour *répertorier* et *régénérer* les clés de stockage. Pour vérifier ces autorisations, Key Vault obtient un jeton OBO (On Behalf Of) de la part du service d’authentification, audience définie pour le gestionnaire de ressources Azure, et effectue un appel de la *liste* clé auprès du service de stockage Azure. Si l’appel de la *liste* échoue, la création de l’objet Key Vault échoue avec le code d’état HTTP *Interdit*. Les clés listées de cette manière sont mises en cache avec le stockage de votre entité de coffre de clés. 

Key Vault doit vérifier que l’identité a l’autorisation de *régénérer* pour pouvoir prendre possession de la régénération des clés. Pour vérifier que l’identité, par le biais du jeton OBO, ainsi que l’identité interne Key Vault disposent de ces autorisations :

- Key Vault liste les autorisations de contrôle d’accès en fonction du rôle sur la ressource du compte de stockage.
- Key Vault valide la réponse par le rapprochement d’actions et d’inactions avec des expressions régulières. 

Recherchez des exemples de prise en charge dans la section relative aux [exemples de clés de comptes de stockage gérés de Key Vault](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167).

Si l’identité n’a pas d’autorisation de *régénération* ou que l’identité interne de Key Vault n’a pas l’autorisation de *répertorier* ou de *régénérer* des éléments, la demande d’intégration échoue en renvoyant un message et un code d’erreur appropriés. 

Le jeton OBO ne fonctionne que si l’on utilise des applications clientes natives internes de PowerShell ou CLI.

## <a name="other-applications"></a>Autres applications

- Les jetons SAP, construits à l’aide de clés de compte de stockage Key Vault, assurent un accès encore plus contrôlé à un compte de stockage Azure. Pour plus d’informations, consultez la page [Utiliser des signatures d’accès partagé](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Voir aussi

- [Présentation des clés, des secrets et des certificats](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog de l’équipe Key Vault](https://blogs.technet.microsoft.com/kv/)

