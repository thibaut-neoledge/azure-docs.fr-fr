---
title: "Configurer une méthode de routage du trafic basé sur les performances | Microsoft Docs"
description: "Cet article vous aide à configurer la méthode de routage du trafic basé sur les performances dans Traffic Manager."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 6dd23b8e-0ed5-4ea4-b5ae-018f42e72688
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 51a3f970059f4b83240cb61411dbf612209d9293


---
<!-- repub for nofollow -->

# <a name="configure-performance-traffic-routing-method"></a>Configurer une méthode de routage du trafic basé sur les performances
Pour router le trafic des services cloud et des sites Web (points de terminaison) qui se trouvent dans différents centres de données du monde entier (également appelés régions), vous pouvez diriger le trafic entrant vers le point de terminaison qui présente la plus faible latence du point de vue du client à l'origine de la demande. En règle générale, le centre de données avec la latence la plus faible est celui qui se trouve géographiquement le plus proche. La méthode de routage du trafic basé sur les performances permet une répartition en fonction de la latence, mais ne tient pas compte des modifications apportées en temps réel à la charge ou à la configuration réseau. Pour plus d’informations sur les différentes méthodes de routage du trafic fournies par Azure Traffic Manager, consultez la rubrique [À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-routing-methods.md).

## <a name="route-traffic-based-on-lowest-latency-across-a-set-of-endpoints"></a>Router le trafic en fonction de la latence la plus faible sur un ensemble de points de terminaison :
1. Dans le volet gauche du portail Azure Classic, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager. Si vous n’avez pas encore créé votre profil Traffic Manager, consultez [Gérer les profils Traffic Manager](traffic-manager-manage-profiles.md) pour connaître les étapes de création d’un profil Traffic Manager de base.
2. Dans le volet Traffic Manager du portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
3. Dans la page de votre profil, cliquez sur **Points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents. Pour savoir comment ajouter ou supprimer des points de terminaison dans votre profil, consultez [Gérer les points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
4. Dans la page de votre profil, cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour **Paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **Performance*. Si ce n’est pas le cas, cliquez sur **Performances** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Notez qu'une barre oblique « / » est une entrée valide pour le chemin d'accès relatif et qu'elle implique que le fichier se trouve dans le répertoire racine (par défaut). Pour plus d’informations sur la surveillance, consultez [À propos de la surveillance avec Traffic Manager](traffic-manager-monitoring.md).
7. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
8. Testez les modifications dans votre configuration. Pour plus d’informations, consultez [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
9. Une fois le profil Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d'entreprise vers le nom de domaine Traffic Manager. Pour plus d’informations sur la marche à suivre, consultez [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).

## <a name="next-steps"></a>Étapes suivantes
[Rediriger un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)

[Configurer la méthode de routage par basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage du trafic en tourniquet (round robin)](traffic-manager-configure-round-robin-routing-method.md)

[Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)

[Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)




<!--HONumber=Nov16_HO3-->


