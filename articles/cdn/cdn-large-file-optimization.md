---
title: "Optimisation via Azure CDN de téléchargement de fichiers volumineux"
description: "Optimisation de téléchargements approfondis de fichiers volumineux"
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
ms.openlocfilehash: 27e202b05f86eeee7071f3fae145caeba3d66827
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017

---
# <a name="large-file-download-optimization-via-azure-cdn"></a>Optimisation via Azure CDN de téléchargement de fichiers volumineux

Les tailles des fichiers de contenu fournis via internet ont progressivement augmenté en raison de l’amélioration des fonctionnalités, des graphiques et d’un contenu multimédia riche. De nombreux facteurs en sont la cause, notamment la pénétration des bandes passantes, des périphériques de stockage économiques supérieurs, la prolifération de vidéos haute définition, des périphériques connectés à internet (IoT), etc.  Fournir un mécanisme de remise plus rapide et efficace de ces fichiers volumineux est essentiel pour garantir une expérience du consommateur continue et plus agréable. 

Il existe plusieurs défis inhérents à la distribution de fichiers volumineux. Tout d’abord, le temps moyen de téléchargement d’un fichier volumineux peut être important, de nombreuses applications ne peuvent pas télécharger toutes les données de manière séquentielle. Dans certains cas, les applications peuvent télécharger la dernière partie d’un fichier avant la première. Par conséquent, lorsqu’une petite partie d’un fichier est requise ou lorsqu’un utilisateur interrompt un téléchargement, cela peut entraîner l’échec ou le retardement du téléchargement jusqu'à ce que l’intégralité du fichier soit récupérée à partir de l’origine par le CDN. 

Ensuite, avec la prolifération des fichiers volumineux sur Internet, les utilisateurs observent souvent que la latence entre l’utilisateur et le fichier dicte finalement le débit ou la vitesse d’affichage du contenu. En outre, des problèmes de capacité et de congestion du réseau impactent le débit et ces problèmes, associés à la distance supérieure entre serveur et l’utilisateur final, créent des risques supplémentaires de perte de paquets, ce qui réduit encore la qualité. La réduction de la qualité causée par le débit limité et une perte de paquets accrue peut se manifester en un délai d’attente considérablement accru jusqu’à la fin du téléchargement. 

Enfin, de nombreux fichiers volumineux ne sont pas livrés dans leur intégralité. Les utilisateurs peuvent annuler un téléchargement en plein milieu ou ne regarder que les premières minutes d’une longue vidéo MP4. Par conséquent, il est utile pour de nombreuses sociétés de distribution de logiciels et de supports de fournir uniquement la partie d’un fichier demandée par l’utilisateur final. De cette manière, seules les parties demandées seront distribuées efficacement aux points les plus reculés d’Internet, réduisant ainsi le trafic de sortie à partir de l’origine et par conséquent, la pression de la mémoire et des e/s sur le serveur d’origine. 

Azure CDN d’Akamai propose désormais une fonctionnalité adaptée à la distribution efficace de fichiers volumineux aux utilisateurs finaux dans le monde entier à des latences à l’échelle et réduite tout en diminuant la charge sur les serveurs d’origine. Cette fonctionnalité est disponible via la fonctionnalité « Optimisé pour » sur le point de terminaison Azure CDN créé sous un profil Azure CDN avec un niveau de tarification « Akamai Standard ».

## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configuration de point de terminaison CDN pour optimiser la distribution de fichiers volumineux

Vous pouvez configurer votre point de terminaison CDN pour optimiser la distribution des fichiers volumineux via le portail Azure en sélectionnant simplement l’option « Téléchargement de fichier volumineux » en sélectionnant la propriété « Optimisé pour » lors de la création du point de terminaison. Pour ce faire, vous pouvez également utiliser nos API REST ou un des kits de développement logiciel client. Les captures d’écran ci-dessous illustrent le processus via le portail Azure.

![Nouveau point de terminaison CDN](./media/cdn-large-file-optimization/01_Adding.png)  
 
*Figure 1 : ajout d’un nouveau point de terminaison CDN à partir du profil CDN*
 
![LFO sélectionné](./media/cdn-large-file-optimization/02_Creating.png)

*Figure 2 : création d’un point de terminaison CDN avec l’optimisation du téléchargement de fichiers volumineux sélectionnée*

Une fois le point de terminaison CDN créé, les optimisations de fichiers volumineux seront appliquées pour tous les fichiers qui correspondent à certains critères. La section suivante décrit cela en détail.

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-akamai"></a>Optimisation pour la distribution de fichiers volumineux avec Azure CDN d’Akamai

