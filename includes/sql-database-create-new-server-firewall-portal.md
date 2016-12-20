
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Créer une règle de pare-feu au niveau du serveur dans le portail Azure

1. Dans le panneau SQL Server, sous Paramètres, cliquez sur **Pare-feu** pour ouvrir le panneau de pare-feu pour le serveur SQL Server.

    ![sql server firewall](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png)
    
2. Vérifiez l’adresse IP du client affichée et confirmez qu’il s’agit de votre adresse IP sur Internet à l’aide du navigateur de votre choix (demandez « quelle est mon adresse IP »). Parfois, elles ne correspondent pas pour diverses raisons.

    ![your IP address](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. En supposant que les adresses IP correspondent, cliquez sur **Ajouter une adresse IP cliente** sur la barre d’outils.

    ![add client IP](../articles/sql-database/media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > Vous pouvez ouvrir le pare-feu SQL Database sur le serveur pour une adresse IP unique ou un ensemble d’adresses. Ouvrir le pare-feu permet aux administrateurs et utilisateurs SQL de se connecter à toute base de données sur le serveur pour laquelle ils disposent d’informations d’identification valides.
    >

4. Cliquez sur **Enregistrer** sur la barre d’outils pour enregistrer cette règle de pare-feu de niveau serveur, puis sur **OK**.

    ![add client IP](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

> [!Tip]
> Pour trouver un didacticiel, consultez [Didacticiel sur la base de données SQL : création d’un serveur, d’une règle de pare-feu de niveau serveur, d’un exemple de base de données, d’une règle de pare-feu de niveau base de données et connexion à SQL Server](../articles/sql-database/sql-database-get-started.md).    
>


<!--HONumber=Nov16_HO5-->


