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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e2e59da29897a40f0fe538d6fe8063ae5edbaccd
ms.openlocfilehash: 4dd4e54f3e2514570ff5cbffcb926f274491cb65


---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager
Cette rubrique décrit comment résoudre certaines erreurs courantes liées au déploiement d’Azure que vous pouvez rencontrer.

## <a name="two-types-of-errors"></a>Deux types d’erreur
Il existe deux types d’erreurs que vous pouvez rencontrer :

* des erreurs de validation
* des erreurs de déploiement

L’illustration suivante montre le journal d’activité d’un abonnement. Il existe trois opérations exécutées dans deux déploiements. Dans le premier déploiement, le modèle a passé la validation, mais a échoué lors de la création de ressources (**déploiements d’écriture**). Dans le deuxième déploiement, la validation du modèle a échoué et n’a pas continué jusqu’aux **déploiements d’écriture**.

![afficher le code d'erreur](./media/resource-manager-common-deployment-errors/show-activity-log.png)

Les erreurs de validation sont liées à des scénarios pouvant être identifiés comme source de problème. Ces erreurs de validation incluent notamment les erreurs de syntaxe dans votre modèle ou le déploiement de ressources entraînant un dépassement des quotas d’abonnement. Les erreurs de déploiement sont liées aux événements se produisant lors du déploiement. Par exemple, une erreur de déploiement peut survenir si vous tentez d’accéder à une ressource qui est déployée en parallèle.

Les deux types d’erreurs retournent un code d’erreur qui vous permet de résoudre les problèmes liés au déploiement. Les deux types d’erreurs apparaissent dans le journal d’activité. Toutefois, les erreurs de validation n’apparaissent pas dans l’historique de votre déploiement, car le déploiement n’a jamais démarré.


## <a name="error-codes"></a>Codes d’erreur
Les erreurs de déploiement retournent le code **DeploymentFailed**. Toutefois, ce code d’erreur est une erreur de déploiement général. Le code d’erreur qui vous permet en fait de résoudre le problème se trouve généralement à un niveau en dessous de cette erreur. L’illustration suivante montre le code d’erreur **RequestDisallowedByPolicy** qui se trouve sous l’erreur de déploiement.

![afficher le code d'erreur](./media/resource-manager-common-deployment-errors/error-code.png)

Les codes d’erreur suivants sont décrits dans cette rubrique :

* [AccountNameInvalid](#accountnameinvalid)
* [Échec de l’autorisation](#authorization-failed)
* [BadRequest](#badrequest)
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

### <a name="invalidtemplate"></a>InvalidTemplate
Cette erreur peut résulter de différents types d’erreurs.

- Erreur de syntaxe

   Si vous recevez un message d’erreur qui indique que la validation du modèle a échoué, vous avez peut-être un problème de syntaxe dans votre modèle.

       Code=InvalidTemplate
       Message=Deployment template validation failed

   Cette erreur est facile à commettre car les expressions de modèle peuvent être complexes. Par exemple, l’affectation de nom suivante pour un compte de stockage contient un jeu de crochets, trois fonctions, trois jeux de parenthèses, un jeu de guillemets simples et une propriété :

       "name": "[concat('storage', uniqueString(resourceGroup().id))]",

   Si vous ne fournissez pas la syntaxe correspondante, le modèle produit une valeur très différente de votre intention.

   Lorsque vous recevez ce type d’erreur, examinez attentivement la syntaxe d’expression. Vous pouvez utiliser un éditeur JSON comme [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) ou [Visual Studio Code](resource-manager-vs-code.md), qui vous signale des erreurs de syntaxe.

- Longueurs de segments incorrectes

   Une autre erreur de modèle non valide se produit lorsque le nom de la ressource n’est pas au format approprié.

       Code=InvalidTemplate
       Message=Deployment template validation failed: 'The template resource {resource-name}'
       for type {resource-type} has incorrect segment lengths.

   Une ressource au niveau racine doit avoir un segment de moins dans le nom que dans le type de ressource. Chaque segment se différencie par une barre oblique. Dans l’exemple suivant, le type comporte deux segments et le nom un segment : il s’agit donc d’un **nom valide**.

       {
         "type": "Microsoft.Web/serverfarms",
         "name": "myHostingPlanName",
         ...
       }

   Mais l’exemple suivant n’est **pas un nom valide** car il possède le même nombre de segments que le type.

       {
         "type": "Microsoft.Web/serverfarms",
         "name": "appPlan/myHostingPlanName",
         ...
       }

   Pour les ressources enfants, le type et le nom ont le même nombre de segments. Ce nombre de segments est logique, car le nom complet et le type de l’enfant incluent le nom du parent et le type. Par conséquent, le nom complet a toujours un segment de moins que le type complet.

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

   Obtenir des segments valides peut être difficile si des types Resource Manager sont appliqués à plusieurs fournisseurs de ressources. Par exemple, l’installation d’un verrou de ressource sur un site web nécessite un type avec quatre segments. Par conséquent, le nom comporte 3 segments :

       {
           "type": "Microsoft.Web/sites/providers/locks",
           "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
           ...
       }

- La copie de l’index n’est pas prévue.

   Vous rencontrez cette erreur **InvalidTemplate** lorsque vous avez appliqué l’élément **copy** à une partie du modèle qui ne prend pas en charge cet élément. Vous pouvez uniquement appliquer l’élément copy à un type de ressource. Vous ne pouvez pas appliquer l’élément copy à une propriété au sein d’un type de ressource. Par exemple, vous appliquez copy à une machine virtuelle, mais ne pouvez pas l’appliquer aux disques du système d’exploitation pour une machine virtuelle. Dans certains cas, vous pouvez convertir une ressource enfant en ressource parent afin de créer une boucle de copie. Pour plus d’informations sur l’utilisation de copy, voir [Création de plusieurs instances de ressources dans Azure Resource Manager](resource-group-create-multiple.md).

- Le paramètre n’est pas valide.

   Si le modèle spécifie les valeurs autorisées pour un paramètre et que vous fournissez une valeur qui n’est pas une de ces valeurs, vous recevez un message similaire à l’erreur suivante :

       Code=InvalidTemplate;
       Message=Deployment template validation failed: 'The provided value {parameter value}
       for the template parameter {parameter name} is not valid. The parameter value is not
       part of the allowed values

   Vérifiez les valeurs autorisées dans le modèle et fournissez-en une pendant le déploiement.

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound et ResourceNotFound
Lorsque votre modèle inclut le nom d’une ressource qui ne peut pas être résolue, vous recevez une erreur similaire à la suivante :

    Code=NotFound;
    Message=Cannot find ServerFarm with name exampleplan.

Si vous tentez de déployer la ressource manquante dans le modèle, vérifiez si vous devez ajouter une dépendance. Resource Manager optimise le déploiement en créant des ressources en parallèle, quand cela est possible. Si une ressource doit être déployée après une autre ressource, vous devez utiliser l’élément **dependsOn** de votre modèle pour créer une dépendance avec l’autre ressource. Par exemple, quand vous déployez une application web, le plan App Service doit être présent. Si vous n’avez pas spécifié que l’application web est dépendante du plan App Service, Resource Manager crée les deux ressources en même temps. Vous recevez une erreur indiquant que la ressource du plan App Service est introuvable, car elle n’existe pas encore quand vous tentez de définir une propriété sur l’application web. Vous pouvez éviter cette erreur en définissant la dépendance dans l’application web.

    {
      "apiVersion": "2015-08-01",
      "type": "Microsoft.Web/sites",
      ...
      "dependsOn": [
        "[variables('hostingPlanName')]"
      ],
      ...
    }

Vous pouvez également voir cette erreur lorsque la ressource se trouve dans un groupe de ressources différent de celui faisant l’objet d’un déploiement. Dans ce cas, utilisez la [fonction resourceId](resource-group-template-functions.md#resourceid) pour obtenir le nom complet de la ressource.

    "properties": {
        "name": "[parameters('siteName')]",
        "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
    }

Si vous essayez d’utiliser les fonctions [reference](resource-group-template-functions.md#reference) ou [listKeys](resource-group-template-functions.md#listkeys) avec une ressource qui ne peut pas être résolue, vous recevez l’erreur suivante :

    Code=ResourceNotFound;
    Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
    group {resource group name} was not found.

Recherchez une expression qui inclut la fonction **reference**. Vérifiez que les valeurs des paramètres sont corrects.

### <a name="parentresourcenotfound"></a>ParentResourceNotFound

Lorsqu’une ressource est parent d’une autre ressource, la ressource parent doit exister avant de créer la ressource enfant. Si elle n’existe pas encore, vous recevez l’erreur suivante :

     Code=ParentResourceNotFound;
     Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."

Le nom de la ressource enfant inclut le nom de la ressource parent. Par exemple, une base de données SQL peut être définie de la manière suivante :

    {
      "type": "Microsoft.Sql/servers/databases",
      "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
      ...

Toutefois, si vous ne spécifiez pas de dépendance sur la ressource parent, la ressource enfant peut être déployée avant cette dernière. Pour corriger cette erreur, incluez une dépendance.

    "dependsOn": [
        "[variables('databaseServerName')]"
    ]

<a id="storagenamenotunique" />
### <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists et StorageAccountAlreadyTaken
Pour les comptes de stockage, vous devez fournir un nom pour la ressource qui est unique dans Azure. Si vous ne fournissez pas un nom unique, une erreur de ce type s’affiche :

    Code=StorageAccountAlreadyTaken
    Message=The storage account named mystorage is already taken.

Vous pouvez créer un nom unique en concaténant votre nommage avec le résultat de la fonction [uniqueString](resource-group-template-functions.md#uniquestring) .

    "name": "[concat('contosostorage', uniqueString(resourceGroup().id))]",
    "type": "Microsoft.Storage/storageAccounts",

Si vous déployez un compte de stockage portant le même nom qu’un compte de stockage existant dans votre abonnement, mais que vous indiquez un emplacement différent, vous recevez une erreur indiquant que le compte de stockage existe déjà dans un autre emplacement. Supprimez le compte de stockage existant ou indiquez le même emplacement que le compte de stockage existant.

### <a name="accountnameinvalid"></a>AccountNameInvalid
Vous voyez l’erreur **AccountNameInvalid** lorsque vous tentez de donner à un compte de stockage un nom qui inclut des caractères interdits. Ce nom doit comprendre entre 3 et 24 caractères, uniquement des lettres en minuscules et des nombres.

### <a name="badrequest"></a>BadRequest

L’état BadRequest peut survenir lorsque vous spécifiez une valeur de propriété non valide. Par exemple, si vous indiquez une valeur de référence (SKU) incorrecte pour un compte de stockage, le déploiement échoue. 

<a id="noregisteredproviderfound" />
### <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound et MissingSubscriptionRegistration
Lorsque vous déployez une ressource, vous pouvez recevoir le message et le code d’erreur suivants :

    Code: NoRegisteredProviderFound
    Message: No registered resource provider found for location {ocation}
    and API version {api-version} for type {resource-type}.

Vous pouvez sinon recevoir un message similaire indiquant :

    Code: MissingSubscriptionRegistration
    Message: The subscription is not registered to use namespace {resource-provider-namespace}

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

    Get-AzureRmResourceProvider -ListAvailable

Pour inscrire un fournisseur, utilisez **Register-AzureRmResourceProvider** et indiquez le nom du fournisseur de ressources que vous voulez inscrire.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn

Pour obtenir les emplacements pris en charge d’un type particulier de ressource, utilisez la commande suivante :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

Pour obtenir les versions d’API prises en charge d’un type particulier de ressource, utilisez la commande suivante :

    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions

**Interface de ligne de commande Azure**

Pour voir si le fournisseur est inscrit, utilisez la commande `azure provider list` .

    azure provider list

Pour inscrire un fournisseur de ressources, utilisez la commande `azure provider register` et indiquez *l’espace de noms* à inscrire.

    azure provider register Microsoft.Cdn

Pour afficher les emplacements et les versions d’API pris en charge pour un fournisseur de ressources, utilisez la commande suivante :

    azure provider show -n Microsoft.Compute --json > compute.json

<a id="quotaexceeded" />
### <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded et OperationNotAllowed
Vous pouvez rencontrer des problèmes quand un déploiement dépasse un quota qui peut concerner entre autres les groupes de ressources, les abonnements ou les comptes. Par exemple, votre abonnement peut être configuré pour limiter le nombre de cœurs dans une région. Si vous tentez de déployer une machine virtuelle avec plus de cœurs que la quantité autorisée, vous recevez une erreur indiquant que le quota a été dépassé.
Pour obtenir des informations complètes sur les quotas, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

Pour examiner les quotas de cœurs de votre abonnement, vous pouvez utiliser la commande `azure vm list-usage` dans l’interface de ligne de commande Azure. L’exemple suivant montre que le quota de cœurs d’un compte d’évaluation gratuit est de 4 :

    azure vm list-usage

Résultat :

    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4
    info:    vm list-usage command OK

Si vous déployez un modèle qui crée plus de quatre cœurs dans la région ouest des États-Unis, vous obtenez une erreur de déploiement similaire à la suivante :

    Code=OperationNotAllowed
    Message=Operation results in exceeding quota limits of Core.
    Maximum allowed: 4, Current in use: 4, Additional requested: 2.

Dans PowerShell, vous pouvez également utiliser l’applet de commande **Get-AzureRmVMUsage** .

    Get-AzureRmVMUsage

Résultat :

    ...
    CurrentValue : 0
    Limit        : 4
    Name         : {
                     "value": "cores",
                     "localizedValue": "Total Regional Cores"
                   }
    Unit         : null
    ...

Dans ce cas, vous devez accéder au portail et signaler un problème de support afin d’augmenter votre quota pour la région vers laquelle vous souhaitez procéder au déploiement.

> [!NOTE]
> N’oubliez pas que pour les groupes de ressources, le quota est défini pour chaque région, pas pour tout l’abonnement. Si vous devez déployer 30 cœurs dans l’Ouest des États-Unis, vous devez demander 30 cœurs Resource Manager dans l’Ouest des États-Unis. Si vous devez déployer 30 cœurs dans l’une des régions auxquelles vous avez accès, vous devez demander 30 cœurs Resource Manager dans toutes les régions.
>
>

### <a name="invalidcontentlink"></a>InvalidContentLink
Lorsque vous recevez le message d’erreur suivant :

    Code=InvalidContentLink
    Message=Unable to download deployment content from ...

Vous avez probablement tenté d’établir une liaison avec un modèle imbriqué qui n’est pas disponible. Vérifiez l’URI que vous avez indiqué pour le modèle imbriqué. Si le modèle existe dans un compte de stockage, assurez-vous que l’URI est accessible. Vous devrez peut-être valider un jeton SAS. Pour plus d’informations, consultez [Utilisation de modèles liés avec Azure Resource Manager](resource-group-linked-templates.md).

### <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
Vous recevez cette erreur lorsque votre abonnement inclut une stratégie de ressource qui empêche une action que vous tentez d’exécuter au cours du déploiement. Dans le message d’erreur, recherchez l’identificateur de la stratégie.

    Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'

Dans **PowerShell**, fournissez cet identificateur de stratégie en tant que paramètre **Id** pour extraire des informations sur la stratégie qui a bloqué votre déploiement.

    (Get-AzureRmPolicyAssignment -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json

Dans **Interface de ligne de commande Azure**, indiquez le nom de la définition de stratégie :

    azure policy definition show regionPolicyDefinition --json

Pour plus d’informations sur les stratégies, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](resource-manager-policy.md).

### <a name="authorization-failed"></a>Échec de l’autorisation
Vous pouvez recevoir une erreur lors du déploiement, car le compte ou le principal du service qui tente de déployer les ressources n’a pas accès à ces actions. Azure Active Directory permet à votre administrateur ou à vous-même de contrôler très précisément quelles identités peuvent accéder à quelles ressources. Par exemple, si votre compte est affecté au rôle de lecteur, vous ne pouvez pas créer de ressources. Dans ce cas, vous voyez un message d’erreur indiquant que l’autorisation a échoué.

Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez la rubrique [Contrôle d’accès en fonction du rôle d’Azure](../active-directory/role-based-access-control-configure.md).

### <a name="skunotavailable"></a>SkuNotAvailable

Lors du déploiement d’une ressource (généralement une machine virtuelle), vous pouvez recevoir le message et le code d’erreur suivants :

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

Vous recevez cette erreur lorsque la ressource de référence (SKU) que vous avez sélectionnée (par exemple, la taille de la machine virtuelle) n’est pas disponible pour l’emplacement que vous avez sélectionné. Deux options s’offrent à vous pour résoudre ce problème :

- Connectez-vous au portail et ajoutez une nouvelle ressource à l’aide de l’interface utilisateur. Lorsque vous définissez les valeurs, vous voyez les références (SKU) disponibles pour cette ressource. Vous n’avez pas besoin de terminer le déploiement.

    ![Références (SKU) disponibles](./media/resource-manager-common-deployment-errors/view-sku.png)

- Si vous ne parvenez pas à trouver une référence appropriée dans cette région ou une autre qui réponde aux besoins de votre entreprise, contactez le [support Azure](https://portal.azure.com/#create/Microsoft.Support).

## <a name="troubleshooting-tricks-and-tips"></a>Conseils et astuces de dépannage

### <a name="enable-debug-logging"></a>Activer l’enregistrement du débogage
Vous pouvez découvrir des informations précieuses sur le traitement de votre déploiement en journalisant la requête, la réponse ou les deux.

- PowerShell

   Dans PowerShell, définissez le paramètre **DeploymentDebugLogLevel** pour All, ResponseContent ou RequestContent.

       New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All

   Examinez le contenu de la requête avec l’applet de commande suivant :

       (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json

   Ou examinez la réponse avec :

       (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json

   Ces informations peuvent vous aider à déterminer si une valeur dans le modèle n’est pas définie correctement.

- Interface de ligne de commande Azure

   Dans l’interface de ligne de commande Azure, définissez le paramètre **--debug-setting** sur All, ResponseContent ou RequestContent.

       azure group deployment create --debug-setting All -f c:\Azure\Templates\storage.json -g examplegroup -n ExampleDeployment

   Examinez les éléments suivants du contenu de la demande et de la réponse journalisé :

       azure group deployment operation list --resource-group examplegroup --name ExampleDeployment --json

   Ces informations peuvent vous aider à déterminer si une valeur dans le modèle n’est pas définie correctement.

- Modèle imbriqué

   Pour enregistrer les informations de débogage pour un modèle imbriqué, utilisez l’élément **debugSetting**.

       {
           "apiVersion": "2016-09-01",
           "name": "nestedTemplate",
           "type": "Microsoft.Resources/deployments",
           "properties": {
               "mode": "Incremental",
               "templateLink": {
                   "uri": "{template-uri}",
                   "contentVersion": "1.0.0.0"
               },
               "debugSetting": {
                  "detailLevel": "requestContent, responseContent"
               }
           }
       }


### <a name="create-a-troubleshooting-template"></a>Création d’un modèle de résolution des problèmes
Dans certains cas, le moyen le plus simple pour résoudre les problèmes de votre modèle consiste à tester des parties de celui-ci. Vous pouvez créer un modèle simplifié qui vous permet de vous concentrer sur la partie que vous pensez erronée. Par exemple, supposons que vous recevez une erreur lorsque vous référencez une ressource. Au lieu de traiter un modèle complet, créez un modèle retournant la partie qui peut être à l’origine de votre problème. Il peut vous aider à déterminer si vous transmettez les paramètres appropriés à l’aide de fonctions de modèle et si vous obtenez la ressource que vous attendez.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageName": {
            "type": "string"
        },
        "storageResourceGroup": {
            "type": "string"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "exampleOutput": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
            "type" : "object"
        }
      }
    }

Si vous rencontrez des erreurs de déploiement que vous pensez liées à une mauvaise définition des dépendances, vous pouvez tester votre modèle en le divisant en modèles plus simples. Commencez par créer un modèle déployant une seule ressource (comme un serveur SQL Server). Lorsque vous êtes sûr que cette ressource est correctement définie, ajoutez une ressource qui en dépend (par exemple une base de données SQL). Une fois ces deux ressources correctement définies, ajoutez les autres ressources dépendantes (telles que les stratégies d’audit). Supprimez le groupe de ressources entre chaque déploiement de test afin de tester correctement les dépendances. 

### <a name="check-deployment-sequence"></a>Vérifier la séquence de déploiement

De nombreuses erreurs de déploiement se produisent lorsque des ressources sont déployées selon une séquence inattendue. Ces erreurs surviennent lorsque les dépendances ne sont pas définies correctement. Une ressource tente d’utiliser la valeur d’une autre ressource, mais cette autre ressource n’existe pas encore. Pour afficher l’ordre des opérations de déploiement :

1. Sélectionnez l’historique de déploiement de votre groupe de ressources.

   ![sélectionner l’historique de déploiement](./media/resource-manager-common-deployment-errors/select-deployment.png)

2. Sélectionnez un déploiement dans l’historique, puis sélectionnez **Événements**.

   ![sélectionner les événements de déploiement](./media/resource-manager-common-deployment-errors/select-deployment-events.png)

3. Examinez la séquence d’événements de chaque ressource. Faites attention à l’état de chaque opération. Par exemple, l’image suivante montre trois comptes de stockage déployés en parallèle. Remarquez que les trois comptes de stockage sont démarrés en même temps.

   ![déploiement en parallèle](./media/resource-manager-common-deployment-errors/deployment-events-parallel.png)

   L’image suivante montre trois comptes de stockage non déployés en parallèle. Le deuxième compte de stockage est marqué comme dépendant du premier compte de stockage, et le troisième dépend du deuxième compte de stockage. Par conséquent, le premier compte de stockage est démarré, accepté et terminé avant que le suivant ne démarre.

   ![déploiement séquentiel](./media/resource-manager-common-deployment-errors/deployment-events-sequence.png)

Examinez vos événements de déploiement pour déterminer si une ressource est démarrée plus tôt que prévu. Dans ce cas, vérifiez les dépendances de cette ressource.

## <a name="troubleshooting-other-services"></a>Résolution des problèmes d’autres services
Si les codes d’erreur de déploiement précédents ne vous permettent pas de résoudre votre problème, vous pouvez rechercher des conseils de dépannage plus détaillés pour le service Azure présentant une erreur.

Le tableau suivant répertorie les rubriques de dépannage des machines virtuelles.

| Erreur | Articles |
| --- | --- |
| Erreurs d’extension de script personnalisé |[Échecs d’extension de machine virtuelle Windows](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou<br />[Échecs d’extension de machine virtuelle Linux](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erreurs de configuration d’image de système d’exploitation |[Erreurs liées aux nouvelles machines virtuelles Windows](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou<br />[Erreurs liées au nouvelles machines virtuelles Linux](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Échecs d’allocation |[Échecs d’allocation de machine virtuelle Windows](../virtual-machines/virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou<br />[Échecs d’allocation de machine virtuelle Linux](../virtual-machines/virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erreurs de Secure Shell (SSH) lors de la tentative de connexion |[Connexions Secure Shell à la machine virtuelle Linux](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erreurs de connexion à l’application s’exécutant sur la machine virtuelle |[Application s’exécutant sur une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />ou<br />[Application s’exécutant sur une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Erreurs de connexion Bureau à distance |[Connexions Bureau à distance avec une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Erreurs de connexion résolues par un nouveau déploiement |[Redéployer une machine virtuelle vers un nouveau nœud Azure](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Erreurs de service cloud |[Problèmes de déploiement de service cloud](../cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

Le tableau suivant répertorie les rubriques de dépannage des autres services Azure. Il présente des problèmes liés au déploiement ou à la configuration des ressources. Si vous avez besoin d’aide pour résoudre les problèmes d’exécution d’une ressource, consultez la documentation du service Azure concerné.

| de diffusion en continu | Article |
| --- | --- |
| Automation |[Conseils de dépannage pour les erreurs courantes dans Azure Automation](../automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack |[Dépannage de Microsoft Azure Stack](../azure-stack/azure-stack-troubleshooting.md) |
| Data Factory |[Résolution des problèmes liés à Data Factory](../data-factory/data-factory-troubleshoot.md) |
| Service Fabric |[Résoudre les problèmes courants rencontrés pendant le déploiement de services dans Azure Service Fabric](../service-fabric/service-fabric-diagnostics-troubleshoot-common-scenarios.md) |
| Site Recovery |[Surveiller et résoudre les problèmes de protection pour les machines virtuelles et les serveurs physiques](../site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Storage |[Analyser, diagnostiquer et dépanner Microsoft Azure Storage](../storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple |[Résolution des problèmes de déploiement d’un appareil StorSimple](../storsimple/storsimple-troubleshoot-deployment.md) |
| Base de données SQL |[Résoudre des problèmes de connexion à la base de données SQL Azure](../sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse |[Résolution des problèmes d’Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
* Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](resource-manager-troubleshoot-deployments-portal.md).



<!--HONumber=Dec16_HO3-->


