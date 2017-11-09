---
title: "Optimisation de la diffusion multimédia en continu via le réseau Azure Content Delivery Network"
description: "Optimisation de la diffusion en continu de fichiers multimédias pour une distribution lisse"
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
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 02cd0fe30a2a14f42a16ed12f714d496bbb23b36
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="media-streaming-optimization-via-the-azure-content-delivery-network"></a>Optimisation de la diffusion multimédia en continu via le réseau Azure Content Delivery Network 
 
L’utilisation de la vidéo haute définition augmente sur Internet, ce qui crée des difficultés pour une distribution efficace de fichiers volumineux. Les clients attendent une lecture fluide de ressources de vidéo en direct ou à la demande sur toutes sortes de réseaux, et une clientèle mondiale. Un mécanisme de distribution rapide et efficace pour la diffusion multimédia de ces fichiers est essentiel pour garantir au consommateur une expérience fluide et agréable.  

La diffusion multimédia en continu en direct est particulièrement difficile à distribuer en raison des tailles importantes des fichiers et du grand nombre d’utilisateurs simultanés. Des délais d’attente prolongés découragent les utilisateurs. Les flux temps réel ne peuvent pas être mis en cache en avance et d’importantes latences ne sont pas acceptables pour les utilisateurs. C’est pourquoi les fragments vidéo doivent être distribués en temps opportun. 

Les modèles de requêtes de diffusion en continu présentent également certains défis. Quand un flux temps réel populaire ou une nouvelle série sont publiés pour la vidéo à la demande, des millions d’utilisateurs peuvent demander ce flux simultanément. Dans ce cas, une consolidation des requêtes actives intelligente est essentielle pour ne pas surcharger les serveurs d’origine lorsque les ressources ne sont pas encore mises en cache.
 
Le réseau Azure Content Delivery Network d’Akamai offre désormais une fonctionnalité qui fournit du contenu multimédia en flux continu efficacement, à grande échelle, à des utilisateurs dans le monde entier. La fonctionnalité réduit les latences, car elle réduit la charge sur les serveurs d’origine. Cette fonctionnalité est disponible au niveau de tarification Standard d’Akamai. 

Le réseau Azure Content Delivery Network de Verizon distribue le contenu multimédia en diffusion continue directement dans le type d’optimisation de livraison web générale.
 
## <a name="configure-an-endpoint-to-optimize-media-streaming-in-the-azure-content-delivery-network-from-akamai"></a>Configurer un point de terminaison pour optimiser la diffusion multimédia en continu dans le réseau Azure Content Delivery Network d’Akamai
 
Vous pouvez configurer votre point de terminaison CDN pour optimiser la distribution de fichiers volumineux via le portail Azure. Pour ce faire, vous pouvez également utiliser nos API REST ou tout SDK client. Les étapes suivantes montrent le processus via le portail Azure :

1. Pour ajouter un nouveau point de terminaison, dans la page **Profil CDN**, sélectionnez **Point de terminaison**.
  
    ![Nouveau point de terminaison](./media/cdn-media-streaming-optimization/01_Adding.png)

