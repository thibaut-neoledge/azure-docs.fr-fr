Dans cette étape, vous créez manuellement l'écouteur du groupe de disponibilité dans le Gestionnaire du cluster de basculement et SQL Server Management Studio (SSMS).

1. Ouvrez le Gestionnaire du cluster de basculement à partir du nœud qui héberge le réplica principal.

1. sélectionnez le nœud **Réseaux** et notez le nom de réseau du cluster. Ce nom sera utilisé dans la variable $ClusterNetworkName dans le script PowerShell.

1. Développez le nom du cluster, puis cliquez sur **Rôles**.

1. Dans le volet**Rôles**, cliquez avec le bouton droit sur le nom du groupe de disponibilité, puis sélectionnez **Ajouter une ressource** > **Point d'accès client**.

	![Ajouter un Point d'accès client pour le groupe de disponibilité](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

1. Dans la zone **Nom**, créez un nom pour ce nouveau port d'écoute, puis cliquez à deux reprises sur **Suivant** et cliquez sur **Terminer**. Ne mettez pas l'écouteur ou la ressource en ligne à ce stade.

1. Cliquez sur l'onglet **Ressources**, puis développez le Point d'accès Client vous venez de créer. Vous verrez la ressource **Adresse IP** pour chacun des réseaux de cluster dans votre cluster. S'il s'agit d'une solution basée uniquement sur Azure, vous verrez seulement une ressource Adresse IP.

1. Si vous configurez une solution hybride, continuez avec cette étape. Si vous configurez une solution basée uniquement sur Azure, passez à l'étape suivante.
	 - Cliquez avec le bouton droit sur la ressource Adresse IP qui correspond à votre sous-réseau local, puis sélectionnez **Propriétés**. Notez le nom de l'adresse IP et le nom du réseau.
	 - Sélectionnez **Adresse IP statique**, affectez une adresse IP inutilisée, puis cliquez sur **OK**.

1. Cliquez avec le bouton droit sur la ressource Adresse IP qui correspond à votre sous-réseau Azure, puis sélectionnez Propriétés.
	>[AZURE.NOTE] Si la mise en ligne de l'écouteur échoue par la suite en raison d'un conflit avec l'adresse IP sélectionnée par DHCP, vous pouvez configurer une adresse IP statique valide dans cette fenêtre de propriétés.

1. Dans la même fenêtre de propriétés **Adresse IP**, modifiez le **Nom de l'adresse IP**. Ce nom d'adresse IP sera utilisé dans la variable **$IPResourceName** du script PowerShell. Répétez cette étape pour chaque ressource IP si votre solution couvre plusieurs réseaux virtuels Azure.
<!-------HONumber=Oct15_HO3-->