---
title: "Optimisation de la diffusion multimédia en continu via Azure CDN"
description: "Optimisation de la diffusion des fichiers multimédia en continu pour la distribution lisse"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>Optimisation de la diffusion multimédia en continu via Azure CDN 
 
La vidéo haute définition devient une partie en constante évolution d’internet, créant alors différents problèmes relatifs à une distribution efficace de fichiers aussi volumineux sur internet. Les clients attendent une lecture fluide des ressources en direct ou en VOD sur des réseaux et des clients variés dans le monde entier. Il est essentiel de fournir un mécanisme de distribution plus rapide et efficace de ces fichiers de diffusion multimédia pour garantir une expérience client continue et agréable.  

La diffusion en continu multimédia en direct est particulièrement difficile à distribuer, non seulement en raison des grandes tailles et du grand nombre d’utilisateurs simultanés, mais également en raison des retards qui représentent une rupture de contrat pour les utilisateurs. Les flux en direct ne peuvent pas être mis en cache en avance et d’importantes latences ne sont pas acceptables pour les utilisateurs. C’est pour cette raison que les fragments vidéo doivent être distribués dans les délais impartis. 

Les modèles de requêtes de diffusion en continu présentent également certains défis. Qu’il s’agisse d’une diffusion en continu en direct ou de la sortie d’une nouvelle série de vidéo à la demande (VOD), des millions d’utilisateurs peuvent tenter de demander la diffusion en même temps. Dans ce cas, une consolidation des requêtes actives intelligente est essentielle pour ne pas surcharger les serveurs d’origine lorsque les ressources ne sont pas encore mises en cache.
 
Azure CDN d’Akamai propose désormais une fonctionnalité adaptée à la distribution efficace de ressources de diffusion multimédia en continu aux utilisateurs finaux dans le monde entier à des latences à l’échelle et réduites tout en diminuant la charge sur les serveurs d’origine. Cette fonctionnalité est disponible via la fonctionnalité « Optimisé pour » sur le point de terminaison Azure CDN créé sous un profil Azure CDN avec un niveau de tarification « Akamai Standard ». Vous devez utiliser l’optimisation « Diffusion multimédia de vidéo à la demande » pour les ressources VOD et le type d’optimisation « Diffusion multimédia en continu général » si vous effectuez une combinaison de diffusion en continu en direct et en VOD.

Azure CDN de Verizon peut offrir la diffusion multimédia en continu directement dans le type d’optimisation « distribution générale sur le Web ».
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>Configuration de point de terminaison CDN pour optimiser la diffusion multimédia en continu dans Azure CDN d’Akamai
 
Vous pouvez configurer votre point de terminaison CDN pour optimiser la distribution des fichiers volumineux via le portail Azure en sélectionnant simplement l’option « Diffusion multimédia en continu générale » ou « Diffusion multimédia en continu de la vidéo à la demande » sous la propriété « Optimisé pour » au cours de la création du point terminal. Pour ce faire, vous pouvez également utiliser nos API REST ou un des kits de développement logiciel client. Les captures d’écran ci-dessous illustrent le processus via le portail Azure. 
  
![Ajouter un nouveau point de terminaison](./media/cdn-media-streaming-optimization/01_Adding.png)

*Figure 1 : ajout d’un nouveau point de terminaison CDN à partir du profil CDN*

