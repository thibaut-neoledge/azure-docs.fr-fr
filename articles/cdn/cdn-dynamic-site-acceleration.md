---
title: "Accélération de site dynamique via Azure CDN"
description: "Présentation approfondie de l’accélération de site dynamique"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: v-semcev
ms.openlocfilehash: be2719e0e02c8bc69800ef4a3e7da3c3164cb9dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Accélération de site dynamique via Azure CDN

Avec l’explosion des médias sociaux, du e-commerce et du web hyper-personnalisé, un pourcentage rapidement croissant du contenu proposé aux utilisateurs finaux est généré en temps réel. Les utilisateurs attendent des expériences web rapides, fiables et personnalisées, indépendamment de leur navigateur, de leur emplacement géographique, de leur appareil ou du réseau. Toutefois, les innovations qui rendent ces expériences si engageantes ont également pour effet de ralentir les téléchargements de pages et risquent de compromettre la qualité de l’expérience des utilisateurs. 

La fonctionnalité CDN Standard permet de rapprocher le cache de fichiers des utilisateurs finaux afin d’accélérer la distribution des fichiers statiques. Toutefois, avec des applications web dynamiques, la mise en cache de ce contenu dans des emplacements de périmètre n’est pas possible, car le serveur génère le contenu en réponse au comportement de l’utilisateur. L’accélération de la distribution d’un tel contenu est plus complexe que la mise en cache de périmètre traditionnelle, et nécessite une solution de bout en bout qui règle finement chaque élément avec le chemin d’accès aux données entier, du début jusqu’à la distribution. L’accélération de site dynamique d’Azure CDN améliore sensiblement le niveau de performance des pages web comprenant du contenu dynamique.

La fonctionnalité Azure CDN d’Akamai et Verizon offre une fonction d’optimisation d’accélération de site dynamique au moyen du menu **Optimisé pour** durant la création du point de terminaison.

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Configuration d’un point de terminaison CDN pour accélérer la distribution de fichiers dynamiques

Vous pouvez configurer votre point de terminaison CDN pour optimiser la distribution des fichiers dynamiques via le portail Azure en sélectionnant l’option **Accélération de site dynamique** sous la propriété **Optimisé pour** pendant la création du point de terminaison. Vous pouvez également utiliser nos API REST ou tout SDK client pour effectuer la même opération par programmation. 

### <a name="probe-path"></a>Chemin d’analyse
Le chemin d’analyse est une fonctionnalité spécifique de l’accélération de site dynamique. Un chemin d’analyse valide est requis pour la création. L’accélération de site dynamique utilise un petit fichier de *chemin d’analyse* placé sur l’origine pour optimiser les configurations de routage réseau pour le CDN. Vous pouvez télécharger et charger notre exemple de fichier sur votre site, ou utiliser comme chemin d’analyse une ressource éventuelle sur votre origine d’à peu près 10 Ko.

