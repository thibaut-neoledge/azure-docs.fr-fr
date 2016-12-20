---
title: "Utilisation des services d’équilibrage de charge dans le cloud Azure | Microsoft Docs"
description: "Ce didacticiel vous montre comment créer un scénario utilisant la gamme de services d’équilibrage de charge Azure : Traffic Manager, Application Gateway et Load Balancer."
services: traffic-manager
documentationcenter: 
author: liumichelle
manager: vitinnan
editor: 
ms.assetid: f89be3be-a16f-4d47-bcae-db2ab72ade17
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 90918f6bb23f0f4e3c009506e1b12c028c45f02b

---

# <a name="using-load-balancing-services-in-the-azure-cloud"></a>Utilisation des services d’équilibrage de charge dans le cloud Azure

## <a name="introduction"></a>Introduction

Microsoft Azure propose plusieurs services destinés à gérer la distribution et l’équilibrage de la charge du trafic réseau. Vous pouvez utiliser ces services individuellement ou combiner leurs méthodes selon vos besoins spécifiques et élaborer une solution optimale.

Dans ce didacticiel, nous allons tout d’abord définir le cas d’usage d’un client et voir comment nous pouvons le rendre plus robuste et plus performant en utilisant la gamme de services d’équilibrage de charge Azure, à savoir, Traffic Manager, Application Gateway et Load Balancer. Nous donnerons ensuite des instructions détaillées pour créer un déploiement géographiquement redondant, qui distribue le trafic à des machines virtuelles et permet de gérer différents types de demandes.

À un niveau conceptuel, chacun de ces services joue un rôle distinct dans la hiérarchie d’équilibrage de charge.

1. **Traffic Manager** assure un équilibrage de charge DNS global.  Il examine les demandes DNS entrantes et répond avec un point de terminaison intègre et conforme à la stratégie de routage choisie par le client. Les méthodes de routage proposées sont le routage basé sur les performances, où le demandeur est orienté vers le point de terminaison le plus proche en termes de latence et de priorité (tout le trafic est alors dirigé vers un point de terminaison déterminé, avec d’autres points de terminaison de secours) et le routage en tourniquet (round robin ) pondéré, où le trafic est distribué en fonction de la pondération affectée à chaque point de terminaison. Le client se connecte directement à ce point de terminaison. Azure Traffic Manager est capable de détecter un point de terminaison défectueux et redirige les clients vers une autre instance intègre. Pour plus d’informations sur ce service, consultez la [documentation Azure Traffic Manager](traffic-manager-overview.md).
2. **Application Gateway** propose Application Delivery Controller (ADC) en tant que service, mettant ainsi plusieurs fonctionnalités d’équilibrage de charge de couche 7 à la disposition de votre application. Il permet aux clients d’optimiser la productivité de la batterie de serveurs web en déchargeant une terminaison SSL gourmande en ressources du processeur vers la passerelle Application Gateway. Parmi les autres fonctionnalités de routage de couche 7, citons la distribution en tourniquet (round robin) du trafic entrant, l’affinité de session basée sur les cookies, le routage basé sur le chemin des URL et la possibilité d’héberger plusieurs sites web derrière une seule passerelle Application Gateway. La passerelle Application Gateway peut être configurée en tant que passerelle internet, passerelle interne uniquement ou une combinaison des deux. La passerelle Application Gateway est une solution Azure entièrement gérée, hautement évolutive et hautement disponible. Elle fournit un ensemble complet de fonctionnalités de diagnostics et de journalisation pour une meilleure gérabilité.
3. **Load Balancer** fait partie intégrante de la pile de mise en réseau (SDN) Azure. Load Balancer fournit des services d’équilibrage de charge de couche 4 hautement performants et à faible latence pour tous les protocoles UDP et TCP.  Il gère les connexions entrantes et sortantes.  Vous pouvez configurer des points de terminaison publics et internes avec un équilibrage de charge et définir des règles de mappage des connexions entrantes aux destinations du pool principal avec des options d’analyse d’intégrité TCP et HTTP de façon à gérer la disponibilité du service.

