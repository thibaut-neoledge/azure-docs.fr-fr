<properties
   pageTitle="Services cloud et sécurité réseau Microsoft | Microsoft Azure"
   description="Découvrez quelques-unes des fonctionnalités clés disponibles dans Azure pour créer des environnements réseau sécurisés"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="jonor;sivae"/>

# Services cloud et sécurité réseau Microsoft

Les services cloud Microsoft offrent une grande évolutivité des services et de l’infrastructure, des capacités de niveau entreprise et de nombreuses options pour la connectivité hybride. Les clients peuvent choisir d’accéder à ces services via Internet ou grâce à ExpressRoute, qui fournit une connectivité réseau privée. La plateforme Microsoft Azure permet aux clients d’étendre leur infrastructure dans le cloud et de générer des architectures à plusieurs niveaux en toute transparence. Pour compléter les services de Microsoft, des tiers peuvent activer des fonctionnalités améliorées en offrant des services de sécurité et des appliances virtuelles. Ce livre blanc fournit une vue d’ensemble des problèmes de sécurité et d’architecture dont les clients doivent tenir compte lorsqu’ils utilisent des services cloud Microsoft auxquels ils accèdent via ExpressRoute, ainsi que lorsqu’ils créent des services sécurisés dans le réseau virtuel de Microsoft Azure.

## Démarrage rapide
Le schéma logique ci-dessous peut vous orienter vers un exemple spécifique des différentes techniques de sécurité disponibles avec la plateforme Microsoft Azure. Utilisez-le pour trouver exactement ce dont vous avez besoin rapidement ou lisez le document depuis le début pour bénéficier d’explications sur la plupart des options disponibles. ![Organigramme des options de sécurité][0]

