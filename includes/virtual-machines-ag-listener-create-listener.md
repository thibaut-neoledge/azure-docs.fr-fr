Dans cette étape, vous créez manuellement l’écouteur du groupe de disponibilité dans le Gestionnaire du cluster de basculement et SQL Server Management Studio.

1. Ouvrez le Gestionnaire du cluster de basculement à partir du nœud qui héberge le réplica principal.

2. Sélectionnez le nœud **Réseaux**, puis notez le nom de réseau du cluster. Ce nom est utilisé dans la variable $ClusterNetworkName dans le script PowerShell.

3. Développez le nom du cluster, puis cliquez sur **Rôles**.

4. Dans le volet **Rôles**, cliquez avec le bouton droit sur le nom du groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d’accès client**.
   
    ![Ajouter un point d’accès client pour le groupe de disponibilité](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Dans la zone **Nom**, créez un nom pour ce nouvel écouteur, cliquez à deux reprises sur **Suivant**, puis cliquez sur **Terminer**.  
    Ne mettez pas l'écouteur ou la ressource en ligne à ce stade.

6. Cliquez sur l’onglet **Ressources**, puis développez le point d’accès client vous venez de créer. 
    La ressource d’adresse IP de chaque réseau de cluster dans votre cluster est affichée. S’il s’agit d’une solution basée uniquement sur Azure, une seule ressource d’adresse IP est affichée.

7. Effectuez l’une des actions suivantes :
   
   * Pour configurer une solution hybride :
     
        a. Cliquez avec le bouton droit sur la ressource d’adresse IP qui correspond à votre sous-réseau local, puis sélectionnez **Propriétés**. Notez le nom de l’adresse IP et le nom du réseau.
   
        b. Sélectionnez **Adresse IP statique**, affectez une adresse IP inutilisée, puis cliquez sur **OK**.
 
   * Pour configurer une solution basée uniquement sur Azure :

        a. Cliquez avec le bouton droit sur la ressource d’adresse IP qui correspond à votre sous-réseau Azure, puis sélectionnez **Propriétés**.
       
       > [!NOTE]
       > Si la mise en ligne de l’écouteur échoue par la suite en raison d’un conflit avec l’adresse IP sélectionnée par DHCP, vous pouvez configurer une adresse IP statique valide dans cette fenêtre de propriétés.
       > 
       > 

       b. Dans la même fenêtre de propriétés **Adresse IP**, modifiez le **Nom de l’adresse IP**.  
        Ce nom est utilisé dans la variable $IPResourceName du script PowerShell. Si votre solution couvre plusieurs réseaux virtuels Azure, répétez cette étape pour chaque ressource IP.

