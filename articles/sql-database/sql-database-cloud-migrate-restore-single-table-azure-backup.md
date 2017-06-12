---
title: "Restaurer une table spécifique à partir d’une sauvegarde Azure SQL Database | Microsoft Docs"
description: "Découvrez comment restaurer une table unique à partir d’une sauvegarde Base de données SQL Azure."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: 340b41bd-9df8-47fb-adfc-03216de38a5e
ms.service: sql-database
ms.custom: business continuity
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
ms.translationtype: Human Translation
ms.sourcegitcommit: 9992b6a2bf73fd84c7c47783d1f4f13e10889a81
ms.openlocfilehash: 8c074243db2ae729c4fd1a483e5ac40fbbebd750
ms.contentlocale: fr-fr
ms.lasthandoff: 01/19/2017


---
# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Comment restaurer une table unique à partir d’une sauvegarde Azure SQL Database
Vous pouvez rencontrer une situation dans laquelle vous avez modifié par inadvertance les données d’une base de données SQL et vous voulez récupérer l’unique table affectée. Cet article décrit comment restaurer une simple table dans une base de données à partir d’une des [sauvegardes automatiques](sql-database-automated-backups.md)de Base de données SQL.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Étapes de préparation : renommer la table et restaurer une copie de la base de données
1. Identifiez la table dans votre base de données SQL Azure que vous souhaitez remplacer par la copie restaurée. Utilisez Microsoft SQL Management Studio pour renommer la table. Par exemple, renommez la table &lt;nom de la table&gt;_old.
   
   > [!NOTE]
   > Pour éviter d’être bloqué, vérifiez qu’aucune activité n’est en cours d’exécution sur la table que vous renommez. Si vous rencontrez des problèmes, veillez à effectuer cette procédure pendant une fenêtre de maintenance.
   >

2. Restaurez une sauvegarde de votre base de données à un point dans le temps auquel vous souhaitez la récupérer en suivant la procédure [Point-In_Time Restore](sql-database-recovery-using-backups.md#point-in-time-restore).
   
   > [!NOTE]
   > Le nom de la base de données restaurée doit être au format NomBD+Horodatage, par exemple, **Adventureworks2012_2016-01-01T22-12Z**. Cette étape ne remplace pas le nom existant de la base de données sur le serveur. Il s’agit d’une mesure de sécurité. Elle vous permet de vérifier la base de données restaurée avant de supprimer la base de données en cours, et de renommer la base de données restaurée pour une utilisation en production.
   
## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copie de la table à partir de la base de données restaurée à l’aide de l’outil Migration de base de données SQL

1. Téléchargez et installez [l’Assistant Migration de base de données SQL](https://sqlazuremw.codeplex.com).
2. Ouvrez l’Assistant Migration de base de données SQL. Dans la page **Sélectionner le processus**, sélectionnez **Base de données sous Analyser/Migrer**, puis cliquez sur **Suivant**.

   ![Assistant Migration de la base de données SQL - Sélectionner un processus](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)

3. Dans la boîte de dialogue **Se connecter au serveur** , entrez les valeurs suivantes :

   * Nom du serveur : **votre SQL server**
   * Authentification : **authentification SQL Server**
   * Connexion : **votre identifiant de connexion**
   * Mot de passe : **votre mot de passe**
   * Base de données : **Master DB (Liste toutes les bases de données)**
   
   > [!NOTE]
   > Par défaut, l’Assistant enregistre vos informations de connexion. Si cela ne vous convient pas, sélectionnez **Oublier les informations de connexion**.
   >
   
     ![Assistant de Migration SQL Database - Sélectionner une source - étape 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Dans la boîte de dialogue **Sélectionner une source**, sélectionnez le nom de la base de données restaurée lors des **étapes de préparation** comme source, puis cliquez sur **Suivant**.
   
    ![Assistant Migration de la base de données SQL - 	Sélectionner une source - étape 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)
5. Dans la boîte de dialogue **Sélectionner les objets**, sélectionnez l’option **Sélectionner des objets de base de données spécifiques**, puis sélectionnez la ou les tables que vous souhaitez migrer vers le serveur cible.
   ![Assistant Migration de la base de données SQL - 	Sélectionner les objets](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)
6. Dans la page **Résumé de l’Assistant Script**, cliquez sur **Oui** lorsque vous êtes invité à indiquer si vous êtes prêt à générer un script SQL. Vous avez également la possibilité d’enregistrer le script TSQL pour une utilisation ultérieure.
   ![Assistant Migration de la base de données SQL - 	Résumé de l’Assistant Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)
7. Dans la page **Résumé des résultats**, cliquez sur **Suivant**.
   ![Assistant Migration de la base de données SQL - 	Résumé des résultats](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)
8. Dans la page **Configurer la connexion au serveur cible**, cliquez sur **Se connecter au serveur**, puis entrez les détails comme suit :
   
   * **Nom du serveur**: instance de serveur cible
   * **Authentification** : **authentification SQL Server**. Entrez vos informations d’identification.
   * **Base de données** : **Master DB (Lister toutes les bases de données)**. Cette option répertorie toutes les bases de données sur le serveur cible.
     
     ![Assistant Migration de la base de données SQL - 	Configurer la connexion au serveur cible](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)
9. Cliquez sur **Se connecter**, sélectionnez la base de données cible dans laquelle vous souhaitez déplacer la table, puis cliquez sur **Suivant**. L’exécution du script généré précédemment doit être terminée et vous devez voir que la table qui vient d’être déplacée a été copiée dans la base de données cible.

## <a name="verification-step"></a>Étape de vérification

- Interrogez et testez la table nouvellement copiée pour vérifier que les données sont intactes. Après confirmation, vous pouvez supprimer la table renommée de la section **Étapes de préparation**. (par exemple, &lt;nom de la table&gt;_old).

## <a name="next-steps"></a>Étapes suivantes
[Sauvegardes automatiques d’une base de données SQL](sql-database-automated-backups.md)


