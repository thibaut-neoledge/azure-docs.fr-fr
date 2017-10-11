---
title: Prise en charge de HTTP/2 dans Azure CDN | Microsoft Docs
description: En savoir plus sur la prise en charge de HTTP/2 et de CDN.
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: 4f8dd685c3ae89535217d7a17a01c5129ca7e6e4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="http2-support-in-azure-cdn"></a>Prise en charge de HTTP/2 dans Azure CDN

HTTP/2 est une révision majeure au HTTP/1.1\. Il fournit plus rapide d’expérience utilisateur améliorée, temps de réponse réduit et performances de site web, tout en conservant les méthodes HTTP familières, les codes d’état et sémantique. Bien que HTTP/2 soit conçu pour fonctionner avec HTTP et HTTPS, de nombreux navigateurs web clients prennent en charge seulement HTTP/2 sur TLS.

###<a name="http2-benefits"></a>Avantages de HTTP/2

Les avantages de HTTP/2 sont les suivants :

*   **Multiplexage et accès concurrentiel**

    Avec HTTP 1.1, effectuer plusieurs demandes de ressources nécessite plusieurs connexions TCP, et chaque connexion représente une surcharge qui a un impact sur les performances. HTTP/2 permet de demander plusieurs ressources sur une même connexion TCP.

*   **Compression des en-têtes**

    Grâce à la compression des en-têtes HTTP pour les ressources traitées, le temps passé sur le réseau est sensiblement réduit.

*   **Dépendances de flux**

    Les dépendances de flux permettent au client indiquer au serveur quelles ressources ont la priorité.


##<a name="http2-browser-support"></a>Prise en charge de HTTP/2 par les navigateurs

Tous les principaux navigateurs ont implémenté la prise en charge de HTTP/2 dans leur version actuelle. Les navigateurs non pris en charge passent automatiquement à HTTP/1.1.

|Browser|Version minimale|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

##<a name="enabling-http2-support-in-azure-cdn"></a>Activation de la prise en charge de HTTP/2 dans Azure CDN

La prise en charge HTTP/2 est actuellement active pour les profils **Azure CDN d’Akamai** et **Azure CDN de Verizon**. Aucune action supplémentaire n’est nécessaire de la part des clients.

##<a name="next-steps"></a>Étapes suivantes

Pour voir les avantages de HTTP/2 en situation, consultez [cette démonstration d’Akamai](https://http2.akamai.com/demo).

Pour en savoir plus sur les HTTP/2, consultez les ressources suivantes :

*   [Page d’accueil de la spécification HTTP/2](https://http2.github.io/)
*   [Forum Aux Questions officiel de HTTP/2](https://http2.github.io/faq/)
*   [Informations sur HTTP/2 d’Akamai](https://http2.akamai.com/)

Pour plus d’informations sur les fonctionnalités disponibles dans Azure CDN, consultez [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](https://azure.microsoft.com/documentation/articles/cdn-overview/).