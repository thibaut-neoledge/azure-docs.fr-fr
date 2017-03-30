---
title: "Équilibrage de charge sur plusieurs configurations IP dans Azure | Microsoft Docs"
description: "Équilibrage de charge sur des configurations IP principales et secondaires."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: cf1e68c7b37b2506de007bdf24eea63a27187a33
ms.lasthandoff: 03/22/2017

---

# <a name="load-balancing-on-multiple-ip-configurations-using-the-azure-portal"></a>Équilibrage de charge sur plusieurs configurations IP dans le portail Azure

> [!div class="op_single_selector"]
> * [Portail](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](load-balancer-multiple-ip-cli.md)

Cet article décrit comment utiliser Azure Load Balancer avec plusieurs adresses IP sur une carte réseau secondaire. Dans ce scénario, deux machines virtuelles exécute Windows, chacune avec une carte réseau principale et une carte réseau secondaire. Chacune des cartes réseau secondaires dispose de deux configurations IP. Chaque machine virtuelle héberge les deux sites web contoso.com et fabrikam.com. Chaque site web est lié à l’une des configurations IP sur la carte réseau secondaire. Nous utilisons Azure Load Balancer pour exposer deux adresses IP frontales, une par site web, afin de distribuer le trafic à la configuration IP correspondante pour le site web. Ce scénario utilise le même numéro de port sur les deux serveurs frontaux, ainsi que les deux adresses IP de pool principal.