[Exemple 1 : Créer une zone démilitarisée (DMZ) pour protéger les applications avec des groupes de sécurité réseau (NSG)](#example-1-build-a-simple-dmz-with-nsgs)</br> [Exemple 2 : Créer une zone démilitarisée (DMZ) pour protéger les applications avec un pare-feu et des groupes de sécurité réseau (NSG)](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br> [Exemple 3 : Créer une zone démilitarisée (DMZ) pour protéger les réseaux avec un pare-feu, un itinéraire défini par l’utilisateur (UDR) et un groupe de sécurité réseau (NSG)](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br> [Exemple 4 : Ajouter une connexion hybride avec un réseau VPN d’appliance virtuelle de site à site](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br> [Exemple 5 : Ajouter une connexion hybride avec un réseau VPN de passerelle Azure de site à site](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br> [Exemple 6 : Ajouter une connexion hybride avec ExpressRoute](#example-6-adding-a-hybrid-connection-with-expressroute)</br> Des exemples relatifs à l’ajout de réseaux virtuels aux connexions de réseau virtuel, à la haute disponibilité et au chaînage de service seront ajoutés à ce document dans les prochains mois.

## Conformité et protection des infrastructures Microsoft
Microsoft joue un rôle de premier plan dans le soutien des initiatives de conformité requises par les clients d’entreprise. Voici quelques-unes des certifications de conformité pour Azure : ![Badges de conformité Azure][1]

Vous trouverez plus de détails à l’adresse : [http://azure.microsoft.com/support/trust-center/compliance/](http://azure.microsoft.com/support/trust-center/compliance/)

Microsoft a adopté une approche complète pour protéger l’infrastructure cloud nécessaire à l’exécution de services mondiaux très évolutifs. L’infrastructure cloud de Microsoft inclut les matériels, logiciels, réseaux, personnels d’administration et d’exploitation, ainsi que les centres de données physiques.

![Fonctionnalités de sécurité Azure][2]

L’approche ci-dessus fournit une base solide permettant aux clients de déployer leurs services dans le cloud de Microsoft. L’étape suivante explique aux clients comment concevoir et créer une architecture de sécurité afin de protéger ces services.

## Architectures de sécurité traditionnelles et zones démilitarisées (DMZ)
Bien que Microsoft investisse fortement dans la protection de l’infrastructure cloud, les clients doivent également protéger leurs services cloud et leurs groupes de ressources. Une approche multicouche de la sécurité constitue la meilleure défense. Une zone démilitarisée (DMZ) de sécurité réseau protège les ressources réseau internes d’un réseau non approuvé. Les zones DMZ sont un concept connu dans le domaine de l’architecture de sécurité des réseaux informatiques d’entreprise, faisant référence aux périmètres ou aux parties du réseau qui se trouvent entre Internet et l’infrastructure informatique protégée d’entreprise.

Dans les réseaux d’entreprise classiques, l’infrastructure centrale est puissamment fortifiée sur les périmètres, avec plusieurs couches d’appareils de sécurité. La limite de chaque couche se compose d’appareils et de points d’application de stratégies. Les appareils peuvent inclure des pare-feu, la prévention DDoS (Distributed Denial of Service, déni de service distribué), des systèmes IDS/IPS (systèmes de détection d’intrusion ou de protection contre les intrusions), appareils de réseaux VPN (Virtual Private Networks, réseaux privés virtuels), etc. L’application de stratégies peut prendre la forme de stratégies de pare-feu, de listes de contrôle d’accès ou de routage spécifique. La première ligne de défense du réseau, acceptant directement le trafic entrant à partir d’Internet, est une combinaison de ces mécanismes visant à bloquer les attaques et le trafic dangereux, tout en transmettant les demandes légitimes plus loin dans le réseau. Ce trafic est directement acheminé vers les ressources dans la zone DMZ. Cette ressource peut ensuite « communiquer » avec les ressources plus loin dans le réseau, en transitant par la limite suivante pour la validation avant d’être acheminée plus loin dans le réseau. La couche la plus à l’extérieur est appelée zone DMZ, car cette partie du réseau est exposée à Internet, généralement avec une certaine forme de protection des deux côtés de la zone DMZ. La figure ci-dessous montre un exemple de zone DMZ à sous-réseau unique dans un réseau d’entreprise avec deux limites de sécurité (zone DMZ-Internet et zone DMZ-réseau local virtuel principal).

![Une zone DMZ dans un réseau d’entreprise][3]

Il existe plusieurs architectures permettant d’implémenter une zone DMZ, allant d’un simple équilibreur de charge devant une batterie de serveurs web à une zone DMZ à sous-réseaux multiples, avec divers mécanismes à chaque limite visant à bloquer le trafic et à protéger les couches les plus profondes du réseau d’entreprise. La conception de la zone DMZ dépend des besoins spécifiques de l’organisation et de la tolérance au risque associée.

Tandis que les clients déplacent leurs charges de travail vers des clouds publics, il est essentiel de prendre en charge des fonctionnalités similaires pour l’architecture de zone DMZ dans Azure pour répondre aux exigences de conformité et de sécurité. Ce document fournit des instructions sur la manière dont les clients peuvent créer un environnement réseau sécurisé dans Azure. Il met l’accent sur les zones DMZ mais englobe des explications complètes sur de nombreux aspects de la sécurité réseau en commençant par les questions suivantes sans s’y limiter :

1.	Comment créer une zone DMZ dans Azure ?
2.	Quelles sont les fonctionnalités Azure disponibles pour créer une zone DMZ ?
3.	Comment les charges de travail principales peuvent-elles être protégées ?
4.	Comment les communications Internet sont-elles contrôlées pour les charges de travail dans Azure ?
5.	Comment les réseaux locaux peuvent-ils être protégés des déploiements dans Azure ?
6.	À quel moment les fonctionnalités de sécurité Azure natives doivent-elles être utilisées par rapport aux appliances ou services tiers ?

Le schéma suivant montre les différentes couches de sécurité offertes par Azure aux clients natifs dans la plateforme Azure elle-même et via des fonctions définies par les clients :

![Architecture de sécurité Azure][4]

Issue d’Internet, la protection DDoS Azure détecte les attaques à grande échelle menées contre Azure. Les points de terminaison publics définis par les clients se trouvent juste après ; ils servent à déterminer quel trafic peut traverser le service cloud pour atteindre le réseau virtuel. L’isolement du réseau virtuel Azure natif garantit l’isolement complet de tous les autres réseaux et la circulation du trafic uniquement via des méthodes et des chemins d’accès configurés par l’utilisateur. Ces chemins d’accès et méthodes sont la couche suivante où les groupes de sécurité réseau (NSG), l’itinéraire défini par l’utilisateur (UDR) et les appliances virtuelles réseau (NVA) peuvent servir à créer des limites de sécurité, y compris des zones DMZ, afin de protéger les déploiements d’applications dans le réseau protégé.

La section suivante fournit une vue d’ensemble des réseaux virtuels Azure. Les réseaux virtuels Azure sont créés par les clients et leurs charges de travail déployées y sont connectées. Les réseaux virtuels constituent la base de toutes les fonctionnalités de sécurité réseau requises pour établir une zone DMZ afin de protéger les déploiements des clients dans Azure.

## Vue d’ensemble des réseaux virtuels Azure
Avant que le trafic Internet puisse atteindre les réseaux virtuels Azure, il existe deux couches de sécurité inhérentes à la plateforme Azure.

1.	**Protection DDoS** : la protection DDoS (Distributed Denial of Service, déni de service distribué) est une couche du réseau physique Azure qui protège la plateforme Azure elle-même contre les attaques Internet à grande échelle où les personnes mal intentionnées utilisent plusieurs nœuds « robot » dans une tentative d’inondation d’un service Internet. Azure est doté d’un maillage de protection DDoS robuste sur toutes les connexions Internet entrantes. Cette couche de protection DDoS ne comporte aucun attribut configurable par l’utilisateur et n’est pas accessible au client. Elle protège Azure en tant que plateforme contre les attaques à grande échelle, mais ne protège pas directement les applications client individuelles. Des couches de résilience supplémentaires peuvent être configurées par le client contre une attaque localisée. Par exemple : si le client A a été attaqué avec une attaque DDoS à grande échelle sur un point de terminaison public, Azure bloque les connexions à ce service. Le client A a pu basculer vers un autre réseau virtuel ou point de terminaison de service non impliqué dans l’attaque afin de restaurer le service. Il convient de noter que, bien que le client A pourrait être affecté sur ce point de terminaison, aucun autre service en dehors de ce point de terminaison ne serait affecté. En outre, les autres clients et services ne verraient aucun impact de cette attaque.
2.	**Points de terminaison de service** : les points de terminaison permettant aux services cloud ou aux groupes de ressources de disposer d’adresses IP et de ports publics (sur Internet) exposés, le point de terminaison va transférer le trafic NAT aux adresses internes et au port sur le réseau virtuel Azure. Il s’agit du principal chemin d’accès pour que le trafic externe passe dans le réseau virtuel Azure. Les points de terminaison de service sont configurables par les utilisateurs pour déterminer le trafic qui est transféré et comment/où il est traduit sur le réseau virtuel. 

Une fois que le trafic atteint le réseau virtuel, de nombreuses fonctionnalités entrent en jeu car les réseaux virtuels Azure constituent la base des clients pour joindre leurs charges de travail et l’emplacement où s’applique la sécurité de base au niveau du réseau. Il s’agit d’un réseau privé (une superposition du réseau virtuel) dans Azure pour les clients dotés des caractéristiques et fonctionnalités suivantes :
 
1.	**Isolement de trafic** : un réseau virtuel est une limite d’isolement du trafic sur la plateforme Azure. Les machines virtuelles dans un réseau virtuel ne peuvent pas communiquer directement avec les machines virtuelles dans un autre réseau virtuel, même si les deux réseaux virtuels sont créés par le même client. Il s’agit d’une propriété critique qui garantit que les machines virtuelles et les communications du client restent privées dans un réseau virtuel. 
2.	**Topologie multiniveau** : les réseaux virtuels permettent aux clients de définir une topologie multiniveau en allouant des sous-réseaux et en spécifiant des espaces d’adressage distincts pour différents éléments ou « niveaux » de leurs charges de travail. Ces regroupements et topologies logiques permettent aux clients de définir différentes stratégies d’accès selon les types de charges de travail et également de contrôler les flux de trafic entre les niveaux. 
3.	**Connexions entre locaux** : les clients peuvent établir des connexions entre locaux entre un réseau virtuel et plusieurs sites locaux ou d’autres réseaux virtuels dans Azure via les passerelles VPN Azure ou des appliances virtuelles réseau tierces. Azure prend en charge les VPN de site à site (S2S) à l’aide des protocoles IPsec/IKE standards et de la connectivité privée ExpressRoute. 
4.	**Groupe de sécurité réseau** (NSG) : permet aux clients de créer des règles (listes de contrôle d’accès) avec le niveau de granularité souhaité : interfaces réseau, machines virtuelles individuelles ou sous-réseaux virtuels. Les clients peuvent contrôler l’accès en autorisant ou en refusant la communication entre les charges de travail au sein d’un réseau virtuel, à partir des systèmes sur les réseaux du client via les connexions entre locaux ou la communication Internet directe. 
5.	**Itinéraires définis par les utilisateurs** (UDR) et **transfert IP** : permettent de définir les chemins d’accès de communication entre les différents niveaux d’un réseau virtuel. Les clients peuvent déployer un pare-feu, les services IDS/IPS et d’autres appliances virtuelles et acheminer le trafic réseau via ces appliances de sécurité pour l’application de stratégies de limites de sécurité, l’audit et l’inspection.
6.	**Appliances virtuelles réseau** dans Azure Marketplace : les appliances de sécurité telles que les pare-feu, les équilibreurs de charge, les services IDS/IPS (services de détection/prévention des intrusions) sont disponibles dans Azure Marketplace et la galerie d’images de machines virtuelles. Pour finaliser un environnement réseau sécurisé multiniveau, les clients peuvent déployer ces appliances dans leurs réseaux virtuels et, en particulier, sur leurs limites de sécurité (y compris les sous-réseaux de zone DMZ).

Avec ces fonctionnalités et capacités, voici un exemple de conception d’architecture de zone DMZ dans Azure :

![Une zone DMZ dans un réseau virtuel Azure][5]

## Caractéristiques et conditions requises de zone DMZ
Cette section décrit les caractéristiques et les conditions requises d’une zone DMZ dans Azure. Comme indiqué précédemment, la zone DMZ est conçue pour être la partie frontale du réseau, créant une interface directe avec les communications d’Internet. Les paquets entrants doivent traverser les appliances de sécurité : pare-feu, IDS, IPS, etc., dans la zone DMZ avant d’atteindre les serveurs principaux. Les paquets Internet des charges de travail peuvent également traverser les appliances de sécurité dans la zone DMZ pour l’application des stratégies, l’inspection et les fins d’audit, avant de quitter le réseau. En outre, la zone DMZ peut également servir à héberger les passerelles VPN entre locaux entre les réseaux virtuels client et les réseaux locaux.

### Caractéristiques de zone DMZ
Faisant référence à la figure ci-dessus, une zone DMZ dans un réseau virtuel Azure, voici certaines caractéristiques d’une bonne zone DMZ :

- Zone DMZ accessible sur Internet (frontale) :
    - Le sous-réseau de la zone DMZ lui-même est accessible sur Internet et communique directement avec Internet
    - Les adresses IP publiques, adresses IP virtuelles et/ou points de terminaison de service transfèrent le trafic Internet vers le réseau frontal et les appareils
    - Le trafic entrant provenant d’Internet passe par des appareils de sécurité avant d’autres ressources sur le réseau frontal
    - Si la sécurité sortante est activée, le trafic passe par les appareils de sécurité lors de la dernière étape, avant d’être transféré à Internet
- « Réseau protégé » (principal) : vers l’infrastructure centrale
    - Aucun chemin d’accès direct depuis Internet vers l’infrastructure centrale
    - Les canaux vers l’infrastructure centrale DOIVENT traverser les appareils de sécurité comme les NSG, pare-feu ou appareils VPN de la zone DMZ
    - D’autres appareils de la zone DMZ NE DOIVENT PAS créer de pont avec Internet et l’infrastructure centrale
    - Les appareils de sécurité sur les limites de la zone DMZ accessibles sur Internet et du réseau protégé (par exemple, les deux icônes de pare-feu illustrées dans la figure ci-dessus) peuvent être une appliance virtuelle unique avec des règles ou des interfaces différenciées pour la limite Internet et principale (c’est-à-dire, un appareil, séparé logiquement, gérant la charge pour les deux limites de la zone DMZ)
- Autres pratiques courantes et contraintes
    - Les charges de travail dans la zone DMZ NE DOIVENT PAS stocker d’informations professionnelles stratégiques
    - L’accès et les mises à jour des configurations et déploiements de zone DMZ sont limités aux seuls administrateurs autorisés

### Conditions requises de zone DMZ
Pour activer ces caractéristiques, la liste suivante fournit des conseils sur les conditions requises de réseau virtuel pour implémenter une zone DMZ réussie :

- Architecture de sous-réseau : spécifiez le réseau virtuel, tel qu’un sous-réseau entier dédié en tant que zone DMZ, séparé par d’autres sous-réseaux dans le même réseau virtuel. Cela garantit que le trafic entre la zone DMZ et d’autres niveaux de sous-réseaux internes ou privés circule via un pare-feu ou une appliance virtuelle IDS/IPS sur les limites de sous-réseau avec les itinéraires définis par l’utilisateur.
- Groupe de sécurité réseau (NSG) pour la zone DMZ : le sous-réseau de la zone DMZ lui-même doit être ouvert pour permettre la communication entre la zone DMZ et Internet, mais cela ne signifie pas que les clients doivent contourner les NSG. Suivez les pratiques de sécurité courantes afin de minimiser les surfaces réseau exposées à Internet en verrouillant les plages d’adresses distantes autorisées à accéder aux déploiements et/ou aux protocoles et ports d’application spécifiques qui sont ouverts. Notez toutefois que cela n’est pas toujours possible. Par exemple, si les clients ont un site web externe dans Azure, alors la zone DMZ doit autoriser les demandes web entrantes à partir de toutes les adresses IP publiques, mais doit uniquement ouvrir les ports d’application web : TCP:80 et TCP:443.
- Table de routage pour zone DMZ : le sous-réseau de la zone DMZ lui-même doit être en mesure de communiquer directement avec Internet, mais ne doit pas permettre une communication directe vers et depuis des réseaux principaux ou locaux sans passer par un pare-feu ou une appliance de sécurité.
- Configuration des appliances de la zone DMZ : afin d’acheminer et d’inspecter les paquets entre la zone DMZ et le reste des réseaux protégés, les appliances de sécurité comme le pare-feu et les appareils IDS et IPS dans la zone DMZ peuvent être multirésidents, avec des cartes réseau distinctes pour la zone DMZ et les sous-réseaux principaux. Les cartes réseau dans la zone DMZ communiquent directement vers et depuis Internet, avec les NSG correspondants et la table de routage de la zone DMZ. Les cartes réseau se connectant aux sous-réseaux principaux disposent de NSG et de tables de routage plus restreints des sous-réseaux principaux correspondants.
- Fonctionnalités des appliances de sécurité de la zone DMZ : les appliances de sécurité déployées dans la zone DMZ exécutent généralement les fonctionnalités suivantes :
    - Pare-feu : application de règles de pare-feu ou de stratégies de contrôle d’accès pour les demandes entrantes
    - Détection et prévention des menaces : détection et atténuation des attaques malveillantes d’Internet
    - Audit et journalisation : gestion de journaux d’audit et d’analyse détaillés
    - Proxy inversé : redirection des demandes entrantes vers les serveurs principaux correspondants en mappant et en traduisant les adresses de destination sur les appareils frontaux de la zone DMZ, généralement les pare-feu, vers des adresses réelles de serveurs principaux
    - Proxy de transfert : indication de NAT et exécution d’audits pour la communication initiée depuis le réseau virtuel vers Internet
    - Routeur : action en tant que routeur afin de transférer le trafic entrant et entre sous-réseaux au sein du réseau virtuel
    - Appareil VPN : action en tant que passerelles VPN entre les locaux, pour la connectivité VPN entre les locaux entre les réseaux locaux client et les réseaux virtuels Azure
    - Serveur VPN : action en tant que serveurs VPN pour accepter les clients VPN se connectant aux réseaux virtuels Azure

>[AZURE.TIP]Gérez les personnes autorisées à accéder au matériel de sécurité de la zone DMZ de manière complètement différente des personnes autorisées en tant qu’administrateurs de développement, de déploiement ou d’opérations d’applications. Conserver une distinction entre ces groupes permet une répartition des tâches et empêche qu’une seule personne contourne les contrôles de sécurité des applications et de sécurité réseau.

### Questions à envisager lors de la création des limites du réseau
Lorsque les « réseaux Azure » sont évoqués dans cette section, sauf mention contraire spécifique, tous les réseaux (réseaux, réseaux virtuels ou sous-réseaux) font référence à des réseaux virtuels Azure privés créés par un administrateur d’abonnements et ne se rapportent pas aux réseaux physiques sous-jacents dans Azure.

En outre, les réseaux virtuels Azure sont souvent utilisés pour étendre les réseaux locaux traditionnels. Il est possible d’incorporer des solutions de mise en réseau hybrides de site à site ou ExpressRoute avec les architectures de zone DMZ. C’est une considération importante à l’heure de concevoir des limites de sécurité réseau. Elle est décrite dans les questions ci-dessous.

Ainsi, lors de la création d’un réseau avec une zone DMZ et plusieurs limites de sécurité, il est essentiel de répondre à trois questions.

#### 1) Combien de limites sont nécessaires ?
En premier lieu, il faut décider le nombre de limites de sécurité nécessaires dans un scénario donné :

- Une seule limite : une sur la zone DMZ frontale entre le réseau virtuel et Internet.
- Deux limites : une du côté Internet du réseau DMZ, l’autre entre le sous-réseau de réseau de la zone DMZ et les sous-réseaux principaux dans les réseaux virtuels Azure.
- Trois limites : une du côté Internet de la zone DMZ, entre la zone DMZ et les sous-réseaux principaux et entre les sous-réseaux principaux et le réseau local.
- N limites : selon les besoins de sécurité, il n’existe aucune limite au nombre de limites de sécurité qui peuvent être appliquées dans un réseau donné.

Le nombre et le type des limites nécessaires varient en fonction de la tolérance au risque d’une société et du scénario spécifique qui est implémenté. C’est souvent une décision conjointe prise par plusieurs groupes au sein d’une entreprise, souvent une équipe spécialiste des risques et de la conformité, une équipe chargée du réseau/de la plateforme et une équipe de développement des applications. Les personnes ayant des connaissances en matière de sécurité, des données impliquées et des technologies utilisées doivent avoir leur mot à dire dans cette décision, afin de garantir une position de sécurité appropriée pour chaque implémentation.

>[AZURE.TIP]Utilisez le plus petit nombre de limites afin de répondre aux exigences de sécurité pour une situation donnée. Plus le nombre de limites est élevé, plus les opérations et le dépannage peuvent être complexes, de même que les frais généraux impliqués dans la gestion de plusieurs stratégies de limites au fil du temps. Toutefois, des limites insuffisantes augmentent les risques. Il est essentiel de trouver le juste équilibre.

![Réseau hybride avec trois limites de sécurité][6]

La figure ci-dessus présente un aperçu de niveau élevé d’un réseau doté de trois limites de sécurité : une limite entre la zone DMZ et Internet, entre les sous-réseaux privés frontaux et principaux Azure et entre le sous-réseau principal Azure et le réseau d’entreprise local.

#### 2) Où se trouvent les limites ?
Une fois que le nombre de limites est décidé, la question de l’emplacement de leur implémentation constitue le point de décision suivant. Généralement, trois choix s’offrent à vous : 1) Utilisation d’un service intermédiaire basé sur Internet (par exemple, WAF basé sur le cloud, thème non abordé dans ce document), 2) Utilisation de fonctionnalités natives et/ou d’appliances virtuelles réseau dans Azure, 3) Utilisation d’appareils physiques sur le réseau local.

