

Deux niveaux d’équilibrage de charge sont disponibles pour les services d’infrastructure Azure :

* **Niveau DNS** : équilibrage de charge du trafic sur différents services cloud situés dans divers centres de données, sur différents sites Web Azure figurant dans différents centres de données, ou sur des points de terminaison externes. Ceci s’effectue à l’aide d’Azure Traffic Manager et de la méthode d’équilibrage de charge de tourniquet (round robin).
* **Niveau réseau** : équilibrage de charge du trafic Internet entrant sur différentes machines virtuelles d’un service cloud, ou équilibrage de charge du trafic entre des machines virtuelles dans un service cloud ou un réseau virtuel. Il s’effectue à l’aide de l’équilibrage de charge Azure.

## <a name="traffic-manager-load-balancing-for-cloud-services-and-websites"></a>Équilibrage de charge de Traffic Manager pour des services cloud et des sites Web
Traffic Manager vous permet de contrôler la répartition du trafic utilisateur sur des points de terminaison, comme des services cloud, des sites web, des sites externes et d’autres profils Traffic Manager. Traffic Manager utilise un moteur de stratégie intelligent pour les requêtes DNS des noms de domaine de vos ressources Internet. Vos services cloud ou vos sites Web peuvent être exécutés dans différents centres de données à travers le monde.

Vous devez utiliser REST ou Windows PowerShell pour configurer les points de terminaison externes ou les profils Traffic Manager en tant que points de terminaison.

Traffic Manager utilise trois méthodes d’équilibrage de charge pour répartir le trafic :

* **Basculement** : utilisez cette méthode quand vous souhaitez utiliser un point de terminaison principal pour l’ensemble du trafic, mais également fournir des sauvegardes au cas où celui-ci deviendrait indisponible.
* **Performances** : utilisez cette méthode lorsque vos points de terminaison se trouvent à des emplacements géographiques différents et que vous souhaitez que les clients à l’origine des demandes utilisent le point de terminaison « le plus proche » (latence la plus faible).
* **Tourniquet (round robin)** : utilisez cette méthode si vous souhaitez répartir la charge sur un ensemble de services cloud situés dans le même centre de données ou sur des services cloud ou des sites Web figurant dans différents centres de données.

Pour plus d’informations, consultez la page [À propos des méthodes d’équilibrage de charge dans Traffic Manager](../articles/traffic-manager/traffic-manager-routing-methods.md).

Le diagramme suivant montre un exemple d’équilibrage de charge de tourniquet (round robin) utilisé pour répartir le trafic entre différents services cloud.

![loadbalancing](./media/virtual-machines-common-load-balance/TMSummary.png)

Cela se déroule généralement de la manière suivante :

1. Un client Internet interroge un nom de domaine correspondant à un service Web.
2. DNS envoie le nom de la demande de requête à Traffic Manager.
3. Traffic Manager choisit le service cloud suivant dans la liste du tourniquet (round robin) et renvoie le nom DNS. Le serveur DNS du client Internet résout le nom en adresse IP et l'envoie au client Internet.
4. Le client Internet se connecte au service cloud choisi par Traffic Manager.

Pour plus d'informations, consultez la rubrique [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md).

## <a name="azure-load-balancing-for-virtual-machines"></a>Équilibrage de charge Azure pour des machines virtuelles
Les machines virtuelles d'un même service cloud ou réseau virtuel peuvent communiquer les unes avec les autres en utilisant directement leurs adresses IP privées. Les ordinateurs et services n'utilisant pas le service cloud ou le réseau virtuel ne peuvent communiquer avec les machines virtuelles d'un service cloud ou d'un réseau virtuel qu'à l'aide d'un point de terminaison configuré. Un point de terminaison est un mappage d'une adresse IP et d'un port publics à cette adresse IP privée, et d'un port d'une machine virtuelle ou d'un rôle Web au sein d'un service cloud Azure.

L'équilibrage de charge Azure répartit aléatoirement un type de trafic entrant spécifique sur plusieurs machines virtuelles ou services via une configuration connue sous le nom de jeu d'équilibrage de charge. Par exemple, vous pouvez répartir la charge du trafic des requêtes Web sur plusieurs serveurs ou rôles Web.

Le diagramme suivant montre un point de terminaison à charge équilibrée pour le trafic web (non chiffré) standard partagé entre trois machines virtuelles pour le port TCP public et privé 80. Celles-ci sont incluses dans un jeu d’équilibrage de la charge.

![loadbalancing](./media/virtual-machines-common-load-balance/LoadBalancing.png)

Pour plus d’informations, consultez la page [Équilibrage de charge Azure](../articles/load-balancer/load-balancer-overview.md). Pour découvrir comment créer un jeu d’équilibrage de charge, consultez la page [Configurer un jeu d’équilibrage de charge](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md).

Azure peut également équilibrer la charge au sein d’un service cloud ou réseau virtuel. On parle alors d'équilibrage de charge interne, que l'on peut utiliser comme suit :

* Pour équilibrer la charge entre des serveurs aux différents niveaux d'une application multiniveau (par exemple, entre les niveaux Web et base de données).
* Pour équilibrer la charge d’applications métier hébergées dans Azure, sans nécessiter du matériel ou du logiciel d’équilibreur de charge supplémentaire.
* Pour inclure des serveurs locaux dans l’ensemble d’ordinateurs dont la charge du trafic est équilibrée.

Tout comme l'équilibrage de charge Azure, l'équilibrage de charge interne est facilité par la configuration d'un jeu d'équilibrage de charge interne.

Le diagramme suivant montre un exemple de point de terminaison interne à charge équilibrée pour une application métier qui est partagée entre trois machines virtuelles au sein d’un réseau virtuel établi entre différents locaux.

![loadbalancing](./media/virtual-machines-common-load-balance/LOBServers.png)

## <a name="load-balancer-considerations"></a>Considérations relatives à l’équilibreur de charge
Un équilibreur de charge est configuré par défaut pour mettre fin à une session inactive pendant 4 minutes. Si l’application derrière un équilibreur de charge conserve une connexion inactive pendant plus de 4 minutes sans une configuration de persistance de connexion, la connexion est supprimée. Vous pouvez modifier le comportement de l’équilibreur de charge Azure pour autoriser un [paramètre de délai d’attente plus long](../articles/load-balancer/load-balancer-tcp-idle-timeout.md).

Un autre élément à prendre en compte est le type de mode de distribution pris en charge par l’équilibreur de charge Azure. Vous pouvez configurer l’affinité d’IP source (adresse IP source, adresse IP de destination) ou le protocole IP source (adresse IP source, adresse IP de destination et protocole). Pour plus d’informations, consultez l’article [Azure Load Balancer distribution mode (source IP affinity)](../articles/load-balancer/load-balancer-distribution-mode.md) (Mode de distribution de l’équilibreur de charge Azure [affinité d’IP source]).

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir comment créer un jeu d’équilibrage de charge, consultez la page [Configurer un jeu d’équilibrage de charge interne](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md).

Pour plus d’informations sur l’équilibrage de charge, voir l’article [Équilibrage de charge interne](../articles/load-balancer/load-balancer-internal-overview.md).



<!--HONumber=Nov16_HO3-->


