<properties linkid="develop-php-website-with-mysql-and-webmatrix" urlDisplayName="Web w/ WebMatrix" pageTitle="PHP website with MySQL and WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in MySQL." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure Website using WebMatrix" authors="" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="" />

# Création et déploiement d'un site web Azure PHP-MySQL avec WebMatrix

Ce didacticiel explique comment utiliser WebMatrix pour développer et déployer une application PHP-MySQL sur un site web Azure. WebMatrix est un outil de développement web gratuit de Microsoft doté de tous les éléments nécessaires pour développer un site web. WebMatrix prend en charge le PHP et inclut Intellisence pour le développement PHP.

Ce didacticiel part du principe que vous avez installé [MySQL][MySQL] sur votre ordinateur pour pouvoir tester une application en local. Cependant, cette installation n'est pas obligatoire pour suivre ce didacticiel. Vous pouvez en effet déployer votre application directement sur Sites Web Azure.

﻿À la fin de ce guide, vous disposerez d'un site web PHP-MySQL exécuté dans Azure.

Vous apprendrez à effectuer les opérations suivantes :

-   Création d'un site web Azure et d'une base de données MySQL à l'aide du portail de gestion Azure. Comme PHP est activé par défaut dans Sites Web Azure, aucune action particulière n'est requise pour exécuter votre code PHP.
-   développement d'une application PHP avec WebMatrix ;
-   publication et republication de votre application dans Azure avec WebMatrix.

En suivant ce didacticiel, vous allez créer une application web Tasklist simple dans PHP. L'application est hébergée dans un site web Azure. Voici une capture d'écran de l'application en cours d'exécution :

![Azure PHP Web Site][Azure PHP Web Site]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Configuration requise

1.  [Téléchargez][Téléchargez] les fichiers de l'application Tasklist. Il s'agit d'une application PHP simple qui vous permet d'ajouter, de marquer comme terminés et de supprimer des éléments d'une liste de tâches. Les éléments de la liste des tâches sont stockés dans une base de données MySQL. L'application se compose des fichiers suivants :

    -   **additem.php** : ajoute un élément à la liste.
    -   **createtable.php** : crée la table MySQL pour l'application. Ce fichier sera appelé une seule fois.
    -   **deleteitem.php** : supprime un élément.
    -   **getitems.php** : obtient tous les éléments de la base de données.
    -   **index.php** : affiche les tâches et fournit un formulaire pour ajouter un élément à la liste.
    -   **markitemcomplete.php** : définit l'état d'un élément sur Terminé.
    -   **taskmodel.php** : contient les fonctionnalités permettant d'ajouter, d'obtenir, de mettre à jour et de supprimer des éléments de la base de données.

2.  Créez une base de données MySQL locale intitulée `tasklist`. Vous pouvez le faire soit depuis l'espace de travail des bases de données dans WebMatrix (après son installation, comme décrit plus loin dans ce didacticiel), soit depuis l'invite de commandes MySQL avec la commande suivante :

        mysql> create database tasklist;

    Suivez cette procédure seulement si vous voulez tester votre application en local.

## <span id="CreateWebsite"></span></a>Création d'un site web Azure et d'une base de données MySQL

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
2.  Cliquez sur l'icône **+ New** dans le coin inférieur gauche du portail.

    ![Créer un site Web Azure][Créer un site Web Azure]

3.  Cliquez sur **SITE WEB**, puis sur **CRÉATION PERSONNALISÉE**.

    ![Création personnalisée d'un site Web][Création personnalisée d'un site Web]

    > [WACOM.NOTE]
    > Vous ne pouvez pas créer de base de données MySQL pour un site web après avoir créé ce dernier. Vous devez créer un site web et une base de données MySQL comme décrit dans les étapes précédentes.

4.  Entrez une valeur pour **URL** et, dans la liste déroulante **BASE DE DONNÉES**, sélectionnez **Créer une base de données MySQL**, puis sélectionnez un centre de données pour votre site web dans la liste déroulante **﻿RÉGION**. Cliquez sur la flèche située en bas de la boîte de dialogue.

    ![Entrer les détails du site Web][Entrer les détails du site Web]