Sur les réseaux purement Azure, les options sont les fonctionnalités Azure natives (par exemple, les équilibreurs de charge Azure) ou les appliances virtuelles réseau à partir d’un riche écosystème de partenaires Azure (par exemple, les pare-feu Check Point).

Si une limite est nécessaire entre Azure et un réseau local, les appareils de sécurité peuvent résider sur un côté de la connexion (ou sur les deux côtés). Par conséquent une décision doit être prise concernant l’emplacement du matériel de sécurité.

Dans la figure ci-dessus, les limites Internet-zone DMZ et frontal-principal sont entièrement contenues dans Azure et doivent être des fonctionnalités Azure natives ou des appliances virtuelles réseau. Les appareils de sécurité sur la limite entre Azure (sous-réseau principal) et le réseau d’entreprise peuvent être du côté Azure ou du côté local ; une combinaison d’appareils des deux côtés est également possible. Ces options présentent des avantages et des inconvénients importants qui doivent être sérieusement envisagés.

Par exemple, utiliser un matériel de sécurité physique existant du côté réseau local. Pour : aucun nouveau matériel n’est requis, une nouvelle configuration suffit. Contre : tout le trafic doit revenir d’Azure vers le réseau local pour être vu par le matériel de sécurité. Ainsi, le trafic Azure à Azure pourrait subir une latence importante et affecter les performances des applications et l’expérience utilisateur s’il a été forcé vers le réseau local pour l’application de stratégies de sécurité.

