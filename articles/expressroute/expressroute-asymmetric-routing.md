---
title: "Routage asymétrique | Microsoft Docs"
description: "Cet article vous présente des solutions aux problèmes auxquels les clients peuvent être confrontés du fait d’un routage asymétrique dans un réseau lorsqu’il existe plusieurs connexions vers une même destination."
documentationcenter: na
services: expressroute
author: osamazia
manager: carmonm
editor: 
ms.assetid: a754bff9-95c9-44b5-9796-377fc21e8322
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: osamam
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: 8568c13d2834a0643e15ab1814a35c92123837d1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Routage asymétrique avec chemins d’accès réseau multiples
Cet article explique comment le trafic réseau entrant et sortant peut emprunter différentes voies lorsque plusieurs chemins d’accès sont disponibles entre la source et la destination réseau.

Pour comprendre le routage asymétrique, il est essentiel de comprendre deux concepts. Le premier est l’impact de l’existence de plusieurs chemins d’accès réseau. Le deuxième est la manière dont les appareils (par exemple, un pare-feu) maintiennent l’état. Ces types d’appareils sont appelés appareils avec état. La combinaison de ces deux facteurs crée des scénarios dans lesquels le trafic réseau est interrompu par un appareil avec état parce que ce dernier n’a pas détecté le trafic généré par lui-même.

## <a name="multiple-network-paths"></a>Chemins d’accès réseau multiples
Lorsqu’un réseau d’entreprise ne dispose que d’une seule connexion à Internet fournie via le fournisseur d’accès à Internet, l’intégralité du trafic Internet entrant et sortant transite par le même chemin. Les entreprises acquièrent souvent plusieurs circuits afin de disposer de chemins redondants et d’améliorer la disponibilité de leur réseau. Dans de tels cas, il peut arriver que le trafic sortant du réseau pour rejoindre Internet transite par une connexion différente de celle empruntée par le trafic entrant. Cela est communément appelé routage asymétrique. Dans le routage asymétrique, le trafic réseau sortant emprunte un autre chemin d’accès à partir du flux d’origine.

