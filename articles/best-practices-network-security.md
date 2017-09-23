---
title: "Bonnes pratiques en matière de sécurité réseau | Microsoft Docs"
description: "Découvrez quelques-unes des fonctionnalités clés disponibles dans Azure pour créer des environnements réseau sécurisés"
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 243c1cd5ebf34f2d8a8fda234fa3875298390336
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017

---

# <a name="microsoft-cloud-services-and-network-security"></a>Services de cloud computing et sécurité réseau Microsoft
Les services cloud Microsoft offrent une grande évolutivité des services et de l’infrastructure, des capacités de niveau d'entreprise et de nombreuses options de connectivité hybride. Les clients peuvent choisir d’accéder à ces services avec Internet ou grâce à Azure ExpressRoute, qui fournit une connectivité réseau privée. La plateforme Microsoft Azure permet aux clients d’étendre leur infrastructure dans le cloud et de générer des architectures à plusieurs niveaux en toute transparence. Par ailleurs, des tiers peuvent activer des fonctionnalités améliorées en offrant des services de sécurité et des appliances virtuelles. Ce livre blanc fournit une vue d’ensemble des problèmes de sécurité et d’architecture dont les clients doivent tenir compte lorsqu’ils utilisent des services de cloud computing Microsoft auxquels ils accèdent avec ExpressRoute. Il décrit également la création de services plus sécurisés dans les réseaux virtuels Azure.

## <a name="fast-start"></a>Démarrage rapide
Le schéma logique suivant peut vous orienter vers un exemple spécifique des différentes techniques de sécurité disponibles avec la plateforme Azure. Pour une consultation rapide, recherchez l'exemple le plus adapté à votre cas. Pour des explications plus complètes, lisez entièrement le document.
[![0]][0]