#### 3) Comment les limites sont-elles implémentées ?
Chaque limite de sécurité aura probablement des exigences de fonctionnalités différentes (par exemple, des règles IDS et de pare-feu du côté Internet de la zone DMZ, mais des listes de contrôle d’accès uniquement entre la zone DMZ et le sous-réseau principal). Le choix des appareils à utiliser dépend du scénario et des exigences de sécurité. Les exemples 1, 2 et 3 de la zone DMZ ci-dessous évoquent certaines options qui peuvent être utilisées. L’examen des fonctionnalités réseau natives Azure et des appareils disponibles dans Azure à partir de l’écosystème de partenaires propose un large éventail d’options disponibles pour résoudre la quasi-totalité des scénarios.

Autre point de décision d’implémentation important : comment connecter le réseau local avec Azure ? Via la passerelle virtuelle Azure ou une appliance virtuelle réseau. Ces options sont décrites et évoquées plus en détail dans les exemples 4, 5 et 6 ci-dessous.

En outre, le trafic de réseau virtuel à réseau virtuel dans Azure pouvant être nécessaire, ces scénarios sont traités plus en détail dans les exemples 7 et 8 ci-dessous.

Une fois que les réponses aux questions ci-dessus sont connues, la section [Démarrage rapide](#fast-start) ci-dessus peut faciliter l’identification des exemples les plus appropriés pour un scénario donné.

## Création de limites de sécurité avec les réseaux virtuels Azure
### Exemple 1 : Créer une zone DMZ simple à l’aide de groupes de sécurité réseau (NSG)
[Revenir à Démarrage rapide](#fast-start) | [Instructions de création détaillées pour cet exemple][Example1]

![Zone DMZ entrante avec NSG][7]

#### Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Deux services cloud : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel « CorpNetwork » avec deux sous-réseaux « FrontEnd » et « BackEnd »
- Un groupe de sécurité réseau qui est appliqué aux deux sous-réseaux
- Un serveur Windows Server qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »)
- Un serveur Windows Server qui représente un serveur DNS (« DNS01 »)

Des détails supplémentaires sont disponibles pour la création de cet exemple (scripts et modèle ARM fournis) à la page [Instructions de création détaillées][Example1].

#### Description d’un groupe de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est créé, puis chargé avec six règles.

>[AZURE.TIP]En règle générale, vous devez d’abord créer vos règles d’« autorisation » spécifiques, puis les règles de « refus » plus génériques. La priorité donnée indique quelles sont les règles évaluées en premier. Une fois qu’il a été déterminé que le trafic s’applique à une règle spécifique, aucune autre règle n’est évaluée. Les règles du groupe de sécurité réseau peuvent s’appliquer dans le sens entrant ou sortant (du point de vue du sous-réseau).

Les règles qui suivent sont générées de façon déclarative pour le trafic entrant :

1.	Le trafic DNS interne (port 53) est autorisé.
2.	Le trafic RDP (port 3389) à partir d’Internet vers n’importe quelle machine virtuelle est autorisé.
3.	Le trafic HTTP (port 80) à partir d’Internet vers le serveur web (IIS01) est autorisé.
4.	Tout trafic (tous les ports) en provenance d’IIS01 vers AppVM1 est autorisé.
5.	Tout trafic (tous les ports) en provenance d’Internet vers l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6.	Tout trafic (tous les ports) en provenance du sous-réseau frontal vers le sous-réseau principal est refusé.

Lorsque ces règles sont associées à chacun des sous-réseaux, si une requête HTTP entrante arrive en provenance d’Internet à destination du serveur web, les règles 3 (autorisation) et 5 (refus) s’appliquent. Cependant, comme la règle 3 a une priorité plus élevée, elle seule s’applique, et la règle 5 n’entre pas en jeu. La requête HTTP est donc autorisée à accéder au serveur web. Si le même trafic tentait d’atteindre le serveur DNS01, la règle 5 (Refus) serait la première à s’appliquer et le trafic ne serait pas autorisé à franchir le serveur. La règle 6 (Refus) bloque la communication du sous-réseau frontal vers le sous-réseau principal (excepté le trafic autorisé dans les règles 1 et 4), ce qui protège le réseau principal en cas d’attaque d’une personne mal intentionnée sur l’application web sur le serveur frontal. Cette personne aurait alors un accès limité au réseau principal « protégé » (uniquement les ressources exposées sur le serveur AppVM01).

Il existe une règle sortante par défaut qui autorise le trafic sortant vers internet. Pour cet exemple, nous allons autoriser le trafic sortant sans modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, l’itinéraire défini par l’utilisateur est requis. Cette opération est expliquée dans l’exemple 3 ci-dessous.

#### Conclusion
Il s’agit d’un moyen relativement simple et direct d’isoler le sous-réseau principal du trafic entrant. Plus d’informations sur cet exemple, notamment :

- Comment créer cette zone DMZ avec des scripts PowerShell
- Comment créer cette zone DMZ avec un modèle ARM
- Descriptions détaillées de chaque commande NSG
- Scénarios de flux de trafic détaillés, montrant comment le trafic est autorisé ou refusé dans chaque couche

disponibles sur la page [Instructions de création détaillées][Example1]

### Exemple 2 : Créer une zone démilitarisée (DMZ) pour protéger les applications avec un pare-feu et des groupes de sécurité réseau (NSG)
[Revenir à Démarrage rapide](#fast-start) | [Instructions de création détaillées pour cet exemple][Example2]

![Zone DMZ entrante avec NVA et NSG][8]

#### Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Deux services cloud : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel « CorpNetwork » avec deux sous-réseaux « FrontEnd » et « BackEnd »
- Un groupe de sécurité réseau unique qui est appliqué aux deux sous-réseaux
- Une appliance virtuelle réseau, dans cet exemple un pare-feu, connecté au sous-réseau principal
- Un serveur Windows Server qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »)
- Un serveur Windows Server qui représente un serveur DNS (« DNS01 »)

Des détails supplémentaires sont disponibles pour la création de cet exemple (scripts et modèle ARM fournis) à la page [Instructions de création détaillées][Example2].

#### Description d’un groupe de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est créé, puis chargé avec six règles.

>[AZURE.TIP]En règle générale, vous devez d’abord créer vos règles d’« autorisation » spécifiques, puis les règles de « refus » plus génériques. La priorité donnée indique quelles sont les règles évaluées en premier. Une fois qu’il a été déterminé que le trafic s’applique à une règle spécifique, aucune autre règle n’est évaluée. Les règles du groupe de sécurité réseau peuvent s’appliquer dans le sens entrant ou sortant (du point de vue du sous-réseau).

Les règles qui suivent sont générées de façon déclarative pour le trafic entrant :

1.	Le trafic DNS interne (port 53) est autorisé.
2.	Le trafic RDP (port 3389) à partir d’Internet vers n’importe quelle machine virtuelle est autorisé.
3.	Tout trafic Internet (tous les ports) vers la NVA (pare-feu) est autorisé.
4.	Tout trafic (tous les ports) en provenance d’IIS01 vers AppVM1 est autorisé.
5.	Tout trafic (tous les ports) en provenance d’Internet vers l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6.	Tout trafic (tous les ports) en provenance du sous-réseau frontal vers le sous-réseau principal est refusé.

Lorsque ces règles sont associées à chacun des sous-réseaux, si une requête HTTP entrante arrive d’Internet à destination du pare-feu, les règles 3 (autorisation) et 5 (refus) s’appliquent. Cependant, comme la règle 3 a une priorité plus élevée, elle seule s’applique, et la règle 5 n’entre pas en jeu. La requête HTTP est donc autorisée à accéder au pare-feu. Si le même trafic tentait d’atteindre le serveur IIS01, même s’il se trouve sur le sous-réseau frontal, la règle 5 (Refus) s’appliquerait et le trafic ne serait pas autorisé à franchir le serveur. La règle 6 (Refus) bloque la communication du sous-réseau frontal vers le sous-réseau principal (excepté le trafic autorisé dans les règles 1 et 4), ce qui protège le réseau principal en cas d’attaque d’une personne mal intentionnée sur l’application web sur le serveur frontal. Cette personne aurait alors un accès limité au réseau principal « protégé » (uniquement les ressources exposées sur le serveur AppVM01).

Il existe une règle sortante par défaut qui autorise le trafic sortant vers internet. Pour cet exemple, nous allons autoriser le trafic sortant sans modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, l’itinéraire défini par l’utilisateur est requis. Cette opération est expliquée dans l’exemple 3 ci-dessous.

#### Description de la règle de pare-feu
Sur le pare-feu, vous devrez créer les règles de transfert. Étant donné que cet exemple achemine uniquement le trafic Internet entrant vers le pare-feu, puis vers le serveur web, seule une règle NAT de transfert est requise.

La règle de transfert accepte n’importe quelle adresse source entrante qui atteint le pare-feu en essayant d’atteindre HTTP (port 80 ou 443 pour HTTPS) et est envoyée à l’interface locale du pare-feu et redirigée vers le serveur web avec l’adresse IP 10.0.1.5.

#### Conclusion
Il s’agit d’un moyen relativement simple de protéger votre application avec un pare-feu et d’isoler le sous-réseau principal du trafic entrant. Plus d’informations sur cet exemple, notamment :

- Comment créer cet exemple DMZ avec des scripts PowerShell
- Comment créer cet exemple avec un modèle ARM
- Descriptions détaillées de chaque commande NSG et règle de pare-feu
- Scénarios de flux de trafic détaillés, montrant comment le trafic est autorisé ou refusé dans chaque couche

disponibles sur la page [Instructions de création détaillées][Example2]

### Exemple 3 : Créer une zone démilitarisée (DMZ) pour protéger les réseaux avec un pare-feu, un itinéraire défini par l’utilisateur (UDR) et un groupe de sécurité réseau (NSG)
[Revenir à Démarrage rapide](#fast-start) | [Instructions de création détaillées pour cet exemple][Example3]

![Zone DMZ bidirectionnelle avec NVA, NSG et UDR][9]

#### Configuration de l’environnement
Dans cet exemple, il existe un abonnement qui contient les éléments suivants :

- Trois services cloud : « SecSvc001 », « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel « CorpNetwork », avec trois sous-réseaux ; « SecNet », « FrontEnd » et « BackEnd »
- Une appliance virtuelle réseau, dans cet exemple un pare-feu, connecté au sous-réseau SecNet
- Un serveur Windows Server qui représente un serveur web d’application (« IIS01 »)
- Deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »)
- Un serveur Windows Server qui représente un serveur DNS (« DNS01 »)

Des détails supplémentaires sont disponibles pour la création de cet exemple (scripts et modèle ARM fournis) à la page [Instructions de création détaillées][Example3].

#### Description de l’itinéraire défini par l’utilisateur (UDR, User Defined Routing)
Par défaut, les itinéraires système suivants sont définis en tant que :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

Le VNETLocal constitue toujours le ou les préfixes d’adresse défini(s) du réseau virtuel correspondant à ce réseau spécifique (il passera d’un réseau virtuel à un autre en fonction de la manière dont chaque réseau virtuel spécifique est défini). Les itinéraires système restants sont statiques et par défaut, ils prennent la valeur indiquée ci-dessus.

Dans cet exemple, deux tables de routage sont créées, une pour chacun des sous-réseaux principal et frontal. Chaque table est chargée avec des itinéraires statiques appropriés au sous-réseau donné. Dans cet exemple, chaque table possède trois itinéraires qui dirigent tout le trafic (0.0.0.0/0) via le pare-feu (tronçon suivant = adresse IP de l’appliance virtuelle) :

1. Trafic de sous-réseau local avec un tronçon suivant défini pour permettre le trafic du sous-réseau local pour contourner le pare-feu
2. Trafic du réseau virtuel avec un tronçon suivant défini comme pare-feu. Cela remplace la règle par défaut qui autorise un acheminement direct du trafic de réseau virtuel
3. Ensemble du trafic restant (0/0) avec le tronçon suivant défini comme pare-feu

Une fois que les tables de routage sont créées, elles sont liées à leurs sous-réseaux. La table de routage de sous-réseau frontal, une fois créée et liée au sous-réseau, doit ressembler à ce qui suit :

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE]Il existe des limitations actuelles avec l’UDR et les réseaux hybrides. Cela est résolu dans une version ultérieure ; des exemples montrant comment activer votre zone DMZ avec ExpressRoute ou la mise en réseau de site à site sont décrits ci-dessous dans les exemples 3 et 4.

