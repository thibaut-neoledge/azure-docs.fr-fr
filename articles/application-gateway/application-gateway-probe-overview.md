---
title: "Présentation de l’analyse du fonctionnement pour Azure Application Gateway | Microsoft Docs"
description: "En savoir plus sur les capacités d’analyse dans Azure Application Gateway"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7eeba328-bb2d-4d3e-bdac-7552e7900b7f
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 899115d213e626f17e58c2e5f01313f760f9e7f4
ms.lasthandoff: 03/27/2017


---

# <a name="application-gateway-health-monitoring-overview"></a>Vue d’ensemble de l’analyse d’intégrité Application Gateway

Azure Application Gateway analyse par défaut l’intégrité de toutes les ressources de son pool principal et supprime automatiquement du pool les ressources considérées comme défectueuses. Application Gateway continue de surveiller les instances défaillantes et les réintroduit dans le pool principal intègre une fois qu’elles redeviennent disponibles et répondent aux sondes d’intégrité. La passerelle d’application envoie les sondes d’intégrité avec le même port que celui défini dans les paramètres HTTP du serveur principal. Cette configuration garantit que la sonde teste le même port que celui qu’utiliseraient les clients pour se connecter au serveur principal.

![exemple de sonde application gateway][1]

En plus d’utiliser la surveillance par sonde d’intégrité par défaut, vous pouvez aussi personnaliser la sonde d’intégrité pour répondre aux exigences de votre application. Dans cet article, nous nous intéressons aux sondes d’intégrité par défaut et personnalisées.

> [!NOTE]
> Si le sous-réseau Application Gateway comporte un groupe de sécurité réseau, les plages de ports 65503-65534 doivent être ouvertes sur ce sous-réseau pour permettre l’arrivée du trafic entrant. Ces ports sont requis pour permettre à l’API relative à l’intégrité du serveur principal de fonctionner correctement.

## <a name="default-health-probe"></a>Sonde d’intégrité par défaut

Une passerelle d’application configure automatiquement une sonde d’intégrité par défaut lorsque vous ne définissez pas de configuration de sonde personnalisée. Le comportement d’analyse par défaut consiste à lancer une requête HTTP aux adresses IP configurées pour le pool principal. En ce qui concerne les sondes par défaut, si les paramètres HTTP du serveur principal sont configurés pour HTTPS, la sonde utilise également HTTPS pour tester l’intégrité des serveurs principaux.

Par exemple : vous configurez votre passerelle d’application pour utiliser les serveurs principaux A, B et C, qui recevront le trafic réseau HTTP sur le port 80. Les contrôles de défaillance par défaut testent les trois serveurs toutes les 30 secondes pour obtenir une réponse HTTP correcte. Le [code d’état](https://msdn.microsoft.com/library/aa287675.aspx) d’une réponse HTTP correcte est compris entre 200 et 399.

Si l’analyse de la sonde par défaut échoue pour le serveur A, la passerelle d’application le retire de son pool principal et le trafic réseau cesse de passer par ce serveur. La sonde par défaut continue de contrôler le serveur A toutes les 30 secondes. Dès que le serveur A répond avec succès à une requête de la sonde d’intégrité par défaut, il est réintroduit dans le pool principal en tant que serveur intègre et le trafic vers celui-ci reprend.

### <a name="default-health-probe-settings"></a>Paramètres de sonde d’intégrité par défaut

| Propriétés de la sonde | Valeur | Description |
| --- | --- | --- |
| URL de sonde |http://127.0.0.1:\<port\>/ |Chemin d'accès de l'URL |
| Intervalle |30 |Intervalle d’analyse en secondes |
| Délai d’attente |30 |Délai d’expiration de l’analyse en secondes |
| Seuil de défaillance sur le plan de l’intégrité |3 |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

> [!NOTE]
> Le port est le même que celui utilisé par les paramètres HTTP du serveur principal.

La sonde par défaut examine uniquement http://127.0.0.1:\<port\> pour déterminer l’état d’intégrité. Si vous devez configurer la sonde d’intégrité de sorte qu’elle accède à une URL personnalisée ou modifier d’autres paramètres, vous devez utiliser des sondes personnalisées comme décrit dans les étapes suivantes :

## <a name="custom-health-probe"></a>Sonde d’intégrité personnalisée

Les sondes personnalisées vous permettent d’avoir un contrôle plus précis de l’analyse de l’intégrité. En utilisant des sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL et le chemin à tester et le nombre de réponses en échec autorisé avant que l’instance de pool principal soit marquée comme étant défectueuse.

### <a name="custom-health-probe-settings"></a>Paramètres de sonde d’intégrité personnalisée

Le tableau suivant fournit des définitions pour les propriétés d’une sonde d’intégrité personnalisée.

| Propriétés de la sonde | Description |
| --- | --- |
| Name |Nom de la sonde. Ce nom est utilisé pour désigner la sonde dans les paramètres HTTP du serveur principal. |
| Protocole |Protocole utilisé pour envoyer la sonde. La sonde utilise le protocole défini dans les paramètres HTTP du serveur principal |
| Host |Nom d’hôte pour l’envoi de la sonde. S’applique uniquement lorsque plusieurs sites sont configurés sur Application Gateway, sinon utilisez '127.0.0.1'. Cette valeur est différente du nom d’hôte de la machine virtuelle. |
| Chemin |Chemin relatif de la sonde. Le chemin valide commence par « / ». |
| Intervalle |Intervalle d’analyse en secondes. Cette valeur est l’intervalle de temps qui s’écoule entre deux analyses consécutives. |
| Délai d’attente |Délai d’expiration de l’analyse en secondes. Si aucune réponse valide n’est reçue dans le délai imparti, la sonde est marquée comme étant en échec.  |
| Seuil de défaillance sur le plan de l’intégrité |Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

> [!IMPORTANT]
> Si Application Gateway est configuré pour un seul site, le nom d’hôte par défaut doit être spécifié sous la forme « 127.0.0.1 », sauf s’il est configuré d’une autre manière dans la sonde personnalisée.
> Pour référence, une sonde personnalisée est envoyée à \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\>. Le port utilisé est identique à celui défini dans les paramètres HTTP du serveur principal.

## <a name="next-steps"></a>Étapes suivantes
Après vous être familiarisé avec l’analyse d’intégrité Application Gateway, vous pouvez configurer une [sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md) dans le portail Azure ou une [sonde d’intégrité personnalisée](application-gateway-create-probe-ps.md) à l’aide de PowerShell et du modèle de déploiement Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

