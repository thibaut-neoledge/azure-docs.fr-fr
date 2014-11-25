<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure websites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure websites in Azure, including how to configure a website to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Websites" authors="mwasson" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/18/2014" ms.author="mwasson" />

# Configuration des sites web

Dans le portail de gestion Azure, vous pouvez changer les options de configuration des sites web, ainsi que les liens vers d'autres ressources Azure, par exemple une base de données.

## Sommaire

-   [changement des options de configuration d'un site web][changement des options de configuration d'un site web]
-   [configuration d'un site web pour utiliser une base de données SQL][configuration d'un site web pour utiliser une base de données SQL]
-   [configuration d'un site web pour utiliser une base de données MySQL][configuration d'un site web pour utiliser une base de données MySQL]
-   [configuration d'un nom de domaine personnalisé][configuration d'un nom de domaine personnalisé]
-   [configuration d'un site web pour utiliser SSL][configuration d'un site web pour utiliser SSL]
-   [Étapes suivantes][Étapes suivantes]

## <a name="howtochangeconfig"></a> changement des options de configuration d'un site web

<!-- HOW TO: CHANGE CONFIGURATION OPTIONS FOR A WEBSITE -->

Pour changer les options de configuration d'un site web :

1.  Dans le [portail de gestion][portail de gestion], ouvrez les pages de gestion du site web.
2.  Cliquez sur l’onglet **Configurer**.

L'onglet **Configurer** comporte les sections suivantes :

### Généralités

**Versions d'infrastructure**. Définissez ces options si votre application utilise l'une de ces infrastructures :

-   **.NET Framework** : définissez la version du .NET Framework.
-   **PHP** : définissez la version du langage PHP, ou choisissez **INACTIF** pour désactiver PHP.
-   **Java** : sélectionnez la version affichée pour activer le langage Java, ou choisissez **INACTIF** pour désactiver Java.
-   Si vous activez Java, utilisez l'option **Conteneur Web** pour choisir entre les versions Tomcat et Jetty.
-   **Python** : sélectionnez la version du langage Python, ou choisissez **INACTIF** pour désactiver Python.

Pour des raisons techniques, l'activation de Java pour votre site web désactive les options .NET, PHP et Python.

**Mode Pipeline géré**. Définit le [mode pipeline][mode pipeline] d'IIS. Laissez la valeur par défaut, Intégré, sauf si vous avez un site web qui demande une version plus ancienne d'IIS.

**Plateforme**. Indique si votre application s'exécute dans un environnement 32 bits ou 64 bits. L'environnement 64 bits demande le mode De base ou Standard. Les modes Gratuit et Partagé s'exécutent uniquement dans un environnement 32 bits.

**WebSockets**. Affectez la valeur **ACTIF** pour activer le protocole WebSocket. Par exemple, si votre site web utilise [ASP.NET SignalR][ASP.NET SignalR] ou [socket.io][socket.io].

**Toujours actif**. Par défaut, les sites web sont déchargés s'ils sont inactifs pendant un certain temps. Cela permet au système d'économiser des ressources. En mode De base ou Standard, vous pouvez activer l'option **Toujours actif** pour garder le site chargé en permanence. Si votre site exécute des travaux web en continu, activez l'option **Toujours actif**. Sinon, les travaux web risquent de ne pas s'exécuter de façon fiable.

**Modifier dans Visual Studio Online**. Permet de modifier du code en direct à l'aide de Visual Studio Online. Si cette option est activée, l'onglet Tableau de bord affiche un lien appelé **Modifier dans Visual Studio Online**, sous la section **Aperçu rapide**. Cliquez sur ce lien pour modifier directement votre site web en ligne. Si une authentification est nécessaire, vous pouvez utiliser vos informations d'identification de déploiement de base.

Remarque : si vous avez activé le déploiement à partir du contrôle de code source, un déploiement peut remplacer les changements effectués dans l'éditeur Visual Studio Online.

### Certificats

En mode De base ou Standard, vous pouvez télécharger des certificats SSL pour un domaine personnalisé. Pour plus d'informations, consultez [Activation du protocole HTTPS pour un site web Azure][Activation du protocole HTTPS pour un site web Azure].

Vos certificats téléchargés sont répertoriés ici. Après avoir téléchargé un certificat, vous pouvez l'attribuer à n'importe quel site web dans votre abonnement et votre région. Les certificats avec caractères génériques peuvent être utilisés pour n'importe quel site du domaine pour lequel ils sont valides. Un certificat peut être supprimé uniquement s'il n'existe aucune liaison active pour ce dernier.

### Noms de domaine

