---
title: "Utilisation de PowerShell pour gérer Traffic Manager dans Azure | Microsoft Docs"
description: Utilisation de Powershell pour Traffic Manager avec Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 1cd7bd7e32c96398d72c7cd3b51e2b456d60f01d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Utilisation de PowerShell pour gérer Traffic Manager

Azure Resource Manager est l’interface de gestion par défaut des services Azure. Les outils et API d’Azure Resource Manager permettent de gérer les profils Azure Traffic Manager.

## <a name="resource-model"></a>Modèle de ressource

Azure Traffic Manager est configuré à l'aide d'un ensemble de paramètres appelé profil Traffic Manager. Ce profil contient les paramètres DNS, les paramètres de routage du trafic, les paramètres de surveillance des points de terminaison et la liste des points de terminaison de service vers lesquels le trafic est acheminé.

Chaque profil Traffic Manager est représenté par une ressource de type « TrafficManagerProfiles ». Au niveau de l'API REST, l'URI de chaque profil est la suivante :

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Configuration d’Azure PowerShell

Ces instructions utilisent Microsoft Azure PowerShell. L’article suivant explique comment installer et configurer Azure PowerShell.

* [Installation et configuration d’Azure PowerShell](/powershell/azure/overview)

Les exemples de cet article supposent que vous disposez déjà d’un groupe de ressources. Vous pouvez en créer un à l’aide de la commande suivante :

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager exige des groupes de ressources qu’ils disposent tous d’un emplacement. Celui-ci est utilisé comme emplacement par défaut pour les ressources créées dans ce groupe de ressources. Cependant, étant donné que les ressources de profil Traffic Manager sont mondiales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucune incidence sur Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Créer un profil Traffic Manager

Pour créer un profil Traffic Manager, utilisez l’applet de commande `New-AzureRmTrafficManagerProfile` :

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Le tableau suivant décrit les paramètres :

| Paramètre | Description |
| --- | --- |
| Name |Nom de la ressource de profil Traffic Manager. Les profils d’un même groupe de ressources doivent avoir un nom unique. Ce nom est différent du nom DNS utilisé pour les requêtes DNS. |
| ResourceGroupName |Nom du groupe de ressources contenant la ressource de profil. |
| TrafficRoutingMethod |Spécifie la méthode de routage du trafic utilisée pour identifier le point de terminaison qui est retourné en réponse à une requête DNS. Les valeurs possibles sont « Performance », « Weighted » ou « Priority ». |
| RelativeDnsName |Spécifie la partie nom d’hôte du nom DNS fourni par ce profil Traffic Manager. Cette valeur est combinée au nom de domaine DNS utilisé par Azure Traffic Manager pour former le nom de domaine complet (FQDN) du profil. Par exemple, la valeur définie « contoso » devient « contoso.trafficmanager.net ». |
| TTL |Spécifie la durée de vie (TTL) du DNS en secondes. Ce paramètre indique aux programmes de résolution DNS locaux et aux clients DNS la durée de mise en cache des réponses DNS pour ce profil Traffic Manager. |
| MonitorProtocol |Spécifie le protocole à utiliser pour surveiller l’intégrité des points de terminaison. Les valeurs possibles sont « HTTP » et « HTTPS ». |
| MonitorPort |Spécifie le port TCP utilisé pour surveiller l’intégrité des points de terminaison. |
| MonitorPort |Spécifie le chemin relatif du nom de domaine de point de terminaison utilisé pour évaluer l’intégrité des points de terminaison. |