Pour Azure CDN d’Akamai, vous pouvez utiliser la fonctionnalité de type d’optimisation de fichiers volumineux pour activer les optimisations et les configurations de réseaux qui rendent la distribution de fichiers volumineux plus rapide et plus réactive. La distribution générale sur le Web avec Akamai ne peut que mettre en cache des fichiers en-dessous de 1,8 Go et peut tunneler (pas mettre en cache) les fichiers jusqu'à 150 Go, tandis que l’optimisation de fichiers volumineux permet la mise en cache de fichiers jusqu'à 150 Go.

L’optimisation de fichiers volumineux est efficace lorsque certaines conditions sont remplies en ce qui concerne la façon dont le serveur d’origine fonctionne, les types de fichiers requis et la taille des fichiers demandés. Avant d’entrer dans les détails pour chacune d’entre elles, il est important de comprendre dans son ensemble le fonctionnement de l’optimisation. 

### <a name="object-chunking"></a>Segmentation des objets 

Azure CDN d’Akamai utilise une technique appelée segmentation d’objets où le CDN récupère des éléments plus petits du fichier à partir de l’origine lorsqu’un fichier volumineux est demandé. Lorsque le serveur Edge / POP CDN reçoit une demande de fichiers de plage d’octets ou entier d’un utilisateur final, il vérifie tout d’abord si le type de fichier appartient à la liste des types de fichiers pris en charge pour cette optimisation et s’il répond aux exigences de tailles de fichier. Si la taille du fichier est supérieure à 10 Mo, le serveur Edge CDN démarre la demande du fichier à partir du serveur d’origine par segments de 2 Mo. Une fois que le segment arrive à la périphérie CDN, il est mis en cache et est immédiatement livré à l’utilisateur final, tandis que le CDN procède à une lecture anticipée du segment suivant en parallèle. Cette « lecture anticipée » garantit que le contenu est disponible plus tôt en laissant un segment devant l’utilisateur tout en réduisant la latence pour l’utilisateur final. Ce processus se poursuit jusqu'à ce que l’intégralité du fichier soit téléchargé (si l’utilisateur final a demandé l’intégralité du fichier), jusqu'à ce que toutes les plages d’octets demandées soient disponibles (si l’utilisateur final a demandé des plages d’octets) ou le client quitte la connexion. 

