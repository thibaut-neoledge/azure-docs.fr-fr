### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure

1. Dans le panneau SQL Server, sous Paramètres, cliquez sur **Pare-feu** pour ouvrir le panneau de pare-feu pour le serveur SQL Server.

    ![sql server firewall](./media/sql-data-warehouse-server-firewall/sql-server-firewall.png)
    
2. Vérifiez l’adresse IP du client affichée et confirmez qu’il s’agit de votre adresse IP sur Internet à l’aide du navigateur de votre choix (demandez « quelle est mon adresse IP »). Parfois, elles ne correspondent pas pour diverses raisons.

    ![your IP address](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. En supposant que les adresses IP correspondent, cliquez sur **Ajouter une adresse IP cliente** sur la barre d’outils.

    ![add client IP](./media/sql-data-warehouse-server-firewall/add-client-ip.png)

    > [!NOTE]
    > Vous pouvez ouvrir le pare-feu sur le serveur SQL (serveur logique) pour une adresse IP unique ou un ensemble d’adresses. Ouvrir le pare-feu permet aux administrateurs et utilisateurs SQL de se connecter à toute base de données sur le serveur pour laquelle ils disposent d’informations d’identification valides.
    >

4. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur, puis sur **OK**.

    ![add client IP](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

<!--HONumber=Jan17_HO3-->


