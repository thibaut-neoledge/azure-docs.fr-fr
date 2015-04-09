<properties 
	pageTitle="Équilibrage de charge pour les services d'infrastructure Azure" 
	description="Décrit les deux types d'équilibrage de la charge pris en charge par Azure : Équilibrage de la charge pour les services cloud et Azure Traffic Manager pour le trafic client." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="cherylmc" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="cherylmc"/>


#Équilibrage de charge pour les services d'infrastructure Azure#

Deux niveaux d'équilibrage de charge sont disponibles pour les services d'infrastructure Azure :

- **Niveau DNS **:  Équilibrage de charge du trafic sur différents services cloud situés dans différents centres de données, sur différents sites Web Azure situés dans différents centres de données, ou sur des points de terminaison externes. Il s'effectue à l'aide de Traffic Manager et de la méthode d'équilibrage de charge Tourniquet.
- **Niveau réseau **:  Équilibrage de charge du trafic Internet entrant sur différentes machines virtuelles, ou équilibrage de charge du trafic entre des machines virtuelles dans un service cloud ou un réseau virtuel. Il s'effectue à l'aide de l'équilibrage de charge Azure.

##Équilibrage de charge de Traffic Manager pour des services cloud et des sites Web##

Azure Traffic Manager vous permet de contrôler la répartition du trafic utilisateur sur des points de terminaison, tels que des services cloud, des sites Web, des sites externes et d'autres profils Traffic Manager. Traffic Manager utilise un moteur de stratégie intelligent pour les requêtes DNS des noms de domaine de vos ressources Internet. Vos services cloud ou vos sites Web peuvent être exécutés dans différents centres de données à travers le monde. 

Vous devez utiliser REST ou Windows PowerShell pour configurer les points de terminaison externes ou les profils Traffic Manager en tant que points de terminaison. 

Azure Traffic Manager utilise trois méthodes d'équilibrage de charge pour répartir le trafic :

- **Basculement **:  Utilisez cette méthode lorsque vous souhaitez utiliser un point de terminaison principal pour l'ensemble du trafic, mais également fournir des sauvegardes au cas où celui-ci deviendrait indisponible.
- **Performances **:  Utilisez cette méthode lorsque vos points de terminaison se trouvent à des emplacements géographiques différents et que vous souhaitez que les clients étant à l'origine des demandes utilisent le point de terminaison " le plus proche " (latence la plus faible).
- **Tourniquet (round robin) :** utilisez cette méthode lorsque vous souhaitez répartir la charge sur un ensemble de services cloud situés dans le même centre de données ou sur des services cloud ou des sites web situés dans différents centres de données.

Pour plus d'informations, consultez la page [À propos des méthodes d'équilibrage de charge dans Traffic Manager](http://msdn.microsoft.com/library/azure/dn339010.aspx).

La figure suivante présente un exemple d'équilibrage de charge Tourniquet utilisé pour répartir le trafic entre différents services cloud.

![loadbalancing](./media/load-balancing-vms/TMSummary.png)

Cela se déroule généralement de la manière suivante :

1.	Un client Internet interroge un nom de domaine correspondant à un service Web.
2.	DNS envoie le nom de la demande de requête à Traffic Manager.
3.	Traffic Manager choisit le service cloud suivant dans la liste du tourniquet et renvoie le nom DNS. Le serveur DNS du client Internet résout le nom en adresse IP et l'envoie au client Internet.
4.	Le client Internet se connecte au service cloud choisi par Traffic Manager.

Pour plus d'informations, consultez la page [Traffic Manager](http://msdn.microsoft.com/library/azure/hh745750.aspx).

## Équilibrage de charge Azure pour des machines virtuelles ##

Les machines virtuelles d'un même service cloud ou réseau virtuel peuvent communiquer les unes avec les autres en utilisant directement leurs adresses IP privées. Les ordinateurs et services n'utilisant pas le service cloud ou le réseau virtuel ne peuvent communiquer avec les machines virtuelles d'un service cloud ou d'un réseau virtuel qu'à l'aide d'un point de terminaison configuré. Un point de terminaison est un mappage d'une adresse IP et d'un port publics à cette adresse IP privée, et d'un port d'une machine virtuelle ou d'un rôle Web au sein d'un service cloud Azure.

L'équilibrage de charge Azure répartit aléatoirement un type de trafic entrant spécifique sur plusieurs machines virtuelles ou services via une configuration connue sous le nom de jeu d'équilibrage de charge. Par exemple, vous pouvez répartir la charge du trafic des requêtes Web sur plusieurs serveurs ou rôles Web.

La figure suivante présente un point de terminaison à charge équilibrée pour le trafic Web (non chiffré) standard partagé entre trois machines virtuelles pour les ports public et privé 80. Celles-ci sont incluses dans un jeu d'équilibrage de la charge.

![loadbalancing](./media/load-balancing-vms/LoadBalancing.png)

Pour plus d'informations, consultez la page [Équilibrage de charge Azure](http://msdn.microsoft.com/library/azure/dn655058.aspx). Pour découvrir comment créer un jeu d'équilibrage de charge, consultez la page [Configurer un jeu d'équilibrage de charge](http://msdn.microsoft.com/library/azure/dn655055.aspx).

Azure peut également équilibrer la charge au sein d'un service cloud ou réseau virtuel. On parle alors d'équilibrage de charge interne, que l'on peut utiliser comme suit :

- Pour équilibrer la charge entre des serveurs aux différents niveaux d'une application multiniveau (par exemple, entre les niveaux Web et base de données).
- Équilibrer la charge pour des applications métier hébergées dans Azure, sans matériel ou logiciel d'équilibrage de charge supplémentaire. 
- Y compris les serveurs locaux d'un ensemble d'ordinateurs dont le trafic est équitablement partagé.

Tout comme l'équilibrage de charge Azure, l'équilibrage de charge interne est facilité par la configuration d'un jeu d'équilibrage de charge interne. 

La figure suivante présente un exemple de point de terminaison interne à charge équilibrée pour une application métier qui est partagé entre trois machines virtuelles au sein d'un réseau virtuel établi entre différents locaux. 

![loadbalancing](./media/load-balancing-vms/LOBServers.png)

Pour plus d'informations, consultez la page [Équilibrage de charge interne](http://msdn.microsoft.com/library/azure/dn690121.aspx). Pour découvrir comment créer un jeu d'équilibrage de charge, consultez la page [Configurer un jeu d'équilibrage de charge interne](http://msdn.microsoft.com/library/azure/dn690125.aspx).

<!-- LINKS -->


<!--HONumber=49-->