5.  Entrez un **nom** pour votre base de données, sélectionnez un centre de données dans la liste déroulante **﻿RÉGION**, puis activez la case à cocher qui indique que vous acceptez les conditions juridiques. Cliquez sur la coche située en bas de la boîte de dialogue.

    ![Créer une base de données MySQL][Créer une base de données MySQL]

    Lorsque le site web est créé, le texte **Création du site Web "[NOM DU SITE]" réussie** apparaît.

    Vous devez ensuite obtenir les informations de connexion MySQL.

6.  Cliquez sur le nom du site web affiché dans la liste des sites web pour ouvrir la page Démarrage rapide du site web.

    ![Ouvrir le tableau de bord du site Web][Ouvrir le tableau de bord du site Web]

7.  Cliquez sur l'onglet **CONFIGURER** :

    ![Configure tab][Configure tab]

8.  Faites défiler jusqu'à la section **chaînes de connexion**. Les valeurs pour `Database`, `Data Source`, `User Id` et `Password` sont (respectivement) le nom de la base de données, le nom du serveur, le nom d'utilisateur et le mot de passe utilisateur. Notez les informations de connexion à la base de données, car vous en aurez besoin ultérieurement.

    ![Chaîne de connexion][Chaîne de connexion]

## Installation de WebMatrix et développement de votre application

Vous pouvez installer WebMatrix à partir du [portail de gestion][portail de gestion Azure].

1.  Une fois connecté, accédez à la page Démarrage rapide de votre site web, puis, en bas de la page, cliquez sur l'icône WebMatrix :

    ![Installation de WebMatrix][Installation de WebMatrix]

    Suivez les instructions des invites pour installer WebMatrix.

2.  Une fois WebMatrix installé, il tentera d'ouvrir votre site en tant que projet WebMatrix. Vous pouvez choisir de modifier votre site directement ou de télécharger une copie locale. Pour ce didacticiel, sélectionnez « Modifier une copie locale ».

3.  Lorsque vous êtes invité à télécharger votre site, sélectionnez **Oui, installer à partir de la galerie de modèles**.

    ![Téléchargement du site Web][Téléchargement du site Web]

4.  Parmi les modèles disponibles, sélectionnez **PHP**.

    ![Site à partir d'un modèle][Site à partir d'un modèle]

5.  Sélectionnez le modèle **Site vide**. Nommez le site, puis cliquez sur **SUIVANT**.

    ![Nommer le site][Nommer le site]

    WebMatrix ouvre votre site ainsi que certains fichiers par défaut.

    Durant les prochaines étapes, vous allez développer l'application Tasklist en ajoutant les fichiers téléchargés auparavant et en apportant quelques modifications. Cependant, vous pouvez ajouter vos propres fichiers ou en créer.

6.  Ajoutez les fichiers de votre application en cliquant sur **Ajouter existant** :

    ![Ajouter des fichiers existants dans WebMatrix][Ajouter des fichiers existants dans WebMatrix]

    Dans la boîte de dialogue qui s'affiche, accédez aux fichiers téléchargés auparavant, sélectionnez-les tous, puis cliquez sur Ouvrir. ﻿Lorsque vous y êtes invité, remplacez le fichier `index.php`.

