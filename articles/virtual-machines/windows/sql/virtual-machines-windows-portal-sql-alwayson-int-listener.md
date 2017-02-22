---
title: "Création d’un écouteur de groupe de disponibilité SQL Server - Machines virtuelles Azure | Microsoft Docs"
description: "Instructions détaillées pour la création d’un écouteur pour un groupe de disponibilité AlwaysOn pour SQL Server dans des machines virtuelles Azure"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configuration d’un équilibreur de charge interne pour un groupe de disponibilité AlwaysOn dans Azure
Cette rubrique explique la procédure de création d’un équilibreur de charge interne pour un groupe de disponibilité AlwaysOn SQL Server dans des machines virtuelles Azure en cours d’exécution dans le modèle Resource Manager. Un groupe de disponibilité nécessite un équilibreur de charge lorsque les instances SQL Server se trouvent sur des machines virtuelles Azure. Cet équilibrage de charge stocke l’adresse IP de l’écouteur de groupe de disponibilité. Si un groupe de disponibilité englobe plusieurs régions, chaque région a besoin d’un équilibreur de charge.

Pour effectuer cette tâche, vous devez disposer d’un groupe de disponibilité SQL Server déployé sur des machines virtuelles Azure dans le modèle Resource Manager. Les deux machines virtuelles SQL Server doivent appartenir au même groupe à haute disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pour créer automatiquement le groupe de disponibilité dans Azure Resource Manager. Ce modèle crée automatiquement l’équilibrage de charge interne. 

Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Cette rubrique requiert que vos groupes de disponibilité soient déjà configurés.  

Rubriques connexes :

