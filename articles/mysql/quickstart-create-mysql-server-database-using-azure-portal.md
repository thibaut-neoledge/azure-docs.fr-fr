---
title: "Démarrage rapide : création d’un serveur Azure Database pour MySQL à l’aide du portail Azure | Microsoft Docs"
description: "Cet article vous guide dans le portail Azure pour créer rapidement un exemple de serveur Azure Database pour MySQL en environ cinq minutes."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Création d’un serveur Azure Database pour MySQL à l’aide du portail Azure
Base de données Azure pour MySQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données MySQL hautement disponibles dans le cloud. Ce guide de démarrage rapide vous montre comment créer en quelques minutes un serveur de base de données Azure pour MySQL à l’aide du portail Azure. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure
Ouvrez votre navigateur web et accédez au [portail Microsoft Azure](https://portal.azure.com/). Entrez vos informations d’identification pour vous connecter au portail. Il s’ouvre par défaut sur le tableau de bord des services.

## <a name="create-azure-database-for-mysql-server"></a>Créer un serveur de base de données Azure pour MySQL
Un serveur de base de données Azure pour MySQL est créé avec un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-unit-and-storage.md). Ce serveur est créé dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Pour créer un serveur de base de données Azure pour MySQL, suivez les étapes ci-après :

1. Cliquez sur le bouton **Nouveau** (+) dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** sur la page **Nouveau**, puis **Base de données Azure pour MySQL** sur la page **Bases de données**. Vous pouvez également saisir **MySQL** dans la zone de recherche de la page Nouveau pour localiser le service.
![Portail Azure - Nouveau - Base de données - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Renseignez le formulaire du nouveau serveur avec les informations suivantes, comme indiqué dans l’illustration précédente :

    **Paramètre** | **Valeur suggérée** | **Description du champ** 
    ---|---|---
    Nom du serveur | myserver4demo | Choisissez un nom unique qui identifie votre serveur de base de données Azure pour MySQL. Le nom de domaine *mysql.database.azure.com* est ajouté au nom de serveur que vous fournissez pour les applications utilisées pour la connexion. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
    Abonnement | Votre abonnement | Abonnement Azure que vous souhaitez utiliser pour votre serveur. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource est facturée.
    Groupe de ressources | myResourceGroup | Vous pouvez définir un nouveau nom de groupe de ressources ou utiliser un nom de groupe existant dans votre abonnement.
    Connexion d’administrateur serveur | myadmin | Définissez votre propre compte de connexion en tant que compte à utiliser lors de la connexion au serveur. Le nom de connexion d’administrateur ne doit pas être « azure_superuser », « admin », « administrator », « root », « guest » ou « public ».
    Mot de passe | *Votre choix* | Créez un mot de passe pour le compte Administrateur du serveur. Ce mot de passe doit comporter entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères appartenant à trois des catégories suivantes : lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
    Confirmer le mot de passe | *Votre choix*| Confirmez le mot de passe du compte d’administrateur.
    Emplacement | *La région la plus proche de vos utilisateurs*| Choisissez l’emplacement le plus proche de vos utilisateurs ou d’autres applications Azure.
    Version | *La version la plus récente*| Choisissez la version la plus récente, sauf si vous avez des exigences spécifiques.
    Niveau de tarification | **De base**, **50 unités de calcul** **50 Go** | Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances pour votre nouvelle base de données. Choisissez le **niveau De base** dans l’onglet figurant dans la partie supérieure. Cliquez sur l’extrémité gauche du curseur **Unités de calcul** pour reconfigurer la valeur sur le plus petit nombre d’unités disponible pour ce guide de démarrage rapide. Cliquez sur **OK** pour enregistrer la sélection du niveau tarifaire. Voir la capture d’écran suivante.
    Épingler au tableau de bord | Vérification | Cochez la case **Épingler au tableau de bord** pour faciliter le suivi de votre serveur sur la première page du tableau de bord de votre portail Azure.

    > [!IMPORTANT]
    > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.
    > 

    ![Portail Azure - Créer MySQL en fournissant l’entrée de formulaire requise](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Cliquez sur **Créer** pour approvisionner le serveur. L’approvisionnement prend plusieurs minutes, 20 au maximum.
   
5.  Dans la barre d’outils, cliquez sur **Notifications** (icône de cloche) pour surveiller le processus de déploiement.

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Base de données Azure pour MySQL crée un pare-feu au niveau du serveur. Le pare-feu empêche les applications et les outils externes de se connecter au serveur et à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. 

1.  Localisez votre serveur après le déploiement. Si nécessaire, vous pouvez le rechercher. Par exemple, cliquez sur **Toutes les ressources** dans le menu de gauche, puis tapez le nom du serveur (tel que *myserver4demo*) pour rechercher le serveur que vous venez de créer. Cliquez sur le nom de votre serveur dans les résultats de la recherche. La page **Présentation** correspondant à votre serveur s’ouvre et propose des options pour poursuivre la configuration de la page.

2. Sur la page du serveur, sélectionnez **Sécurité de la connexion**.

3.  Sous le titre **Règles de pare-feu**, cliquez sur la zone de texte vide de la colonne **Nom de la règle** pour commencer à créer la règle de pare-feu. 

    Pour ce guide de démarrage rapide, autorisons toutes les adresses IP sur le serveur en remplissant la zone de texte de chaque colonne avec les valeurs suivantes :

    Nom de la règle | Adresse IP de début | Adresse IP de fin 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Dans la barre d’outils supérieure de la page **Sécurité de la connexion**, cliquez sur **Enregistrer**. Avant de continuer, attendez quelques instants que la notification indiquant que la mise à jour de la sécurité de la connexion est terminée avec succès.

    > [!NOTE]
    > Les connexions à la base de données Azure pour MySQL communiquent via le port 3306. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 3306 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur, sauf si votre service informatique ouvre le port 3306.
    > 

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion
Pour vous connecter à votre serveur de base de données, vous devez rappeler le nom de serveur complet et les informations d’identification de connexion d’administrateur. Il est possible que vous ayez noté ces valeurs précédemment dans l’article relatif au démarrage rapide. Si vous ne l’avez pas fait, vous pouvez facilement localiser le nom du serveur et les informations de connexion sur la page **Vue d’ensemble** ou sur la page **Propriétés** du serveur sur le portail Azure.

1. Ouvrez la page **Vue d’ensemble** de votre serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**. 
    Placez le curseur sur chaque champ afin de faire apparaître l’icône de copie à droite du texte. Cliquez sur l’icône de copie appropriée pour copier les valeurs qui vous intéressent.

    Dans cet exemple, le nom du serveur est *myserver4demo.mysql.database.azure.com*, et la connexion d’administrateur du serveur est *myadmin@myserver4demo*.

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>Se connecter à MySQL avec l’outil de ligne de commande mysql
Vous pouvez utiliser différentes applications pour vous connecter à votre serveur de base de données Azure pour MySQL. Commençons par utiliser l’outil de ligne de commande [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) pour illustrer la procédure de connexion au serveur.  Vous pouvez utiliser un navigateur web et le service Azure Cloud Shell comme décrit dans cet article sans avoir besoin d’installer d’autres logiciels. Si vous avez installé l’utilitaire mysql en local sur votre propre machine, vous pouvez également vous y connecter depuis cet emplacement.

1. Lancez Azure Cloud Shell via l’icône de la console ( >_ ) située dans le coin supérieur droit du portail Azure.

2. Azure Cloud Shell s’ouvre dans votre navigateur pour vous permettre de taper des commandes de l’interpréteur de commandes bash.

    ![Invite de commandes - Exemple de ligne de commande mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. À l’invite Cloud Shell, connectez-vous à votre serveur de base de données Azure pour MySQL en tapant la ligne de commande mysql à l’invite verte.

    Le format suivant est utilisé pour se connecter à un serveur de base de données Azure pour MySQL avec l’utilitaire mysql :
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Par exemple, la commande ci-après permet de se connecter au serveur de notre exemple :
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    Paramètre mysql |Valeur suggérée|Description
    ---|---|---
    --host | *nom du serveur* | Spécifiez la valeur de nom de serveur utilisée lorsque vous avez créé la base de données Azure pour MySQL. Le serveur que nous utilisons dans notre exemple est myserver4demo.mysql.database.azure.com. Utilisez le nom de domaine complet (\*.mysql.database.azure.com), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion. 
    --user | *nom de connexion d’administrateur du serveur* |Tapez le nom d’utilisateur de connexion d’administrateur du serveur fourni lorsque vous avez créé la base de données Azure pour MySQL. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion.  Le format correct est *username@servername*.
    --password | *patienter jusqu’à être invité* | Vous serez invité à « Entrer le mot de passe » après avoir entré la commande. Lorsque vous y êtes invité, tapez le mot de passe que vous avez fourni lorsque vous avez créé le serveur.  Notez que les caractères du mot de passe que vous tapez ne sont pas visibles au niveau de l’invite bash. Après avoir tapé tous les caractères, appuyez sur Entrée pour vous authentifier et vous connecter.

   Une fois connecté, l’utilitaire mysql affiche une invite `mysql>` dans laquelle vous pouvez taper des commandes. 

    Exemple de sortie mysql :
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Si le pare-feu n’est pas configuré pour autoriser l’adresse IP d’Azure Cloud Shell, l’erreur suivante se produit :
    >
    > ERROR 2003 (28000) : Le client avec l’adresse IP 123.456.789.0 n’est pas autorisé à accéder au serveur.
    >
    > Pour résoudre l’erreur, veillez à ce que la configuration du serveur corresponde à celle détaillée dans la section *Configurer une règle de pare-feu au niveau du serveur* de l’article.

4. Affichez l’état du serveur pour vous assurer que la connexion fonctionne. Saisissez `status` à l’invite mysql> une fois la connexion établie.
    ```sql
    status
    ```

   > [!TIP]
   > Pour les autres commandes, consultez le [Manuel de référence de MySQL 5.7 - Chapitre 4.5.1](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Créez une base de données vide à l’invite mysql en tapant la commande suivante :
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    La commande peut prendre quelques instants. 

    Dans un serveur Azure Database pour MySQL, vous pouvez créer une ou plusieurs bases de données. Vous pouvez choisir de créer une seule base de données par serveur pour utiliser toutes les ressources, ou de créer plusieurs bases de données pour partager les ressources. Il n’existe aucune limite au nombre de bases de données qui peuvent être créées, mais plusieurs bases de données partagent les mêmes ressources de serveur. 

6. Répertoriez les bases de données à l’invite mysql en tapant la commande suivante :

    ```sql
    SHOW DATABASES;
    ```

7.  Tapez `\q`, puis appuyez sur ENTRÉE pour quitter l’outil mysql. Une fois terminé, vous pouvez fermer Azure Cloud Shell.

Vous venez de vous connecter à la base de données Azure pour MySQL et de créer une base de données utilisateur vide. Passez à la section suivante pour renouveler l’exercice afin de vous connecter au même serveur à l’aide d’un autre outil commun, MySQL Workbench.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connectez-vous au serveur à l’aide de l’outil MySQL Workbench GUI
Pour vous connecter au serveur Azure MySQL à l’aide de l’outil d’interface graphique MySQL Workbench :

1.  Lancez l’application MySQL Workbench sur votre ordinateur client. Vous pouvez télécharger et installer MySQL Workbench à partir [d’ici](https://dev.mysql.com/downloads/workbench/).

2.  Dans la boîte de dialogue **Configurer une nouvelle connexion**, entrez les informations suivantes dans l’onglet **Paramètres** :

    ![configurer une nouvelle connexion](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **Paramètre** | **Valeur suggérée** | **Description du champ** |
    |---|---|---|
    |   Nom de connexion | Connexion démo | Spécifiez une étiquette pour cette connexion. |
    | Méthode de connexion | Standard (TCP/IP) | Standard (TCP/IP) est suffisant. |
    | Nom d’hôte | *nom du serveur* | Spécifiez la valeur de nom de serveur utilisée lorsque vous avez créé la base de données Azure pour MySQL. Le serveur que nous utilisons dans notre exemple est myserver4demo.mysql.database.azure.com. Utilisez le nom de domaine complet (\*.mysql.database.azure.com), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion.  |
    | Port | 3306 | Utilisez toujours le port 3306 lorsque vous vous connectez au service Base de données Azure pour MySQL. |
    | Nom d’utilisateur |  *nom de connexion d’administrateur du serveur* | Tapez le nom d’utilisateur de connexion d’administrateur du serveur fourni lorsque vous avez créé la base de données Azure pour MySQL. Le nom d’utilisateur dans notre exemple est myadmin@myserver4demo. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion. Le format correct est *username@servername*.
    | Mot de passe | votre mot de passe | Cliquez sur le bouton Stocker dans le coffre-fort… pour enregistrer le mot de passe. |

    Cliquez sur **Tester la connexion** pour tester si tous les paramètres sont correctement configurés. Cliquez sur OK pour enregistrer la connexion. 

    > [!NOTE]
    > Le protocole SSL est appliqué par défaut sur votre serveur et requiert une configuration supplémentaire afin de vous connecter avec succès. Consultez la page [Configurer la connectivité SSL dans une application pour se connecter en toute sécurité à la base de données Azure pour MySQL](./howto-configure-ssl.md).  Si vous souhaitez désactiver le protocole SSL pour ce démarrage rapide, visitez le portail Azure et cliquez sur la page Sécurité de la connexion pour désactiver le bouton bascule Appliquer une connexion SSL.

## <a name="clean-up-resources"></a>Supprimer des ressources
Supprimez les ressources créées lors du démarrage rapide en supprimant le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md), qui comprend toutes les ressources du groupe de ressources, ou bien en supprimant une seule ressource du serveur, si vous souhaitez conserver les autres ressources.

> [!TIP]
> Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides, ne nettoyez pas les ressources créées au cours de ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.
>

Pour supprimer l’intégralité du groupe de ressources, y compris le serveur nouvellement créé :
1.  Localisez votre groupe de ressources dans le portail Azure. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom du groupe de ressources, **myresourcegroup** dans notre exemple.
2.  Sur la page de votre groupe de ressources, cliquez sur **Supprimer**. Tapez ensuite le nom de votre groupe de ressources, **myresourcegroup** dans notre exemple, dans la zone de texte pour confirmer la suppression, et cliquez sur **Supprimer**.

Autrement, vous pouvez supprimer le serveur nouvellement créé :
1.  Localisez votre serveur dans le portail Azure, s’il n’est pas ouvert. Dans le menu de gauche du portail Azure, cliquez sur **Toutes les ressources**, puis recherchez le serveur que vous avez créé.
2.  Sur la page **Présentation**, cliquez sur le bouton **Supprimer** dans le volet supérieur.
![Base de données Azure pour MySQL : Supprimer le serveur](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Vérifiez le nom du serveur à supprimer et affichez les bases de données affectées situées sous celui-ci. Tapez votre nom de serveur dans la zone de texte, **myserver4demo** dans notre exemple, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Concevoir votre première base de données Azure pour MySQL](./tutorial-design-database-using-portal.md)