7.  Vous devez ensuite ajouter les informations de connexion à votre base de données MySQL locale au fichier `taskmodel.php`. Ouvrez le fichier `taskmodel.php` en double-cliquant dessus, puis mettez à jour les informations de connexion à la base de données dans la fonction `connect`. (**Remarque** : passez à l'étape [Publication de votre application][Publication de votre application] si vous ne voulez pas tester votre application en local, mais plutôt la publier directement sur Sites Web Azure).

        // DB connection info
        $host = "localhost";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    Enregistrez le fichier `taskmodel.php`.

8.  Pour exécuter l'application, vous devez créer la table `items`. Cliquez avec le bouton droit sur le fichier `createtable.php`, puis sélectionnez **Lancer dans le navigateur**. Ceci lancera `createtable.php` dans votre navigateur et exécutera le code créant la table `items` dans la base de données `tasklist`.

    ![Lancer createtable.php dans le navigateur WebMatrix][Lancer createtable.php dans le navigateur WebMatrix]

9.  ﻿À présent, vous pouvez tester l'application en local. Cliquez avec le bouton droit sur le fichier `index.php`, puis sélectionnez **Lancer dans le navigateur**. Testez l'application en ajoutant des éléments, en les marquant comme terminés et en les supprimant.

## <span id="Publish"></span></a>Publication de votre application

Avant de publier votre application dans Sites Web Azure, vous devez mettre à jour les informations de connexion à la base de données dans `taskmodel.php` en utilisant les informations de connexion que vous avez obtenues précédemment (dans la section [Création d'un site web Azure et d'une base de données SQL][Création d'un site web Azure et d'une base de données SQL]).

1.  Ouvrez le fichier `taskmodel.php` en double-cliquant dessus, puis mettez à jour les informations de connexion à la base de données dans la fonction `connect`.

        // DB connection info
        $host = "value of Data Source";
        $user = "value of User Id";
        $pwd = "value of Password";
        $db = "value of Database";

    Enregistrez le fichier `taskmodel.php`.

2.  Dans WebMatrix, cliquez sur **Publier**, puis dans la boîte de dialogue **Aperçu de publication**, cliquez sur **Continuer**.

    ![Publier dans WebMatrix][Publier dans WebMatrix]

3.  Accédez à http://[nom de votre site web].azurewebsites.net/createtable.php pour créer la table `items`.

4.  Enfin, accédez à http://[nom de votre site web].azurewebsites.net/index.php pour utiliser l'application.

## Modification et republication de votre application

Vous pouvez facilement modifier votre application en modifiant la copie locale du site que vous avez téléchargée précédemment, puis en la republiant, ou en apportant directement vos modifications en mode Distant. Ici, nous allons simplement modifier le titre dans le fichier `index.php`, puis l'enregistrer directement dans le site.

1.  Dans WebMatrix, cliquez sur l'onglet Distant de votre site, puis sélectionnez **Ouvrir l'affichage distant**. Ceci ouvre votre site à distance pour vous permettre de le modifier directement.
     ![Ouvrir l'affichage distant dans WebMatrix][Ouvrir l'affichage distant dans WebMatrix]

2.  Ouvrez le fichier `index.php` en double-cliquant dessus.
    ![WebMatrix - Open index file][WebMatrix - Open index file]

3.  Remplacez **My ToDo List** par **My Task List** dans les balises **title** et **h1**, puis enregistrez le fichier.

4.  Une fois l'enregistrement terminé, cliquez sur le bouton Exécuter pour afficher les modifications sur le site actif.
    ![WebMatrix - Launch site in Remote][WebMatrix - Launch site in Remote]

# Étapes suivantes

Vous savez désormais comment créer et déployer un site web de WebMatrix vers Azure. Pour en savoir plus sur WebMatrix, consultez les ressources suivantes :

-   [WebMatrix pour Azure][WebMatrix pour Azure]

-   [Site Web WebMatrix][Site Web WebMatrix]

  [MySQL]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
  [Azure PHP Web Site]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
  [Téléchargez]: http://go.microsoft.com/fwlink/?LinkId=252506
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Créer un site Web Azure]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
  [Création personnalisée d'un site Web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
  [Entrer les détails du site Web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
  [Créer une base de données MySQL]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
  [Ouvrir le tableau de bord du site Web]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
  [Configure tab]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
  [Chaîne de connexion]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
  [Installation de WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
  [Téléchargement du site Web]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
  [Site à partir d'un modèle]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
  [Nommer le site]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
  [Ajouter des fichiers existants dans WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
  [Publication de votre application]: #Publish
  [Lancer createtable.php dans le navigateur WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
  [Création d'un site web Azure et d'une base de données SQL]: #CreateWebsite
  [Publier dans WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
  [Ouvrir l'affichage distant dans WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
  [WebMatrix - Open index file]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
  [WebMatrix - Launch site in Remote]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png
  [WebMatrix pour Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [Site Web WebMatrix]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
