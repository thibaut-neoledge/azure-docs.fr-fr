<!--
includes/sql-database-include-connection-string-20-portalshots.md

Latest Freshness check:  2015-09-02 , GeneMi.

## Connection string
-->


### Obtenir la chaîne de connexion sur le portail Azure


Utilisez le [portail Azure en version préliminaire](https://portal.azure.com/) pour obtenir la chaîne de connexion nécessaire pour que votre programme client interagisse avec Azure SQL Database :


1. Cliquez sur **PARCOURIR** > **Bases de données SQL**.

    ![Sélectionner SQL][1-select-sql]

2. Entrez le nom de votre base de données dans la zone de texte de filtre, en haut à gauche du panneau **Bases de données SQL**.

    ![Sélectionner la base de données][2-select-database]]

3. Cliquez sur la ligne qui correspond à votre base de données.

4. Une fois que le panneau de votre base de données s’affiche, pour un meilleur confort visuel, vous pouvez cliquer sur les commandes standard de réduction afin de réduire les panneaux que vous avez utilisés pour la navigation et le filtrage de la base de données.

5. Dans le panneau de votre base de données, cliquez sur **Afficher les chaînes de connexion de la base de données**.

6. Si vous prévoyez d’utiliser la bibliothèque de connexions PHP, copiez la chaîne intitulée **PHP**.

	![Copier la chaîne de connexion PHP pour votre base de données][3-get-connection-string]

7. Collez les informations de chaîne de connexion dans le code de votre programme client. Vous devez remplacer {your\_password\_here} par votre mot de passe réel.


<!--
Could not find a good link for PHP

For more information, see:<br/>[Connection Strings and Configuration Files](https://msdn.microsoft.com/library/ms378428.aspx).
-->


<!-- Image references. -->

[1-select-sql]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-sql.png

[2-select-database]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-select-database.PNG

[3-get-connection-string]: ./media/sql-database-include-connection-string-20-portalshots/connection-string-php.PNG


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->

<!---HONumber=AcomDC_0128_2016-->