#### Description du transfert IP
Le transfert IP est associé à l’UDR. Il s’agit d’un paramètre d’appliance virtuelle qui permet de recevoir du trafic pas spécialement adressé à l’appliance, puis de transférer ce trafic vers sa destination finale.

Par exemple, si le trafic à partir d’AppVM01 fait une demande au serveur DNS01, l’UDR l’achemine vers le pare-feu. Lorsque le transfert IP est activé, le trafic de la destination de DNS01 (10.0.2.4) est accepté par l’appliance (10.0.0.4), puis transféré vers sa destination finale (10.0.2.4). Si le routage IP n’est pas activé sur le pare-feu, le trafic n’est pas accepté par l’appliance, même si le tronçon suivant de la table de routage est le pare-feu. Pour utiliser une appliance virtuelle, il est essentiel de ne pas oublier d’activer le transfert IP en conjonction avec l’itinéraire défini par l’utilisateur.

#### Description d’un groupe de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est créé, puis chargé avec une seule règle. Ce groupe est ensuite lié uniquement aux sous-réseaux frontaux et principaux (et pas au SecNet). La règle suivante est générée de manière déclarative :

1.	Tout le trafic (tous les ports) depuis Internet vers l’ensemble du réseau virtuel entier (tous les sous-réseaux) est refusé.

