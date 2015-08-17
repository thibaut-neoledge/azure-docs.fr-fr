## Concepts de base du réseau virtuel

### Qu’est un réseau virtuel Azure (VNet) ?

Vous pouvez utiliser des réseaux virtuels pour configurer et gérer des réseaux privés virtuels (VPN) dans Azure et, éventuellement, lier les réseaux virtuels avec les autres réseaux virtuels dans Azure ou avec votre infrastructure informatique locale pour créer des solutions hybrides ou intersite. Chaque réseau virtuel que vous créez dispose de son propre bloc CIDR et peut être lié à d’autres réseaux virtuels et locaux, du moment que les blocs CIDR ne sont pas en conflit. Vous pouvez également contrôler les paramètres de serveur DNS pour les réseaux virtuels et la segmentation du réseau virtuel en sous-réseaux.

Utilisez les réseaux virtuels pour effectuer les actions suivantes :

- Créer un réseau virtuel dédié uniquement au cloud privé.
									
	Vous n’avez pas toujours besoin d’une configuration intersite pour votre solution. Lorsque vous créez un réseau virtuel, vos services et les machines virtuelles au sein de votre réseau virtuel peuvent communiquer directement et en toute sécurité dans le cloud. Cela maintient le trafic de façon sécurisée dans le réseau virtuel, tout en vous permettant de configurer des connexions au point de terminaison pour les machines virtuelles et les services qui requièrent une communication Internet dans le cadre de votre solution.

- Étendre en toute sécurité votre centre de données.
									
	Avec les réseaux virtuels, vous pouvez créer des VPN site à site (S2S) traditionnels pour faire évoluer en toute sécurité la capacité de votre centre de données. Les VPN S2S utilisent le protocole IPSEC pour fournir une connexion sécurisée entre votre passerelle VPN d’entreprise et Azure.

- Activer les scénarios de cloud hybride.
									
	Les réseaux virtuels vous donnent la possibilité de prendre en charge une variété de scénarios de nuage hybride. Vous pouvez connecter en toute sécurité des applications informatiques à n’importe quel type de système local, comme les ordinateurs centraux et les systèmes Unix.

### Comment savoir si j’ai besoin d’un réseau virtuel ?

Voir [Présentation du réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=296649) pour afficher la table des décisions qui vous aidera à déterminer la meilleure option de conception de réseau pour vous.

### Comment faire pour démarrer ?

