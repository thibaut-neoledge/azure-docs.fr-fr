<properties
	pageTitle="Mise à l’échelle automatique et groupes identiques de machines virtuelles | Microsoft Azure"
	description="En savoir plus sur l’utilisation des ressources de diagnostic et de mise à l’échelle pour mettre à l’échelle automatiquement des machines virtuelles dans un groupe identique."
    services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="davidmu"/>

# Mise à l’échelle automatique et groupes identiques de machines virtuelles

La mise à l’échelle automatique des machines virtuelles dans un jeu de mise à l’échelle correspond à la création ou à la suppression de machines dans le jeu en fonction des besoins d’une application pour répondre aux exigences de performances et aux contrats de niveau de service (SLA). Au fur et à mesure de l’évolution du volume de travail, une application peut nécessiter des ressources supplémentaires afin d’exécuter les tâches de manière efficace.

La mise à l’échelle automatique est un processus automatisé qui allège les contraintes de gestion. Grâce à la réduction de la surcharge, il devient inutile de surveiller en continu les performances du système et de décider d’ajouter ou de supprimer des ressources. La mise à l’échelle est un processus élastique : lorsque la charge sur le système augmente, il est possible d’allouer davantage de ressources, et lorsque la demande diminue, des ressources peuvent être libérées pour réduire les coûts tout en conservant des performances adéquates et en respectant les contrats de niveau de service.

Configurez la mise à l’échelle automatique dans un jeu de mise à l’échelle à l’aide d’un modèle Azure Resource Manager, à l’aide d’Azure PowerShell ou à l’aide de l’interface de ligne de commande Azure.

## Configurer la mise à l’échelle à l’aide de modèles Resource Manager

Au lieu de déployer et de gérer chaque ressource de votre application séparément, utilisez un modèle qui déploie et alloue toutes les ressources en une seule opération coordonnée. Dans le modèle, les ressources d’application sont définies et les paramètres de déploiement sont spécifiés pour différents environnements. Le modèle se compose de JSON et d'expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement. Pour en savoir plus, consultez [Créer des modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Dans le modèle, vous indiquez l’élément de capacité :

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 3
    },

La capacité correspond au nombre de machines virtuelles dans le jeu. Vous pouvez modifier manuellement la capacité en déployant un modèle avec une valeur différente. Si vous déployez un modèle simplement pour modifier la capacité, vous pouvez inclure uniquement l’élément SKU avec la capacité mise à jour.

Modifiez automatiquement la capacité de votre jeu de mise à l’échelle en combinant la ressource autoscaleSettings fournie par Azure Resource Manager et l’extension Azure Diagnostics installée sur les machines du jeu de mise à l’échelle.

### Configurer l’extension Azure Diagnostics

La mise à l’échelle automatique peut uniquement être réalisée si la collecte de mesures est réussie sur chaque machine virtuelle du jeu de mise à l’échelle. L’extension Azure Diagnostics offre des capacités de surveillance et de diagnostic qui répondent aux besoins en matière de collecte de mesures de la ressource de mise à l’échelle. Vous pouvez installer l’extension en tant que partie du modèle Resource Manager.

Cet exemple montre les variables utilisées dans le modèle pour configurer l’extension de diagnostic :

	"diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'saa')]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/', 'Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\Thread Count" sampleRate="PT15S" unit="Percent"><annotation displayName="Thread Count" locale="fr-FR"/></PerformanceCounterConfiguration></PerformanceCounters>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"

Lorsque le modèle est déployé, les paramètres sont fournis pour certaines valeurs, telles que le nom du compte de stockage où sont stockés les données et le nom du jeu de mise à l’échelle à partir duquel les données sont collectées. Dans cet exemple Windows Server, seul le compteur de performances du nombre de threads est collecté, mais tous les compteurs de performance disponibles dans Windows ou Linux peuvent être utilisés pour collecter des informations de diagnostic et peuvent être inclus dans la configuration de l’extension.

Cet exemple illustre la définition de l’extension dans le modèle :

    "extensionProfile": {
      "extensions": [
        {
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.5",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
              "storageAccount": "[variables('diagnosticsStorageAccountName')]"
            },
            "protectedSettings": {
              "storageAccountName": "[variables('diagnosticsStorageAccountName')]",
              "storageAccountKey": "[listkeys(variables('accountid'), variables('apiVersion')).key1]",
              "storageAccountEndPoint": "https://core.windows.net"
            }
          }
        }
      ]
    }

