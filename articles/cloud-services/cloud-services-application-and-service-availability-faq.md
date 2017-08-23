---
title: "Questions fréquentes (FAQ) concernant les problèmes de disponibilité des applications et des services pour Microsoft Azure Cloud Services | Microsoft Docs"
description: "Cet article répertorie les questions fréquentes sur la disponibilité des applications et des services pour Microsoft Azure Cloud Services."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 8aa00757ff11ed9086cd178e4c7190b93ee62701
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017


---
# <a name="application-and-service-availability-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problèmes de disponibilité des applications et des services pour Azure Cloud Services : questions fréquentes (FAQ)

Cet article comprend des questions fréquentes sur les problèmes de disponibilité des applications et des services pour [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="my-role-got-recycled-was-there-any-update-rolled-out-for-my-cloud-service"></a>Mon rôle a été recyclé. Mon service cloud a-t-il fait l’objet d’une mise à jour ?
Environ une fois par mois, Microsoft publie une nouvelle version du système d’exploitation (SE) invité pour les machines virtuelles PaaS Microsoft Azure. Le SE invité n’est qu’une mise à jour parmi d’autres. Une publication peut être affectée par beaucoup d’autres facteurs. Par ailleurs, Azure s’exécute sur des centaines de milliers de machines. Il est donc impossible de prédire la date et l’heure exactes auxquelles vos rôles seront redémarrés. Nous mettrons à jour le Flux RSS de mise à jour du SE invité avec les dernières informations dont nous disposons, mais considérez l’heure indiquée comme une valeur approximative. Nous sommes conscients que cela peut être problématique pour les clients. Nous tachons d’ailleurs de limiter ou minuter avec précision ces redémarrages.

Pour plus d’informations sur les dernières mises à jour du SE invité, consultez [Versions du SE invité et matrice de compatibilité du kit SDK Azure](cloud-services-guestos-update-matrix.md).

Pour obtenir des informations utiles sur les redémarrages et des liens vers d’autres détails techniques concernant les mises à jour des SE invité et hôte, consultez le billet de blog MSDN intitulé [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Redémarrage des instances de rôle en raison des mises à niveau du SE).

## <a name="why-does-the-first-request-to-my-cloud-service-after-the-service-has-been-idle-for-some-time-take-longer-than-usual"></a>Pourquoi la première requête à destination de mon service cloud prend-elle plus de temps que d’habitude après que le service est resté inactivité pendant un certain temps ?
Quand le serveur web reçoit la première requête, il commence par recompiler le code avant de traiter la requête. C’est pourquoi la première requête prend plus de temps que les autres. Par défaut, le pool d’applications s’arrête en cas d’inactivité de l’utilisateur. Par ailleurs, le pool d’applications est recyclé toutes les 1 740 minutes (29 heures).

Les pools d’applications IIS (Internet Information Services) peuvent être recyclées régulièrement pour éviter des états instables susceptibles d’entraîner des blocages d’applications, une absence de réponse ou des fuites de mémoire.

Les documents suivants vous aideront à comprendre et à limiter ce problème :
* [Fixing slow initial load for IIS](http://stackoverflow.com/questions/13386471/fixing-slow-initial-load-for-iis) (Remédier à la lenteur du chargement initial pour IIS)
* [IIS 7.5 web application first request after app-pool recycle very slow](http://stackoverflow.com/questions/13917205/iis-7-5-web-application-first-request-after-app-pool-recycle-very-slow) (Premier requête d’application web IIS 7.5 après une opération de recyclage de pool d’applications très lente)

Si vous voulez modifier le comportement par défaut d’IIS, vous devez avoir recours à des tâches de démarrage, car si vous appliquez manuellement des modifications aux instances de rôle web, les modifications seront perdues à la fin du processus.

Pour plus d’informations, consultez [Comment configurer et exécuter des tâches de démarrage pour un service cloud](cloud-services-startup-tasks.md).