Affichez ou ajoutez des noms de domaine supplémentaires pour le site web. Pour plus d'informations, consultez [Configuration d'un nom de domaine personnalisé pour un site web Azure][Configuration d'un nom de domaine personnalisé pour un site web Azure].

### Liaisons SSL

Si vous avez téléchargé des certificats SSL, vous pouvez les lier à des noms de domaine personnalisés. Pour plus d'informations, consultez [Activation du protocole HTTPS pour un site web Azure][Activation du protocole HTTPS pour un site web Azure].

### Déploiements

Cette section s'affiche seulement si vous avez activé le déploiement à partir du contrôle de code source. Utilisez ces paramètres pour configurer les déploiements.

-   **URL Git**. Si vous avez créé un référentiel Git pour votre site web Azure, il s'agit de l'URL vers laquelle vous effectuez une transmission de type push de votre contenu.
-   **URL du déclencheur du déploiement**. Cette URL peut être définie sur un référentiel GitHub, CodePlex, Bitbucket ou tout autre référentiel pour déclencher le déploiement quand une validation fait l'objet d'une transmission de type push vers le référentiel.
-   **Branche à déployer**. Spécifie la branche à déployer quand vous effectuez une transmission de type push du contenu.

Pour configurer le déploiement à partir du contrôle de code source, consultez l'onglet **Tableau de bord**, puis cliquez sur **Configurer le déploiement à partir du contrôle de code source**.

### Diagnostic d'application

Options liées à l'écriture des journaux de diagnostic à partir d'une application web qui prend en charge la journalisation :

-   **Système de fichiers**. Écrit les journaux dans le système de fichiers du site web. La journalisation du système de fichiers a une durée de 12 heures. Vous pouvez accéder aux journaux à partir du partage FTP du site web. (Consultez [Informations d'identification FTP][Informations d'identification FTP]).
-   **Stockage de tables**. Écrit les journaux dans le stockage de tables Azure. Il n'y a aucune limite de temps. En outre, la journalisation reste activée jusqu'à ce que vous la désactiviez.
-   **Stockage d'objets BLOB**. Écrit les journaux dans le stockage d'objets Blob Azure. Il n'y a aucune limite de temps. En outre, la journalisation reste activée jusqu'à ce que vous la désactiviez.

**Niveau de journalisation**. Quand la journalisation est activée, cette option indique la quantité d'informations qui sera enregistrée (Erreur, Avertissement, Information ou Détaillé).

**Gérer le stockage des tables**. Quand le stockage de table est activé, cliquez sur ce bouton pour définir le compte de stockage et le nom de la table.

**Gérer le stockage des objets blob.** Quand le stockage d'objets blob est activé, cliquez sur ce bouton pour définir le compte de stockage et le nom du stockage d'objets blob.

### Diagnostic de site

Options de collecte d'informations de diagnostic pour votre site web.

**Journalisation du serveur Web**. Active la journalisation du serveur web. Les journaux sont enregistrés au format de fichier journal étendu W3C. Vous pouvez les enregistrer dans Azure Storage ou dans le système de fichiers du site web.

-   Si vous choisissez **Système de fichiers**, les journaux sont enregistrés sur le site FTP indiqué sous « Journaux de diagnostic FTP » dans la page Tableau de bord. (Consultez [Informations d'identification FTP][Informations d'identification FTP]).
-   Si vous choisissez **Système de fichiers**, utilisez la zone **Quota** pour définir l'espace disque maximal alloué aux fichiers journaux. L'espace est compris entre 20 Mo et 100 Mo. La valeur par défaut est de 35 Mo. Lorsque le quota est atteint, les fichiers les plus anciens sont remplacés par les plus récents. Si vous devez conserver plus de 100 Mo de données d'historique, utilisez Azure Storage, car sa capacité de stockage est bien supérieure.
-   Éventuellement, cliquez sur **Définir la rétention** pour supprimer automatiquement les fichiers après un certain délai. Par défaut, les journaux ne sont jamais supprimés.

**Messages d'erreur détaillés**. Si cette option est activée, les messages d'erreur détaillés sont enregistrés en tant que fichiers .htm. Pour afficher les fichiers, accédez au site FTP indiqué sous « Journaux de diagnostic FTP » dans la page Tableau de bord. Les fichiers sont enregistrés sous /LogFiles/DetailedErrors sur le site FTP. (Consultez [Informations d'identification FTP][Informations d'identification FTP]).

**Suivi des demandes ayant échoué**. Si cette option est activée, les requêtes ayant échoué sont journalisées dans des fichiers XML. Pour afficher les fichiers, accédez au site FTP indiqué sous « Journaux de diagnostic FTP » dans la page Tableau de bord. (Consultez [Informations d'identification FTP][Informations d'identification FTP]). Les fichiers sont enregistrés sous /LogFiles/W3SVC*xxx*, où xxx est un identificateur unique. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Veillez à télécharger le fichier XSL, car il fournit des fonctionnalités de mise en forme et de filtrage du contenu des fichiers XML.

**Débogage distant**. Active le débogage à distance. Quand cette option est activée, vous pouvez utiliser le débogueur distant de Visual Studio pour vous connecter directement à votre site web Azure. Le débogage à distance reste activé pendant 48 heures.

**Remarque** : le débogage à distance ne fonctionne pas avec un nom de site ou d'utilisateur comportant plus de 20 caractères.

### Surveillance

En mode De base ou Standard, vous pouvez tester la disponibilité des points de terminaison HTTP ou HTTPS, à partir de trois emplacements géo-distribués au maximum. Un test de surveillance échoue si le code de réponse HTTP est une erreur (4xx ou 5xx) ou si la réponse prend plus de 30 secondes. Un point de terminaison est considéré comme disponible s'il passe avec succès les différents tests de surveillance à partir de tous les emplacements spécifiés.

Pour plus d'informations, consultez la page [surveillance de l'état d'un point de terminaison web][surveillance de l'état d'un point de terminaison web].

### Analyse développeur

Choisissez **Module complémentaire** pour sélectionner un module complémentaire d'analyse dans une liste, ou pour accéder au Magasin Azure et en choisir un. Sélectionnez **Personnalisé** pour choisir un fournisseur d'analyses dans une liste ; New Relic, par exemple. Si vous utilisez un fournisseur personnalisé, vous devez entrer la clé de licence dans la zone **Clé de fournisseur**.

Pour plus d'informations sur l'utilisation de New Relic avec Sites Web Azure, consultez [Gestion des performances de l'application New Relic sur des sites web Azure][Gestion des performances de l'application New Relic sur des sites web Azure].

### Paramètres de l'application

Paires nom/valeur qui seront chargées par votre application web au démarrage.

-   Dans le cas des sites .NET, ces paramètres sont inclus dans votre configuration .NET AppSettings au moment de l'exécution, en remplacement des paramètres existants.

-   Les applications PHP, Python, Java et Node peuvent accéder à ces paramètres sous forme de variables d'environnement au moment de l'exécution. Pour chaque paramètre d'application, deux variables d'environnement sont créées : l'une avec le nom spécifié par l'entrée du paramètre d'application, et l'autre avec le préfixe APPSETTING\_. Elles contiennent toutes les deux la même valeur.

### Chaînes de connexion

Chaînes de connexion des ressources liées.

Pour les sites .NET, ces chaînes de connexion sont incluses dans les paramètres connectionStrings de votre configuration .NET au moment de l'exécution, en remplacement des entrées existantes où la clé est identique à celle du nom de la base de données liée.

Pour les applications PHP, Python, Java et Node, ces paramètres sont disponibles en tant que variables d'environnement au moment de l'exécution, avec le type de connexion comme préfixe. Les préfixes de la variable d'environnement sont les suivants :

-   SQL Server : SQLCONNSTR\_
-   MySQL : MYSQLCONNSTR\_
-   Base de données SQL : SQLAZURECONNSTR\_
-   Personnalisé : CUSTOMCONNSTR\_

Par exemple, si une chaîne de connexion MySql se nomme connectionstring1, elle est accessible par le biais de la variable d'environnement `MYSQLCONNSTR_connectionString1`.

**Remarque** : Les chaînes de connexion sont également créées quand vous liez une ressource de base de données à un site web. Les chaînes de connexion créées de cette façon sont en lecture seule quand elles sont affichées
dans la page de gestion de la configuration.

### Documents par défaut

Le document par défaut d'un site web est la page web qui s'affiche sans spécifier de page particulière. Si votre site web contient plusieurs fichiers dans la liste, assurez-vous de mettre votre document par défaut en haut de la liste.

Les applications web peuvent utiliser des modules qui effectuent un routage en fonction de l'URL, au lieu de proposer du contenu statique. Dans ce cas, il n'existe pas de document par défaut.

### Mappages de gestionnaires

Utilisez cette zone pour ajouter des processeurs de script personnalisés et gérer les requêtes portant sur des extensions de fichiers spécifiques.

-   **Extension**. Extension de fichier à gérer, par exemple \*.php ou handler.fcgi.
-   **Chemin d'accès du processeur de script**. Chemin d'accès absolu du processeur de script. Les requêtes de fichiers qui correspondent à l'extension de fichier sont traitées par le processeur de script. Utilisez le chemin d'accès `D:\home\site\wwwroot` pour faire référence au répertoire racine de votre site web.
-   **Arguments supplémentaires**. Arguments de ligne de commande facultatifs pour le processeur de script.

### Applications et répertoires virtuels

Pour configurer des applications et des répertoires virtuels associés à votre site web, spécifiez chaque répertoire virtuel et son chemin d'accès physique correspondant par rapport à la racine du site. Vous pouvez éventuellement activer la case à cocher **Application** pour marquer un répertoire virtuel comme application dans la configuration du site.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A SQL DATABASE -->

## <a name="howtoconfigSQL"></a> configuration d'un site web pour utiliser une base de données SQL

Pour lier un site web à une base de données SQL, procédez comme suit :

1.  Dans le [portail de gestion][1], sélectionnez **Sites Web** pour afficher la liste des sites web créés par le compte connecté.

2.  Sélectionnez un site web dans la liste des sites web pour ouvrir les pages **Gestion** correspondantes.

3.  Cliquez sur l'onglet **Linked Resources**. Le message suivant s'affiche alors sur la page **Linked Resources** : **You have no linked resources**.

4.  Cliquez sur **Link a Resource** pour ouvrir l'Assistant **Link a Resource**.

5.  Cliquez sur **Créer une ressource** pour afficher la liste des types de ressource qu'il est possible de lier à votre site web.

6.  Cliquez sur **Base de données SQL** pour afficher l'Assistant **Link Database**.

7.  Renseignez les champs obligatoires sur les pages 3 et 4 de l'Assistant **Link Database**, puis cliquez sur la coche **Terminer** sur la page 4.

Azure va alors créer une base de données SQL avec les paramètres spécifiés et la lier au site web.

<!-- HOW TO: CONFIGURE A WEBSITE TO USE A MYSQL DATABASE -->

## <a name="howtoconfigMySQL"></a> configuration d'un site web pour utiliser une base de données MySQL

Pour configurer un site web et utiliser une base de données MySQL, suivez la procédure d'utilisation d'une base de données SQL, mais dans l'Assistant **Lier une ressource**, choisissez **Base de données MySQL** au lieu de **Base de données SQL**.

Une autre solution consiste à créer le site web avec l'option **Création personnalisée**. Dans la liste déroulante **Base de données**, sélectionnez **Create a new MySQL database** ou **Use an existing MySQL database**.

## <a name="howtodomain"></a> Configuration d'un nom de domaine personnalisé

Pour plus d'informations sur la configuration de votre site web afin d'utiliser un nom de domaine personnalisé, consultez [Configuration d'un nom de domaine personnalisé pour un site web Azure][Configuration d'un nom de domaine personnalisé pour un site web Azure].

## <a name="howtoconfigSSL"></a> configuration d'un site web pour utiliser SSL

Pour plus d'informations sur la configuration du protocole SSL pour un domaine personnalisé sur Azure, consultez l'article [Activation du protocole HTTPS pour un site Web Azure][Activation du protocole HTTPS pour un site Web Azure].

## <a name="next"></a>Étapes suivantes

-   [Mise à l'échelle de sites Web][Mise à l'échelle de sites Web]

-   [Surveillance de sites Web][Surveillance de sites Web]

  [changement des options de configuration d'un site web]: #howtochangeconfig
  [configuration d'un site web pour utiliser une base de données SQL]: #howtoconfigSQL
  [configuration d'un site web pour utiliser une base de données MySQL]: #howtoconfigMySQL
  [configuration d'un nom de domaine personnalisé]: #howtodomain
  [configuration d'un site web pour utiliser SSL]: #howtoconfigSSL
  [Étapes suivantes]: #next
  [portail de gestion]: https://manage.windowsazure.com/
  [mode pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
  [ASP.NET SignalR]: http://www.asp.net/signalr
  [socket.io]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-nodejs-chat-app-socketio/
  [Activation du protocole HTTPS pour un site web Azure]: href="http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
  [Configuration d'un nom de domaine personnalisé pour un site web Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-custom-domain-name/
  [Informations d'identification FTP]: http://azure.microsoft.com/fr-fr/documentation/articles/web-sites-manage#ftp-credentials
  [surveillance de l'état d'un point de terminaison web]: http://go.microsoft.com/fwLink/?LinkID=279906&clcid=0x409
  [Gestion des performances de l'application New Relic sur des sites web Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/
  [1]: http://manage.windowsazure.com
  [Activation du protocole HTTPS pour un site Web Azure]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-configure-ssl-certificate/
  [Mise à l'échelle de sites Web]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-scale/
  [Surveillance de sites Web]: http://www.windowsazure.com/fr-fr/documentation/articles/web-sites-monitor/
