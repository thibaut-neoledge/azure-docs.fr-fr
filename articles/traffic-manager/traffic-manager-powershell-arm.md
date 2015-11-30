<properties
   pageTitle="Prise en charge d’Azure Resource Manager pour la version préliminaire d’Azure Traffic Manager | Microsoft Azure"
   description="Utilisation de Powershell pour Traffic Manager avec Azure Resource Manager (ARM) en version préliminaire"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />



# Prise en charge d’Azure Resource Manager pour la version préliminaire d’Azure Traffic Manager
Azure Resource Manager (ARM) est la nouvelle infrastructure de gestion des services dans Azure. Les outils et API d’Azure Resource Manager permettent désormais de gérer les profils Azure Traffic Manager. Pour plus d’informations sur Azure Resource Manager, consultez [Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure](../azure-preview-portal-using-resource-groups.md).

>[AZURE.NOTE]La prise en charge d’ARM dans Traffic Manager est actuellement en version préliminaire. Ceci concerne également l’API REST, Azure PowerShell, l’interface de ligne de commande Azure et le Kit de développement logiciel (SDK) .NET.



## Modèle de ressource

Azure Traffic Manager est configuré à l'aide d'un ensemble de paramètres appelé profil Traffic Manager. Ce profil contient les paramètres DNS, les paramètres de routage du trafic, les paramètres de surveillance des points de terminaison et la liste des points de terminaison de service auxquels le trafic sera acheminé.

Dans ARM, chaque profil Traffic Manager est représenté par une ressource ARM, de type « TrafficManagerProfiles » et gérée par le fournisseur de ressources « Microsoft.Network ». Au niveau de l'API REST, l'URI de chaque profil est la suivante :

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Comparaison avec l'API de gestion des services d’Azure Traffic Manager

L'utilisation d’ARM pour configurer les profils Traffic Manager permet d'accéder au même jeu de fonctionnalités Traffic Manager que l’API de gestion des services (non ARM), à l'exception des limitations de la version préliminaire indiquées ci-dessous.

Toutefois, si les fonctionnalités restent les mêmes, la terminologie a changé :

- La « méthode d’équilibrage de charge », qui détermine comment Traffic Manager choisit le point de terminaison auquel acheminer le trafic en réponse à une requête DNS, s’appelle désormais « méthode de routage du trafic ».

- La méthode de routage du trafic par « tourniquet » a été renommée en « pondérée ».

- La méthode de routage du trafic par « basculement » a été renommée en « priorité ».

## Limitations de la version préliminaire
Comme la prise en charge d’Azure Resource Manager dans Traffic Manager est un service actuellement en version préliminaire, il présente quelques limitations :

- Les profils Traffic Manager créés à l'aide de l’API de gestion des services (non ARM), des outils et du portail ne sont pas disponibles dans ARM, et inversement. La migration de profils entre une API non ARM et une API ARM n'est pas prise en charge.

- 	L'API REST ne prend pas en charge l’application de correctifs aux profils Traffic Manager. Pour mettre à jour une propriété d’un profil, vous devez récupérer ce dernier (opération GET) et remettre en place le profil modifié (opération PUT).
- 	Seuls les points de terminaison « externes » sont pris en charge. Ceux-ci permettent toujours d’utiliser Traffic Manager avec les services Azure. Dans ce cas, ces points de terminaison sont facturés au tarif de point de terminaison interne. (La seule conséquence de l'utilisation de points de terminaison externes est qu'ils ne sont pas désactivés ou supprimés automatiquement si le service Azure sous-jacent est désactivé ou supprimé. Vous devez désactiver ou supprimer le point de terminaison manuellement).
-	Azure Traffic Manager n'est pas encore disponible dans le portail Azure. Il ne l’est que sur le portail classique.

## Configuration d’Azure PowerShell

Ces instructions utilisent Microsoft Azure PowerShell qui doit être configuré comme indiqué ci-après.

Ceux qui n’utilisent pas PowerShell peuvent effectuer les mêmes opérations dans d’autres interfaces.

