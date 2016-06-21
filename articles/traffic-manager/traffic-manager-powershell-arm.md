<properties
   pageTitle="Prise en charge d’Azure Resource Manager pour Azure Traffic Manager | Microsoft Azure"
   description="Utilisation de Powershell pour Traffic Manager avec Azure Resource Manager (ARM)"
   services="traffic-manager"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="jtuliani" />

# Prise en charge d’Azure Resource Manager pour Azure Traffic Manager
Azure Resource Manager (ARM) est la nouvelle infrastructure de gestion des services dans Azure. Les outils et API d’Azure Resource Manager permettent désormais de gérer les profils Azure Traffic Manager.

## Modèle de ressource

Azure Traffic Manager est configuré à l'aide d'un ensemble de paramètres appelé profil Traffic Manager. Ce profil contient les paramètres DNS, les paramètres de routage du trafic, les paramètres de surveillance des points de terminaison et la liste des points de terminaison de service auxquels le trafic sera acheminé.

Dans ARM, chaque profil Traffic Manager est représenté par une ressource ARM, de type « TrafficManagerProfiles » et gérée par le fournisseur de ressources « Microsoft.Network ». Au niveau de l'API REST, l'URI de chaque profil est la suivante :

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Comparaison avec l'API de gestion des services d’Azure Traffic Manager

L’utilisation d’ARM pour configurer les profils Traffic Manager permet d’accéder au même jeu de fonctionnalités Traffic Manager que l’API de gestion des services (ASM), à l’exception des limitations de la version préliminaire répertoriées ci-dessous.

Toutefois, si les fonctionnalités restent les mêmes, la terminologie a changé :

- La « méthode d’équilibrage de charge », qui détermine comment Traffic Manager choisit le point de terminaison auquel acheminer le trafic en réponse à une requête DNS, s’appelle désormais « méthode de routage du trafic ».

- La méthode de routage du trafic par « tourniquet » a été renommée en « pondérée ».

- La méthode de routage du trafic par « basculement » a été renommée en « priorité ».

## Limites
Il existe actuellement un petit nombre de limitations dans la prise en charge ARM d’Azure Traffic Manager :

- Les profils Traffic Manager créés à l’aide de l’API (non ARM) de gestion des services (ASM), des outils et du portail classique ne sont pas disponibles dans ARM et inversement. La migration des profils d’API ASM vers ARM n’est pas actuellement pas prise en charge. La seule possibilité consiste à supprimer puis recréer le profil.

- Dans le cadre d’une référence à une application web, les points de terminaison Traffic Manager de type « AzureEndpoints » ne peuvent référencer que l’[emplacement d’application web](../app-service-web/web-sites-staged-publishing.md) par défaut (production). Les emplacements personnalisés ne sont pas encore pris en charge. Pour contourner ce problème, vous pouvez configurer des emplacements personnalisés à l’aide du type « ExternalEndpoints ».

## Configuration d’Azure PowerShell

Ces instructions utilisent Microsoft Azure PowerShell qui doit être configuré comme indiqué ci-après.

Pour les utilisateurs non PowerShell ou non Windows, des opérations analogues peuvent être exécutées via l’interface de ligne de commande Azure. Toutes les opérations, à l’exception de la gestion des profils Traffic Manager « imbriqués », sont également disponibles par le biais du portail Azure.

### Étape 1
Installez la dernière version d’Azure PowerShell, disponible dans la page des téléchargements d’Azure.

### Étape 2 :
Connectez-vous à votre compte Azure.

	PS C:\> Login-AzureRmAccount

Vous devez indiquer vos informations d’identification.

### Étape 3
Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

	PS C:\> Set-AzureRmContext -SubscriptionName "MySubscription"

Pour afficher la liste des abonnements disponibles, utilisez l’applet de commande « Get-AzureRmSubscription ».

### Étape 4

Le service Traffic Manager est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré auprès de ce fournisseur de ressources pour pouvoir utiliser Traffic Manager via ARM. Cette opération n’est à effectuer qu’une fois pour chaque abonnement.

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### Étape 5
Créez un groupe de ressources (ignorez cette étape si vous en avez un) :

	PS C:\> New-AzureRmResourceGroup -Name MyRG -Location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que les ressources des profils Traffic Manager sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure Traffic Manager.

## Créer un profil Traffic Manager

