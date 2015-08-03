<properties 
   pageTitle="Présentation de l’équilibrage de charge Azure | Microsoft Azure"
   description="Présentation des fonctionnalités, de l’architecture et de l’implémentation de l’équilibrage de charge Azure. Il est utile de comprendre comment fonctionne l’équilibrage de charge et d’en tirer parti sur le cloud."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/10/2015"
   ms.author="joaoma" />


# Présentation de l’équilibrage de charge 
L’équilibrage de charge Azure offre une haute disponibilité et des performances réseau élevées pour vos applications. Il s’agit d’un équilibrage de charge de type Layer-4 (TCP, UDP) qui distribue le trafic entrant parmi les instances de service saines dans les services cloud ou les machines virtuelles définis dans un jeu d’équilibrage de la charge.
 
Il peut être configuré pour :

- équilibrer la charge du trafic Internet entrant sur les machines virtuelles. Nous appelons cela [l’équilibrage de charge accessible sur Internet](load-balancer-overview.md).
- équilibrer le trafic entre des machines virtuelles dans un réseau virtuel, entre des machines virtuelles dans les services cloud ou entre des ordinateurs locaux et des machines virtuelles dans un réseau virtuel entre différents locaux. Nous appelons cela [l’équilibrage de charge interne (ILB)](load-balancer-internal-overview.md).
- 	Transfert du trafic externe à une instance spécifique de machine virtuelle


## Fonctionnalités d’équilibrage de charge

### Équilibrage de charge Layer-4, distribution basée sur le hachage

L’équilibrage de charge Azure utilise un algorithme de distribution basé sur le hachage. Par défaut, il utilise un hachage à 5 tuples (adresse IP source, port source, adresse IP de destination, port de destination, type de protocole) pour mapper le trafic vers les serveurs disponibles. Il fournit uniquement l’adhérence dans une session de transport. Les paquets de la même session TCP ou UDP sont dirigés vers la même instance de centre de données IP (DIP) derrière le point de terminaison d’équilibrage de charge. Lorsque le client ferme et rouvre la connexion ou démarre une nouvelle session à partir de la même adresse IP source, le port source change. Cela peut pousser le trafic à accéder à un autre point de terminaison DIP.


Plus d’informations sur le [Mode de distribution de l’équilibrage de charge](load-balancer-distribution-mode.md)

![équilibrage de charge basé sur le hachage](./media/load-balancer-overview/load-balancer-distribution.png)

### Réacheminement de port

L’équilibrage de charge Azure vous permet de contrôler la gestion de toutes les communications entrantes, telles que le trafic initié par des hôtes Internet ou des machines virtuelles dans d’autres services cloud ou réseaux virtuels. Ce contrôle est représenté par un point de terminaison (également appelé point de terminaison d’entrée).

Un point de terminaison écoute sur un port public et transmet le trafic vers un port interne. Vous pouvez mapper les mêmes ports pour un point de terminaison interne ou externe, ou utiliser un port différent. Par exemple : vous pouvez avoir une écoute configurée du serveur Web sur le port 81 alors que le mappage de point de terminaison public est le port 80. La création d’un point de terminaison public déclenche la création d’un équilibrage de charge Azure.

Par défaut, les points de terminaison sur une machine virtuelle que vous créez via le portail de gestion Azure sont utilisés et configurés pour le trafic RDP (Remote Desktop Protocol) et de la session Windows PowerShell distante. Ces points de terminaison vous permettent d'administrer à distance la machine virtuelle sur Internet.


### Reconfiguration automatique lors de la montée ou descente en puissance

L’équilibrage de charge Azure se reconfigure instantanément lorsque vous faites monter ou descendre en puissance des instances (soit en raison de l’augmentation du nombre d’instances de rôle Web/de travail ou du fait de l’ajout de machines virtuelles sous le même jeu d’équilibrage de la charge).


### Surveillance des services
L’équilibrage de charge Azure offre la possibilité de sonder l’intégrité des différentes instances de serveur. Lorsqu’une sonde ne répond pas, l’équilibrage de charge Azure n’envoie plus de nouvelles connexions aux instances défaillantes. Les connexions existantes ne sont pas concernées.

Trois types de sondes sont pris en charge :
 
