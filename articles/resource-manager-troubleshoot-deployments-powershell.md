<properties
   pageTitle="Affichage des opérations de déploiement avec PowerShell | Microsoft Azure"
   description="Explique comment utiliser Azure PowerShell pour détecter les problèmes de déploiement de Resource Manager."
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
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# Afficher les opérations de déploiement Azure PowerShell

> [AZURE.SELECTOR]
- [Portail](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Interface de ligne de commande Azure](resource-manager-troubleshoot-deployments-cli.md)
- [API REST](resource-manager-troubleshoot-deployments-rest.md)

Vous pouvez afficher les opérations d’un déploiement via Azure PowerShell. Il est plus intéressant d’afficher les opérations lorsque vous recevez une erreur lors du déploiement ; cet article porte donc sur l’affichage des opérations ayant échoué. PowerShell fournit des applets de commande qui vous permettent de rechercher facilement les erreurs et de déterminer des corrections potentielles.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Vous pouvez éviter certaines erreurs en validant votre modèle et votre infrastructure avant le déploiement. Vous pouvez également enregistrer des informations supplémentaires de requête et de réponse pendant le déploiement, qui pourront se révéler utiles plus tard pour la résolution des problèmes. Pour en savoir plus sur la validation et l’enregistrement d’informations de requête et de réponse, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

## Utilisation des opérations de déploiement pour résoudre les problèmes

1. Pour obtenir l’état global d’un déploiement, utilisez la commande **Get-AzureRmResourceGroupDeployment**. Vous pouvez filtrer les résultats en les restreignant aux déploiements qui ont échoué.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Qui retourne les déploiements ayant échoué dans le format suivant :
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Chaque déploiement est généralement constitué de plusieurs opérations, chacune d’elles représentant une étape du processus de déploiement. Pour découvrir la cause du problème rencontré lors d’un déploiement, vous devez généralement afficher les détails concernant les opérations de déploiement. Vous pouvez afficher l'état des opérations avec **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Cette requête retourne plusieurs opérations, chacune au format suivant :
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Pour obtenir plus d’informations sur les opérations ayant échoué, récupérez les propriétés des opérations dont l’état est **Failed**.

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Cette requête retourne toutes les opérations ayant échoué, chacune au format suivant :
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Notez l’ID de suivi pour l’opération. Vous l’utiliserez à l’étape suivante afin de vous concentrer sur une opération en particulier.

4. Pour obtenir le message d’état d’une opération ayant échoué, utilisez la commande suivante :

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Résultat :
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## Utilisation des journaux d'audit pour résoudre les problèmes

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Pour afficher les erreurs d’un déploiement, procédez comme suit :

1. Pour récupérer les entrées de journal, exécutez la commande **Get-AzureRmLog**. Vous pouvez utiliser les paramètres **ResourceGroup** et **Status** pour retourner uniquement les événements qui ont échoué pour un seul groupe de ressources. Si vous ne spécifiez pas une heure de début et de fin, les entrées de la dernière heure sont retournées. Par exemple, pour récupérer les opérations ayant échoué de la dernière heure :

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Vous pouvez spécifier un intervalle de temps en particulier. Dans l’exemple suivant, nous examinons les actions ayant échoué la veille.

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    Vous pouvez également définir un début et une fin exacts pour les actions ayant échoué :

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Si cette commande renvoie trop d’entrées et de propriétés, vous pouvez cibler vos efforts d’audit en récupérant la propriété **Properties**. Nous allons également inclure le paramètre **DetailedOutput** pour voir les messages d’erreur.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Qui retourne les propriétés des entrées du journal dans le format suivant :
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. En nous basant sur ces résultats, concentrons-nous sur le deuxième élément. Vous pouvez affiner les résultats en examinant le message d’état pour cette entrée.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Résultat :
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## Étapes suivantes

- Pour obtenir de l’aide afin de résoudre des erreurs de déploiement spécifiques, consultez [Résoudre les erreurs courantes lors du déploiement de ressources sur Azure avec Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Pour en savoir plus sur l’utilisation des journaux d’audit pour surveiller d’autres types d’actions, consultez [Auditer les opérations avec le Gestionnaire de ressources](resource-group-audit.md).
- Pour valider votre déploiement avant son exécution, consultez [Déployer un groupe de ressources avec le modèle Azure Resource Manager](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0622_2016-->