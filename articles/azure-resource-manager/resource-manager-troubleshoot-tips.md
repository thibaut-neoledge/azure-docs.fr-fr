---
title: "Comprendre les erreurs de déploiement Azure | Microsoft Docs"
description: "Décrit comment en savoir plus sur les erreurs de déploiement Azure."
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "erreur de déploiement, déploiement Azure, déployer dans azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---

# <a name="understand-azure-deployment-errors"></a>Comprendre les erreurs de déploiement Azure
Cette rubrique décrit les erreurs de déploiement et vous explique comment obtenir plus d’informations sur une erreur. Pour savoir comment résoudre les erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="two-types-of-errors"></a>Deux types d’erreur
Il existe deux types d’erreurs que vous pouvez rencontrer :

* des erreurs de validation
* des erreurs de déploiement

L’illustration suivante montre le journal d’activité d’un abonnement. Deux déploiements sont représentés. Lors de l’un des déploiements, la validation du modèle a échoué (**Validate**) et le déploiement n’a pas été effectué. Dans l’autre déploiement, le modèle a passé la validation, mais a échoué lors de la création de ressources (**Write Deployments**). 

![afficher le code d'erreur](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

Les erreurs de validation sont liées à des scénarios pouvant être identifiés avant le déploiement. Elles incluent notamment les erreurs de syntaxe dans votre modèle ou le déploiement de ressources entraînant un dépassement des quotas d’abonnement. Les erreurs de déploiement sont liées aux événements se produisant lors du déploiement. Elles incluent la tentative d’accès à une ressource qui est déployée en parallèle.

Les deux types d’erreurs retournent un code d’erreur qui vous permet de résoudre les problèmes liés au déploiement. Les deux types d’erreurs apparaissent dans le [journal d’activité](resource-group-audit.md). Toutefois, les erreurs de validation n’apparaissent pas dans l’historique de votre déploiement, car le déploiement n’a jamais démarré.

## <a name="determine-error-code"></a>Déterminer le code d’erreur

Vous pouvez en savoir plus sur une erreur en examinant le message et le code d’erreur. L’article [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md) répertorie les différentes résolutions par code d’erreur. Cette rubrique explique comment utiliser le portail Azure pour comprendre le code d’erreur.

### <a name="validation-errors"></a>Erreurs de validation

Lors d’un déploiement via le portail, une erreur de validation s’affiche après l’envoi de vos valeurs.

![afficher l’erreur de validation dans le portail](./media/resource-manager-troubleshoot-tips/validation-error.png)

Sélectionnez le message pour obtenir plus d’informations. Dans l’image suivante, vous pouvez voir une erreur **InvalidTemplateDeployment** et un message qui indique qu’une stratégie a bloqué le déploiement.

![afficher les détails de validation](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>Erreurs de déploiement

Lorsque l’opération passe la validation, mais qu’elle échoue pendant le déploiement, l’erreur s’affiche dans les notifications. Sélectionnez la notification.

![erreur de notification](./media/resource-manager-troubleshoot-tips/notification.png)

Vous voyez plus d’informations sur le déploiement. Sélectionnez l’option pour rechercher plus d’informations sur l’erreur.

![échec du déploiement](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

Vous voyez le message et les codes d’erreur. Notez qu’il y a deux codes d’erreur. Le premier code d’erreur (**DeploymentFailed**) est un code d’erreur général qui ne fournit pas les détails dont vous avez besoin pour résoudre l’erreur. Le deuxième code d’erreur (**StorageAccountNotFound**) fournit les détails dont vous avez besoin. 

![détails de l’erreur](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>Activer l’enregistrement du débogage
Vous avez parfois besoin de plus d’informations sur la demande et la réponse pour connaître la cause du problème. Avec PowerShell ou l’interface de ligne de commande Azure, vous pouvez demander la journalisation d’informations supplémentaires lors d’un déploiement.

- PowerShell

   Dans PowerShell, définissez le paramètre **DeploymentDebugLogLevel** pour All, ResponseContent ou RequestContent.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   Examinez le contenu de la requête avec l’applet de commande suivant :

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   Ou examinez la réponse avec :

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   Ces informations peuvent vous aider à déterminer si une valeur dans le modèle n’est pas définie correctement.

- Interface de ligne de commande Azure

   Examinez les opérations de déploiement avec la commande suivante :

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- Modèle imbriqué

   Pour enregistrer les informations de débogage pour un modèle imbriqué, utilisez l’élément **debugSetting**.

  ```json
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
  ```


## <a name="create-a-troubleshooting-template"></a>Création d’un modèle de résolution des problèmes
Dans certains cas, le moyen le plus simple pour résoudre les problèmes de votre modèle consiste à tester des parties de celui-ci. Vous pouvez créer un modèle simplifié qui vous permet de vous concentrer sur la partie que vous pensez erronée. Par exemple, supposons que vous recevez une erreur lorsque vous référencez une ressource. Au lieu de traiter un modèle complet, créez un modèle retournant la partie qui peut être à l’origine de votre problème. Il peut vous aider à déterminer si vous transmettez les paramètres appropriés à l’aide de fonctions de modèle et si vous obtenez la ressource que vous attendez.

```json
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
```

Si vous rencontrez des erreurs de déploiement que vous pensez liées à une mauvaise définition des dépendances, vous pouvez tester votre modèle en le divisant en modèles plus simples. Commencez par créer un modèle déployant une seule ressource (comme un serveur SQL Server). Lorsque vous êtes sûr que cette ressource est correctement définie, ajoutez une ressource qui en dépend (par exemple une base de données SQL). Une fois ces deux ressources correctement définies, ajoutez les autres ressources dépendantes (telles que les stratégies d’audit). Supprimez le groupe de ressources entre chaque déploiement de test afin de tester correctement les dépendances. 

## <a name="check-deployment-sequence"></a>Vérifier la séquence de déploiement

De nombreuses erreurs de déploiement se produisent lorsque des ressources sont déployées selon une séquence inattendue. Ces erreurs surviennent lorsque les dépendances ne sont pas définies correctement. Lorsqu’il vous manque une dépendance requise, une ressource tente d’utiliser la valeur d’une autre ressource, mais cette autre ressource n’existe pas encore. Vous obtenez une erreur indiquant qu’une ressource est introuvable. Vous pouvez rencontrer ce type d’erreur par intermittence, car la durée du déploiement peut varier pour chaque ressource. Par exemple, votre première tentative pour déployer vos ressources réussit, car le déploiement d’une ressource requise se termine par hasard à temps. Cependant, votre seconde tentative échoue, car le déploiement de la ressource requise ne se termine pas à temps. 

Il vaut mieux éviter de définir des dépendances qui ne sont pas nécessaires. Lorsque vous avez des dépendances inutiles, vous prolongez la durée du déploiement en empêchant les ressources qui ne sont pas interdépendantes d’être déployées en parallèle. Par ailleurs, il se peut que créiez des dépendances circulaires qui bloquent le déploiement. La fonction [reference](resource-group-template-functions-resource.md#reference) crée une dépendance implicite envers la ressource référencée, lorsque cette ressource est déployée dans le même modèle. Il se peut donc que vous ayez des dépendances en plus des dépendances spécifiées dans la propriété **dependsOn**. La fonction [resourceId](resource-group-template-functions-resource.md#resourceid) ne crée pas de dépendance implicite ou ne valide pas l’existence de la ressource.

Lorsque vous rencontrez des problèmes de dépendance, vous devez déterminer l’ordre de déploiement des ressources. Pour afficher l’ordre des opérations de déploiement :

1. Sélectionnez l’historique de déploiement de votre groupe de ressources.

   ![sélectionner l’historique de déploiement](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. Sélectionnez un déploiement dans l’historique, puis sélectionnez **Événements**.

   ![sélectionner les événements de déploiement](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. Examinez la séquence d’événements de chaque ressource. Faites attention à l’état de chaque opération. Par exemple, l’image suivante montre trois comptes de stockage déployés en parallèle. Remarquez que les trois comptes de stockage sont démarrés en même temps.

   ![déploiement en parallèle](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   L’image suivante montre trois comptes de stockage non déployés en parallèle. Le deuxième compte de stockage dépend du premier compte de stockage, et le troisième du deuxième. Par conséquent, le premier compte de stockage est démarré, accepté et terminé avant que le suivant ne démarre.

   ![déploiement séquentiel](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

Même dans des scénarios plus compliqués, vous pouvez utiliser la même technique pour identifier le moment où le déploiement est démarré et terminé pour chaque ressource. Examinez vos événements de déploiement pour déterminer si la séquence est bien celle prévue. Si ce n’est pas le cas, réévaluez les dépendances de cette ressource.

Resource Manager identifie les dépendances circulaires lors de la validation du modèle. Il renvoie un message d’erreur indiquant spécifiquement qu’il existe une dépendance circulaire. Pour résoudre une dépendance circulaire :

1. Dans votre modèle, recherchez la ressource identifiée dans la dépendance circulaire. 
2. Pour cette ressource, examinez la propriété **dependsOn** et les utilisations de la fonction **référence** pour voir de quelles ressources elle dépend. 
3. Examinez ces ressources pour voir de quelles ressources elles dépendent. Suivez les dépendances jusqu’à ce que vous trouviez une ressource qui dépend de la ressource d’origine.
5. Pour les ressources impliquées dans la dépendance circulaire, examinez attentivement toutes les utilisations de la propriété **dependsOn** pour identifier les dépendances qui ne sont pas nécessaires. Supprimer ces dépendances. Si vous n’êtes pas certain qu’une dépendance soit nécessaire, essayez de la supprimer. 
6. Redéployez le modèle.

La suppression de valeurs de la propriété **dependsOn** peut provoquer des erreurs lors du déploiement du modèle. Si vous rencontrez une erreur, rajoutez la dépendance dans le modèle. 

Si cette approche ne résout pas la dépendance circulaire, essayez de déplacer une partie de votre logique de déploiement dans les ressources enfants (par exemple, les extensions ou les paramètres de configuration). Configurez ces ressources enfants pour qu’elles se déploient après les ressources impliquées dans la dépendance circulaire. Par exemple, supposons que vous déployiez deux machines virtuelles, mais que vous deviez définir sur chacune d’elles des propriétés faisant référence les unes aux autres. Vous pouvez les déployer dans l’ordre suivant :

1. Machine virtuelle 1
2. Machine virtuelle 2
3. L’extension sur la machine virtuelle 1 dépend des machines virtuelles 1 et 2. L’extension définit sur la machine virtuelle 1 des valeurs qu’elle obtient de la machine virtuelle 2.
4. L’extension sur la machine virtuelle 2 dépend des machines virtuelles 1 et 2. L’extension définit sur la machine virtuelle 2 des valeurs qu’elle obtient de la machine virtuelle 1.

Cette approche fonctionne aussi pour les applications App Service. Essayez de déplacer des valeurs de configuration dans une ressource enfant de la ressource d’application. Vous pouvez déployer deux applications web dans l’ordre suivant :

1. Application web 1
2. Application web 2
3. La configuration de l’application web 1 dépend des applications web 1 et 2. Elle contient des paramètres d’application avec les valeurs de l’application web 2.
4. La configuration de l’application web 2 dépend des applications web 1 et 2. Elle contient des paramètres d’application avec les valeurs de l’application web 1.


## <a name="next-steps"></a>Étapes suivantes
* Pour savoir comment résoudre les erreurs courantes de déploiement, consultez la page [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Pour en savoir plus sur les actions d’audit, consultez [Opérations d’audit avec Resource Manager](resource-group-audit.md).
* Pour en savoir plus sur les actions visant à déterminer les erreurs au cours du déploiement, consultez [Voir les opérations de déploiement](resource-manager-deployment-operations.md).

