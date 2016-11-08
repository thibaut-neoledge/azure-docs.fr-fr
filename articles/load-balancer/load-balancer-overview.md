---
title: Présentation de l’équilibrage de charge Azure | Microsoft Docs
description: Présentation des fonctionnalités, de l'architecture et de l'implémentation de l'équilibrage de charge Azure. Découvrez comment fonctionne l’équilibrage de charge et l’exploiter dans le cloud.
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/22/2016
ms.author: sewhee

---
# Vue d’ensemble de l’équilibreur de charge Azure
L'équilibrage de charge Azure offre une haute disponibilité et des performances réseau élevées pour vos applications. Il s’agit d’un équilibreur de charge Layer-4 (TCP, UDP) qui distribue le trafic entrant parmi des instances saines de services définis dans un jeu à charge équilibrée.

## Configuration d’Azure Load Balancer
Il peut être configuré pour :

* équilibrer la charge du trafic Internet entrant sur les machines virtuelles. Cette configuration est appelée [équilibrage de charge avec accès par Internet](load-balancer-internet-overview.md).
* équilibrer le trafic entre des machines virtuelles dans un réseau virtuel, entre des machines virtuelles dans les services cloud ou entre des ordinateurs locaux et des machines virtuelles dans un réseau virtuel entre différents locaux. Cette configuration est appelée [équilibrage de charge interne](load-balancer-internal-overview.md).
* transférer du trafic externe vers une instance spécifique de machine virtuelle.

Toutes les ressources du cloud ont besoin d'une adresse IP publique pour être accessibles depuis Internet. Au sein de l’infrastructure de cloud, Microsoft Azure utilise des adresses IP non routables pour ses ressources. Azure utilise la traduction d’adresses réseau (NAT) avec des adresses IP publiques pour communiquer avec Internet.

## Modèles de déploiement Azure
Il est important de comprendre les différences entre les [modèles de déploiement](../resource-manager-deployment-model.md) Azure Classic et Resource Manager. Azure Load Balancer est configuré différemment selon les modèles.

### Modèle de déploiement classique Azure
Dans ce modèle, une adresse IP publique et un nom de domaine complet sont affectés à un service cloud. Les machines virtuelles déployées dans une limite de service cloud peuvent être regroupées afin d’utiliser un équilibreur de charge. L’équilibreur de charge effectue une traduction de port et distribue le trafic réseau reçu sur l’adresse IP publique du service cloud.

Le trafic à charge équilibrée est défini par points de terminaison. Les points de terminaison de traduction de port ont une relation un-à-un entre le port public affecté à l’adresse IP publique et le port local affecté au service sur une machine virtuelle spécifique. Les points de terminaison d’équilibrage de charge ont une relation un-à-plusieurs entre l’adresse IP publique et les ports locaux affectés aux services sur les machines virtuelles du service cloud.

Le nom de domaine de l’adresse IP publique que l’équilibreur de charge utilise pour ce modèle de déploiement est <nom du service cloud>.cloudapp.net. Le graphique suivant montre Azure Load Balancer dans ce modèle.

![L’équilibrage de charge dans le modèle de déploiement classique](./media/load-balancer-overview/asm-lb.png)

### Modèle de déploiement Azure Resource Manager
Dans le modèle de déploiement Resource Manager, il n’est pas nécessaire de créer un service cloud. L’équilibreur de charge peut être créé explicitement pour acheminer le trafic entre les machines virtuelles.

Dans le modèle Resource Manager, une adresse IP publique est sa propre ressource, qui peut être associée à un nom de domaine ou un nom DNS. Dans ce cas, l’adresse IP publique est associée à la ressource d’équilibrage de charge. Les règles d’équilibrage de charge et les règles NAT entrantes utilisent l’adresse IP publique comme point de terminaison Internet pour les ressources recevant le trafic réseau à charge équilibrée.

Une adresse IP privée ou publique est affectée à la ressource d’interface réseau associée à une machine virtuelle. Une fois l’interface réseau ajoutée au pool d’adresses IP principales de l’équilibreur de charge, ce dernier est capable d’envoyer le trafic réseau à charge équilibrée selon les règles à charge équilibrée créées.

