
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Manage server-level firewall rules through the new Azure portal
-->


1. Connectez-vous au [portail Azure](http://portal.azure.com/) sur http://portal.azure.com/.

2. Dans la bannière de gauche, cliquez sur **PARCOURIR TOUT**. Le panneau **Parcourir** s’affiche.

3. Faites défiler l’écran, puis cliquez sur **Serveurs SQL**. Le panneau **Serveurs SQL** s’affiche.

	![Rechercher votre serveur Azure SQL Database sur le portail][b21-FindServerInPortal]

4. Pour plus de commodité, cliquez sur la commande de réduction sur le panneau **Parcourir** précédent.

5. Dans la zone de texte de filtre, tapez les premières lettres du nom de votre serveur. La ligne correspondante s’affiche.

6. Cliquez dessus. Un panneau dédié à votre serveur s’affiche.

7. Dans ce panneau, cliquez sur **Paramètres**. Le panneau **Paramètres** s’affiche.

8. Cliquez sur **Pare-feu**. Le panneau **Paramètres du pare-feu** s’affiche.

	![Cliquer sur Paramètres, puis sur Pare-feu][b31-SettingsFirewallNavig]

9. Cliquez sur **Ajouter une adresse IP cliente**. Dans la première zone de texte, tapez un nom pour votre nouvelle règle.

10. Tapez les valeurs d’adresse IP basse et haute de la plage que vous souhaitez autoriser.
 - Pour des raisons pratiques, vous pouvez terminer les valeurs basse et haute par **.0** et **.255**, respectivement. 

	![Ajouter une plage d’adresses IP à autoriser][b41-AddRange]

11. Cliquez sur **Enregistrer**.



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=AcomDC_1223_2015-->