![Image du scénario LB](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

##<a name="prerequisites"></a>Composants requis
Cet exemple suppose que vous disposiez d’un groupe de ressources nommé *contosofabrikam* avec la configuration suivante :
 -  Il comprend un réseau virtuel intitulé *myVNet*, deux machines virtuelles appelées *VM1* et *VM2* respectivement dans le même groupe à haute disponibilité nommé *myAvailset*. 
 - Chaque machine virtuelle possède une carte réseau principale et une carte réseau secondaire. Les cartes réseau principales s’appellent *VM1NIC1* et *VM2NIC1*, tandis que les cartes réseau secondaires ont pour nom *VM1NIC2* et *VM2NIC2*. Pour plus d’informations sur la création de machines virtuelles avec plusieurs cartes réseau, consultez [Créer une machine virtuelle avec plusieurs cartes réseau à l’aide de PowerShell](../virtual-network/virtual-network-deploy-multinic-arm-ps.md).

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Étapes pour équilibrer la charge sur plusieurs configurations IP

Pour mener à bien le scénario décrit dans cet article, procédez comme suit :

### <a name="step-1-configure-the-secondary-nics-for-each-vm"></a>ÉTAPE 1 : Configurer les cartes réseau secondaires pour chaque machine virtuelle

Pour chaque machine virtuelle dans votre réseau virtuel, ajoutez la configuration IP définie pour la carte réseau secondaire comme suit :  

1. Dans un navigateur, accédez au portail Azure (http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur l’icône des groupes de ressources, puis sur celui contenant les machines virtuelles (par exemple, *contosofabrikam*). Le panneau **Groupes de ressources** qui répertorie toutes les ressources et les interfaces réseau des machines virtuelles s’affiche.
3. À la carte réseau secondaire de chaque machine virtuelle, ajoutez une configuration IP comme suit :
    1. Sélectionnez l’interface réseau à laquelle vous souhaitez ajouter la configuration IP.
    2. Dans le panneau affichant la carte réseau que vous avez sélectionnée, cliquez sur **Configurations IP**. Cliquez ensuite sur le bouton **Ajouter** en haut du panneau qui s’affiche.
    3. Dans le panneau **Add IP configurations (Ajouter des configurations IP)**, ajoutez une seconde configuration IP à la carte réseau comme suit : 
        1. Tapez le nom de votre configuration IP secondaire (par exemple, pour VM1 et VM2, nommez les configurations IP *VM1NIC2-ipconfig2* et *VM2NIC2-ipconfig2* respectivement).
        2. Dans **Private IP address (Adresse IP privée)**, pour **Allocation**, sélectionnez **Statique**.
        3. Cliquez sur **OK**.
        4. Lorsque la seconde configuration IP de la carte réseau secondaire est terminée, elle s’affiche dans le panneau **Configurations IP** de la carte réseau donnée.

### <a name="step-2-create-a-load-balancer"></a>ÉTAPE 2 : Créer un équilibrage de charge

Créez un équilibrage de charge comme suit :

1. Dans un navigateur, accédez au portail Azure (http://portal.azure.com) et connectez-vous avec votre compte Azure.
2. Dans l’angle supérieur gauche de l’écran, cliquez sur **Nouveau** > **Réseau** > **Load Balancer (Équilibrage de charge)**. Ensuite, cliquez sur **Créer**.
3. Dans le panneau **Créer un équilibreur de charge** , tapez le nom de votre équilibreur de charge. Ici, il s’appelle *myLoadBalancer*.
4. Sous Public IP Address (Adresse IP publique), créez une adresse IP publique appelée **myPublicIP1**.
5. Sous Groupe de ressources, sélectionnez le groupe de ressources de vos machines virtuelles (par exemple, *contosofabrikam*). Ensuite, sélectionnez l’emplacement approprié, puis cliquez sur **OK**. L’équilibreur de charge commencera ensuite le déploiement, qui prendra plusieurs minutes pour se terminer avec succès.
6. Une fois déployé, l’équilibrage de charge s’affiche comme une ressource dans votre groupe de ressources.

### <a name="step-3-configure-the-frontend-ip-pool"></a>ÉTAPE 3 : Configurer le pool IP frontal

Configurez votre pool IP frontal pour chaque site Web (Contoso et Fabrikam) comme suit :

1. Dans le portail, cliquez sur **Plus de services**, puis saisissez **Adresse IP publique** dans la zone de filtre et cliquez sur **Public IP Addresses (Adresses IP publiques)**. Cliquez sur **Ajouter** en haut du panneau qui s’affiche.
2. Configurez deux adresses IP publiques (*PublicIP1* et *PublicIP2*) pour les deux sites Web (contoso et fabrikam) comme suit :
    1. Tapez le nom de votre adresse IP frontale.
    2. Dans **Groupe de ressources**, sélectionnez le groupe de ressources des machines virtuelles (par exemple, *contosofabrikam*).
    3. Dans **Emplacement**, sélectionnez le même emplacement que les machines virtuelles.
    4. Cliquez sur **OK**.
    5. Une fois les deux adresses IP publiques créées, elles s’affichent dans le panneau **Public IP Addresses (Adresses IP publiques)**.
3. Dans le portail, cliquez sur **Plus de services**, saisissez **Équilibrage de charge** dans la zone de filtre, puis cliquez sur **Load Balancer (Équilibrage de charge)**.  
4. Sélectionnez l’équilibrage de charge (*mylb*) auquel vous souhaitez ajouter le pool IP frontal.
5. Sous **Paramètres**, sélectionnez **Frontend Pools (Pools frontaux)**. Cliquez ensuite sur le bouton **Ajouter** en haut du panneau qui s’affiche.
6. Tapez le nom de votre adresse IP frontale (*farbikamfe* ou **contosofe*).
7. Cliquez sur **Adresse IP**, puis dans le panneau **Public IP Address (Adresse IP publique)**, sélectionnez les adresses IP de votre serveur frontal (*PublicIP1* ou *PublicIP2*).
8. Répétez les étapes 3 à 7 de cette section pour créer la deuxième adresse IP frontale.
9. Lorsque la configuration du pool IP frontal est terminée, les deux adresses IP frontales s’affichent dans le panneau **Frontend IP Pool (Pool IP frontal)** de votre équilibrage de charge. 
    
### <a name="step-4-configure-the-backend-pool"></a>ÉTAPE 4 : Configurer le pool principal   
Configurez les pools d’adresses principaux de votre équilibrage de charge pour chaque site Web (Contoso et Fabrikam) comme suit :
        
1. Dans le portail, cliquez sur **Plus de services**, saisissez équilibrage de charge dans la zone de filtre, puis cliquez sur **Load Balancer (Équilibrage de charge)**.  
2. Sélectionnez l’équilibrage de charge (*mylb*) auquel vous souhaitez ajouter les pools principaux.
3. Sous **Paramètres**, sélectionnez **Backend Pools (Pools principaux)**. Tapez le nom de votre pool principal (par exemple, *contosopool* ou *fabrikampool*). Cliquez ensuite sur le bouton **Ajouter** en haut du panneau qui s’affiche. 
4. Dans **Associated to (Associé à)**, sélectionnez **Availability set (Groupe à haute disponibilité)**.
5. Dans **Availability set (Groupe à haute disponibilité)**, sélectionnez **myAvailset**.
6. Ajoutez les configurations IP réseau cibles pour les deux machines virtuelles comme suit (voir la figure 2) :  
    1. Dans **Target Virtual machine (Machine virtuelle cible)**, sélectionnez la machine virtuelle que vous souhaitez ajouter au pool principal (par exemple, VM1 ou VM2).
    2. Dans **Network IP configuration (Configuration IP du réseau)**, sélectionnez la configuration IP des cartes réseau secondaires de cette machine virtuelle (par exemple, VM1NIC2-ipconfig2 ou VM2NIC2-ipconfig2).
    ![Image du scénario LB](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
            
        **Figure 2** : Configuration de l’équilibrage de charge avec les pools principaux  
7. Cliquez sur **OK**.
8. Lorsque la configuration du pool principal est terminée, les deux pools d’adresses principales s’affichent dans le panneau **Backend Pool (Pool principal)** de votre équilibrage de charge.

### <a name="step-5-configure-a-health-probe-for-your-load-balancer"></a>ÉTAPE 5 : Configuration d’une sonde d’intégrité pour votre équilibrage de charge
Configurez une sonde d’intégrité pour votre équilibrage de charge comme suit :
    1. Dans le portail, cliquez sur Plus de services, saisissez équilibrage de charge dans la zone de filtre, puis cliquez sur **Load Balancer (Équilibrage de charge)**.  
    2. Sélectionnez l’équilibrage de charge auquel vous souhaitez ajouter les pools principaux.
    3. Sous **Paramètres**, sélectionnez **Health Probe (Sonde d’intégrité)**. Cliquez ensuite sur le bouton **Ajouter** en haut du panneau qui s’affiche.
    4. Tapez le nom de la sonde d’intégrité (par exemple, HTTP), puis cliquez sur **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>ÉTAPE 6 : Configurer les règles d’équilibrage de charge
Configurez les règles d’équilibrage de charge (*HTTPc* et *HTTPf*) pour chaque site Web comme suit :
    
1. Sous **Paramètres**, sélectionnez **Health Probe (Sonde d’intégrité)**. Cliquez ensuite sur le bouton **Ajouter** en haut du panneau qui s’affiche.
2. Dans **Nom**, tapez le nom de la règle d’équilibrage de charge (par exemple, *HTTPc* pour Contoso ou *HTTPf* pour Fabrikam)
3. Dans Frontend IP Address (Adresse IP frontale), sélectionnez l’adresse IP frontale (par exemple, *Contosofe* ou *Fabrikamfe*)
4. Dans **Port** et **Backend port (Port principal)**, conservez la valeur par défaut (**80**).
5. Dans **Adresse IP flottante (retour serveur direct)**, cliquez sur **Activé**.
6. Cliquez sur **OK**.
7. Répétez les étapes 1 à 6 de cette section pour créer la deuxième règle d’équilibrage de charge.
8. Lorsqu’elles sont configurées, les deux règles (*HTTPc* et *HTTPf*) s’affichent dans le panneau **Load balancing rules (Règles d’équilibrage de charge)** de votre équilibrage de charge.

### <a name="step-7-configure-dns-records"></a>ÉTAPE 7 : Configurer les enregistrements DNS
Enfin, vous devez configurer les enregistrements de ressource DNS pour qu’ils pointent vers l’adresse IP frontale de l’équilibrage de charge. Vous pouvez héberger vos domaines dans Azure DNS. Pour plus d’informations sur l’utilisation d’Azure DNS avec un équilibrage de charge, voir [Utiliser Azure DNS avec d’autres services Azure](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour en savoir plus sur la combinaison de services d’équilibrage de charge dans Azure, consultez [Utilisation des services d’équilibrage de charge dans Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- Pour savoir comment gérer et dépanner l’équilibrage de charge à l’aide de différents types de journaux dans Azure, consultez [Analyse des journaux de l’équilibreur de charge Azure](../load-balancer/load-balancer-monitor-log.md).

