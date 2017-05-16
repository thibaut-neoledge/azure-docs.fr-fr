<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### <a name="obtain-the-connection-string-from-the-azure-portal"></a>Obtenir la chaîne de connexion sur le portail Azure
Utilisez le [portail Azure](https://portal.azure.com/) pour obtenir la chaîne de connexion nécessaire pour que votre programme client interagisse avec la base de données SQL Azure :

1. Cliquez sur **PARCOURIR** > **Bases de données SQL**.
   
    ![Sélectionner SQL][1-select-sql]
2. Entrez le nom de votre base de données dans la zone de texte de filtre, en haut à gauche du panneau **Bases de données SQL** .
   
    ![Sélectionner la base de données][2-select-database]
3. Cliquez sur la ligne qui correspond à votre base de données.
4. Une fois que le panneau de votre base de données s’affiche, pour un meilleur confort visuel, vous pouvez cliquer sur les commandes standard de réduction afin de réduire les panneaux que vous avez utilisés pour la navigation et le filtrage de la base de données.
5. Dans le panneau de votre base de données, cliquez sur **Afficher les chaînes de connexion de la base de données**.
6. Si vous prévoyez d’utiliser la bibliothèque de connexions JDBC, copiez la chaîne intitulée **JDBC**.
   
    ![Copier la chaîne de connexion JDBC pour votre base de données][3-get-connection-string]
7. Collez les informations de chaîne de connexion dans le code de votre programme client.  Vous devez remplacer {your_password_here} par votre mot de passe réel.

Pour plus d’informations, consultez :<br/>[Chaînes de connexion et fichiers de configuration](https://msdn.microsoft.com/library/ms378428.aspx).

<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png


[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-jdbc.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