* [Configurer des groupes de disponibilité Always On dans une machine virtuelle Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Dans ce document, vous allez apprendre à créer et configurer un équilibreur de charge dans le portail Azure. Une fois cette opération terminée, vous allez configurer le cluster afin qu’il utilise l’adresse IP de l’équilibreur de charge pour l’écouteur de groupe de disponibilité.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Créer et configurer l’équilibrage de charge dans le portail Azure
Dans cette partie de la tâche, vous allez exécuter les étapes suivantes dans le portail Azure :

1. Créer l’équilibrage de charge et configurer l’adresse IP
2. Configurer le pool principal
3. Créer la sonde 
4. Configurer les règles d’équilibrage de charge

> [!NOTE]
> Si les serveurs SQL se trouvent dans des régions et des groupes de ressources différents, vous effectuerez toutes ces étapes à deux reprises, une fois par groupe de ressources.
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Créer l’équilibrage de charge et configurer l’adresse IP
La première étape consiste à créer l’équilibrage de charge. Dans le portail Azure, ouvrez le groupe de ressources contenant les machines virtuelles SQL Server. Dans le groupe de ressources, cliquez sur **Ajouter**.

1. Recherchez **l’équilibrage de charge**. Dans les résultats de la recherche, sélectionnez l’élément **Équilibrage de charge** publié par **Microsoft**.
1. Dans le panneau **Équilibrage de charge**, cliquez sur **Créer**.
1. Dans **Créer l’équilibreur de charge**, configurez l’équilibreur de charge comme suit :

   | Paramètre | Valeur |
   | --- | --- |
   | **Nom** |Nom de l’équilibrage de charge. Par exemple, **sqlLB**. |
   | **Type** |**Interne** |
   | **Réseau virtuel** |Sélectionnez le réseau virtuel contenant les serveurs SQL. |
   | **Sous-réseau** |Sélectionnez le sous-réseau contenant les serveurs SQL. |
   | **Affectation d’adresses IP** |**Statique** |
   | **Adresse IP privée** |Spécifiez une adresse IP disponible à partir du sous-réseau. Vous allez l’utiliser pour créer un écouteur sur le cluster. Utilisez cette adresse pour la variable `$ILBIP` dans un script PowerShell plus loin dans cet article. |
   | **Abonnement** |Si vous avez plusieurs abonnements, ce champ peut s’afficher. Sélectionnez l’abonnement que vous souhaitez associer à cette ressource. Normalement, il s’agit du même abonnement que pour toutes les ressources du groupe de disponibilité. |
   | **Groupe de ressources** |Sélectionnez le groupe de ressources réseau contenant les serveurs SQL. |
   | **Emplacement** |Sélectionnez l’emplacement Azure contenant les serveurs SQL. |

1. Cliquez sur **Create**. 

Azure crée l’équilibreur de charge. Cet équilibrage de charge appartient à un réseau, un sous-réseau, un groupe de ressources et un emplacement spécifiques. Une fois l’opération terminée, vérifiez les paramètres de l’équilibrage de charge dans Azure. 

### <a name="2-configure-the-backend-pool"></a>2. Configurer le pool principal
L’étape suivante consiste à créer un pool d’adresses principal. Azure appelle *pool principal*ce pool d’adresses principal. En l’occurrence, le pool principal contient les adresses des deux serveurs SQL dans votre groupe de disponibilité. 

1. Dans votre groupe de ressources, cliquez sur l’équilibreur de charge créé. 
1. Dans **Paramètres**, cliquez sur **Pools principaux**.
1. Dans **Pools principaux**, cliquez sur **Ajouter** pour créer un pool d’adresses principal. 
1. Dans **Ajouter un pool principal** under **Nom**.
1. Sous **Machines virtuelles**, cliquez sur **+ Ajouter une machine virtuelle**. 
1. Sous **Choisir des machines virtuelles**, cliquez sur **Choisir un groupe à haute disponibilité** et spécifiez le groupe à haute disponibilité auquel les machines virtuelles SQL Server appartiennent.
1. Après avoir choisi le groupe à haute disponibilité, cliquez sur **Choisir les machines virtuelles**. Cliquez sur les deux machines virtuelles qui hébergent les instances SQL Server dans le groupe de disponibilité. Cliquez sur **Sélectionner**. 
1. Cliquez sur **OK** pour fermer les panneaux **Choisir les machines virtuelles** et **Ajouter un pool principal**. 

Azure met à jour les paramètres du pool d’adresses principal. Votre groupe à haute disponibilité contient maintenant un pool de deux serveurs SQL.

### <a name="3-create-a-probe"></a>3. Créer une sonde
L’étape suivante consiste à créer une sonde. La sonde définit la manière d’Azure d’identifier celui des serveurs SQL qui possède l’écouteur de groupe de disponibilité. Azure teste le service avec l’adresse IP sur un port que vous définissez lors de la création de la sonde.

1. Dans le panneau **Paramètres** de l’équilibreur de charge, cliquez sur **Sondes d’intégrité**. 
1. Dans le panneau **Sondes d’intégrité**, cliquez sur **Ajouter**.
1. Configurez la sonde dans le panneau **Ajouter une sonde** . Utilisez les valeurs suivantes pour configurer la sonde :

   | Paramètre | Valeur |
   | --- | --- |
   | **Nom** |Nom de la sonde. Par exemple, **SQLAlwaysOnEndPointProbe**. |
   | **Protocole** |**TCP** |
   | **Port** |Vous pouvez utiliser n’importe quel port disponible. Par exemple, *59999*. |
   | **Intervalle** |*5* |
   | **Seuil de défaillance sur le plan de l’intégrité** |*2* |

1.  Cliquez sur **OK**. 

> [!NOTE]
> Vérifiez que le port spécifié est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs requièrent une règle de trafic entrant sur le port TCP utilisé. Pour plus d’informations, consultez [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx) . 
> 
> 

Azure crée la sonde. Azure utilise cette sonde pour identifier le serveur SQL propriétaire de l’écouteur de groupe de disponibilité.

### <a name="4-set-the-load-balancing-rules"></a>4. Configurer les règles d’équilibrage de charge
Configurez les règles d’équilibrage de charge. Les règles d’équilibrage de charge déterminent comment l’équilibrage de charge achemine le trafic aux serveurs SQL. Pour cet équilibreur de charge, activez le retour direct du serveur, car seul un des deux serveurs SQL possède la ressource d’écouteur de groupe de disponibilité.

1. Dans le panneau **Paramètres** de l’équilibrage de charge, cliquez sur **Règles d’équilibrage de charge**. 
1. Dans le panneau **Règles d’équilibrage de charge**, cliquez sur **Ajouter**.
1. Utilisez le panneau **Ajouter une règle d’équilibrage de charge** pour configurer la règle d’équilibrage de charge. Utilisez les paramètres suivants : 

   | Paramètre | Valeur |
   | --- | --- |
   | **Nom** |Nom de la règle d’équilibrage de charge. Par exemple, **SQLAlwaysOnEndPointListener**. |
   | **Protocole** |**TCP** |
   | **Port** |*1433* |
   | **Port principal** |*1433*. Ce paramètre est désactivé, car cette règle utilise le paramètre **Adresse IP flottante (retour direct du serveur)**. |
   | **Sonde** |Utilisez le nom de la sonde que vous avez créée pour cet équilibrage de charge. |
   | **Persistance de session** |**Aucun** |
   | **Délai d’inactivité (minutes).** |*4* |
   | **Adresse IP flottante (retour serveur direct)** |**Activé** |

   > [!NOTE]
   > Vous devrez peut-être faire défiler le panneau vers le bas pour afficher tous les paramètres.
   > 

1. Cliquez sur **OK**. 
1. Azure configure la règle d’équilibrage de charge. L’équilibrage de charge est maintenant configuré pour acheminer le trafic vers le serveur SQL qui héberge l’écouteur de groupe de disponibilité. 

À ce stade, le groupe de ressources a un équilibreur de charge qui relie les deux ordinateurs SQL Server. L’équilibreur de charge contient également l’adresse IP de l’écouteur de groupe de disponibilité AlwaysOn SQL Server, afin que l’un ou l’autre ordinateur puisse répondre aux demandes des groupes de disponibilité.

> [!NOTE]
> Si vos serveurs SQL se trouvent dans deux régions, répétez les étapes dans l’autre région. Chaque région nécessite un équilibrage de charge. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurer le cluster pour qu’il utilise l’adresse IP de l’équilibrage de charge
L’étape suivante consiste à configurer l’écouteur sur le cluster et à le mettre en ligne. Effectuez également les étapes suivantes : 

1. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement 
2. Mettre l'écouteur en ligne

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement
Dans cette étape, vous créez manuellement l'écouteur du groupe de disponibilité dans le Gestionnaire du cluster de basculement et SQL Server Management Studio (SSMS).

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>Vérifiez la configuration de l’écouteur

Si les ressources de cluster et les dépendances sont correctement configurées, vous devez être en mesure de voir l’écouteur dans les studios de gestion SQL Server. Procédez comme suit pour définir le port d’écoute :

1. Lancez SQL Server Management Studio et connectez-vous au réplica principal.
2. Accédez à **Haute disponibilité AlwaysOn** | **Groupes de disponibilité** | **Écouteurs de groupe de disponibilité**. 
1. Vous devez maintenant voir le nom de l'écouteur que vous avez créé dans le Gestionnaire du cluster de basculement. Cliquez avec le bouton droit sur l’écouteur, puis cliquez sur **Propriétés**.
1. Dans le champ **Port**, indiquez le numéro de port de l’écouteur de groupe de disponibilité à l’aide du paramètre $EndpointPort utilisé précédemment (valeur par défaut :&1433;), puis cliquez sur **OK**.

Vous avez maintenant un groupe de disponibilité AlwaysOn SQL Server sur des machines virtuelles Azure en mode Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Tester la connexion à l’écouteur
Pour tester la connexion :

1. Envoyez une requête RDP à un serveur SQL qui se trouve dans le même réseau virtuel, mais qui ne possède pas le réplica. Il peut s’agir de l’autre serveur SQL du cluster.
2. Utilisez l’utilitaire **sqlcmd** pour tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** au réplica principal par le biais de l’écouteur avec une authentification Windows :
   
        sqlcmd -S <listenerName> -E

La connexion SQLCMD se connecte automatiquement à l’instance SQL Server hébergeant le réplica principal. 

## <a name="guidelines-and-limitations"></a>Instructions et limitations
Notez les instructions suivantes concernant l’écouteur de groupe de disponibilité dans Azure utilisant l’équilibrage de charge interne :

* Le service cloud ne prend en charge qu’un écouteur de groupe de disponibilité interne, car l’écouteur est configuré sur le seul équilibreur de charge interne. Toutefois, il est possible de créer plusieurs écouteurs externes. 
* Avec un équilibreur de charge interne, vous n’accédez à l’écouteur qu’à partir du même réseau virtuel.


<!--HONumber=Jan17_HO2-->


