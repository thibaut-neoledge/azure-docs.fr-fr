L’écouteur de groupe de disponibilité est une adresse IP et un nom réseau sur lesquels le groupe de disponibilité de SQL Server écoute. Pour créer l’écouteur de groupe de disponibilité, procédez comme suit :

1. [Récupérez le nom de la ressource réseau de cluster](#getnet).

1. [Ajoutez le point d’accès client](#addcap).

1. [Configurez la ressource IP du groupe de disponibilité](#congroup).

1. [Créez une dépendance entre la ressource de groupe de disponibilité de SQL Server et le point d’accès client](#dependencyGroup).

1. [Créez une dépendance entre la ressource du point d’accès client et l’adresse IP](#listname).

1. [Définissez les paramètres de cluster dans PowerShell](#setparam).

Des instructions détaillées relatives à chacune de ces procédures sont fournies dans les sections suivantes. 

#### <a name="getnet"></a>Récupérer le nom de la ressource réseau de cluster

1. Utilisez le protocole RDP pour vous connecter à la machine virtuelle Azure qui héberge le réplica principal. 

1. Ouvrez le Gestionnaire du cluster de basculement.

1. sélectionnez le nœud **Réseaux** et notez le nom de réseau du cluster. Utilisez ce nom dans la variable `$ClusterNetworkName` du script PowerShell.

   Dans l’image suivante, le nom réseau du cluster est **Cluster Network 1** :

   ![Nom réseau du cluster](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

#### <a name="addcap"></a>Ajouter le point d’accès client

Le point d’accès client est le nom réseau que les applications utilisent pour se connecter aux bases de données dans un groupe de disponibilité. Créez le point d’accès client dans le Gestionnaire du cluster de basculement . 

1. Développez le nom du cluster, puis cliquez sur **Rôles**.

1. Dans le volet **Rôles**, cliquez avec le bouton droit sur le nom du groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès client**.

   ![Point d’accès client](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

1. Dans la zone **Nom**, créez un nom pour ce nouvel écouteur. 

   Le nom du nouvel écouteur est le nom réseau que les applications utilisent pour se connecter aux bases de données dans le groupe de disponibilité de SQL Server.
   
   Pour terminer la création de l’écouteur, cliquez sur **Suivant** deux fois, puis cliquez sur **Terminer**. Ne mettez pas l'écouteur ou la ressource en ligne à ce stade.
   
#### <a name="congroup"></a>Configurer la ressource IP du groupe de disponibilité

1. Cliquez sur l’onglet **Ressources**, puis développez le point d’accès client que vous avez créé. Le point d’accès client est hors connexion.

   ![Point d’accès client](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

1. Cliquez avec le bouton droit sur la ressource IP, puis cliquez sur Propriétés. Notez le nom de l’adresse IP. Utilisez ce nom dans la variable `$IPResourceName` du script PowerShell.

1. Sous **Adresse IP**, cliquez sur **Adresse IP statique**. Définissez l’adresse IP sur l’adresse que vous avez utilisée pour définir celle de l’équilibreur de charge sur le portail Azure.

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

<!-----------------------I don't see this option on server 2016
1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
------------------------->

#### <a name = "dependencyGroup"></a>Créer une dépendance entre la ressource de groupe de disponibilité de SQL Server et le point d’accès client

1. Dans le Gestionnaire du cluster de basculement, cliquez sur **Rôles**, puis sur votre groupe de disponibilité.

1. Dans l’onglet **Ressources**, cliquez avec le bouton droit sur la ressource de groupe de disponibilité sous **Nom du serveur**, puis cliquez sur **Propriétés**. 

1. Dans l’onglet Dépendances, ajoutez la ressource de nom. Cette ressource est le point d’accès client. 

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

1. Cliquez sur **OK**.

#### <a name="listname"></a>Créer une dépendance entre la ressource du point d’accès client et l’adresse IP

1. Dans le Gestionnaire du cluster de basculement, cliquez sur **Rôles**, puis sur votre groupe de disponibilité. 

1. Dans l’onglet **Ressources**, cliquez avec le bouton droit sur la ressource de point d’accès client sous **Nom du serveur**, puis cliquez sur **Propriétés**. 

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

1. Cliquez sur l'onglet **Dépendances** . Définissez une dépendance du nom de ressource de l’écouteur. Si plusieurs ressources sont répertoriées, vérifiez que les adresses IP ont des dépendances OR (et non des dépendances AND). Cliquez sur **OK**. 

   ![Ressource IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

1. Cliquez avec le bouton droit sur l'écouteur, puis cliquez sur **Mettre en ligne**. 

#### <a name="setparam"></a>Définir les paramètres de cluster dans PowerShell

1. Copiez le script PowerShell suivant dans l’un de vos serveurs SQL Server. Mettez à jour les variables de votre environnement.     
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

2. Définissez les paramètres du cluster en exécutant le script PowerShell sur l’un des nœuds du cluster.  

> [!NOTE]
> Si vos serveurs SQL se trouvent dans différentes régions, vous devez exécuter le script PowerShell à deux reprises. La première fois, utilisez `$ILBIP` et `$ProbePort` à partir de la première région. La seconde fois, utilisez `$ILBIP` et `$ProbePort` à partir de la seconde région. Le nom réseau du cluster et le nom de ressource IP du cluster sont identiques. 


