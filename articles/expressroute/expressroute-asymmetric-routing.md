<properties
   pageTitle="Routage asymétrique | Microsoft Azure"
   description="Cet article offre des solutions aux problèmes auxquels les clients peuvent être confrontés du fait d’un routage asymétrique dans leur réseau lorsqu’il existe plusieurs connexions vers une même destination."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="osamazia"/>

# Routage asymétrique avec chemins d’accès réseau multiples

Cet article explique comment le trafic entrant et sortant peut emprunter différentes voies lorsque plusieurs chemins d’accès sont disponibles entre la source et la destination.

La compréhension du routage asymétrique présuppose celle de deux concepts. Le premier concept est l’impact de l’existence de plusieurs chemins d’accès réseau. Le second est le comportement des dispositifs de maintien d’état tels que les pare-feu, également appelés dispositifs avec état. L’association de ces deux facteurs donne lieu à des situations où le trafic est interrompu par un dispositif avec état parce que ce dernier n’a pas reconnu le trafic généré par lui-même.

## Chemins d’accès réseau multiples

Lorsqu’un réseau d’entreprise ne dispose que d’une seule connexion à Internet fournie par le fournisseur d’accès à Internet, l’intégralité du trafic Internet entrant et sortant passe par le même chemin. Les entreprises acquièrent souvent plusieurs circuits afin de disposer de chemins redondants et d’améliorer la disponibilité de leur réseau. Dans de tels cas, il peut arriver que le trafic sortant du réseau pour rejoindre Internet passe par une connexion différente que celle empruntée par le réseau entrant. Ce phénomène est communément appelé routage asymétrique, en ce que le trafic entrant emprunte un chemin différent de celui emprunté par le flux d’origine.