L’applet de commande crée un profil Traffic Manager dans Azure et retourne un objet de profil correspondant à PowerShell. À ce stade, le profil ne contient aucun point de terminaison. Pour plus d’informations sur l’ajout de points de terminaison à un profil Traffic Manager, consultez [Ajout de points de terminaison Traffic Manager](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Récupérer un profil Traffic Manager

Pour récupérer un objet de profil Traffic Manager existant, utilisez l’applet de commande `Get-AzureRmTrafficManagerProfle` :

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Cette applet de commande renvoie un objet de profil Traffic Manager.

## <a name="update-a-traffic-manager-profile"></a>Mettre à jour un profil Traffic Manager

La modification des profils Traffic Manager est un processus en 3 étapes :

1. Récupérez le profil à l’aide de `Get-AzureRmTrafficManagerProfile` ou utilisez le profil retourné par `New-AzureRmTrafficManagerProfile`.
2. Modifiez le profil. Vous pouvez ajouter et supprimer des points de terminaison ou modifier les paramètres des points de terminaison ou du profil. Ces modifications sont des opérations hors connexion. Vous modifiez seulement l’objet local en mémoire qui représente le profil.
3. Validez vos modifications à l’aide de l’applet de commande `Set-AzureRmTrafficManagerProfile`.

Toutes les propriétés de profil peuvent être modifiées à l’exception de RelativeDnsName. Pour modifier la propriété RelativeDnsName, vous devez supprimer le profil et en créer un sous un nouveau nom.

L’exemple suivant montre comment modifier la durée de vie (TTL) du profil :

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Il existe trois types de points de terminaison Traffic Manager :

1. Les **points de terminaison Azure** sont des services hébergés dans Azure.
2. Les **points de terminaison externes** sont des services hébergés en dehors d’Azure.
3. Les **points de terminaison imbriqués** servent à construire des hiérarchies imbriquées de profils Traffic Manager. Ils permettent de définir des configurations de routage du trafic avancées pour les applications complexes.

Dans ces trois cas, les points de terminaison peuvent être ajoutés de deux manières :

1. En suivant le processus en 3 étapes décrit précédemment. L’avantage de cette méthode est que plusieurs modifications de point de terminaison peuvent être apportées en une seule mise à jour.
2. En utilisant l’applet de commande New-AzureRmTrafficManagerEndpoint. Celle-ci permet d’ajouter un point de terminaison à un profil Traffic Manager existant en une seule opération.

## <a name="adding-azure-endpoints"></a>Ajout de points de terminaison Azure

Les points de terminaison Azure font référence à des services hébergés dans Azure. Les types de point de terminaison Azure pris en charge sont au nombre de deux :

1. Azure Web Apps 
2. Ressources PublicIpAddress Azure (peuvent être attachées à un équilibreur de charge ou à une carte réseau de machine virtuelle). PublicIpAddress doit se voir affecter un nom DNS pour être utilisé dans Traffic Manager.

Dans chaque cas :

* Le service est spécifié à l’aide du paramètre « targetResourceId » de `Add-AzureRmTrafficManagerEndpointConfig` ou `New-AzureRmTrafficManagerEndpoint`.
* « Target » et « EndpointLocation » sont définis implicitement selon TargetResourceId.
* La spécification du paramètre « Pondération » est facultative. Les pondérations ne sont utilisées que si le profil est configuré pour utiliser la méthode de routage du trafic « Weighted ». Autrement, elles sont ignorées. S’il est spécifié, le paramètre doit avoir une valeur numérique comprise entre 1 et 1 000. La valeur par défaut est « 1 ».
* La spécification du paramètre « Priorité » est facultative. Les priorités ne sont utilisées que si le profil est configuré pour utiliser la méthode d’acheminement de trafic « Priority ». Autrement, elles sont ignorées. Les valeurs valides vont de 1 à 1 000, les valeurs basses indiquant une priorité élevée. Si elles sont spécifiées pour un point de terminaison, elles doivent l’être pour tous les points de terminaison. En cas d’omission, les valeurs par défaut à partir de « 1 » sont appliquées dans l’ordre où les points de terminaison sont répertoriés.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exemple 1 : ajout de points de terminaison d’application web à l’aide de `Add-AzureRmTrafficManagerEndpointConfig`

Dans cet exemple, nous créons un profil Traffic Manager et ajoutons deux points de terminaison d’application web à l’aide de l’applet de commande `Add-AzureRmTrafficManagerEndpointConfig`.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : ajout d’un point de terminaison publicIpAddress à l’aide de `New-AzureRmTrafficManagerEndpoint`

Dans cet exemple, une ressource d’adresse IP publique est ajoutée au profil Traffic Manager. L’adresse IP publique doit avoir un nom DNS configuré et peut être liée à la carte réseau d’une machine virtuelle ou à un équilibreur de charge.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Ajout de points de terminaison externes

Traffic Manager utilise les points de terminaison externes pour diriger le trafic vers les services hébergés en dehors d’Azure. Comme les points de terminaison Azure, les points de terminaison externes peuvent être ajoutés à l’aide de `Add-AzureRmTrafficManagerEndpointConfig` suivi de `Set-AzureRmTrafficManagerProfile`, ou avec `New-AzureRMTrafficManagerEndpoint`.

Lors de la spécification de points de terminaison externes :

* Le nom de domaine du point de terminaison doit être spécifié en utilisant le paramètre « Target »
* Si la méthode de routage du trafic « Performance » est utilisée, le paramètre « EndpointLocation » doit être renseigné. Sinon, il est facultatif. La valeur doit être un [nom de région Azure valide](https://azure.microsoft.com/regions/).
* Les paramètres « Weight » et « Priority » sont facultatifs.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : ajout de points de terminaison externes à l’aide de `Add-AzureRmTrafficManagerEndpointConfig` et `Set-AzureRmTrafficManagerProfile`

Dans cet exemple, nous créons un profil Traffic Manager, ajoutons deux points de terminaison externes et validons les modifications.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : ajout de points de terminaison externes à l’aide de `New-AzureRmTrafficManagerEndpoint`

Dans cet exemple, nous ajoutons un point de terminaison externe à un profil existant. Le profil est spécifié en utilisant les noms de profil et de groupe de ressources.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Ajout de points de terminaison « imbriqués »

Chaque profil Traffic Manager spécifie une seule méthode de routage du trafic. Cependant, certains scénarios exigent une méthode de routage du trafic plus sophistiquée que celle proposée par un profil Traffic Manager unique. Vous pouvez imbriquer des profils Traffic Manager pour combiner les avantages de plusieurs méthodes de routage du trafic. Les profils imbriqués permettent de modifier le comportement par défaut de Traffic Manager pour prendre en charge des déploiements d’applications plus importants et plus complexes. Pour obtenir des informations plus détaillées, consultez [Profils Traffic Manager imbriqués](traffic-manager-nested-profiles.md).

Les points de terminaison imbriqués sont configurés sur le profil parent, à l’aide d’un type de point de terminaison spécifique, « NestedEndpoints ». Lors de la spécification de points de terminaison imbriqués :

* Le point de terminaison doit être spécifié à l’aide du paramètre « targetResourceId ».
* Si la méthode de routage du trafic « Performance » est utilisée, le paramètre « EndpointLocation » doit être renseigné. Sinon, il est facultatif. La valeur doit être un [nom de région Azure valide](http://azure.microsoft.com/regions/).
* Les paramètres « Pondération » et « Priorité » sont facultatifs, comme pour les points de terminaison Azure.
* Le paramètre « MinChildEndpoints » est facultatif. La valeur par défaut est « 1 ». Si le nombre de points de terminaison disponibles tombe sous ce seuil, le profil parent considère le profil enfant comme étant « dégradé » et dirige le trafic vers les autres points de terminaison du profil parent.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : ajout de points de terminaison imbriqués à l’aide de `Add-AzureRmTrafficManagerEndpointConfig` et `Set-AzureRmTrafficManagerProfile`

Dans cet exemple, nous créons des profils Traffic Manager enfant et parent, ajoutons l’enfant en tant que point de terminaison imbriqué au parent et validons les modifications.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Par souci de concision, dans cet exemple, nous n’avons pas ajouté d’autres points de terminaison aux profils enfant ou parent.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : ajout de points de terminaison imbriqués à l’aide de `New-AzureRmTrafficManagerEndpoint`

Dans cet exemple, nous ajoutons un profil enfant existant en tant que point de terminaison imbriqué à un profil parent existant. Le profil est spécifié en utilisant les noms de profil et de groupe de ressources.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="update-a-traffic-manager-endpoint"></a>Mettre à jour un point de terminaison Traffic Manager

Il existe deux façons de mettre à jour un point de terminaison Traffic Manager existant :

1. Obtenir le profil Traffic Manager à l’aide de `Get-AzureRmTrafficManagerProfile`, mettre à jour les propriétés de point de terminaison dans le profil et valider les modifications apportées à l’aide de `Set-AzureRmTrafficManagerProfile`. Cette méthode présente l’avantage de pouvoir mettre à jour plusieurs points de terminaison en une seule opération.
2. Obtenir le point de terminaison Traffic Manager à l’aide de `Get-AzureRmTrafficManagerEndpoint`, mettre à jour les propriétés de point de terminaison et valider les modifications apportées à l’aide de `Set-AzureRmTrafficManagerEndpoint`. Cette méthode est plus simple, car elle ne nécessite pas d’indexation dans le tableau de points de terminaison dans le profil.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : mise à jour des points de terminaison à l’aide de `Get-AzureRmTrafficManagerProfile` et `Set-AzureRmTrafficManagerProfile`

Dans cet exemple, nous modifions l’ordre de priorité de deux points de terminaison dans un profil existant.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exemple 2 : mise à jour d’un point de terminaison à l’aide de `Get-AzureRmTrafficManagerEndpoint` et `Set-AzureRmTrafficManagerEndpoint`

Dans cet exemple, nous modifions le poids d’un point de terminaison unique dans un profil existant.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Activation et désactivation des points de terminaison et des profils

Traffic Manager permet d’activer et de désactiver des points de terminaison individuels, tout en permettant l’activation et la désactivation des profils complets.
Ces modifications peuvent être effectuées par obtention/mise à jour/définition du point de terminaison ou de ressources de profil. Pour rationaliser les opérations courantes, elles sont également prises en charge via les applets de commande dédiées.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemple 1 : activation et désactivation d’un profil Traffic Manager

Pour activer un profil Traffic Manager, utilisez `Enable-AzureRmTrafficManagerProfile`. Le profil peut être spécifié à l’aide d’un objet de profil. L’objet de profil peut être transmis via le pipeline ou en utilisant le paramètre « -TrafficManagerProfile ». Dans cet exemple, nous spécifions le profil avec le nom de profil et le nom de groupe de ressources.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Pour désactiver un profil Traffic Manager :

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

L’applet de commande Disable-AzureRmTrafficManagerProfile vous demande de confirmer l’opération. Ce message peut être supprimé en utilisant le paramètre « -Force ».

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemple 2 : activation et désactivation d’un point de terminaison Traffic Manager

Pour activer un point de terminaison Traffic Manager, utilisez `Enable-AzureRmTrafficManagerEndpoint`. Il existe deux façons de spécifier le point de terminaison :

1. en utilisant un objet TrafficManagerEndpoint, qui est transmis via le pipeline ou en utilisant le paramètre « -TrafficManagerEndpoint » ;
2. en utilisant le nom du point de terminaison, le type du point de terminaison, le nom du profil et le nom du groupe de ressources :

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

De même, pour désactiver un point de terminaison Traffic Manager :

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Comme `Disable-AzureRmTrafficManagerProfile`, l’applet de commande `Disable-AzureRmTrafficManagerEndpoint` vous demande de confirmer l’opération. Ce message peut être supprimé en utilisant le paramètre « -Force ».

## <a name="delete-a-traffic-manager-endpoint"></a>Supprimer un point de terminaison Traffic Manager

Pour supprimer des points de terminaison individuels, utilisez l’applet de commande `Remove-AzureRmTrafficManagerEndpoint` :

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Cette applet de commande vous demande de confirmer l’opération. Ce message peut être supprimé en utilisant le paramètre « -Force ».

## <a name="delete-a-traffic-manager-profile"></a>Supprimer un profil Traffic Manager

Pour supprimer un profil Traffic Manager, utilisez l’applet de commande `Remove-AzureRmTrafficManagerProfile` en indiquant les noms de profil et de groupe de ressources :

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Cette applet de commande vous demande de confirmer l’opération. Ce message peut être supprimé en utilisant le paramètre « -Force ».

Le profil à supprimer peut également être spécifié par un objet de profil :

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Cette séquence peut également être canalisée :

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Étapes suivantes

[Surveillance avec Traffic Manager](traffic-manager-monitoring.md)

[Considérations sur les performances de Traffic Manager](traffic-manager-performance-considerations.md)