### Étape 1
Installez la dernière version d’Azure PowerShell, disponible dans la page des téléchargements d’Azure.
### Étape 2
Activez le mode PowerShell pour utiliser les applets de commande ARM. Pour plus d'informations, consultez Utilisation de Windows PowerShell avec Resource Manager.

	PS C:\> Switch-AzureMode -Name AzureResourceManager
### Étape 3
Connectez-vous à votre compte Azure.

	PS C:\> Add-AzureAccount

Vous devez indiquer vos informations d’identification.

### Étape 4
Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

	PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Pour afficher la liste des abonnements disponibles, utilisez l'applet de commande « Get-AzureSubscription ».

### Étape 5

 Le service Traffic Manager est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré auprès de ce fournisseur de ressources pour pouvoir utiliser Traffic Manager via ARM. Cette opération n’est à effectuer qu’une fois pour chaque abonnement.

	PS C:\> Register-AzureProvider –ProviderNamespace Microsoft.Network

### Étape 6
Créez un groupe de ressources (ignorez cette étape si vous en avez un) :

	PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Toutefois, étant donné que les ressources des profils Traffic Manager sont globales et non régionales, le choix de l’emplacement du groupe de ressources n’a aucun impact sur Azure Traffic Manager.

## Créer un profil Traffic Manager

Pour créer un profil Traffic Manager, utilisez l'applet de commande New-AzureTrafficManagerProfile :

	PS C:\> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Les paramètres sont les suivants :

- Name : nom de la ressource ARM pour la ressource de profil Traffic Manager. Les profils d’un même groupe de ressources doivent avoir un nom unique. Ce nom est différent du nom DNS utilisé pour les requêtes DNS.

-	ResourceGroupName : nom du groupe de ressources ARM qui contient la ressource de profil.

-	TrafficRoutingMethod : spécifie la méthode de routage du trafic, utilisée pour déterminer le point de terminaison renvoyé en réponse aux requêtes DNS entrantes. Les valeurs possibles sont « Performance », « Weighted » ou « Priority ».

-	RelativeDnsName : spécifie le nom DNS relatif fourni par ce profil Traffic Manager. Cette valeur est combinée au nom de domaine DNS utilisé par Azure Traffic Manager pour former le nom de domaine complet (FQDN) du profil. Par exemple, la valeur « contoso » donnera à un profil Traffic Manager le nom complet « contoso.trafficmanager.net ».

-	TTL : spécifie la durée de vie (TTL) du DNS en secondes. Ce paramètre indique aux programmes de résolution du DNS local et aux clients DNS la durée de mise en cache des réponses DNS fournies par ce profil Traffic Manager.

-	MonitorProtocol : spécifie le protocole à utiliser pour surveiller l'intégrité des points de terminaison. La valeur possible est « HTTP » ou « HTTPS ».

-	MonitorPort : spécifie le port TCP utilisé pour surveiller l’intégrité des points de terminaison.

-	MonitorPath : spécifie le chemin d'accès relatif au nom de domaine des points de terminaison utilisé pour évaluer l’intégrité des points de terminaison.

L'applet de commande crée un profil Traffic Manager dans Azure Traffic Manager et renvoie un objet de profil correspondant. À ce stade, le profil ne contient aucun point de terminaison. Pour savoir comment ajouter des points de terminaison à un profil Traffic Manager, consultez [Mettre à jour un profil Traffic Manager](#update-a-traffic-manager-profile).

## Récupérer un profil Traffic Manager

Pour récupérer un profil Traffic Manager, utilisez l'applet de commande Get-AzureTrafficManagerProfile :

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

Cette applet de commande renvoie un objet de profil Traffic Manager.

## Mettre à jour un profil Traffic Manager [](#update-traffic-manager-profile)

La modification de profils Traffic Manager, par exemple par ajout/suppression des points de terminaison ou par changement des paramètres du profil, s’effectue en 3 étapes :

1.	Récupérez le profil à l'aide de Get-AzureTrafficManagerProfile (ou utilisez le profil renvoyé par New-AzureTrafficManagerProfile).

2.	Modifiez le profil en ajoutant/supprimant des points de terminaison, en changeant les paramètres des points de terminaison ou en actualisant les paramètres du profil. Ces modifications s’effectuent hors connexion. Seul l'objet local représentant le profil est modifié.