2. Dans la liste déroulante **Optimisé pour**, sélectionnez **Streaming de vidéo à la demande** pour les ressources de vidéo à la demande. Si vous offrez une combinaison de diffusion en continu en direct et de vidéo à la demande, sélectionnez **Streaming de contenu général**.

    ![Diffusion en continu sélectionné](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
Une fois le point de terminaison créé, il applique les optimisations pour tous les fichiers correspondant à certains critères. La section suivante décrit ce processus. 
 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-akamai"></a>Optimisations de la diffusion multimédia en continu pour le réseau Azure Content Delivery Network d’Akamai
 
L’optimisation de la diffusion multimédia en continu d’Akamai est efficace pour la diffusion multimédia en continu en direct ou en vidéo à la demande, qui utilise des fragments multimédias individuels pour la remise. Ce processus diffère du transfert d’une seule ressource volumineuse via un téléchargement progressif ou en utilisant des demandes de plage d’octets. Pour plus d’informations sur ce style de livraison de données multimédia, voir [Optimisation des fichiers volumineux](cdn-large-file-optimization.md).


L’optimisation des livraisons de données multimédias générale ou en vidéo à la demande utilise un CDN avec des optimisations du serveur principal pour distribuer plus rapidement des ressources multimédias. Elle utilisent également des configurations pour les ressources multimédias, basées sur les meilleures pratiques apprises au fil du temps.

### <a name="caching"></a>Mise en cache

Si le réseau Azure Content Delivery Network d’Akamai détecte que la ressource est un manifeste ou un fragment de diffusion en continu, il utilise des délais d’expiration de mise en cache différents de la livraison web générale (voir la liste complète dans le tableau suivant). Comme toujours, les en-têtes Cache-control ou Expires en provenance de l’origine sont respectés. Si la ressource n’est pas une ressource multimédia, elle est mise en cache dans le respect des délais d’expiration pour la livraison web générale.

Le temps de mise en cache négatif court est utile pour le déchargement de l’origine lorsque de nombreux utilisateurs demandent un fragment qui n’existe pas encore. Un exemple est un flux temps réel où les paquets ne sont pas disponibles à l’origine à cette seconde. L’intervalle de mise en cache plus long facilite également les demandes de déchargement de l’origine, car le contenu vidéo n’est généralement pas modifié.
 

|   | Livraison web générale | Diffusion multimédia en continu générale | Diffusion multimédia en continu de vidéo à la demande  
--- | --- | --- | ---
Mise en cache : positive <br> HTTP 200, 203, 300, <br> 301, 302 et 410 | 7 jours |365 jours | 365 jours   
Mise en cache : négative <br> HTTP 204, 305, 404, <br> et 405 | Aucune | 1 seconde | 1 seconde
 
### <a name="deal-with-origin-failure"></a>Traitement des défaillances de l’origine  

Les livraisons de données multimédias générale et en vidéo à la demande ont aussi un délai d’expiration d’origine et un journal des tentatives basé sur les meilleures pratiques pour les modèles de demande standard. Par exemple, étant donné que la livraison de données multimédias générale est destinée la distribution en direct et en vidéo à la demande, elle utilise un délai d'expiration de connexion plus court parce que la diffusion en continu en direct est, par nature, soumise à une contrainte de temps.

Quand une connexion arrive à expiration, le CDN retente de l’établir un certain nombre de fois avant d’envoyer une erreur « 504 - Dépassement du délai de la passerelle » au client. 

Quand un fichier correspond à la liste des types de fichiers et des conditions de taille, le CDN utilise le comportement applicable à la diffusion multimédia en continu. Autrement, il utilise une livraison web générale.
   
### <a name="conditions-for-media-streaming-optimization"></a>Conditions d’optimisation de la diffusion multimédia en direct 

Le tableau suivant répertorie l’ensemble des critères à satisfaire pour cette optimisation de la diffusion multimédia en continu : 
 
Types de diffusions en continu pris en charge | Extensions de fichier  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, key, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Structure de l’URL seg-frag <br> (expression régulière correspondante : ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Diffusion en continu lisse | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-the-azure-content-delivery-network-from-verizon"></a>Optimisations de la diffusion multimédia en continu pour le réseau Azure Content Delivery Network de Verizon

Le réseau Azure Content Delivery Network de Verizon distribue les ressources multimédia en diffusion continue directement en utilisant le type d’optimisation de livraison web générale. Quelques fonctionnalités du CDN contribuent directement à la distribution de ressources multimédias par défaut.

### <a name="partial-cache-sharing"></a>Partage de cache partiel

Le partage de cache partiel permet au CDN de servir du contenu partiellement mis en cache aux nouvelles demandes. Par exemple, si la première demande adressée au CDN aboutit à une absence dans le cache, la demande est envoyée à l’origine. Bien que ce contenu incomplet soit chargé dans le cache du CDN, d’autres demandes adressées au CDN peuvent commencer à obtenir ces données. 

### <a name="cache-fill-wait-time"></a>Temps d’attente de remplissage du cache

 La fonctionnalité de temps d’attente de remplissage du cache oblige le serveur de périphérie à mettre en attente toutes les demandes suivantes pour la même ressource jusqu'à ce que les en-têtes de réponse HTTP arrivent en provenance du serveur d’origine. Si les en-têtes de réponse HTTP en provenance de l’origine arrivent avant l’expiration du minuteur, toutes les demandes mises en attente sont servies à partir du cache croissant. En même temps, le cache est rempli par des données en provenance de l’origine. Par défaut, le temps d’attente de remplissage du cache est défini sur 3 000 millisecondes. 

