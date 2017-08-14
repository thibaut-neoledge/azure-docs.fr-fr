---
title: "Portail Azure : Créer une base de données Azure pour PostgreSQL | Microsoft Docs"
description: "Guide de démarrage rapide pour créer et gérer un serveur de base de données Azure pour PostgreSQL à l’aide de l’interface utilisateur du portail Azure."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/04/2017
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: e89058a500aeb542ae4c7dc6bb4a9b9ae54db7f2
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Créer une base de données Azure pour PostgreSQL dans le portail Azure

Base de données Azure pour PostgreSQL est un service géré qui vous permet d’exécuter, de gérer et de mettre à l’échelle des bases de données PostgreSQL hautement disponibles dans le cloud. Ce guide de démarrage rapide vous montre comment créer un serveur de base de données Azure pour PostgreSQL à l’aide du portail Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Créer une base de données Azure pour PostgreSQL

Un serveur de base de données Azure pour PostgreSQL est créé. Il contient un ensemble défini de [ressources de calcul et de stockage](./concepts-compute-unit-and-storage.md). Ce serveur est créé dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Pour créer un serveur de base de données Azure pour PostgreSQL, suivez les étapes ci-après :
1.  Cliquez sur le bouton **Nouveau** (+) dans le coin supérieur gauche du portail Azure.
2.  Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données Azure pour PostgreSQL** dans la page **Bases de données**.
 ![Base de données Azure pour PostgreSQL - Créer la base de données](./media/quickstart-create-database-portal/1-create-database.png)

