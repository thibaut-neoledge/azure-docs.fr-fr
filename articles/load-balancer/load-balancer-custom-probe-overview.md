---
title: "Sondes personnalisées de l’équilibreur de charge et surveillance de l’état d’intégrité | Microsoft Docs"
description: "Découvrez comment utiliser les sondes personnalisées pour l’équilibreur de charge Azure afin de surveiller les instances situées derrière un équilibreur de charge"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: ce2550ca8301fd12d61cca143b2851b84f1a0f50
ms.openlocfilehash: 01afa3a08bbb56d4c6b8b18c5eb07f49706c6482

---

# <a name="understand-load-balancer-probes"></a>Comprendre les sondes de l’équilibrage de charge

L’équilibreur de charge Azure permet de surveiller l’intégrité des instances de serveur à l’aide de sondes. Quand une sonde ne répond pas, l’équilibreur de charge n’envoie plus de nouvelles connexions à l’instance défectueuse. Les connexions existantes ne sont pas affectées et les nouvelles connexions sont envoyées aux instances saines.

Les rôles de service cloud (rôles de travail et rôles Web) utilisent un agent invité pour la surveillance par sonde. Des sondes personnalisées TCP ou HTTP doivent être configurées quand vous utilisez des machines virtuelles derrière un équilibreur de charge.

## <a name="understand-probe-count-and-timeout"></a>Présentation du nombre et du délai d’expiration des sondes

Le comportement des sondes dépend des éléments suivants :

* Le nombre de sondes ayant réussi qui permet à une instance d’être étiquetée comme étant en cours d’exécution.
* Le nombre de sondes ayant échoué qui permet à une instance d’être étiquetée comme n’étant pas en cours d’exécution.

La valeur du délai d’expiration et de la fréquence définie dans SuccessFailCount détermine si une instance est confirmée comme étant en cours d’exécution ou pas. Dans le portail Azure, le délai d’expiration est défini sur deux fois la valeur de la fréquence.

La configuration de sonde de toutes les instances à charge équilibrée pour un point de terminaison (jeu d’équilibrage de la charge) doit être identique. Cela signifie que vous ne pouvez pas avoir de configuration de sonde différente pour chaque instance de rôle ou machine virtuelle du même service hébergé pour une combinaison de points de terminaison donnée. Par exemple, chaque instance doit avoir des délais d’expiration et des ports locaux identiques.

> [!IMPORTANT]
> Une sonde d’équilibreur de charge utilise une adresse IP 168.63.129.16. Cette adresse IP publique facilite la communication vers les ressources de plateforme internes pour le scénario « Apportez votre propre réseau virtuel IP Azure ». L’adresse IP publique virtuelle 168.63.129.16 est utilisée dans toutes les régions et ne change pas. Nous vous conseillons d’autoriser cette adresse IP dans toutes les stratégies de pare-feu local. Elle ne doit pas être considérée comme un risque de sécurité, car seule la plateforme Azure interne peut envoyer un message à partir de cette adresse. Si vous ne l’autorisez pas, un comportement inattendu peut se produire dans divers scénarios comme la configuration de la même plage d’adresses IP 168.63.129.16 et l’obtention de plusieurs adresses IP identiques.

## <a name="learn-about-the-types-of-probes"></a>En savoir plus sur les types de sondes

### <a name="guest-agent-probe"></a>Sonde d’agent invité

Cette sonde est disponible pour Azure Cloud Services uniquement. L’équilibreur de charge utilise l’agent invité de la machine virtuelle, puis écoute et répond avec une réponse HTTP 200 OK uniquement si l’instance est prête (c.-à-d., si l’état de l’instance n’est pas Occupé, Recyclage ou Arrêté).