> [!Note]
> L’accélération de site dynamique entraîne des frais supplémentaires. Pour plus d’informations, consultez la [page relative aux tarifs](https://azure.microsoft.com/pricing/details/cdn/).

Les captures d’écran suivantes illustrent le processus via le portail Azure.
 
![Ajout d’un point de terminaison CDN](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*Figure 1 : ajout d’un point de terminaison CDN à partir du profil CDN*
 
![Création d’un point de terminaison CDN avec accélération de site dynamique](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*Figure 2 : création d’un point de terminaison CDN avec l’option Optimisation de l’accélération de site dynamique sélectionnée*

Une fois créé, le point de terminaison CDN applique les optimisations d’accélération de site dynamique pour tous les fichiers correspondant à certains critères. La section suivante décrit l’optimisation d’accélération de site dynamique.

## <a name="dsa-optimization-using-azure-cdn"></a>Optimisation d’accélération de site dynamique à l’aide de la fonctionnalité Azure CDN

L’accélération de site dynamique sur l’Azure CDN accélère la distribution de ressources dynamiques à l’aide des techniques suivantes :

-   Optimisation du routage
-   Optimisations de protocole TCP
-   Prérécupération d’objet (Akamai uniquement)
-   Compression d’image mobile (Akamai uniquement)

### <a name="route-optimization"></a>Optimisation du routage

L’optimisation du routage est importante, car Internet est un espace dynamique où le trafic et les pannes temporaires modifient constamment la topologie du réseau. Le protocole de passerelle frontière (BGP) est le protocole de routage d’internet, mais il peut y avoir des routages plus rapides via des serveurs PoP (Point of Presence) intermédiaires. 

L’optimisation du routage choisit le chemin d’accès optimal à l’origine, afin qu’un site soit accessible en continu et que le contenu dynamique soit distribué aux utilisateurs finaux via le routage le plus rapide et le plus fiable possible. 

Le réseau Akamai utilise des techniques pour collecter des données en temps réel et comparer divers chemins d’accès via différents nœuds dans le serveur Akamai, ainsi que l’itinéraire BGP par défaut sur l’Internet ouvert afin de déterminer le routage le plus rapide entre l’origine et le périmètre CDN. Ces techniques évitent les points de surcharge d’Internet et les itinéraires longs. 

De même, le réseau Verizon combine DNS Anycast, PoP haute capacité et contrôles d’intégrité pour déterminer les meilleures passerelles afin d’acheminer au mieux les données depuis le client vers l’origine.
 
Ainsi, le contenu entièrement dynamique et transactionnel est distribué plus rapidement et de façon plus fiable aux utilisateurs finaux, même s’il ne peut pas être mis en cache. 

### <a name="tcp-optimizations"></a>Optimisations de protocole TCP

Le protocole TCP est le standard de la suite de protocoles Internet utilisé pour distribuer des informations entre des applications sur un réseau IP.  Par défaut, plusieurs demandes dans les deux sens sont requises pour configurer une connexion TCP, et il existe des limites pour éviter des surcharges du réseau entraînant des inefficacités à grande échelle. Le CDN Azure d’Akamai traite ce problème en optimisant de trois façons : 

 - élimination des démarrages lents ;
 - exploitation des connexions persistantes ;
 - réglage des paramètres de paquet TCP (Akamai uniquement).

#### <a name="eliminating-slow-start"></a>Élimination des démarrages lents

Le *démarrage lent* est un aspect du protocole TCP qui empêche la surcharge du réseau en limitant la quantité de données envoyées sur le réseau. Il démarre avec des fenêtres de congestion de petite taille entre l’expéditeur et le récepteur, jusqu’à ce que la taille maximale soit atteinte ou une perte de paquet détectée.

Azure CDN d’Akamai et de Verizon élimine les démarrages lents en trois étapes :

1.  Les réseaux d’Akamai et de Verizon utilisent la surveillance de l’intégrité et de la bande passante pour mesurer la bande passante des connexions entre les serveurs PoP de périphérie.
2. Les métriques sont partagées entre les serveurs PoP de périphérie afin que chaque serveur tienne compte des conditions de réseau et de l’intégrité des autres serveurs PoP alentour.  
3. Les serveurs de périphérie CDN sont à présent en mesure de bâtir des hypothèses sur certains paramètres de transmission, tels que la taille de fenêtre optimale lors de la communication avec d’autres serveurs de périphérie CDN à proximité. Cette étape signifie que la taille de la fenêtre de congestion initiale peut être augmentée si l’intégrité de la connexion entre les serveurs de périphérie CDN permet des transferts de données par paquets de plus grande taille.  

#### <a name="leveraging-persistent-connections"></a>Exploitation des connexions persistantes

Avec un CDN, moins de machines se connectent directement à votre serveur d’origine par rapport aux utilisateurs qui se connectent directement à votre origine. Le CDN Azure d’Akamai et de Verizon regroupe également les demandes utilisateur afin de réduire le nombre de connexions établies avec l’origine.

Comme mentionné précédemment, les connexions TCP prennent plusieurs demandes dans les deux sens dans une poignée de main pour établir une connexion. Les connexions HTTP persistantes réutilisent les connexions TCP existantes pour plusieurs requêtes HTTP afin de raccourcir les durées de boucles et d’accélérer la distribution. 

Le réseau Verizon envoie également des paquets de persistance périodiques via la connexion TCP pour empêcher la fermeture d’une connexion.

#### <a name="tuning-tcp-packet-parameters"></a>Réglage des paramètres de paquet TCP

Le CDN Azure d’Akamai règle également les paramètres qui régissent les connexions de serveur à serveur, et réduit la quantité de boucles longues requises pour récupérer le contenu incorporé dans le site en utilisant les techniques suivantes :

1.  augmentation de la fenêtre de congestion initiale afin que davantage de paquets puissent être envoyés sans attendre d’accusé de réception ;
2.  réduction du délai de retransmission initial permettant la détection de perte et l’accélération de la retransmission ;
3.  réduction des délais de retransmission minimal et maximal pour réduire le temps d’attente avant de considérer que des paquets ont été perdus durant la transmission.

### <a name="object-prefetch-akamai-only"></a>Prérécupération d’objet (Akamai uniquement)

La plupart des sites web sont constitués d’une page HTML, qui fait référence à diverses autres ressources telles que des images et des scripts. En général, quand un client demande une page web, le navigateur commence par télécharger et analyser l’objet HTML, puis envoie des demandes supplémentaires à des ressources liées requises pour le chargement complet de la page. 

La *prérécupération* est une technique permettant de récupérer des images et des scripts incorporés dans la page HTML pendant que le code HTML est envoyé au navigateur, et avant même que celui-ci demande ces objets. 

Avec l’option de **prérécupération** activée au moment où le CDN sert la page de base HTML au navigateur du client, le CDN analyse le fichier HTML, effectue des demandes supplémentaires de ressources liées et stocke celles-ci dans son cache. Quand le client effectue les demandes de ressources liées, le serveur de périphérie CDN dispose déjà des objets demandés, et peut servir ceux-ci immédiatement sans effectuer d’aller-retour avec l’origine. Cette optimisation est bénéfique pour le contenu tant mis en cache que non mis en cache.

### <a name="adaptive-image-compression-akamai-only"></a>Compression d’image adaptative (Akamai uniquement)

Certains appareils, en particulier les appareils mobiles, sont parfois confrontés à des ralentissements de réseau. Dans ces scénarios, il est préférable pour l’utilisateur de recevoir plus rapidement des images de plus petite taille dans sa page web que d’attendre longtemps des images en pleine résolution.

Cette fonctionnalité surveille automatiquement la qualité du réseau et utilise des méthodes de compression JPEG standard lorsque la vitesse du réseau ralentit afin de raccourcir le temps de distribution.

Compression d’image adaptative | Extensions de fichier  
--- | ---  
Compression JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Mise en cache

Avec l’accélération de site dynamique, la mise en cache est désactivée par défaut sur le CDN, même quand l’origine inclut les en-têtes cache-control/expires dans la réponse. En effet, l’accélération de site dynamique est généralement utilisée pour les ressources dynamiques qui ne doivent pas être mises en cache dans la mesure où elles sont uniques à chaque client, et l’activation de la mise en cache par défaut peut rompre ce comportement.

Si vous avez un site web combinant ressources statiques et ressources dynamiques, il est préférable d’adopter une approche hybride pour obtenir le meilleure niveau de performance. 

Si vous utilisez ADN avec Verizon Premium, vous pouvez réactiver la mise en cache dans des cas spécifiques à l’aide du moteur de règles.  

Une autre solution consiste à utiliser deux points de terminaison CDN : l’un doté de l’accélération de site dynamique, pour fournir des ressources dynamiques, l’autre présentant un type d’optimisation statique, tel qu’une remise web générale, pour fournir les ressources pouvant être mises en cache. Pour mettre en œuvre cette solution, vous devez modifier les URL de vos pages web afin qu’elles pointent directement sur le point de terminaison CDN vers la ressource que vous envisagez d’utiliser. 

Par exemple : `mydynamic.azureedge.net/index.html` est une page dynamique et est chargée à partir du point de terminaison avec accélération de site dynamique.  La page html fait référence à plusieurs ressources statiques telles que des images ou des bibliothèques JavaScript qui sont chargées à partir du point de terminaison CDN statique, telles que `mystatic.azureedge.net/banner.jpg` et `mystatic.azureedge.net/scripts.js`. 

Vous trouverez [ici](https://docs.microsoft.com/azure/cdn/cdn-cloud-service-with-cdn#controller) un exemple d’utilisation de contrôleurs dans une application web ASP.NET pour servir du contenu par le biais d’une URL CDN spécifique.