Bien que dans cet exemple, on utilise des NSG, son principal objectif est celui d’une couche secondaire de défense contre les erreurs de configuration manuelle. Nous voulons bloquer tout trafic entrant en provenance d’Internet vers les sous-réseaux frontal ou principal, le trafic doit circuler uniquement via le sous-réseau SecNet vers le pare-feu (puis, le cas échéant, sur les sous-réseaux frontal ou principal). En outre, avec les règles UDR en place, tout trafic ayant atteint les sous-réseaux principal ou frontal est dirigé vers le pare-feu (grâce à l’UDR). Le pare-feu serait considéré comme un flux asymétrique et abandonnerait le trafic sortant. Par conséquent, il existe trois couches de sécurité protégeant les sous-réseaux frontaux et principaux ; (1) aucun point de terminaison n’est ouvert sur les services cloud FrontEnd001 et BackEnd001, (2) NSG refusant le trafic provenant d’Internet, (3) pare-feu abandonnant le trafic asymétrique.

Point intéressant concernant le groupe de sécurité réseau dans cet exemple : il contient une seule règle qui consiste à refuser le trafic internet de l’ensemble du réseau virtuel qui inclut le sous-réseau de sécurité. Toutefois, étant donné que le NSG est associé uniquement aux sous-réseaux frontaux et principaux, la règle n’est pas exécutée sur le trafic entrant du sous-réseau de sécurité. Par conséquent, même si la règle NSG n’indique aucun trafic Internet sur une adresse de réseau virtuel, le NSG n’est jamais lié au sous-réseau de sécurité, le trafic passe au sous-réseau de sécurité.

#### Règles de pare-feu
Sur le pare-feu, vous devrez créer les règles de transfert. Étant donné que le pare-feu bloque ou transfère le trafic entrant, sortant ou intra-réseau virtuel, de nombreuses règles de pare-feu. En outre, tout trafic entrant atteindra l’adresse IP publique de service de sécurité (sur différents ports), pour être traité par le pare-feu. L’une des meilleures pratiques consiste à faire un schéma des flux logiques avant de configurer les règles de sous-réseau et de pare-feu afin d’éviter la reprise du travail par la suite. La figure qui suit est une vue logique des règles de pare-feu de cet exemple :
 
![Affichage logique des règles de pare-feu][10]

>[AZURE.NOTE]Selon l’appliance virtuelle réseau utilisée, les ports de gestion peuvent varier. Dans cet exemple, un pare-feu de NG Barracuda utilisant les ports 22, 801 et 807 est référencé. Veuillez consulter la documentation du fournisseur d’appliance pour rechercher les ports exacts utilisés pour la gestion de l’appareil utilisé.

#### Description des règles de pare-feu
Dans le schéma logique ci-dessus, le sous-réseau de sécurité n’est pas affiché car le pare-feu est la seule ressource de ce sous-réseau, et ce schéma présente les règles de pare-feu et la façon dont elles autorisent ou refusent les flux et non les itinéraires réels. En outre, les ports externes sélectionnés pour le trafic RDP appartiennent à la plage supérieure de ports (8014 – 8026) et ont été sélectionnés pour s’aligner à peu près sur les deux derniers octets de l’adresse IP locale, pour faciliter la lecture (par exemple, l’adresse du serveur local 10.0.1.4 est associée à un port externe 8014), cependant, tous les ports supérieurs non conflictuels peuvent être utilisés.

Dans cet exemple, nous avons besoin de 7 types de règles, qui se présentent comme suit :

- Règles externes (pour le trafic entrant) :
  1.	Règle de gestion de pare-feu : cette règle de redirection de l’application autorise le trafic à traverser les ports de gestion de l’appliance virtuelle réseau.
  2.	Règles de RDP (pour chaque serveur Windows) : ces quatre réseaux (une pour chaque serveur) permettront la gestion des serveurs individuels via RDP. Ces éléments pourraient être regroupés en une règle, en fonction de la capacité de l’appliance virtuelle réseau utilisée.
  3.	Règles de trafic d’application : elles sont au nombre de deux, la première correspondant au trafic web frontal, et la seconde, pour le trafic de l’ordinateur principal (par exemple, serveur web vers couche de données). La configuration de ces règles dépend de l’architecture réseau (sur lequel sont placés vos serveurs) et les flux de trafic (direction du flux de trafic et ports utilisés).
      - La première règle permet au trafic d’application réel de parvenir au serveur d’applications. Les autres règles concernent la sécurité, la gestion, etc., les règles d’application sont celles qui permettent aux utilisateurs externes ou aux services d’accéder aux applications. Pour cet exemple, il existe un serveur web sur le port 80, et donc une seule règle d’application redirige le trafic entrant vers l’adresse IP externe, vers l’adresse IP interne des serveurs web. L’adresse réseau de la session de trafic redirigée sera traduite vers le serveur interne.
      - La seconde règle de trafic d’application et la règle principale qui permet au serveur web de communiquer avec le serveur AppVM01 (et non AppVM02) via n’importe quel port.
