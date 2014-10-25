# Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio

Vous pouvez utiliser le portail de gestion de base de données SQL Azure ou l’application cliente SSMS (SQL Server Management Studio) pour administrer vos abonnements à la base de données SQL, ainsi que créer et gérer des bases de données et les serveurs logiques associés. Les instructions ci-dessous expliquent comment utiliser Management Studio pour gérer des bases de données et des serveurs logiques de base de données SQL.

<div class="dev-callout-new-collapsed">
<strong>Remarque<span>Cliquez pour r&eacute;duire</span></strong>
<div class="dev-callout-content">
<p>Vous pouvez utiliser la versions SQL Server&nbsp;2014, SQL Server&nbsp;2012 ou SQL Server&nbsp;2008 R2 de SSMS. Les versions ant&eacute;rieures ne sont pas prises en charge.</p>
</div>

</div>

Cette rubrique comprend les étapes suivantes :

-   [Étape 1 : obtention de SQL Server Management Studio][]
-   [Étape 2 : connexion à la base de données SQL][]
-   [Étape 3 : création et gestion de bases de données][]
-   [Étape 4 : création et gestion de connexions][]
-   [Étape 5 : surveillance de la base de données SQL au moyen de vues de gestion dynamique][]

## <span id="Step1"></span> </a>Étape 1 : obtention de Management Studio

Management Studio est un environnement intégré permettant
de gérer des bases de données SQL. Lors de la gestion de bases de données
dans Azure, vous pouvez utiliser l'application Management Studio installée
avec SQL Server ou télécharger la version gratuite de SSMSE (SQL Server 2012 Management Studio Express). La procédure ci-dessous décrit
l'installation de SSMSE.

1.  Sur la page [Microsoft SQL Server 2012 Express][], sélectionnez la version x86 de Management Studio si vous utilisez un système d'exploitation 32 bits ou la version x64 si vous utilisez un système d'exploitation 64 bits. Cliquez sur **Télécharger**, puis à l’invite, exécutez le programme d’installation.

2.  Cliquez sur **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une
    installation existante**, puis sur **OK**.

3.  Acceptez les termes du contrat de licence, puis cliquez sur **Suivant**.

4.  Cliquez sur **Installer** pour installer les fichiers requis par le programme d’installation de SQL Server.

5.  Dans l'écran **Sélection de composant**, **Outils de gestion -
    De base** est présélectionné. Ceci est dû au fait que vous exécutez le programme d’installation pour Management Studio. Si vous exécutez le programme d’installation pour l’ensemble de SQL Server Express, choisissez l’option **Outils de gestion - De base** et cliquez sur **Suivant**.

6.  Dans l'écran **Création de rapports d'erreurs**, vous avez la possibilité de choisir
    d'envoyer des rapports d’erreurs à Microsoft. Cette action n’est pas obligatoire pour pouvoir utiliser SSMSE. Cliquez sur
    **Suivant** pour démarrer l’installation.

7.  Une fois l’installation terminée, la page **Terminé**
     s’affiche. Cliquez sur **Fermer**.

## <span id="Step2"></span> </a>Étape 2 : Connexion à la base de données SQL

La connexion à la base de données SQL requiert que vous connaissiez le nom du serveur sous Azure. Vous devrez peut-être vous connecter au portail pour obtenir ces informations.

1.  Connectez-vous au [portail de gestion Azure][].

2.  Dans le volet gauche, cliquez sur **Bases de données SQL**.

3.  Sur la page Bases de données SQL, cliquez sur **SERVEURS** en haut de la page pour répertorier tous les serveurs associés à votre abonnement. Recherchez le nom du serveur auquel vous voulez vous connecter et copiez-le dans le Presse-papiers.

    Configurez ensuite le pare-feu de votre base de données SQL
    pour autoriser les connexions à partir de votre machine locale. Pour cela, ajoutez les adresses IP de vos machines locales à la liste des exceptions du pare-feu.

4.  Sur la page d’accueil Bases de données SQL, cliquez sur **SERVEURS**, puis sur le serveur auquel vous voulez vous connecter.

5.  Cliquez sur **Configurer** en haut de la page.

6.  Copiez l’adresse IP dans le champ Adresse IP actuelle du client.

7.  Dans la page Configurer, la section **Adresses IP autorisées** inclut trois zones vous permettant de spécifier un nom de règle et une plage d’adresses IP comme valeurs de début et de fin. Comme nom de règle, vous pouvez entrer le nom de l’ordinateur. Pour les plages de début et de fin, collez l’adresse IP de votre ordinateur dans les deux zones, puis cochez la case qui s’affiche.

    Le nom de la règle doit être unique. S’il s’agit de votre ordinateur de développement, vous pouvez entrer l’adresse IP à la fois dans la zone de début de la plage d’adresses IP et dans la zone de fin de la plage d’adresses IP. Sinon, vous pouvez entrer une plage plus étendue d’adresses IP afin de prendre en compte les connexions d’ordinateurs supplémentaires de l’organisation.