- sonde d’agent invité (sur des machines virtuelles PaaS uniquement) L’équilibrage de charge Azure utilise l’agent invité de la machine virtuelle, écoute et répond avec une réponse HTTP 200 OK uniquement si l’instance est prête (c.-à-d. que l’instance n’est pas en état Occupé, Recyclage, Arrêté, etc.). Si l’agent invité ne répond pas avec HTTP 200 OK, l’équilibrage de charge Azure marque l’instance comment ne répondant pas et arrête d’envoyer du trafic vers cette instance. L’équilibrage de charge Azure continue d’effectuer un test ping sur l’instance et, si l’agent invité répond avec un HTTP 200, l’équilibrage de charge Azure recommence à envoyer le trafic vers cette instance. Lorsque vous utilisez un rôle Web, votre code de site Web s’exécute généralement dans w3wp.exe, qui n’est pas surveillé par la structure Azure ou l’agent invité, ce qui signifie que les échecs dans w3wp.exe (par exemple les réponses HTTP 500) ne sont pas notifiés à l’agent invité et l’équilibrage de charge ne peut pas mettre cette instance hors service.

- sondes HTTP personnalisées La sonde d’équilibrage de charge personnalisée remplace la sonde de l’agent invité par défaut et vous permet de créer votre propre logique personnalisée pour déterminer l’intégrité de l’instance de rôle. L’équilibrage de charge sonde régulièrement votre point de terminaison (toutes les 15 secondes, par défaut) et l’instance est considérée comme en service si elle répond avec le code de statut HTTP 200 ou un paquet TCP ACK dans le délai imparti (31 secondes par défaut). Cela peut être utile pour implémenter votre propre logique, afin de supprimer des instances de la rotation d’équilibrage de charge, par exemple en retournant un état autre que 200 si l’instance dépasse 90 % de l’UC. Pour les rôles Web utilisant w3wp.exe, cela signifie également que vous obtenez une analyse automatique de votre site Web, dans la mesure où les erreurs dans votre code de site Web renvoient un état autre que 200 à la sonde de l’équilibrage de charge.

- sondes TCP personnalisées Les sondes TCP s’appuient sur l’établissement réussi d’une session TCP sur un port de sonde défini.

Pour plus d’informations, consultez [Sonde d’intégrité de l’équilibrage de charge](https://msdn.microsoft.com/library/azure/jj151530.aspx).

### SNAT (Source NAT)


Tout le trafic sortant vers Internet à partir de votre service d’origine passe par SNAT (Source NAT) avec la même adresse IP virtuelle que pour le trafic entrant. SNAT offre des avantages importants :

- il permet une mise à niveau et une récupération d’urgence simples des services, étant donné que l’adresse IP virtuelle peut être dynamiquement mappée à une autre instance du service ;

- il facilite la gestion de l’ACL puisqu’elle peut être exprimée en termes d’adresses IP virtuelles. Par conséquent, elle ne change pas lorsque les services sont redimensionnés ou redéployés.

La configuration de l’équilibrage de charge Azure prend en charge le NAT « plein cône » pour UDP. Le NAT« plein cône » est un type de NAT où le port autorise les connexions entrantes à partir de n’importe quel hôte externe (en réponse à une requête sortante).

![snat](./media/load-balancer-overview/load-balancer-snat.png)


>[AZURE.NOTE]Notez que pour chaque nouvelle connexion sortante initiée par une machine virtuelle, un port sortant est également alloué par l’équilibrage de charge Azure. L’hôte externe voit le trafic entrant comme un port alloué d’adresse IP virtuelle. Si vous avez besoin d’un grand nombre de connexions sortantes, il est recommandé que les machines virtuelles utilisent des adresses IP publiques de niveau d’instance, afin de disposer d’une adresse IP sortante dédiée pour SNAT (Source Network Address Translation). Cela réduit le risque d’épuisement du port.
>
>Le nombre maximal de ports pouvant être utilisés par l'adresse IP virtuelle ou ILPIP est 64K. Il s'agit d'une limite TCP standard.


**Prise en charge de plusieurs adresses IP à équilibrage de charge pour les machines virtuelles**

Vous pouvez obtenir plusieurs adresses IP publiques à équilibrage de charge attribuées à un jeu de machines virtuelles. Cela vous permet d’héberger plusieurs sites Web SSL et/ou plusieurs écouteurs de groupe de disponibilité SQL Always On sur le même jeu de machines virtuelles. Pour plus d’informations, consultez [Plusieurs adresses IP virtuelles par service cloud](load-balancer-multivip.md)

**Déploiements basés sur des modèles à l’aide d’Azure Resource Manager** Azure Resource Manager (ARM) est la nouvelle infrastructure de gestion des services dans Azure. L’équilibrage de charge Azure peut maintenant être géré à l’aide des outils et API d’Azure Resource Manager. Pour en savoir plus sur Azure Resource Manager, consultez [Iaas just got easier with Azure Resource Manager](http://azure.microsoft.com/blog/2015/04/29/iaas-just-got-easier-again/)


## Étapes suivantes

[Présentation de l’équilibrage de charge accessible sur Internet](load-balancer-internet-overview.md)

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de l’équilibrage de charge accessible sur Internet](load-balancer-internet-getstarted.md)
 

<!---HONumber=July15_HO4-->