![Réseau avec plusieurs chemins d’accès](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Même si cela se produit principalement sur Internet, le routage asymétrique s’applique également à d’autres combinaisons de chemins d’accès multiples. Par exemple, cela s’applique à un chemin d’accès Internet et à un chemin privé menant à la même destination, et à plusieurs chemins privés menant à la même destination.

Chaque routeur présent entre la source et la destination calcule le meilleur chemin d’accès à une destination. La détermination du meilleur chemin d’accès par le routeur repose sur deux facteurs principaux :

* Le routage entre des réseaux externes repose sur le protocole de routage BGP (protocole de passerelle frontière). Le BGP collecte les annonces voisines et les soumet à plusieurs opérations afin de déterminer le meilleur chemin d’accès à la destination prévue. Il stocke le meilleur chemin d’accès dans sa table de routage.
* La longueur d’un masque de sous-réseau associé à un itinéraire influence les chemins de routage. Si un routeur reçoit plusieurs annonces pour la même adresse IP mais avec différents masques de sous-réseau, le routeur privilégie l’annonce présentant le masque de sous-réseau le plus long car l’itinéraire est considéré comme plus précis.

## <a name="stateful-devices"></a>Appareils avec état
Les routeurs examinent l’en-tête IP d’un paquet pour les besoins du routage. Certains appareils procèdent à une analyse plus en profondeur du paquet. En règle générale, ces appareils examinent les en-têtes Layer4 (Transmission Control Protocol ou TCP ; ou User Datagram Protocol ou UDP), ou même Layer7 (couche application). Ces types d’appareils sont soit des appareils de sécurité, soit des appareils d’optimisation de la bande passante. 

Un pare-feu est un exemple courant d’appareil avec état. Un pare-feu autorise ou refuse un paquet à transiter via ses interfaces en fonction de divers champs tels que le protocole, le port TCP/UDP et les en-têtes d’URL. Ce niveau d’inspection des paquets soumet l’appareil à une forte charge de traitement. Afin d’améliorer les performances, le pare-feu inspecte le premier paquet d’un flux. S’il autorise le paquet à poursuivre, il conserve les informations de flux dans sa table des états. Tous les paquets suivants liés à ce flux sont autorisés en fonction de la détermination initiale. Il se peut qu’un paquet faisant partie d’un flux existant arrive au niveau du pare-feu. Si le pare-feu ne dispose d’aucune information d’état préalable le concernant, le pare-feu interrompt le paquet.

## <a name="asymmetric-routing-with-expressroute"></a>Routage asymétrique avec ExpressRoute
Lorsque vous vous connectez à Microsoft via Azure ExpressRoute, votre réseau change de la manière suivante :

* Vous disposez de plusieurs connexions à Microsoft. L’une d’elles est votre connexion Internet existante, et l’autre est celle établie via ExpressRoute. Il se peut qu’une partie du trafic circulant vers Microsoft passe par Internet mais revienne via ExpressRoute, et inversement.
* ExpressRoute retourne des adresses IP plus spécifiques. Ainsi, concernant le trafic de votre réseau vers Microsoft pour les services proposés via ExpressRoute , les routeurs privilégient toujours ExpressRoute.

Pour comprendre l’impact de ces deux modifications sur un réseau, étudions quelques scénarios. Supposons par exemple que vous disposez d’un circuit unique de connexion à Internet, et que vous utilisez tous les services Microsoft par le biais d’Internet. Le trafic circulant de votre réseau vers Microsoft et dans le sens inverse passe par la même connexion Internet, et transite par le pare-feu. Le pare-feu enregistre le flux lorsqu’il identifie le premier paquet et les paquets de retour sont autorisés car le flux existe dans la table des états.

![Routage asymétrique avec ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Ensuite, vous activez ExpressRoute et utilisez des services proposés par Microsoft via ExpressRoute. Tous les autres services Microsoft sont utilisés sur Internet. Vous déployez un pare-feu distinct sur votre périphérie connectée à ExpressRoute. Microsoft annonce des préfixes plus spécifiques à votre réseau via ExpressRoute pour des services spécifiques. Votre infrastructure de routage choisit ExpressRoute comme chemin d’accès par défaut pour ces préfixes. Si vous ne publiez pas vos adresses IP publiques sur Microsoft via ExpressRoute, Microsoft communique vos adresses IP publiques via Internet. Le trafic sortant de votre réseau vers Microsoft utilise ExpressRoute, tandis que le trafic entrant depuis Microsoft utilise Internet. Lorsque le pare-feu à la périphérie voit un paquet de réponse pour un flux introuvable dans la table des états, il interrompt le trafic entrant.

Si vous choisissez d’utiliser le même pool NAT (traduction d’adresse réseau) pour ExpressRoute et Internet, vous verrez des problèmes similaires avec les clients sur les adresses IP privées dans votre réseau. Les requêtes de services tels que Windows Update passe par Internet car les adresses IP pour ces services ne sont pas publiées via ExpressRoute. Le trafic entrant revient toutefois via ExpressRoute. Si Microsoft reçoit une adresse IP avec le même masque de sous-réseau à partir d’Internet et d’ExpressRoute, ExpressRoute est favorisé par rapport à Internet. Si un pare-feu ou un autre appareil avec état à la périphérie du réseau exposé à ExpressRoute ne dispose d’aucune information préalable sur le flux, il interrompt les paquets appartenant à ce flux.

## <a name="asymmetric-routing-solutions"></a>Solutions de routage asymétrique
Vous disposez de deux options principales pour résoudre le problème de routage asymétrique. L’une est le routage et l’autre est l’utilisation de la NAT basée sur la source (SNAT).

### <a name="routing"></a>Routage
Assurez-vous que vos adresses IP publiques sont publiées sur les connexions WAN appropriées. Par exemple, si vous souhaitez utiliser Internet pour le trafic d’authentification et ExpressRoute pour le trafic de messagerie, vous ne devez pas publier vos adresses IP publiques Active Directory Federation Services (ADFS) via ExpressRoute. De même, assurez-vous de ne pas exposer un serveur ADFS local aux adresses IP reçues par le routeur via ExpressRoute. Les itinéraires reçus via ExpressRoute étant plus spécifiques, ils entraînent la prévalence d’ExpressRoute comme chemin du trafic d’authentification vers Microsoft. Cela engendre un routage asymétrique.

Si vous souhaitez utiliser ExpressRoute pour l’authentification, assurez-vous que vous publiez les adresses IP publiques ADFS via ExpressRoute sans NAT. De cette façon, le trafic provenant de Microsoft et se dirigeant vers le serveur ADFS local transite via ExpressRoute. Le trafic entrant transitant entre le client et Microsoft utilise ExpressRoute car il s’agit de l’itinéraire privilégié sur Internet.

### <a name="source-based-nat"></a>NAT basé sur la source
Les problèmes de routage asymétrique peuvent également être résolus à l’aide d’une NAT basée sur la source (SNAT). Par exemple, vous n’avez pas publié l’adresse IP publique d’un serveur SMTP (Simple Mail Transfer Protocol) local via ExpressRoute, car vous prévoyez d’utiliser Internet pour ce type de communication. Une requête en provenance de Microsoft qui accède au serveur SMTP local transite via Internet. Vous définissez l’adresse SNAT de la requête entrante sous une adresse IP interne. Le trafic sortant du serveur SMTP passe par le pare-feu périphérique (utilisé pour la NAT) à la place d’ExpressRoute. Le trafic sortant revient en transitant par Internet.

![Configuration réseau avec NAT basée sur la source](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Détection du routage asymétrique
La détermination d’itinéraire constitue le meilleur moyen de vous assurer que le trafic réseau circule par le chemin attendu. Si vous souhaitez que le trafic entre votre serveur SMTP local et Microsoft passe par Internet, déterminez son itinéraire attendu à partir du serveur SMTP vers Office 365. Le résultat valide le passage du trafic sortant de votre réseau par Internet et non par ExpressRoute.


