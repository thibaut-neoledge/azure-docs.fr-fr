### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Création d'un point de terminaison TCP pour la machine virtuelle
Pour accéder à SQL Server depuis Internet, la machine virtuelle doit avoir un point de terminaison pour écouter les communications TCP entrantes. Dans cette étape de configuration Azure, le trafic du port TCP entrant est dirigé vers un port TCP accessible à la machine virtuelle.

> [!NOTE]
> Si vous vous connectez dans le même service cloud ou réseau virtuel, vous n’avez pas besoin de créer un point de terminaison accessible publiquement. Dans ce cas, vous pouvez passer à l’étape suivante. Pour plus d'informations, consultez [Scénarios de connexion](../articles/virtual-machines/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. Dans le portail Azure, sélectionnez **Machines virtuelles (classiques)**.
2. Sélectionnez ensuite une machine virtuelle SQL Server.
3. Sélectionnez **Points de terminaison**, puis cliquez sur le bouton **Ajouter** bouton en haut du panneau Points de terminaison.
   
    ![Étapes pour la création d’un point de terminaison sur le portail](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. Dans le panneau **Ajouter un point de terminaison**, fournissez un **Nom**, par exemple SQLEndpoint.
5. Sélectionnez **TCP** pour le **Protocole**.
6. Pour **Port public**, spécifiez un numéro de port comme **57500**.
7. Pour **Port privé**, spécifiez le port d’écoute de SQL Server, par défaut **1433**.
8. Cliquez sur **OK** pour créer le point de terminaison.



<!--HONumber=Nov16_HO3-->


