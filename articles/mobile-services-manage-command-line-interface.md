<properties 
	pageTitle="Administration d'un service mobile à l'aide d'une invite de commandes - Didacticiel Azure" 
	description="Découvrez comment créer, déployer et gérer votre service mobile Azure à l'aide des outils en ligne de commande." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Automatisation des services mobiles avec les outils en ligne de commande 

Cette rubrique explique comment utiliser les outils en ligne de commande Azure pour automatiser la création et la gestion d'Azure Mobile Services. Elle montre également comment installer et prendre en main les outils en ligne de commande et les utiliser pour effectuer les tâches Mobile Services suivantes :

-	[Création d'un service mobile]
-	[Création d'une table]
-   [Enregistrement d'un script dans une opération de table][Inscription d'un nouveau script de table]
-   [Affichage de la liste des tables]
- 	[Suppression d'une table existante]
-	[Affichage de la liste des services mobiles]
-   [Suppression d'un service mobile existant]
 
Lorsqu'elles sont combinées dans un seul script ou dans un fichier de commandes, ces commandes individuelles automatisent le processus de création, de vérification et de suppression d'un service mobile. 

Pour utiliser les outils en ligne de commande Azure afin de gérer Mobile Services, vous devez disposer d'un compte Azure sur lequel la fonctionnalité Azure Mobile Services est activée.

+ Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/" target="_blank">Version d'évaluation gratuite Azure</a>.

+ Si vous disposez d'un compte, mais que vous devez activer la version préliminaire d'Azure Mobile Services, consultez la rubrique <a href="http://azure.microsoft.com/documentation/articles/php-create-account/#enable" target="_blank">Activation des fonctionnalités préliminaires d'Azure</a>.

Cette rubrique présente une sélection de tâches d'administration courantes prises en charge par les outils en ligne de commande Azure. Pour plus d'informations, consultez la [documentation sur les outils en ligne de commande Azure][reference-docs].

<!--+  Vous devez télécharger et installer les outils de ligne de commande Azure sur votre ordinateur local. Pour cela, suivez les instructions de la première section de cette rubrique. 

+ (Facultatif) Pour pouvoir exécuter des requêtes HTTP directement à partir de la ligne de commande, vous devez utiliser cURL ou un outil équivalent. cURL fonctionne sur différentes plateformes. Recherchez et installez cURL pour votre plateforme à partir de la <a href=http://go.microsoft.com/fwlink/p/?LinkId=275676 target="_blank">page de téléchargement cURL</a>.-->

<h2><a name="install"></a>Installation des outils en ligne de commande Azure</h2>

La liste suivante contient des informations permettant d'installer les outils en ligne de commande en fonction de votre système d'exploitation :

* **Windows** : téléchargez le [programme d'installation des outils en ligne de commande Azure][windows-installer]. Ouvrez le fichier .msi téléchargé et suivez les étapes d'installation indiquées.

* **Mac** : téléchargez le [programme d'installation du Kit de développement logiciel (SDK)Azure][mac-installer]. Ouvrez le fichier .pkg téléchargé et suivez les étapes d'installation indiquées.

* **Linux** : installez la dernière version de [Node.js][nodejs-org] (voir [Installation de Node.js via Package Manager][install-node-linux]), puis exécutez la commande suivante :

		npm install azure-cli -g

Pour tester l'installation, tapez `azure` à l'invite de commandes. Lorsque l'installation est terminée, une liste de toutes les commandes `azure` disponibles apparaît.
<h2><a name="import-account"></a>Téléchargement et importation des paramètres de publication.</h2>

Pour commencer, vous devez d'abord télécharger et importer vos paramètres de publication. Cela vous permet d'utiliser les outils servant à créer et à gérer les services Azure. Pour télécharger vos paramètres de publication, utilisez la commande `account download` :

		azure account download

Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au portail de gestion. Une fois que vous êtes connecté, votre fichier `.publishsettings` est téléchargé. Notez l'emplacement d'enregistrement du fichier.

Importez ensuite le fichier `.publishsettings` en exécutant la commande suivante, en remplaçant <path-to-settings-file> par le chemin d'accès de votre fichier `.publishsettings` :

		azure account import <path-to-settings-file>

Vous pouvez supprimer toutes les informations stockées par la commande <code>import</code> en utilisant la commande <code>account clear</code> :

		azure account clear

Pour afficher la liste des options des commandes `account`, utilisez l'option -help :

		azure account -help

Après avoir importé vos paramètres de publication, veillez à supprimer le fichier `.publishsettings` pour des raisons de sécurité. Pour plus d'informations, consultez la page [Installation des outils en ligne de commande Azure pour Mac et Linux]. Vous êtes désormais prêt à créer et à gérer Azure Mobile Services à partir de la ligne de commande ou dans des fichiers de commandes.  

<h2><a name="create-service"></a>Création d'un service mobile</h2>

Vous pouvez utiliser les outils en ligne de commande pour créer une instance de service mobile. Lors de la création du service mobile, vous créez également une instance de base de données SQL dans un nouveau serveur. 

La commande suivante crée une instance de service mobile dans votre abonnement, où`<service-name>` est le nom du nouveau service mobile, `<server-admin>` le nom de connexion du nouveau serveur, et `<server-password>` le mot de passe de la nouvelle connexion :

		azure mobile create <service-name> <server-admin> <server-password>

La commande `mobile create` échoue si le service mobile spécifié existe. Dans vos scripts d'automatisation, vous devez essayer de supprimer un service mobile avant de tenter de le recréer.

<h2><a name="list-services"></a>Affichage de la liste des services mobiles existants dans un abonnement</h2>

La commande suivante renvoie la liste de tous les services mobiles d'un abonnement Azure :

		azure mobile list

Cette commande affiche également l'état actuel et l'URL de chaque service mobile.

<h2><a name="delete-service"></a>Suppression d'un service mobile existant</h2>

Vous pouvez utiliser les outils en ligne de commande pour supprimer un service mobile existant ainsi que le serveur et la base de données SQL connexes. La commande suivante supprime le service mobile, où <service-name> est le nom du service mobile à supprimer :

		azure mobile delete <service-name> -a -q

En incluant les paramètres -a et -q, cette commande supprime également le serveur et la base de données SQL utilisés par le service mobile sans afficher d'invite.

> [AZURE.NOTE] Si vous ne spécifiez pas le paramètre <code>-q</code> avec <code>-a</code> ou <code>-d</code>, l'exécution est mise en pause et vous êtes invité à sélectionner les options de suppression pour votre base de données SQL. Utilisez le paramètre <code>-a</code> uniquement lorsqu'aucun autre service mobile n'utilise la base de données ou le serveur. Sinon, utilisez le paramètre <code>-d</code> pour supprimer seulement les données appartenant au service mobile en cours de suppression.

<h2><a name="create-table"></a>Création d'une table dans le service mobile</h2>

La commande suivante supprime la table dans le service mobile spécifié, où <service-name> est le nom du service mobile et<table-name>est le nom de la table à créer :

		azure mobile table create <service-name> <table-name>

Ceci permet de créer une table avec les autorisations par défaut, `application`, pour les opérations de table : `insert`, `read`, `update` et `delete`. 

La commande suivante crée une table dont l'autorisation `read` est définie sur public, mais dont l'autorisation `delete` est octroyée uniquement aux administrateurs :

		azure mobile table create <service-name> <table-name> -p read=public,delete=admin

Le tableau suivant présente une comparaison entre la valeur d'autorisation du script et la valeur d'autorisation du [portail de gestion Azure].

<table border="1" width="100%"><tr><th>Valeur du script</th><th>Valeur du portail de gestion</th></tr>
<tr><td><code>public</code></td><td>Tout le monde</td></tr>
<tr><td><code>application</code> (par défaut)</td><td>Toute personne avec la clé d'application</td></tr>
<tr><td><code>user</code></td><td>Seuls les utilisateurs authentifiés</td></tr>
<tr><td><code>admin	</code></td><td>Seuls les scripts et les administrateurs</td></tr></table>

La commande `mobile table create` échoue si la table spécifiée existe. Dans vos scripts d'automatisation, vous devez essayer de supprimer une table avant de tenter de la recréer.

<h2><a name="list-tables"></a>Affichage de la liste des tables existantes dans un service mobile</h2>

La commande suivante renvoie la liste de toutes les tables d'un service mobile, où <service-name> est le nom du service mobile :

		azure mobile table list <service-name>

Cette commande affiche également le nombre d'index sur chaque table et le nombre de lignes de données actuellement dans la table.

<h2><a name="delete-table"></a>Suppression d'une table existante du service mobile</h2>

La commande suivante supprime la table du service mobile, où <service-name> est le nom du service mobile et<table-name>est le nom de la table à supprimer :

		azure mobile table delete <service-name> <table-name> -q

Dans les scripts d'automatisation, utilisez le paramètre -q pour supprimer la table sans afficher d'invite de confirmation qui bloque l'exécution.

<h2><a name="register-script"></a>Enregistrement d'un script dans une opération de table</h2>

La commande suivante télécharge et inscrit une fonction dans une opération sur une table, où <service-name> est le nom du service mobile,<table-name> est le nom de la table, et <operation> est l'opération de table, qui peut être définie sur `read`, `insert`, `update` ou `delete` :

		azure mobile script upload <service-name> table/<table-name>.<operation>.js

Notez que cette opération charge un fichier JavaScript (.js) à partir de l'ordinateur local. Le nom du fichier doit se composer des noms d'une table et d'une opération et doit être situé dans le sous-dossier `table` par rapport à l'emplacement où est exécutée la commande. Par exemple, l'opération suivante charge et enregistre un nouveau script `insert` appartenant à la table `TodoItems` :

		azure mobile script upload todolist table/todoite
	ms.insert.js

La déclaration de fonction du fichier de script doit également correspondre à l'opération de table enregistrée. Cela signifie que, pour un script `insert`, le script chargé contient une fonction avec la signature suivante :

		function insert(item, user, request) {
		    ...
		} 

Pour plus d'informations sur l'enregistrement des scripts, consultez la page [Référence de script serveur Mobile Services].

<!--<h2><a name="test-service"></a>Test du nouveau service mobile</h2>

Lorsque vous automatisez la création de votre service mobile, vous pouvez éventuellement utiliser cURL ou un autre générateur de requête de ligne de commande. 

## <a name="nextsteps"> </a>Étapes suivantes
Étapes suivantes ici...
-->
<!-- Anchors. -->
[Téléchargement et installation des outils en ligne de commande]: #install
[Téléchargement et importation des paramètres de publication]: #import
[Création d'un service mobile]: #create-service
[Obtention de la clé principale]: #get-master-key
[Création d'une table]: #create-table
[Inscription d'un nouveau script de table]: #register-script
[Suppression d'une table existante]: #delete-table
[Suppression d'un service mobile existant]: #delete-service
[Test du service mobile]: #test-service
[Affichage de la liste des services mobiles]: #list-services
[Affichage de la liste des tables]: #list-tables
[Étapes suivantes]: #next-steps

<!-- Images. -->











<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/p?LinkId=262293

[Portail de gestion Azure]: https://manage.windowsazure.com/
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager

[mac-installer]: http://go.microsoft.com/fwlink/p?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/p?LinkID=275464
[reference-docs]: /fr-fr/manage/linux/other-resources/command-line-tools/#Commands_to_manage_mobile_services
[Installation des outils en ligne de commande Azure pour Mac et Linux]: http://go.microsoft.com/fwlink/p/?LinkId=275795



<!--HONumber=42-->
