<properties 
	pageTitle="Installation de tâches de bases de données élastiques" 
	description="Passez en revue l'installation de la fonctionnalité de tâche élastique." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# Installation des composants de tâches de bases de données élastiques

Le [pool de base de données élastique (version préliminaire)](sql-database-elastic-pool-portal.md) fournit un modèle prédictible pour déployer un grand nombre de bases de données. Une fois que vous avez créé un pool de base de données élastique, vous pouvez utiliser des **tâches de bases de données élastiques** pour exécuter des tâches d’administration dans chaque base de données du pool de base de données élastique. Par exemple, vous pouvez déployer un nouveau schéma comme la définition d'une stratégie RLS sur chaque base de données pour limiter la consultation des données sensibles à une personne disposant des informations d'identification adéquates. Voici comment installer des **tâches de bases de données élastiques**.

**Durée estimée :** 10 minutes.

## Conditions préalables
* Un abonnement Azure. Pour obtenir un essai gratuit, voir [Version d'évaluation d'un mois gratuite](http://azure.microsoft.com/pricing/free-trial/).
* Un pool élastique de bases de données. Consultez [Créer un pool de base de données SQL Azure élastique (version préliminaire)](sql-database-elastic-pool-portal.md).

## Installer les composants de service
Connectez-vous tout d'abord à la [version préliminaire du portail Azure](https://ms.portal.azure.com/#).


1. Dans la vue du tableau de bord du pool élastique de bases de données, cliquez sur **Créer une tâche**.
2. Si vous créez une tâche pour la première fois, vous devez installer les **tâches de base de données élastiques** en cliquant sur **PREVIEW TERMS**. 
3. Acceptez les termes en cliquant sur la case à cocher.
4. Dans la vue « Installer les services », cliquez sur **JOB CREDENTIALS**.

	![Installation des serveurs][1]

5. Tapez un nom d'utilisateur et un mot de passe d'administrateur de base de données. Si un utilisateur commun pour l'exécution du script existe déjà dans chaque base de données du pool élastique de bases de données, vous pouvez utiliser ce même utilisateur, ce qui vous évite d'ajouter un nouvel utilisateur à chaque base de données pour l'exécution du script. Dans le cadre de l'installation, un nouveau serveur de base de données SQL Azure est créé. Sur ce nouveau serveur, une nouvelle base de données, appelée base de données de contrôle, est créée pour contenir les métadonnées des tâches de bases de données élastiques. Le nom d'utilisateur et le mot de passe créés ici visent deux objectifs : (1) se connecter à la base de données de contrôle, et (2) servir d'informations d'identification pour se connecter à chaque base de données du pool élastique lors de l'exécution d'une tâche pour un script.
 
	![Créer le nom d'utilisateur et le mot de passe][2]
6. Cliquez sur le bouton OK. Les composants sont créés pour vous en quelques minutes dans un nouveau [groupe de ressources](../resource-group-portal.md). Le nouveau groupe de ressources est épinglé au panneau de démarrage, comme illustré ci-dessous. Les tâches de bases de données élastiques (Service Cloud, Base de données SQL, Service Bus et Storage) sont toutes créées dans le groupe.

	![groupe de ressources dans le panneau de démarrage][3]


7. Si vous tentez de créer ou de gérer une tâche pendant l’installation de tâches de bases de données élastiques, le message suivant apparaît lorsque vous fournissez les **informations d'identification**.

	![Déploiement en cours][4]

8. Si l'installation échoue pour une raison quelconque, supprimez le groupe de ressources. Voir [Désinstaller les composants de tâches de bases de données élastiques](sql-database-elastic-jobs-uninstall.md)


## Étapes suivantes

Si de nouvelles informations d'identification ont été fournies lors de l'installation de tâches de bases de données élastiques qui n'existent pas déjà dans chaque base de données du pool élastique avec les droits appropriés pour l'exécution du script, les informations d'identification doivent être créées sur chaque base de données. Voir [Ajout d'utilisateurs au pool élastique de bases de données](sql-database-elastic-jobs-add-logins-to-dbs.md). Pour comprendre la création de la tâche, voir [Création et gestion d'une tâche de base de données élastique](sql-database-elastic-jobs-create-and-manage.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=July15_HO4-->