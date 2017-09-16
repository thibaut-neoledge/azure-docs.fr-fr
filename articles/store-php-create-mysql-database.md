---
title: "Création et connexion à une base de données MySQL dans Azure"
description: "Apprenez à utiliser le portail Azure pour créer une base de données MySQL et vous y connecter à partir d’une application web PHP dans Azure."
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 017e08b7e1e37c0bcb95ae0d9d702471bb8bc6bc
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Création et connexion à une base de données MySQL dans Azure
Ce tutoriel vous montre comment créer une base de données MySQL sur le [portail Azure](https://portal.azure.com) (fournisseur [ClearDB](http://www.cleardb.com/)) et s’y connecter à partir d’une application web en PHP s’exécutant dans [Azure App Service](app-service/app-service-value-prop-what-is.md).

> [!NOTE]
> Vous pouvez également créer une base de données MySQL dans le cadre d’un <a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">modèle d’application Marketplace</a>.
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>Création d’une base de données MySQL dans le portail Azure
Pour créer une base de données MySQL dans le portail Azure, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Nouveau** > **Données + Stockage** > **Base de données MySQL**.

    ![Création d’une base de données MySQL dans Azure - démarrer](./media/store-php-create-mysql-database/create-db-1-start.png)
3. Dans le [panneau](azure-portal-overview.md)Nouvelle base de données MySQL, configurez votre nouvelle base de données MySQL comme suit (*panneau*: une page du portail s’ouvre à l’horizontale) :

   * **Nom de base de données**: entrez un nom d’identification unique
   * **Abonnement**: choisissez l’abonnement à utiliser
   * **Type de base de données** : sélectionnez **Partagé** pour les niveaux économiques ou gratuits, ou **Dédié** pour obtenir des ressources dédiées.
   * **Groupe de ressources**: ajoutez la base de données MySQL à un [groupe de ressources](azure-resource-manager/resource-group-overview.md) existant ou placez-la dans un nouveau groupe. Les ressources du même groupe peuvent être facilement gérées ensemble.
   * **Emplacement**: choisissez un emplacement proche. Lorsque vous ajoutez la base de données à un groupe de ressources existant, vous êtes limité à l’emplacement du groupe de ressources.
   * **Niveau tarifaire** : cliquez sur **Niveau tarifaire**, puis sélectionnez une option de tarification (le niveau **Mercure** est gratuit), puis cliquez sur **Sélectionner**.
   * **Conditions juridiques** : cliquez sur **Conditions juridiques**, passez en revue les détails de l’achat, puis cliquez sur **Acheter**.
   * **Épingler au tableau de bord**: sélectionnez cette option si vous souhaitez bénéficier d’un accès direct depuis le tableau de bord. Cette fonctionnalité est particulièrement utile si vous n’êtes pas encore tout à fait familiarisé avec la navigation dans le portail.

     La capture d’écran suivante illustre une manière de configurer votre base de données MySQL.  
     ![Création d’une base de données MySQL dans Azure - configurer](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. Une fois la configuration terminée, cliquez sur **Créer**.

    ![Création d’une base de données MySQL dans Azure - créer](./media/store-php-create-mysql-database/create-db-3-create.png)

    Une notification contextuelle vous informe que le déploiement a démarré.

    ![Création d’une base de données MySQL dans Azure - en cours](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Une autre fenêtre contextuelle s’affiche une fois le déploiement réussi. Le portail ouvre aussi automatiquement le panneau de votre base de données MySQL.

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>Connexion à la base de données MySQL
Pour afficher les informations de connexion de votre nouvelle base de données MySQL, cliquez simplement sur **Propriétés** sur le panneau de votre application web.

![Création d'une base de données MySQL dans Azure - panneau Base de données MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

Vous pouvez désormais utiliser ces informations de connexion dans n’importe quelle application web. Cliquez [ici](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)pour obtenir un exemple d’utilisation des informations de connexion à partir d’une simple application PHP.

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Connecter une application web Laravel (à partir du didacticiel de prise en main de PHP)
Supposons que vous venez de terminer le didacticiel [Create, configure, and deploy a PHP web app to Azure](app-service-web/app-service-web-php-get-started.md) (Créer, configurer et déployer une application web PHP sur Azure) et que vous disposez d’une application web [Laravel](https://www.laravel.com/) exécutée dans Azure. Vous pouvez facilement ajouter des fonctionnalités de base de données à votre application Laravel. Pour ce faire, procédez simplement comme suit :

> [!NOTE]
> La procédure suivante suppose que vous avez terminé le didacticiel [Create, configure, and deploy a PHP web app to Azure](app-service-web/app-service-web-php-get-started.md)(Créer, configurer et déployer une application web PHP sur Azure).
>
>

1. Configurez l’application Laravel dans votre environnement de développement local pour pointer vers la base de données MySQL. Pour ce faire, ouvrez `.env` à partir du répertoire racine de votre application Laravel, puis configurez les options de la base de données MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

   > [!NOTE]
   > Dans le panneau **Propriétés**, le nom de votre base de données MySQL peut correspondre ou non à celui indiqué dans le champ **NOM DE BASE DE DONNÉES**. Il est préférable de vérifier le paramètre de base de données dans le champ **CHAÎNE DE CONNEXION** .    
   >
   > ![Création d’une base de données MySQL dans Azure - en cours](./media/store-php-create-mysql-database/connect-db-1-database-name.png)
   >
   >
2. Pour vérifier que vous avez maintenant accès à MySQL, le moyen le plus rapide consiste à utiliser la [structure d’authentification par défaut de Laravel](https://laravel.com/docs/5.2/authentication#authentication-quickstart).
   Dans le terminal de ligne de commande, exécutez les commandes suivantes à partir du répertoire racine de votre application Laravel :

         php artisan migrate
         php artisan make:auth

    La première commande crée les tables dans Azure en fonction des migrations prédéfinies dans le répertoire `database/migrations` ; la deuxième commande structure les vues et itinéraires de base pour l’inscription et l’authentification de l’utilisateur.
3. Exécutez maintenant le serveur de développement :

        php artisan serve
4. Dans le navigateur, accédez à http://localhost:8000 et procédez comme suit pour inscrire un nouvel utilisateur :

    ![Connexion à une base de données MySQL dans Azure - inscrire un utilisateur](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Suivez les instructions de l’interface utilisateur pour terminer l’inscription. Une fois l’inscription terminée, vous serez connecté.

    ![Connexion à une base de données MySQL dans Azure - inscrire un utilisateur](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    Vous allez maintenant développer votre application sur la base de données MySQL dans Azure.
5. Il vous suffit à présent de répliquer vos paramètres `.env` dans votre application web Azure. Exécutez les commandes suivantes dans l’interface de ligne de commande Azure :

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

6. Ensuite, validez et envoyez dans Azure les modifications locales que vous avez effectuées pendant l’exécution de `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master
7. Accédez à l’application web Azure.

        azure site browse
8. Connectez-vous en utilisant les informations d’identification utilisateur créées précédemment.

    ![Connexion à une base de données MySQL dans Azure - accéder à l’application web Azure](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Une fois connecté, vous devriez accéder à l’écran post-connexion.

    ![Connexion à une base de données MySQL dans Azure - connecté](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Félicitations, votre application web PHP dans Azure a désormais accès aux données à partir de votre base de données MySQL.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