Le graphique suivant montre Azure Load Balancer dans ce modèle :

![L’équilibrage de charge Azure dans Resource Manager](./media/load-balancer-overview/arm-lb.png)

#### Déploiements basés sur modèle avec Azure Resource Manager
L’équilibrage de charge peut maintenant être géré à l’aide des outils et API de Resource Manager. Pour plus d’informations sur Resource Manager, consultez la [Présentation de Resource Manager](../resource-group-overview.md).

## Fonctionnalités d’équilibrage de charge
* Distribution basée sur le hachage
  
    L’équilibrage de charge utilise un algorithme de distribution basé sur le hachage. Par défaut, il utilise un hachage à 5 tuples (adresse IP source, port source, adresse IP de destination, port de destination et type de protocole) pour mapper le trafic vers les serveurs disponibles. Il fournit l’adhérence uniquement *dans* une session de transport. Les paquets de la même session TCP ou UDP sont dirigés vers la même instance derrière le point de terminaison à charge équilibrée. Lorsque le client ferme et rouvre la connexion ou démarre une nouvelle session à partir de la même adresse IP source, le port source change. Cela peut pousser le trafic à accéder à un autre point de terminaison dans un autre centre de données.
  
    Pour en savoir plus, consultez [Mode de distribution de l’équilibrage de charge](load-balancer-distribution-mode.md). Le graphique suivant montre la distribution basée sur le hachage :
  
    ![Distribution basée sur le hachage](./media/load-balancer-overview/load-balancer-distribution.png)
* Réacheminement de port
  
    L’équilibrage de charge vous permet de contrôler la gestion des communications entrantes. Ces communications peuvent inclure le trafic initié par des hôtes Internet ou des machines virtuelles dans d'autres services cloud ou réseaux virtuels. Ce contrôle est représenté par un point de terminaison (également appelé point de terminaison d'entrée).
  
    Un point de terminaison d’entrée écoute sur un port public et transmet le trafic vers un port interne. Vous pouvez mapper les mêmes ports pour un point de terminaison interne ou externe, ou utiliser un port différent. Par exemple : vous pouvez avoir une écoute configurée du serveur web sur le port 81 alors que le mappage de point de terminaison public est le port 80. La création d’un point de terminaison public déclenche la création d’une instance d’équilibrage de charge.
  
    En cas de création avec le Portail Azure, le portail crée automatiquement des points de terminaison sur la machine virtuelle pour le trafic RDP (Remote Desktop Protocol) et de la session Windows PowerShell distante. Vous pouvez utiliser ces points de terminaison pour administrer à distance la machine virtuelle sur Internet.
* Reconfiguration automatique
  
    L’équilibrage de charge se reconfigure instantanément lors de la mise à l’échelle d’instances vers le haut ou vers le bas. Cette reconfiguration se produit, par exemple, lorsque l’on augmente le nombre d’instances de rôles Web/de travail dans un service cloud ou lorsque l’on ajoute des machines virtuelles supplémentaires dans le même jeu à charge équilibrée.
