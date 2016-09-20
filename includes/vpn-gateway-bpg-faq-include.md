### Le protocole BGP est-il pris en charge sur toutes les références de passerelle VPN Azure ?

Non. Le protocole BGP est pris en charge sur les passerelles VPN Azure **Standard** et **HighPerformance**. La référence **De base** N’EST PAS prise en charge.

### Puis-je utiliser le protocole BGP avec les passerelles VPN Azure basées sur des stratégies ?

Non. Le protocole BGP est pris en charge uniquement sur les passerelles VPN basées sur des itinéraires.

### Puis-je utiliser des NSA (numéros de système autonomes) privés ?

Oui, vous pouvez utiliser vos propres NSA publics ou privés pour vos réseaux locaux et les réseaux virtuels Azure.

#### Existe-t-il des NSA réservés par Azure ?

Oui. Les NSA suivants sont réservés par Azure pour les homologations internes et externes :

- NSA publics : 8075, 8076, 12076
- NSA privés : 65515, 65517, 65518, 65519, 65520

Vous ne pouvez pas spécifier ces NSA pour vos périphériques VPN locaux lors de la connexion à des passerelles VPN Azure.

### Puis-je utiliser le même NSA pour les réseaux VPN locaux et les réseaux virtuels Azure ?

Non. Vous devez attribuer des NSA différents à vos réseaux locaux et vos réseaux virtuels Azure si vous les interconnectez avec le protocole BGP. Le NSA 65515 est attribué aux passerelles VPN Azure par défaut, et ce, que le protocole BGP soit activé ou non pour la connectivité entre sites locaux. Vous pouvez remplacer cette valeur par défaut en attribuant un NSA différent lors de la création de la passerelle VPN, ou modifier le NSA après avoir créé la passerelle. Vous devez affecter vos NSA locaux aux passerelles de réseau local Azure correspondantes.

### Quels préfixes d’adresse les passerelles VPN Azure publieront-elles pour moi ?

La passerelle VPN Azure publiera les itinéraires suivants pour vos périphériques BGP locaux :

- préfixes d’adresse de votre réseau virtuel ;
- préfixes d’adresse de chaque passerelle de réseau local connectée à la passerelle VPN Azure ;
- itinéraires obtenus à partir d’autres sessions d’homologation BGP connectées à la passerelle VPN Azure, **à l’exception de l’itinéraire par défaut ou des itinéraires se chevauchant avec un préfixe de réseau virtuel**.

#### Puis-je publier l’itinéraire par défaut (0.0.0.0/0) vers les passerelles VPN Azure ?

Pas pour l'instant.

#### Puis-je publier les mêmes préfixes que ceux de mon adresse de réseau virtuel ?

Non. La publication des mêmes préfixes que ceux de votre adresse de réseau virtuel est bloquée ou filtrée par la plateforme Azure. Toutefois, vous pouvez publier un préfixe qui soit un sur-ensemble des éléments de votre réseau virtuel. Par exemple, votre réseau virtuel peut utiliser l’espace d’adresse 10.10.0.0/16 et vous pouvez publier 10.0.0.0/8.

### Puis-je utiliser le protocole BGP avec des connexions entre réseaux virtuels ?

Oui. Vous pouvez utiliser le protocole BGP pour les connexions entre sites locaux et entre réseaux virtuels.

### Puis-je combiner des connexions BGP et non-BGP pour mes passerelles VPN Azure ?

Oui. Vous pouvez combiner des connexions BGP et non-BGP pour la même passerelle VPN Azure.

### La passerelle VPN Azure prend-elle en charge le routage de transit BGP ?

Oui. Le routage de transit BGP est pris en charge. Cependant, les passerelles VPN Azure ne publieront **PAS** les itinéraires par défaut vers les autres homologues BGP. Pour activer le routage de transit via plusieurs passerelles VPN Azure, vous devez activer le protocole BGP sur toutes les connexions intermédiaires entre réseaux virtuels.

### Puis-je avoir plusieurs tunnels entre ma passerelle VPN Azure et mon réseau local ?

Oui. Vous pouvez établir plusieurs tunnels VPN S2S entre une passerelle VPN Azure et votre réseau local. Notez que tous ces tunnels seront comptabilisés par rapport au nombre total de tunnels pour vos passerelles VPN Azure. Par exemple, si vous avez deux tunnels redondants entre votre passerelle VPN Azure et l’un de vos réseaux locaux, 2 tunnels seront utilisés sur le quota total de votre passerelle VPN Azure (10 pour la référence Standard et 30 pour la référence HighPerformance).

### Puis-je avoir plusieurs tunnels entre deux réseaux virtuels Azure avec protocole BGP ?

Non. Les tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

### Puis-je utiliser BGP pour les passerelles VPN S2S dans une configuration de coexistence entre des passerelles ExpressRoute/S2S ?

Pas pour l'instant.

### Quelle adresse la passerelle VPN Azure utilise-t-elle pour l’IP d’homologue BGP ?

La passerelle VPN Azure alloue une adresse IP unique à partir de la plage GatewaySubnet définie pour le réseau virtuel. Par défaut, il s’agit de l’avant-dernière adresse de la plage. Par exemple, si votre GatewaySubnet est 10.12.255.0/27 (de 10.12.255.0 à 10.12.255.31), l’adresse IP d’homologue BGP sur la passerelle VPN Azure sera 10.12.255.30. Vous pouvez trouver ces informations lorsque vous affichez la liste des informations de passerelle VPN Azure.

### Quelles sont les conditions requises concernant les adresses IP d’homologue BGP sur mon périphérique VPN ?

Votre adresse d’homologue BGP local **NE DOIT PAS** être identique à l’adresse IP publique de votre périphérique VPN. Utilisez une adresse IP différente de l’IP d’homologue BGP pour votre périphérique VPN. Il peut s’agir d’une adresse affectée à l’interface de bouclage sur le périphérique. Spécifiez cette adresse sur la passerelle de réseau local correspondante, représentant l’emplacement.

### Que dois-je spécifier comme préfixes d’adresse pour la passerelle de réseau local lorsque j’utilise le protocole BGP ?

La passerelle de réseau local Azure spécifie les préfixes d’adresse initiaux pour le réseau local. Avec le protocole BGP, vous devez allouer le préfixe de l’hôte (préfixe /32) de votre adresse IP d’homologue BGP en tant qu’espace d’adressage pour ce réseau local. Si votre adresse IP d’homologue BGP est 10.52.255.254, vous devez spécifier « 10.52.255.254/32 » comme espace localNetworkAddressSpace de la passerelle de réseau local, représentant ce réseau local. Ainsi, vous vous assurez que la passerelle VPN Azure établit la session BGP via le tunnel VPN S2S.

### Que dois-je ajouter à mon périphérique VPN local pour la session d’homologation BGP ?

Vous devez ajouter un itinéraire hôte de l’adresse IP d’homologue BGP Azure sur votre périphérique VPN pointant vers le tunnel VPN S2S IPsec. Par exemple, si l’adresse IP d’homologue VPN Azure est « 10.12.255.30 », vous devez ajouter un itinéraire hôte pour « 10.12.255.30 » avec l’interface de tronçon suivant de l’interface de tunnel IPsec correspondante sur votre périphérique VPN.

<!---HONumber=AcomDC_0907_2016-->