<properties
	pageTitle="Azure Insights : exemples de démarrage rapide Azure Insights PowerShell. | Microsoft Azure"
	description="Les commandes PowerShell des exemples de démarrage rapide Azure Insights peuvent vous aider à accéder rapidement aux fonctions de surveillance Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Exemples de démarrage rapide Azure Insights PowerShell

Cet article vous présente des exemples de commandes PowerShell qui vous aideront à accéder rapidement aux fonctions de surveillance Azure Insights. Azure Insights permet une mise à l'échelle automatique des services cloud, des machines virtuelles et des applications web, et d’envoyer des notifications d'alerte ou d’appeler des URL web basées sur des valeurs de données de télémétrie configurées.

## Configurer PowerShell
Si vous ne l’avez déjà fait, configurez PowerShell pour s’exécuter sur votre ordinateur. Pour plus d’informations, consultez la rubrique [Comment installer et configurer PowerShell](../powershell-install-configure.md).

## Exemples de cet article

Les exemples de cet article montrent comment utiliser les applets de commande Azure Insights. Vous pouvez également consulter la liste complète des applets de commande Azure Insights (surveillance) dans la rubrique [Applets de commande Azure Insights](https://msdn.microsoft.com/library/mt282452.aspx).


## Se connecter et utiliser des abonnements

Connectez-vous d’abord à votre abonnement Azure.

```
Login-AzureRmAccount
```

Vous devez vous identifier. Une fois cette opération effectuée, vos compte, ID de locataire et ID d'abonnement par défaut s’affichent. Toutes les applets de commande Azure fonctionnent dans le cadre de votre abonnement par défaut. Pour afficher la liste des abonnements auxquels vous avez accès, utilisez la commande suivante.

```
Get-AzureRmSubscription
```

Pour remplacer votre contexte de travail par un autre abonnement, utilisez la commande suivante.

```
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## Récupérer les journaux d'audit d’un abonnement
Utilisez l’applet de commande `Get-AzureRmLog`. Voici quelques exemples courants.

Obtenir les entrées de journal à partir de cette date/heure :

```
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obtenir les entrées de journal entre une plage de dates/heures :

```
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir les entrées de journal à partir d'un groupe de ressources spécifique :

```
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obtenir les entrées de journal à partir d'un fournisseur de ressources spécifique entre une plage de dates/heures :

```
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir toutes les entrées de journal avec un appelant spécifique :

```
Get-AzureRmLog -Caller 'myname@company.com'
```

La commande suivante récupère les 1000 derniers événements du journal d'audit :

```
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` prend en charge de nombreux autres paramètres. Pour plus d'informations, consultez `Get-AzureRmLog`.

>[AZURE.NOTE] `Get-AzureRmLog` fournit uniquement 15 jours d'historique. Le paramètre **-MaxEvents** vous permet d'interroger les N derniers événements, au-delà de 15 jours. Pour accéder aux événements antérieurs à 15 jours, utilisez l'API REST ou le Kit SDK (exemple de code C# à l'aide du SDK). Si vous n'incluez pas **StartTime**, la valeur par défaut est **EndTime** moins une heure. Si vous n'incluez pas **EndTime**, la valeur par défaut est l'heure actuelle. Toutes les heures sont exprimées en heure UTC.

## Récupérer l'historique des alertes
Pour afficher tous les événements d'alerte, vous pouvez interroger les journaux Azure Resource Manager (ARM) en utilisant les exemples suivants.

```
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Pour afficher l'historique d'une règle d'alerte spécifique, vous pouvez utiliser l’applet de commande `Get-AzureRmAlertHistory`, en passant l'ID de ressource de la règle d'alerte.

```
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

L’applet de commande `Get-AzureRmAlertHistory` prend en charge différents paramètres. Plus d'informations, consultez [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## Récupérer des informations sur les règles d'alerte
Toutes les commandes suivantes s’appliquent à un groupe de ressources nommé « montest ».

Afficher toutes les propriétés de la règle d'alerte :

```
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Récupérer toutes les alertes d'un groupe de ressources :

```
Get-AzureRmAlertRule -ResourceGroup montest
```

Récupérer toutes les règles d'alerte définies pour une ressource cible. Par exemple, toutes les règles d'alerte définies sur une machine virtuelle.

```
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` prend en charge d'autres paramètres. Consultez [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) pour plus d'informations.

## Créer des règles d'alerte
Vous pouvez utiliser l’applet de commande `Add-AlertRule` pour créer, mettre à jour ou désactiver une règle d'alerte.

Vous pouvez créer des propriétés de messagerie et webhook à l'aide de `New-AzureRmAlertRuleEmail` et `New-AzureRmAlertRuleWebhook`, respectivement. Dans l'applet de commande de la règle d'alerte, affectez ces éléments comme des actions à la propriété **Actions** de la règle d'alerte.

La section suivante contient un exemple montrant comment créer une règle d'alerte avec différents paramètres.

### Règle d'alerte d'une mesure
Le tableau suivant décrit les paramètres et les valeurs utilisés pour créer une alerte à l'aide d'une mesure.


|paramètre|value|
|---|---|
|Nom|	simpletestdiskwrite|
|Emplacement de cette règle d'alerte|	Est des États-Unis|
|ResourceGroup|	montest|
|TargetResourceId|	/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|MetricName de l'alerte créée|	\\PhysicalDisk(\_Total)\\Disk Writes/sec. Voir l’applet de commande `Get-MetricDefinitions` ci-dessous pour récupérer les noms exacts des mesures|
|operator| GreaterThan| 
|Valeur de seuil (nombre/sec pour cette mesure)| 1|
|WindowSize (hh:mm:ss format)| 00:05:00|
|aggregator (statistique de la mesure, qui utilise la valeur Average dans ce cas)| Average|
|courriers électroniques personnalisés (string array)|'foo@example.com','bar@example.com'|
|envoyer un courrier électronique aux propriétaires, contributeurs et lecteurs| -SendToServiceOwners|

Créer un courrier électronique

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Créer une action Webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Créer la règle d'alerte sur la mesure CPU% sur une machine virtuelle classique

```
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Récupérer la règle d'alerte

```
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

L'applet de commande Add alert met également à jour la règle, s'il existe une règle d'alerte pour les propriétés spécifiées. Pour désactiver une règle d'alerte, incluez le paramètre **-DisableRule**.

### Alerte sur un événement du journal d'audit

>[AZURE.NOTE] Cette fonctionnalité est encore en version préliminaire.

Dans ce scénario, vous enverrez un courrier électronique lorsqu'un site Web est démarré dans mon abonnement dans le groupe de ressources *abhingrgtest123*.

Configurer une règle de courrier électronique

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configurer une règle webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Créer la règle sur l'événement

```
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Récupérer la règle d'alerte

```
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

L’applet de commande `Add-AlertRule` permet d’utiliser divers autres paramètres. Plus d'informations, consultez [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## Obtenir la liste des mesures disponibles pour les alertes
Vous pouvez utiliser l’applet de commande`Get-AzureRmMetricDefinition` pour afficher la liste de toutes les mesures pour une ressource spécifique.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

L'exemple suivant génère une table avec le nom de la mesure et son unité.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Une liste complète des options disponibles pour `Get-AzureRmMetricDefinition` est disponible à la rubrique [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## Créer et gérer les paramètres de mise à l'échelle automatique
Une ressource, par exemple une application Web, une machine virtuelle, un service cloud ou un jeu de mise à l’échelle de machine virtuelle ne peut avoir qu'un seul paramètre de mise à l'échelle automatique configuré. Cependant, chaque paramètre de mise à l'échelle automatique peut avoir plusieurs profils. Par exemple, un pour un profil de mise à l'échelle en fonction des performances et un second pour un profil basé sur une planification. Chaque profil peut avoir plusieurs règles configurées. Pour plus d’informations sur la mise à l’échelle automatique, consultez [Mise à l'échelle automatique d’une application](../cloud-services/cloud-services-how-to-scale.md).

Voici la procédure que nous allons suivre :

1. Créez une ou plusieurs règles.
2. Créez un ou plusieurs profils correspondant aux règles que vous avez créées précédemment pour les profils.
3. Facultatif : créez des notifications de mise à l'échelle automatique en configurant les propriétés de courrier électronique et webhook.
4. Créez un paramètre de mise à l'échelle automatique avec un nom pour la ressource cible en mappant les profils et les notifications que vous avez créés aux étapes précédentes.

Les exemples suivants vous montrent comment créer un paramètre de mise à l'échelle automatique pour un jeu de mise à l’échelle de machines virtuelles d’un système d'exploitation Windows à l'aide de la mesure d'utilisation du processeur.

Tout d'abord, créez une règle de montée en charge, avec une augmentation du nombre d’instances.

```
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```		

Créez ensuite une règle de baisse de charge, avec une diminution du nombre d’instances.

```
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Puis créez un profil pour les règles.

```
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Créez une propriété webhook

```
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Créez la propriété de notification pour le paramètre de mise à l'échelle automatique, y compris les propriétés de courrier électronique et webhook que vous avez créées précédemment.

```
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Enfin, créez le paramètre de mise à l'échelle automatique pour ajouter le profil que vous avez créé précédemment.

```
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Pour plus d'informations sur la gestion des paramètres de mise à l’échelle automatique, consultez [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## Historique de la mise à l'échelle automatique
L'exemple suivant vous montre comment consulter les dernières mises à l'échelle automatiques et les derniers événements d'alerte. Utilisez la recherche du journal d'audit pour afficher l'historique de mise à l'échelle automatique.

```
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Vous pouvez utiliser l’applet de commande `Get-AzureRmAutoScaleHistory` pour récupérer l’historique de mise à l’échelle automatique.

```
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Pour plus d’informations, consultez [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### Afficher les détails d'un paramètre de mise à l'échelle automatique
Vous pouvez utiliser l’applet de commande `Get-Autoscalesetting` pour récupérer des informations supplémentaires sur le paramètre de mise à l’échelle automatique.

L'exemple suivant affiche des détails concernant tous les paramètres de mise à l'échelle automatique dans le groupe de ressources ’myrg1’.

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

L'exemple suivant affiche des détails concernant tous les paramètres de mise à l'échelle automatique dans le groupe de ressources ’myrg1’, et en particulier le paramètre de mise à l'échelle automatique nommé ’MyScaleVMSSSetting’.

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### Supprimer un paramètre de mise à l'échelle automatique
Vous pouvez utiliser l’applet de commande `Remove-Autoscalesetting` pour supprimer un paramètre de mise à l’échelle automatique.

```
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## Gérer les profils de journal pour les journaux d'audit

Vous pouvez créer un *profil de journal* et exporter les données de vos journaux d’audit vers un compte de stockage, et configurer la rétention de données pour celui-ci. Si vous le souhaitez, vous pouvez aussi transmettre en continu les données vers votre hub d'événements. Notez que cette fonctionnalité est actuellement en version préliminaire et vous ne pouvez créer qu'un seul profil de journal par abonnement. Vous pouvez utiliser les applets de commande suivantes avec votre abonnement actuel pour créer et gérer des profils de journal. Vous pouvez également choisir un abonnement spécifique. Bien que PowerShell utilise par défaut l’abonnement actif, vous pouvez toujours modifier ce paramètre avec `Set-AzureRmContext`. Vous pouvez configurer les journaux d'audit afin d’acheminer les données vers n'importe quel compte de stockage ou un hub d'événements au sein de cet abonnement. Les données sont écrites en tant que fichiers blob au format JSON.

### Obtenir un profil de journal
Pour extraire vos profils de journal existants, utilisez l’applet de commande `Get-AzureRmLogProfile`.

### Ajouter un profil de journal sans conservation des données

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Supprimer un profil de journal

```
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### Ajouter un profil de journal avec conservation des données

Vous pouvez spécifier la propriété **-RetentionInDays** en indiquant le nombre de jours (entier positif) durant lequel les données seront conservées.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### Ajouter un profil de journal avec conservation des données et hub d'événements
En plus du routage de vos données vers un compte de stockage, vous pouvez également transmettre en continu ces données vers un hub d'événements. Notez que dans cette version préliminaire, la configuration du compte de stockage est obligatoire mais la configuration du hub d'événements est facultative.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## Configuration des journaux de diagnostic
De nombreux services Azure fournissent des journaux supplémentaires ainsi qu’une télémétrie, notamment les groupes de sécurité réseau Azure, les équilibreurs de charge logiciels, le coffre de clés, les services Azure Search et les applications logiques. Et ils peuvent être configurés pour enregistrer les données dans votre compte de stockage Azure. Cette opération ne peut être effectuée qu’au niveau d'une ressource, et le compte de stockage doit être présent dans la même région que la ressource cible où les paramètres de diagnostic sont configurés.

### Obtenir le paramètre de diagnostic

```
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Désactiver le paramètre de diagnostic

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Activer le paramètre de diagnostic sans conservation

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Activer le paramètre de diagnostic avec conservation

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Activer un paramètre diagnostic avec conservation pour une catégorie de journal spécifique

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

<!---HONumber=AcomDC_0406_2016-->