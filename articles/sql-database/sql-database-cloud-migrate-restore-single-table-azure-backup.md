<properties
	pageTitle="Restauration d’une table spécifique à partir d’une sauvegarde Base de données SQL Azure | Microsoft Azure"
	description="Découvrez comment restaurer une table unique à partir d’une sauvegarde Base de données SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2016"
	ms.author="daleche"/>


# Comment restaurer une table unique à partir d’une sauvegarde Azure SQL Database

Vous pouvez rencontrer une situation dans laquelle vous avez modifié par inadvertance les données d’une base de données SQL et vous voulez récupérer l’unique table affectée. Cet article décrit comment restaurer une table unique dans une base de données à partir d’une des sauvegardes automatiquement effectuées par Base de données SQL Azure, en fonction du niveau de performances sélectionné.

## Étapes de préparation : renommer la table et restaurer une copie de la base de données
1. Identifiez la table dans votre base de données SQL Azure que vous souhaitez remplacer par la copie restaurée. Utilisez Microsoft SQL Management Studio pour renommer la table. Par exemple, renommez la table &lt;nom de la table&gt;\_old.

	**Remarque** Pour éviter d’être bloqué, vérifiez qu’aucune activité n’est en cours d’exécution sur la table que vous renommez. Si vous rencontrez des problèmes, veillez à effectuer cette procédure pendant une fenêtre de maintenance.

2. Restaurez une sauvegarde de votre base de données à un point dans le temps que vous souhaitez récupérer. Pour plus d’informations à ce sujet, consultez [Récupération d’une base de données SQL Microsoft Azure suite à une erreur de l’utilisateur](../sql-database/sql-database-user-error-recovery.md).

	**Remarques** :
	- Le nom de la base de données restaurée doit être au format NomBD+Horodatage, par exemple, **Adventureworks2012\_2016-01-01T22-12Z**. Cette étape ne remplace pas le nom existant de la base de données sur le serveur. Il s’agit d’une mesure de sécurité. Celle-ci permet à l’utilisateur de vérifier la base de données restaurée avant de supprimer la base de données en cours et de renommer la base de données restaurée pour une utilisation en production.
	- Tous les niveaux de performances De base à Premium sont automatiquement sauvegardés par le service, avec diverses mesures de rétention de sauvegarde, selon le niveau :

| Restauration de base de données | Niveau de base | Niveaux Standard | Niveaux Premium |
| :-- | :-- | :-- | :-- |
| Limite de restauration dans le temps | Tout point de restauration dans un délai de 7 jours|Tout point de restauration dans un délai de 14 jours| Tout point de restauration dans un délai de 35 jours|

## Copie de la table à partir de la base de données restaurée à l’aide de l’outil Migration de base de données SQL
1. Téléchargez et installez l’[Assistant Migration de base de données SQL](https://sqlazuremw.codeplex.com).

2. Ouvrez l’Assistant Migration de base de données SQL. Dans la page **Sélectionner le processus**, sélectionnez **Base de données sous Analyser/Migrer**, puis cliquez sur **Suivant**. ![Assistant Migration de la base de données SQL - Sélectionner un processus](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Dans la boîte de dialogue **Se connecter au serveur**, entrez les valeurs suivantes :
 - **Nom du serveur** : votre instance SQL Azure
 - **Authentification** : **Authentification SQL Server**. Entrez vos informations d’identification.
 - **Base de données** : **Master DB (Lister toutes les bases de données)**.
 - **Remarque** Par défaut, l’Assistant enregistre vos informations de connexion. Si cela ne vous convient pas, sélectionnez **Oublier les informations de connexion**. ![Assistant Migration de la base de données SQL - Sélectionner une source - étape 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Dans la boîte de dialogue **Sélectionner une source**, sélectionnez le nom de la base de données restaurée lors des **étapes de préparation** comme source, puis cliquez sur **Suivant**.

	![Assistant Migration de la base de données SQL - Sélectionner une source - étape 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Dans la boîte de dialogue **Sélectionner les objets**, sélectionnez l’option **Sélectionner des objets de base de données spécifiques**, puis sélectionnez la ou les tables que vous souhaitez migrer vers le serveur cible. ![Assistant Migration de la base de données SQL - Sélectionner les objets](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Dans la page **Résumé de l’Assistant Script**, cliquez sur **Oui** lorsque vous êtes invité à indiquer si vous êtes prêt à générer un script SQL. Vous avez également la possibilité d’enregistrer le script TSQL pour une utilisation ultérieure. ![Assistant Migration de la base de données SQL - Résumé de l’Assistant Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Dans la page **Résumé des résultats**, cliquez sur **Suivant**. ![Assistant Migration de la base de données SQL - Résumé des résultats](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Dans la page **Configurer la connexion au serveur cible**, cliquez sur **Se connecter au serveur**, puis entrez les détails comme suit :
	- **Nom du serveur** : instance de serveur cible
	- **Authentification** : **Authentification SQL Server**. Entrez vos informations d’identification.
	- **Base de données** : **Master DB (Lister toutes les bases de données)**. Cette option répertorie toutes les bases de données sur le serveur cible.

	![Assistant Migration de la base de données SQL - Configurer la connexion au serveur cible](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Cliquez sur **Se connecter**, sélectionnez la base de données cible dans laquelle vous souhaitez déplacer la table, puis cliquez sur **Suivant**. L’exécution du script généré précédemment doit être terminée et vous devez voir que la table qui vient d’être déplacée a été copiée dans la base de données cible.

## Étape de vérification
1. Interrogez et testez la table nouvellement copiée pour vérifier que les données sont intactes. Après confirmation, vous pouvez supprimer la table renommée de la section **Étapes de préparation** (par exemple, &lt;nom de la table&gt;\_old).

<!---HONumber=AcomDC_0608_2016-->