- Règles internes (pour le trafic réseau virtuel interne)
  4.	Règle de sortie vers Internet : cette règle autorise le transfert du trafic en provenance de n’importe quel réseau vers les réseaux sélectionnés. Cette règle est généralement une règle par défaut déjà sur le pare-feu, mais à l’état désactivé. Pour cet exemple, cette règle doit être activée.
  5.	Règle DNS : cette règle autorise uniquement le trafic DNS (port 53) vers le serveur DNS. Pour cet environnement, la majeure partie du trafic du serveur frontal vers le serveur principal est bloquée. Cette règle autorise spécifiquement DNS à partir de n’importe quel sous-réseau local.
  6.	Règle sous-réseau à sous-réseau : cette règle permet à n’importe quel serveur principal du sous-réseau de se connecter à n’importe quel serveur du sous-réseau frontal (mais pas l’inverse).
- Règle de prévention de défaillance (pour le trafic ne répondant à aucun des éléments ci-dessus) :
  7.	Refuser toutes les règles de trafic : il doit toujours s’agir de la dernière règle (en termes de priorité) et par conséquent si un trafic ne correspond à aucune des règles qui précèdent, il sera abandonné par cette règle. Il s’agit d’une règle par défaut et généralement activée, et en général, aucune modification n’est nécessaire.

>[AZURE.TIP]Sur la deuxième règle de trafic de l’application, n’importe quel port est autorisé pour simplifier cet exemple. Dans un scénario réel, le port le plus spécifique et les plages d’adresses doivent être utilisés pour réduire la surface d’attaque de cette règle.

Une fois que toutes les règles ci-dessus sont créées, il est important de revoir la priorité de chaque règle pour s’assurer que le trafic sera autorisé ou rejeté comme souhaité. Pour cet exemple, les règles sont dans l’ordre de priorité.

#### Conclusion
C’est une façon plus complexe mais plus complète de protéger et d’isoler le réseau (l’exemple 2 protégeait simplement l’application et l’exemple 1 isolait simplement les sous-réseaux). Cette conception permet de surveiller le trafic dans les deux sens ; elle protège non seulement le serveur d’applications entrantes mais applique également la stratégie de sécurité réseau pour tous les serveurs sur ce réseau. En outre, selon l’appliance utilisée, une connaissance et un audit complets du trafic sont possibles. Plus d’informations sur cet exemple, notamment :

- Comment créer cet exemple DMZ avec des scripts PowerShell
- Comment créer cet exemple avec un modèle ARM
- Descriptions détaillées de chaque UDR, commande NSG et règle de pare-feu
- Scénarios de flux de trafic détaillés, montrant comment le trafic est autorisé ou refusé dans chaque couche

disponibles sur la page [Instructions de création détaillées][Example3]