3.  Renseignez le formulaire du nouveau serveur avec les informations suivantes, comme indiqué dans l’illustration précédente :

    Paramètre|Valeur suggérée|Description
    ---|---|---
    Nom du serveur |*mypgserver-20170401*|Choisissez un nom unique qui identifie votre serveur de base de données Azure pour PostgreSQL. Le nom de domaine *postgres.database.azure.com* est ajouté au nom de serveur que vous fournissez pour les applications utilisées pour la connexion. Le nom de serveur ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 63 caractères.
    Abonnement|*Votre abonnement*|Abonnement Azure que vous souhaitez utiliser pour votre serveur. Si vous avez plusieurs abonnements, sélectionnez l’abonnement approprié dans lequel la ressource est facturée.
    Groupe de ressources|*myresourcegroup*| Vous pouvez définir un nouveau nom de groupe de ressources ou utiliser un nom de groupe existant dans votre abonnement.
    Connexion d’administrateur serveur |*mylogin*| Définissez votre propre compte de connexion en tant que compte à utiliser lors de la connexion au serveur. Le nom de connexion d’administrateur ne doit pas être « azure_superuser », « azure_pg_admin », « admin », « administrator », « root », « guest » ni « public », et ne doit pas commencer par « pg_ ».
    Mot de passe |*Votre choix* | Créez un mot de passe pour le compte Administrateur du serveur. Ce mot de passe doit comporter entre 8 et 128 caractères. Votre mot de passe doit contenir des caractères appartenant à trois des catégories suivantes : lettres majuscules, lettres minuscules, chiffres (0 à 9) et caractères non alphanumériques (!, $, #, %, etc.).
    Lieu|*La région la plus proche de vos utilisateurs*| Choisissez l’emplacement le plus proche de vos utilisateurs.
    Version de PostgreSQL.|*La version la plus récente*| Choisissez la version la plus récente, sauf si vous avez des exigences spécifiques.
    Niveau de tarification | **De base**, **50 unités de calcul** **50 Go** | Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances pour votre nouvelle base de données. Choisissez le niveau De base dans l’onglet figurant dans la partie supérieure. Cliquez sur l’extrémité gauche du curseur Unités de calcul pour reconfigurer la valeur sur le plus petit nombre d’unités disponible pour ce guide de démarrage rapide. Cliquez sur **OK** pour enregistrer la sélection du niveau tarifaire. Voir la capture d’écran suivante.
    | Épingler au tableau de bord | Vérification | Cochez la case **Épingler au tableau de bord** pour faciliter le suivi de votre serveur sur la première page du tableau de bord de votre portail Azure.

  > [!IMPORTANT]
  > La connexion d’administrateur serveur et le mot de passe que vous spécifiez ici seront requis plus loin dans ce guide de démarrage rapide pour la connexion au serveur et à ses bases de données. Retenez ou enregistrez ces informations pour une utilisation ultérieure.

    ![Base de données Azure pour PostgreSQL - Choisir le niveau tarifaire](./media/quickstart-create-database-portal/2-service-tier.png)

4.  Cliquez sur **Créer** pour approvisionner le serveur. L’approvisionnement prend plusieurs minutes, 20 au maximum.

5.  Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.
 ![Base de données Azure pour PostgreSQL - Consulter les notifications](./media/quickstart-create-database-portal/3-notifications.png)
   
  Par défaut, la création de la base de données **postgres** intervient sous votre serveur. La base de données [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) est une base de données par défaut destinée aux utilisateurs, utilitaires et applications tierces. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurer une règle de pare-feu au niveau du serveur

Le service Base de données Azure pour PostgreSQL crée un pare-feu au niveau du serveur. Le pare-feu empêche les applications et les outils externes de se connecter au serveur et à toute base de données sur le serveur, sauf si une règle de pare-feu est créée pour ouvrir le pare-feu à des adresses IP spécifiques. 

1.  Localisez votre serveur après le déploiement. Si nécessaire, vous pouvez le rechercher. Par exemple, cliquez sur **Toutes les ressources** dans le menu de gauche, puis tapez le nom du serveur (tel que mypgserver-20170401) pour rechercher le serveur que vous venez de créer. Cliquez sur le nom de votre serveur dans les résultats de la recherche. La page Présentation correspondant à votre serveur s’ouvre et propose des options pour poursuivre la configuration de la page.
 
    ![Base de données Azure pour PostgreSQL - Rechercher le serveur ](./media/quickstart-create-database-portal/4-locate.png)

2.  Sur la page du serveur, sélectionnez **Sécurité de la connexion**. 
    ![Base de données Azure pour PostgreSQL - Créer une règle de pare-feu](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  Sous le titre **Règles de pare-feu**, cliquez sur la zone de texte vide de la colonne **Nom de la règle** pour commencer à créer la règle de pare-feu. 

    Pour ce guide de démarrage rapide, autorisons toutes les adresses IP sur le serveur en remplissant la zone de texte de chaque colonne avec les valeurs suivantes :

    Nom de la règle | Adresse IP de début | Adresse IP de fin 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Dans la barre d’outils supérieure de la page Sécurité de la connexion, cliquez sur **Enregistrer**. Puis cliquez sur **X** pour fermer la page Sécurité de la connexion.

    > [!NOTE]
    > Le serveur Azure PostgreSQL communique sur le port 5432. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 5432 peut être bloqué par le pare-feu de votre réseau. Dans ce cas, vous ne pouvez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 5432.
  >

## <a name="get-the-connection-information"></a>Obtenir les informations de connexion

Lorsque nous avons créé notre serveur de base de données Azure pour PostgreSQL, une base de données par défaut nommée **postgres** a été créée. Pour vous connecter à votre serveur de base de données, vous devez rappeler le nom de serveur complet et les informations d’identification de connexion d’administrateur. Il est possible que vous ayez noté ces valeurs précédemment dans le cadre de ce guide de démarrage rapide. Si vous ne l’avez pas fait, localisez facilement le nom du serveur et les informations de connexion sur la page Vue d’ensemble du serveur sur le portail Azure.

1. Ouvrez la page **Vue d’ensemble** de votre serveur. Prenez note du **nom du serveur** et du **nom de connexion d’administrateur du serveur**.
    Placez le curseur sur chaque champ afin de faire apparaître l’icône de copie à droite du texte. Cliquez sur l’icône de copie appropriée pour copier les valeurs qui vous intéressent.

 ![Base de données Azure pour PostgreSQL - Connexion d’administrateur du serveur](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Se connecter à la base de données PostgreSQL à l’aide de psql dans Cloud Shell

Vous pouvez utiliser différentes applications pour vous connecter à votre serveur de base de données Azure pour PostgreSQL. Commençons par utiliser l’utilitaire en ligne de commande psql pour illustrer la procédure de connexion au serveur.  Vous pouvez utiliser un navigateur web et le service Azure Cloud Shell comme décrit dans cet article sans avoir besoin d’installer d’autres logiciels. Si vous avez installé l’utilitaire psql en local sur votre propre machine, vous pouvez également vous y connecter à cet emplacement.

1. Exécutez Azure Cloud Shell via l’icône de la console dans le volet de navigation supérieur.

   ![Base de données Azure pour PostgreSQL - Icône de la console Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Azure Cloud Shell s’ouvre dans votre navigateur pour vous permettre de taper des commandes de l’interpréteur de commandes bash.

   ![Base de données Azure pour PostgreSQL - Invite bash Azure Shell](./media/quickstart-create-database-portal/8-bash.png)

3. À l’invite Cloud Shell, connectez-vous à une base de données de votre serveur de base de données Azure pour PostgreSQL en tapant la ligne de commande psql à l’invite de couleur verte.

    Le format suivant est utilisé pour se connecter à un serveur de base de données Azure pour PostgreSQL avec l’utilitaire [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) :
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Par exemple, la commande ci-après permet de se connecter à un exemple de serveur :

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    Paramètre psql |Valeur suggérée|Description
    ---|---|---
    --host | *nom du serveur* | Spécifiez la valeur de nom de serveur utilisée lorsque vous avez créé la base de données Azure pour PostgreSQL. L’exemple de serveur que nous utilisons ici est mypgserver-20170401.postgres.database.azure.com. Utilisez le nom de domaine complet (\*.postgres.database.azure.com), comme indiqué dans l’exemple. Si vous ne vous souvenez pas du nom de votre serveur, suivez les instructions de la section précédente pour obtenir les informations de connexion. 
    --port | **5432** | Utilisez toujours le port 5432 lorsque vous vous connectez au service Base de données Azure pour PostgreSQL. 
    --username | *nom de connexion d’administrateur du serveur* |Tapez le nom d’utilisateur de connexion d’administrateur du serveur fourni lorsque vous avez créé la base de données Azure pour PostgreSQL. Si vous ne vous souvenez pas du nom d’utilisateur, suivez les instructions de la section précédente pour obtenir les informations de connexion.  Le format correct est *username@servername*.
    --dbname | **postgres** | Pour la première connexion, utilisez le nom de base de données *postgres* généré par défaut par le système. Par la suite, vous créerez votre propre base de données.

    Après avoir exécuté la commande psql avec vos propres valeurs de paramètre, vous serez invité à taper le mot de passe d’administrateur du serveur. Il s’agit du mot de passe que vous avez fourni lorsque vous avez créé le serveur. 

    Paramètre psql |Valeur suggérée|Description
    ---|---|---
    password | *votre mot de passe d’administrateur* | Notez que les caractères du mot de passe que vous tapez ne sont pas visibles au niveau de l’invite bash. Après avoir tapé tous les caractères, appuyez sur Entrée pour vous authentifier et vous connecter.

4.  Une fois connecté au serveur, créez une base de données vide à l’invite en tapant la commande suivante :
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

5.  À l’invite, exécutez la commande suivante pour basculer la connexion sur la base de données **mypgsqldb** nouvellement créée.
    ```bash
    \c mypgsqldb
    ```

6.  Tapez \q, puis appuyez sur ENTRÉE pour quitter l’utilitaire psql. Vous pouvez fermer Azure Cloud Shell.

Vous venez de vous connecter à la base de données Azure pour PostgreSQL et de créer une base de données utilisateur vide. Pour vous connecter à l’aide d’un autre outil courant, pgAdmin, passez à la section suivante.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Se connecter à la base de données PostgreSQL avec pgAdmin

Pour se connecter au serveur Azure PostgreSQL à l’aide de l’outil d’interface graphique _pgAdmin_
1.  Lancez l’application _pgAdmin_ sur votre ordinateur client. Vous pouvez installer _pgAdmin_ à partir de http://www.pgadmin.org/.
2.  Choisissez **Ajouter un nouveau serveur** dans le menu **Liens rapides**.
3.  Dans l’onglet **Général** de la boîte de dialogue **Créer - Serveur**, entrez un nom convivial unique pour le serveur comme **Serveur Azure PostgreSQL**.
![Outil pgAdmin - Créer - Serveur](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  Dans l’onglet **Connexion** de la boîte de dialogue **Créer - Serveur**, utilisez les paramètres comme indiqué et cliquez sur **Enregistrer**.
   ![pgAdmin - Créer - Serveur](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Nom d’hôte/adresse** : mypgserver-20170401.postgres.database.azure.com 
        - Nom complet du serveur.
    - **Port :**  5432
        - Le numéro du port utilisé par ce serveur de base de données est 5432.
    - **Maintenance Database** (Base de données de maintenance) : postgres 
        - Nom de la base de données par défaut générée par le système.
    - **Nom d’utilisateur :** mylogin@mypgserver-20170401 
        - La connexion d’administrateur du serveur (user@mypgserver) obtenue précédemment dans ce démarrage rapide.
    - **Mot de passe** : le mot de passe que vous avez choisi lorsque vous avez créé le serveur plus tôt dans ce démarrage rapide.
    - **Mode SSL** : Exiger
        - Par défaut, tous les serveurs Azure PostgreSQL sont créés avec l’application du SSL activée. Pour désactiver l’application du SSL, consultez les détails dans [Configure SSL connectivity in Azure Database for PostgreSQL](./concepts-ssl-connection-security.md) (Configurer la connectivité SSL dans Base de données Azure pour PostgreSQL).
5.  Cliquez sur **Save**.
6.  Dans le volet gauche Navigateur, développez **Groupes de serveurs**. Choisissez votre serveur **Serveur Azure PostgreSQL**.
7.  Choisissez le **serveur** auquel vous vous êtes connecté, puis choisissez **Bases de données** sous ce dernier. 
8.  Cliquez avec le bouton droit sur **Bases de données** pour créer une base de données.
9.  Choisissez un nom de base de données **mypgsqldb** et son propriétaire en tant qu’administrateur serveur **mylogin**.
10. Cliquez sur **Enregistrer** pour créer une base de données vide.
11. Dans le volet **Navigateur**, développez **Serveur**. Développez le serveur que vous avez créé, et consultez la base de données **mypgsqldb** se trouvant en dessous.
 ![pgAdmin - Créer - Base de données](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Nettoyez toutes les ressources vous avez créées au cours de ce démarrage rapide en supprimant le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Les autres démarrages rapides de cette collection reposent sur ce démarrage rapide. Si vous souhaitez continuer à utiliser d’autres démarrages rapides, ne nettoyez pas les ressources créées au cours de ce démarrage rapide. Sinon, procédez comme suit pour supprimer toutes les ressources créées au cours de ce démarrage rapide dans le portail Azure.

1.  Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur **myresourcegroup**.
2.  Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez **myresourcegroup** dans la zone de texte, puis cliquez sur Supprimer.

Si vous souhaitez simplement supprimer le serveur nouvellement créé :
1.  Dans le menu de gauche du portail Azure, cliquez sur PostgreSQL servers (Serveurs PostgreSQL), puis recherchez le serveur que vous venez de créer.
2.  Sur la page Présentation, cliquez sur le bouton Supprimer dans le volet supérieur ![Base de données Azure pour PostgreSQL - Supprimer un serveur](./media/quickstart-create-database-portal/12-delete.png).
3.  Vérifiez le nom du serveur à supprimer et affichez les bases de données sous celui-ci qui seront affectées. Tapez **mypgserver-20170401** dans la zone de texte, puis cliquez sur Supprimer.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Migration de votre base de données PostgreSQL par exportation et importation](./howto-migrate-using-export-and-import.md)

