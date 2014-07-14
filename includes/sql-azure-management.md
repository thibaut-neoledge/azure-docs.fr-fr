
# Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio

Vous pouvez utiliser le portail de gestion de base de données SQL Azure ou l'application cliente SSMS (SQL Server Management Studio) pour administrer vos abonnements à la base de données SQL, ainsi que créer et gérer des bases de données et les serveurs logiques associés. Les instructions ci-dessous expliquent comment utiliser Management Studio pour gérer des bases de données et des serveurs logiques de base de données SQL. Pour plus d'informations sur l'utilisation de connexions de base de données SQL dans du code d'application, consultez la page [Utilisation de la base de données SQL Windows][1].

 
<div  class="dev-callout-new-collapsed">
<strong>Remarque<span>Cliquez pour réduire</span></strong>
<div  class="dev-callout-content">
<p>Vous pouvez utiliser indifféremment SQL Server 2012 ou la version SQL Server 2008 R2 de Management Studio. Les versions antérieures ne sont pas prises en charge.</p>
</div>

 Cette procédure comprend les étapes suivantes :

* [Étape 1 : obtention de SQL Server Management Studio](#Step1)
* [Étape 2 : connexion à la base de données SQL](#Step2)
* [Étape 3 : création et gestion de bases de données](#Step3)
* [Étape 4 : création et gestion de connexions](#Step4)
* [Étape 5 : contrôle de la base de données SQL au moyen de vues de gestion dynamique](#Step5)

<h2><a id="Step1" name="Step1"> </a>Étape 1 : obtention de Management Studio</h2>


Management Studio est un environnement intégré permettant de gérer des bases de données SQL. Lors de la gestion de bases de données dans Azure, vous pouvez utiliser l'application Management Studio installée avec SQL Server ou télécharger la version gratuite de SSMSE (SQL Server 2012 Management Studio Express) . La procédure ci-dessous décrit l'installation de SSMSE.

1.  Sur la page [Microsoft SQL Server 2012 Express][], sélectionnez la version x86 de Management Studio si vous exécutez un système d'exploitation 32 bits ou la version x64 si vous exécutez un système d'exploitation 64 bits. Cliquez sur **Télécharger**, puis à l'invite, exécutez le programme d'installation.

2.  Cliquez sur **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante**, puis sur **OK**.

3.  Acceptez les termes du contrat de licence, puis cliquez sur **OK**.

4.  Cliquez sur **Installer** pour installer les fichiers requis par le programme d'installation de SQL Server.

5.  Dans l'écran **Sélection de composant**, **Outils de gestion - De base** est présélectionné. Ceci est dû au fait que vous exécutez le programme d'installation pour Management Studio. Si vous exécutez le programme d'installation pour l'ensemble de SQL Server Express, choisissez l'option **Outils de gestion - De base** et cliquez sur **Suivant**.

6.  Dans l'écran **Rapport d'erreurs**, vous avez la possibilité de choisir d'envoyer les rapports d'erreurs à Microsoft. Cette action n'est pas obligatoire pour pouvoir utiliser SSMSE. Cliquez sur **Suivant** pour démarrer l'installation.

7.  Une fois l'installation terminée, la page **Terminé** s'affiche. Cliquez sur **Fermer**.

<h2><a id="Step2" name="Step2"> </a>Étape 2 : connexion à la base de données SQL</h2>


La connexion à la base de données SQL requiert que vous connaissiez le nom du serveur sous Azure. Vous devrez peut-être vous connecter au portail pour obtenir ces informations.

1.  Connectez-vous au [portail de gestion Azure][].

2.  Dans le volet gauche, cliquez sur **Bases de données SQL**.

3.  Sur la page Bases de données SQL, cliquez sur **SERVEURS** en haut de la page pour répertorier tous les serveurs associés à votre abonnement. Recherchez le nom du serveur auquel vous voulez vous connecter et copiez-le dans le Presse-papiers.
    
    Configurez ensuite le pare-feu de votre base de données SQL pour autoriser les connexions à partir de votre machine locale. Pour cela, ajoutez les adresses IP de vos machines locales à la liste des exceptions du pare-feu.

4.  Sur la page d'accueil Bases de données SQL, cliquez sur **SERVEURS**, puis sur le serveur auquel vous voulez vous connecter.

5.  Cliquez sur **Configurer** en haut de la page.

6.  Copiez l'adresse IP dans le champ Adresse IP actuelle du client.

7.  Dans la page Configurer, la section **Adresses IP autorisées** inclut trois zones vous permettant de spécifier un nom de règle et une plage d'adresses IP comme valeurs de début et de fin. Comme nom de règle, vous pouvez entrer le nom de l'ordinateur. Pour les plages de début et de fin, collez l'adresse IP de votre ordinateur dans les deux zones, puis activez la case à cocher qui s'affiche.
    
    Le nom de la règle doit être unique. S'il s'agit de votre ordinateur de développement, vous pouvez entrer l'adresse IP à la fois dans la zone de début de la plage d'adresses IP et dans la zone de fin de la plage d'adresses IP. Sinon, vous pouvez entrer une plage plus étendue d'adresses IP afin de prendre en compte les connexions d'ordinateurs supplémentaires de l'organisation.

8.  Cliquez sur **ENREGISTRER** en bas de la page.
    
    **Remarque :** il peut y avoir un délai maximal de cinq minutes pour que les modifications apportées au pare-feu entrent en vigueur.
    
    Vous êtes à présent prêt à vous connecter la base de données SQL au moyen de Management Studio.

9.  Dans la barre des tâches, cliquez sur **Démarrer**, pointez sur **Tous les programmes**, pointez sur **Microsoft SQL Server 2012**, puis cliquez sur **SQL Server Management Studio**.

10. Dans **Se connecter au serveur**, spécifiez le nom complet du serveur sous la forme *nom\_serveur*.database.windows.net. Dans Azure, le nom du serveur est une chaîne générée automatiquement composée de caractères alphanumériques.

11. Sélectionnez **Authentification SQL Server**.

12. Dans la zone **Connexion**, entrez le nom de connexion de l'administrateur SQL Server que vous avez spécifié sur le portail lorsque vous avez créé votre serveur sous la forme *connexion*@*nom\_serveur*.

13. Dans la zone **Mot de passe**, entrez le mot de passe que vous avez spécifié sur le portail lors de la création de votre serveur.

14. Cliquez sur **Se connecter** pour établir la connexion.

Dans Azure, chaque serveur logique de base de données SQL est une abstraction qui définit un regroupement de bases de données. L'emplacement physique de chaque base de données peut correspondre à n'importe quel ordinateur du centre de données.

Dans les versions précédentes, vous deviez vous connecter directement à la base de données **principale** lors de la configuration de la connexion dans Management Studio. Cette étape n'est plus nécessaire. Les connexions s'effectuent à présent sur la base du nom du serveur, du type d'authentification et des informations d'identification de l'administrateur.

Plusieurs des Assistants SSMS que vous pouvez utiliser pour des tâches telles que la création et la modification de connexions et de bases de données dans une base de données SQL Server ne sont pas disponibles pour les bases de données SQL dans Azure. Par conséquent, il vous faudra utiliser les instructions Transact-SQL pour effectuer ces tâches. Les étapes ci-dessous fournissent des exemples de ces instructions. Pour plus d'informations sur l'utilisation de Transact-SQL avec la base de données SQL, y compris des informations sur les commandes prises en charge, consultez la rubrique [Référence Transact-SQL (Base de données SQL)][].

<h2><a id="Step3" name="Step3"> </a>Étape 3 : création et gestion de bases de données</h2>


Lorsque vous êtes connecté à la base de données **principale**, vous pouvez créer des bases de données sur le serveur et modifier ou supprimer les bases de données existantes. La procédure ci-dessous décrit la façon d'accomplir plusieurs tâches courantes de gestion de base de données par le biais de Management Studio. Pour effectuer ces tâches, vérifiez que vous êtes connecté à la base de données
**principale** avec la connexion principale de niveau serveur que vous avez créée lors de la configuration de votre serveur.

Pour ouvrir une fenêtre de requête dans Management Studio, ouvrez le dossier Bases de données, cliquez avec le bouton droit sur
**principal**, puis cliquez sur **Nouvelle requête**.

Cliquez sur **Exécuter** pour exécuter la requête.

* Utilisez l'instruction **CREATE DATABASE** pour créer une base de données. Pour plus d'informations, consultez la rubrique [CREATE DATABASE (Base de données SQL)][]. L'instruction ci-dessous crée une base de données appelée **maBDTest** et spécifie qu'il s'agit d'une base de données Édition avec une taille maximale de 1 Go.
  
      	CREATE DATABASE maBDTest
      	(MAXSIZE=1GB,
      	EDITION=’web’);

* Utilisez l'instruction **ALTER DATABASE** pour modifier une base de données existante, par exemple si vous voulez modifier le nom, la taille maximale ou l'édition (entreprise ou Web) de la base de données. Pour plus d'informations, consultez la rubrique [ALTER DATABASE (Base de données SQL)][]. L'instruction ci-dessous modifie la base de données que vous avez créée à l'étape précédente pour définir la taille maximale sur 5 Go.
  
      	ALTER DATABASE maBDTest
      	MODIFY
      	(MAXSIZE=5GB,
       	EDITION=’web’);

* Utilisez l'instruction **DROP DATABASE** pour supprimer une base de données existante. Pour plus d'informations, consultez la rubrique [DROP DATABASE (Base de données SQL)][]. L'instruction ci-dessous supprime la base de données **maBDTest**, mais ne la supprimez pas maintenant car vous en aurez besoin pour créer des connexions à la prochaine étape.
  
      	DROP DATABASE maBDTest;

* La base de données principale dispose d'une vue **sys.databases** vous permettant d'afficher des informations sur toutes les bases de données. Pour afficher toutes les bases de données existantes, exécutez l'instruction suivante :
  
      	SELECT * FROM sys.databases;

* Dans la base de données SQL, l'instruction **USE** ne permet pas de passer d'une base de données à une autre. À la place, vous devez créer une connexion directe à la base de données cible.

 
<div  class="dev-callout-new">
 <strong>Remarque<span>Cliquez pour réduire</span></strong>
 <div  class="dev-callout-content">
   <p>Plusieurs des instructions Transact-SQL permettant de créer ou de modifier une base de données doivent être exécutées au sein de leur propre lot et ne peuvent pas être regroupées avec d’autres instructions Transact-SQL. Pour plus d’informations, consultez les informations relatives aux instructions, disponibles à partir des liens proposés ci-dessus.</p>
</div>

 <h2><a id="Step4" name="Step4"> </a>Étape 4 : création et gestion de connexions</h2>


La base de données principale effectue le suivi des connexions et indique celles autorisées à créer des bases de données ou d'autres connexions. Gérez les connexions en vous connectant à la base de données **principale** avec la connexion principale de niveau serveur que vous avez créée lors de la configuration de votre serveur. Vous pouvez utiliser les instructions **CREATE LOGIN**, **ALTER LOGIN** ou **DROP LOGIN** pour exécuter des requêtes dans la base de données en vue de gérer les connexions sur l'ensemble du serveur. Pour plus d'informations, consultez la rubrique [Gestion des bases de données et des connexions dans la base de données SQL][].

* Utilisez l'instruction **CREATE LOGIN** pour créer une connexion de niveau serveur. Pour plus d'informations, consultez la rubrique [CREATE LOGIN (Base de données SQL)][]. L'instruction ci-dessous crée une connexion intitulée **connexion1**. Remplacez **motdepasse1** par le mot de passe de votre choix.
  
      	CREATE LOGIN connexion1 WITH password=’motdepasse’;

* Utilisez l'instruction **CREATE USER** pour octroyer des autorisations au niveau de la base de données. Toutes les connexions doivent être créées dans la base de données **principale**, mais pour qu'une connexion permette d'accéder à une autre base de données, vous devez lui octroyer des autorisations de niveau de base de données en utilisant l'instruction **CREATE USER** dans cette base de données. Pour plus d'informations, consultez la rubrique [CREATE USER (Base de données SQL)][].

* Pour fournir à connexion1 des autorisations d'accès à une base de données appelée **maBDTest**, procédez comme suit :
  
  1.  Actualisez l'Explorateur d'objets pour afficher la base de données **maBDTest** que vous venez de créer. Elle doit apparaître en dessous du dossier **Bases de données système** contenant la base de données **principale**.
  
  	Si vous avez fermé la connexion, vous pouvez vous reconnecter en sélectionnant **Connecter l'Explorateur d'objets** dans le menu Fichier. Répétez les instructions de l'[Étape 2 : Connexion à la base de données SQL](#Step2) pour vous connecter à la base de données.
  
  1.  Cliquez avec le bouton droit sur **maBDTest** et sélectionnez **Nouvelle requête**.
  
  2.  Appliquez l'instruction suivante à la base de données maBDTest pour créer un utilisateur de base de données nommé **utilisateur\_connexion1** correspondant à la connexion de niveau serveur **connexion1**.
      
          CREATE USER utilisateur_connexion1 FROM LOGIN connexion1;

* Utilisez la procédure stockée **sp\_addrolemember** pour fournir au compte d'utilisateur le niveau approprié d'autorisations dans la base de données. Pour plus d'informations, consultez la rubrique [sp\_addrolemember (Transact-SQL)][]. L'instruction ci-dessous fournit à **utilisateur\_connexion1** des autorisations d'accès en lecture seule à la base de données en ajoutant **utilisateur\_connexion1** au rôle **db\_datareader**.
  
      	exec sp_addrolemember ’db_datareader’, ’utilisateur_connexion1’;    

* Utilisez l'instruction **ALTER LOGIN** pour modifier une connexion existante, par exemple si vous voulez modifier le mot de passe de la connexion. Pour plus d'informations, consultez la rubrique [ALTER LOGIN (Base de données SQL)][]. L'instruction **ALTER LOGIN** doit être exécutée sur la base de données **principale**. Revenez à la fenêtre de requête qui est connectée à cette base de données.
  
    L'instruction ci-dessous modifie la connexion **connexion1** pour réinitialiser le mot de passe. Remplacez **nouveauMotPasse** par le mot de passe de votre choix et **ancienMotPasse** par le mot de passe actuel de la connexion.
  
      	ALTER LOGIN connexion1
      	WITH PASSWORD = ’nouveauMotPasse’
      	OLD_PASSWORD = ’ancienMotPasse’;

* Utilisez l'instruction **DROP LOGIN** pour supprimer une connexion existante. La suppression d'une connexion au niveau du serveur a également pour effet de supprimer tous les comptes d'utilisateur de base de données associés. Pour plus d'informations, consultez la rubrique [DROP DATABASE (Base de données SQL)][]. L'instruction **DROP LOGIN** doit être exécutée sur la base de données **principale**. L'instruction ci-dessous supprime la connexion **connexion1**.
  
      	DROP LOGIN connexion1;

* La base de données principale dispose d'une vue **sys.sql\_logins** vous permettant d'afficher les connexions. Pour afficher toutes les connexions existantes, exécutez l'instruction suivante :
  
      	SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Étape 5: contrôle de la base de données SQL au moyen de vues de gestion dynamique</h2>


La base de données SQL prend en charge plusieurs vues de gestion dynamique vous permettant de surveiller une base de données individuelle. Voici quelques exemples du type de données de surveillance que vous pouvez récupérer au moyen de ces vues. Pour plus d'informations et pour accéder à des exemples d'utilisations, consultez la rubrique [Contrôle de la base de données SQL Windows Azure à l'aide de vues de gestion dynamique][].

* L'interrogation d'une vue de gestion dynamique nécessite des autorisations **VIEW DATABASE STATE**. Pour octroyer l'autorisation **VIEW DATABASE STATE** à un utilisateur de base de données spécifique, connectez-vous à la base de données que vous voulez gérer avec votre connexion de principe de niveau serveur et exécutez l'instruction suivante dans la base de données :
  
     	 GRANT VIEW DATABASE STATE TO utilisateur_connexion1;

* Calculez la taille de la base de données au moyen de la vue **sys.dm\_db\_partition\_stats**. La vue **sys.dm\_db\_partition\_stats** renvoie les informations de page et de nombre de lignes pour chaque partition de la base de données, vous permettant de calculer la taille de la base de données. La requête suivante renvoie la taille de votre base de données en mégaoctets :
  
      	SELECT SUM(reserved_page_count)*8.0/1024
      	FROM sys.dm_db_partition_stats;   

* Utilisez les vues **sys.dm\_exec\_connections** et **sys.dm\_exec\_sessions** pour extraire les informations concernant les connexions utilisateur actuelles et les tâches internes associées à la base de données. La requête suivante renvoie des informations sur la connexion actuelle :
  
      	SELECT
          	e.connection_id,
          	s.session_id,
          	s.login_name,
          	s.last_request_end_time,
          	s.cpu_time
      	FROM
          	sys.dm_exec_sessions s
          	INNER JOIN sys.dm_exec_connections e
            	ON s.session_id = e.session_id;

* Utilisez la vue **sys.dm\_exec\_query\_stats** pour extraire les statistiques de performance consolidées pour les plans de requête mise en cache. La requête suivante renvoie des informations relatives aux cinq requêtes principales classées sur la base du temps processeur moyen.
  
      	SELECT TOP 5 query_stats.query_hash AS "Query Hash",
          	SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
          	MIN(query_stats.statement_text) AS "Statement Text"
      	FROM
          	(SELECT QS.*,
          	SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
          	((CASE statement_end_offset
              	WHEN -1 THEN DATALENGTH(ST.text)
              	ELSE QS.statement_end_offset END
                  	- QS.statement_start_offset)/2) + 1) AS statement_text
           	FROM sys.dm_exec_query_stats AS QS
           	CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
      	GROUP BY query_stats.query_hash
      	ORDER BY 2 DESC;

<h2>Ressources supplémentaires</h2>


* [Présentation de la base de données SQL][]
* [Gestion des bases de données et des connexions dans la base de données SQL][]
* [Contrôle de la base de données SQL Windows Azure à l'aide de vues de gestion dynamique][]
* [Modèle de déploiement de la base de données SQL][]
* [Ajout d'utilisateurs à votre base de données SQL][]
* [Référence Transact-SQL (Base de données SQL)][]
  

   
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
  [Programme d'installation SSMS - Sélectionner le type d'installation]: /media/installer_installation_type.png
  [Programme d'installation SSMS - Sélection de composant]: /media/installer_feature_selection.png
  [Programme d'installation SSMS Installer - Installation terminée]: /media/installer_completed.png
  [Portail de gestion Azure]: http://manage.windowsazure.com/
  [Obtention du nom du serveur de base de données SQL à partir du portail de gestion]: /media/portal_get_database_name.png
  [Connexion à SSMS]: /media/ssms_connect.png
  [Connexion à SSMS -- propriétés]: /media/ssms_connect_properties.png
  [Référence Transact-SQL (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336281.aspx
  [CREATE DATABASE (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394109.aspx
  [DROP DATABASE (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336259.aspx
  [Gestion des bases de données et des connexions dans la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336268.aspx
  [CREATE USER (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/fr-fr/library/ms187750.aspx
  [ALTER LOGIN (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336254.aspx
  [Contrôle de la base de données SQL Windows Azure à l'aide de vues de gestion dynamique]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394114.aspx
  [Présentation de la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336230.aspx
  [Modèle de déploiement de la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/ee336227.aspx
  [Ajout d'utilisateurs à votre base de données SQL]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx



[1]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-azure/