8.  Cliquez sur **ENREGISTRER** en bas de la page.

    **Remarque :** il peut y avoir un délai maximal de cinq minutes pour que
    les modifications apportées au pare-feu entrent en vigueur.

    Vous êtes à présent prêt à vous connecter la base de données SQL au moyen de Management Studio.

9.  Dans la barre des tâches, cliquez sur **Démarrer**, pointez sur **Tous les programmes**, pointez sur
    **Microsoft SQL Server 2012**, puis cliquez sur **SQL Server
    Management Studio**.

10. Dans **Se connecter au serveur**, spécifiez le nom complet du
    serveur sous la forme *nom_serveur*.database.windows.net. Dans Azure, le nom du serveur est une chaîne générée automatiquement, composée de caractères alphanumériques.

11. Sélectionnez **Authentification SQL Server**.

12. Dans la zone **Connexion**, entrez le nom de connexion de l'administrateur SQL Server que vous
    avez spécifié sur le portail lors de la création de votre serveur sous la forme
    *nom_de_connexion*@*nom_de_votre_serveur*.

13. Dans la zone **Mot de passe**, entrez le mot de passe que vous avez spécifié
    sur le portail lors de la création de votre serveur.

14. Cliquez sur **Se connecter** pour établir la connexion.

Dans Azure, chaque serveur logique de base de données SQL est une abstraction qui définit un regroupement de bases de données. L’emplacement physique de chaque base de données peut correspondre à n’importe quel ordinateur du centre de données.

Dans les versions précédentes, vous deviez vous connecter directement à la base de données **principale** lors de la configuration de la connexion dans Management Studio. Cette étape n’est plus nécessaire. Les connexions s’effectuent à présent sur la base du nom du serveur, du type d’authentification et des informations d’identification de l’administrateur.

Plusieurs des Assistants SSMS que vous pouvez utiliser pour des tâches
comme la création et la modification de connexions et de bases de données dans une base de données SQL Server ne
sont pas disponibles pour les bases de données SQL dans Azure. Par conséquent, il vous faudra utiliser des instructions
Transact-SQL pour effectuer ces tâches. Les étapes ci-dessous
donnent des exemples de ces instructions. Pour plus d'informations sur l'utilisation de
Transact-SQL avec la base de données SQL, y compris des informations sur les commandes prises en charge,
consultez [Informations de référence sur Transact-SQL (Base de données SQL)][].

## <span id="Step3"></span> </a>Étape 3 : création et gestion de bases de données

Quand vous êtes connecté à la base de données **master**, vous pouvez créer
des bases de données sur le serveur, et modifier ou supprimer les bases de données existantes. La procédure
ci-dessous décrit comment accomplir plusieurs tâches courantes de gestion
de base de données via Management Studio. Pour effectuer ces tâches, vérifiez que vous êtes connecté à la base de données
**master** avec la connexion principale de niveau serveur que vous avez
créée lors de la configuration de votre serveur.

Pour ouvrir une fenêtre de requête dans Management Studio, ouvrez le dossier Bases de données, développez **Bases de données système**, cliquez avec le bouton droit sur **master**, puis cliquez sur **Nouvelle requête**.

-   Utilisez l’instruction **CREATE DATABASE** pour créer une base de données. Pour
    plus d'informations, consultez [CREATE DATABASE (Base de données SQL)][]. L'instruction ci-dessous crée une base de données appelée
    **myTestDB** et spécifie qu'il s'agit d'une base de données Web Edition
    avec une taille maximale de 1 Go.

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

Cliquez sur **Exécuter** pour exécuter la requête.

-   Utilisez l'instruction **ALTER DATABASE** pour modifier une base de données existante,
    par exemple si vous voulez modifier le nom, la taille maximale ou l'édition
    (Business ou Web) de la base de données. Pour plus d'informations, consultez [ALTER DATABASE (Base de données SQL)][]. L'instruction
    ci-dessous modifie la base de données que vous avez créée à l'étape
    précédente pour définir sa taille maximale à 5 Go.

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   Utilisez l'instruction **DROP DATABASE** pour supprimer une base de données existante.
    Pour plus d'informations, consultez [DROP DATABASE (Base de données SQL)][]. L’instruction ci-dessous supprime la base de données **maBDTest**
    , mais ne la supprimez pas maintenant car vous en aurez besoin pour créer des connexions à la prochaine étape.

        DROP DATABASE myTestBase;

