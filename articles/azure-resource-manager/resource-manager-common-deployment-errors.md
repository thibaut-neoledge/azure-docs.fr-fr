---
title: "Résolution des erreurs courantes lors du déploiement Azure | Microsoft Docs"
description: "Décrit comment résoudre les erreurs courantes lors du déploiement de ressources sur Azure à l’aide d’Azure Resource Manager."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "erreur de déploiement, déploiement Azure, déployer dans azure"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 30adc10d01290f14a3e116813b19916fa36ab0bc
ms.contentlocale: fr-fr
ms.lasthandoff: 08/19/2017

---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager
Cette rubrique décrit comment résoudre certaines erreurs courantes liées au déploiement d’Azure que vous pouvez rencontrer.

Les codes d’erreur suivants sont décrits dans cette rubrique :

* [AccountNameInvalid](#accountnameinvalid)
* [Échec de l’autorisation](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [DisallowedOperation](#disallowedoperation)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

## <a name="deploymentfailed"></a>DeploymentFailed

Ce code d’erreur indique une erreur de déploiement générale, mais il ne s’agit pas du code d’erreur dont vous avez besoin pour la résolution du problème. Le code d’erreur qui vous permet de résoudre le problème se trouve généralement à un niveau en dessous de cette erreur. Par exemple, l’illustration suivante montre le code d’erreur **RequestDisallowedByPolicy** qui se trouve sous l’erreur de déploiement.

![afficher le code d'erreur](./media/resource-manager-common-deployment-errors/error-code.png)

## <a name="skunotavailable"></a>SkuNotAvailable

Lors du déploiement d’une ressource (généralement une machine virtuelle), vous pouvez recevoir le message et le code d’erreur suivants :

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Vous recevez cette erreur lorsque la ressource de référence (SKU) que vous avez sélectionnée (par exemple, la taille de la machine virtuelle) n’est pas disponible pour l’emplacement que vous avez sélectionné. Pour résoudre ce problème, vous devez déterminer quelles références sont disponibles dans une région. Pour identifier les références disponibles, vous pouvez utiliser PowerShell, le portail ou une opération REST.

- Pour PowerShell, utilisez [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) et filtrez par emplacement. Pour cette commande, vous devez disposer de la version la plus récente de PowerShell.

  ```powershell
  Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
  ```

  Les résultats incluent une liste de références pour l’emplacement et toutes les restrictions éventuellement liées aux références concernées.

  ```powershell
  ResourceType                Name      Locations Restriction                      Capability Value
  ------------                ----      --------- -----------                      ---------- -----
  availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
  availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
  virtualMachines      Standard_A0 southcentralus
  virtualMachines      Standard_A1 southcentralus
  virtualMachines      Standard_A2 southcentralus
  ```

- Pour utiliser le [portail](https://portal.azure.com), connectez-vous au portail et ajoutez une ressource à l’aide de l’interface. Lorsque vous définissez les valeurs, vous voyez les références (SKU) disponibles pour cette ressource. Vous n’avez pas besoin de terminer le déploiement.

    ![Références disponibles](./media/resource-manager-common-deployment-errors/view-sku.png)

- Pour utiliser l’API REST pour des machines virtuelles, envoyez la demande suivante :

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  Elle renvoie les références et les régions disponibles dans le format suivant :

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

Si vous ne parvenez pas à trouver une référence appropriée dans cette région ou une autre qui réponde aux besoins de votre entreprise, envoyez une [demande de référence SKU](https://aka.ms/skurestriction) au support Azure.

## <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

Si vous recevez cette erreur, vous utilisez un abonnement qui n’est pas autorisé à accéder à des services Azure autres qu’Azure Active Directory. Vous pouvez avoir ce type d’abonnement lorsque vous avez besoin d’accéder au Portail Classic mais que vous n’êtes pas autorisé à déployer des ressources. Pour résoudre ce problème, vous devez utiliser un abonnement qui dispose de l’autorisation de déployer des ressources.  

Pour afficher vos abonnements disponibles avec PowerShell, utilisez :

```powershell
Get-AzureRmSubscription
```

Pour définir l’abonnement actif, utilisez :

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

Pour afficher vos abonnements disponibles avec Azure CLI 2.0, utilisez :

```azurecli
az account list
```

Pour définir l’abonnement actif, utilisez :

```azurecli
az account set --subscription {subscription-name}
```

## <a name="invalidtemplate"></a>InvalidTemplate
Cette erreur peut résulter de différents types d’erreurs.

- Erreur de syntaxe

   Si vous recevez un message d’erreur qui indique que la validation du modèle a échoué, vous avez peut-être un problème de syntaxe dans votre modèle.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   Cette erreur est facile à commettre car les expressions de modèle peuvent être complexes. Par exemple, l’affectation de nom suivante pour un compte de stockage contient un jeu de crochets, trois fonctions, trois jeux de parenthèses, un jeu de guillemets simples et une propriété :

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   Si vous ne fournissez pas la syntaxe correspondante, le modèle produit une valeur très différente de votre intention.

   Lorsque vous recevez ce type d’erreur, examinez attentivement la syntaxe d’expression. Vous pouvez utiliser un éditeur JSON comme [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou [Visual Studio Code](resource-manager-vs-code.md), qui vous signale des erreurs de syntaxe.

- Longueurs de segments incorrectes

   Une autre erreur de modèle non valide se produit lorsque le nom de la ressource n’est pas au format approprié.

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   Une ressource au niveau racine doit avoir un segment de moins dans le nom que dans le type de ressource. Chaque segment se différencie par une barre oblique. Dans l’exemple suivant, le type comporte deux segments et le nom un segment : il s’agit donc d’un **nom valide**.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   Mais l’exemple suivant n’est **pas un nom valide** car il possède le même nombre de segments que le type.

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   Pour les ressources enfants, le type et le nom ont le même nombre de segments. Ce nombre de segments est logique, car le nom complet et le type de l’enfant incluent le nom du parent et le type. Par conséquent, le nom complet a toujours un segment de moins que le type complet.

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   Obtenir des segments valides peut être difficile si des types Resource Manager sont appliqués à plusieurs fournisseurs de ressources. Par exemple, l’installation d’un verrou de ressource sur un site web nécessite un type avec quatre segments. Par conséquent, le nom comporte 3 segments :

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- La copie de l’index n’est pas prévue.

   Vous rencontrez cette erreur **InvalidTemplate** lorsque vous avez appliqué l’élément **copy** à une partie du modèle qui ne prend pas en charge cet élément. Vous pouvez uniquement appliquer l’élément copy à un type de ressource. Vous ne pouvez pas appliquer l’élément copy à une propriété au sein d’un type de ressource. Par exemple, vous appliquez copy à une machine virtuelle, mais ne pouvez pas l’appliquer aux disques du système d’exploitation pour une machine virtuelle. Dans certains cas, vous pouvez convertir une ressource enfant en ressource parent afin de créer une boucle de copie. Pour plus d’informations sur l’utilisation de copy, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

- Le paramètre n’est pas valide.

   Si le modèle spécifie les valeurs autorisées pour un paramètre et que vous fournissez une valeur qui n’est pas une de ces valeurs, vous recevez un message similaire à l’erreur suivante :

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   Vérifiez les valeurs autorisées dans le modèle et fournissez-en une pendant le déploiement.

- Dépendance circulaire détectée

   Vous recevez cette erreur lorsque des ressources dépendant les unes des autres empêchent le déploiement de démarrer. À cause de l’effet combiné d’interdépendances, plusieurs ressources attendent d’autres ressources qui sont elles aussi en attente. Par exemple, la ressource 1 dépend de la ressource 3, la ressource 2 de la ressource 1 et la ressource 3 de la ressource 2. Vous pouvez généralement résoudre ce problème en supprimant les dépendances inutiles. 

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound et ResourceNotFound
Lorsque votre modèle inclut le nom d’une ressource qui ne peut pas être résolue, vous recevez une erreur similaire à la suivante :

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Si vous tentez de déployer la ressource manquante dans le modèle, vérifiez si vous devez ajouter une dépendance. Resource Manager optimise le déploiement en créant des ressources en parallèle, quand cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** de votre modèle pour créer une dépendance avec l’autre ressource. Par exemple, quand vous déployez une application web, le plan App Service doit être présent. Si vous n’avez pas spécifié que l’application web est dépendante du plan App Service, Resource Manager crée les deux ressources en même temps. Vous recevez une erreur indiquant que la ressource du plan App Service est introuvable, car elle n’existe pas encore quand vous tentez de définir une propriété sur l’application web. Vous pouvez éviter cette erreur en définissant la dépendance dans l’application web.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Pour obtenir des conseils sur la résolution des erreurs de dépendance, consultez [Vérifier la séquence de déploiement](#check-deployment-sequence).

Vous pouvez également voir cette erreur lorsque la ressource se trouve dans un groupe de ressources différent de celui faisant l’objet d’un déploiement. Dans ce cas, utilisez la [fonction resourceId](resource-group-template-functions-resource.md#resourceid) pour obtenir le nom complet de la ressource.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

Si vous essayez d’utiliser les fonctions [reference](resource-group-template-functions-resource.md#reference) ou [listKeys](resource-group-template-functions-resource.md#listkeys) avec une ressource qui ne peut pas être résolue, vous recevez l’erreur suivante :

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

Recherchez une expression qui inclut la fonction **reference**. Vérifiez que les valeurs des paramètres sont corrects.

## <a name="parentresourcenotfound"></a>ParentResourceNotFound

Lorsqu’une ressource est parent d’une autre ressource, la ressource parent doit exister avant de créer la ressource enfant. Si elle n’existe pas encore, vous recevez l’erreur suivante :

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

Le nom de la ressource enfant inclut le nom de la ressource parent. Par exemple, une base de données SQL peut être définie de la manière suivante :

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Toutefois, si vous ne spécifiez pas de dépendance sur la ressource parent, la ressource enfant peut être déployée avant cette dernière. Pour corriger cette erreur, incluez une dépendance.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />

## <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists et StorageAccountAlreadyTaken
Pour les comptes de stockage, vous devez fournir un nom pour la ressource qui est unique dans Azure. Si vous ne fournissez pas un nom unique, une erreur de ce type s’affiche :

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Vous pouvez créer un nom unique en concaténant votre nommage avec le résultat de la fonction [uniqueString](resource-group-template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Si vous déployez un compte de stockage portant le même nom qu’un compte de stockage existant dans votre abonnement, mais que vous indiquez un emplacement différent, vous recevez une erreur indiquant que le compte de stockage existe déjà dans un autre emplacement. Supprimez le compte de stockage existant ou indiquez le même emplacement que le compte de stockage existant.

## <a name="accountnameinvalid"></a>AccountNameInvalid
Vous voyez l’erreur **AccountNameInvalid** lorsque vous tentez de donner à un compte de stockage un nom qui inclut des caractères interdits. Ce nom doit comprendre entre 3 et 24 caractères, uniquement des lettres en minuscules et des nombres. La fonction [uniqueString](resource-group-template-functions-string.md#uniquestring) renvoie 13 caractères. Si vous concaténez un préfixe au résultat de la fonction **uniqueString**, fournissez un préfixe de 11 caractères maximum.

## <a name="badrequest"></a>BadRequest

L’état BadRequest peut survenir lorsque vous spécifiez une valeur de propriété non valide. Par exemple, si vous indiquez une valeur de référence (SKU) incorrecte pour un compte de stockage, le déploiement échoue. Pour déterminer les valeurs valides pour la propriété, consultez la [documentation de l’API REST](/rest/api) pour le type de ressource que vous déployez.

<a id="noregisteredproviderfound" />

## <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound et MissingSubscriptionRegistration
Lorsque vous déployez une ressource, vous pouvez recevoir le message et le code d’erreur suivants :

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

Vous pouvez sinon recevoir un message similaire indiquant :

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

Ces erreurs apparaissent pour l’une des trois raisons suivantes :

1. Le fournisseur de ressources n’a pas été inscrit pour votre abonnement.
2. La version de l’API n’est pas prise en charge pour le type de ressource.
3. L’emplacement n’est pas pris en charge pour le type de ressource.

Le message d’erreur doit fournir des suggestions pour les emplacements et les versions d’API pris en charge. Vous pouvez changer votre modèle en l’une des valeurs suggérées. La plupart des fournisseurs sont inscrits automatiquement par le portail Azure ou l’interface de ligne de commande que vous utilisez, mais pas tous. Si vous n’avez pas déjà utilisé un fournisseur de ressources spécifique, vous devrez peut-être inscrire ce dernier. Vous pouvez en savoir plus sur les fournisseurs de ressources via PowerShell ou l’interface de ligne de commande Azure.

**Portail**

Vous pouvez voir l’état de l’inscription et inscrire un espace de noms de fournisseur de ressources par le biais du portail.

1. Sur votre abonnement, sélectionnez **Fournisseurs de ressources**.

   ![sélectionner Fournisseurs de ressources](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. Passez en revue la liste des fournisseurs de ressources et, si nécessaire, sélectionnez le lien **Inscrire** pour inscrire le fournisseur de ressources du type que vous voulez déployer.

   ![répertorier les fournisseurs de ressources](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

Pour afficher l’état de votre inscription, utilisez **Get-AzureRmResourceProvider**.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

Pour inscrire un fournisseur, utilisez **Register-AzureRmResourceProvider** et indiquez le nom du fournisseur de ressources que vous voulez inscrire.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

Pour obtenir les emplacements pris en charge d’un type particulier de ressource, utilisez la commande suivante :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

Pour obtenir les versions d’API prises en charge d’un type particulier de ressource, utilisez la commande suivante :

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**Interface de ligne de commande Azure**

Pour voir si le fournisseur est inscrit, utilisez la commande `azure provider list` .

```azurecli
az provider list
```

Pour inscrire un fournisseur de ressources, utilisez la commande `azure provider register` et indiquez *l’espace de noms* à inscrire.

```azurecli
az provider register --namespace Microsoft.Cdn
```

Pour afficher les emplacements et les versions d’API pris en charge pour un type de ressources, utilisez la commande suivante :

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />

## <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded et OperationNotAllowed
Vous pouvez rencontrer des problèmes quand un déploiement dépasse un quota qui peut concerner entre autres les groupes de ressources, les abonnements ou les comptes. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une machine virtuelle avec plus de cœurs que la quantité autorisée, vous recevez une erreur indiquant que le quota a été dépassé.
Pour obtenir des informations complètes sur les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

Pour examiner les quotas de cœurs de votre abonnement, vous pouvez utiliser la commande `azure vm list-usage` dans l’interface de ligne de commande Azure. L’exemple suivant montre que le quota de cœurs d’un compte d’évaluation gratuit est de 4 :

```azurecli
az vm list-usage --location "South Central US"
```

Résultat :

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

Si vous déployez un modèle qui crée plus de quatre cœurs dans la région ouest des États-Unis, vous obtenez une erreur de déploiement similaire à la suivante :

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Dans PowerShell, vous pouvez également utiliser l’applet de commande **Get-AzureRmVMUsage** .

```powershell
Get-AzureRmVMUsage
```

Résultat :

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

Dans ce cas, vous devez accéder au portail et signaler un problème de support afin d’augmenter votre quota pour la région vers laquelle vous souhaitez procéder au déploiement.

> [!NOTE]
> N’oubliez pas que pour les groupes de ressources, le quota est défini pour chaque région, pas pour tout l’abonnement. Si vous devez déployer 30 cœurs dans l’Ouest des États-Unis, vous devez demander 30 cœurs Resource Manager dans l’Ouest des États-Unis. Si vous devez déployer 30 cœurs dans l’une des régions auxquelles vous avez accès, vous devez demander 30 cœurs Resource Manager dans toutes les régions.
>
>

## <a name="invalidcontentlink"></a>InvalidContentLink
Lorsque vous recevez le message d’erreur suivant :

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

Vous avez probablement tenté d’établir une liaison avec un modèle imbriqué qui n’est pas disponible. Vérifiez l’URI que vous avez indiqué pour le modèle imbriqué. Si le modèle existe dans un compte de stockage, assurez-vous que l’URI est accessible. Vous devrez peut-être valider un jeton SAS. Pour plus d’informations, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

## <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Vous recevez cette erreur lorsque votre abonnement inclut une stratégie de ressource qui empêche une action que vous tentez d’exécuter au cours du déploiement. Dans le message d’erreur, recherchez l’identificateur de la stratégie.

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

Dans **PowerShell**, fournissez cet identificateur de stratégie en tant que paramètre **Id** pour extraire des informations sur la stratégie qui a bloqué votre déploiement.

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

Dans **Interface de ligne de commande Azure**, indiquez le nom de la définition de stratégie :

```azurecli
az policy definition show --name regionPolicyAssignment
```

Pour plus d’informations, consultez les articles suivants :

- [Erreur RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
- [Utilisez le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

## <a name="authorization-failed"></a>Échec de l’autorisation
Vous pouvez recevoir une erreur lors du déploiement, car le compte ou le principal du service qui tente de déployer les ressources n’a pas accès à ces actions. Azure Active Directory permet à votre administrateur ou à vous-même de contrôler très précisément quelles identités peuvent accéder à quelles ressources. Par exemple, si votre compte est affecté au rôle de lecteur, vous ne pouvez pas créer de ressources. Dans ce cas, vous voyez un message d’erreur indiquant que l’autorisation a échoué.

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la rubrique [Contrôle d’accès en fonction du rôle d’Azure](../active-directory/role-based-access-control-configure.md).


## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
* Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](resource-manager-deployment-operations.md).