[Exemple 1 : Créer un réseau de périmètre (également appelé DMZ, zone démilitarisée ou sous-réseau filtré) pour protéger les applications avec des groupes de sécurité réseau (NSG).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Exemple 2 : Créer un réseau de périmètre pour protéger les applications avec un pare-feu et des groupes de sécurité réseau.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Exemple 3 : Créer un réseau de périmètre pour protéger les réseaux avec un pare-feu, un routage défini par l’utilisateur (UDR) et un groupe de sécurité réseau.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Exemple 4 : Ajouter une connexion hybride avec un réseau privé virtuel (VPN) d’appliance virtuelle de site à site.](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Exemple 5 : Ajouter une connexion hybride avec une passerelle VPN Azure de site à site.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Exemple 6 : Ajouter une connexion hybride avec ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Des exemples d'ajout de connexions entre réseaux virtuels, de haute disponibilité et de chaînage de service seront ajoutés à ce document dans les prochains mois.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Conformité et protection des infrastructures Microsoft
Microsoft propose plus de 40 certifications et attestations pour aider les entreprises à répondre aux exigences nationales, régionales et sectorielles régissant la collecte et l’utilisation des données des individus. Cette offre est la plus complète parmi tous les fournisseurs de services cloud.

Pour plus d'informations, consultez les informations de conformité du [Centre de gestion de la confidentialité Microsoft][TrustCenter].

Microsoft a adopté une approche complète pour protéger l’infrastructure cloud nécessaire à l’exécution de services mondiaux très évolutifs. L’infrastructure cloud de Microsoft inclut du matériel, des logiciels, des réseaux et du personnel d’administration et d’exploitation, en complément des centres de données physiques.

![2]

Cette approche fournit une base plus solide permettant aux clients de déployer leurs services dans le cloud de Microsoft. L’étape suivante explique aux clients comment concevoir et créer une architecture de sécurité afin de protéger ces services.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Architectures de sécurité traditionnelles et réseaux de périmètre
Bien que Microsoft investisse fortement dans la protection de l’infrastructure cloud, les clients doivent également protéger leurs services cloud et leurs groupes de ressources. Une approche multicouche de la sécurité constitue la meilleure défense. Une zone de sécurité de type réseau de périmètre protège les ressources réseau internes d’un réseau non approuvé. Un réseau de périmètre concerne la périphérie ou les parties du réseau qui se trouvent entre Internet et l'infrastructure informatique protégée de l’entreprise.

Dans les réseaux d’entreprise classiques, l’infrastructure centrale est puissamment fortifiée sur les périmètres, avec plusieurs couches d’appareils de sécurité. La limite de chaque couche se compose d’appareils et de points d’application de stratégies. Chaque couche peut comprendre une combinaison des appareils de sécurité réseau suivants : des pare-feu, la prévention DoS (Denial of Service), des systèmes IDS (détection d’intrusion) / IPS (protection contre les intrusions) et des appareils VPN. L’application de stratégies peut prendre la forme de stratégies de pare-feu, de listes de contrôle d’accès (ACL) ou de routage spécifique. La première ligne de défense du réseau, acceptant directement le trafic entrant à partir d’Internet, est une combinaison de ces mécanismes visant à bloquer les attaques et le trafic dangereux, tout en autorisant la transmission des demandes légitimes plus loin dans le réseau. Ce trafic conduit directement aux ressources dans le réseau de périmètre. Cette ressource peut ensuite « communiquer » avec les ressources plus loin dans le réseau, en transitant par la limite suivante en vue d’une validation préalable. La couche la plus à l’extérieur est appelée réseau de périmètre, car cette partie du réseau est exposée à Internet, généralement avec une certaine forme de protection des deux côtés. La figure suivante montre un exemple de réseau de périmètre à sous-réseau unique dans un réseau d’entreprise, avec deux limites de sécurité.

![3]

Plusieurs architectures sont utilisées pour implémenter un réseau de périmètre. Ces architectures peuvent aller d’un simple équilibreur de charge à un réseau de périmètre à sous-réseaux multiples, avec divers mécanismes à chaque limite visant à bloquer le trafic et à protéger les couches les plus profondes du réseau d’entreprise. La conception du réseau de périmètre dépend des besoins spécifiques de l’organisation et de sa tolérance au risque globale.

Tandis que les clients déplacent leurs charges de travail vers des clouds publics, il est essentiel de prendre en charge des fonctionnalités similaires pour l’architecture de réseau de périmètre dans Azure pour répondre aux exigences de conformité et de sécurité. Ce document fournit aux clients des instructions pour créer un environnement réseau sécurisé dans Azure. Il se concentre sur le réseau de périmètre, mais inclut également une discussion complète sur de nombreux aspects de la sécurité réseau. Cette discussion s’articule autour des questions suivantes :

* Comment créer un réseau de périmètre dans Azure ?
* Quelles sont les fonctionnalités Azure disponibles pour créer le réseau de périmètre ?
* Comment les charges de travail principales peuvent-elles être protégées ?
* Comment les communications Internet sont-elles contrôlées pour les charges de travail dans Azure ?
* Comment les réseaux locaux peuvent-ils être protégés des déploiements dans Azure ?
* Dans quels cas les fonctionnalités de sécurité Azure natives doivent-elles être utilisées par rapport aux appliances ou services tiers ?

Le diagramme suivant montre les différentes couches de sécurité fournies aux clients par Azure. Ces couches sont natives à la fois sur la plateforme Azure elle-même et dans les fonctionnalités définies par le client :

![4]

Issue d’Internet, la protection DDoS Azure protège contre les attaques à grande échelle menées contre Azure. Les adresses IP publiques (points de terminaison) définies par les clients constituent la couche suivante servent à déterminer quel trafic peut traverser le service cloud pour atteindre le réseau virtuel. L’isolement du réseau virtuel Azure natif garantit l’isolement complet de tous les autres réseaux et la circulation du trafic uniquement au moyen des méthodes et des chemins d’accès configurés par l’utilisateur. Ces chemins d’accès et méthodes constituent la couche suivante, où les groupes de sécurité réseau, le routage défini par l’utilisateur et les appliances virtuelles réseau peuvent servir à créer des limites de sécurité afin de protéger les déploiements d’applications dans le réseau protégé.

La section suivante fournit une vue d’ensemble des réseaux virtuels Azure. Ces réseaux virtuels sont créés par les clients et leurs charges de travail déployées y sont connectées. Les réseaux virtuels constituent la base de toutes les fonctionnalités de sécurité réseau requises pour établir un réseau de périmètre afin de protéger les déploiements des clients dans Azure.

## <a name="overview-of-azure-virtual-networks"></a>Vue d’ensemble des réseaux virtuels Azure
Avant que le trafic Internet puisse atteindre les réseaux virtuels Azure, il existe deux couches de sécurité inhérentes à la plateforme Azure :

1.    **Protection DDoS**: la protection DDoS est une couche du réseau physique Azure qui protège la plateforme Azure elle-même contre les attaques Internet à grande échelle. Ces attaques utilisent plusieurs nœuds « robot » pour tenter de surcharger un service Internet. Azure comprend un maillage de protection DDoS robuste sur toutes les connexions entrantes, sortantes et inter-régions Azure. Cette couche de protection DDoS ne comporte aucun attribut configurable par l’utilisateur et n’est pas accessible au client. La couche de protection DDoS protège Azure en tant que plateforme contre les attaques à grande échelle, et surveille également le trafic sortant et inter-régions Azure. Le client peut configurer des couches de résilience supplémentaires à l’aide d'appliances virtuelles réseau pour une protection contre une attaque à plus petite échelle qui ne viole pas la protection au niveau de la plateforme. Un exemple de DDoS en action : si une adresse IP internet a subi une attaque DDoS à grande échelle, Azure détecte les sources des attaques et nettoie le trafic malveillant avant qu’il n'atteigne sa destination prévue. Dans la plupart des cas, le point de terminaison attaqué n’est pas affecté par l’attaque. Dans de rares cas où un point de terminaison est affecté, aucun trafic n’est affecté vers d’autres points de terminaison, seul le point de terminaison attaqué. Les autres clients et services ne sont donc pas impactés par cette attaque. Il est très important de noter qu'Azure DDoS recherche uniquement les attaques à grande échelle. Il est possible que votre service spécifique puisse être submergé avant que les seuils de niveau de protection de plateforme soient dépassés. Par exemple, un site web sur un serveur A0 IIS unique pourrait être mis en mode hors connexion par une attaque DDoS avant que la protection contre les DDoS au niveau de la plateforme Azure ne l'identifie comme une menace.

2.  **Adresses IP publiques** : les adresse IP publique (activées par le biais de points de terminaison de service, d'adresses IP publiques, d'Application Gateway et d’autres fonctionnalités Azure qui présentent une adresse IP publique vers internet acheminée vers votre ressource) permettent à des services cloud ou à des groupes de ressources d'exposer des adresses IP Internet publiques et des ports. Le point de terminaison utilise la traduction d'adresses réseau (NAT) pour acheminer le trafic vers l'adresse et le port internes sur le réseau virtuel Azure. Il s’agit du principal chemin d’accès pour que le trafic externe passe dans le réseau virtuel. Les adresses IP publiques sont configurables de manière à déterminer quel trafic est transféré et comment et où il est traduit sur le réseau virtuel.

Une fois que le trafic a atteint le réseau virtuel, de nombreuses fonctionnalités entrent en jeu. Les réseaux virtuels Azure constituent la base à laquelle les clients joignent leurs charges de travail et à laquelle s’applique la sécurité de base au niveau du réseau. Il s’agit d’un réseau privé (une superposition du réseau virtuel) dans Azure pour les clients dotés des caractéristiques et fonctionnalités suivantes :

* **Isolement de trafic**: un réseau virtuel est une limite d’isolement du trafic sur la plateforme Azure. Les machines virtuelles dans un réseau virtuel ne peuvent pas communiquer directement avec les machines virtuelles dans un autre réseau virtuel, même si les deux réseaux virtuels sont créés par le même client. Cet isolement est une propriété critique qui garantit que les machines virtuelles et les communications du client restent privées dans un réseau virtuel.

>[!NOTE]
>L'isolement du trafic se rapporte uniquement au trafic *entrant* dans le réseau virtuel. Par défaut, le trafic sortant du réseau virtuel vers Internet est autorisé, mais peut être évité si les groupes de sécurité réseau l'exigent.
>
>

* **Topologie multiniveau** : les réseaux virtuels permettent aux clients de définir une topologie multiniveau en allouant des sous-réseaux et en spécifiant des espaces d’adressage distincts pour différents éléments ou « niveaux » de leurs charges de travail. Ces regroupements et topologies logiques permettent aux clients de définir différentes stratégies d’accès selon les types de charges de travail et également de contrôler les flux de trafic entre les niveaux.
* **Connexions entre différents locaux**: les clients peuvent établir des connexions entre locaux entre un réseau virtuel et plusieurs sites locaux ou d’autres réseaux virtuels dans Azure. Pour créer une connexion, les clients peuvent utiliser l’homologation de réseau virtuel, des passerelles VPN Azure, des appliances virtuelles de réseau tiers ou ExpressRoute. Azure prend en charge les VPN de site à site (S2S) à l’aide des protocoles IPsec/IKE standards et de la connectivité privée ExpressRoute.
* **Groupe de sécurité réseau** : permet aux clients de créer des règles (listes de contrôle d’accès) avec le niveau de granularité souhaité : interfaces réseau, machines virtuelles individuelles ou sous-réseaux virtuels. Les clients peuvent contrôler l’accès en autorisant ou en refusant la communication entre les charges de travail au sein d’un réseau virtuel, à partir des systèmes sur les réseaux du client au moyen des connexions entre différents locaux ou de la communication Internet directe.
* **Routage défini par l’utilisateur** et **transfert IP** : permettent de définir les chemins d’accès de communication entre les différents niveaux d’un réseau virtuel. Les clients peuvent déployer un pare-feu, les services IDS/IPS et d’autres appliances virtuelles et acheminer le trafic réseau à travers ces appliances de sécurité pour l’application de stratégies de limites de sécurité, l’audit et l’inspection.
* **Appliances virtuelles réseau** dans Azure Marketplace : les appliances de sécurité telles que les pare-feu, les équilibreurs de charge, les systèmes de détection/prévention des intrusions sont disponibles dans Azure Marketplace et la galerie d’images de machines virtuelles. Pour finaliser un environnement réseau sécurisé multiniveau, les clients peuvent déployer ces appliances dans leurs réseaux virtuels et, en particulier, sur leurs limites de sécurité (notamment les sous-réseaux du réseau de périmètre).

Avec ces fonctionnalités et capacités, le diagramme suivant est un exemple de conception d’architecture de réseau de périmètre dans Azure :

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Caractéristiques et conditions requises d’un réseau de périmètre
Le réseau de périmètre est la partie frontale du réseau, qui crée une interface directe avec les communications depuis Internet. Les paquets entrants doivent traverser les appliances de sécurité, notamment le pare-feu et les systèmes de détection et de prévention d’intrusion, avant d’atteindre les serveurs principaux. Les paquets Internet des charges de travail peuvent également traverser les appliances de sécurité dans le réseau de périmètre pour l’application des stratégies, l’inspection et les fins d’audit, avant de quitter le réseau. En outre, le réseau de périmètre peut héberger les passerelles VPN entre différents locaux entre les réseaux virtuels client et les réseaux locaux.

### <a name="perimeter-network-characteristics"></a>Caractéristiques d’un réseau de périmètre
En référence à la figure précédente, voici certaines caractéristiques d'un bon réseau de périmètre :

* Accès sur Internet :
  * Le sous-réseau du réseau de périmètre lui-même est accessible sur Internet et communique directement avec Internet.
  * Les adresses IP publiques, les adresses IP virtuelles et/ou points de terminaison de service transfèrent le trafic Internet vers le réseau frontal et les appareils.
  * Le trafic entrant provenant d’Internet passe par les appareils de sécurité avant d’autres ressources sur le réseau frontal.
  * Si la sécurité sortante est activée, le trafic passe par les appareils de sécurité, dernière étape avant d’être transféré vers Internet.
* Réseau protégé :
  * Il n’y a aucun chemin d’accès direct à partir d’Internet vers l’infrastructure centrale.
  * Les canaux vers l’infrastructure centrale doivent traverser les appareils de sécurité comme les NSG, pare-feu ou périphériques VPN.
  * Les autres appareils ne doivent pas créer de pont avec Internet et l’infrastructure centrale.
  * Les appareils de sécurité sur les limites du réseau de périmètre accessibles sur Internet ou avec le réseau protégé (par exemple, les deux icônes pare-feu de l'illustration précédente) peuvent correspondre en réalité à une seule appliance virtuelle avec des règles ou des interfaces différenciées pour chaque limite. Par exemple, un seul appareil, avec séparation logique, pour gérer la charge des deux limites du réseau de périmètre.
* Autres pratiques courantes et contraintes :
  * Les charges de travail ne doivent pas stocker d’informations professionnelles stratégiques.
  * L’accès et les mises à jour des configurations et déploiements de réseau de périmètre sont limités aux seuls administrateurs autorisés.

### <a name="perimeter-network-requirements"></a>Conditions requises pour le réseau de périmètre
Afin d’activer ces caractéristiques, suivez ces instructions sur les conditions requises du réseau virtuel pour implémenter un réseau de périmètre réussi :

* **Architecture de sous-réseau :** spécifiez le réseau virtuel, tel qu’un sous-réseau entier dédié en tant que réseau de périmètre, séparé par d’autres sous-réseaux dans le même réseau virtuel. Cette séparation garantit que le trafic entre le réseau de périmètre et d’autres niveaux de sous-réseaux internes ou privés circule à travers un pare-feu ou une appliance virtuelle IDS/IPS.  Les itinéraires définis par l’utilisateur sur les sous-réseaux de limite sont tenus d'acheminer ce trafic vers l’appliance virtuelle.
* **Groupe de sécurité réseau :** le sous-réseau du réseau de périmètre lui-même doit être ouvert pour permettre la communication avec Internet, mais cela ne signifie pas que les clients doivent contourner les NSG. Suivez les pratiques de sécurité courantes afin de réduire les surfaces du réseau exposées à Internet. Verrouillez les plages d'adresses à distance autorisées à accéder aux déploiements ou aux ports et aux protocoles d'application spécifiques ouverts. Il peut arriver, cependant, qu'un verrouillage complet ne soit pas possible. Par exemple, si des clients ont un site web externe dans Azure, le réseau de périmètre doit autoriser les demandes web entrantes à partir de toutes les adresses IP publiques, mais doit uniquement ouvrir les ports d’application web : TCP sur le port 80 et/ou TCP sur le port 443.
* **Table de routage :** le sous-réseau du réseau de périmètre lui-même doit être en mesure de communiquer directement avec Internet, mais ne doit pas permettre une communication directe vers et à partir des réseaux principaux ou locaux sans passer par un pare-feu ou une appliance de sécurité.
* **Configuration des appliances de sécurité :** pour acheminer et inspecter les paquets entre le réseau de périmètre et le reste des réseaux protégés, les appliances de sécurité comme le pare-feu et les appareils de détection et de prévention d’intrusion peuvent être multirésidents. Ils peuvent avoir des cartes réseau distinctes pour le réseau de périmètre et les sous-réseaux principaux. Les cartes réseau du réseau de périmètre communiquent directement vers et à partir d’Internet, avec les NSG correspondants et la table de routage du réseau de périmètre. Les cartes réseau se connectant aux sous-réseaux principaux disposent de NSG et de tables de routage plus restreints des sous-réseaux principaux correspondants.
* **Fonctionnalités des appliances de sécurité :** les appliances de sécurité déployées du réseau de périmètre exécutent généralement les fonctionnalités suivantes :
  * Pare-feu : application de règles de pare-feu ou de stratégies de contrôle d’accès pour les demandes entrantes.
  * Détection et prévention des menaces : détection et atténuation des attaques malveillantes d’Internet.
  * Audit et journalisation : gestion de journaux d’audit et d’analyse détaillés.
  * Proxy inversé : redirection des demandes entrantes vers les serveurs principaux correspondants. La redirection implique le mappage et la traduction des adresses de destination sur les appareils frontaux, généralement des pare-feu, vers les adresses des serveurs principaux.
  * Proxy de transfert : indication de NAT et exécution d’audits pour la communication initiée à partir du réseau virtuel vers Internet.
  * Routeur : transfert du trafic entrant et entre sous-réseaux au sein du réseau virtuel.
  * Périphérique VPN : action en tant que passerelles VPN entre différents locaux, pour la connectivité VPN entre différents locaux, entre les réseaux locaux client et les réseaux virtuels Azure.
  * Serveur VPN : acceptation des clients VPN se connectant aux réseaux virtuels Azure.

> [!TIP]
> Maintenez les deux groupes suivants séparés : les personnes autorisées à accéder au matériel de sécurité du réseau de périmètre et les personnes autorisées en tant qu’administrateurs de développement d’applications, de déploiement ou d’opérations. Conserver une distinction entre ces groupes permet une répartition des tâches et empêche qu’une seule personne contourne les contrôles de sécurité des applications et de sécurité réseau.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Questions à envisager lors de la création des limites du réseau
Dans cette section, sauf mention contraire, le terme « réseaux » fait référence à des réseaux virtuels privés Azure créés par un administrateur d’abonnement. Le terme ne fait pas référence aux réseaux physiques sous-jacents dans Azure.

En outre, les réseaux virtuels Azure sont souvent utilisés pour étendre les réseaux locaux traditionnels. Il est possible d’incorporer des solutions de mise en réseau hybrides de site à site ou ExpressRoute avec les architectures de réseau de périmètre. Ce lien hybride est un aspect important de la création de limites de sécurité réseau.

Les trois questions suivantes sont essentielles lorsque vous créez un réseau avec un réseau de périmètre et plusieurs limites de sécurité.

#### <a name="1-how-many-boundaries-are-needed"></a>1) Combien de limites sont nécessaires ?
En premier lieu, il faut décider le nombre de limites de sécurité nécessaires dans un scénario donné :

* Une seule limite : une sur le réseau de périmètre frontal entre le réseau virtuel et Internet.
* Deux limites : une du côté Internet du réseau de périmètre, l’autre entre le sous-réseau du réseau de périmètre et les sous-réseaux principaux dans les réseaux virtuels Azure.
* Trois limites : une du côté Internet du réseau de périmètre, une autre entre le réseau de périmètre et les sous-réseaux principaux et la dernière entre les sous-réseaux principaux et le réseau local.
* N limites : nombre variable. Selon les besoins de sécurité, le nombre de limites de sécurité pouvant être appliquées dans un réseau donné n'a pas de limite.

Le nombre et le type des limites nécessaires varient en fonction de la tolérance au risque d’une société et du scénario spécifique implémenté. Cette décision est souvent prise conjointement par plusieurs groupes au sein d’une entreprise, parmi lesquels figurent souvent une équipe spécialiste des risques et de la conformité, une équipe chargée du réseau et de la plateforme et une équipe de développement des applications. Les personnes ayant des connaissances en matière de sécurité, des données impliquées et des technologies utilisées doivent avoir leur mot à dire dans cette décision, afin de garantir une position de sécurité appropriée pour chaque implémentation.

> [!TIP]
> Utilisez le plus petit nombre de limites afin de répondre aux exigences de sécurité pour une situation donnée. Plus le nombre de limites est élevé, plus les opérations et le dépannage peuvent être complexes, de même que les frais généraux impliqués dans la gestion de plusieurs stratégies de limites au fil du temps. Toutefois, des limites insuffisantes augmentent les risques. Il est essentiel de trouver le juste équilibre.
>
>

![6]

La figure précédente montre une vue d'ensemble d'un réseau à trois limites de sécurité. Ces limites se trouvent entre le réseau de périmètre et Internet, entre les sous-réseaux privés Azure frontaux et principaux et entre le sous-réseau Azure principal et le réseau d'entreprise local.

#### <a name="2-where-are-the-boundaries-located"></a>2) Où se trouvent les limites ?
Une fois que le nombre de limites est décidé, la question de l’emplacement de leur implémentation constitue le point de décision suivant. Il existe généralement trois possibilités :

* À l'aide d'un service Internet intermédiaire (par exemple, un pare-feu d'applications web cloud, non abordé dans ce document)
* À l'aide des fonctionnalités natives et/ou des appliances de réseau virtuel dans Azure
* À l'aide d’appareils physiques sur le réseau local

Sur les réseaux exclusivement Azure, les options sont les fonctionnalités Azure natives (par exemple, les équilibreurs de charge Azure) ou les appliances virtuelles réseau à partir d’un riche écosystème de partenaires Azure (par exemple, les pare-feu Check Point).

Si une limite est nécessaire entre Azure et un réseau local, les appareils de sécurité peuvent résider sur n’importe quel côté de la connexion (ou sur les deux côtés). Par conséquent une décision doit être prise concernant l’emplacement du matériel de sécurité.

Dans la figure précédente, les limites Internet-réseau de périmètre et frontal-principal sont entièrement contenues dans Azure et doivent être des fonctionnalités Azure natives ou des appliances virtuelles réseau. Les appareils de sécurité sur la limite entre Azure (sous-réseau principal) et le réseau d’entreprise peuvent être du côté Azure ou du côté local ; une combinaison d’appareils des deux côtés est également possible. Ces options présentent des avantages et des inconvénients importants qui doivent être sérieusement envisagés.

Par exemple, l’utilisation d’un matériel de sécurité physique existant sur le réseau local présente l'avantage de ne pas nécessiter de nouveau matériel. Une reconfiguration suffit. L’inconvénient, cependant, est que tout le trafic doit revenir d’Azure vers le réseau local pour être vu par le matériel de sécurité. Ainsi, le trafic Azure à Azure pourrait subir une latence importante et affecter les performances des applications et l’expérience utilisateur s’il a été forcé vers le réseau local pour l’application de stratégies de sécurité.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) Comment les limites sont-elles implémentées ?
Chaque limite de sécurité aura probablement des exigences de fonctionnalités différentes (par exemple, des règles IDS et de pare-feu du côté Internet du réseau de périmètre, mais des listes de contrôle d’accès uniquement entre le réseau de périmètre et le sous-réseau principal). Le choix de l'appareil (ou du nombre d'appareils) à utiliser dépend du scénario et des exigences de sécurité. Dans la section suivante, les exemples 1, 2 et 3 abordent certaines des options qui peuvent être utilisées. L’examen des fonctionnalités réseau natives Azure et des appareils disponibles dans Azure à partir de l’écosystème de partenaires montre les nombreuses options disponibles pour résoudre la quasi-totalité des scénarios.

Autre point de décision d’implémentation important : comment connecter le réseau local avec Azure ? Faut-il utiliser la passerelle virtuelle Azure ou une appliance virtuelle réseau ? Ces options sont évoquées plus en détail dans la section suivante (exemples 4, 5 et 6).

En outre, le trafic entre les réseaux virtuels dans Azure peut être nécessaire. Ces scénarios seront ajoutés à une date ultérieure.

Une fois que vous avez répondu aux questions précédentes, la section [Démarrage rapide](#fast-start) peut faciliter l’identification des exemples les plus appropriés pour un scénario donné.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Exemples : Création de limites de sécurité avec les réseaux virtuels Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Exemple 1 : Créer un réseau de périmètre pour protéger les applications avec des groupes de sécurité réseau
[Revenir à Démarrage rapide](#fast-start) | [Instructions de génération détaillées pour cet exemple][Example1]

[![7]][7]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les ressources suivantes :

- un seul groupe de ressources.
- un réseau virtuel avec deux sous-réseaux « FrontEnd » et « BackEnd »,
- un groupe de sécurité réseau est appliqué aux deux sous-réseaux,
- un serveur Windows Server représentant un serveur web d’application (« IIS01 »),
- deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »),
- Un serveur Windows Server qui représente un serveur DNS (« DNS01 »)
- Une adresse IP publique associée au serveur d’applications web

Pour accéder aux scripts et à un modèle Azure Resource Manager, consultez les [instructions de génération détaillées][Example1].

#### <a name="nsg-description"></a>Description du groupe de sécurité réseau
Dans cet exemple, un groupe NSG est créé, puis chargé avec six règles.

> [!TIP]
> En règle générale, vous devez d’abord créer les règles d’« autorisation » spécifiques, suivies des règles de « refus » plus générales. La priorité donnée indique quelles sont les règles évaluées en premier. Une fois qu’il a été déterminé que le trafic répond à une règle spécifique, aucune autre règle n’est évaluée. Les règles du groupe de sécurité réseau peuvent s’appliquer dans le sens entrant ou sortant (du point de vue du sous-réseau).
>
>

Les règles qui suivent sont générées de façon déclarative pour le trafic entrant :

1. Le trafic DNS interne (port 53) est autorisé.
2. Le trafic RDP (port 3389) à partir d’Internet vers n’importe quelle machine virtuelle est autorisé.
3. Le trafic HTTP (port 80) à partir d’Internet vers le serveur web (IIS01) est autorisé.
4. Tout trafic (tous les ports) IIS01 vers AppVM1 est autorisé.
5. Tout trafic (tous les ports) en provenance d’Internet vers l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6. Tout trafic (tous les ports) en provenance du sous-réseau frontal vers le sous-réseau principal est refusé.

Avec ces règles associées à chaque sous-réseau, si une requête HTTP est entrante à partir d'Internet vers le serveur web, les règles 3 (autoriser) et 5 (refuser) s'appliquent. Mais, étant donné que la règle 3 a une priorité plus élevée, elle est la seule à s’appliquer et la règle 5 n’entre pas en jeu. La requête HTTP est donc autorisée à accéder au serveur web. Si le même trafic tentait d’atteindre le serveur DNS01, la règle 5 (refus) serait la première à s’appliquer et le trafic ne serait pas autorisé à accéder au serveur. La règle 6 (refuser) bloque la communication du sous-réseau frontal vers le sous-réseau principal (à l'exception du trafic autorisé dans les règles 1 et 4). Cet ensemble de règles protège le réseau principal au cas où un attaquant compromettrait l'application web sur le serveur frontal. L’attaquant aurait un accès limité au réseau principal « protégé » (uniquement aux ressources exposées sur le serveur AppVM01).

Il existe une règle par défaut qui autorise le trafic sortant vers Internet. Pour cet exemple, nous allons autoriser le trafic sortant sans modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, le routage défini par l’utilisateur est requis (voir l’exemple 3).

#### <a name="conclusion"></a>Conclusion
Cet exemple est un moyen relativement simple et direct d’isoler le sous-réseau du serveur principal du trafic entrant. Pour plus d’informations, consultez les [instructions de génération détaillées][Example1]. Vous trouverez les instructions suivantes :

* Comment créer ce réseau de périmètre avec des scripts PowerShell classiques.
* Comment créer ce réseau de périmètre avec un modèle Azure Resource Manager.
* Des descriptions détaillées de chaque commande NSG.
* Des scénarios de flux de trafic détaillés, montrant comment le trafic est autorisé ou refusé dans chaque couche.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Exemple 2 : Créer un réseau de périmètre pour protéger les applications avec un pare-feu et des groupes de sécurité réseau
[Revenir à Démarrage rapide](#fast-start) | [Instructions de génération détaillées pour cet exemple][Example2]

[![8]][8]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les ressources suivantes :

* un seul groupe de ressources.
* un réseau virtuel avec deux sous-réseaux « FrontEnd » et « BackEnd »,
* un groupe de sécurité réseau est appliqué aux deux sous-réseaux,
* une appliance virtuelle réseau, dans ce cas un pare-feu, connectée au sous-réseau principal
* un serveur Windows Server représentant un serveur web d’application (« IIS01 »),
* deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »),
* Un serveur Windows Server qui représente un serveur DNS (« DNS01 »),

Pour accéder aux scripts et à un modèle Azure Resource Manager, consultez les [instructions de génération détaillées][Example2].

#### <a name="nsg-description"></a>Description du groupe de sécurité réseau
Dans cet exemple, un groupe NSG est créé, puis chargé avec six règles.

> [!TIP]
> En règle générale, vous devez d’abord créer les règles d’« autorisation » spécifiques, suivies des règles de « refus » plus générales. La priorité donnée indique quelles sont les règles évaluées en premier. Une fois qu’il a été déterminé que le trafic répond à une règle spécifique, aucune autre règle n’est évaluée. Les règles du groupe de sécurité réseau peuvent s’appliquer dans le sens entrant ou sortant (du point de vue du sous-réseau).
>
>

Les règles qui suivent sont générées de façon déclarative pour le trafic entrant :

1. Le trafic DNS interne (port 53) est autorisé.
2. Le trafic RDP (port 3389) à partir d’Internet vers n’importe quelle machine virtuelle est autorisé.
3. Tout trafic Internet (tous les ports) vers l’appliance virtuelle réseau (pare-feu) est autorisé.
4. Tout trafic (tous les ports) IIS01 vers AppVM1 est autorisé.
5. Tout trafic (tous les ports) en provenance d’Internet vers l’ensemble du réseau virtuel (les deux sous-réseaux) est refusé.
6. Tout trafic (tous les ports) en provenance du sous-réseau frontal vers le sous-réseau principal est refusé.

Avec ces règles associées à chaque sous-réseau, si une requête HTTP est entrante à partir d'Internet vers le pare-feu, les règles 3 (autoriser) et 5 (refuser) s'appliquent. Mais, étant donné que la règle 3 a une priorité plus élevée, elle est la seule à s’appliquer et la règle 5 n’entre pas en jeu. La requête HTTP est donc autorisée à accéder au pare-feu. Si le même trafic tentait d’atteindre le serveur IIS01, même s’il se trouvait sur le sous-réseau frontal, la règle 5 (refus) s’appliquerait et le trafic ne serait pas autorisé à franchir le serveur. La règle 6 (refuser) bloque la communication du sous-réseau frontal vers le sous-réseau principal (à l'exception du trafic autorisé dans les règles 1 et 4). Cet ensemble de règles protège le réseau principal au cas où un attaquant compromettrait l'application web sur le serveur frontal. L’attaquant aurait un accès limité au réseau principal « protégé » (uniquement aux ressources exposées sur le serveur AppVM01).

Il existe une règle par défaut qui autorise le trafic sortant vers Internet. Pour cet exemple, nous allons autoriser le trafic sortant sans modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux sens, le routage défini par l’utilisateur est requis (voir l’exemple 3).

#### <a name="firewall-rule-description"></a>Description de la règle de pare-feu
Sur le pare-feu, vous devez créer des règles de transfert. Étant donné que cet exemple achemine uniquement le trafic Internet entrant vers le pare-feu, puis vers le serveur web, seule une règle de traduction d’adresses réseau (NAT) de transfert est requise.

La règle de transfert accepte n'importe quelle adresse source entrante qui atteint le pare-feu, en essayant d'atteindre HTTP (port 80 ou 443 pour HTTPS). Elle est envoyée hors de l'interface locale du pare-feu et redirigée vers le serveur web avec l'adresse IP 10.0.1.5.

#### <a name="conclusion"></a>Conclusion
Cet exemple est un moyen relativement simple de protéger votre application avec un pare-feu et d’isoler le sous-réseau principal du trafic entrant. Pour plus d’informations, consultez les [instructions de génération détaillées][Example2]. Vous trouverez les instructions suivantes :

* Comment créer ce réseau de périmètre avec des scripts PowerShell classiques.
* Comment créer cet exemple avec un modèle Azure Resource Manager.
* Descriptions détaillées de chaque commande NSG et règle de pare-feu.
* Des scénarios de flux de trafic détaillés, montrant comment le trafic est autorisé ou refusé dans chaque couche.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Exemple 3 : Créer un réseau de périmètre pour protéger les réseaux avec un pare-feu, un routage défini par l’utilisateur et un groupe de sécurité réseau
[Revenir à Démarrage rapide](#fast-start) | [Instructions de génération détaillées pour cet exemple][Example3]

[![9]][9]

#### <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il existe un abonnement qui contient les ressources suivantes :

* un seul groupe de ressources.
* Un réseau virtuel avec trois sous-réseaux : « SecNet », « FrontEnd » et « BackEnd »
* Une appliance virtuelle du réseau, dans ce cas un pare-feu, connecté au sous-réseau SecNet
* un serveur Windows Server représentant un serveur web d’application (« IIS01 »),
* deux serveurs Windows Server qui représentent les serveurs principaux d’applications (« AppVM01 », « AppVM02 »),
* Un serveur Windows Server qui représente un serveur DNS (« DNS01 »),

Pour accéder aux scripts et à un modèle Azure Resource Manager, consultez les [instructions de génération détaillées][Example3].

#### <a name="udr-description"></a>Description du routage défini par l’utilisateur
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

Le VNETLocal est toujours un ou plusieurs préfixes d’adresse définis constituant le réseau virtuel de ce réseau spécifique (autrement dit, il change de réseau virtuel en réseau virtuel, en fonction de la définition spécifique de chaque réseau virtuel). Les itinéraires système restants sont statiques et, par défaut, ils prennent la valeur indiquée dans le tableau.

Dans cet exemple, deux tables de routage sont créées, une pour chacun des sous-réseaux principal et frontal. Chaque table est chargée d’itinéraires statiques appropriés au sous-réseau donné. Dans cet exemple, chaque table possède trois itinéraires qui dirigent tout le trafic (0.0.0.0/0) à travers le pare-feu (tronçon suivant = adresse IP de l’appliance virtuelle) :

1. Trafic du sous-réseau local sans tronçon suivant défini pour autoriser le trafic du sous-réseau local à contourner le pare-feu.
2. Le trafic réseau virtuel avec un tronçon suivant défini en tant que pare-feu. Ce tronçon suivant remplace la règle par défaut qui autorise un acheminement direct du trafic de réseau virtuel.
3. Ensemble du trafic restant (0/0) avec un tronçon suivant défini comme pare-feu.

> [!TIP]
> L’absence d’entrée de sous-réseau local dans l’itinéraire défini par l’utilisateur interrompt les communications du sous-réseau local.
>
> * Dans notre exemple, il est essentiel que 10.0.1.0/24 pointe vers le VNETLocal ! Sinon, un paquet quittant le serveur Web (10.0.1.4) destiné à un autre serveur local (par exemple) 10.0.1.25 échouera, car il sera envoyé à la NVA. La NVA l'enverra alors au sous-réseau, et le sous-réseau le renverra au NVA, créant ainsi une boucle infinie.
> * Les probabilités de boucle de routage sont généralement plus élevées sur des appliances avec plusieurs cartes d'interface réseau connectées à différents sous-réseaux, ce qui est souvent le cas des appliances locales traditionnelles.
>
>

Une fois que les tables de routage sont créées, elles doivent être liées à leurs sous-réseaux. La table de routage du sous-réseau frontal, une fois créée et liée au sous-réseau, doit ressembler à ce qui suit :

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> UDR peut désormais être appliqué à ce sous-réseau de passerelle sur lequel le circuit ExpressRoute est connecté.
>
> Des exemples d'activation de votre réseau de périmètre avec ExpressRoute ou la mise en réseau de site à site sont présentés dans les exemples 3 et 4.
>
>

#### <a name="ip-forwarding-description"></a>Description du transfert IP
Le transfert IP est une fonctionnalité associée au routage défini par l’utilisateur. Le transfert IP un paramètre d’appliance virtuelle qui permet de recevoir du trafic pas spécialement adressé à l’appliance, puis de transférer ce trafic vers sa destination finale.

Par exemple, si AppVM01 fait une demande au serveur DNS01, l’UDR achemine ce trafic vers le pare-feu. Lorsque le transfert IP est activé, le trafic de la destination de DNS01 (10.0.2.4) est accepté par l’appliance (10.0.0.4), puis transféré vers sa destination finale (10.0.2.4). Si le transfert IP n’est pas activé sur le pare-feu, le trafic ne sera pas accepté par l’équipement, même si le tronçon suivant de la table d’itinéraires est le pare-feu. Pour utiliser une appliance virtuelle, il est essentiel de ne pas oublier d’activer le transfert IP avec le routage défini par l’utilisateur.

#### <a name="nsg-description"></a>Description du groupe de sécurité réseau
Dans cet exemple, un groupe NSG est créé, puis chargé avec une seule règle. Ce groupe est ensuite lié uniquement aux sous-réseaux frontaux et principaux (et pas au SecNet). La règle suivante est générée de manière déclarative :

* Tout trafic (tous les ports) en provenance d’Internet vers l’ensemble du réseau virtuel (tous les sous-réseaux) est refusé.

Bien que dans cet exemple, on utilise des NSG, son principal objectif est celui d’une couche secondaire de défense contre les erreurs de configuration manuelle. L'objectif est de bloquer tout le trafic entrant à partir d'Internet vers les sous-réseaux frontaux ou principaux. Le trafic ne doit circuler que du sous-réseau SecNet au pare-feu (puis, le cas échéant, vers les sous-réseaux frontaux ou principaux). En outre, avec les règles UDR en place, tout trafic ayant atteint les sous-réseaux principal ou frontal est dirigé vers le pare-feu (grâce à l’UDR). Le pare-feu considèrerait ce trafic comme un flux asymétrique et abandonnerait le trafic sortant. Il existe par conséquent trois couches de sécurité protégeant les sous-réseaux :

* Aucune adresse IP publique sur une carte d’interface réseau FrontEnd ou BackEnd.
* Des groupes de sécurité réseau qui bloquent le trafic provenant d'Internet.
* Le pare-feu qui supprime le trafic asymétrique.

Point intéressant concernant le groupe de sécurité réseau dans cet exemple : il contient une seule règle qui consiste à refuser le trafic Internet de l’ensemble du réseau virtuel, y compris le sous-réseau de sécurité. Toutefois, étant donné que le NSG est associé uniquement aux sous-réseaux frontaux et principaux, la règle n’est pas exécutée sur le trafic entrant du sous-réseau de sécurité. Par conséquent, le trafic circule vers le sous-réseau de sécurité.

#### <a name="firewall-rules"></a>Règles de pare-feu
Sur le pare-feu, vous devez créer des règles de transfert. Étant donné que le pare-feu bloque ou transfère le trafic entrant, sortant ou intra-réseau virtuel, de nombreuses règles de pare-feu sont requises. Tout trafic entrant atteint l’adresse IP publique de service de sécurité (sur différents ports) pour être traité par le pare-feu. L’une des meilleures pratiques consiste à faire un schéma des flux logiques avant de configurer les règles de sous-réseau et de pare-feu afin d’éviter la reprise du travail par la suite. La figure qui suit est une vue logique des règles de pare-feu de cet exemple :

![10]

> [!NOTE]
> Les ports de gestion peuvent varier selon l’appliance virtuelle réseau utilisée. Dans cet exemple, il est fait référence à un pare-feu Barracuda NextGen Firewall utilisant les ports 22, 801 et 807. Consultez la documentation du fournisseur d’appliance pour rechercher les ports exacts utilisés pour la gestion de l’appareil utilisé.
>
>

#### <a name="firewall-rules-description"></a>Description des règles de pare-feu
Dans le diagramme logique précédent, le sous-réseau de sécurité n’est pas affiché, car le pare-feu est la seule ressource de ce sous-réseau. Ce diagramme présente les règles de pare-feu et la façon dont elles autorisent ou refusent logiquement les flux et non l’itinéraire réel. En outre, les ports externes sélectionnés pour le trafic RDP appartiennent à la plage supérieure de ports (8014 – 8026) et ont été sélectionnés pour s’aligner à peu près sur les deux derniers octets de l’adresse IP locale, pour faciliter la lecture (par exemple, l’adresse du serveur local 10.0.1.4 est associée à un port externe 8014). Cependant, tous les ports supérieurs non conflictuels peuvent être utilisés.

Pour cet exemple, nous avons besoin de sept types de règles :

* Règles externes (pour le trafic entrant) :
  1. Règle de gestion de pare-feu : cette règle de redirection de l’application autorise le trafic à traverser les ports de gestion de l’appliance virtuelle réseau.
  2. Règles de RDP (pour chaque serveur Windows) : ces quatre règles (une pour chaque serveur) permettent la gestion des serveurs individuels avec RDP. Les quatre règles RDP peuvent être rassemblées en une règle, en fonction de la capacité de l’appliance virtuelle réseau utilisée.
  3. Règles de trafic d’application : elles sont au nombre de deux, la première correspondant au trafic web frontal, et la seconde au trafic de l’ordinateur principal (par exemple, serveur web vers couche de données). La configuration de ces règles dépend de l’architecture réseau (sur lequel sont placés vos serveurs) et des flux de trafic (direction du flux de trafic et ports utilisés).
     * La première règle permet au trafic d’application réel de parvenir au serveur d’applications. Les autres règles concernant la sécurité et la gestion, les règles de trafic d’application sont celles qui permettent aux utilisateurs externes ou aux services d’accéder aux applications. Pour cet exemple, il n’y a qu’un serveur web sur le port 80. Par conséquent, une seule règle d’application redirige le trafic entrant vers l’adresse IP externe, vers l’adresse IP interne des serveurs web. L’adresse réseau de la session de trafic redirigée sera traduite avec la traduction d’adresses réseau vers le serveur interne.
     * La seconde règle est la règle du serveur principal qui permet au serveur web de communiquer avec le serveur AppVM01 (et non AppVM02) avec n’importe quel port.
* Règles internes (pour le trafic intra-réseau virtuel)
  1. Règle sortant vers Internet : cette règle autorise le transfert du trafic en provenance de n’importe quel réseau vers les réseaux sélectionnés. Cette règle est généralement une règle par défaut déjà présente sur le pare-feu, mais à l’état désactivé. Pour cet exemple, cette règle doit être activée.
  2. Règle DNS : cette règle autorise uniquement le trafic DNS (port 53) vers le serveur DNS. Pour cet environnement, la majeure partie du Front end vers le principal est bloquée. Cette règle autorise spécifiquement le DNS à partir de n'importe quel sous-réseau local.
  3. Règle sous-réseau à sous-réseau : cette règle permet à n’importe quel serveur du sous-réseau principal de se connecter à n’importe quel serveur du sous-réseau frontal (mais pas l’inverse).
* Règle de prévention de défaillance (pour le trafic ne répondant à aucun des éléments précédents) :
  1. Règle Refuser tout le trafic : cette règle doit toujours être la dernière règle (en termes de priorité) et, par conséquent, si un trafic ne correspond à aucune des règles qui précèdent, il sera refusé par cette règle. Cette règle est une règle par défaut et est généralement mise en place et active. Généralement, aucune modification n’est nécessaire pour cette règle.

> [!TIP]
> Sur la deuxième règle de trafic d'application, pour simplifier cet exemple, n'importe quel port est autorisé. Dans un scénario réel, le port et les plages d'adresses les plus spécifiques doivent être utilisés afin de réduire la surface d'attaque de cette règle.
>
>

Une fois les règles précédentes créées, il est important de revoir la priorité de chaque règle pour s’assurer que le trafic est autorisé ou rejeté de façon pertinente. Pour cet exemple, les règles sont classées par ordre de priorité.

#### <a name="conclusion"></a>Conclusion
Cet exemple est plus complexe, mais il s'agit d'une manière plus complète de protéger et d'isoler le réseau par rapport aux exemples précédents. (L’exemple 2 protège uniquement l'application et l’exemple 1 isole simplement les sous-réseaux). Cette conception permet de surveiller le trafic dans les deux sens ; elle protège non seulement le serveur d’applications entrantes mais applique également la stratégie de sécurité réseau à tous les serveurs sur ce réseau. En outre, selon l’appliance utilisée, une connaissance et un audit complets du trafic sont possibles. Pour plus d’informations, consultez les [instructions de génération détaillées][Example3]. Vous trouverez les instructions suivantes :

* Comment créer cet exemple de réseau de périmètre avec des scripts PowerShell classiques.
* Comment créer cet exemple avec un modèle Azure Resource Manager.
* Des descriptions détaillées de chaque UDR, commande NSG et règle de pare-feu.
* Des scénarios de flux de trafic détaillés, montrant comment le trafic est autorisé ou refusé dans chaque couche.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Exemple 4 : Ajouter une connexion hybride avec un réseau VPN d’appliance virtuelle de site à site
[Revenir à Démarrage rapide](#fast-start) | Instructions de génération détaillées bientôt disponibles

[![11]][11]

#### <a name="environment-description"></a>Description de l’environnement
La mise en réseau hybride à l’aide d’une appliance virtuelle réseau (NVA) peut être ajoutée à l’un des types de réseaux de périmètre décrits dans les exemples 1, 2 ou 3.

Comme l’indique la figure précédente, une connexion VPN sur Internet (de site à site) permet de connecter un réseau local à un réseau virtuel Azure au moyen d’une appliance virtuelle réseau.

> [!NOTE]
> Si vous utilisez ExpressRoute avec l'option d'homologation publique Azure activée, un itinéraire statique doit être créé. Cet itinéraire statique doit acheminer vers l'adresse IP du VPN de l’appliance virtuelle réseau hors de votre Internet d’entreprise et non par le biais de la connexion ExpressRoute. La traduction d’adresses réseau requise avec l'option d'homologation publique d’Azure ExpressRoute peut arrêter la session VPN.
>
>

Une fois la connexion VPN en place, la NVA devient le « hub » central pour tous les réseaux et sous-réseaux. Les règles de transfert de pare-feu déterminent quels flux de trafic sont autorisés, se voient appliquer la traduction d’adresses réseau, sont redirigés ou sont refusés (même pour les flux de trafic entre le réseau local et Azure).

Les flux de trafic doivent être envisagés avec précaution car ils peuvent être optimisés ou détériorés par ce modèle de conception, en fonction du cas d’utilisation.

L’utilisation de l’environnement créé dans l’exemple 3 et l’ajout d’une connexion réseau hybride VPN de site à site produisent la conception suivante :

[![12]][12]

Le routeur local ou tout autre appareil réseau compatible avec votre appliance virtuelle réseau pour VPN serait le client VPN. Cet appareil physique est responsable de l’initialisation et de la maintenance de la connexion VPN avec votre appliance virtuelle réseau.

Logiquement pour la NVA, le réseau ressemble à quatre « zones de sécurité » distinctes, les règles sur la NVA dirigeant principalement le trafic entre ces zones :

![13]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion réseau hybride VPN de site à site à un réseau virtuel Azure peut étendre le réseau local dans Azure de manière sécurisée. À l’aide d’une connexion VPN, votre trafic est chiffré et acheminé par Internet. La NVA, dans cet exemple, fournit un emplacement central pour appliquer et gérer la stratégie de sécurité. Pour plus d'informations, consultez les instructions de génération détaillées (bientôt disponibles). Vous trouverez les instructions suivantes :

* Comment créer cet exemple de réseau de périmètre avec des scripts PowerShell.
* Comment créer cet exemple avec un modèle Azure Resource Manager.
* Des scénarios de flux de trafic détaillés, montrant comment le trafic circule dans cette conception.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Exemple 5 : Ajouter une connexion hybride avec une passerelle VPN Azure de site à site
[Revenir à Démarrage rapide](#fast-start) | Instructions de génération détaillées bientôt disponibles

[![14]][14]

#### <a name="environment-description"></a>Description de l’environnement
La mise en réseau hybride à l’aide d’une passerelle VPN Azure peut être ajoutée à l’un des types de réseaux de périmètre décrits dans les exemples 1 ou 2.

Comme l’indique la figure précédente, une connexion VPN sur Internet (de site à site) permet de connecter un réseau local à un réseau virtuel Azure au moyen d’une passerelle VPN Azure.

> [!NOTE]
> Si vous utilisez ExpressRoute avec l'option d'homologation publique Azure activée, un itinéraire statique doit être créé. Cet itinéraire statique doit acheminer vers l'adresse IP du VPN de l’appliance virtuelle réseau hors de votre Internet d’entreprise et non par le biais du WAN ExpressRoute. La traduction d’adresses réseau requise avec l'option d'homologation publique d’Azure ExpressRoute peut arrêter la session VPN.
>
>

La figure suivante montre les deux périphéries de réseau dans cet exemple. Sur la première périphérie, les appliances virtuelles réseau et les groupes de sécurité réseau contrôlent les flux de trafic pour les réseaux intra-Azure et entre Azure et Internet. La deuxième périphérie est la passerelle VPN Azure, une périphérie de réseau distincte et isolée entre le réseau local et Azure.

Les flux de trafic doivent être envisagés avec précaution car ils peuvent être optimisés ou détériorés par ce modèle de conception, en fonction du cas d’utilisation.

L’utilisation de l’environnement créé dans l’exemple 1 et l’ajout d’une connexion réseau hybride VPN de site à site produisent la conception suivante :

[![15]][15]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion réseau hybride VPN de site à site à un réseau virtuel Azure peut étendre le réseau local dans Azure de manière sécurisée. À l’aide de la passerelle VPN Azure native, votre trafic est chiffré IPSec et acheminé par Internet. En outre, la passerelle VPN Azure peut fournir une option à moindre coût (aucun coût de licence supplémentaire à l’instar des appliances virtuelles réseau tierces). Cette option est plus économique dans l’exemple 1, où aucune appliance virtuelle réseau n’est utilisée. Pour plus d'informations, consultez les instructions de génération détaillées (bientôt disponibles). Vous trouverez les instructions suivantes :

* Comment créer cet exemple de réseau de périmètre avec des scripts PowerShell.
* Comment créer cet exemple avec un modèle Azure Resource Manager.
* Des scénarios de flux de trafic détaillés, montrant comment le trafic circule dans cette conception.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Exemple 6 : Ajouter une connexion hybride avec ExpressRoute
[Revenir à Démarrage rapide](#fast-start) | Instructions de génération détaillées bientôt disponibles

[![16]][16]

#### <a name="environment-description"></a>Description de l’environnement
La mise en réseau hybride à l’aide d’une connexion d’homologation privée ExpressRoute peut être ajoutée à l’un des types de réseaux de périmètre décrits dans les exemples 1 ou 2.

Comme l’indique la figure précédente, l’homologation privée ExpressRoute fournit une connexion directe entre votre réseau local et le réseau virtuel Azure. Le trafic passe uniquement dans le réseau du prestataire de services et le réseau Microsoft Azure, sans jamais atteindre Internet.

> [!TIP]
> L’utilisation d’ExpressRoute conserve le trafic réseau d’entreprise hors d’Internet. Il prend également en charge les accords de niveau de service de votre fournisseur ExpressRoute. La passerelle Azure peut transférer jusqu’à 10 Gbits/s avec ExpressRoute, tandis qu’avec les VPN de site à site, le débit maximal de la passerelle Azure est de 200 Mbits/s.
>
>

Comme nous voyons sur le diagramme suivant, avec cette option, l’environnement dispose maintenant de deux périphéries de réseau. La NVA et le NSG contrôlent les flux de trafic pour les réseaux intra-Azure et entre Azure et Internet, tandis que la passerelle est une périphérie de réseau distincte et isolée entre le réseau local et Azure.

Les flux de trafic doivent être envisagés avec précaution car ils peuvent être optimisés ou détériorés par ce modèle de conception, en fonction du cas d’utilisation.

L’utilisation de l’environnement créé dans l’exemple 1 et l’ajout d’une connexion réseau hybride ExpressRoute produisent la conception suivante :

[![17]][17]

#### <a name="conclusion"></a>Conclusion
L’ajout d’une connexion réseau d’homologation privée ExpressRoute peut étendre le réseau local dans Azure d’une manière sécurisée, avec moins de latence et de meilleures performances. En outre, l’utilisation de la passerelle Azure native, comme dans cet exemple, fournit une option à moindre coût (aucune licence supplémentaire à l’instar des appliances virtuelles réseau tierces). Pour plus d'informations, consultez les instructions de génération détaillées (bientôt disponibles). Vous trouverez les instructions suivantes :

* Comment créer cet exemple de réseau de périmètre avec des scripts PowerShell.
* Comment créer cet exemple avec un modèle Azure Resource Manager.
* Des scénarios de flux de trafic détaillés, montrant comment le trafic circule dans cette conception.

## <a name="references"></a>Références
### <a name="helpful-websites-and-documentation"></a>Sites web et documentation utiles
* Accès à Azure avec Azure Resource Manager :
* Accès à Azure avec PowerShell : [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Documentation relative à la mise en réseau virtuelle : [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Documentation relative aux groupes de sécurité réseau : [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/virtual-networks-nsg.md)
* Documentation relative au routage défini par l’utilisateur : [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Passerelles virtuelles Azure : [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* VPN de site à site : [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Documentation relative à ExpressRoute (consultez les sections « Mise en route » et « Procédure ») : [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Organigramme des options de sécurité"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Fonctionnalités de sécurité Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Une zone DMZ dans un réseau d’entreprise"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architecture de sécurité Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Une zone DMZ dans un réseau virtuel Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Réseau hybride avec trois limites de sécurité"
[7]: ./media/best-practices-network-security/example1design.png "Zone DMZ avec groupe de sécurité réseau (NSG)"
[8]: ./media/best-practices-network-security/example2design.png "Zone DMZ entrante avec NVA et NSG"
[9]: ./media/best-practices-network-security/example3design.png "Zone DMZ bidirectionnelle avec NVA, NSG et UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Affichage logique des règles de pare-feu"
[11]: ./media/best-practices-network-security/example3designoptions.png "Zone DMZ avec réseau hybride connecté à une NVA"
[12]: ./media/best-practices-network-security/example4designs2s.png "Zone DMZ avec NVA connectée à l’aide d’un VPN de site à site"
[13]: ./media/best-practices-network-security/example4networklogical.png "Réseau logique du point de vue de la NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "Zone DMZ avec réseau hybride de site à site connecté à une passerelle Azure"
[15]: ./media/best-practices-network-security/example5designs2s.png "Zone DMZ avec passerelle Azure utilisant un VPN de site à site"
[16]: ./media/best-practices-network-security/example6designoptions.png "Zone DMZ avec réseau hybride ExpressRoute connecté à une passerelle Azure"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Zone DMZ avec passerelle Azure utilisant une connexion ExpressRoute"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

