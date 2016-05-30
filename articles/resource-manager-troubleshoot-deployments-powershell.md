<properties
   pageTitle="Résolution des problèmes liés aux déploiements avec Azure PowerShell | Microsoft Azure"
   description="Décrit comment utiliser Azure PowerShell pour détecter et résoudre les problèmes de déploiement du Gestionnaire de ressources."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Résolution des problèmes liés aux déploiements de groupes de ressources avec Azure PowerShell

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interface de ligne de commande Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Si vous avez reçu une erreur lors du déploiement des ressources sur Azure, vous devez résoudre le problème. Azure PowerShell fournit des applets de commande qui vous permettent de rechercher facilement les erreurs et de déterminer des corrections potentielles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

## Utilisation des journaux d'audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs d’un déploiement, procédez comme suit :

1. Pour récupérer les entrées de journal, exécutez la commande **Get-AzureRmLog**. Vous pouvez utiliser les paramètres **ResourceGroup** et **Status** pour retourner uniquement les événements qui ont échoué pour un seul groupe de ressources. Si vous ne spécifiez pas une heure de début et de fin, les entrées de la dernière heure sont retournées. Par exemple, pour récupérer les opérations ayant échoué de la dernière heure :

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Vous pouvez spécifier un intervalle de temps en particulier. Dans l'exemple suivant, nous examinons les actions ayant échoué durant les 14 derniers jours.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    Vous pouvez également définir un début et une fin exacts pour les actions ayant échoué :

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Si cette commande renvoie trop d’entrées et de propriétés, vous pouvez cibler vos efforts d’audit en récupérant la propriété **Properties**. Nous allons également inclure le paramètre **DetailedOutput** pour voir les messages d’erreur.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
    Qui retourne les propriétés des entrées du journal dans le format suivant :
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. Vous pouvez affiner les résultats en examinant le message d’état d’une entrée en particulier.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
    Qui retourne le message d'état dans le format suivant :
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## Utilisation des opérations de déploiement pour résoudre les problèmes

1. Pour obtenir l'état global d'un déploiement, utilisez la commande **AzureRmResourceGroupDeployment**. Vous pouvez filtrer les résultats en les restreignant aux déploiements qui ont échoué.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Qui retourne les déploiements ayant échoué dans le format suivant :
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. Chaque déploiement est généralement constitué de plusieurs opérations, chacune d’elles représentant une étape du processus de déploiement. Pour découvrir la cause du problème rencontré lors d’un déploiement, vous devez généralement afficher les détails concernant les opérations de déploiement. Vous pouvez afficher l'état des opérations avec **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment | Format-List
        
    Qui retourne les opérations dans le format suivant :
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. Pour obtenir plus d'informations sur l'opération, récupérez l’objet **Properties**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
    Qui retourne les propriétés de l'opération dans le format suivant :
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. Pour vous concentrer sur le message d'état des opérations ayant échoué, utilisez la commande suivante :

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
    Qui retourne le message d'état dans le format suivant :
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## Étapes suivantes

- Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l'utilisation des journaux d'audit pour surveiller d'autres types d'actions, consultez [Auditer les opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0518_2016-->