Pour créer un profil Traffic Manager, utilisez l’applet de commande New-AzureRmTrafficManagerProfile :

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Les paramètres sont les suivants :

- Name : nom de la ressource ARM pour la ressource de profil Traffic Manager. Les profils d’un même groupe de ressources doivent avoir un nom unique. Ce nom est différent du nom DNS utilisé pour les requêtes DNS.

- ResourceGroupName : nom du groupe de ressources ARM qui contient la ressource de profil.

- TrafficRoutingMethod : spécifie la méthode de routage du trafic, utilisée pour déterminer le point de terminaison renvoyé en réponse aux requêtes DNS entrantes. Les valeurs possibles sont « Performance », « Weighted » ou « Priority ».

- RelativeDnsName : spécifie le nom DNS relatif fourni par ce profil Traffic Manager. Cette valeur est combinée au nom de domaine DNS utilisé par Azure Traffic Manager pour former le nom de domaine complet (FQDN) du profil. Par exemple, la valeur « contoso » donnera un profil Traffic Manager le nom complet « contoso.trafficmanager.net ».

- TTL : spécifie la durée de vie (TTL) du DNS en secondes. Ce paramètre indique aux programmes de résolution du DNS local et aux clients DNS la durée de mise en cache des réponses DNS fournies par ce profil Traffic Manager.

- MonitorProtocol : spécifie le protocole à utiliser pour surveiller l'intégrité des points de terminaison. La valeur possible est « HTTP » ou « HTTPS ».

- MonitorPort : spécifie le port TCP utilisé pour surveiller l’intégrité des points de terminaison.

- MonitorPath : spécifie le chemin d'accès relatif au nom de domaine des points de terminaison utilisé pour évaluer l’intégrité des points de terminaison.

L'applet de commande crée un profil Traffic Manager dans Azure Traffic Manager et renvoie un objet de profil correspondant. À ce stade, le profil ne contient aucun point de terminaison (voir [Ajouter des points de terminaison de Traffic Manager](#adding-traffic-manager-endpoints) pour plus de détails sur la façon d’ajouter des points de terminaison à un profil Traffic Manager.

## Récupérer un profil Traffic Manager

Pour récupérer un profil Traffic Manager, utilisez l’applet de commande Get-AzureRmTrafficManagerProfile :

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG

Cette applet de commande renvoie un objet de profil Traffic Manager.

## Mettre à jour un profil Traffic Manager [](#update-traffic-manager-profile)

La modification de profils Traffic Manager, par exemple par ajout/suppression des points de terminaison ou par changement des paramètres du profil, s’effectue en 3 étapes :

1.	Récupérez le profil à l’aide de Get-AzureRmTrafficManagerProfile (ou utilisez le profil renvoyé par New-AzureRmTrafficManagerProfile).

2.	Modifiez le profil en ajoutant/supprimant des points de terminaison, en changeant les paramètres des points de terminaison ou en actualisant les paramètres du profil. Les opérations de modification s’effectuent hors connexion. Seul l’objet local représentant le profil est modifié.

3.	Validez vos modifications à l’aide de l’applet de commande Set-AzureRmTrafficManagerProfile. Le profil modifié remplace par l’ancien dans Azure Traffic Manager.

Toutes les propriétés de profil peuvent être modifiées, à l’exception du paramètre RelativeDnsName de profil, qui n’est pas modifiable après la création du profil. (Pour modifier cette valeur, vous devez supprimer le profil, puis le recréer).

Par exemple, pour modifier la durée de vie (TTL) du profil :

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

## Ajouter des points de terminaison Traffic Manager
Il existe trois types de points de terminaison Traffic Manager :

1. Les points de terminaison Azure : ils représentent des services hébergés dans Azure.<BR>
2. Les points de terminaison externes : ils représentent des services hébergés en dehors d’Azure.<BR>
3. Les points de terminaison imbriqués : ils sont utilisés pour établir des hiérarchies de profils Traffic Manager imbriquées, et ce, pour mettre en place des configurations de routage de trafic avancées pour les applications plus complexes. Ils ne sont pas encore pris en charge via l’API ARM.<BR>

Dans ces trois cas, les points de terminaison peuvent être ajoutés de deux manières :<BR>