## <a name="scenario"></a>Scénario

Dans ce scénario, nous utilisons un site web simple qui affiche deux types de contenu : des images et des pages web affichées dynamiquement. Ce site web doit être géographiquement redondant et doit desservir ses utilisateurs à partir de l’emplacement le plus proche (avec la latence la plus faible). Le développeur de l’application a décidé que les URL correspondant au modèle /images /* seraient desservies par un pool dédié de machines virtuelles différent du reste de la batterie de serveurs web.

De plus, le pool de machines virtuelles par défaut affichant le contenu dynamique doit communiquer avec une base de données principale hébergée sur un cluster à haute disponibilité. Le déploiement est entièrement approvisionné via Azure Resource Manager.

L’utilisation de Traffic Manager, d’Application Gateway et de Load Balancer permet à ce site web d’atteindre les objectifs de conception suivants :

1. **Géo-redondance multiple** : avec Traffic Manager, si une région connaît une défaillance, le trafic est acheminé en toute transparence vers la meilleure région suivante sans aucune intervention du propriétaire de l’application.
2. **Latence réduite** : comme Azure Traffic Manager dirige automatiquement le client vers la région la plus proche, celui-ci subit une latence plus faible quand il demande le contenu de la page web.
3. **Extensibilité indépendante** : sachant que la charge de travail de l’application web est séparée selon le type de contenu, le propriétaire de l’application peut mettre à l’échelle les charges de travail des demandes indépendamment les unes des autres. Application Gateway vérifie que le trafic est acheminé vers les pools appropriés en fonction des règles spécifiées et de l’intégrité de l’application.
4. **Équilibrage de charge interne** : Load Balancer se trouvant devant le cluster à haute disponibilité, seul le point de terminaison actif et intègre d’une base de données est exposé à l’application.  De plus, un administrateur de base de données peut optimiser la charge de travail en répartissant les réplicas actifs et passifs dans le cluster, indépendamment de l’application frontale.  Load Balancer fournit les connexions au cluster à haute disponibilité et fait en sorte que seules les bases de données intègres reçoivent les demandes de connexion.

Le diagramme suivant illustre l’architecture de ce scénario :

![image du diagramme du scénario](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Cet exemple est l’une des innombrables configurations possibles offertes par les services d’équilibrage de charge Azure. Azure Traffic Manager, Application Gateway et Load Balancer peuvent être combinés et associés pour répondre au mieux à vos besoins d’équilibrage de charge. Par exemple, s’il n’est pas nécessaire de faire appel au déchargement SSL ou au traitement de la couche 7, Load Balancer peut être utilisé à la place d’Application Gateway.

## <a name="setting-up-the-load-balancing-stack"></a>Configuration de la pile d’équilibrage de charge

### <a name="step-1-create-a-traffic-manager-profile"></a>Étape 1 : créer un profil Traffic Manager

1. Accédez au portail Azure, cliquez sur **Nouveau**, puis recherchez « profil Traffic Manager » sur la place de marché.
2. Dans le panneau « Créer un profil Traffic Manager », fournissez les informations de base permettant de créer un profil Traffic Manager :

   * **Nom** : donnez un nom de préfixe DNS à votre profil Traffic Manager
   * **Méthode de routage** : sélectionnez la stratégie de méthode routage du trafic. Pour plus d’informations sur les méthodes, consultez [À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-routing-methods.md)
   * **Abonnement** : abonnement qui contient le profil
   * **Groupe de ressources** : groupe de ressources devant contenir le profil Traffic Manager ; il peut s’agir d’un groupe de ressources nouveau ou existant
   * **Emplacement du groupe de ressources** : Traffic Manager est un service global qui n’est lié à aucun emplacement. Cependant, il convient de spécifier une région pour le groupe où résident les métadonnées associées au profil Traffic Manager. Cet emplacement n’a aucune incidence sur la disponibilité du profil au moment de l’exécution.

3. Cliquez sur **Créer** pour générer le profil Traffic Manager

    ![panneau créer un profil traffic manager](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Étape 2 : créer les passerelles Application Gateway

1. Accédez au portail Azure puis, dans le menu de gauche, cliquez sur **Nouveau** > **Mise en réseau** > **Application Gateway**
2. Remplissez ensuite les informations de base relatives à la passerelle Application Gateway. Quand vous avez terminé, cliquez sur OK. Les informations nécessaires pour les paramètres de base sont les suivantes :

   * **Nom** -Nom de la passerelle Application Gateway.
   * **Taille de la référence (SKU)** - Taille de la passerelle Application Gateway. Les options disponibles sont Petit, Moyen et Grand.
   * **Nombre d’instances** - Nombre d’instances. Cette valeur doit être un nombre compris entre 2 et 10.
   * **Groupe de ressources** - Groupe de ressources destiné à contenir la passerelle Application Gateway. Il peut s’agir d’un groupe de ressources existant ou nouveau.
   * **Emplacement** - Région de la passerelle Application Gateway. Il s’agit du même emplacement que celui du groupe de ressources. Cette notion est importante, car le réseau virtuel et l’adresse IP publique doivent se trouver au même endroit que la passerelle.

3. Définissez ensuite le réseau virtuel, le sous-réseau, l’adresse IP frontale et les configurations d’écouteur pour la passerelle Application Gateway. Dans ce scénario, l’adresse IP frontale est de type **Public** pour pouvoir être ajoutée par la suite comme point de terminaison au profil Traffic Manager.
4. Le prochain paramètre Application Gateway à configurer est celui relatif à l’écouteur. Si http est utilisé, vous n’avez rien d’autre à configurer et pouvez cliquer sur **OK**. Pour utiliser https, une configuration supplémentaire est nécessaire. Consultez [Créer une passerelle Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md), à partir de l’étape 9.

#### <a name="configure-url-routing-for-application-gateways"></a>Configurer le routage d’URL pour les passerelles Application Gateway

Au moment de choisir le pool principal, une passerelle Application Gateway configurée avec une règle basée sur le chemin adopte un modèle de chemin de l’URL de la demande en plus du mode de distribution en tourniquet (round robin). Dans ce scénario, nous ajoutons une règle basée sur le chemin pour diriger les URL contenant « /images/\* » vers le pool de serveurs d’images. Pour plus d’informations sur la configuration du routage basé sur le chemin des URL pour une passerelle Application Gateway, consultez [Créer une règle basée sur le chemin pour une passerelle Application Gateway](../application-gateway/application-gateway-create-url-route-portal.md).

![diagramme de niveau web](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. À partir de votre groupe de ressources, accédez à l’instance de la passerelle Application Gateway créée aux étapes précédentes.
2. Sous Paramètres, sélectionnez **Pools principaux** et sélectionnez Ajouter pour ajouter les machines virtuelles à associer aux pools principaux de niveau web.
3. Dans le panneau « Ajouter un pool principal », entrez le nom du pool principal et toutes les adresses IP des machines résidant dans le pool. Dans ce scénario, nous connectons deux pools de serveurs principaux de machines virtuelles.

    ![passerelles application gateway - ajouter un pool principal](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Ensuite, dans les paramètres de la passerelle Application Gateway, sélectionnez **Règles**, puis cliquez sur le bouton **Basé sur le chemin** pour ajouter une nouvelle règle.

    ![passerelles application gateway - ajouter une règle de chemin](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Dans le panneau « Ajouter une règle basée sur le chemin », fournissez les informations suivantes pour configurer la règle :

   * Paramètres de base :
     + **Nom** : nom convivial de la règle accessible dans le portail
     + **Écouteur** : écouteur utilisé pour la règle
     + **Pool principal par défaut** : pool principal à utiliser pour la règle par défaut
     + **Paramètres HTTP par défaut** : paramètres HTTP à utiliser pour la règle par défaut
   * Règles basées sur le chemin :
     + **Nom** : nom convivial de la règle basée sur le chemin
     + **Chemins** : règle de chemin utilisée pour transférer le trafic.
     + **Pool principal** : pool principal à utiliser avec cette règle
     + **Paramètre HTTP** : paramètres HTTP à utiliser avec cette règle

     > [!IMPORTANT]
     > Chemins : pour être valides, les chemins doivent commencer par « / ». Le caractère générique « \* » n’est autorisé qu’à la fin. /xyz, /xyz\* ou /xyz/\* sont des exemples valides

     ![passerelle application gateway - panneau ajouter une règle de chemin](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Étape 3 : ajouter des passerelles Application Gateway aux points de terminaison Traffic Manager

Dans ce scénario, Traffic Manager est connecté à des instances d’Application Gateway (tel que configuré dans les étapes précédentes) qui résident dans des régions différentes. Maintenant que les passerelles Application Gateway sont configurées, l’étape suivante consiste à les connecter à notre profil Traffic Manager.

1. Accédez à votre instance du profil Traffic Manager (pour cela, regardez dans votre groupe de ressources ou recherchez le nom du profil Traffic Manager dans « Toutes les ressources »).
2. Dans ce panneau, sélectionnez **Points de terminaison** et **Ajouter** pour ajouter un nouveau point de terminaison.

    ![traffic manager - ajouter un point de terminaison](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Dans le panneau « Ajouter un point de terminaison », fournissez les informations permettant de créer un point de terminaison

   * **Type** : type du point de terminaison qui doit faire l’objet d’un équilibrage de charge. Dans ce scénario, il s’agit d’un point de terminaison Azure, car nous le connectons aux instances Application Gateway configurées précédemment
   * **Nom** : nom du point de terminaison
   * **Type de ressource cible** : sélectionnez Adresse IP publique puis, dans le paramètre « Ressource cible » ci-dessous, sélectionnez l’adresse IP publique de la passerelle Application Gateway configurée précédemment

     ![traffic manager - ajouter un point de terminaison](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. À ce stade, vous pouvez tester votre configuration en y accédant avec le DNS de votre profil Traffic Manager (dans cet exemple : TrafficManagerScenario.trafficmanager.net). Vous pouvez renvoyer les demandes, mettre en service/hors service les machines virtuelles/serveurs web créés dans les différentes régions, puis modifier les paramètres du profil Traffic Manager pour tester votre configuration.

### <a name="step-4-create-the-load-balancer"></a>Étape 4 : créer l’équilibreur de charge

Dans ce scénario, Load Balancer distribue les connexions de niveau web aux bases de données au sein d’un cluster à haute disponibilité.

Si votre cluster de bases de données à haute disponibilité utilise SQL AlwaysOn, consultez Configurer un ou plusieurs [écouteurs de groupe de disponibilité Always On](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour obtenir des instructions pas à pas.

Pour plus d’informations sur la configuration d’un équilibreur de charge interne, consultez [Créer un équilibreur de charge interne dans le portail Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)

1. Accédez au portail Azure puis, dans le menu de gauche, cliquez sur **Nouveau** > **Mise en réseau** > **Équilibreur de charge**
2. Dans le panneau « Créer un équilibreur de charge », choisissez un nom pour votre équilibreur de charge
3. Définissez le **type** sur Interne et choisissez le réseau et le sous-réseau virtuels appropriés dans lesquels l’équilibreur de charge doit résider
4. Sous **Attribution d’adresses IP**, sélectionnez Dynamique ou Statique
5. Ensuite, sous **Groupe de ressources**, choisissez le groupe de ressources pour l’équilibreur de charge
6. Sous **Emplacement**, choisissez la région appropriée pour l’équilibreur de charge
7. Enfin, cliquez sur **Créer** pour générer l’équilibreur de charge

#### <a name="connect-backend-database-tier-to-load-balancer"></a>Connecter un niveau de base de données principale à l’équilibreur de charge

1. À partir de votre groupe de ressources, recherchez l’équilibreur de charge créé aux étapes précédentes.
2. Sous Paramètres, cliquez sur **Pools principaux** puis sur **Ajouter** pour ajouter un nouveau pool principal
3. Dans le panneau « Ajouter un pool principal », entrez le nom du pool principal
4. À ce stade, vous pouvez ajouter au pool principal des machines individuelles ou un groupe à haute disponibilité.

   ![équilibreur de charge - ajouter au pool](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

#### <a name="configure-a-probe"></a>Configurer une sonde

1. Dans les paramètres de votre équilibreur de charge, sélectionnez **Sondes** puis **Ajouter** pour ajouter une nouvelle sonde
2. Dans le panneau « Ajouter une sonde », entrez le **nom** de la sonde
3. Sélectionnez le **protocole** de la sonde. Pour une base de données, vous choisirez sans doute une sonde TCP plutôt qu’une sonde HTTP.   Pour plus d’informations sur les sondes d’équilibreur de charge, consultez [Sondes d’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md).
4. Ensuite, entrez le **port** de votre base de données à utiliser au moment d’accéder à la sonde.
5. Sous **Intervalle**, spécifiez la fréquence de sondage de l’application
6. Sous **Seuil de défaillance sur le plan de l’intégrité**, spécifiez le nombre d’échecs de sonde successifs qui doivent se produire avant que la machine virtuelle principale soit considérée comme défectueuse.
7. Cliquez sur **OK** pour créer la sonde

   ![sonde d’équilibreur de charge](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

#### <a name="configure-load-balancing-rules"></a>Configuration des règles d’équilibrage de la charge

1. Dans les paramètres de votre équilibreur de charge, sélectionnez **Règles d’équilibrage de charge** puis **Ajouter** pour créer une règle
2. Dans le panneau « Ajouter une règle d’équilibrage de charge », entrez le **nom** de la règle d’équilibrage de charge
3. Choisissez l’**adresse IP frontale de l’équilibreur de charge**, le **protocole** et le **port**
4. Sous **Port principal**, spécifiez le port à utiliser dans le pool principal
5. Sélectionnez le **pool principal** et la **sonde** créés aux étapes précédentes pour lui appliquer la règle
6. Sous **Persistance de session**, choisissez la façon dont vous souhaitez que les sessions soient conservées
7. Sous **Délais d’inactivité**, spécifiez le délai d’inactivité en minutes
8. Sélectionnez Désactivé ou Activé pour **IP flottante**
9. Cliquez sur **OK** pour créer la règle

### <a name="step-5-connect-web-tier-vms-to-load-balancer"></a>Étape 5 : connecter des machines virtuelles de niveau web à l’équilibreur de charge

Nous allons maintenant configurer l’adresse IP et le port frontal de l’équilibreur de charge dans les applications s’exécutant sur vos machines virtuelles de niveau web pour les connexions de base de données éventuelles. Cette configuration est propre à l’application qui s’exécute sur ces machines virtuelles. Pour savoir comment configurer l’adresse IP et le port de destination, consultez la documentation de l’application. Pour trouver l’adresse IP du serveur frontal, accédez au pool d’adresses IP frontales dans le panneau des paramètres de l’équilibreur de charge du portail Azure.

![équilibreur de charge - pool d’adresses ip frontales](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de Traffic Manager](traffic-manager-overview.md)
* [Vue d’ensemble d’Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Vue d’ensemble d’Azure Load Balancer](../load-balancer/load-balancer-overview.md)



<!--HONumber=Nov16_HO3-->


