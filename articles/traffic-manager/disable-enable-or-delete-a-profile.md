---
title: "Désactiver, activer ou supprimer un profil Traffic Manager | Microsoft Docs"
description: "Cet article vous aide à utiliser vos profils Traffic Manager."
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: f19143e2-2f4f-4ead-bb00-89b95ac92cb4
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3ea5860767cd21d263c0f0126362d3f6d92d58c6


---
<!-- repub for nofollow -->

# <a name="disable-enable-or-delete-a-profile"></a>Désactiver, activer ou supprimer un profil
Vous pouvez désactiver un profil Traffic Manager existant afin qu’il ne renvoie pas les demandes utilisateur vers ses points de terminaison configurés. Lorsque vous désactivez un profil Traffic Manager, le profil et les informations qu’il contient demeurent intacts et peuvent être modifiés via l’interface de Traffic Manager. Vous pouvez réactiver le profil facilement via le portail Azure. Dans ce cas, les références seront rétablies. Lorsque vous créez un profil Traffic Manager dans le portail Azure, il est automatiquement activé.

## <a name="to-disable-a-profile"></a>Désactivation d’un profil
1. Modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin d’utiliser le type d’enregistrement approprié et un pointeur adéquat vers un autre nom ou vers l’adresse IP d’un emplacement spécifique sur Internet. En d’autres termes, modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin qu’il n’utilise plus un enregistrement de ressource CNAME pointant vers le nom de domaine de votre profil Traffic Manager.
2. Le trafic ne sera plus dirigé vers les points de terminaison via les paramètres du profil Traffic Manager.
3. Sélectionnez le profil que vous souhaitez désactiver. Sélectionnez le profil dans la page Traffic Manager en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
4. Après avoir sélectionné le profil, cliquez sur Désactiver en bas de la page.

## <a name="to-enable-a-profile"></a>Activation d’un profil
1. Sélectionnez le profil que vous souhaitez activer. Sélectionnez le profil dans la page Traffic Manager en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
2. Après avoir sélectionné le profil, cliquez sur Activer en bas de la page.
3. Modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin d’utiliser le type d’enregistrement CNAME, qui mappe votre nom de domaine d’entreprise au nom de domaine de votre profil Traffic Manager. Pour plus d’informations, consultez [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).
4. Le trafic est de nouveau dirigé vers les points de terminaison.

## <a name="delete-a-profile"></a>Supprimer un profil
1. Assurez-vous que l’enregistrement de ressource DNS sur votre serveur DNS Internet n’utilise plus un enregistrement de ressource CNAME pointant vers le nom de domaine de votre profil Traffic Manager.
2. Sélectionnez le profil que vous souhaitez supprimer. Pour sélectionner le profil, dans la page Traffic Manager, mettez-le en surbrillance en
3. cliquant sur la colonne en regard de celui-ci. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
4. Après avoir sélectionné le profil, cliquez sur Supprimer en bas de la page.

## <a name="next-steps"></a>Étapes suivantes
[Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

[Configurer la méthode de routage par basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage du trafic en tourniquet (round robin)](traffic-manager-configure-round-robin-routing-method.md)

[Configurer la méthode de routage basé sur les performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO3-->