3.	Validez vos modifications à l'aide de l'applet de commande Set-AzureTrafficManagerProfile. Le profil modifié remplace par l’ancien dans Azure Traffic Manager.

Les exemples ci-dessous vous permettront d’y voir plus clair :

### Ajouter des points de terminaison à un profil

Vous pouvez ajouter des points de terminaison à un profil Traffic Manager à l'aide de l'applet de commande « Add-AzureTrafficManagerEndpointConfig » :

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Les paramètres d’Add-AzureTrafficManagerEndpointConfig sont les suivants :

- EndpointName : nom du point de terminaison. Les points de terminaison d’un même profil doivent avoir un nom unique. Cela permet de référencer le point de terminaison lors d'opérations de gestion des services. Il ne s’agit pas du nom DNS du point de terminaison.

-	TrafficManagerProfile : profil Traffic Manager auquel le point de terminaison est ajouté.

-	Type : type du point de terminaison Traffic Manager. Actuellement, l’API ARM ne prend en charge que le type « ExternalEndpoint ». Consultez [Limitations de la version préliminaire](#preview-limitations).

-	Target : nom DNS complet du point de terminaison. Traffic Manager renvoie cette valeur dans les réponses DNS pour diriger le trafic vers ce point de terminaison.

-	EndpointStatus : indique l'état du point de terminaison. Si le point de terminaison est activé, son intégrité est vérifiée et il est inclus dans la méthode de routage du trafic. La valeur possible est « Enabled » ou « Disabled ».

-	Weight : spécifie la pondération attribuée au point de terminaison. Ce paramètre n’est utilisé que si le profil Traffic Manager est configuré pour utiliser la méthode de routage de trafic « pondérée ». Les valeurs possibles varient entre 1 et 1000.

-	Priority : spécifie la priorité de ce point de terminaison en cas d’utilisation de la méthode de routage de trafic « priorité ». La priorité doit être comprise entre 1 et 1000. Plus la valeur est basse, plus la priorité est élevée.

-	EndpointLocation : spécifie l'emplacement du point de terminaison externe, à utiliser avec la méthode de routage du trafic « Performance ». Pour obtenir la liste des emplacements possibles, consultez Get-AzureLocation.

Les paramètres EndpointStatus, Weight et Priority sont facultatifs. En cas d'omission, ils ne sont pas transmis par PowerShell et les valeurs par défaut du serveur sont appliquées.

### Supprimer des points de terminaison d’un profil

Pour supprimer un point de terminaison d’un profil, utilisez l’applet de commande « Remove-AzureTrafficmanagerEndpointConfig », en spécifiant le nom du point de terminaison à supprimer :

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

La séquence des opérations d’ajout ou de suppression de points de terminaison peut également être « canalisée », en transmettant le profil par le canal et non comme paramètre. Par exemple :

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### Modifier les paramètres d’un profil ou d’un point de terminaison

Les paramètres d’un profil et d’un point de terminaison sont modifiables. Les modifications doivent être validées à l'aide de Set-AzureTrafficManagerProfile. Seule exception : le paramètre RelativeDnsName n’est pas modifiable après la création du profil. Pour modifier cette valeur, vous devez supprimer puis recréer le profil. Par exemple, pour modifier le paramètre TTL du profil et l'état du premier point de terminaison :

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> $profile.Ttl = 300
	PS C:\> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### Supprimer un profil Traffic Manager
Pour supprimer un profil Traffic Manager, utilisez l'applet de commande Remove-AzureTrafficManagerProfile, en spécifiant le nom du profil et le nom du groupe de ressources :

	PS C:\> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

Cette applet de commande vous demande de confirmer l'opération. Le commutateur facultatif « -Force » permet de supprimer l'invite de confirmation. Le profil à supprimer peut également être spécifié par un objet de profil :

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Cette séquence peut également être canalisée :

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## Étapes suivantes

[Surveillance avec Traffic Manager](traffic-manager-monitoring.md)

[Considérations sur les performances de Traffic Manager](traffic-manager-performance-considerations.md)
 

<!---HONumber=Nov15_HO4-->