![Routage3](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Si la description qui précède s’applique à Internet, elle concerne également d’autres combinaisons de chemins multiples. En voici quelques exemples : un chemin d’accès Internet et un chemin privé menant à la même destination, plusieurs chemins privés menant à la même destination, etc.

Chaque routeur situé entre la source et la destination détermine le meilleur chemin vers une destination en fonction de son calcul du meilleur chemin pour atteindre la destination. La détermination du meilleur chemin est fondée sur deux facteurs principaux.

1.	Le routage entre des réseaux externes repose sur le protocole de routage appelé Protocole de passerelle frontière ou BGP. Le BGP collecte les annonces voisines et les soumet à plusieurs opérations afin de déterminer le meilleur chemin pour atteindre la destination, et installe ce dernier dans sa table de routage.
2.	Longueur du masque de sous-réseau associé à un itinéraire. En cas de réception de plusieurs annonces pour une même adresse IP mais de différents masques de sous-réseau, la priorité est donnée à l’annonce présentant le masque de sous-réseau le plus long car l’itinéraire est considéré comme plus précis.

## Appareils avec état

Les routeurs examinent l’en-tête IP du paquet pour les besoins du routage. Certains dispositifs procèdent cependant à une analyse plus en profondeur du paquet. Ils examinent généralement les en-têtes Couche 4 (TCP/UDP) ou même Couche 7 (couche application). Ces dispositifs sont soit des dispositifs de sécurité, soit des dispositifs d’optimisation de la bande passante. Le pare-feu est un exemple courant de dispositif avec état. Le pare-feu autorise ou refuse un paquet via ses interfaces en fonction de divers champs tels que le protocole, le port TCP/UDP, ou encore les en-têtes d’URL. Cette inspection des paquets soumet le dispositif à une forte charge de traitement. Afin d’améliorer les performances, le pare-feu inspecte le premier paquet d’un flux. Si le paquet est autorisé, il conserve les informations de flux dans sa table des états. Tous les paquets suivants liés à ce flux sont autorisés en fonction de la décision initiale. Par conséquent, lorsqu’un paquet faisant partie d’un flux existant arrive au niveau du pare-feu, et lorsque le pare-feu ne possède aucune information d’état préalable le concernant, le pare-feu ignore ce paquet.

## Routage asymétrique avec ExpressRoute

Lorsque vous vous connectez à Microsoft via ExpressRoute, les modifications suivantes se produisent sur votre réseau.

1.	Vous disposez de plusieurs connexions à Microsoft. L’une d’elles est votre connexion Internet existante, et l’autre est celle établie via ExpressRoute. Une partie du trafic circulant vers Microsoft peut passer par Internet mais revenir via ExpressRoute, et inversement.
2.	ExpressRoute retourne des adresses IP plus spécifiques. Ainsi, le trafic de votre réseau vers Microsoft pour les services proposés via ExpressRoute toujours préfère systématiquement ExpressRoute.

Pour comprendre l’impact des deux éléments ci-dessus, penchons-nous sur différents scénarios. Supposons que vous disposez d’un circuit unique de connexion à Internet, et que vous utilisez tous les services Microsoft par le biais d’Internet. Le trafic circulant de votre réseau vers Microsoft et dans le sens inverse passe par la même connexion Internet, et transite par le pare-feu. Le pare-feu enregistre le flux lorsqu’il identifie le premier paquet et les paquets de retour sont autorisés car le flux existe dans la table des états.

![Routage1](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)


Vous activez ExpressRoute et utilisez des services proposés par Microsoft via ExpressRoute. Tous les autres services Microsoft sont utilisés sur Internet. Vous déployez un pare-feu distinct sur votre périphérie connectée à ExpressRoute. Microsoft annoncera alors des préfixes plus spécifiques à votre réseau via ExpressRoute pour des services spécifiques. Votre infrastructure de routage choisira ExpressRoute comme chemin d’accès par défaut pour ces préfixes. Si ne vous publiez pas vos adresses IP publiques sur Microsoft via ExpressRoute, Microsoft communiquera vos adresses IP publiques via Internet. Par conséquent, le trafic sortant de votre réseau vers Microsoft utilisera ExpressRoute, tandis que le trafic entrant depuis Microsoft utilisera Internet. Lorsque le pare-feu à la périphérie verra un paquet de réponse pour un flux introuvable dans la table des états, il interrompra le trafic entrant.

Si vous choisissez d’utiliser le même pool NAT pour ExpressRoute et Internet, vous verrez des émissions similaires avec les clients sur les adresses IP privées dans votre réseau. Les requêtes de services tels que Windows Update passeront par Internet car les adresses IP pour ces services ne sont pas publiées via ExpressRoute. Le trafic entrant reviendra toutefois via ExpressRoute. Si Microsoft reçoit une adresse IP avec le même masque de sous-réseau à partir d’Internet et d’ExpressRoute, ExpressRoute sera favorisé par rapport à Internet. Si un pare-feu ou un autre dispositif à la périphérie du réseau exposé à ExpressRoute ne dispose d’aucune information préalable sur le flux, il annulera les paquets appartenant à ce flux.

## Solutions pour le routage asymétrique

Il existe deux façons de résoudre le problème du routage asymétrique. L’un s’effectue par le biais du routage, et l’autre par le biais d’un NAT basé sur la source (SNAT).

1. Routage

    - Assurez-vous que vos adresses IP publiques sont publiées sur les connexions WAN qui conviennent. Par exemple, si vous souhaitez utiliser Internet pour le trafic d’authentification et ExpressRoute pour votre trafic de messagerie. Ainsi, vous n’aurez pas à publier vos adresses IP publiques AD FS via ExpressRoute. De même, un serveur AD FS local ne doit pas être exposé à des adresses IP reçues via ExpressRoute. Les itinéraires reçus via ExpressRoute étant plus spécifiques, ils entraînent la prévalence d’ExpressRoute comme chemin d’authentification vers Microsoft, ce qui engendre un routage asymétrique.

    - Si vous souhaitez utiliser ExpressRoute pour l’authentification, assurez-vous que vous publiez les adresses IP publiques AD FS via ExpressRoute sans NAT. Ainsi, le trafic circulant depuis Microsoft vers un serveur AD FS local passera par ExpressRoute, tandis que le trafic entrant depuis le client vers Microsoft utilisera ExpressRoute du fait de sa prévalence par rapport à Internet.

2. NAT basé sur la source

	Les problèmes de routage asymétrique peuvent également être résolus à l’aide d’un NAT basé sur la source (SNAT). Par exemple, si vous n’avez pas publié l’adresse IP publique du serveur SMTP local via ExpressRoute dans l’intention d’utiliser Internet pour cette communication. Une requête en provenance de Microsoft vers votre serveur SMTP local circule sur Internet. Vous définissez l’adresse source de la requête entrante sous une adresse IP interne. Le trafic entrant à partir du serveur SMTP passera par le pare-feu périphérique (utilisé pour le NAT) à la place d’ExpressRoute. Ainsi, le trafic entrant reviendra via Internet.


![Routage2](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## Détection de routage asymétrique

La détermination d’itinéraire constitue le meilleur moyen de vous assurer que le trafic circule par le chemin attendu. Si vous souhaitez que le trafic entre votre serveur SMTP local et Microsoft passe par Internet, déterminez son itinéraire à partir du serveur SMTP vers Office 365. Le résultat validera le passage du trafic sortant de votre réseau par Internet et non par ExpressRoute.

<!---HONumber=AcomDC_0824_2016-->