---
title: "En savoir plus sur la limitation dans BizTalk Services | Microsoft Docs"
description: "En savoir plus sur les seuils de limitation et les comportements qui s&quot;ensuivent lors de l&quot;exécution pour BizTalk Services. La limitation est basée sur l&quot;utilisation de la mémoire et le nombre de messages. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: e14f42497d5ee0c89fe1fa0824431e2d82e6555a


---
# <a name="biztalk-services-throttling"></a>Limitation BizTalk Services
Azure BizTalk Services met en place un service de limitation basé sur deux conditions : l’utilisation de la mémoire et le nombre de messages traités simultanément. Cette rubrique répertorie les seuils de limitation et décrit le comportement d’exécution lorsque les conditions de limitation sont réunies.

## <a name="throttling-thresholds"></a>Seuils de limitation
Le tableau suivant répertorie les sources et seuils de limitation :

|  | Description | Seuil minimum | Seuil maximum |
| --- | --- | --- | --- |
| Mémoire |Pourcentage de la mémoire système totale disponible/PageFileBytes (octets de fichier de page). <p><p>Le total de PageFileBytes disponible correspond environ à 2 fois la mémoire RAM du système. |60 % |70 % |
| Traitement de message |Nombre de messages traités simultanément |40 * nombre de cœurs |100 * nombre de cœurs |

Lorsqu'un seuil maximal est atteint, Azure BizTalk Services active les limitations. La limitation s'arrête une fois le seuil minimal atteint. Par exemple, votre service utilise 65 % de la mémoire système. Dans ce cas, le service n'utilise pas de limitation. Votre service monte à 70 % d'utilisation de mémoire système. Dans ce cas, le service lance une limitation et continue de l'appliquer jusqu'à ce que le service utilise 60 % (seuil minimal) de la mémoire système.

Azure BizTalk Services assure le suivi du statut de la limitation (état normal contre état limité) et de la durée de la limitation.

## <a name="runtime-behavior"></a>Comportement d'exécution
Lorsque Azure BizTalk Services passe en mode limitation, les actions suivantes se produisent :

* La limitation s'effectue par instance de rôle. Par exemple :<br/>
  L'instance RoleInstanceA est limitée. L'instance RoleInstanceB ne l'est pas. Dans ce cas, les messages dans RoleInstanceB sont traités normalement. Les messages dans RoleInstanceA sont ignorés et le message d'erreur suivant est affiché :<br/><br/>
  **Le serveur est occupé. Réessayez.**<br/><br/>
* Les sources d'extraction cessent d'interroger et de télécharger les messages. Par exemple : <br/>
   Un pipeline extrait les messages depuis une source FTP externe. L'instance de rôle responsable de l'extraction passe en mode limité. Dans cette situation, le pipeline cesse de télécharger de nouveaux messages tant que l'instance de rôle est limitée.
* Une réponse est envoyée au client afin qu'il puisse soumettre le message de nouveau.
* Vous devez patienter jusqu'à la fin de la limitation. Plus précisément, vous devez attendre que le seuil minimal soit atteint.

## <a name="important-notes"></a>Remarques importantes
* Il n'est pas possible de désactiver la limitation.
* Il n'est pas possible de modifier les seuils de limitation.
* La limitation est déployée sur l'ensemble du système.
* Le serveur de base de données SQL d'Azure comporte également un dispositif de limitation intégré.

## <a name="additional-azure-biztalk-services-topics"></a>Autres rubriques Azure BizTalk Services
* [Installation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Didacticiels : Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Voir aussi
* [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services : approvisionnement à l’aide du portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Sauvegarde et restauration de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Nom et clé de l'émetteur dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>




<!--HONumber=Nov16_HO3-->


