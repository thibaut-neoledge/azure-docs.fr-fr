---
title: "Configurer les méthodes de routage de Traffic Manager | Microsoft Docs"
description: "Cet article explique comment configurer différentes méthodes de routage dans Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: d4481115dbf8cc720019096969c55509f33d250c

---

# <a name="configure-traffic-manager-routing-methods"></a>Configurer les méthodes de routage de Traffic Manager

Azure Traffic Manager offre trois méthodes de routage qui contrôlent la façon dont le trafic est acheminé vers les points de terminaison de service disponibles. La méthode Routage du trafic est appliquée à chaque requête DNS reçue, afin de déterminer le point de terminaison à retourner dans la réponse DNS.

Trois méthodes de routage du trafic sont disponibles dans Traffic Manager :

* **Priorité :** sélectionnez « Priority (Priorité) » si vous souhaitez utiliser un point de terminaison de service principal et disposer de sauvegardes en cas d’indisponibilité de celui-ci.
* **Pondération :** sélectionnez « Weighted (Pondéré) » si vous souhaitez distribuer le trafic entrant sur un ensemble de points de terminaison, soit uniformément, soit en fonction du poids que vous définissez.
* **Performances :** sélectionnez « Performance » quand vos points de terminaison se trouvent sur des emplacements géographiques différents et que vous souhaitez que les utilisateurs finaux utilisent le point de terminaison « le plus proche » en termes de latence réseau la plus faible.

## <a name="configure-priority-routing-method"></a>Configurer la méthode de routage en fonction de la priorité

Indépendamment du mode de site web, Sites web Azure fournit des fonctionnalités basculement pour des sites web à l’intérieur d’un centre de données (également appelé région). Traffic Manager assure le basculement pour des sites web dans différents centres de données.

Dans le cadre du basculement des services, il est courant de diriger le trafic vers un service principal, et de définir un ensemble de services de sauvegarde identiques pour le basculement. Les étapes suivantes expliquent comment configurer ce basculement hiérarchisé avec Azure cloud services et des sites web :

1. Dans le volet gauche du portail Azure Classic, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager.
2. Dans le volet Traffic Manager du Portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres que vous souhaitez modifier. Pour ouvrir la page des paramètres du profil, cliquez sur la flèche à droite du nom de profil.
3. Dans la page de votre profil, cliquez en haut sur **Points de terminaison**. Vérifiez que les services cloud et les sites web que vous souhaitez inclure dans votre configuration sont présents.
4. Cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour les **paramètres de la méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **Basculement**. Si ce n’est pas le cas, cliquez sur **Basculement** dans la liste déroulante.
6. Pour **Liste prioritaire de basculement**, ajustez l’ordre de basculement de vos points de terminaison. Lorsque vous configurez la méthode de routage du trafic par **Basculement** , l'ordre des points de terminaison sélectionnés est important. Le point de terminaison principal doit se trouver en haut de la liste. Utilisez les flèches vers le haut et vers le bas pour modifier l'ordre si besoin. Pour plus d’informations sur la définition de la priorité de basculement à l’aide de Windows PowerShell, consultez [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Une barre oblique (« / ») est une entrée valide pour le chemin d’accès relatif. Elle implique que le fichier se trouve dans le répertoire racine (par défaut).
8. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
9. Testez les modifications dans votre configuration.
10. Une fois le profil Traffic Manager opérationnel, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d’entreprise vers le nom de domaine Traffic Manager.

## <a name="configure-weighted-routing-method"></a>Configurer la méthode de routage en fonction de la pondération

Il est courant d'utiliser un ensemble de points de terminaison identiques, comprenant des services cloud et des sites web, et de répartir le trafic en tourniquet. Les étapes suivantes décrivent comment configurer ce type de méthode de routage du trafic.

> [!NOTE]
> Azure Websites fournit déjà des fonctionnalités d’équilibrage de charge de tourniquet pour les sites web dans un centre de données (également appelé région). Traffic Manager vous permet de spécifier une méthode de routage du trafic en tourniquet (round robin) pour des sites web de différents centres de données.

1. Dans le volet gauche du portail Azure Classic, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager.
2. Dans le volet Traffic Manager, localisez le profil Traffic Manager contenant les paramètres que vous souhaitez modifier. Pour ouvrir la page des paramètres du profil, cliquez sur la flèche à droite du nom de profil.
3. Dans la page de votre profil, cliquez sur **Points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents.
4. Dans la page de votre profil, cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour **Paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **Tourniquet**. Si ce n’est pas le cas, cliquez sur **Tourniquet** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Une barre oblique (« / ») est une entrée valide pour le chemin d’accès relatif. Elle implique que le fichier se trouve dans le répertoire racine (par défaut).
7. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
8. Testez les modifications dans votre configuration.
9. Une fois le profil Traffic Manager opérationnel, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d’entreprise vers le nom de domaine Traffic Manager.

## <a name="configure-performance-traffic-routing-method"></a>Configurer la méthode de routage en fonction des performances

La méthode de routage du trafic en fonction des performances vous permet de diriger le trafic vers le point de terminaison dont la latence est la plus faible dans le réseau du client. En règle générale, le centre de données dont la latence est la plus faible est géographiquement le plus proche. Cette méthode de routage du trafic ne peut pas prendre en compte des modifications en temps réel de la configuration ou de la charge du réseau.

1. Dans le volet gauche du portail Azure Classic, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager.
2. Dans le volet Traffic Manager, localisez le profil Traffic Manager contenant les paramètres que vous souhaitez modifier. Pour ouvrir la page des paramètres du profil, cliquez sur la flèche à droite du nom de profil.
3. Dans la page de votre profil, cliquez sur **Points de terminaison** en haut de la page et vérifiez que les points de terminaison de service que vous souhaitez inclure dans votre configuration sont présents.
4. Dans la page de votre profil, cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour **Paramètres de méthode de routage du trafic**, vérifiez que la méthode de routage du trafic est **Performance**. Si ce n’est pas le cas, cliquez sur **Performances** dans la liste déroulante.
6. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Une barre oblique (« / ») est une entrée valide pour le chemin d’accès relatif. Elle implique que le fichier se trouve dans le répertoire racine (par défaut).
7. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
8. Testez les modifications dans votre configuration.
9. Une fois le profil Traffic Manager opérationnel, modifiez l’enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d’entreprise vers le nom de domaine Traffic Manager.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer des profils Traffic Manager](traffic-manager-manage-profiles.md)
* [Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)
* [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md)
* [Rediriger un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md)
* [Gérer les points de terminaison de Traffic Manager](traffic-manager-manage-endpoints.md)
* [Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO5-->


