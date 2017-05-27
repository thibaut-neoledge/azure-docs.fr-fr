---
title: "Démarrage rapide : création d’un serveur Azure Database pour MySQL à l’aide du portail Azure | Microsoft Docs"
description: "Cet article vous guide dans le portail Azure pour créer rapidement un exemple de serveur Azure Database pour MySQL en environ cinq minutes."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 85bb0396809297f0efb7323bef081055f17ede62
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure

Cet article vous guide dans le portail Azure pour créer un exemple de serveur Azure Database pour MySQL en environ cinq minutes. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure
Ouvrez votre navigateur web et accédez au [portail Microsoft Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-azure-database-for-mysql-server"></a>Créer un serveur de base de données Azure pour MySQL

1. Accédez à **Bases de données** > **MySQL**. Si vous ne trouvez pas le serveur Azure Database pour MySQL sous **Bases de données**, cliquez sur **Tout afficher** pour afficher tous les services de base de données disponibles. Vous pouvez également taper **MySQL** dans la zone de recherche pour localiser rapidement le service.
![Portail Azure - Nouveau - Base de données - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

2. Cliquez sur l’icône **MySQL**, puis sur **Créer**.
Dans notre exemple, vous renseignez la page Azure Database pour MySQL avec les informations suivantes :

| **Champ du formulaire** | **Description du champ** |
|----------------|-----------------------|
| *Nom du serveur* | mysqlserver4demo (le nom du serveur est globalement unique) |
| *Abonnement* | MySQLaaS (sélectionnez dans le menu déroulant) |
| *Groupe de ressources* | myresource (créez un groupe de ressources ou utilisez un groupe existant) |
| *Connexion d’administrateur du serveur* | myadmin (nom du compte administrateur de l’installation) |
| *Mot de passe* | mot de passe du compte administrateur de l’installation |
| *Confirmer le mot de passe* | confirmez le mot de passe du compte administrateur |
| *Emplacement* | Europe du Nord (choisissez entre **Europe du Nord** et **États-Unis de l’Ouest**) |
| *Version* | 5.6 (sélectionnez la version du serveur MySQL) |
| *Configurer les performances* | De base (choisissez **Niveau de performance**, **Unités de calcul**, **Stockage**, puis cliquez sur **OK**) |

![Portail Azure - Créer MySQL en fournissant l’entrée de formulaire requise](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

Après quelques minutes, votre serveur Azure Database pour MySQL sera configuré et lancé. Dans la barre d’outils, cliquez sur le bouton **Notifications** (icône de cloche) pour surveiller le processus de déploiement.

> [!TIP]
> Nous vous recommandons de placer les services Azure dans la même région et de sélectionner l’emplacement le plus proche de vous. De plus, vous pouvez cocher l’option **Épingler au tableau de bord** pour faciliter le suivi de vos déploiements.

## <a name="configure-the-firewall"></a>Configurer le pare-feu
Avant de vous connecter la première fois à Base de données Azure pour MySQL à partir de votre client, vous devez configurer le pare-feu et ajouter à la liste blanche l’adresse IP de réseau public du client (ou une plage d’adresses IP).

1. Cliquez sur le serveur qui vient d’être créé, puis sur **Paramètres**.
  ![Portail Azure - MySQL - Bouton Paramètres](./media/quickstart-create-mysql-server-database-using-azure-portal/4_server-settings.png)

2. Sous la section **Général**, cliquez sur **Paramètres du pare-feu**. Vous pouvez cliquer sur **Ajouter mon adresse IP** pour ajouter l’adresse IP de votre ordinateur local ou configurer une plage d’adresses IP. N’oubliez pas de cliquer sur **Enregistrer** après avoir créé les règles.
  ![Portail Azure - Ajouter une règle de pare-feu et enregistrer](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>Obtenir des informations de connexion
Obtenez le nom de domaine complet de votre serveur Azure MySQL dans le portail Azure. Vous utilisez le nom de domaine complet pour vous connecter à votre serveur à l’aide de l’outil de ligne de commande **mysql.exe**.

1.    Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Toutes les ressources** dans le menu de gauche, puis sur votre serveur de base de données Azure pour MySQL.

2.    Cliquez sur **Propriétés**. Notez les valeurs de **NOM DU SERVEUR** et de **CONNEXION D’ADMINISTRATEUR DU SERVEUR**.
Dans cet exemple, le nom du serveur est *mysql4doc.database.windows.net*, et la connexion d’administrateur du serveur est *mysqladmin@mysql4doc*.

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>Connectez-vous au serveur avec l’outil de ligne de commande mysql.exe
Vous pouvez créer plusieurs bases de données dans un serveur MySQL. Il n’existe aucune limite au nombre de bases de données qui peuvent être créées, mais plusieurs bases de données partagent les mêmes ressources de serveur.  Pour vous connecter à votre serveur à l’aide de l’outil de ligne de commande **mysql.exe**, ouvrez **Azure Cloud Shell** dans le portail et saisissez les informations suivantes :

1. Connectez-vous au serveur avec l’outil de ligne de commande **mysql** :
```dos
 mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

2. Afficher l’état du serveur :
```dos
 mysql> status
```
  ![Invite de commandes - Exemple de ligne de commande mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

> [!TIP]
> Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.6 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connectez-vous au serveur à l’aide de l’outil MySQL Workbench GUI
1.    Lancez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir [d’ici](https://dev.mysql.com/downloads/workbench/).

2.    Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations suivantes dans l’onglet **Paramètres** :

| **Paramètres** | **Description** |
|----------------|-----------------|
|    *Nom de connexion* | spécifier un nom pour cette connexion (il peut s’agir de n’importe quoi) |
| *Méthode de connexion* | choisissez Standard (TCP/IP) |
| *Nom d’hôte* | mycliserver.database.windows.net (le NOM DE SERVEUR que vous avez noté précédemment) |
| *Port* | 3306 |
| *Nom d’utilisateur* | myadmin@mycliserver(l’IDENTIFIANT ADMIN SERVEUR que vous avez noté précédemment) |
| *Mot de passe* | Vous pouvez stocker le mot de passe du compte administrateur dans le coffre |

![configurer une nouvelle connexion](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

3.    Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés.

4.    À présent, vous pouvez cliquer sur la connexion qui vient d’être créée pour vous connecter au serveur.

> SSL est appliqué par défaut sur votre serveur, ce qui requiert une configuration supplémentaire afin de vous connecter avec succès. Consultez [Configuration de la connectivité SSL dans votre application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md).  Si vous souhaitez désactiver SSL pour ce guide de démarrage rapide, vous pouvez accéder à « Sécurité de connexion » dans le portail pour désactiver l’application de SSL.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez pas besoin de ces ressources pour un autre didacticiel de démarrage rapide, vous pouvez les supprimer en procédant comme suit :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myresource**. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myresource** dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concevoir votre première base de données Azure pour MySQL](./tutorial-design-database-using-portal.md)


