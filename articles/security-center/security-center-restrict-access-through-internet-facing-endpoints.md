---
title: "Limiter l’accès par le biais d’un point de terminaison accessible sur Internet dans Azure Security Center | Microsoft Docs"
description: "Ce document vous montre comment implémenter la recommandation du Centre de sécurité Azure **Limiter l&quot;accès par le biais d&quot;un point de terminaison accessible sur Internet**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 6b7d2da8ababba65146503ecfbe6fd6e142a359c
ms.openlocfilehash: f7309c617f1705205e2c9f1b1b48d141391d45da


---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Restreindre l’accès via des points de terminaison accessibles sur Internet dans le Centre de sécurité Azure
Le Centre de sécurité Azure vous recommande de restreindre l’accès via les points de terminaison accessibles sur Internet si l’un de vos groupes de sécurité réseau (NSG) possède une ou plusieurs règles de trafic entrant autorisant l’accès à partir de « n’importe quelle » adresse IP source. Un accès « total » peut permettre aux pirates d’accéder à vos ressources. Le Centre de sécurité vous recommande de modifier ces règles de trafic entrant afin de restreindre l’accès aux adresses IP source qui en ont réellement besoin.

Cette recommandation est générée pour n’importe quel port non web avec une « quelconque » source.

> [!NOTE]
> Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.
>
>

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
1. Dans le panneau **Recommandations**, sélectionnez **Limiter l'accès par le biais d'un point de terminaison accessible sur Internet**.

   ![Restreindre l’accès via un point de terminaison accessible sur Internet][1]
2. Le panneau **Restreindre l’accès via un point de terminaison accessible sur Internet**s’ouvre. Ce panneau répertorie les machines virtuelles dont les règles de trafic entrant créent un problème de sécurité potentiel. Sélectionnez une machine virtuelle.

   ![Sélectionner une machine virtuelle][2]
3. Le panneau **NSG** affiche des informations sur le groupe de sécurité réseau (NSG), les règles de trafic entrant associées ainsi que la machine virtuelle associée. Sélectionnez **Modifier les règles de trafic entrant** pour poursuivre la modification d’une règle de trafic entrant.

   ![Panneau Groupe de sécurité réseau][3]
4. Dans le panneau **Règles de sécurité entrantes** , sélectionnez la règle de trafic entrant à modifier. Dans cet exemple, sélectionnons **AllowWeb**.

   ![Règles de sécurité de trafic entrant][4]

   Notez que vous pouvez également sélectionner **Règles par défaut** pour afficher l’ensemble de règles par défaut de tous les groupes de sécurité réseau. Les règles par défaut ne peuvent pas être supprimées, mais comme une priorité plus basse leur est attribuée, elles peuvent être remplacées par les règles que vous créez. En savoir plus sur les [règles par défaut](../virtual-network/virtual-networks-nsg.md#default-rules).

   ![Règles par défaut][5]
5. Dans le panneau **AllowWeb**, modifiez les propriétés de la règle de trafic entrant pour que la valeur **Source** affiche une adresse IP ou un bloc d’adresses IP. Pour en savoir plus sur les propriétés de la règle de trafic entrant, consultez [Règles de groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md#nsg-rules).

   ![Modifier une règle de trafic entrant][6]

## <a name="see-also"></a>Voir aussi
Cet article vous a montré comment implémenter la recommandation du Centre de sécurité Azure « Restreindre l’accès via un point de terminaison accessible sur Internet ». Pour plus d’informations sur l’activation de groupes de sécurité et de règles, consultez les rubriques suivantes :

* [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
* [Gestion des groupes de sécurité réseau à l’aide du portail Azure](../virtual-network/virtual-networks-create-nsg-arm-pportal.md)

Pour plus d’informations sur Security Center, consultez :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md): découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md): découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md): découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md): découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions de partenaires.
* [FAQ du Centre de sécurité Azure](security-center-faq.md): forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/): découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png



<!--HONumber=Feb17_HO1-->


