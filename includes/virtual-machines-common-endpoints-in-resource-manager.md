L’approche adoptée pour les points de terminaison Azure diffère légèrement selon qu’elle s’applique aux modèles de déploiement Classic ou Resource Manager. Vous avez maintenant la possibilité de créer des filtres réseau qui contrôlent le flux de trafic entrant et sortant des machines virtuelles, ce qui vous permet de créer des environnements réseau complexes au-delà d’un simple point de terminaison comme dans le modèle de déploiement Classic. Cet article fournit une vue d’ensemble des groupes de sécurité réseau et montre en quoi ils diffèrent de l’utilisation des points de terminaison Classic, de la création de ces règles de filtrage et des exemples de scénarios de déploiement.


## Vue d’ensemble des déploiements Resource Manager
Les points de terminaison dans le modèle de déploiement Classic sont remplacés par des groupes de sécurité réseau et des règles de liste de contrôle d’accès (ACL). Les étapes rapides d’implémentation des règles ACL du groupe de sécurité réseau sont les suivantes :

- Création d'un groupe de sécurité réseau
- Définition de vos règles ACL de groupe de sécurité réseau pour autoriser ou refuser du trafic
- Affectation de votre groupe de sécurité réseau à une interface réseau ou à un sous-réseau de réseau virtuel

Si vous voulez également effectuer un réacheminement de port, vous devez placer un équilibrage de charge devant votre machine virtuelle et utiliser des règles NAT. Les étapes rapides d’implémentation des règles NAT et de l’équilibrage de charge sont les suivantes :

- Créer un équilibrage de charge
- Création d’un pool principal et ajout de vos machines virtuelles au pool
- Définition de vos règles NAT pour le réacheminement de port requis
- Affectation des règles NAT à vos machines virtuelles


## Vue d’ensemble du groupe de sécurité réseau
Les groupes de sécurité réseau sont une nouvelle fonctionnalité qui fournit une couche de sécurité pour que vous puissiez autoriser les ports et sous-réseaux spécifiques à accéder à vos machines virtuelles. Vous disposez généralement d’un groupe de sécurité réseau fournissant cette couche de sécurité entre vos machines virtuelles et le monde extérieur. Les groupes de sécurité réseau peuvent être appliqués à un sous-réseau de réseau virtuel ou à une interface réseau spécifique pour une machine virtuelle. Au lieu de créer des règles ACL de point de terminaison, vous créez désormais des règles ACL de groupe de sécurité réseau. Ces règles ACL fournissent un meilleur contrôle que la simple création d’un point de terminaison pour transférer un port donné. Vous pouvez [en savoir plus sur les groupes de sécurité réseau](../articles/virtual-network/virtual-networks-nsg.md).

