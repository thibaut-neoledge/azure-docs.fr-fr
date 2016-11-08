---
title: Configurer des écouteurs de groupes de disponibilité Always On – Microsoft Azure
description: Configurez les écouteurs de groupe de disponibilité sur le modèle Azure Resource Manager, à l’aide d’un équilibreur de charge interne avec une ou plusieurs adresses IP.
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar

ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: MikeRayMSFT

---
# <a name="configure-one-or-more-always-on-availability-group-listeners---resource-manger"></a>Configurer un ou plusieurs écouteurs de groupe Always On - Resource Manager
Cette rubrique explique comment effectuer deux opérations :

* créer un équilibreur de charge interne pour les groupes de disponibilité SQL Server à l’aide d’applets de commande PowerShell ;
* ajouter des adresses IP supplémentaires à un équilibreur de charge pour prendre en charge plusieurs groupes de disponibilité SQL Server. 

> **Important** La prise en charge de plusieurs écouteurs pour les groupes de disponibilité AlwaysOn sur les machines virtuelles Azure est une fonctionnalité préliminaire. Celle-ci est donc soumise aux conditions du contrat de licence (par exemple, le Contrat Entreprise, le Contrat Microsoft Azure ou le Contrat d’abonnement à Microsoft Online), ainsi qu’à toutes les [Conditions d’utilisation supplémentaires des versions préliminaires de Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> 
> 

Dans SQL Server, un écouteur de groupe de disponibilité est un nom de réseau virtuel auquel les clients se connectent afin d’accéder à une base de données dans le réplica primaire ou secondaire. Sur les machines virtuelles Azure, un équilibreur de charge comporte l’adresse IP de l’écouteur. L’équilibreur de charge achemine le trafic vers l’instance de SQL Server qui est à l’écoute sur le port de la sonde. Dans la plupart des cas, un groupe de disponibilité utilise un équilibreur de charge interne. Un équilibreur de charge interne Azure peut héberger une ou plusieurs adresses IP. Chaque adresse IP utilise un port de sonde spécifique. Ce document explique comment utiliser PowerShell pour créer un équilibreur de charge ou ajouter des adresses IP à un équilibreur de charge existant pour les groupes de disponibilité SQL Server. 

L’affectation de plusieurs adresses IP à un équilibreur de charge interne est une nouveauté d’Azure qui n’est disponible que dans le modèle Resource Manager. Pour effectuer cette tâche, vous devez disposer d’un groupe de disponibilité SQL Server déployé sur des machines virtuelles Azure dans le modèle Resource Manager. Les deux machines virtuelles SQL Server doivent appartenir au même groupe à haute disponibilité. Vous pouvez utiliser le [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pour créer automatiquement le groupe de disponibilité dans Azure Resource Manager. Ce modèle crée automatiquement le groupe de disponibilité, y compris l’équilibreur de charge interne. Si vous préférez, vous pouvez [configurer manuellement un groupe de disponibilité AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Cette rubrique requiert que vos groupes de disponibilité soient déjà configurés.  

Rubriques connexes :

* [Configuration de groupes de disponibilité AlwaysOn dans Azure VM (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[!INCLUDE [Start your PowerShell session](../../includes/sql-vm-powershell.md)]

## <a name="configure-the-windows-firewall"></a>Configurer le Pare-feu Windows
Configurez le Pare-feu Windows pour autoriser l’accès à SQL Server. Vous devez configurer le pare-feu pour autoriser les connexions TCP aux ports utilisés par l’instance de SQL Server, ainsi qu’au port utilisé par la sonde de l’écouteur. Pour obtenir des instructions détaillées, consultez [Configurer un Pare-feu Windows pour accéder au moteur de base de données](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Créez une règle entrante pour le port SQL Server et pour le port de la sonde.

## <a name="example-script:-create-an-internal-load-balancer-with-powershell"></a>Exemple de script : Créer un équilibreur de charge interne à l’aide de PowerShell
> [!NOTE]
> Si vous avez créé un groupe de disponibilité à l’aide du [modèle Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) , cette étape est superflue. 
> 
> 

Le script PowerShell suivant crée un équilibreur de charge interne, configure les règles d’équilibrage de charge et affecte une adresse IP pour l’équilibrage de charge. Pour exécuter le script, ouvrez Windows PowerShell ISE, puis collez le script dans le volet Script. Utilisez `Login-AzureRMAccount` pour vous connecter à PowerShell. Si vous possédez plusieurs abonnements Azure, utilisez `Select-AzureRmSubscription ` pour définir l’abonnement. 

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## <a name="example-script:-add-an-ip-address-to-an-existing-load-balancer-with-powershell"></a>Exemple de script : Ajouter une adresse IP à un équilibreur de charge existant à l’aide de PowerShell
Pour utiliser plusieurs groupes de disponibilité, utilisez PowerShell afin d’ajouter une adresse IP supplémentaire à un équilibreur de charge existant. Chaque adresse IP requiert une règle d’équilibrage de charge, un port de sonde et un port frontal propres.

Le port frontal est le port que les applications utilisent pour se connecter à l’instance SQL Server. Les adresses IP de groupes de disponibilité différents peuvent utiliser le même port frontal.

> [!NOTE]
> Pour les groupes de disponibilité SQL Server, chaque adresse IP nécessite un port de sonde spécifique. Par exemple, si une adresse IP sur un équilibreur de charge utilise le port de sonde 59999, aucune autre adresse IP de cet équilibreur de charge ne peut utiliser le port 59999 de la sonde. 
> 
> 

* Pour plus d’informations sur les limites de l’équilibreur de charge, consultez **Adresse IP frontale privée par équilibreur de charge** sous [Limites de réseau – Azure Resource Manager](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).
* Pour plus d’informations sur les limites de groupe de disponibilité, consultez [Restrictions (groupes de disponibilité)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

Le script suivant ajoute une nouvelle adresse IP à un équilibreur de charge existant. Mettez à jour les variables de votre environnement. L’équilibreur de charge interne utilise le port d’écoute pour le port frontal de l’équilibreur de charge. Ce port peut être le port sur lequel SQL Server écoute le trafic. Pour les instances par défaut de SQL Server, il s’agit du port 1433. La règle d’équilibrage de charge d’un groupe de disponibilité nécessite une adresse IP flottante (retour direct du serveur) de sorte que le port de serveur principal est le même que le port frontal.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
``` 



## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configurer le cluster pour qu’il utilise l’adresse IP de l’équilibrage de charge
L’étape suivante consiste à configurer l’écouteur sur le cluster et à le mettre en ligne. Pour ce faire, procédez comme suit : 

1. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement 
2. Mettre l'écouteur en ligne

## <a name="1.-create-the-availability-group-listener-on-the-failover-cluster"></a>1. Créer l’écouteur de groupe de disponibilité sur le cluster de basculement
À cette étape, vous allez ajouter un point d’accès client au cluster de basculement avec le Gestionnaire du cluster de basculement, puis utiliser PowerShell afin de configurer la ressource de cluster pour l’écoute sur le port de sonde. 

* Utilisez le protocole RDP pour vous connecter à la machine virtuelle Azure qui héberge le réplica principal. 
* Ouvrez le Gestionnaire du cluster de basculement.
* sélectionnez le nœud **Réseaux** et notez le nom de réseau du cluster. Utilisez ce nom dans la variable `$ClusterNetworkName` du script PowerShell.
* Développez le nom du cluster, puis cliquez sur **Rôles**.
* Dans le volet **Rôles**, cliquez avec le bouton droit sur le nom du groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès client**.
* Dans la zone **Nom**, créez un nom pour ce nouvel écouteur, puis cliquez à deux reprises sur **Suivant** et cliquez sur **Terminer**. Ne mettez pas l'écouteur ou la ressource en ligne à ce stade.
  
  > [!NOTE]
  > Le nom du nouvel écouteur est le nom du réseau que les applications vont utiliser pour se connecter aux bases de données du groupe de disponibilité SQL Server.
  > 
  > 
* Cliquez sur l'onglet **Ressources** , puis développez le Point d'accès Client vous venez de créer. Cliquez avec le bouton droit sur la ressource IP, puis cliquez sur Propriétés. Notez le nom de l’adresse IP. Vous utiliserez ce nom dans la variable `$IPResourceName` du script PowerShell.
* Dans **IP Address** click **Adresse IP statique** and set the static Adresse IP to the same address that you used when you set the load balancer Adresse IP on the Azure portal. 
* Désactivez NetBIOS pour cette adresse et cliquez sur **OK**. Répétez cette étape pour chaque ressource IP si votre solution couvre plusieurs réseaux virtuels Azure. 
* Créez une dépendance entre la ressource de groupe de disponibilité de SQL Server et l’adresse IP. Cliquez avec le bouton droit sur la ressource sur le gestionnaire de clusters sous l’onglet **Ressources** situé sous **Autres ressources**. 
* Sur le nœud de cluster qui héberge actuellement le réplica principal, ouvrez une fenêtre PowerShell ISE avec élévation de privilèges et collez les commandes suivantes dans un nouveau script. Sous l’onglet **Dépendances** , cliquez sur le nom de l’écouteur. 
  
    ```powershell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
  
    Import-Module FailoverClusters
  
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
* Mettez à jour les variables et exécutez le script PowerShell pour configurer l’adresse IP et le port du nouvel écouteur.
  
  > [!NOTE]
  > Si vos serveurs SQL se trouvent dans différentes régions, vous devez exécuter le script PowerShell à deux reprises. La première fois, utilisez le nom de réseau du cluster, le nom de la ressource IP du cluster et l’adresse IP de l’équilibrage de charge correspondant au premier groupe de ressources. La seconde fois, utilisez le nom de réseau du cluster, le nom de la ressource IP du cluster et l’adresse IP de l’équilibrage de charge correspondant au second groupe de ressources.
  > 
  > 

Maintenant, le cluster a un écouteur de groupe de disponibilité.

## <a name="2.-bring-the-listener-online"></a>2. Mettre l'écouteur en ligne
Une fois l’écouteur de groupe de disponibilité configuré, vous pouvez le mettre en ligne afin que les applications puissent se connecter aux bases de données du groupe de disponibilité avec celui-ci.

* Retournez dans le Gestionnaire du cluster de basculement. Développez les **Rôles** , puis mettez votre groupe de disponibilité en surbrillance. Dans l’onglet **Ressources**, cliquez avec le bouton droit sur le nom de l’écouteur, puis cliquez sur **Propriétés**.
* Cliquez sur l'onglet **Dépendances** . Si plusieurs ressources sont répertoriées, vérifiez que les adresses IP ont des dépendances OR (et non des dépendances AND). Cliquez sur **OK**.
* Cliquez avec le bouton droit sur l'écouteur, puis cliquez sur **Mettre en ligne**.
* Une fois l’écouteur en ligne, allez dans l’onglet **Ressources**, cliquez avec le bouton droit sur le groupe de disponibilité, puis cliquez sur **Propriétés**.
* Créez une dépendance sur la ressource de nom d'écouteur (pas le nom de ressources Adresse IP). Cliquez sur **OK**.
* Lancez SQL Server Management Studio et connectez-vous au réplica principal.
* Accédez à **Haute disponibilité AlwaysOn** | **Groupes de disponibilité** | **Écouteurs de groupe de disponibilité**. 
* Vous devez maintenant voir le nom de l'écouteur que vous avez créé dans le Gestionnaire du cluster de basculement. Cliquez avec le bouton droit sur l’écouteur, puis cliquez sur **Propriétés**.
* Dans le champ **Port**, indiquez le numéro de port de l’écouteur de groupe de disponibilité à l’aide du paramètre $EndpointPort utilisé précédemment (valeur par défaut : 1433), puis cliquez sur **OK**.

Vous avez maintenant un groupe de disponibilité SQL Server dans des machines virtuelles Azure exécutées en mode Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Tester la connexion à l’écouteur
Pour tester la connexion :

1. Envoyez une requête RDP à un serveur SQL qui se trouve dans le même réseau virtuel, mais qui ne possède pas le réplica. Il peut s’agir de l’autre serveur SQL du cluster.
2. Utilisez l’utilitaire **sqlcmd** pour tester la connexion. Par exemple, le script suivant établit une connexion **sqlcmd** avec le réplica principal au moyen de l’écouteur avec une authentification Windows :
   
        sqlmd -S <listenerName> -E
   
    Si l’écouteur utilise un port autre que le port par défaut (1433), spécifiez le port dans la chaîne de connexion. Par exemple, la commande sqlcmd suivante se connecte à un écouteur sur le port 1435 : 
   
        sqlcmd -S <listenerName>,1435 -E

La connexion SQLCMD se connecte automatiquement à l’instance SQL Server hébergeant le réplica principal. 

> [!NOTE]
> Vérifiez que le port spécifié est ouvert sur le pare-feu des deux serveurs SQL. Les deux serveurs requièrent une règle de trafic entrant sur le port TCP utilisé. Pour plus d’informations, consultez [Ajouter ou modifier une règle de pare-feu](http://technet.microsoft.com/library/cc753558.aspx) . 
> 
> 

## <a name="guidelines-and-limitations"></a>Instructions et limitations
Notez les instructions suivantes concernant l’écouteur de groupe de disponibilité dans Azure utilisant l’équilibrage de charge interne :

* Avec un équilibrage de charge interne, vous n’accédez à l’écouteur qu’à partir du même réseau virtuel.

## <a name="for-more-information"></a>Pour plus d’informations
Pour plus d’informations, consultez [Configuration manuelle d’un groupe de disponibilité Always On dans une machine virtuelle Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### <a name="powershell-cmdlets"></a>Applets de commande PowerShell
Utilisez les applets de commande PowerShell suivantes pour créer un équilibreur de charge interne pour les machines virtuelles Azure.

* `New-AzureRmLoadBalancer` crée un équilibreur de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) . 
* `New-AzureRMLoadBalancerFrontendIpConfig` crée une configuration IP frontale pour un équilibreur de charge. Pour plus d’informations, consultez [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) .
* `New-AzureRmLoadBalancerRuleConfig` crée une configuration de règle pour un équilibreur de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) . 
* `New-AzureRMLoadBalancerBackendAddressPoolConfig` crée une configuration de pool d’adresses principales pour un équilibreur de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) . 
* `New-AzureRmLoadBalancerProbeConfig` crée une configuration de sonde pour un équilibreur de charge. Pour plus d’informations, consultez [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) .

Si vous devez supprimer un équilibreur de charge à partir d’un groupe de ressources Azure, utilisez `Remove-AzureRmLoadBalancer`. Pour plus d’informations, consultez [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).

<!--HONumber=Oct16_HO2-->


