
<properties
    pageTitle="Liste des ports et URL devant figurer dans une liste blanche lors du déploiement d’Azure RemoteApp dans un réseau virtuel client | Microsoft Azure"
    description="Identifiez les ports et URL que vous devez configurer pour prendre en charge la communication par le biais d’Azure RemoteApp."
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/29/2016"
    ms.author="elizapo" />



# Liste des ports et URL devant être accessibles à Azure RemoteApp en cas de déploiement dans un réseau virtuel client 

Cet article s’applique à une collection cloud ou hybride Azure RemoteApp déployée dans un réseau virtuel. Pour plus d’informations sur les réseaux virtuels, consultez [Présentation des réseaux virtuels](virtual-networks-overview.md). Si vous avez créé un groupe de sécurité réseau qui limite le trafic aux ressources de votre réseau virtuel que vous avez choisies pour Azure RemoteApp, vérifiez que les éléments suivants sont accessibles et autorisés par le biais des stratégies de sécurité sur le réseau virtuel. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Qu’est qu’un groupe de sécurité réseau ?](virtual-networks-nsg.md).

##  Points de terminaison et URL devant être accessibles à un sous-réseau Azure RemoteApp : 
*	**.servicebus.windows.net
*	 **.servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 Sortant : TCP : 443, TCP : 10101-10175 
*	 Facultatif – UDP : 10201-10275  
 
## Points de terminaison et URL devant être accessibles aux clients Azure RemoteApp : 

J’entends par clients les bureaux, appareils, etc. que les personnes utilisent pour se connecter aux applications déployées dans la collection Azure RemoteApp.

-  https://telemetry.remoteapp.windowsazure.com  
-  https://*.remoteapp.windowsazure.com (les ports UDP facultatifs se rapportent à cette adresse) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://*.core.windows.net  
-  Sortant : TCP : 443  
-  Facultatif - UDP : 3391 

<!---HONumber=AcomDC_0204_2016-->