Voir [Documentation du réseau virtuel](http://azure.microsoft.com/documentation/services/virtual-network/)pour commencer. Cette page comporte des liens vers les étapes de configuration courantes, ainsi que des informations qui vous aideront à comprendre les éléments à prendre en considération lors de la conception du réseau virtuel.

### Quels services puis-je utiliser avec les réseaux virtuels ?

Les réseaux virtuels peuvent être utilisés avec un large éventail de services Azure, tels que les services cloud (PaaS), les machines virtuelles et les applications web. Toutefois, il existe quelques services qui ne sont pas pris en charge sur un réseau virtuel. Vérifiez que le service spécifique que vous souhaitez utiliser est compatible.

### Puis-je utiliser des réseaux virtuels sans connectivité intersite ?

Oui. Vous pouvez utiliser un réseau virtuel sans connectivité de site à site. Cela est particulièrement utile si vous souhaitez exécuter des contrôleurs de domaine et les batteries de serveurs SharePoint dans Azure.

## Configuration du réseau virtuel

### Quels outils utiliser pour créer un réseau virtuel ?

Vous pouvez utiliser les outils suivants pour créer ou configurer un réseau virtuel :

- Vous pouvez utiliser le portail de gestion Azure. Voir [Gestion des propriétés du réseau virtuel](virtual-networks-settings.md).

- Vous pouvez utiliser un fichier de configuration réseau (netcfg). Voir [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](virtual-networks-using-network-configuration-file.md).

### Quelles plages d’adresses puis-je utiliser dans mes réseaux virtuels ?

Vous pouvez utiliser des plages d’adresses IP publiques et toute plage d’adresses IP définies dans[RFC 1918](http://tools.ietf.org/html/rfc1918).

### Puis-je avoir des adresses IP publiques dans mes réseaux virtuels ?

Oui. Pour plus d’informations sur les plages d’adresses IP publiques, voir [Espace d’adressage IP Public dans un réseau virtuel (VNet)](virtual-networks-public-ip-within-vnet.md). N’oubliez pas que vos adresses IP publiques ne seront pas directement accessibles à partir d’Internet.

### Y a-t-il une limite au nombre de sous-réseaux dans mon réseau virtuel ?

Il n’existe aucune limite sur le nombre de sous-réseaux que vous utilisez dans un réseau virtuel. Tous les sous-réseaux doivent être entièrement contenus dans l’espace d’adressage de réseau virtuel et ne doivent pas se chevaucher.

### Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?

Azure réserve des adresses IP dans chaque sous-réseau. La première et la dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, ainsi que deux adresses supplémentaires utilisées pour les services Azure.

### Quelle taille peuvent avoir les réseaux virtuels et les sous-réseaux ?

Le plus petit sous-réseau pris en charge est /29 et le plus grand est /8 (à l’aide de définitions de sous-réseau CIDR).

### Puis-je ajouter mes VLAN à Azure à l’aide de réseaux virtuels ?

Non. Les réseaux virtuels sont des superpositions de couche 3. Azure ne prend en charge aucune sémantique de couche 2.

### Puis-je spécifier des stratégies de routage personnalisées sur des réseaux virtuels et des sous-réseaux ?

Oui. Vous pouvez utiliser le routage défini par utilisateur (UDR, User Defined Routing). Pour plus d’informations sur l’UDR, voir [Itinéraires définis d’utilisateur et transfert IP](virtual-networks-udr-overview.md).

### Les réseaux virtuels prennent-ils en charge la multidiffusion ou la diffusion ?

Non. Nous ne prenons pas en charge la multidiffusion ou la diffusion.

### Quels protocoles puis-je utiliser au sein de réseaux virtuels ?

Vous pouvez utiliser des protocoles IP standard au sein des réseaux virtuels. Cependant, la multidiffusion, la diffusion, les paquets encapsulés IP dans IP et les paquets Encapsulation générique de routage (GRE, Generic Routing Encapsulation) sont bloqués dans les réseaux virtuels. Les protocoles standard compatibles sont les suivants :

- TCP
- UDP
- ICMP

### Puis-je effectuer un test Ping sur mes routeurs par défaut au sein d’un réseau virtuel ?

Non.

### Puis-je utiliser l’application tracert pour diagnostiquer la connectivité ?

Non.

### Puis-je ajouter des sous-réseaux après avoir créé le réseau virtuel ?

Oui. Des sous-réseaux peuvent être ajoutés aux réseaux virtuels à tout moment, du moment que l’adresse de sous-réseau ne fait pas partie d’un autre sous-réseau dans le réseau virtuel.

### Puis-je modifier la taille de mon réseau virtuel après sa création ?

Vous pouvez ajouter, supprimer, développer ou réduire un sous-réseau si aucune machine virtuelle ou aucun service n’est déployé à l’aide des applets de commande PowerShell ou du fichier NETCFG. Vous pouvez également ajouter, supprimer, développer ou réduire des préfixes tant que les sous-réseaux contenant des machines virtuelles ou des services ne sont pas affectés par la modification.

### Puis-je modifier des sous-réseaux après leur création ?

Oui. Vous pouvez ajouter, supprimer et modifier les blocs CIDR utilisés par un réseau virtuel.

### Puis-je me connecter à Internet si j’exécute mes services dans un réseau virtuel ?

Oui. Tous les services déployés au sein d’un réseau virtuel peuvent être connectés à Internet. Chaque service cloud déployé dans Azure dispose d’une adresse IP virtuelle publiquement adressable qui lui est assignée. Vous devez définir des points de terminaison d’entrée pour les rôles PaaS et pour les machines virtuelles afin de permettre à ces services d’accepter les connexions à partir d’Internet.

### Les réseaux virtuels prennent-ils en charge IPv6 ?

Non. Vous ne pouvez pas utiliser IPv6 avec les réseaux virtuels pour l’instant.

### Un réseau virtuel peut-il couvrir plusieurs régions ?

Non. Un réseau virtuel est limité à une seule région.

### Puis-je connecter un réseau virtuel à un autre réseau virtuel dans Azure ?

Oui. Vous pouvez créer une communication de réseau virtuel à réseau virtuel à l’aide des API REST ou de Windows PowerShell. Voir [Configuration d’une connexion de réseau virtuel à réseau virtuel](virtual-networks-configure-vnet-to-vnet-connection.md).

## Résolution de noms pour les machines virtuelles et les instances de rôle

### Quelles sont les options DNS pour les réseaux virtuels ?

Utilisez la table des décisions sur la page [Résolution de noms pour les machines virtuelles et les instances de rôle](virtual-networks-name-resolution-for-vms-and-role-instances.md) pour plus informations sur les options DNS disponibles.

### Puis-je spécifier des serveurs DNS pour un réseau virtuel ?

Oui. Vous pouvez spécifier des adresses IP de serveur DNS dans les paramètres de réseau virtuel. Elles seront appliquées en tant que serveurs DNS par défaut pour toutes les machines virtuelles du réseau virtuel.

### Combien de serveurs DNS puis-je spécifier ?

Vous pouvez spécifier jusqu’à 12 serveurs DNS.

### Puis-je modifier mes serveurs DNS une fois que le réseau créé ?

Oui. Vous pouvez modifier la liste des serveurs DNS de votre réseau virtuel à tout moment. Si vous modifiez la liste des serveurs DNS, vous devez redémarrer chacune des machines virtuelles dans votre réseau virtuel de façon à sélectionner le nouveau serveur DNS.


### Qu’est ce qu’un serveur DNS fourni par Azure et fonctionne-t-il avec les réseaux virtuels ?

Le serveur DNS fourni par Azure est un serveur DNS mutualisé proposé par Microsoft. Azure enregistre toutes vos machines virtuelles et instances de rôle dans ce service. Ce service fournit la résolution de noms par nom d’hôte pour les machines virtuelles et les instances de rôles contenues dans le même service cloud et par nom de domaine complet pour les machines virtuelles et les instances de rôle du même réseau virtuel.

> [AZURE.NOTE]Il existe pour l’instant une limitation aux 100 premiers services cloud du réseau virtuel pour la résolution de nom inter-clients à l’aide du serveur DNS fourni par Azure. Si vous utilisez votre propre serveur DNS, cette restriction ne s’applique pas.

### Puis-je remplacer mes paramètres DNS sur la base machine virtuelle/service ?

Oui. Vous pouvez configurer des serveurs DNS sur une base de service cloud pour remplacer les paramètres de réseau par défaut. Toutefois, nous vous recommandons d’utiliser autant que possible le DNS à l’échelle du réseau.

### Puis-je afficher mon propre suffixe DNS ?

Non. Vous ne pouvez pas spécifier un suffixe DNS personnalisé pour vos réseaux virtuels.

## Réseaux virtuels et machines virtuelles

### Puis-je déployer des machines virtuelles sur un réseau virtuel ?

Oui.

### Puis-je déployer des machines virtuelles Linux sur un réseau virtuel ?

Oui. Vous pouvez déployer n’importe quel distributeur Linux pris en charge par Azure.

### Quelle est la différence entre une adresse IP virtuelle publique et une adresse IP interne ?

- Une adresse IP interne est l’adresse IP affectée à chaque machine virtuelle d’un réseau virtuel par DHCP. Elle n’est pas publique. Si vous avez créé un réseau virtuel, l’adresse IP interne est affectée à partir de la plage que vous avez spécifiée dans les paramètres de sous-réseau de votre réseau virtuel. Si vous n’avez pas de réseau virtuel, une adresse IP interne sera toujours attribuée. L’adresse IP interne restera avec la machine virtuelle pendant toute sa durée de vie, sauf si la machine virtuelle est désallouée.

- Une adresse IP virtuelle publique est l’adresse IP publique affectée à votre équilibreur de charge ou à votre service cloud. Elle n’est pas affectée directement à la carte-réseau de votre machine virtuelle. L’adresse IP virtuelle reste avec le service cloud auquel elle est affectée à jusqu’à ce que toutes les machines virtuelles de ce service cloud soient désallouées ou supprimées. Elle est ensuite libérée.

### Quelle adresse IP ma machine virtuelle recevra-t-elle ?

- **Adresse IP interne** : si vous déployez une machine virtuelle sur un réseau virtuel, celle-ci reçoit une adresse IP interne issue d’un pool d’adresses IP internes que vous avez spécifiées. Les machines virtuelles communiquent au sein des réseaux virtuels à l’aide des adresses IP internes. Bien qu’Azure affecte une adresse IP interne dynamique, vous pouvez demander une adresse statique pour votre machine virtuelle. Pour en savoir plus sur les adresses IP internes statiques, voir [Comment définir une adresse IP interne statique](virtual-networks-reserved-private-ip.md).

- **Adresse IP virtuelle** : votre machine virtuelle est également associée à une adresse IP virtuelle, même si une adresse IP virtuelle n’est jamais directement affectée à la machine virtuelle. Une adresse IP virtuelle est une adresse IP publique qui peut être affectée à votre service cloud. Vous pouvez, si vous le souhaitez, réserver une adresse IP virtuelle pour votre service cloud. Voir [Adresses IP publiques réservées](virtual-networks-reserved-public-ip.md).

- **Adresse IP publique de niveau d’instance** : vous pouvez également configurer une adresse IP publique de niveau d’instance (ILPIP). Les ILPIPs sont directement associées à la machine virtuelle, plutôt qu’au service cloud. Pour en savoir plus sur les ILPIPs, voir [Vue d’ensemble des adresses IP publiques de niveau d’instance](virtual-networks-instance-level-public-ip.md).

### Puis-je réserver une adresse IP interne pour une machine virtuelle que je créerai ultérieurement ?

Non. Vous ne pouvez pas réserver d’adresse IP interne. Si une adresse IP interne est disponible, elle sera affectée à une machine virtuelle ou à une instance de rôle par le serveur DHCP. Cette machine virtuelle peut être celle à laquelle vous souhaitez assigner l’adresse IP interne. Vous pouvez toutefois modifier l’adresse IP interne d’une machine virtuelle déjà créée et utiliser n’importe quelle adresse IP interne disponible.

### Les adresses IP internes peuvent-elles changer pour les machines virtuelles d’un réseau virtuel ?

Oui. Les adresses IP internes restent avec la machine virtuelle pour sa durée de vie, sauf si la machine virtuelle est désallouée. Lorsqu’une machine virtuelle est désallouée, l’adresse IP interne est publiée, sauf si vous avez défini une adresse IP interne statique pour votre machine virtuelle. Si la machine virtuelle est simplement arrêtée (et non placée dans l’état **Arrêté (désalloué)**), l’adresse IP reste affectée à la machine virtuelle.

### Puis-je attribuer manuellement des adresses IP aux cartes réseau des machines virtuelles ?

Non. Vous ne devez pas modifier les propriétés d’interface des machines virtuelles. Toute modification peut entraîner la perte de connectivité à la machine virtuelle.

### Qu’advient-il des adresses IP lorsqu’une machine virtuelle est arrêtée ?

Rien. Les adresses IP (adresse virtuelle publique et adresse IP interne) sont conservées avec votre service cloud ou votre machine virtuelle.

> [AZURE.NOTE]Si vous voulez simplement arrêter la machine virtuelle, n’utilisez pas le portail de gestion pour ce faire. Actuellement, le bouton d’arrêt désalloue la machine virtuelle.

### Puis-je déplacer des machines virtuelles d’un sous-réseau vers un autre sous-réseau dans un réseau virtuel sans redéploiement ?

Oui. Pour plus d’informations, consultez la [page suivante](virtual-networks-move-vm-role-to-subnet.md) :

### Puis-je configurer une adresse MAC statique pour ma machine virtuelle ?

Non. Une adresse MAC ne peut pas être configurée de manière statique.

### Une fois créée, l’adresse MAC reste-t-elle la même pour ma machine virtuelle ?

Non. L’adresse MAC d’une machine virtuelle peut changer pour différentes raisons. Si la machine virtuelle est placée dans l’état Arrêté (Désalloué), et que vous modifiez la taille de la machine virtuelle, ou qu’une réparation de service ou une maintenance planifiée est en cours sur le serveur hôte, l’adresse MAC n’est pas conservée.

### Puis-je me connecter à Internet à partir d’une machine virtuelle dans un réseau virtuel ?

Oui. Tous les services déployés au sein d’un réseau virtuel peuvent se connecter à Internet. En outre, chaque service cloud déployé dans Azure dispose d’une adresse IP virtuelle publiquement adressable qui lui est assignée. Vous devez définir des points de terminaison d’entrée pour les rôles PaaS et pour les points de terminaison pour les machines virtuelles afin de permettre à ces services d’accepter les connexions à partir d’Internet.

## Réseaux virtuels et services

### Quels services puis-je utiliser avec les réseaux virtuels ?

Vous pouvez uniquement utiliser les services de calcul au sein des réseaux virtuels. Les services de calcul sont limités aux services cloud (rôles web et de travail) et aux machines virtuelles.

### Puis-je utiliser des applications web avec un réseau virtuel ?

Non. Une application web Azure ne peut pas être déployée dans un réseau virtuel. Toutefois, des applications web peuvent se connecter en toute sécurité et accéder aux ressources de votre réseau virtuel Azure si vous avez configuré une connectivité de point à site pour votre réseau virtuel. Pour plus d’informations, consultez les liens suivants :

- [Intégration au réseau virtuel d’applications web](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Utilisation de l’intégration au réseau virtuel et des connexions hybrides avec les applications web](http://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Intégrer une application web à un réseau virtuel Azure](web-sites-integrate-with-vnet.md)


### Puis-je déployer des services cloud avec les rôles web et de travail (PaaS) dans un réseau virtuel ?

Oui. Vous pouvez déployer les services PaaS dans les réseaux virtuels.

### Comment déployer des rôles PaaS sur un réseau virtuel ?

Pour cela, vous devez spécifier le nom de réseau virtuel et les mappages rôle/sous-réseau dans la section de configuration réseau de votre configuration de service. Il est inutile de mettre à jour vos fichiers binaires.

### Puis-je faire entrer ou sortir mes services dans les réseaux virtuels ?

Non. Impossible de faire entrer ou de faire sortir des services dans les réseaux virtuels. Vous devez supprimer et redéployer le service pour le déplacer vers un autre réseau virtuel.

## Réseaux virtuels et sécurité

### Quel est le modèle de sécurité pour les réseaux virtuels ?

Les réseaux virtuels sont complètement isolés les uns des autres, ainsi que des autres services hébergés dans l’infrastructure Azure. Un réseau virtuel est une délimitation d’approbation.

### Puis-je définir des listes ACL ou des groupes NSG sur mes réseaux virtuels ?

Non. Vous ne pouvez pas associer des listes ACL ou des groupes NSG à des réseaux virtuels. Toutefois, des listes ACL peuvent être définies sur les points de terminaison d’entrée pour les machines virtuelles qui ont été déployées sur des réseaux virtuels, et des groupes NSG peuvent être associés à des sous-réseaux ou des cartes réseau.

### Existe-t-il un livre blanc sécurité des réseaux virtuels ?

Oui. Vous pouvez le télécharger [ici](http://go.microsoft.com/fwlink/?LinkId=386611).

## API, schémas et outils

### Puis-je gérer les réseaux virtuels à partir du code ?

Oui. Vous pouvez utiliser l’API REST pour gérer la connectivité des réseaux virtuels et intersite. Des informations supplémentaires sont disponibles [ici](http://go.microsoft.com/fwlink/?LinkId=296833).

### Existe-t-il une prise en charge des outils pour les réseaux virtuels ?

Oui. Vous pouvez utiliser les outils PowerShell et de ligne de commande pour une variété de plateformes. Des informations supplémentaires sont disponibles [ici](http://go.microsoft.com/fwlink/?LinkId=317721).

<!---HONumber=August15_HO6-->