-   La base de données master a une vue **sys.databases** que vous pouvez utiliser pour
    afficher des informations détaillées sur toutes les bases de données. Pour afficher toutes les bases de données existantes,
    exécutez l'instruction suivante :

        SELECT * FROM sys.databases;

-   Dans la base de données SQL, l'instruction **USE** n'est pas prise en charge pour passer d'une base de données
    à une autre. Au lieu de cela, vous devez établir une connexion directe
    à la base de données cible.

<div class="dev-callout-new">
 <strong>Remarque<span>Cliquez pour r&eacute;duire</span></strong>
 <div class="dev-callout-content">
   <p>Beaucoup d'instructions Transact-SQL qui cr&eacute;ent ou modifient une
base de donn&eacute;es doivent &ecirc;tre ex&eacute;cut&eacute;es dans leur propre fichier par lots et ne peuvent pas &ecirc;tre group&eacute;es avec
d'autres instructions Transact-SQL. Pour plus d'informations, consultez les informations sp&eacute;cifiques aux instructions, disponibles &agrave; partir des liens propos&eacute;s ci-dessus.</p>
</div>

</div>

## <span id="Step4"></span> </a>Étape 4 : création et gestion de connexions

La base de données **master** effectue le suivi des connexions et
de leurs autorisations de créer des bases de données ou d'autres connexions. Gérez les connexions
en vous connectant à la base de données **master** avec la connexion principale de niveau
serveur que vous avez créée lors de la configuration de votre serveur. Vous pouvez utiliser les instructions
**CREATE LOGIN**, **ALTER LOGIN** ou **DROP LOGIN** pour
exécuter des requêtes sur la base de données master pour gérer les connexions
sur l'ensemble du serveur. Pour plus d'informations, consultez [Gestion des bases de données et des connexions dans la base de données SQL][].

-   Utilisez l'instruction **CREATE LOGIN** pour créer une connexion de
    niveau serveur. Pour plus d'informations, consultez [CREATE LOGIN (Base de données SQL)][]. L'instruction ci-dessous crée une connexion
    nommée **login1**. Remplacez **password1** par le mot de passe de
    votre choix.

        CREATE LOGIN login1 WITH password='password1';

-   Utilisez l'instruction **CREATE USER** pour accorder des autorisations au niveau
    de la base de données. Toutes les connexions doivent être créées dans la base de données **master**,
    mais pour qu'une connexion puisse accéder à une autre base de données, vous
    devez lui octroyer des autorisations de niveau base de données en utilisant l'instruction **CREATE USER**
     sur cette base de données. Pour plus d'informations, consultez [CREATE USER (Base de données SQL)][].

-   Pour fournir à login1
    des autorisations d'accès à une base de données appelée **myTestDB**, procédez comme
    suit :

 1.  Pour actualiser l'Explorateur d'objets de façon à voir la base de données **myTestDB** que vous venez de créer, cliquez avec le bouton droit sur le nom du serveur dans l'Explorateur d'objets, puis cliquez sur **Actualiser**.

    Si vous avez fermé la connexion, vous pouvez vous reconnecter en sélectionnant **Connecter l’Explorateur d’objets** dans le menu Fichier. Répétez les instructions de l’[Étape 2 : Connexion à la base de données SQL][Étape 2 : connexion à la base de données SQL] pour vous connecter à la base de données.

 2.  Cliquez avec le bouton droit sur **maBDTest** et sélectionnez **Nouvelle requête**.

    1.  Exécutez l'instruction suivante sur la base de données myTestDB pour créer
        un utilisateur de base de données nommé **login1User** qui correspond
        à la connexion de niveau serveur **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Utilisez la procédure stockée **sp\_addrolemember** pour donner au compte
    d'utilisateur le niveau d'autorisations approprié sur la base de données. Pour
    plus d'informations, consultez [sp\_addrolemember (Transact-SQL)][]. L’instruction ci-dessous donne à **login1User**
     des autorisations d’accès en lecture seule à la base de données en ajoutant **login1User** au
    rôle **db\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Utilisez l'instruction **ALTER LOGIN** pour modifier une connexion existante,
    par exemple si vous voulez modifier le mot de passe de la connexion. Pour
    plus d'informations, consultez [ALTER LOGIN (Base de données SQL)][]. L'instruction **ALTER LOGIN** doit être exécutée sur la base de données
    **master**. Revenez à la fenêtre de requête qui est connectée à cette base de données.

    L'instruction ci-dessous modifie la connexion **login1** pour réinitialiser le mot de passe.
    Remplacez **newPassword** par le mot de passe de votre choix, et
    **oldPassword** par le mot de passe actuel de la connexion.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Utilisez l'instruction **DROP LOGIN** pour supprimer une connexion existante.
    La suppression d'une connexion au niveau du serveur supprime également tous les comptes d'utilisateurs
    de base de données associés. Pour plus d'informations,
    consultez [DROP DATABASE (Base de données SQL)][]. L’instruction **DROP LOGIN**
     doit être exécutée sur la base de données **principale**
    . L'instruction
    ci-dessous supprime la connexion **login1**.

        DROP LOGIN login1;