Lorsque l’extension de diagnostic s’exécute, les données sont collectées dans une table qui se trouve dans le compte de stockage que vous spécifiez. La table WADPerformanceCounters regroupe les données collectées. Par exemple, voici le nombre de threads collectés lorsque les machines virtuelles ont été créées pour la première fois dans un jeu de mise à l’échelle :

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountBefore2.png)

### Configurer la ressource autoScaleSettings

La ressource autoscaleSettings utilise les informations collectées par l’extension de diagnostic pour prendre des décisions sur la façon d’augmenter ou de diminuer le nombre de machines virtuelles dans le jeu de mise à l’échelle.

Cet exemple illustre la configuration de la ressource dans le modèle :

    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 650
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "\\Process(_Total)\\Thread Count",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 550
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
      }
    }

Dans l’exemple ci-dessus, deux règles sont créées afin de définir les actions de mise à l’échelle automatique. La première règle définit l’augmentation de la taille des instances et la deuxième règle définit la diminution de la taille des instances. Ces valeurs sont fournies dans les règles :

- **metricName** : il s’agit du même compteur que le compteur de performance que vous avez défini dans la variable wadperfcounter pour l’extension de diagnostic. Dans l’exemple ci-dessus, le compteur du nombre de threads est utilisé.
- **metricResourceUri** : il s’agit de l’identificateur de ressource du jeu de mise à l’échelle de machine virtuelle. Cet identificateur contient le nom du groupe de ressources, le nom du fournisseur de ressources et le nom du jeu de mise à l’échelle à mettre à l’échelle.
- **timeGrain** : il s’agit de la granularité des mesures collectées. Dans l’exemple ci-dessus, les données sont collectées sur un intervalle d’une minute. Cette valeur est utilisée en association avec timeWindow.
- **statistiques** : ce paramètre détermine la façon dont les mesures sont combinées pour prendre en charge l’action de mise à l’échelle automatique. Les valeurs possibles sont : Moyenne, Min, Max.
- **timeWindow** : il s’agit de la plage de temps pendant laquelle les données d’instance sont collectées. Elle doit être comprise entre 5 minutes et 12 heures.
- **timeAggregation** : ce paramètre détermine la façon dont les données recueillies doivent être combinées sur la durée. La valeur par défaut est Average. Les valeurs possibles sont : Moyenne, Minimum, Maximum, Dernier, Total, Nombre.
- **opérateur** : opérateur utilisé pour comparer les données de mesure et le seuil. Les valeurs possibles sont : est égal à -Equals), différent de (NotEquals), supérieur à (GreaterThan), égal ou supérieur à (GreaterThanOrEqual), Inférieur à (LessThan), Inférieur ou égal à (LessThanOrEqual).
- **seuil** : il s’agit de la valeur qui déclenche l’action de mise à l’échelle. Veillez à mettre en place une différence suffisante entre le seuil que vous définissez pour l’augmentation de la taille des instances et le seuil que vous définissez pour la diminution de la taille des instances. Si vous définissez des valeurs identiques, par exemple en paramétrant les deux valeurs sur 600 threads dans l’exemple ci-dessus, le système anticipera une augmentation et une réduction constantes de la taille du jeu et n’implémentera pas d’action de mise à l’échelle.
- **direction** : ce paramètre détermine l’opération qui est effectuée lorsque la valeur de seuil est atteinte. Les valeurs possibles sont Augmenter ou Diminuer.
- **type** : il s’agit du type d’action qui doit se produire. Il doit être défini sur ChangeCount.
- **valeur** : il s’agit du nombre de machines virtuelles qui sont ajoutées ou supprimées du groupe identique. Cette valeur doit être définie sur 1 ou supérieur.
- **ralentissement** : durée d’attente depuis la dernière opération de mise à l’échelle avant que l’action se produise. La valeur doit être comprise entre une minute et une semaine.

En fonction du compteur de performance que vous utilisez, certains éléments de la configuration du modèle sont utilisés différemment. Dans l’exemple présenté, le compteur de performance utilisé correspond au nombre de threads et la valeur de seuil est définie sur 650 pour l’augmentation de la taille des instances et 550 pour la diminution de la taille des instances. Si vous utilisez un compteur tel que le pourcentage de temps processeur, la valeur de seuil est définie sur le pourcentage d’utilisation du processeur qui détermine une action de mise à l’échelle.