### Exemple 4 : Ajouter une connexion hybride avec un réseau VPN d’appliance virtuelle de site à site
[Revenir à Démarrage rapide](#fast-start) | Instructions de création détaillées bientôt disponibles

![Zone DMZ avec réseau hybride connecté à une NVA][11]

#### Configuration de l’environnement
La mise en réseau hybride à l’aide d’une appliance virtuelle réseau (Network Virtual Appliance, NVA) peut être ajoutée à l’un des types de zone DMZ décrits dans l’exemple 1, 2 ou 3.

Comme indiqué dans la figure ci-dessus, une connexion VPN sur Internet (de site à site) permet de connecter un réseau local à un réseau virtuel Azure via une appliance virtuelle réseau.

>[AZURE.NOTE]Si vous utilisez ExpressRoute avec l’option d’homologation Azure public activée, un itinéraire statique devra être créé pour les routages vers l’adresse IP VPN NVA en dehors du périmètre Internet de votre entreprise et non via le périmètre WAN ExpressRoute. Cela est dû à la traduction d’adresses réseau requise sur l’option d’homologation Azure public ExpressRoute qui arrêtera probablement la session VPN (généralement, le protocole IPSec n’aime pas la traduction d’adresses réseau).

Une fois que la connexion VPN est en place, la NVA devient le « hub » central pour tous les réseaux et sous-réseaux. Les règles de transfert de pare-feu déterminent quels flux de trafic sont autorisés, se voient appliquer la traduction d’adresses réseau, sont redirigées ou sont abandonnés (même pour les flux de trafic entre le réseau local et Azure, si les flux sont conçus de cette façon).

Les flux de trafic doivent être envisagés avec précaution car ils peuvent être optimisés ou détériorés par ce modèle de conception, selon le cas d’utilisation spécifique.

L’utilisation de l’environnement créé dans l’exemple 3, « Créer une zone démilitarisée (DMZ) pour protéger les réseaux avec un pare-feu, un itinéraire défini par l’utilisateur (UDR) et un groupe de sécurité réseau (NSG) », et l’ajout d’une connexion réseau hybride VPN de site à site produirait la conception suivante :

![Zone DMZ avec NVA connectée à l’aide d’un VPN de site à site][12]

Dans la conception ci-dessus, le routeur local ou tout autre appareil réseau qui est compatible avec votre appliance virtuelle réseau pour VPN serait le client VPN. Cet appareil physique est responsable de l’initialisation et de la maintenance de la connexion VPN avec votre appliance virtuelle réseau.

Logiquement pour la NVA, le réseau ressemble à quatre « zones de sécurité » distinctes avec les règles sur la NVA dirigeant principalement le trafic entre ces zones. Logiquement, cela ressemblerait à ce qui suit :

![Réseau logique du point de vue NVA][13]

#### Conclusion
L’ajout d’une connexion réseau hybride VPN de site à site à un réseau virtuel Azure peut étendre le réseau local dans Azure de manière sécurisée. À l’aide d’une connexion VPN, votre trafic est chiffré et acheminé via Internet. L’utilisation de la NVA, comme dans cet exemple, fournit un emplacement central pour appliquer et gérer la stratégie de sécurité. Plus d’informations sur cet exemple, notamment :

- Comment créer cet exemple DMZ avec des scripts PowerShell
- Comment créer cet exemple avec un modèle ARM
- Scénarios de flux de trafic détaillés, montrant comment le trafic circule dans cette conception

bientôt disponibles et liés à partir de cette page.

### Exemple 5 : Ajouter une connexion hybride avec un réseau VPN de passerelle Azure de site à site
[Revenir à Démarrage rapide](#fast-start) | Instructions de création détaillées bientôt disponibles

![Zone DMZ avec réseau hybride connecté à une passerelle][14]

#### Configuration de l’environnement
La mise en réseau hybride à l’aide d’une passerelle VPN Azure peut être ajoutée à l’un des types de zone DMZ décrits dans l’exemple 1 et 2.

Comme indiqué dans la figure ci-dessus, une connexion VPN sur Internet (de site à site) permet de connecter un réseau local à un réseau virtuel Azure via une passerelle VPN Azure.

>[AZURE.NOTE]Si vous utilisez ExpressRoute avec l’option d’homologation Azure public activée, un itinéraire statique devra être créé pour les routages vers l’adresse IP de passerelle VPN Azure en dehors du périmètre Internet de votre entreprise et non via le périmètre WAN ExpressRoute. Cela est dû à la traduction d’adresses réseau requise sur l’option d’homologation Azure public ExpressRoute qui arrêtera probablement la session VPN (généralement, le protocole IPSec n’aime pas la traduction d’adresses réseau).

Comme illustré ci-dessous, avec cette option, l’environnement dispose maintenant de deux périmètres réseau. Sur le premier périmètre, la NVA et les NSG contrôlent les flux de trafic pour les réseaux intra-Azure et entre Azure et Internet, tandis que le deuxième périmètre est la passerelle VPN Azure qui est un périmètre réseau totalement distinct et isolé entre le réseau local et Azure.

Les flux de trafic doivent être envisagés avec précaution car ils peuvent être optimisés ou détériorés par ce modèle de conception, selon le cas d’utilisation spécifique.

L’utilisation de l’environnement créé dans l’exemple 1, « Créer une zone démilitarisée (DMZ) pour protéger les applications avec des groupes de sécurité réseau (NSG) », et l’ajout d’une connexion réseau hybride VPN de site à site produirait la conception suivante :

![Zone DMZ avec passerelle connectée à l’aide d’une connexion ExpressRoute][15]

#### Conclusion
L’ajout d’une connexion réseau hybride VPN de site à site à un réseau virtuel Azure peut étendre le réseau local dans Azure de manière sécurisée. À l’aide de la passerelle VPN Azure native, votre trafic est chiffré IPSec et acheminé via Internet. En outre, la passerelle VPN Azure fournit une option à moindre coût (aucun coût de licence supplémentaire à l’instar des appliances virtuelles réseau tierces). Cette solution est plus économique dans l’exemple 1 où aucune appliance virtuelle réseau n’est utilisée. Plus d’informations sur cet exemple, notamment :

- Comment créer cet exemple DMZ avec des scripts PowerShell
- Comment créer cet exemple avec un modèle ARM
- Scénarios de flux de trafic détaillés, montrant comment le trafic circule dans cette conception

bientôt disponibles et liés à partir de cette page.

### Exemple 6 : Ajouter une connexion hybride avec ExpressRoute
[Revenir à Démarrage rapide](#fast-start) | Instructions de création détaillées bientôt disponibles

![Zone DMZ avec réseau hybride connecté à une passerelle][16]

#### Configuration de l’environnement
La mise en réseau hybride à l’aide d’une connexion d’homologation privée ExpressRoute peut être ajoutée à l’un des types de zone DMZ décrits dans l’exemple 1 ou 2.

Comme indiqué dans la figure ci-dessus, l’homologation privée ExpressRoute fournit une connexion directe entre votre réseau local et le réseau virtuel Azure. Le trafic passe uniquement dans le réseau du prestataire de services et le réseau Microsoft/Azure, sans jamais toucher Internet.

>[AZURE.NOTE]Il existe une limite à l’utilisation de l’itinéraire défini par utilisateur (UDR) et ExpressRoute, et ce, en raison de la complexité du routage dynamique utilisé sur la passerelle virtuelle Azure. L’UDR ne doit pas être appliqué aux sous-réseaux communiquant avec la passerelle Azure et fournissant la connexion ExpressRoute. La passerelle Azure ne doit pas être l’appareil de tronçon suivant des sous-réseaux UDR associés. La possibilité d’intégrer entièrement l’UDR et ExpressRoute sera activée dans une future version d’Azure.

</br>
>[AZURE.TIP]L’utilisation d’ExpressRoute tient le trafic du réseau d’entreprise éloigné d’Internet pour une meilleure sécurité, améliorant ainsi significativement les performances et autorisant les accords de niveau de service de votre fournisseur ExpressRoute. En ce qui concerne les performances ExpressRoute, la passerelle Azure peut transférer jusqu’à 2 Gbits/s avec ExpressRoute, tandis qu’avec les VPN de site à site, le débit maximal de la passerelle Azure est de 200 Mbits/s.

Comme on le voit dans le schéma ci-dessous, avec cette option, l’environnement dispose maintenant de deux périmètres réseau. La NVA et le NSG contrôlent les flux de trafic pour les réseaux intra-Azure et entre Azure et Internet, tandis que la passerelle est un périmètre réseau totalement distinct et isolé entre le réseau local et Azure.

Les flux de trafic doivent être envisagés avec précaution car ils peuvent être optimisés ou détériorés par ce modèle de conception, selon le cas d’utilisation spécifique.

L’utilisation de l’environnement créé dans l’exemple 1, « Créer une zone DMZ simple à l’aide de groupes de sécurité réseau (NSG) », et l’ajout d’une connexion réseau hybride ExpressRoute produirait la conception suivante :

![Zone DMZ avec passerelle connectée à l’aide d’une connexion ExpressRoute][17]

#### Conclusion
L’ajout d’une connexion réseau d’homologation privée ExpressRoute peut étendre le réseau local dans Azure d’une manière sécurisée, avec moins de latence et de meilleures performances. En outre, l’utilisation de la passerelle Azure native, comme dans cet exemple, fournit une option à moindre coût (aucune licence supplémentaire à l’instar des appliances virtuelles réseau tierces). Plus d’informations sur cet exemple, notamment :

- Comment créer cet exemple DMZ avec des scripts PowerShell
- Comment créer cet exemple avec un modèle ARM
- Scénarios de flux de trafic détaillés, montrant comment le trafic circule dans cette conception

bientôt disponibles et liés à partir de cette page.

## Références
### Sites web et documentation utiles
- Accès Azure avec ARM : 
- Accéder à Azure avec PowerShell : [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentation relative à la mise en réseau virtuelle : [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentation relative aux groupes de sécurité réseau : [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentation relative à l’itinéraire défini par l’utilisateur : [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Passerelles virtuelles Azure : [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- VPN de site à site : [https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/](./virtual-network/vpn-gateway-site-to-site-create.md)
- Documentation relative à ExpressRoute (veillez à consulter les sections « Mise en route » et « Procédure ») : [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Organigramme des options de sécurité"
[1]: ./media/best-practices-network-security/compliancebadges.png "Badges de conformité Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Fonctionnalités de sécurité Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Une zone DMZ dans un réseau d’entreprise"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architecture de sécurité Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Une zone DMZ dans un réseau virtuel Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Réseau hybride avec trois limites de sécurité"
[7]: ./media/best-practices-network-security/example1design.png "Zone DMZ entrante avec NSG"
[8]: ./media/best-practices-network-security/example2design.png "Zone DMZ entrante avec NVA et NSG"
[9]: ./media/best-practices-network-security/example3design.png "Zone DMZ bidirectionnelle avec NVA, NSG et UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Affichage logique des règles de pare-feu"
[11]: ./media/best-practices-network-security/example4designoptions.png "Zone DMZ avec réseau hybride connecté à une NVA"
[12]: ./media/best-practices-network-security/example4designs2s.png "Zone DMZ avec NVA connectée à l’aide d’un VPN de site à site"
[13]: ./media/best-practices-network-security/example4networklogical.png "Réseau logique du point de vue NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "Zone DMZ avec réseau hybride de site à site connecté à une passerelle Azure"
[15]: ./media/best-practices-network-security/example5designs2s.png "Zone DMZ avec passerelle Azure utilisant un VPN de site à site"
[16]: ./media/best-practices-network-security/example6designoptions.png "Zone DMZ avec réseau hybride ExpressRoute connecté à une passerelle Azure"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Zone DMZ avec passerelle Azure utilisant une connexion ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

<!---HONumber=Sept15_HO4-->