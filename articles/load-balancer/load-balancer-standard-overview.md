---
title: "Présentation de la référence Standard d’Azure Load Balancer | Microsoft Docs"
description: "Présentation de la référence Standard d’Azure Load Balancer"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c863dbdcb242bdea208f7e72f6c1f61b5ba04844
ms.openlocfilehash: 77d157800d71d1afc3411553b31c1182a1d0bc28
ms.contentlocale: fr-fr
ms.lasthandoff: 09/22/2017

---

# <a name="azure-load-balancer-standard-overview-preview"></a>Présentation de la référence Standard d’Azure Load Balancer (préversion)

Utilisées ensemble, la référence SKU Standard d’Azure Load Balancer et la référence SKU Standard d’adresse IP publique vous permettent de créer des architectures hautement évolutives et fiables.  Les applications utilisant la référence Standard de Load Balancer peuvent tirer parti des nouvelles fonctionnalités en plus d’une faible latence, d’un débit élevé et d’une mise à l’échelle pour des millions de flux de toutes les applications TCP et UDP.

>[!NOTE]
> La référence SKU Standard de Load Balancer est actuellement en préversion. Les niveaux de disponibilité et de fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour vos services de production, utilisez la [référence SKU De base de Load Balancer](load-balancer-overview.md) de la version publique.

## <a name="why-use-load-balancer-standard"></a>Pourquoi utiliser la référence Standard de Load Balancer ?

Utilisez la référence Standard de Load Balancer pour l’intégralité de la plage de centres de données virtuels, allant de déploiements à petite échelle à des architectures multizones étendues et complexes, pour tirer parti de ses capacités, détaillées ci-après :

- [Une mise à l’échelle d’entreprise](#enterprisescale) peut être obtenue avec la référence Standard de Load Balancer.  Elle peut être utilisée avec n’importe quelle instance de machine virtuelle dans un réseau virtuel et avec un maximum de 1 000 instances de machine virtuelle.

- [De nouvelles informations de diagnostic](#diagnosticinsights) sont disponibles pour vous permettre de comprendre, gérer et résoudre les problèmes de ce composant essentiel de votre centre de données virtuel. Utilisez Azure Monitor (préversion) pour afficher, filtrer et regrouper les nouveaux indicateurs multidimensionnels pour mesurer en continu l’intégrité du chemin de données du serveur frontal à la machine virtuelle, les sondes d’intégrité de point de terminaison, les tentatives de connexion TCP et les connexions sortantes.

- [Les groupes de sécurité réseau](#nsg) sont désormais nécessaires pour toute instance de machine virtuelle associée aux références SKU Standard de Load Balancer ou d’adresse IP publique et assurent une meilleure sécurité.

- [Les ports HA offrent haute fiabilité](#highreliability) et mise à l’échelle pour les appliances virtuelles réseau et d’autres scénarios d’application. Les ports HA équilibrent la charge de tous les ports d’un serveur frontal Load Balancer interne sur un pool d’instances de machine virtuelle.

- [Les connexions sortantes](#outboundconnections) utilisent désormais un nouveau modèle d’allocation de port SNAT qui fournit une meilleure résilience et mise à l’échelle.

- [La référence Standard de Load Balancer avec des zones de disponibilité](#availabilityzones) peut être utilisée pour construire des architectures zonales et redondantes dans une zone avec l’équilibrage de charge entre les zones. Vous pouvez obtenir une redondance dans une zone sans dépendance sur les enregistrements DNS : une seule adresse IP est simplement redondante dans une zone par défaut et peut atteindre toutes les machines virtuelles d’un réseau virtuel dans une région sur toutes les zones de disponibilité.


Vous pouvez utiliser la référence Standard de Load Balancer dans des configurations publiques ou internes, qui continuent à prendre en charge les scénarios de base suivants :

- Équilibrer la charge du trafic entrant sur des instances de serveur principal saines.
- Réacheminer les ports du trafic entrant sur une instance de serveur principal unique.
- Traduire le trafic sortant d’une adresse IP privée dans le réseau en une adresse IP publique.

### <a name = "enterprisescale"></a>Mise à l’échelle d’entreprise

 Utilisez la référence Standard de Load Balancer lors de la conception de votre centre de données virtuel hautes performances. Vous pouvez utiliser des instances de machine virtuelle autonomes ou des groupes de machines virtuelles identiques avec 1 000 instances maximum dans un pool de serveur principal et prendre en charge toute application TCP ou UDP. Avec la référence Standard de Load Balancer, votre application peut toujours exploiter la faible latence de réacheminement, les performances de débit élevé et la mise à l’échelle de millions de flux sur un service Azure entièrement géré.
 
La référence Standard de Load Balancer peut réacheminer le trafic sur toute instance de machine virtuelle dans un réseau virtuel d’une région. Les tailles de pool de serveur principal peuvent comporter 1 000 instances maximum, avec n’importe quelle combinaison de :

- machines virtuelles autonomes sans groupe à haute disponibilité
- machines virtuelles autonomes avec groupes à haute disponibilité
- groupes de machines virtuelles identiques (groupe de machines virtuelles identiques) avec 1 000 instances maximum
- plusieurs groupes de machines virtuelles identiques
- mélange de machines virtuelles et de groupes de machines virtuelles identiques.

Les groupes à haute disponibilité ne sont plus requis, mais vous pouvez choisir d’en utiliser pour bénéficier des autres avantages qu’ils proposent.

### <a name = "diagnosticinsights"></a>Informations de diagnostic

La référence Standard de Load Balancer offre de nouvelles fonctionnalités de diagnostic multidimensionnel pour les configurations Load Balancer publiques et internes. Ces nouveaux indicateurs sont fournis via Azure Monitor (préversion) et utilisent toutes les fonctions connexes, y compris la possibilité d’intégration à divers consommateurs en aval.

| Mesure | Description |
| --- | --- |
| Disponibilité VIP | La référence Standard de Load Balancer teste en continu le chemin de données d’une région vers le serveur frontal Load Balancer, et enfin vers la pile SDN prenant en charge votre machine virtuelle. Tant que les instances saines restent, la mesure suit le même chemin que le trafic avec charge équilibré de vos applications et valide le chemin de données que vos clients peuvent utiliser. La mesure est invisible pour votre application et n’interfère pas avec celle-ci.|
| Disponibilité DIP | La référence Standard de Load Balancer utilise un service de détection d’intégrité distribué qui surveille l’intégrité du point de terminaison de votre application en fonction de ce que vous avez configuré.  Cet indicateur fournit un agrégat ou une vue filtrée par point de terminaison de chaque point de terminaison d’instance dans le pool Load Balancer.  Vous pouvez observer comment Load Balancer voit l’intégrité de votre application comme indiqué par votre configuration de sonde d’intégrité.
| Paquets SYN | La référence Standard de Load Balancer ne termine pas les connexions TCP et n’interagit pas avec les flux de paquets UDP et TCP ; les flux et leurs établissements de liaisons sont toujours entre la source et l’instance de machine virtuelle. Pour mieux résoudre les problèmes posés par tous vos scénarios de protocole TCP, vous pouvez utiliser cet indicateur afin de comprendre le nombre de tentatives de connexion TCP effectuées. Cet indicateur fournit le nombre de paquets SYN TCP qui ont été reçus et peut indiquer les clients qui tentent d’établir des connexions à votre service.|
| Connexions SNAT | La référence Standard de Load Balancer indique le nombre de connexions sortantes usurpées sur le serveur frontal d’adresse IP publique.  Les ports SNAT constituent une ressource qui n’est pas inépuisable, et cet indicateur peut donner une idée de l’importance du rôle joué par SNAT dans votre application pour les connexions sortantes.|
| Compteurs d’octets | La référence Standard de Load Balancer indique les données traitées par serveur frontal.|
| Compteurs de paquets | La référence Standard de Load Balancer indique les paquets traités par serveur frontal.|

### <a name = "highreliability"></a>Haute fiabilité

Configurez des règles d’équilibrage de charge pour mettre à l’échelle votre application et qu’elle soit très fiable.  Vous pouvez configurer des règles pour chaque port ou vous pouvez utiliser les nouveaux ports HA pour équilibrer tout le trafic, quel que soit le numéro de port TCP ou UDP.  

Vous pouvez utiliser la nouvelle fonctionnalité de ports HA pour déverrouiller un éventail de scénarios, y compris la haute disponibilité et la mise à l’échelle d’appliances virtuelles réseau et d’autres scénarios où il est difficile ou pas souhaitable de spécifier les ports individuels. Les ports HA assurent la redondance et la mise à l’échelle en autorisant autant d’instances que nécessaires plutôt que d’être limité à des scénarios actifs/passifs. Vos configurations de sonde d’intégrité protègent votre service en réacheminant le trafic uniquement sur les instances saines.

Les fournisseurs d’appliances virtuelles réseau peuvent offrir des scénarios résilients et entièrement pris en charge par le fournisseur en supprimant un point de défaillance unique pour leurs clients et en autorisant plusieurs instances actives pour la mise à l’échelle. Vous pouvez mettre à l’échelle deux ou plusieurs instances si votre appliance autorise de telles configurations. Votre fournisseur d’appliances virtuelles réseau doit donner davantage d’instructions pour ces scénarios.

### <a name = "availabilityzones"></a>Zones de disponibilité

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Développez la résilience de votre application avec l’utilisation de zones de disponibilité dans des régions prises en charge. Les zones de disponibilité sont actuellement en préversion dans des régions spécifiques et nécessitent un abonnement supplémentaire.

### <a name="automatic-zone-redundancy"></a>Redondance dans une zone automatique

Vous pouvez choisir si Load Balancer doit fournir un serveur frontal zonal ou redondant dans une zone pour chacune de vos applications.  Vous pouvez facilement créer une redondance dans une zone avec la référence Standard de Load Balancer.  Une adresse IP de serveur frontal unique est automatiquement redondante dans une zone.  Un serveur frontal redondant dans une zone est servi par toutes les zones de disponibilité d’une région simultanément. Cette opération crée un chemin de données redondant dans une zone pour les connexions entrantes et sortantes. La redondance dans une zone dans Azure ne nécessite pas plusieurs adresses IP et enregistrements DNS. 

Cette redondance dans une zone est disponible pour les serveurs frontaux publics ou internes. Votre adresse IP publique, ainsi que votre adresse IP privée de serveur frontal Load Balancer interne peut être redondante dans une zone.

Créez une adresse IP publique redondante dans une zone avec le code suivant (ajoutez « sku » aux modèles Resource Manager existants) :

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Créez une configuration d’adresse IP de serveur frontal redondant dans une zone Load Balancer interne avec le code suivant (ajoutez « sku » aux modèles Resource Manager existants) :

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Si votre serveur frontal d’adresse IP publique est redondant dans une zone, toutes les connexions sortantes établies à partir d’instances de machine virtuelle deviennent automatiquement redondantes dans une zone et sont protégées des défaillances de zone.  Votre allocation de port SNAT survit aux défaillances de zone.

#### <a name="cross-zone-load-balancing"></a>Équilibrage de charge entre les zones

L’équilibrage de charge entre les zones est disponible dans une région pour le pool de serveur principal, ce qui offre une flexibilité maximale à vos instances de machine virtuelle.  Un serveur frontal peut fournir des flux à toutes les machines virtuelles dans le réseau virtuel, quelle que soit la zone de disponibilité de l’instance de machine virtuelle.

En outre, vous pouvez également choisir de spécifier une zone particulière pour vos instances de serveur frontal et de serveur principal, afin d’aligner votre chemin de données et vos ressources avec une zone spécifique.

Étant donné que les sous-réseaux et réseaux virtuels ne sont jamais limités par une zone, il vous suffit de définir un pool de serveur principal avec vos instances de machine virtuelle souhaitées et la configuration est terminée.

#### <a name="zonal-deployments"></a>Déploiements zonaux

Si vous le souhaitez, vous pouvez également aligner le serveur frontal sur une zone spécifique en définissant un serveur frontal zonal.  Un serveur frontal zonal est servi par la zone de disponibilité unique désignée seulement, et lorsqu’il est combiné à des instances de machine virtuelle zonales, vous pouvez aligner des ressources sur des zones spécifiques.

Créez une adresse IP publique zonale dans une zone de disponibilité 1 avec le code suivant (ajoutez « zones » et « sku »aux modèles Resource Manager existants) :

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Créez un serveur frontal Load Balancer interne dans la zone de disponibilité 1 avec le code suivant (ajoutez « sku » aux modèles Resource Manager existants et placez « zones » dans la ressource enfant de configuration d’adresse IP de serveur frontal) :

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

En outre, vous pouvez utiliser l’équilibrage de charge entre les zones pour votre serveur principal en plaçant vos instances de machine virtuelle d’un réseau virtuel dans un pool de serveur principal.

La ressource Standard de Load Balancer elle-même est toujours régionale et redondante dans une zone, là où les zones de disponibilité sont prises en charge. Un serveur frontal Load Balancer interne ou une adresse IP publique qui n’a pas été assigné à une zone peut être déployé dans n’importe quelle région, quelle que soit la prise en charge des zones de disponibilité. Si une région obtient des zones de disponibilité plus tard, un serveur frontal Load Balancer interne ou une adresse IP publique déjà déployé devient automatiquement redondant dans une zone. Le chemin de données redondant dans une zone n’implique pas 0 % de pertes de paquets.

### <a name = "nsg"></a>Groupes de sécurité réseau

Les références Standard de Load Balancer et Standard d’adresse IP publique s’intègrent totalement au réseau virtuel, et des groupes de sécurité réseau sont désormais nécessaires. Le groupe de sécurité réseau autorise la mise en liste verte des flux, ainsi que les configurations dans lesquelles les clients contrôlent totalement les moments où le trafic vers leur déploiement est possible via le groupe de sécurité réseau plutôt que lorsque d’autres configurations sont terminées.

Associez un groupe de sécurité réseau avec des sous-réseaux ou des cartes d’interface réseau d’instances de machine virtuelle dans le pool de serveur principal.  Cela s’applique aux références Standard de Load Balancer et Standard d’adresse IP publique en cas d’utilisation en tant qu’adresse IP publique de niveau d’instance. Vous devez explicitement mettre en liste verte le trafic que vous souhaitez autoriser avec le groupe de sécurité réseau pour qu’il puisse circuler.

Pour plus d’informations sur les groupes de sécurité réseau et comment les appliquer à votre scénario, consultez [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Connexions sortantes

 La référence Standard de Load Balancer fournit des connexions sortantes pour les machines virtuelles à l’intérieur du réseau virtuel en cas d’association à un équilibrage de charge utilisant un algorithme SNAT d’usurpation d’identité de port.

Quand une ressource de Load Balancer publique est associée à des instances de machine virtuelle, la source de chaque connexion sortante est réécrite depuis l’espace d’adressage IP privé du réseau virtuel dans l’adresse IP publique du serveur frontal Load Balancer.  La référence Standard de Load Balancer utilise un nouvel algorithme SNAT d’usurpation d’identité de port pour davantage de robustesse et une meilleure mise à l’échelle.  

En outre, lorsqu’elles sont utilisées avec un serveur frontal redondant dans une zone, les connexions sortantes sont également redondantes dans une zone et les allocations de port SNAT survivent aux défaillances de zone.

Le nouvel algorithme de la référence Standard de Load Balancer préalloue les ports SNAT à chaque interface réseau de machine virtuelle au moment où ils sont ajoutés au pool selon les niveaux suivants :

| Taille de pool de serveur principal | Ports SNAT préalloués |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Les ports SNAT ne traduisent pas directement en nombre de connexions. Un port SNAT peut être réutilisé pour plusieurs destinations uniques.  Consultez l’article sur les [connexions sortantes](load-balancer-outbound-connections.md) pour plus d’informations.

Si votre pool de serveur principal est augmenté et passe d’une taille à la taille supérieure suivante, la moitié de vos ports alloués est récupérée. Toute connexion associée à un port récupéré expire et doit être établie une nouvelle fois. Toute nouvelle tentative de connexion réussit immédiatement. Si votre pool de serveur principal est réduit et passe d’une taille à la taille inférieure suivante, les ports SNAT disponibles se développent et les connexions existantes ne sont pas affectées.

La référence Standard de Load Balancer dispose également d’une option de configuration supplémentaire, fonctionnant sur une base par règle pour fournir un contrôle client sur le serveur frontal utilisé pour l’algorithme SNAT d’usurpation d’identité de port lorsque plusieurs serveurs frontaux sont disponibles.

Enfin, lorsque seule la référence Standard de Load Balancer traite les instances de machine virtuelle, les connexions SNAT sortantes sont indisponibles. Vous pouvez restaurer cette capacité explicitement en assignant également des instances de machine virtuelle à un Load Balancer public ou en assignant une adresse IP publique en tant qu’adresse IP publique de niveau d’instance directement à chaque instance de machine virtuelle. Cela peut être nécessaire pour certains scénarios d’application et de système d’exploitation. 

### <a name="port-forwarding"></a>Réacheminement de port

 Les références Standard et De base de Load Balancer permettent de configurer des règles NAT entrantes pour mapper un port de serveur frontal à une instance de serveur principal individuelle.  Cette fonctionnalité peut être utilisée de nombreuses façons, y compris pour l’exposition des points de terminaison de protocole RDP (Remote Desktop Protocol), des points de terminaison SSH ou pour divers autres scénarios d’application.

La référence Standard de Load Balancer continue de fournir la fonction de réacheminement de port via des règles NAT entrantes.  Utilisées avec des serveurs frontaux redondants dans une zone, les règles NAT entrantes deviennent redondantes dans une zone et survivent aux défaillances de zone.

### <a name="multiple-frontends"></a>Plusieurs serveurs frontaux

Configurez plusieurs serveurs frontaux pour une conception flexible dans laquelle les applications ont besoin que plusieurs adresses IP individuelles soient exposées (par exemple, des sites web TLS ou des points de terminaison de groupes de disponibilité AlwaysOn SQL).  Pour plus d’informations, cliquez [ici](load-balancer-multivip-overview.md).

La référence Standard de Load Balancer continue de fournir plusieurs serveurs frontaux quand il est souhaitable d’exposer un point de terminaison d’application spécifique sur une adresse IP unique.

 Pour plus d’informations, cliquez [ici](load-balancer-multivip-overview.md).

## <a name = "sku"></a>À propos des références SKU

Les références SKU sont uniquement disponibles dans un modèle de déploiement Azure Resource Manager.  Cette préversion introduit deux références SKU (De base et Standard) pour les ressources de Load Balancer et d’adresse IP publique.  Les fonctionnalités, les caractéristiques de performances, les limitations et certains comportements intrinsèques diffèrent d’une référence SKU à l’autre. Les machines virtuelles peuvent être utilisées avec l’une ou l’autre des références SKU. Pour les ressources de Load Balancer et d’adresse IP publique, les références SKU restent des attributs facultatifs, et lorsqu’elles sont omises, les valeurs par défaut sont celles de De base.

>[!IMPORTANT]
>La référence SKU d’une ressource n’est pas mutable.  Vous ne pouvez pas modifier la référence SKU d’une ressource existante.  

### <a name="load-balancer"></a>Load Balancer

La [ressource de Load Balancer existante](load-balancer-overview.md) devient la référence SKU De base et reste disponible publiquement et inchangée.

La référence SKU Standard de Load Balancer est une nouvelle offre et actuellement en préversion. La version d’API du 01-08-2017 pour Microsoft.Network/loadBalancers introduit les références SKU à la ressource.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Lorsqu’elle est utilisée dans une région qui propose également des zones de disponibilité, la référence Standard de Load Balancer est automatiquement résiliente dans la zone, sauf si elle a été déclarée comme étant zonale.

### <a name="public-ip"></a>Adresse IP publique

La [ressource d’adresse IP publique existante](../virtual-network/virtual-network-ip-addresses-overview-arm.md) devient la référence SKU De base et reste disponible publiquement avec toutes ses fonctionnalités, caractéristiques de performances et limitations.

La référence SKU Standard d’adresse IP publique est une nouvelle offre et actuellement en préversion. La version d’API du 01-08-2017 pour les ressources Microsoft.Network/publicIPAddresses introduit les références SKU.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Contrairement à la référence De base d’adresse IP publique qui propose plusieurs méthodes d’allocation, la référence Standard d’adresse IP publique offre toujours une méthode d’allocation statique.

Lorsqu’elle est utilisée dans une région qui propose également des zones de disponibilité, la référence Standard d’adresse IP est automatiquement résiliente dans la zone, sauf si elle a été déclarée comme étant zonale.

## <a name="migration-between-skus"></a>Migration entre les références SKU

Si vous souhaitez passer d’une référence SKU de ressource à l’autre, procédez comme suit :

### <a name="migrating-from-basic-to-standard-sku"></a>Migration de la référence SKU De base à la référence SKU Standard

1. Créez une ressource Standard (Load Balancer et adresses IP publiques si nécessaire) et recréez vos règles et définitions de sonde.
2. Supprimez les ressources de la référence SKU De base (adresse IP publique et Load Balancer) de toutes les instances de machine virtuelle (cela inclut toutes les instances d’un groupe à haute disponibilité).
3. Associez toutes les instances de machine virtuelle aux nouvelles ressources de référence SKU Standard.

### <a name="migrating-from-standard-to-basic-sku"></a>Migration de la référence SKU Standard à la référence SKU De base :

1. Créez une ressource De base (Load Balancer et adresses IP publiques si nécessaire) et recréez vos règles et définitions de sonde. 
2. Supprimez les ressources de la référence SKU Standard (adresse IP publique et Load Balancer) de toutes les instances de machine virtuelle (cela inclut toutes les instances d’un groupe à haute disponibilité).
3. Associez toutes les instances de machine virtuelle aux nouvelles ressources de référence SKU De base.

  >[!NOTE]
  >Les ports HA et les diagnostics de la référence SKU Standard sont uniquement disponibles dans la référence SKU Standard. Vous ne pouvez pas effectuer la migration de la référence SKU Standard à la référence SKU De base et conserver cette fonction.

Les références SKU qui correspondent doivent être utilisées pour les ressources de Load Balancer et d’adresse IP publique.  Il n’est pas possible de mélanger les ressources de référence SKU Standard et De base, ni d’associer une machine virtuelle, des machines virtuelles dans un groupe à haute disponibilité, ou un groupe de machines virtuelles identiques aux deux références simultanément.

## <a name="region-availability"></a>Disponibilité des régions

La référence Standard de Load Balancer est actuellement disponible dans ces régions :
- Est des États-Unis 2
- Centre des États-Unis
- Europe du Nord
- Centre-Ouest des États-Unis
- Europe de l'Ouest
- Asie du Sud-Est

## <a name="service-limits--abilities-comparison"></a>Comparaison des fonctions et limites de service

[Les limites de service pour la mise en réseau Azure](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits) s’appliquent par région et par abonnement. 

Le tableau suivant fournit une comparaison des fonctions et limites entre les références SKU De base et Standard pour Load Balancer :

| Load Balancer | De base | Standard |
| --- | --- | --- |
| Taille de pool de serveur principal | jusqu’à 100 | jusqu’à 1 000 |
| Limite de pool de serveur principal | Groupe à haute disponibilité | Réseau virtuel, région |
| Conception de pool de serveur principal | Machines virtuelles dans un groupe à haute disponibilité ou groupe de machines virtuelles identiques dans un groupe à haute disponibilité | Toute instance de machine virtuelle dans le réseau virtuel |
| Ports HA | Non pris en charge | Disponible |
| Diagnostics | Limités, publics uniquement | Disponible |
| Disponibilité VIP  | Non pris en charge | Disponible |
| Mobilité d’adresses IP rapide | Non pris en charge | Disponible |
|Scénarios de zones de disponibilité | Zonaux uniquement | Équilibrage de charge entre les zones, redondant dans une zone, zonal |
| Algorithme SNAT sortant | À la demande | Préalloué |
| Sélection du serveur frontal SNAT sortant | Non configurable, plusieurs candidats | Configuration facultative pour réduire les candidats |
| Groupe de sécurité réseau | Facultatif sur la carte d’interface réseau/le sous-réseau | Requis |

Le tableau suivant fournit une comparaison des fonctions et limites entre les références SKU De base et Standard pour l’adresse IP publique :

| Adresse IP publique | De base | Standard |
| --- | --- | --- |
| Scénarios de zones de disponibilité | Zonaux uniquement | Redondants dans une zone (par défaut), zonaux (facultatif) | 
| Mobilité d’adresses IP rapide | Non pris en charge | Disponible |
| Disponibilité VIP | Non pris en charge | Disponible |
| Counters | Non pris en charge | Disponible |
| Groupe de sécurité réseau | Facultatif sur la carte d’interface réseau | Requis |


## <a name="preview-sign-up"></a>S’inscrire à la préversion

Pour découvrir la préversion de la référence SKU Standard de Load Balancer et à la référence SKU Standard d’adresse IP publique associée, inscrivez votre abonnement et bénéficiez d’un accès via PowerShell ou Azure CLI 2.0.

- S’inscrire à l’aide de PowerShell

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

- S’inscrire à l’aide d’Azure CLI 2.0

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```

>[!NOTE]
>Si vous souhaitez utiliser des zones de disponibilité avec Load Balancer et l’adresse IP publique, vous devez également inscrire votre abonnement à la préversion des zones de disponibilité.

## <a name="pricing"></a>Tarification

La facturation de la référence SKU Standard de Load Balancer repose sur les règles configurées et les données traitées.  Aucuns frais ne sont facturés lors de la durée de la préversion.  Consultez les pages de tarification de [Load Balancer](https://aka.ms/lbpreviewpricing) et [d’adresse IP publique](https://aka.ms/lbpreviewpippricing) pour plus d’informations.

Les clients peuvent continuer à profiter de la référence SKU De base de Load Balancer sans rien débourser.

## <a name="limitations"></a>Limites

Les limitations suivantes s’appliquent au moment de la version préliminaire et sont susceptibles de changer :

- Actuellement, les instances de serveur principal Load Balancer ne peuvent pas être placées dans des réseaux homologués. Toutes les instances de serveur principal doivent être dans la même région.
- Les références SKU ne sont pas mutables. Vous ne pouvez pas modifier la référence SKU d’une ressource existante.
- Vous pouvez utiliser les références SKU De base ou Standard avec une machine virtuelle autonome, toutes les instances de machine virtuelle dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques. Une machine virtuelle autonome, toutes les instances de machine virtuelle dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques ne peuvent pas être utilisées avec les deux références simultanément. Le mélange de références SKU n’est pas autorisé.
- L’utilisation d’une référence Standard de Load Balancer interne avec une instance de machine virtuelle (ou une partie d’un groupe à haute disponibilité) désactive les [connexions sortantes SNAT par défaut](load-balancer-outbound-connections.md).  Vous pouvez restaurer cette fonction sur une machine virtuelle autonome ou des instances de machine virtuelle dans un groupe à haute disponibilité ou un groupe de machines virtuelles identiques et établir des connexions sortantes en assignant simultanément une référence Standard de Load Balancer public ou une référence Standard d’adresse IP publique en tant qu’adresse IP publique de niveau d’instance à la même instance de machine virtuelle. Une fois cette opération effectuée, l’algorithme SNAT d’usurpation d’identité de port sur une adresse IP publique est de nouveau disponible.
- Les instances de machine virtuelle doivent éventuellement être rassemblées dans des groupes à haute disponibilité pour obtenir une mise à l’échelle intégrale du pool de serveur principal. Jusqu’à 150 groupes à haute disponibilité et machines virtuelles autonomes peuvent être placés dans un seul pool de serveur principal.
- IPv6 n’est pas pris en charge.
- Dans le contexte des zones de disponibilité, un serveur frontal ne peut pas passer de zonal à redondant dans une zone, et vice versa. Une fois créé et défini comme étant redondant dans une zone, il est toujours redondant dans une zone. Une fois créé et défini comme étant zonal, il est toujours zonal.
- Dans le contexte des zones de disponibilité, une adresse IP publique zonale ne peut pas être déplacée d’une zone à l’autre.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [la référence De base de Load Balancer](load-balancer-overview.md)
- En savoir plus sur les [zones de disponibilité](../availability-zones/az-overview.md)
- Découvrir les autres [fonctionnalités de réseau](../networking/networking-overview.md) clés d’Azure


