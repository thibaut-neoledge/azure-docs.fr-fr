---
title: "Démarrage rapide : création d’un serveur Azure Database pour MySQL à l’aide du portail Azure | Microsoft Docs"
description: "Cet article vous guide dans le portail Azure pour créer rapidement un exemple de serveur Azure Database pour MySQL en environ cinq minutes."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: hero-article
ms.date: 06/14/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: dba50b369fb87d5f6d5118038c75392bd719cc10
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure
Cet article vous guide dans le portail Azure pour créer un serveur Azure Database pour MySQL en environ cinq minutes. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure
Ouvrez votre navigateur web et accédez au [portail Microsoft Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-azure-database-for-mysql-server"></a>Créer un serveur de base de données Azure pour MySQL
1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** sur la page **Nouveau**, puis **Base de données Azure pour MySQL** sur la page **Bases de données**. Vous pouvez également saisir **MySQL** dans la zone de recherche de la page Nouveau pour localiser le service.
![Portail Azure - Nouveau - Base de données - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Renseignez le formulaire du nouveau serveur avec les informations suivantes, comme indiqué dans l’illustration précédente :

| **Paramètre** | **Valeur suggérée** | **Description du champ** |
|---|---|---|
| *Nom du serveur* | myserver4demo  | Le nom du serveur doit être unique au niveau global. |
| *Abonnement* | mysubscription | Sélectionnez votre abonnement dans la liste déroulante. |
| *Groupe de ressources* | myResourceGroup | Créez un groupe de ressources ou utilisez un groupe existant. |
| *Connexion d’administrateur du serveur* | myadmin | Spécifiez un nom de compte d’administrateur dans le moteur MySQL. |
| *Mot de passe* |  | Définissez un mot de passe complexe pour le compte d’administrateur. |
| *Confirmer le mot de passe* |  | Confirmez le mot de passe du compte d’administrateur. |
| *Emplacement* |  | Sélectionnez une région disponible. |
| *Version* | 5.7 | Choisissez la version la plus récente. |
| *Niveau tarifaire* | De base, 50 unités de calcul, 50 Go de stockage  | Choisissez le **niveau de performance**, les **unités de calcul** et le **stockage**, puis cliquez sur **OK**. |
| *Épingler au tableau de bord* | Vérification | Il est recommandé de cocher cette case pour trouver facilement le serveur plus tard. |

   Cliquez sur **Niveau tarifaire** pour spécifier le niveau tarifaire et le niveau de performances pour votre nouvelle base de données. Pour ce guide de démarrage rapide, choisissez le niveau de base, 50 unités de calcul et 50 Go de stockage inclus. Cliquez ensuite sur **OK** pour enregistrer le niveau tarifaire.
   
   ![Portail Azure - Créer MySQL en fournissant l’entrée de formulaire requise](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

   Cliquez sur **Créer**. En une minute ou deux, un nouveau serveur de base de données Azure pour MySQL s’exécute dans le cloud. Dans la barre d’outils, cliquez sur le bouton **Notifications** (icône de cloche) pour surveiller le processus de déploiement.

## <a name="configure-the-firewall"></a>Configurer le pare-feu
Avant de vous connecter la première fois à la base de données Azure pour MySQL, configurez le pare-feu et ajoutez à la liste blanche l’adresse IP de réseau public du client (ou une plage d’adresses IP).

1. Une fois le déploiement terminé, cliquez sur **Toutes les ressources** dans le menu de gauche et saisissez le nom **myserver4demo** pour rechercher le serveur qui vient d’être créé. Cliquez sur le nom du serveur figurant dans les résultats de la recherche. La page Présentation correspondant à votre serveur s’ouvre et propose des options pour poursuivre la configuration de la page.

2. Dans le panneau du serveur, sélectionnez **Sécurité de la connexion**.

3. Cliquez sur **Ajouter mon adresse IP** pour ajouter l’adresse IP de votre ordinateur local ou configurer une plage d’adresses IP. N’oubliez pas de cliquer sur **Enregistrer** après avoir créé les règles.
  ![Portail Azure - Ajouter une règle de pare-feu et enregistrer](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez le nom de domaine complet de votre serveur Azure MySQL dans le portail Azure. Vous utilisez le nom de domaine complet pour vous connecter à votre serveur à l’aide de l’outil de ligne de commande **mysql.exe**.

1.  Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Toutes les ressources** dans le menu de gauche, puis sur votre serveur de base de données Azure pour MySQL.

2.  Cliquez sur **Propriétés**. Notez les valeurs de **NOM DU SERVEUR** et de **CONNEXION D’ADMINISTRATEUR DU SERVEUR**.
Dans cet exemple, le nom du serveur est *myserver4demo.mysql.database.azure.com*, et la connexion d’administrateur du serveur est *myadmin@myserver4demo*.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Connectez-vous au serveur avec l’outil de ligne de commande mysql.exe
Utilisez [l’outil de ligne de commande mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) pour établir une connexion à votre serveur de base de données Azure pour MySQL. Vous pouvez exécuter l’outil de ligne de commande mysql dans le navigateur à l’aide d’Azure Cloud Shell ou le lancer à partir de votre propre ordinateur à l’aide des outils mysql installés localement. Pour lancer Azure Cloud Shell, cliquez sur le bouton `Try It` dans un bloc de code de cet article, ou visitez le [portail Azure](https://portal.azure.com) et cliquez sur l’icône `>_` dans la barre d’outils en haut à droite. 

1. Saisissez cette commande pour vous connecter :
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. Affichez l’état du serveur pour vous assurer que la connexion fonctionne. Saisissez `status` à l’invite mysql> une fois la connexion établie.
```sql
status
```

   ![Invite de commandes - Exemple de ligne de commande mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

   > [!TIP]
   > Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.7 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

3. Créez une base de données vide en saisissant la commande `CREATE DATABASE` à l’invite mysql>.

   ```sql
   CREATE DATABASE quickstartdb;
   ```

   Dans un serveur de base de données Azure pour MySQL, vous pouvez avoir une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Il n’existe aucune limite au nombre de bases de données qui peuvent être créées, mais plusieurs bases de données partagent les mêmes ressources de serveur.  

4. Répertoriez les bases de données en saisissant la commande `SHOW DATABASES` à l’invite mysql>.

   ```sql
   SHOW DATABASES;
   ```

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connectez-vous au serveur à l’aide de l’outil MySQL Workbench GUI
1.  Lancez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir [d’ici](https://dev.mysql.com/downloads/workbench/).

2.  Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations suivantes dans l’onglet **Paramètres** :

   ![configurer une nouvelle connexion](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

| **Paramètre** | **Valeur suggérée** | **Description du champ** |
|---|---|---|
|   *Nom de connexion* | Connexion démo| Spécifiez une étiquette pour cette connexion. |
| *Méthode de connexion* | Standard (TCP/IP) | Standard (TCP/IP) est suffisant. |
| *Nom d’hôte* | myserver4demo.mysql.database.azure.com | Utilisez le nom complet du serveur. |
| *Port* | 3306 | Utilisez le port par défaut 3306. |
| *Nom d’utilisateur* | myadmin@myserver4demo  | Utilisez le nom de connexion de l’administrateur du serveur que vous avez noté précédemment avec un caractère « @ » et le nom du serveur. |
| *Mot de passe* | votre mot de passe | Cliquez sur le bouton Stocker dans le coffre-fort… pour enregistrer le mot de passe. |

Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés. Cliquez sur OK pour enregistrer la connexion. 

> [!NOTE]
> Le protocole SSL est appliqué par défaut sur votre serveur et requiert une configuration supplémentaire afin de vous connecter avec succès. Consultez [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md).  Si vous souhaitez désactiver le protocole SSL pour ce démarrage rapide, visitez le portail Azure et cliquez sur la page Sécurité de la connexion pour désactiver le bouton bascule Appliquer une connexion SSL.

## <a name="clean-up-resources"></a>Supprimer des ressources
Nettoyez toutes les ressources vous avez créées au cours de ce démarrage rapide en supprimant le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides, ne nettoyez pas les ressources créées au cours de ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.

1.  Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myresourcegroup**.
2.  Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myresourcegroup** dans la zone de texte, puis cliquez sur Supprimer.

Si vous souhaitez supprimer le serveur nouvellement créé, procédez comme suit :
1.  Dans le menu de gauche du portail Azure, cliquez sur PostgreSQL servers (Serveurs PostgreSQL), puis recherchez le serveur que vous venez de créer.
2.  Sur la page Présentation, cliquez sur le bouton Supprimer dans le volet supérieur ![Base de données Azure pour MySQL - Supprimer un serveur](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png).
3.  Vérifiez le nom du serveur à supprimer et affichez les bases de données sous celui-ci qui seront affectées. Saisissez **myserver4demo** dans la zone de texte, puis cliquez sur Supprimer.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concevoir votre première base de données Azure pour MySQL](./tutorial-design-database-using-portal.md)