1. En utilisant un processus en 3 étapes similaire à celui qui est décrit dans [Mettre à jour un profil Traffic Manager](#update-traffic-manager-profile) : obtenez un objet de profil à l’aide de Get-AzureRmTrafficManagerProfile ; mettez-le à jour hors ligne pour ajouter un point de terminaison à l’aide de Add-AzureRmTrafficManagerEndpointConfig ; téléchargez les modifications dans Azure Traffic Manager à l’aide de Set-AzureRmTrafficManagerProfile. L’avantage de cette méthode est que plusieurs modifications de point de terminaison peuvent être apportées en une seule mise à jour.<BR>

2. Utilisation de l’applet de commande New-AzureRmTrafficManagerEndpoint. Elle permet d’ajouter un point de terminaison à un profil existant en une seule opération.

### Ajout de points de terminaison Azure

Les points de terminaison Azure font référence à des services hébergés dans Azure. Actuellement, 3 types de point de terminaison Azure sont pris en charge :<BR>
1. Azure Web Apps <BR>
2. Services cloud « classiques » (qui peuvent contenir un service PaaS ou des machines virtuelles IaaS)<BR>
3. Ressources ARM de Microsoft.Network/publicIpAddress (peuvent être attachées à un équilibreur de charge ou à une carte réseau de machine virtuelle). Notez que publicIpAddress doit se voir affecter un nom DNS pour être utilisé dans Traffic Manager.

Dans chaque cas :
 - Le service est spécifié à l’aide du paramètre « targetResourceId » de Add-AzureRmTrafficManagerEndpointConfig ou New-AzureRmTrafficManagerEndpoint.<BR>
 - Les paramètres « Target » et « EndpointLocation » ne doivent pas être spécifiés, ils sont implicites avec le paramètre TargetResourceId spécifié ci-dessus<BR>
 - La spécification du paramètre « Pondération » est facultative. Les poids ne sont utilisés que si le profil est configuré pour utiliser la méthode d’acheminement de trafic « Pondéré ». Autrement, ils sont ignorés. S’ils sont spécifiés, ils doivent se situer dans la plage 1 à 1 000. La valeur par défaut est « 1 ».<BR>
 - La spécification du paramètre « Priorité » est facultative. Les priorités sont utilisées uniquement si le profil est configuré pour utiliser la méthode d’acheminement de trafic « Priorité ». Dans le cas contraire, elles sont ignorées. Les valeurs valides sont comprises entre 1 et 1 000 (des valeurs inférieures ont une priorité plus élevée). Si elles sont spécifiées pour un point de terminaison, elles doivent l’être pour tous les points de terminaison. En cas d’omission, les valeurs par défaut à partir de 1, 2, 3, etc. sont appliquées dans l’ordre dans lequel les points de terminaison sont fournis.

#### Exemple 1 : ajout de points de terminaison Web App à l’aide de Add-AzureRmTrafficManagerEndpointConfig
Dans cet exemple, nous créer un profil Traffic Manager et ajouter deux points de terminaison Web App à l’aide de l’applet de commande Add-AzureRmTrafficManagerEndpointConfig, puis valider le profil mis à jour vers Azure Traffic Manager à l’aide de Set-AzureRmTrafficManagerProfile.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Exemple 2 : ajout d’un point de terminaison de service cloud « classique » à l’aide de New-AzureRmTrafficManagerEndpoint
Dans cet exemple, un point de terminaison Cloud Service « classique » est ajouté à un profil Traffic Manager. Notez que dans ce cas, nous avons choisi de spécifier le profil à l’aide du nom du profil et le nom de groupe de ressources, plutôt que de transmettre un objet de profil (les deux approches sont prises en charge).

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### Exemple 3 : ajout d’un point de terminaison publicIpAddress à l’aide de New-AzureRmTrafficManagerEndpoint
Dans cet exemple, une ressource d’adresse IP publique ARM est ajoutée au profil Traffic Manager. L’adresse IP publique doit avoir un nom DNS configuré et peut être liée à la carte réseau d’une machine virtuelle ou à un équilibreur de charge.

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### Ajout de points de terminaison externes
Traffic Manager utilise les points de terminaison externes pour diriger le trafic vers les services hébergés en dehors d’Azure. Comme avec les points de terminaison Azure, les points de terminaison externes peuvent être ajoutés à l’aide de Add-AzureRmTrafficManagerEndpointConfig suivi de Set-AzureRmTrafficManagerProfile ou New-AzureRMTrafficManagerEndpoint.

Lors de la spécification de points de terminaison externes :
 - Le nom de domaine du point de terminaison doit être spécifié en utilisant le paramètre « Target »<BR>
 - Le paramètre « EndpointLocation » est requis si la méthode d’acheminement de trafic « Performance » est utilisée, sinon il est facultatif. La valeur doit être un [nom de région Azure valide](https://azure.microsoft.com/regions/).<BR>
 - Les paramètres « Pondération » et « Priorité » sont facultatifs, comme pour les points de terminaison Azure.<BR>
 

#### Exemple 1 : ajout de points de terminaison externes à l’aide d’Add-AzureRmTrafficManagerEndpointConfig et Set-AzureRmTrafficManagerProfile
Dans cet exemple, nous allons créer un profil Traffic Manager, ajouter deux points de terminaison externes et valider les modifications.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Exemple 2 : ajout de points de terminaison externes à l’aide de New-AzureRmTrafficManagerEndpoint
Dans cet exemple, nous ajoutons un point de terminaison externe à un profil existant, spécifié à l’aide du nom du profil et du nom de groupe de ressources.

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

### Ajout de points de terminaison « imbriqués »

Traffic Manager vous permet de configurer un profil Traffic Manager (que nous appellerons profil « enfant ») en tant que point de terminaison dans un autre profil Traffic Manager (que nous appellerons profil « parent »).

L’imbrication de Traffic Manager vous permet de créer des schémas de routage du trafic et de basculement plus souples et plus puissants pour répondre aux besoins des déploiements plus vastes et plus complexes. [Ce billet de blog](https://azure.microsoft.com/blog/new-azure-traffic-manager-nested-profiles/) donne plusieurs exemples.

Les points de terminaison imbriqués sont configurés sur le profil parent, à l’aide d’un type de point de terminaison spécifique, « NestedEndpoints ». Lors de la spécification de points de terminaison imbriqués :
 - Le point de terminaison (par exemple, le profil enfant) doit être spécifié à l’aide du paramètre « targetResourceId » <BR>
 - Le paramètre « EndpointLocation » est requis si la méthode d’acheminement de trafic « Performance » est utilisée, sinon il est facultatif. La valeur doit être un [nom de région Azure valide](http://azure.microsoft.com/regions/).<BR>
 - Les paramètres « Pondération » et « Priorité » sont facultatifs, comme pour les points de terminaison Azure.<BR>
 - Le paramètre « MinChildEndpoints » est facultatif, la valeur par défaut est « 1 ». Si le nombre de points de terminaison disponibles dans le profil enfant tombe sous ce seuil, le profil parent considère le profil enfant comme étant « dégradé » et dirige le trafic vers les autres points de profil parents.<BR>


#### Exemple 1 : ajout de points de terminaison imbriqués à l’aide d’Add-AzureRmTrafficManagerEndpointConfig et Set-AzureRmTrafficManagerProfile

Dans cet exemple, nous créons des profils Traffic Manager enfant et parent, ajoutons l’enfant en tant que point de terminaison imbriqué dans le parent et validons les modifications. (Par souci de concision, nous n’ajoutons pas d’autres points de terminaison au profil enfant ou parent, bien que cela soit normalement nécessaire.)<BR>

	PS C:\> $child = New-AzureRmTrafficManagerProfile –Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $parent = New-AzureRmTrafficManagerProfile –Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName child-endpoint –TrafficManagerProfile $parent –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Exemple 2 : ajout de points de terminaison imbriqués à l’aide de New-AzureRmTrafficManagerEndpoint

Dans cet exemple, nous ajoutons un profil enfant existant en tant que point de terminaison imbriqué à un profil parent existant, spécifié à l’aide du nom du profil et du nom de groupe de ressources.

	PS C:\> $child = Get-AzureRmTrafficManagerEndpoint –Name child -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name child-endpoint –ProfileName parent -ResourceGroupName MyRG –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2


## Mettre à jour un point de terminaison Traffic Manager
Il existe deux façons de mettre à jour un point de terminaison Traffic Manager existant :<BR>

1. Obtenir le profil Traffic Manager à l’aide de Get-AzureRmTrafficManagerProfile, mettre à jour les propriétés de point de terminaison dans le profil et valider les modifications apportées à l’aide de Set-AzureRmTrafficManagerProfile. Cette méthode présente l’avantage de pouvoir mettre à jour plusieurs points de terminaison en une seule opération.<BR>
2. Obtenir le point de terminaison Traffic Manager à l’aide de Get-AzureRmTrafficManagerEndpoint, mettre à jour les propriétés de point de terminaison et valider les modifications apportées à l’aide de Set-AzureRmTrafficManagerEndpoint. Cette méthode est plus simple, car elle ne nécessite pas d’indexation dans le tableau de points de terminaison dans le profil.<BR>

#### Exemple 1 : mise à jour de points de terminaison à l’aide de Get-AzureRmTrafficManagerProfile et Set-AzureRmTrafficManagerProfile
Dans cet exemple, nous allons modifier l’ordre de priorité de deux points de terminaison dans un profil existant.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Exemple 2 : mise à jour d’un point de terminaison à l’aide de Get-AzureRmTrafficManagerEndpoint et Set-AzureRmTrafficManagerEndpoint
Dans cet exemple, nous allons modifier le poids d’un seul point de terminaison dans un profil existant.

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## Activation et désactivation des points de terminaison et des profils
Traffic Manager permet d’activer et de désactiver des points de terminaison individuels, tout en permettant l’activation et la désactivation des profils complets. Ces modifications peuvent être effectuées par obtention/mise à jour/définition du point de terminaison ou de ressources de profil. Pour rationaliser les opérations courantes, elles sont également prises en charge via les applets de commande dédiées.

#### Exemple 1 : activation et désactivation d’un profil Traffic Manager
Pour activer un profil Traffic Manager, utilisez Enable-AzureRmTrafficManagerProfile. Le profil peut être spécifié à l’aide d’un objet de profil (transmis via le pipeline ou à l’aide du paramètre « -TrafficManagerProfile »), ou en spécifiant directement le nom du profil et le nom de groupe de ressources comme dans cet exemple.

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

De même, pour désactiver un profil Traffic Manager :

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

L’applet de commande Disable-AzureRmTrafficManagerProfile vous invite à confirmer l’opération. Ce message peut être supprimé à l’aide du paramètre « -Force ».

#### Exemple 2 : activation et désactivation d’un point de terminaison Traffic Manager
Pour activer un point de terminaison Traffic Manager, utilisez Enable-AzureRmTrafficManagerEndpoint. Le point de terminaison peut être spécifié à l’aide d’un objet TrafficManagerEndpoint (transmis via le pipeline ou à l’aide du paramètre « -TrafficManagerEndpoint »), ou par l’utilisation du nom de point de terminaison, du type de point de terminaison, du nom du profil et du nom de groupe de ressources :

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG

De même, pour désactiver un point de terminaison Traffic Manager :

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force

Comme avec Disable-AzureRmTrafficManagerProfile, l’applet de commande Disable-AzureRmTrafficManagerEndpoint inclut une invite de confirmation qui peut être supprimée à l’aide du paramètre « -Force ».

## Supprimer un point de terminaison Traffic Manager
Un des moyens de supprimer un point de terminaison Traffic Manager consiste à récupérer l’objet de profil (à l’aide de Get-AzureRmTrafficManagerProfile), à mettre à jour la liste des points de terminaison de l’objet de profil local, puis à valider vos modifications (à l’aide de Set-AzureRmTrafficManagerProfile). Cette méthode permet à plusieurs modifications de point de terminaison d’être validées simultanément.

Une autre façon de supprimer des points de terminaison individuels consiste à utiliser l’applet de commande Remove-AzureRmTrafficManagerEndpoint :

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
	
Cette applet de commande vous invite à confirmer l’opération à moins que le paramètre « -Force » soit utilisé pour supprimer l’invite.

## Supprimer un profil Traffic Manager
Pour supprimer un profil Traffic Manager, utilisez l’applet de commande AzureRmTrafficManagerProfile, en spécifiant le nom du profil et le nom du groupe de ressources :

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG [-Force]

Cette applet de commande vous demande de confirmer l'opération. Le commutateur facultatif « -Force » permet de supprimer l'invite de confirmation. Le profil à supprimer peut également être spécifié par un objet de profil :

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> Remove-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Cette séquence peut également être canalisée :

	PS C:\> Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]

## Étapes suivantes

[Surveillance avec Traffic Manager](traffic-manager-monitoring.md)

[Considérations sur les performances de Traffic Manager](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_0608_2016-->