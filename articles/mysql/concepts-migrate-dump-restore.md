---
title: "Migrer une base de données MySQL à l’aide des images mémoire et de la restauration dans la base de données Azure pour MySQL | Microsoft Docs"
description: "Présente la migration d’une base de données Azure pour MySQL."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/17/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: c0029e025cf6d0af478d1f21dc6acc7860905a81
ms.contentlocale: fr-fr
ms.lasthandoff: 05/18/2017

---

# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrer une base de données MySQL vers une base de données Azure pour MySQL à l’aide des images mémoire et de la restauration
Cet article décrit deux méthodes courantes pour sauvegarder et restaurer des bases de données dans votre base de données Azure pour MySQL.
- Sauvegarder et restaurer à partir de la ligne de commande (en utilisant mysqldump) 
- Sauvegarder et restaurer à l’aide de PHPMyAdmin 

## <a name="before-you-begin"></a>Avant de commencer
Pour parcourir ce guide pratique, vous aurez besoin des éléments suivants :
- [Créer un serveur de base de données Azure pour MySQL - Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md)
- Utilitaire de ligne de commande [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) installé sur un ordinateur
- MySQL Workbench [Téléchargement de MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat ou tout outil MySQL tiers

## <a name="use-common-tools"></a>Utiliser des outils courants
Utilisez les outils courants tels que MySQL Workbench, mysqldump, Toad ou Navicat pour vous connecter à distance et restaurer les données dans la base de données Azure pour MySQL. Utilisez ces outils sur votre ordinateur client disposant d’une connexion web pour se connecter à la base de données Azure pour MySQL. Utilisez une connexion chiffrée SSL pour appliquer les meilleures pratiques de sécurité ; consultez également la page [Configurer la connectivité SSL dans la base de données Azure pour MySQL](concepts-ssl-connection-security.md). Il est inutile de déplacer les fichiers d’images mémoire dans un emplacement spécifique du cloud lors de la migration vers la base de données Azure pour MySQL. 

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Créer un fichier de sauvegarde en ligne de commande avec mysqldump
Pour sauvegarder une base de données MySQL existante localement ou sur une machine virtuelle, exécutez la commande suivante : 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Voici les paramètres à fournir :
- [uname] le nom d’utilisateur de votre base de données ; 
- [pass] le mot de passe de votre base de données (remarque : il n’y a pas d’espace entre -p et le mot de passe) ; 
- [dbname] le nom de votre base de données ; 
- [backupfile.sql] le nom du fichier de sauvegarde de votre base de données ; 
- [--opt] l’option mysqldump. 

Par exemple, pour sauvegarder une base de données nommée « testdb » avec le nom d’utilisateur « testuser » et sans mot de passe dans un fichier testdb_backup.sql, utilisez la commande suivante. Cette commande sauvegarde la base de données « testdb » dans un fichier appelé testdb_backup.sql, qui contient toutes les instructions SQL nécessaires pour recréer la base de données. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Pour sélectionner certaines tables en particulier à sauvegarder dans votre base de données, listez les noms de table en les séparant par des espaces. Par exemple, pour sauvegarder seulement les tables table1 et table2 de « testdb », suivez cet exemple : 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Pour sauvegarder plusieurs bases de données à la fois, utilisez le commutateur --database et listez les noms de bases de données en les séparant par des espaces. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Pour sauvegarder toutes les bases de données du serveur en même temps, il est recommandé d’utiliser l’option --all-databases.
```
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-mysql-server"></a>Créer une base de données sur le serveur MySQL Azure cible
Vous devez créer une base de données vide sur le serveur cible de base de données Azure pour MySQL vers lequel vous souhaitez migrer les données avec MySQL Workbench, Toad, Navicat ou tout autre outil tiers pour MySQL. La base de données peut avoir le même nom que celle qui contient les données capturées, mais vous pouvez également créer une base de données avec un autre nom.

![Chaîne de connexion de la base de données Azure pour MySQL](./media/concepts-migrate-import-export/p5.png)

![Chaîne de connexion MySQL Workbench](./media/concepts-migrate-import-export/p4.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Restaurer votre base de données MySQL à l’aide d’une ligne de commande ou de MySQL Workbench
Une fois que vous avez créé la base de données cible, vous pouvez utiliser la commande mysql ou MySQL Workbench pour restaurer les données dans la base de données créée à cet effet à partir du fichier d’image mémoire.
```bash
mysql -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Dans cet exemple, nous restaurons les données sur la nouvelle base de données testdb3 sur le serveur cible.
```bash
$ mysql -u root -p testdb3 < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exporter avec PHPMyAdmin
Pour exporter, vous pouvez utiliser l’outil courant phpMyAdmin, que vous avez peut-être déjà installé localement dans votre environnement. Pour exporter votre base de données MySQL avec PHPMyAdmin :
- Ouvrez phpMyAdmin.
- Sélectionnez votre base de données en cliquant sur le nom de la base de données dans la liste située à gauche de l’écran. 
- Cliquer sur le lien Exporter. Un nouvel écran indique Afficher l’image mémoire de la base de données. 
- Dans la zone Exporter, cliquez sur le lien Sélectionner tout pour sélectionner toutes les tables de votre base de données. 
- Dans la zone des options SQL, cliquez sur les options de votre choix. 
- Cliquez sur l’option de fichier Enregistrer sous et sur l’option de compression correspondante, puis sur le bouton « OK ». Une boîte de dialogue vous invite à enregistrer le fichier en local.

## <a name="import-using-phpmyadmin"></a>Importer avec PHPMyAdmin
L’importation d’une base de données est similaire à l’exportation. Effectuez les actions suivantes :
- Ouvrez phpMyAdmin. 
- Créez une base de données en lui donnant un nom approprié et sélectionnez-la en cliquant sur son nom dans la liste située à gauche de l’écran. Si vous souhaitez réécrire l’importation sur une base de données existante, cliquez sur le nom de la base de données, cochez toutes les cases qui se trouvent à côté, puis sélectionnez Supprimer pour supprimer toutes les tables existantes de la base de données. 
- Cliquez sur le lien SQL. Cette action ouvre un nouvel écran dans lequel vous pouvez taper des commandes SQL ou charger votre fichier SQL. 
- Utilisez le bouton Parcourir pour trouver le fichier de base de données. 
- Cliquez sur le bouton OK. Cela a pour effet d’exporter la sauvegarde, d’exécuter les commandes SQL et de recréer votre base de données.

## <a name="next-steps"></a>Étapes suivantes

[Créer un serveur de base de données Azure pour MySQL à l’aide du Portail Azure](quickstart-create-mysql-server-database-using-azure-portal.md) 
[Créer un serveur de base de données Azure pour MySQL à l’aide d’Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