-   La base de données master a une vue **sys.sql\_logins** qui vous permet
    d'afficher les connexions. Pour afficher toutes les connexions existantes, exécutez
    l'instruction suivante :

        SELECT * FROM sys.sql_logins;

## <span id="Step5"></span> </a>Étape 5 : contrôle de la base de données SQL au moyen de vues de gestion dynamique

La base de données SQL prend en charge plusieurs vues de gestion dynamique qui
vous permettent de surveiller une base de données individuelle. Voici quelques exemples du type de données
de surveillance que vous pouvez récupérer au moyen de ces vues. Pour
plus d'informations et pour accéder à d'autres exemples d’utilisation, consultez [Contrôle de la base de données SQL à l’aide de vues de gestion dynamique][].

-   L’interrogation d’une vue de gestion dynamique nécessite des autorisations **VIEW DATABASE STATE**
    . Pour accorder l'autorisation **VIEW DATABASE STATE** à un utilisateur
    de base de données spécifique, connectez-vous à la base de données que vous voulez gérer
    avec votre connexion principale de niveau serveur et exécutez l'instruction
    suivante sur la base de données :

        GRANT VIEW DATABASE STATE TO login1User;

-   Calculez la taille de la base de données au moyen de la vue **sys.dm\_db\_partition\_stats**
    . La vue **sys.dm\_db\_partition\_stats** retourne les informations de page
    et de nombre de lignes pour chaque partition de la base de données, ce qui vous permet
    de calculer la taille de la base de données. La requête suivante retourne
    la taille de votre base de données en mégaoctets :

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Utilisez les vues **sys.dm\_exec\_connections** et **sys.dm\_exec\_sessions**
    pour extraire les informations concernant les connexions
    utilisateur actuelles et les tâches internes associées à la base de données. La requête suivante
    retourne des informations sur la connexion actuelle :

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

-   Utilisez la vue **sys.dm\_exec\_query\_stats** pour récupérer des statistiques
    de performance agrégées pour les plans de requêtes mises en cache. La requête suivante
    retourne des informations sur les cinq requêtes classées en premier sur la base du temps
    processeur moyen.

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

## Ressources supplémentaires

-   [Introduction à la base de données SQL][]
-   [Gestion des bases de données et des connexions dans la base de données SQL][]
-   [Contrôle de la base de données SQL à l'aide de vues de gestion dynamique][Contrôle de la base de données SQL à l’aide de vues de gestion dynamique]
-   [Modèle de configuration de la base de données SQL][]
-   [Ajout d'utilisateurs à votre base de données SQL][]
-   [Informations de référence sur Transact-SQL (Base de données SQL)][]

  [Étape 1 : obtention de SQL Server Management Studio]: #Step1
  [Étape 2 : connexion à la base de données SQL]: #Step2
  [Étape 3 : création et gestion de bases de données]: #Step3
  [Étape 4 : création et gestion de connexions]: #Step4
  [Étape 5 : surveillance de la base de données SQL au moyen de vues de gestion dynamique]: #Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/fr-fr/download/details.aspx?id=29062
  [portail de gestion Azure]: http://manage.windowsazure.com/
  [\*nom\_de\_connexion\*@\*nom\_de\_votre\_serveur\*]: mailto:*login*@*yourServerName*
  [Informations de référence sur Transact-SQL (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394109.aspx
  [DROP DATABASE (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336259.aspx
  [Gestion des bases de données et des connexions dans la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336268.aspx
  [CREATE USER (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/ee336277.aspx
  [sp\_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/fr-fr/library/ms187750.aspx
  [ALTER LOGIN (Base de données SQL)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336254.aspx
  [Contrôle de la base de données SQL à l’aide de vues de gestion dynamique]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff394114.aspx
  [Introduction à la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee336230.aspx
  [Modèle de configuration de la base de données SQL]: http://msdn.microsoft.com/fr-fr/library/ee336227.aspx
  [Ajout d'utilisateurs à votre base de données SQL]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx
