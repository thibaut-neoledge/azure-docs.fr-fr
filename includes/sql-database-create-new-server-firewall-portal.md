
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-08-01 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## Créer un pare-feu SQL Azure au niveau du serveur

Exécutez les étapes suivantes dans le portail Azure pour créer une règle de pare-feu au niveau du serveur qui autorise les connexions depuis une adresse IP individuelle (votre ordinateur client) ou une plage d’adresses IP entière à un serveur logique de base de données SQL.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](http://portal.azure.com).
2. Dans le panneau par défaut, cliquez sur **Serveurs SQL**.

  	![Nouveau pare-feu de serveur](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)

3. Dans le panneau **Serveurs SQL**, cliquez sur le serveur sur lequel créer la règle de pare-feu.

 	![Nouveau pare-feu de serveur](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)

4. Passez en revue les propriétés de votre serveur.

 	![Nouveau pare-feu de serveur](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)

5. Dans le panneau **Paramètres**, cliquez sur **Pare-feu**.

 	![Nouveau pare-feu de serveur](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)

 	> [AZURE.NOTE] Vous pouvez également accéder au panneau **Paramètres de pare-feu** du niveau serveur à partir de la barre d’outils du panneau **Base de données**.

6. Cliquez sur **Ajouter l’IP du client** pour qu’Azure crée une règle pour l’adresse IP du client.

      ![Nouveau pare-feu de serveur](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)

7. Pour autoriser l’accès à une plage d’adresses IP, cliquez sur l’adresse IP qui a été ajoutée pour modifier l’adresse du pare-feu.

      ![Nouveau pare-feu de serveur](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)

8. Cliquez sur **Enregistrer** pour créer la règle de pare-feu au niveau du serveur.

     ![Nouveau pare-feu de serveur](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)

	>[AZURE.IMPORTANT] Votre adresse IP du client peut changer de temps à autre, et vous ne serez peut-être pas en mesure d’accéder à votre serveur jusqu’à ce que vous créiez une nouvelle règle de pare-feu. Vous pouvez vérifier votre adresse IP à l’aide de [Bing](http://www.bing.com/search?q=my%20ip%20address). Ajoutez ensuite une adresse IP ou une plage d’adresses IP. Pour plus d’informations, voir l’article sur la [gestion des paramètres de pare-feu](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal)

<!----HONumber=AcomDC_0912_2016-->