> [AZURE.TIP] Vous pouvez affecter des groupes de sécurité réseau à plusieurs sous-réseaux ou interfaces réseau. Il n’existe aucun mappage 1:1, ce qui signifie que vous pouvez créer un groupe de sécurité réseau avec un ensemble commun de règles ACL et l’appliquer à plusieurs sous-réseaux ou interfaces réseau. En outre, le groupe de sécurité réseau peut être appliqué aux ressources au sein de votre abonnement (sur la base des [Contrôles d’accès en fonction du rôle](../articles/active-directory/role-based-access-control-what-is.md).


## Vue d’ensemble des équilibrages de charge
Dans le modèle de déploiement Classic, Azure effectue pour vous les tâches de traduction d’adresses réseau (NAT) et de réacheminement de port sur un service cloud. Lorsque vous créez un point de terminaison, vous pouvez spécifier le port externe à exposer, ainsi que le port interne vers lequel diriger le trafic. Les groupes de sécurité réseau n’effectuent pas eux-mêmes ces tâches NAT et de réacheminement de port.

Pour pouvoir créer des règles NAT pour le réacheminement de port, créez un équilibreur Azure Load Balancer dans votre groupe de ressources. Là encore, l’équilibreur de charge est suffisamment précis pour s’appliquer uniquement aux machines virtuelles spécifiques si nécessaire. Les règles NAT d’Azure Load Balancer fonctionnent conjointement avec les règles ACL de groupe de sécurité réseau pour fournir plus de souplesse et de contrôle que lors de l’utilisation des points de terminaison de service cloud. Pour en savoir plus, consultez la [Vue d’ensemble de l’équilibrage de charge](../articles/load-balancer/load-balancer-overview.md).


## Règles ACL du groupe de sécurité réseau
Les règles ACL vous permettent de définir quel trafic peut entrer et sortir de votre machine virtuelle en fonction de ports, de plages de ports ou de protocoles spécifiques. Les règles sont affectées à des machines virtuelles individuelles ou à un sous-réseau. La capture d’écran suivante fournit un exemple des règles ACL pour un serveur web commun :

![Liste des règles ACL du groupe de sécurité réseau](./media/virtual-machines-common-endpoints-in-resource-manager/example-acl-rules.png)

Les règles ACL sont appliquées en fonction d’une mesure de priorité que vous spécifiez : plus la valeur est élevée, plus la priorité est faible. Chaque groupe de sécurité réseau comporte trois règles par défaut qui sont conçues pour gérer le flux de trafic réseau Azure, avec un `DenyAllInbound` explicite comme règle finale. Les règles ACL par défaut reçoivent une priorité faible pour ne pas interférer avec les règles que vous créez.


## Affectation de groupes de sécurité réseau
Vous affectez un groupe de sécurité réseau à un sous-réseau ou à une interface réseau. Cette approche vous permet d’être suffisamment précis lors de l’application de vos règles ACL à une machine virtuelle spécifique seulement, ou de garantir qu’un ensemble commun de règles ACL est appliqué à toutes les machines virtuelles d’un sous-réseau :

![Appliquer des groupes de sécurité réseau aux sous-réseaux ou aux interfaces réseau](./media/virtual-machines-common-endpoints-in-resource-manager/apply-nsg-to-resources.png)

Le comportement du groupe de sécurité réseau ne change pas selon qu’il est affecté à un sous-réseau ou à une interface réseau. Dans un scénario de déploiement courant, le groupe de sécurité réseau est affecté à un sous-réseau pour assurer la conformité de toutes les machines virtuelles attachées à ce sous-réseau. Vous pouvez en savoir plus sur [l’application de groupes de sécurité réseau aux ressources](../virtual-nework/virtual-networks-nsg.md#associating-nsgs).


## Comportement par défaut des groupes de sécurité réseau
Selon la manière dont vous créez votre groupe de sécurité réseau et le moment auquel vous le faites, les règles par défaut peuvent être créées pour autoriser l’accès RDP sur le port TCP 3389. Les machines virtuelles Linux autorisent l’accès SSH sur le port TCP 22. Ces règles ACL automatiques sont créées dans les conditions suivantes :

- Si vous créez une machine virtuelle Windows par le biais du portail et que vous acceptez l’action par défaut pour créer un groupe de sécurité réseau, une règle ACL autorisant le port TCP 3389 (RDP) est créée.
- Si vous créez une machine virtuelle Linux par le biais du portail et que vous acceptez l’action par défaut pour créer un groupe de sécurité réseau, une règle ACL autorisant le port TCP 22 (SSH) est créée.

Dans toutes les autres conditions, ces règles ACL par défaut ne sont pas créées. Vous ne pourrez pas vous connecter à votre machine virtuelle sans créer les règles ACL appropriées. Pour cela, vous devrez effectuer les actions courantes suivantes :

- Créer un groupe de sécurité réseau via le portail, indépendamment de la création de la machine virtuelle.
- Créer un groupe de sécurité réseau par programmation via PowerShell, l’interface de ligne de commande Azure, les API Rest, etc.
- Créer une machine virtuelle et l’affecter à un groupe de sécurité réseau existant qui n’a pas déjà de règle ACL appropriée définie.

Dans tous les cas précédents, vous devez créer des règles ACL pour votre machine virtuelle afin d’autoriser les connexions de gestion à distance appropriées.


## Comportement par défaut d’une machine virtuelle sans groupe de sécurité réseau
Vous pouvez créer une machine virtuelle sans créer un groupe de sécurité réseau. Dans ces situations, vous pouvez vous connecter à votre machine virtuelle à l’aide de RDP ou SSH sans créer de règles ACL. De même, si vous avez installé un service web sur le port 80, ce service est automatiquement accessible à distance. La machine virtuelle a tous les ports ouverts.

> [AZURE.NOTE] Pour les connexions à distance, vous devez toujours avoir une adresse IP publique affectée à une machine virtuelle. L’absence de groupe de sécurité réseau pour le sous-réseau et l’interface réseau n’expose pas la machine virtuelle au trafic externe. Lors de la création d’une machine virtuelle via le portail, l’action par défaut consiste à créer une nouvelle adresse IP publique. Pour toutes les autres formes de création d’une machine virtuelle comme PowerShell, l’interface de ligne de commande Azure ou le modèle Resource Manager, une adresse IP publique n’est pas automatiquement créée, sauf en cas de demande explicite. L’action par défaut par le biais du portail consiste également à créer un groupe de sécurité réseau. Vous ne devriez donc pas vous retrouver avec une machine virtuelle exposée ne disposant d’aucun filtrage réseau.


## Présentation des règles NAT et de l’équilibrage de charge
Le modèle de déploiement Classic vous permet de créer des points de terminaison qui effectuent également un réacheminement de port. Lorsque vous créez une machine virtuelle dans le modèle de déploiement Classic, les règles ACL pour RDP ou SSH sont automatiquement créées. Elles n’exposent pas le port TCP 3389 ou le port TCP 22 respectivement au monde extérieur. Au lieu de cela, un port TCP de valeur élevée mappé sur le port interne approprié est exposé. Vous pouvez également créer vos propres règles ACL de la même manière, par exemple en exposant un serveur web sur le port TCP 4280 au monde extérieur. Ces règles ACL et ces mappages de port sont illustrés dans la capture d’écran suivante du portail Classic :

![Réacheminement de port avec des points de terminaison Classic](./media/virtual-machines-common-endpoints-in-resource-manager/classic-endpoints-port-forwarding.png)

Avec les groupes de sécurité réseau, cette fonction de réacheminement de port est gérée par un équilibrage de charge. Vous pouvez en savoir plus sur [l’équilibrage de charge dans Azure](../articles/load-balancer/load-balancer-overview.md). Vous trouverez un exemple d’équilibrage de charge avec une règle NAT de réacheminement du port TCP 4222 vers le port TCP 22 interne d’une machine virtuelle dans la capture d’écran suivante effectuée à partir du portail :

![Règles NAT d’équilibrage de charge pour le réacheminement de port](./media/virtual-machines-common-endpoints-in-resource-manager/load-balancer-nat-rules.png)

> [AZURE.NOTE] Lorsque vous implémentez un équilibreur de charge, vous n’affectez généralement pas d’adresse IP publique à la machine virtuelle elle-même. Au lieu de cela, une adresse IP publique est affectée à l’équilibreur de charge. Vous devez toujours créer un groupe de sécurité réseau et des règles ACL pour définir le flux du trafic entrant et sortant de la machine virtuelle. Les règles NAT d’équilibrage de charge consistent simplement à définir quels ports sont autorisés via l’équilibrage de charge et comment ils sont distribués sur les machines virtuelles principales. Vous devez donc créer une règle NAT pour que le trafic circule via l’équilibrage de charge, puis créer une règle ACL de groupe de sécurité réseau pour autoriser le trafic à atteindre la machine virtuelle.

<!---HONumber=AcomDC_0810_2016-->