![Diffusion en continu sélectionné](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
*Figure 2 : création d’un point de terminaison CDN avec la diffusion multimédia en continu de la vidéo à la demande sélectionnée* 
 
Une fois le point de terminaison CDN créé, l’optimisation de tous les fichiers qui correspondent à certains critères est appliquée. La section suivante décrit cela en détail. 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Optimisations de la diffusion multimédia en continu pour Azure CDN d’Akamai
 
L’optimisation de la diffusion multimédia en continu d’Akamai est efficace pour la diffusion multimédia en continu en direct et en VOD qui utilise des fragments multimédias individuels pour la distribution, contrairement au transfert d’une ressource volumineuse via le téléchargement progressif ou au moyen de requêtes de plages d’octets. Pour ce style de distribution multimédia, consultez [l’optimisation de fichiers volumineux](cdn-large-file-optimization.md).

À l’aide de la distribution multimédia générale ou en VOD, le type d’optimisation utilise un réseau CDN avec des optimisations principales pour distribuer des ressources multimédias plus rapidement, ainsi que des configurations pour des ressources multimédias basées sur les meilleures pratiques acquises au fil du temps.

### <a name="caching"></a>Mise en cache

Si Azure CDN d’Akamai détecte que la ressource est un manifeste ou un fragment de la diffusion en continu (consultez la liste complète ci-dessous), le CDN utilise différents délais d’expiration mis en cache à partir de la distribution générale. Les en-têtes Cache-Control ou Expires envoyés depuis l’origine sont toujours respectés et, si la ressource n’est pas une ressource multimédia, la mise en cache est effectuée au moyen du délai d’expiration de la distribution générale sur le Web.

Le court délai de mise en cache négatif est utile pour le déchargement de l’origine lorsque de nombreux utilisateurs demandent un fragment qui n’existe pas encore, comme dans une diffusion en continu en direct où les paquets ne sont pas disponibles dans la seconde depuis l’origine. L’intervalle de mise en cache plus long permet également de décharger des requêtes depuis l’origine lorsque le contenu vidéo n’est pas généralement modifié.
 

|    | Généralités<br> web<br>livraison continue | Généralités<br> Médias<br> diffusion en continu | VOD<br>Médias<br> diffusion en continu  
--- | --- | --- | ---
Mise en cache - positive <br> HTTP 200, 203, 300, <br> 301, 302 et 410 | 7 jours |365 jours | 365 jours   
Mise en cache - négative <br> HTTP 204, 305, 404, <br> et 405 | Aucun | 1 seconde | 1 seconde
 
### <a name="dealing-with-origin-failure"></a>Traitement des défaillances d’origine  

La distribution multimédia générale et la distribution multimédia en VOD ont aussi un délai d’expiration d’origine et relancent le journal basé sur les meilleures pratiques pour les modèles de requêtes standard. Par exemple, étant donné que la distribution multimédia générale est destinée à la fois à la distribution multimédia en direct et en VOD, elle utilise un délai de connexion beaucoup plus court en raison de l’importance du temps dans le cadre de la diffusion en continu en direct.

Lorsqu’une connexion expire, le CDN retente la connexion un certain nombres de fois avant d’envoyer une erreur de délai d’attente de la passerelle 504 au client. 

Lorsqu’un fichier correspond à la liste des conditions de types de fichiers et de tailles, le CDN utilise le comportement pour la diffusion multimédia en continu, sinon celui de la distribution générale sur le Web. 
   
### <a name="conditions-for-media-streaming-optimization"></a>Conditions d’optimisation de la diffusion multimédia en direct 

Le tableau suivant répertorie l’ensemble des critères à satisfaire pour cette optimisation 
 
Types de diffusion en direct pris en charge | Extensions de fichiers  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, key, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Structure de l’URL seg-frag <br> (expression régulière correspondante : ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Smooth Streaming | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Optimisations de la diffusion multimédia en continu pour Azure CDN de Verizon

Azure CDN de Verizon peut distribuer des ressources multimédias en continu directement à l’aide du type d’optimisation « distribution générale sur le Web ». Quelques fonctionnalités sur le CDN par défaut assistent directement la distribution de ressources multimédias.

### <a name="partial-cache-sharing"></a>Partage de cache partiel

Ceci permet la diffusion de contenu partiellement mis en cache à partir du CDN vers de nouvelles requêtes. Par exemple, la première requête au CDN se traduit par une absence de cache et la requête est alors envoyée à l’origine. Tandis que ce contenu est toujours chargé dans le cache du CDN (mais est toujours incomplet), les autres requêtes envoyées vers le CDN peuvent démarrer l’obtention de ces données. 

### <a name="cache-fill-wait-time"></a>Temps d’attente de remplissage du cache

En association avec le partage du cache partiel, la fonctionnalité du temps d’attente de remplissage du cache est utile car elle oblige le serveur Edge à retenir toutes les requêtes suivantes pour la même ressource jusqu'à ce que les en-têtes de réponse HTTP arrivent du serveur d’origine. Si les en-têtes de réponse HTTP du serveur d’origine arrivent avant l’expiration du délai, toutes les requêtes mises en attente sont sorties de la mémoire cache croissante (en même temps qu’elle est rempli par les données de l’origine). Le temps d’attente de remplissage du cache est défini sur 3 000 millisecondes, par défaut. 


