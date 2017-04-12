---
title: "Liste des ports et URL devant figurer dans une liste blanche lors du déploiement d’Azure RemoteApp dans un réseau virtuel client | Microsoft Docs"
description: "Identifiez les ports et URL que vous devez configurer pour prendre en charge la communication par le biais d’Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: mghosh1616
manager: mbaldwin
ms.assetid: 5a001ff7-14c9-47fa-9b39-78fd5a5f0250
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9390af174262e0dd2bb5beb30ae08b3063c1a5e6
ms.lasthandoff: 03/31/2017


---
# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Liste des ports et URL devant être accessibles à Azure RemoteApp en cas de déploiement dans un réseau virtuel client
> [!IMPORTANT]
> Azure RemoteApp ne sera plus disponible à partir du 31 août 2017. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Si vous déployez une collection cloud ou hybride Azure RemoteApp dans un réseau virtuel (VNET), passez en revue les informations suivantes sur les ports. Pour plus d’informations sur les réseaux virtuels, consultez [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md). Si vous avez créé un groupe de sécurité réseau qui limite le trafic aux ressources du réseau virtuel dans votre collection, vérifiez que les ports suivants sont accessibles et autorisés par le biais des stratégies de sécurité sur le réseau virtuel. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Qu’est qu’un groupe de sécurité réseau ?](../virtual-network/virtual-networks-nsg.md).

## <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Points de terminaison et URL devant être accessibles à un sous-réseau Azure RemoteApp :
* *.servicebus.windows.net
* *.servicebus.net
* https://*.remoteapp.windowsazure.com  
* https://www.remoteapp.windowsazure.com 
* https://*remoteapp.windowsazure.com  
* https://*.core.windows.net  
* Sortant : TCP : TCP : 443, 9351, 9352, 10101-10175 
* Facultatif – UDP : 10201-10275  

## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Points de terminaison et URL devant être accessibles aux clients Azure RemoteApp :
J’entends par clients les bureaux, appareils, etc. que les personnes utilisent pour se connecter aux applications déployées dans la collection Azure RemoteApp.

* https://telemetry.remoteapp.windowsazure.com  
* https://*.remoteapp.windowsazure.com (les ports UDP facultatifs sont destinés à cette adresse) 
* https://login.windows.net  
* https://login.microsoftonline.com  
* https://www.remoteapp.windowsazure.com 
* https://*.core.windows.net  
* Sortant : TCP : 443  
* Facultatif - UDP : 3391 


