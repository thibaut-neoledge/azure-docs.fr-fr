<properties linkid="develop-mobile-tutorials-command-line-administration" urlDisplayName="Command Line Administration" pageTitle="Administering a Mobile Service at the command line - Azure tutorial" metaKeywords="" description="Learn how to create, deploy, and manage your Azure Mobile Service using command-line tools." metaCanonical="" services="" documentationCenter="Mobile" title="Automate mobile services with command-line tools" authors="glenga" solutions="" manager="" editor="" />

Automatisation des services mobiles avec les outils en ligne de commande
========================================================================

Cette rubrique explique comment utiliser les outils en ligne de commande Azure pour automatiser la création et la gestion d'Azure Mobile Services. Elle montre également comment installer et prendre en main les outils en ligne de commande et les utiliser pour effectuer les tâches Mobile Services suivantes :

-   [Création d'un service mobile](#create-service)
-   [Création d'une table](#create-table)
-   [Enregistrement d'un script dans une opération de table](#register-script)
-   [Affichage de la liste des tables](#list-tables)
-   [Suppression d'une table existante](#delete-table)
-   [Affichage de la liste des services mobiles](#list-services)
-   [Suppression d'un service mobile existant](#delete-service)

Lorsqu'elles sont combinées dans un seul script ou dans un fichier de commandes, ces commandes individuelles automatisent le processus de création, de vérification et de suppression d'un service mobile.

Pour utiliser les outils en ligne de commande Azure afin de gérer Mobile Services, vous devez disposer d'un compte Azure sur lequel la fonctionnalité Azure Mobile Services est activée.

-   Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/).

-   Si vous disposez d'un compte, mais que vous devez activer la version préliminaire d'Azure Mobile Services, consultez la rubrique [Activation des fonctionnalités préliminaires d'Azure](../create-a-windows-azure-account/#enable).

Cette rubrique présente une sélection de tâches d'administration courantes prises en charge par les outils en ligne de commande Azure. Pour plus d'informations, consultez la [documentation sur les outils en ligne de commande Azure](/en-us/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services).

Installation des outilsInstallation des outils en ligne de commande Azure
-------------------------------------------------------------------------

La liste suivante contient des informations permettant d’installer les outils en ligne de commande en fonction de votre système d’exploitation :

-   **Windows** : téléchargez le [programme d'installation des outils en ligne de commande Azure](http://go.microsoft.com/fwlink/p?LinkID=275464). Ouvrez le fichier .msi téléchargé et suivez les étapes d’installation indiquées.

-   **Mac** : téléchargez le [programme d'installation du Kit de développement logiciel (SDK) Azure](http://go.microsoft.com/fwlink/p?LinkId=252249). Ouvrez le fichier .pkg téléchargé et suivez les étapes d’installation indiquées.

-   **Linux** : installez la dernière version de [Node.js](http://nodejs.org/) (voir [Installation de Node.js via le Gestionnaire de package](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)), puis exécutez la commande suivante :

          npm install azure-cli -g

Pour tester l’installation, tapez `azure` à l’invite. Lorsque l'installation est terminée, une liste de toutes les commandes `azure` disponibles apparaît.

Importation des paramètresTéléchargement et importation des paramètres de publication
-------------------------------------------------------------------------------------

Pour commencer, vous devez d'abord télécharger et importer vos paramètres de publication. Cela vous permet d'utiliser les outils servant à créer et à gérer les services Azure. Pour télécharger vos paramètres de publication, utilisez la commande `account download` :

     azure account download

Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au portail de gestion. Une fois que vous êtes connecté, votre fichier `.publishsettings` est téléchargé. Notez l'emplacement d'enregistrement du fichier.

Importez ensuite le fichier `.publishsettings` en exécutant la commande suivante, en remplaçant `<path-to-settings-file>` par le chemin d'accès de votre fichier `.publishsettings` :

     azure account import <path-to-settings-file>

Vous pouvez supprimer toutes les informations stockées par la commande `import` en utilisant la commande `account clear` :

     azure account clear

Pour afficher la liste des options des commandes `account`, utilisez l’option `-help` :

     azure account -help

Après avoir importé vos paramètres de publication, veillez à supprimer le fichier `.publishsettings` pour des raisons de sécurité. Pour plus d'informations, consultez la page [Installation des outils en ligne de commande Azure pour Mac et Linux](http://go.microsoft.com/fwlink/p/?LinkId=275795). Vous êtes désormais prêt à créer et à gérer Azure Mobile Services à partir de la ligne de commande ou dans des fichiers de commandes.

Création d'un serviceCréation d'un service mobile
-------------------------------------------------

Vous pouvez utiliser les outils en ligne de commande pour créer une instance de service mobile. Lors de la création du service mobile, vous créez également une instance de base de données SQL dans un nouveau serveur.

La commande suivante crée une instance de service mobile dans votre abonnement, où `<service-name>` est le nom du nouveau service mobile, où `<server-admin>` est l'identifiant du nouveau serveur et où `<server-password>` est le mot de passe du nouvel identifiant :

     azure mobile create <service-name> <server-admin> <server-password>

La commande `mobile create` échoue si le service mobile spécifié existe. Dans vos scripts d'automatisation, vous devez essayer de supprimer un service mobile avant de tenter de le recréer.

Affichage de la liste des servicesAffichage de la liste des services mobiles existants dans un abonnement
---------------------------------------------------------------------------------------------------------

La commande suivante renvoie la liste de tous les services mobiles d'un abonnement Azure :

     azure mobile list

Cette commande affiche également l'état actuel et l'URL de chaque service mobile.

Suppression d'un serviceSuppression d'un service mobile existant
----------------------------------------------------------------

Vous pouvez utiliser les outils en ligne de commande pour supprimer un service mobile existant ainsi que le serveur et la base de données SQL connexes. La commande suivante supprime le service mobile, où `<service-name>` est le nom du service mobile à supprimer :

     azure mobile delete <service-name> -a -q

En incluant les paramètres `-a` et `-q`, cette commande supprime également le serveur et la base de données SQL utilisés par le service mobile sans afficher d'invite.
**Remarque**

Si vous ne spécifiez pas le paramètre `-q` avec `-a` ou `-d`, l'exécution est mise en pause et vous êtes invité à sélectionner les options de suppression pour votre base de données SQL. Utilisez le paramètre `-a` uniquement lorsqu'aucun autre service mobile n'utilise la base de données ou le serveur. Sinon, utilisez le paramètre `-d` pour supprimer seulement les données appartenant au service mobile en cours de suppression.

Création d'une tableCréation d'une table dans le service mobile
---------------------------------------------------------------

La commande suivante crée une table dans le service mobile spécifié, où `<service-name>` est le nom du service mobile et où `<table-name>` est le nom de la table à créer :

     azure mobile table create <service-name> <table-name>

Ceci permet de créer une table avec les autorisations par défaut, `application`, pour les opérations de table : `insert`, `read`, `update` et `delete`.

La commande suivante crée une table dont l'autorisation `read` est définie sur public, mais dont l'autorisation `delete` est octroyée uniquement aux administrateurs :

     azure mobile table create <service-name> <table-name> -p read=public,delete=admin

Le tableau suivant présente une comparaison entre la valeur d'autorisation du script et la valeur d'autorisation du [portail de gestion Azure](https://manage.windowsazure.com/).

<table border="1" width="100%"><tr><th>Valeur du script</th><th>Valeur du portail de gestion</th></tr>
<tr><td><code>public</code></td><td>Tout le monde</td></tr>
<tr><td><code>application</code> (par défaut)</td><td>Toute personne avec la clé d'application</td></tr>
<tr><td><code>user</code></td><td>Seuls les utilisateurs authentifiés</td></tr>
<tr><td><code>admin	</code></td><td>Seuls les scripts et les administrateurs</td></tr></table>

La commande `mobile table create` échoue si la table spécifiée existe déjà. Dans vos scripts d'automatisation, vous devez essayer de supprimer une table avant de tenter de la recréer.

Affichage de la liste des tablesAffichage de la liste des tables existantes dans un service mobile
--------------------------------------------------------------------------------------------------

La commande suivante renvoie la liste de toutes les tables d'un service mobile, où `<service-name>` est le nom du service mobile :

     azure mobile table list <service-name>

Cette commande affiche également le nombre d'index sur chaque table et le nombre de lignes de données actuellement dans la table.

Suppression d'une tableSuppression d'une table existante du service mobile
--------------------------------------------------------------------------

La commande suivante supprime une table du service mobile, où `<service-name>` est le nom du service mobile et où `<table-name>` est le nom de la table à supprimer :

     azure mobile table delete <service-name> <table-name> -q

Dans les scripts d'automatisation, utilisez le paramètre `-q` pour supprimer la table sans afficher d'invite de confirmation qui bloque l'exécution.

Enregistrement d'un scriptEnregistrement d'un script sur une opération de table
-------------------------------------------------------------------------------

La commande suivante charge et enregistre une fonction sur une opération sur une table, où `<service-name>` est le nom du service mobile, où `<table-name>` est le nom de la table et où `<operation>` est l'opération de table, qui peut être `read`, `insert`, `update` ou `delete` :

     azure mobile script upload <service-name> table/<table-name>.<operation>.js

Notez que cette opération charge un fichier JavaScript (.js) à partir de l'ordinateur local. Le nom du fichier doit se composer des noms d'une table et d'une opération et doit être situé dans le sous-dossier `table` par rapport à l'emplacement où est exécutée la commande. Par exemple, l'opération suivante charge et enregistre un nouveau script `insert` appartenant à la table `TodoItems` :

     azure mobile script upload todolist table/todoitems.insert.js

La déclaration de fonction du fichier de script doit également correspondre à l'opération de table enregistrée. Cela signifie que, pour un script `insert`, le script chargé contient une fonction avec la signature suivante :

     function insert(item, user, request) {
            ...
        } 

Pour plus d'informations sur l'enregistrement des scripts, consultez la page [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/p?LinkId=262293).

