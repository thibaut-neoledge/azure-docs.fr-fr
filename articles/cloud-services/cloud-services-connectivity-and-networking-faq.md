---
title: "Questions fréquentes (FAQ) concernant les problèmes de connectivité et de mise en réseau pour Microsoft Azure Cloud Services | Microsoft Docs"
description: "Cet article répertorie les questions fréquentes sur la connectivité et la mise en réseau pour Microsoft Azure Cloud Services."
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
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: fr-fr
ms.lasthandoff: 06/15/2017


---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problèmes de connectivité et de mise en réseau pour Azure Cloud Services : questions fréquentes (FAQ)

Cet article comprend des questions fréquentes sur les problèmes de connectivité et de mise en réseau pour [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Vous pouvez également consulter la page [Taille de services cloud](cloud-services-sizes-specs.md) pour obtenir des informations sur la taille.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Impossible de réserver une adresse IP dans un service cloud à plusieurs adresses IP virtuelles
Tout d’abord, vérifiez que l’instance de machine virtuelle pour laquelle vous tentez de réserver l’adresse IP est activée. Ensuite, veillez à utiliser des adresses IP réservées aussi bien pour les déploiements intermédiaires que pour les déploiements de production. **Ne** modifiez pas les paramètres pendant la mise à niveau du déploiement.

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>Comment établir un Bureau à distance lorsque je possède un groupe de sécurité réseau ?
Ajoutez des règles à un groupe de sécurité réseau qui autorisent le trafic sur les ports **3389** et **20000**.  Bureau à distance utilise le port **3389**.  Les instances de service cloud sont soumis à l’équilibrage de charge, donc vous ne pouvez pas contrôler directement à quelle instance vous vous connectez.  Les agents *RemoteForwarder* et *RemoteAccess* gèrent le trafic RDP, et permettent au client d’envoyer un cookie RDP et de spécifier une instance individuelle à laquelle se connecter.  Les agents *RemoteForwarder* et *RemoteAccess* exigent que ce port **20000** soit ouvert : il peut être bloqué si vous avez un groupe de sécurité réseau.

## <a name="can-i-ping-a-cloud-service"></a>Est-il possible d’effectuer un test ping sur un service cloud ?

Non, pas en utilisant le test « ping » normal/protocole ICMP. Le protocole ICMP n’est pas autorisé via l’équilibreur de charge Azure.

Pour tester la connectivité, nous vous recommandons d’effectuer un test ping au niveau du port. Si Ping.exe utilise ICMP, d’autres outils tels que PSPing, Nmap et telnet permettent de tester la connectivité sur un port TCP spécifique.

Pour plus d’informations, consultez [Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (Tester la connectivité des machines virtuelles Azure à l’aide de tests ping au niveau du port à la place d’ICMP).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>Comment éviter de recevoir des milliers d’accès en provenance d’adresses IP inconnues qui portent à croire que le service cloud est la cible d’une sorte d’attaque malveillante ?
Azure met en œuvre une sécurité réseau multicouche pour protéger ses services de plateforme contre les attaques par déni de service distribué (DDoS). Le système de défense Azure DDoS fait partie intégrante du processus de surveillance continu d’Azure, qui est constamment amélioré à travers des tests de pénétration. Ce système de défense DDoS est conçu non seulement pour résister aux attaques extérieures, mais aussi à celles perpétrées par d’autres locataires Azure. Pour plus d’informations, consultez [Sécurité réseau Microsoft Azure](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Vous pouvez aussi créer une tâche de démarrage pour bloquer de manière sélective certaines adresses IP spécifiques. Pour plus d’informations, consultez [Bloquer une adresse IP spécifique](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Quand j’essaie de me connecter à mon instance de service cloud via RDP, j’obtiens le message, « Le compte d’utilisateur a expiré ».
Vous pouvez obtenir le message d’erreur « Ce compte d’utilisateur a expiré » quand vous ne tenez pas compte de la date d’expiration configurée dans vos paramètres RDP. Vous pouvez modifier la date d’expiration à partir du portail en procédant comme suit :
1. Connectez-vous à la console de gestion Azure (https://manage.windowsazure.com), accédez à votre service cloud, puis sélectionnez l’onglet **Configurer**.
2. Sélectionnez **Accès distant**.
3. Modifiez la date dans le champ « Expire le », puis enregistrez la configuration.

Vous devriez maintenant pouvoir vous connecter à votre machine via RDP.

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>Pourquoi le service LoadBalancer n’équilibre-t-il pas équitablement le trafic ?
Pour plus d’informations sur le fonctionnement interne de l’équilibreur de charge, consultez [Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Nouveau mode de distribution d’Azure Load Balancer).

L’algorithme de distribution utilisé est un hachage à 5 tuples (IP source, port source, IP de destination, port de destination, type de protocole) pour mapper le trafic aux serveurs disponibles. Il fournit l’adhérence uniquement dans une session de transport. Les paquets de la même session TCP ou UDP sont dirigés vers la même instance IP (DIP) de centre de données derrière le point de terminaison d’équilibrage de charge. Lorsque le client ferme et rouvre la connexion ou démarre une nouvelle session à partir du même IP source, le port source change et contraint le trafic à se diriger vers un autre point de terminaison DIP.