Pour plus d’informations, voir [Configuring the service definition file (csdef) for health probes](https://msdn.microsoft.com/library/azure/ee758710.aspx) (configuration du fichier de définition de service (csdef) pour les sondes d’intégrité) ou [Création d’un équilibreur de charge accessible sur Internet pour les services cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Dans quelles conditions une sonde d’agent invité marque-t-elle une instance comme étant défectueuse ?

Si l’agent invité ne répond pas avec HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répondant pas et arrête d’envoyer du trafic vers celle-ci. L’équilibreur de charge continue d’effectuer un test ping sur l’instance. Si l’agent invité répond avec HTTP 200, l’équilibreur de charge renvoie du trafic vers cette instance.

Quand vous utilisez un rôle web, le code du site web s’exécute généralement dans w3wp.exe, qui n’est pas surveillé par l’agent de structure Azure ou l’agent invité. Cela signifie que les échecs dans w3wp.exe (par exemple, les réponses HTTP 500) ne sont pas signalés à l’agent invité et que l’équilibreur de charge ne sort pas cette instance de la rotation.

### <a name="http-custom-probe"></a>Sonde HTTP personnalisée

La sonde d’équilibreur de charge HTTP personnalisée remplace la sonde de l’agent invité par défaut et vous permet de créer votre propre logique personnalisée pour déterminer l’intégrité de l’instance de rôle. L’équilibreur de charge sonde régulièrement votre point de terminaison (toutes les 15 secondes, par défaut). L’instance est considérée comme faisant partie de la rotation de l’équilibreur de charge si elle répond avec HTTP 200 dans le délai imparti (31 secondes par défaut).

Cela peut être utile pour implémenter votre propre logique afin de supprimer des instances de la rotation de l’équilibreur de charge. Par exemple, vous pouvez décider de supprimer une instance si elle utilise plus de 90 % du processeur et retourne un état autre que 200. Si certains de vos rôles web utilisent w3wp.exe, cela signifie également que vous obtenez une analyse automatique de votre site web, dans la mesure où les défaillances dans votre code de site web renvoient un état autre que 200 à la sonde d’équilibreur de charge.

> [!NOTE]
> La sonde HTTP personnalisée prend en charge les chemins relatifs et le protocole HTTP uniquement. HTTPS n’est pas pris en charge.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Dans quelles conditions une sonde HTTP personnalisée marque-t-elle une instance comme étant défectueuse ?

* L’application HTTP retourne un code de réponse HTTP autre que 200 (par exemple, 403, 404 ou 500). Il s’agit d’une reconnaissance positive selon laquelle l’instance d’application doit être mise immédiatement hors service.
* Le serveur HTTP ne répond pas du tout après le délai d’expiration. Selon la valeur définie pour le délai d’expiration, cela peut signifier que plusieurs demandes d’analyse ne reçoivent pas de réponse avant que celle-ci ne soit marquée comme n’étant pas en cours d’exécution (autrement dit, avant que les sondes SuccessFailCount soient envoyées).
* Le serveur ferme la connexion via une réinitialisation TCP.

### <a name="tcp-custom-probe"></a>Sonde TCP personnalisée

Les sondes TCP établissent une connexion en effectuant une connexion en trois temps au port défini.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Dans quelles conditions une sonde TCP personnalisée marque-t-elle une instance comme étant défectueuse ?

* Le serveur TCP ne répond pas du tout à la fin du délai d’expiration. La sonde est marquée comme n’étant pas en cours d’exécution en fonction du nombre de demandes d’analyse ayant échoué, qui ont été configurées pour rester sans réponse avant que la sonde soit marquée comme n’étant pas en cours d’exécution.
* La sonde reçoit une réinitialisation TCP de l’instance de rôle.

Pour plus d’informations sur la configuration d’une sonde d’intégrité HTTP ou d’une sonde TCP, consultez [Création d’un équilibreur de charge accessible sur Internet dans Resource Manager à l’aide de PowerShell](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Rajouter des instances saines à la rotation d’équilibrage de charge

Les sondes TCP et HTTP sont considérées comme saines et marquent l’instance de rôle comme saine dans les cas suivants :

* L’équilibreur de charge reçoit une sonde positive au premier démarrage de la machine virtuelle.
* Le nombre SuccessFailCount (décrit précédemment) définit la valeur des sondes ayant réussi nécessaires pour marquer l’instance de rôle comme étant saine. Si une instance de rôle a été supprimée, le nombre de sondes ayant réussi successives doit être égal ou supérieur à la valeur de SuccessFailCount pour marquer l’instance de rôle comme étant en cours d’exécution.

> [!NOTE]
> Si l’intégrité d’une instance de rôle fluctue, l’équilibreur de charge attend plus longtemps avant de remettre l’instance de rôle dans un état d’intégrité normal. Cette opération est effectuée via une stratégie pour protéger l’utilisateur et l’infrastructure.

## <a name="use-log-analytics-for-load-balancer"></a>Utiliser l’analytique des journaux pour l’équilibreur de charge

Vous pouvez utiliser l’ [analytique des journaux pour l’équilibreur de charge](load-balancer-monitor-log.md) pour vérifier le nombre et l’état d’intégrité des sondes. La journalisation peut être utilisée avec Power BI ou Operational Insights pour fournir des statistiques sur l’état d’intégrité de l’équilibreur de charge.



<!--HONumber=Nov16_HO3-->


