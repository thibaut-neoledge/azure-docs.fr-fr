---
title: "Concevoir votre première base de données Azure pour MySQL - Portail Azure | Microsoft Docs"
description: "Ce didacticiel explique comment créer et gérer un serveur de base de données Azure pour MySQL à l’aide du portail Azure."
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
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2958486a0ec055cf1fe334e97389536b2c2bb01f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="design-your-first-azure-database-for-mysql-database"></a>Concevoir votre première base de données Azure pour MySQL

Base de données Azure pour MySQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MySQL hautement disponibles dans le cloud. À l’aide du portail Azure, vous pouvez facilement gérer votre serveur et concevoir une base de données.

Ce didacticiel vous montre comment utiliser le portail Azure pour :

> [!div class="checklist"]
> * Créer une base de données Azure pour MySQL
> * Configurer le pare-feu du serveur
> * Utiliser [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour créer une base de données
> * Charger les exemples de données
> * Données de requête
> * Mettre à jour des données
> * Restaurer des données

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.
Ouvrez votre navigateur web et rendez-vous sur le [portail Azure Microsoft](https://portal.azure.com/). Connectez-vous au portail avec vos informations d’identification. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-an-azure-database-for-mysql-server"></a>Créer un serveur de base de données Azure pour MySQL
Un serveur de base de données Azure pour MySQL est créé avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-unit-and-storage.md). Ce serveur est créé dans un [groupe de ressources Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview).

1.    Accédez à **Bases de données** -> **MySQL**. Si vous ne trouvez pas MySQL Server sous **Bases de données**, cliquez sur **Tout afficher** pour afficher tous les services de base de données disponibles. Vous pouvez également taper **MySQL** dans la zone de recherche pour localiser rapidement le service.
![2-1 Accéder à MySQL](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2.    Cliquez sur l’icône **MySQL**, puis sur **Créer**.

Dans notre exemple, vous renseignez le formulaire de base de données Azure pour MySQL avec les informations suivantes :

| **Champ du formulaire** | **Description du champ** |
|----------------|-----------------------|
| *Nom du serveur* | mysqlserver4demo (le nom du serveur est globalement unique) |
| *Abonnement* | MySQLaaS (sélectionnez une option dans le menu déroulant) |
| *Groupe de ressources* | myresource (créez un groupe de ressources ou utilisez un groupe existant) |
| *Connexion d’administrateur du serveur* | myadmin (nom du compte administrateur de l’installation) |
| *Mot de passe* | mot de passe du compte administrateur de l’installation |
| *Confirmer le mot de passe* | confirmez le mot de passe du compte administrateur |
| *Emplacement* | Europe du Nord (choisissez entre **Europe du Nord** et **États-Unis de l’Ouest**) |
| *Version* | 5.6 (sélectionnez la version du serveur MySQL) |
| *Configurer les performances* | De base (choisissez **Niveau de performance**, **Unités de calcul**, **Stockage**, puis cliquez sur **OK**). |

Cliquez sur **Créer**. En une minute ou deux, vous disposez d’un nouveau serveur de base de données Azure pour MySQL qui s’exécute dans le cloud. Dans la barre d’outils, cliquez sur le bouton **Notifications** pour surveiller le processus de déploiement.

> [!TIP]
> Nous vous recommandons de placer les services Azure dans la même région et de sélectionner l’emplacement le plus proche de vous. De plus, vous pouvez cocher l’option **Épingler au tableau de bord** pour faciliter le suivi de vos déploiements.

![2-2 Créer un serveur](./media/tutorial-design-database-using-portal/2_2-Create-server.png)

## <a name="configure-firewall"></a>Configurer le pare-feu
Les bases de données Azure pour MySQL sont protégées par un pare-feu. Par défaut, toutes les connexions au serveur et aux bases de données du serveur sont rejetées. Avant de vous connecter la première fois à Base de données Azure pour MySQL à partir de votre client, vous devez configurer le pare-feu et ajouter l’adresse IP de réseau public du client (ou une plage d’adresses IP) à la liste blanche.

1.    Cliquez sur le serveur qui vient d’être créé, puis sur **Sécurité de la connexion**.

![3-1 Sécurité de la connexion](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)

2.    Vous pouvez choisir **Ajouter mon adresse IP** ou configurer les règles de pare-feu ici. N’oubliez pas de cliquer sur **Enregistrer** après avoir créé les règles.

Vous pouvez maintenant vous connecter au serveur en utilisant l’outil de ligne de commande mysql ou MySQL Workbench GUI.

> Le serveur de base de données Azure pour MySQL communique sur le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur Azure MySQL, sauf si votre service informatique ouvre le port 3306.

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez le nom de serveur complet de votre serveur Azure MySQL dans le portail Azure. Vous utilisez le nom de serveur complet pour vous connecter à votre serveur avec l’outil de ligne de commande mysql.

1.    Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Toutes les ressources** dans le menu de gauche, puis sur votre serveur de base de données Azure pour MySQL.

2.    Cliquez sur **Propriétés**. Notez les valeurs de **NOM DU SERVEUR** et de **CONNEXION D’ADMINISTRATEUR DU SERVEUR**.
![4-2 Propriétés de serveur](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

Dans cet exemple, le nom du serveur est *mysql4doc.database.windows.net*, et la connexion d’administrateur du serveur est *mysqladmin@mysql4doc*.

## <a name="connect-to-the-server-using-mysql"></a>Se connecter au serveur à l’aide de mysql
Utilisez [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour établir une connexion à votre serveur de base de données pour MySQL. Dans cet exemple, la commande est :
```cmd
mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

## <a name="create-a-blank-database"></a>Créer une base de données vide
Une fois que vous êtes connecté au serveur, créez une base de données vide sur laquelle travailler.
```sql
CREATE DATABASE mysampledb;
```

À l’invite, exécutez la commande suivante pour basculer la connexion sur la base de données nouvellement créée :
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Créer des tables dans la base de données
Maintenant que vous savez comment vous connecter à la base de données Azure pour MySQL, nous pouvons aborder certaines tâches de base.

Tout d’abord, nous pouvons créer une table et y charger des données. Nous allons créer une table qui stocke des données d’inventaire.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Charger des données dans les tables
Maintenant que nous disposons d’une table, nous pouvons y insérer des données. Dans la fenêtre d’invite de commandes ouverte, exécutez la requête suivante pour insérer des lignes de données.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Vous avez maintenant chargé deux lignes de données dans la table que vous avez créée précédemment.

## <a name="query-and-update-the-data-in-the-tables"></a>Interroger et mettre à jour les données des tables
Exécutez la requête suivante pour récupérer des informations à partir de la table de base de données.
```sql
SELECT * FROM inventory;
```

Vous pouvez également mettre à jour les données des tables.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

La ligne est mise à jour en conséquence lorsque vous récupérez les données.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurer une version antérieure d’une base de données
Imaginez que vous avez supprimé cette table par erreur. Il s’agit de quelque chose que vous ne pouvez pas récupérer facilement. Base de données Azure pour MySQL vous permet de revenir à n’importe quel moment dans le temps au cours de ces 35 derniers jours et de procéder à une restauration vers un nouveau serveur. Vous pouvez alors utiliser ce nouveau serveur pour récupérer les données supprimées. Les étapes suivantes restaurent le serveur dans l’état dans lequel il était avant l’ajout de la table.

1 - Dans la page Base de données Azure pour mySQL pour votre serveur, cliquez sur **Restaurer** dans la barre d’outils. La page **Restauration** s’ouvre.
![10-1 Restaurer une base de données](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2- Renseignez le formulaire **Restaurer** avec les informations requises :

-    Point de restauration : sélectionnez un point dans le temps avant la modification du serveur.
-    Serveur cible : fournissez un nouveau nom de serveur sur lequel vous souhaitez effectuer la restauration.
-    Emplacement : vous ne pouvez pas sélectionner la région. Par défaut, elle est identique à celle du serveur source.
-    Niveau tarifaire : vous ne pouvez pas modifier cette valeur lors de la restauration d’un serveur. Elle est identique à celle du serveur source.
![10-2 Formulaire de restauration](./media/tutorial-design-database-using-portal/10_2-restore-form.png)

3 - Cliquez sur **OK** pour restaurer le serveur [à un point dans le temps](./howto-restore-server-portal.md) avant la suppression des tables. La restauration d’un serveur à un autre point dans le temps crée un serveur en double par rapport au serveur d’origine, à condition qu’il se trouve au sein de la période de rétention de votre niveau de service.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous montre comment utiliser le portail Azure pour :

> [!div class="checklist"]
> * Créer une base de données Azure pour MySQL
> * Configurer le pare-feu du serveur
> * Utiliser [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) pour créer une base de données
> * Charger les exemples de données
> * Données de requête
> * Mettre à jour des données
> * Restaurer des données

[Créer et gérer des règles de pare-feu Base de données Azure pour MySQL à l’aide du portail Azure](./howto-manage-firewall-using-portal.md)