Vous trouverez les détails de la demande de plage d’octets dans le [RFC 7233](https://tools.ietf.org/html/rfc7233).

Le CDN met en cache les segments lorsqu’ils sont reçus et ne demande pas que l’intégralité du fichier soit mise en cache sur le cache CDN. Les demandes suivantes pour les plages d’octets ou de fichiers seront traitées par le cache CDN et utiliseront une « lecture anticipée » pour demander des segments à partir de l’origine si tous les segments ne sont pas mis en cache sur le CDN. Comme vous pouvez le constater, cette optimisation s’appuie sur le serveur d’origine qui prend en charge les demandes de plages d’octets. _Si le serveur d’origine ne prend pas en charge les demandes de plages d’octets, cette optimisation ne sera pas effective._ 

### <a name="caching"></a>Mise en cache
Les fichiers volumineux utilisent des délais d’expiration de mise en cache par défaut différent pour la distribution générale. Il établit la distinction entre la mise en cache positive et négative basée sur les codes de réponse HTTP. Si l’origine spécifie une heure à l’aide d’un délai d’expiration via l’en-tête Cache-Control ou Expires dans la réponse, le CDN respecte toujours cette valeur. Lorsque l’origine ne spécifie pas l’origine et que le fichier correspond à la liste des conditions de types de fichiers et de tailles de fichiers pour ce type d’optimisation, le CDN utilise les valeurs par défaut pour l’optimisation des fichiers volumineux. Sinon, le CDN utilise par défaut pour la distribution générale sur le Web.

 
|    | Web générale | Optimisation des fichiers volumineux 
--- | --- | --- 
Mise en cache - positive <br> HTTP 200, 203, 300, <br> 301, 302 et 410 | 7 jours |1 jour  
Mise en cache - négative <br> HTTP 204, 305, 404, <br> et 405 | Aucun | 1 seconde 

### <a name="dealing-with-origin-failure"></a>Traitement des défaillances d’origine

Dans le type d’optimisation de fichiers volumineux, la longueur du délai d’attente de lecture d’origine est augmentée de 2 secondes dans la distribution générale sur le Web à 2 minutes pour prendre en compte les tailles de fichiers supérieures pour que la connexion n’expire pas prématurément.

Comme pour la distribution générale sur le Web, lorsqu’une connexion expire, nous allons réessayer un certain nombre de fois avant l’envoi d’une erreur de délai d’attente de la passerelle 504 au client. 

### <a name="conditions-for-large-file-optimization"></a>Conditions d’optimisation des fichiers volumineux

Le tableau suivant répertorie l’ensemble de critères à satisfaire pour l’optimisation des fichiers volumineux :

Condition | Valeurs 
--- | --- 
Types de fichiers pris en charge | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Taille minimale du fichier | 10 Mo 
Taille maximale du fichier | 150 Go 
Caractéristiques du serveur d’origine | Doit prendre en charge des demandes de plages d’octets 

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimisation pour la distribution de fichiers volumineux avec Azure CDN de Verizon

Azure CDN de Verizon est capable de distribuer des fichiers volumineux sans limite de la taille des fichiers et a différentes fonctionnalités qui activent la distribution de fichiers volumineux plus rapidement par défaut.

### <a name="complete-cache-fill"></a>Remplissage de cache complet

Azure CDN de Verizon dispose d’une fonctionnalité par défaut appelée Remplissage de cache complet dans laquelle le CDN extrait un fichier dans le cache lorsque la demande initiale est abandonnée ou perdue. 

Cette fonctionnalité est particulièrement utile pour les ressources volumineuses que les utilisateurs ne téléchargent pas généralement du début à la fin (par exemple, le téléchargement progressif des vidéos). Par conséquent, cette fonctionnalité est activée par défaut avec Azure CDN de Verizon. Le comportement par défaut consiste à forcer le serveur Edge à lancer une récupération en arrière-plan de la ressource à partir du serveur d’origine. Après quoi, la ressource se trouvera dans le cache local du serveur Edge. Une fois l’objet complet dans le cache, la périphérie peut répondre aux demandes de plages d’octets dans le CDN pour l’objet mis en cache.

Le comportement de remplissage de cache complet par défaut peut être désactivé via le moteur de règles dans le niveau premium de Verizon.

### <a name="peer-cache-fill-hotfiling"></a>Remplissage à chaud du cache homologue

Il s’agit d’une fonctionnalité par défaut de Azure CDN de Verizon, dans laquelle un algorithme propriétaire sophistiqué peut tirer parti de serveurs Edge de mise en cache basés sur des mesures telles que la bande passante et des requêtes d’agrégation pour répondre aux demandes du client pour des objets volumineux, très populaires. Cela empêche une situation dans laquelle un grand nombre de demandes supplémentaires seraient envoyées au serveur d’origine d’un client. 

### <a name="conditions-for-large-file-optimization"></a>Conditions d’optimisation des fichiers volumineux

Les fonctionnalités d’optimisation pour Verizon sont activées par défaut et il n’existe aucune limite de taille de fichier maximale. 

## <a name="additional-considerations"></a>Considérations supplémentaires

Il existe quelques aspects supplémentaires à prendre en compte lors de l’utilisation de ce type d’optimisation.
 
### <a name="azure-cdn-from-akamai"></a>Azure CDN d’Akamai

- Le processus de segmentation aboutit à des requêtes supplémentaires pour le serveur d’origine, mais le volume total de données transmises à partir de l’origine sera beaucoup plus petit, puisque la segmentation entraîne de meilleures caractéristiques de mise en cache sur le CDN.
- Il y aura également un avantage supplémentaire de réduction de la pression de la mémoire et des e/s à l’origine en raison de la distribution d’éléments plus petits du fichier. 
- Pour les segments mis en cache dans le CDN, il n’y aura pas d’autres demandes à l’origine jusqu'à ce que le contenu expire à partir du cache ou soit supprimé du cache pour d’autres raisons. 
- L’utilisateur peut effectuer des demandes de plages pour le CDN et elles seront traités simplement comme tout fichier normal. L’optimisation s’applique uniquement s’il s’agit d’un type de fichier valide et que la plage d’octets est entre 10 Mo et 150 Go. Si la taille moyenne de fichier demandée est inférieure à 10 Mo, vous pouvez souhaiter utiliser la distribution générale sur le Web à la place.

### <a name="azure-cdn-from-verizon"></a>Azure CDN de Verizon

L’optimisation de la distribution générale sur le Web peut distribuer des fichiers volumineux.