Lors du déclenchement d’une action de mise à l’échelle dû à la création d’une charge sur les machines virtuelles du jeu, le nombre de machines du jeu augmente en fonction de l’élément de valeur dans le modèle. Par exemple, dans un jeu de mise à l’échelle où la capacité est définie sur 3 et la valeur d’action de mise à l’échelle est définie sur 1 :

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerBefore.png)

Lorsque la charge est créée et fait passer le nombre moyen de threads au-dessus du seuil de 650 :

![](./media/virtual-machine-scale-sets-autoscale-overview/ThreadCountAfter.png)

Une augmentation de la taille des instances est déclenchée et provoque l’augmentation de la capacité du jeu de 1 :

    "sku": {
      "name": "Standard_A0",
      "tier": "Standard",
      "capacity": 4
    },

Et une machine virtuelle est ajoutée au jeu de mise à l’échelle :

![](./media/virtual-machine-scale-sets-autoscale-overview/ResourceExplorerAfter.png)

Après une période de refroidissement de cinq minutes, si le nombre moyen de threads sur les machines est toujours supérieur à 600, une autre machine est ajoutée au jeu. Si le nombre moyen de threads reste inférieur à 550, la capacité du jeu de mise à l’échelle est diminuée d’une unité et une machine est supprimée du jeu.

## Configuration de mise à l’échelle à l’aide d’Azure PowerShell

Azure PowerShell permet de configurer la mise à l’échelle automatique pour un groupe identique. Voici un exemple d’utilisation de PowerShell pour créer les règles décrites précédemment dans cet article :

    $rule1 = New-AutoscaleRule -MetricName "\Processor(_Total)\Thread Count" -MetricResourceId "/subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1" -Operator GreaterThan -MetricStatistic Average -Threshold 650 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"  
 
    $rule2 = New-AutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/rainvmss/providers/Microsoft.Compute/virtualMachineScaleSets/rainvmss1" -Operator LessThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue "2" 
 
    $profile1 = New-AutoscaleProfile -DefaultCapacity "2" -MaximumCapacity "10" -MinimumCapacity "2" -Rules $rule1, $rule2 -Name "ScalePuppy" 
 
    Add-AutoscaleSetting -Location "East US" -Name 'MyScaleVMSSSetting' -ResourceGroup rainvmss -TargetResourceId "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/rainvmss/providers/Microsoft.Compute/virtualMachineScaleSets/rainvmss1" -AutoscaleProfiles $profile1 

## Configuration de mise à l’échelle à l’aide de l’interface de ligne de commande Azure

(informations non encore disponibles)

## Examiner les actions de mise à l’échelle

- [Portail Azure]() : vous pouvez obtenir une quantité limitée d’informations par le biais du portail.
- [Azure Resource Explorer]() : il s’agit du meilleur outil pour déterminer l’état actuel de votre groupe identique. Suivez ce chemin d’accès suivant pour afficher la vue d’instance du groupe identique que vous avez créé : abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Compute > virtualMachineScaleSets > {votre groupe identique} > machines virtuelles
- Azure PowerShell : utilisez cette commande pour obtenir des informations :

        Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmsstestrg1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15
        Get-Autoscalesetting -ResourceGroup rainvmss -DetailedOutput
        
- Connectez-vous à la machine virtuelle jumpbox comme vous le feriez pour n’importe quel autre ordinateur et vous pouvez ensuite accéder à distance aux machines virtuelles du groupe à échelle identique pour surveiller les processus individuels.

## Étapes suivantes

- Consultez [Mettre à l’échelle automatiquement des groupes identiques de machines virtuelles](virtual-machine-scale-sets-windows-autoscale.md) pour voir un exemple montrant comment créer un groupe identique une fois la mise à l’échelle automatique configurée.
- Découvrez des exemples de fonctionnalités de surveillance Azure Insights dans les [exemples de démarrage rapide d’Azure Insights PowerShell](../azure-portal/insights-powershell-samples.md)
- Pour en savoir plus sur les fonctionnalités de notification, consultez [Utilisation d’actions de mise à l’échelle automatique pour envoyer des notifications d’alerte webhook et par courrier électronique dans Azure Insights](../azure-portal/insights-autoscale-to-webhook-email.md) et [Utiliser les journaux d’audit pour envoyer des notifications webhook et par courrier électronique dans Azure Insights](../azure-portal/insights-auditlog-to-webhook-email.md)
- En savoir plus sur les [scénarios avancés de mise à l’échelle automatique](./virtual-machine-scale-sets-advanced-autoscale.md).

<!---HONumber=AcomDC_0907_2016-->