<properties
	pageTitle="Créer une base de données SQL Data Warehouse dans le portail Azure en version préliminaire | Microsoft Azure"
	description="Découvrez comment créer une base de données Azure SQL Data Warehouse dans le portail Azure en version préliminaire"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="barbkess"
	manager="jhubbard"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/01/2015"
   ms.author="lodipalm;barbkess"/>

# Créer une base de données SQL Data Warehouse à l’aide du portail Azure en version préliminaire#

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-TSQL.md)
- [PowerShell](sql-data-warehouse-get-started-create-powershell.md)

Cette procédure pas à pas vous explique comment créer une base de données Azure SQL Data Warehouse en quelques minutes à l’aide du portail Azure en version préliminaire.

Lors de cette procédure pas à pas, vous allez :

- Créer un serveur qui héberge votre base de données.
- Créer une base de données qui contient l’exemple de base de données AdventureWorksDW.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Étape 1 : Se connecter et se mettre en route

1. Connectez-vous au [portail en version préliminaire](https://portal.azure.com).

2. Cliquez sur **Nouveau** > **Données et stockage** > **SQL Data Warehouse**.

	![Créer un entrepôt de données](./media/sql-data-warehouse-get-started-provision/new-data-warehouse.png)

1. Entrez un nom pour votre base de données dans le panneau SQL Data Warehouse. Dans cet exemple, nous nommons la base de données AdventureWorksDW.

    ![Entrer un nom de base de données](./media/sql-data-warehouse-get-started-provision/database-name.png)


## Étape 2 : Configurer et créer un serveur
Dans la base de données SQL et SQL Data Warehouse, chaque base de données est affectée à un serveur, et chaque serveur est affecté à un emplacement géographique. Le serveur est appelé serveur SQL logique.

> [AZURE.NOTE]<a name="note"></a>Un serveur SQL logique : >> + Fournit un moyen cohérent de configurer plusieurs bases de données dans le même emplacement géographique. > + N’est pas un matériel physique comme pour un serveur local qui héberge une base de données. Il fait partie du logiciel de service. C’est pourquoi nous l’appelons *serveur logique*. > + Peut héberger plusieurs bases de données sans affecter leurs performances. > + Utilise un *s* minuscule dans son nom. Un **s**erveur SQL est un serveur logique Azure, tandis que SQL **S**erver est un nom de produit.

1. Cliquez sur **Serveur** > **Créer un serveur**. Aucuns frais ne s’appliquent pour le serveur. Si vous avez déjà un serveur V12 que vous souhaitez utiliser, sélectionnez votre serveur existant et passez à l’étape suivante. 

    ![Créer un serveur](./media/sql-data-warehouse-get-started-provision/create-server.png)

3. Renseignez les informations pour le nouveau serveur.
    
	- **Nom du serveur**. Entrez un nom pour votre serveur logique.
	- **Nom de l’administrateur du serveur**. Entrez un nom d’utilisateur pour le compte d’administrateur de serveur.
	- **Mot de passe**. Entrez le mot de passe de l’administrateur de serveur. 
	- **Emplacement**. Choisissez un emplacement géographique pour votre serveur. Pour réduire le temps de transfert des données, il est préférable de placer votre serveur à proximité des autres ressources de données auxquelles cette base de données accédera.
	- **Créer le serveur V12**. OUI est la seule option disponible pour SQL Data Warehouse. 
	- **Autoriser les services Azure à accéder au serveur**. Cette option est toujours activée pour SQL Data Warehouse.

    >[AZURE.NOTE]Veillez à stocker le nom du serveur, le nom de l’administrateur du serveur et le mot de passe quelque part. Vous aurez besoin de ces informations pour vous connecter au serveur.

1. Cliquez sur **OK** pour enregistrer les paramètres de configuration du serveur et revenir au panneau SQL Data Warehouse.

    ![Configurer un nouveau serveur](./media/sql-data-warehouse-get-started-provision/configure-server.png)

## Étape 3 : Configurer et créer une base de données
Maintenant que vous avez sélectionné votre serveur, vous êtes prêt à terminer la création de la base de données.
 
2. Dans le panneau **SQL Data Warehouse**, renseignez les champs restants. 

    ![Création d’une base de données](./media/sql-data-warehouse-get-started-provision/create-database.png)
    
    - **Performances** : nous vous recommandons de commencer par 400 DWU. Si vous le pouvez, déplacez le curseur vers la gauche ou vers la droite pour ajuster le niveau de performances de votre base de données, maintenant et après la création de la base de données. 

        > [AZURE.NOTE]Les performances sont mesurées en termes de DWU (Data Warehouse Units). À mesure que vous augmentez les DWU, SQL Data Warehouse augmente les ressources informatiques disponibles pour vos opérations de base de données. Lorsque vous exécutez votre charge de travail, vous pouvez voir les relations entre les DWU et les performances de votre charge de travail.
        > 
        > Vous pouvez modifier rapidement et facilement le niveau de performance après avoir créé la base de données. Par exemple, si vous n’utilisez pas la base de données, déplacez le curseur vers la gauche pour réduire les coûts. Vous pouvez aussi améliorer les performances quand davantage de ressources sont nécessaires. Là réside toute la puissance d’extensibilité de SQL Data Warehouse.

    - **Sélectionner une source**. Cliquez sur **Sélectionner une source** > **Exemple**. Étant donné qu’un seul exemple de base de données est disponible à ce stade, lorsque vous sélectionnez Exemple, Azure remplit automatiquement le paramètre **Sélectionner un exemple** avec AdventureWorksDW.
  
        ![Sélectionner un exemple](./media/sql-data-warehouse-get-started-provision/select-source.png)

    - **Groupe de ressources**. Conservez les valeurs par défaut. Un groupe de ressources est un conteneur conçu pour vous aider à gérer un ensemble de ressources Microsoft Azure. En savoir plus sur les [groupes de ressources](../azure-portal/resource-group-portal.md).
    
    - **Abonnement**. Sélectionnez l’abonnement à facturer pour cette base de données.

1. Cliquez sur **Créer** pour créer votre base de données SQL Data Warehouse.

1. Patientez quelques minutes et votre base de données est prête. Lorsque vous avez terminé, votre base de données s’affiche sur votre tableau de bord. Vous devriez maintenant être redirigé vers le [portail Azure en version préliminaire](https://portal.azure.com). Comme vous pouvez le constater, votre base de données SQL Data Warehouse a été ajoutée sur la page.

    ![Vue du portail](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)


## Étape 4 : Configurer l’accès au pare-feu du serveur pour l’adresse IP de votre client
Pour pouvoir vous connecter au serveur à partir de votre adresse IP actuelle, vous devez ajouter l’adresse IP de votre client aux règles du pare-feu. Cette étape vous montre comment procéder.

1. Cliquez sur **Parcourir** > **Serveurs SQL**, choisissez votre serveur, puis cliquez sur **Paramètres** > **Pare-feu**.

    ![Rechercher les paramètres du pare-feu](./media/sql-data-warehouse-get-started-provision/find-firewall-settings.png)

4. Cliquez sur **Ajouter l’adresse IP du client** pour qu’Azure crée une règle pour cette adresse IP, puis cliquez sur **Enregistrer**.

	![Ajouter l’adresse IP](./media/sql-data-warehouse-get-started-provision/add-client-ip.png)

1. Créer une règle de pare-feu avec une plage d’adresses IP. Vous pouvez effectuer cette opération maintenant ou ultérieurement.

	>[AZURE.IMPORTANT]Votre adresse IP est susceptible de changer de temps à autre, et vous ne serez peut-être pas en mesure d’accéder à votre serveur jusqu’à ce que vous créiez une nouvelle règle de pare-feu. Vous pouvez vérifier votre adresse IP à l’aide de [Bing](http://www.bing.com/search?q=my%20ip%20address), puis ajouter une adresse IP unique ou une plage d’adresses IP. Voir [Configuration des paramètres du pare-feu](../sql-database/sql-database-configure-firewall-settings.md) pour plus d’informations.

    Pour créer une règle, entrez un nom et la plage d’adresses IP, puis cliquez sur **Enregistrer**.

    ![Ajouter une règle de pare-feu](./media/sql-data-warehouse-get-started-provision/add-rule.png)

Maintenant que vous avez configuré le pare-feu, vous devriez être en mesure d’établir des connexions entre votre ordinateur de bureau et la base de données Azure SQL Data Warehouse que vous venez de créer.


## Étapes suivantes

Maintenant que vous avez créé un exemple de base de données pour SQL Data Warehouse, découvrez comment utiliser SQL Data Warehouse dans [Se connecter et interroger](./sql-data-warehouse-get-started-connect-query.md).

>[AZURE.NOTE] Nous souhaitons améliorer cet article. Si vous choisissez de répondre « non » à la question « Cet article vous a-t-il été utile ? », veuillez ajouter une brève suggestion concernant ce qui manque ou comment cet article peut être amélioré. Merci d’avance !

<!---HONumber=Oct15_HO2-->