* Surveillance des services
  
    L’équilibrage de charge peut tester l’intégrité des différentes instances de serveur. Lorsqu’une sonde ne répond pas, l’équilibrage de charge n’envoie plus de nouvelles connexions aux instances défaillantes. Les connexions existantes ne sont pas concernées.
  
    Trois types de sondes sont pris en charge :
  
  * **Sonde d’agent invité (sur machines virtuelles Paas uniquement)**** :** l’équilibrage de charge utilise l’agent invité dans la machine virtuelle. L’agent invité écoute et répond HTTP 200 OK uniquement lorsque l’instance est prête (c’est-à-dire qu’elle ne se trouve pas dans un état de type occupé, recyclage ou arrêt). Si l’agent ne répond pas HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répondant pas et arrête d’envoyer du trafic vers cette instance. L’équilibreur de charge continue d’effectuer un test ping sur l’instance. Si l’agent invité répond avec un HTTP 200, l’équilibrage de charge envoie à nouveau du trafic vers cette instance. Lorsque vous utilisez un rôle web, le code de votre site web s'exécute généralement dans w3wp.exe, qui n'est pas surveillé par l'agent Azure Fabric ou l'agent invité. Cela signifie que les échecs dans w3wp.exe (par exemple, les réponses HTTP 500) ne seront pas signalés à l’agent invité et l’équilibrage de charge ne saura pas qu’il doit placer cette instance hors service.
  * **Sonde personnalisée HTTP :** cette sonde remplace la sonde (agent invité) par défaut. Vous pouvez l'utiliser pour créer votre propre logique personnalisée pour déterminer l'état de l'instance de rôle. L’équilibrage de charge sonde régulièrement votre point de terminaison (toutes les 15 secondes, par défaut). L’instance est considérée comme en service si elle répond avec le code de statut HTTP 200 ou un paquet TCP ACK dans le délai imparti (31 secondes par défaut). C’est utile pour implémenter votre propre logique afin de supprimer des instances de l’équilibreur de charge. Vous pouvez, par exemple, configurer l'instance pour qu'elle renvoie un état autre que 200 si l'instance a une utilisation supérieure à 90 % de l'UC. Pour les rôles Web utilisant w3wp.exe, vous obtenez également une analyse automatique de votre site web, dans la mesure où les erreurs dans votre code de site web renvoient un état autre que 200 à la sonde.
  * **Sonde personnalisée TCP :** cette sonde s’appuie sur l’établissement réussi d’une session TCP sur un port de sonde défini.
    
    Pour plus d’informations, consultez le [schéma LoadBalancerProbe](https://msdn.microsoft.com/library/azure/jj151530.aspx).
* Source NAT
  
    Tout le trafic sortant vers Internet à partir de votre service d’origine passe par SNAT (Source NAT) avec la même adresse IP virtuelle que le trafic entrant. SNAT offre des avantages importants :
  
  * il permet une mise à niveau et une récupération d'urgence simples des services, étant donné que l'adresse IP virtuelle peut être dynamiquement mappée à une autre instance du service ;
  * Il facilite la gestion des listes de contrôle d’accès (ACL). Les ACL exprimées sous forme d’adresses IP virtuelles ne changent pas lorsque les services montent en puissance, descendent en puissance ou sont redéployés.
    
    La configuration de l’équilibrage de charge prend en charge le NAT « plein cône » pour UDP. Le NAT« plein cône » est un type de NAT où le port autorise les connexions entrantes à partir de n’importe quel hôte externe (en réponse à une requête sortante).

    >[AZURE.NOTE] Pour chaque nouvelle connexion sortante initiée par une machine virtuelle, un port sortant est également alloué par l’équilibrage de charge. L’hôte externe voit le trafic avec un port alloué à une adresse IP virtuelle. Pour les scénarios exigeant un grand nombre de connexions sortantes, il est recommandé d’utiliser des adresses [IP publiques de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) afin que les machines virtuelles disposent d’une adresse IP sortante dédiée pour SNAT. Cela réduit le risque d’épuisement du port.
    >
    >Le nombre maximal de ports pouvant être utilisés par l’adresse IP virtuelle ou une adresse IP publique de niveau d’instance est 64 000. Il s'agit d'une limite TCP standard.


### Prise en charge de plusieurs adresses IP à équilibrage de charge pour les machines virtuelles
Vous pouvez affecter plusieurs adresses IP publiques à charge équilibrée à un jeu de machines virtuelles. Cela vous permet d'héberger plusieurs sites Web SSL et/ou plusieurs écouteurs de groupe de disponibilité SQL Server Always On sur le même jeu de machines virtuelles. Pour plus d’informations, consultez [Plusieurs adresses IP virtuelles par service cloud](load-balancer-multivip.md).

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Étapes suivantes
[Présentation de l'équilibrage de charge accessible sur Internet](load-balancer-internet-overview.md)

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de la création d'un équilibreur de charge accessible sur Internet](load-balancer-get-started-internet-arm-ps.md)

<!---HONumber=AcomDC_0831_2016-->