<properties linkid="manage-services-how-to-configure-websites" urlDisplayName="How to configure" pageTitle="How to configure web sites - Azure service management" metaKeywords="Azure websites, configuring Azure websites, Azure SQL database, Azure MySQL" description="Learn how to configure web sites in Azure, including how to configure a web site to use a SQL Database or MySQL database." metaCanonical="" services="web-sites" documentationCenter="" title="How to Configure Web Sites" authors="timamm" solutions="" manager="" editor="mollybos" />

Configuration des sites Web
===========================

Le portail de gestion Azure vous permet de modifier les options de configuration des sites Web, ainsi que de lier un site Web à d'autres ressources Azure. Vous pouvez, par exemple, lier des sites Web à une base de données SQL afin de fournir des fonctionnalités supplémentaires. Il est également possible de configurer des sites Web en vue d'utiliser une base de données MySQL nouvelle ou existante.

Sommaire
--------

-   [Modification des options de configuration d'un site Web](#howtochangeconfig)
-   [Configuration d'un site Web afin d'utiliser une base de données SQL](#howtoconfigSQL)
-   [Configuration d'un site Web afin d'utiliser une base de données MySQL](#howtoconfigMySQL)
-   [Configuration d'un nom de domaine personnalisé](#howtodomain)
-   [Configuration d'un site Web afin d'utiliser SSL](#howtoconfigSSL)
-   [Étapes suivantes](#next)

Modification des options de configuration d'un site Web
-------------------------------------------------------

Pour modifier les options de configuration d'un site Web, procédez comme suit.

1.  Dans le portail de gestion, ouvrez les pages de gestion du site Web.
2.  Cliquez sur l'onglet **Configurer** pour ouvrir la page de gestion **Configurer**.
3.  Définissez les options de configuration suivantes pour le site Web :
    -   **général**
        -   **.NET Framework Version** : si votre application Web utilise .NET Framework, définissez la version requise.
        -   **PHP Version** : si votre application Web utilise PHP, définissez la version requise.
        -   **Java Version** : sélectionnez la version affichée de Java afin de l'activer pour votre application Web ou sélectionnez **OFF** pour désactiver Java. Si vous activez Java pour votre application Web, l'option **Web Container** vous permet de choisir entre les versions Tomcat et Jetty.

            **Remarque** : pour des raisons techniques, l'activation de Java pour votre site Web désactive les options .NET, PHP et Python.

        -   **Python Version** : affiche la version Python (non configurable).
        -   **Mode pipeline géré** : deux options vous sont proposées **Classique** et **Intégré** (option par défaut). N'utilisez l'option Classique que si vous avez des sites Web hérités fonctionnant exclusivement sur des versions plus anciennes d'IIS.
        -   **Plate-forme** : dans le cas des sites en mode Standard, vous pouvez indiquer si l'application doit s'exécuter dans un environnement 32 bits ou 64 bits. Les sites en modes Gratuit et Partagé s'exécutent uniquement dans un environnement 32 bits.
        -   **Web Sockets** : sélectionnez **On** pour permettre à votre site Web d'utiliser des applications de modèle de requête en temps réel, telles que la conversation instantanée.
        -   **Always On** : par défaut, les sites Web sont déchargés s'ils sont inactifs depuis une certaine période. Cela permet au système d'économiser des ressources. Vous pouvez activer le paramètre **Always On** pour un site en mode Standard s'il doit être chargé en permanence. Il se peut que les tâches Web en continu ne s'exécutent pas de manière fiable lorsque le paramètre **Always On** est désactivé. Il est donc conseillé de l'activer lors de l'exécution de tâches de ce type sur le site.
        -   **Edit in Visual Studio Online** : sélectionnez **On** pour activer la modification de code dynamique avec Visual Studio Online. Lorsque vous avez enregistré cette modification de configuration, le lien **Edit in Visual Studio Online** s'affiche dans la section **Quick Glance** de l'onglet Tableau de bord. Cliquez sur ce lien pour modifier directement votre site Web en ligne. Si une authentification est nécessaire, vous pouvez utiliser vos informations d'identification de déploiement de base.

            **Remarque** : si vous avez activé le déploiement à partir du contrôle de code source, il est possible qu'un déploiement écrase les modifications effectuées dans l'éditeur Visual Studio Online. Il est donc préférable de ne pas activer cette option si vous souhaitez modifier directement le contenu du site avec Visual Studio Online.

    -   **Certificats** : en mode Standard uniquement, vous pouvez cliquer sur **Télécharger** pour télécharger un certificat SSL pour un domaine personnalisé. Les certificats téléchargés sont répertoriés ici. Les certificats avec caractère générique (certificats avec un astérisque) sont pris en charge. Après avoir téléchargé un certificat, vous pouvez l'attribuer à n'importe quel site Web dans votre abonnement et votre région. Un certificat avec un astérisque ne doit être téléchargé qu'une seule fois, mais il peut être utilisé avec n'importe quel site dans le domaine pour lequel il est valide. Un certificat ne peut être supprimé que si aucune liaison correspondante n'est active dans un site.
        **Remarque :** les domaines personnalisés ne sont disponibles que dans les modes Partagé et Standard. Quant à la prise en charge SSL des domaines personnalisés, elle n'est disponible qu'en mode Standard. Pour plus d'informations sur la configuration du protocole SSL pour un domaine personnalisé sur Azure, consultez l'article [Activation du protocole HTTPS pour un site Web Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).
    -   **Noms de domaine** : vous pouvez afficher ou ajouter ici des noms de domaine supplémentaires pour le site Web. Vous pouvez ajouter des domaines personnalisés en cliquant sur **Gérer les domaines**. Les domaines personnalisés ne sont disponibles que dans les modes **Partagé** et **Standard**. Vous pouvez modifier le mode du site Web sur la page de gestion **Scale**. Les domaines personnalisés ne sont pas disponibles en mode Gratuit. Pour plus d'informations sur la configuration de domaines personnalisés, consultez l'article [Configuration d'un nom de domaine personnalisé pour un site Web Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).
    -   **SSL Bindings** : les liaisons SSL vers des domaines personnalisés ne sont disponibles qu'en mode Standard. Choisissez un mode SSL (**SNI**, **IP** ou **Pas de SSL**) pour un nom de domaine particulier. Si vous choisissez SNI ou IP, vous pouvez spécifier un certificat pour le domaine à partir des certificats que vous avez téléchargés. Pour plus d'informations sur la configuration du protocole SSL pour un domaine personnalisé sur Azure, consultez l'article [Activation du protocole HTTPS pour un site Web Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/). Pour plus d'informations sur SNI, consultez l'article [Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication).
    -   **Déploiements** : utilisez ces paramètres pour configurer des déploiements.
        -   **URL Git** : si vous avez créé un référentiel Git sur votre site Web Azure, il s'agit de son adresse URL ; à savoir l'emplacement vers lequel vous diffusez votre contenu.
        -   **Deployment Trigger URL** : cette URL peut être définie sur GitHub, CodePlex, Bitbucket ou un autre référentiel afin de déclencher le déploiement lorsqu'une validation est envoyée au référentiel.
        -   **Branch to Deploy** : cette option vous permet de spécifier la branche qui sera déployée lorsque vous diffuserez du contenu vers celle-ci.
    -   **Diagnostic d'application** : configurez les options de collecte des traces de diagnostic à partir d'une application dont le code a été instrumenté avec des traces. Les options de journalisation relatives au diagnostic d'application sont les suivantes :
        -   **Application Logging (File System)** : sélectionnez **On** pour que les journaux d'application soient écrits dans le système de fichiers du site Web. Lorsqu'elle est activée, la journalisation du système de fichiers a une durée de 12 heures. Vous pouvez accéder aux journaux à partir du partage FTP du site Web. Le lien vers le partage FTP est également disponible sur le **Tableau de bord**. Sous **Quick Glance**, sélectionnez **FTP Diagnostic Logs** ou **FTPS Diagnostic Logs**.
        -   **Application Logging (Storage)** : sélectionnez **On** pour que vos journaux d'application soient écrits dans un compte de stockage Azure. La journalisation de données dans un compte de stockage n'est pas limitée dans le temps et reste active jusqu'à ce que vous la désactiviez. Par défaut, les journaux sont stockés dans une table nommée WAWSAppLogTable.
        -   **Logging Level** : lorsque la journalisation est activée, cette option indique la quantité d'informations qui sera enregistrée (Erreur, Avertissement, Information ou Détaillé).
        -   **Diagnostic Storage** : lorsque vous cliquez sur **Manage Connection**, la boîte de dialogue **Manage diagnostic storage** s'ouvre. Elle contient les options suivantes pour vous permettre d'enregistrer des journaux dans votre compte de stockage Azure :
            -   **Nom du compte de stockage** : sélectionnez le compte de stockage dans lequel vous souhaitez enregistrer les journaux.
            -   **Clé d'accès de stockage** : affiche la clé du compte de stockage que vous avez choisi. Si vous avez régénéré la clé du compte de stockage, tapez manuellement la nouvelle clé dans cette zone ou utilisez l'un des boutons **Synchroniser**. Les boutons de synchronisation ne sont disponibles que si l'utilisateur connecté a accès au compte de stockage sélectionné.
            -   **Synchronize Primary Key** : récupère la clé primaire la plus récente de votre compte Azure Storage.
            -   **Synchronize Secondary Key** : récupère la clé secondaire la plus récente de votre compte Azure Storage.
                **Remarque :** pour plus d'informations sur les clés d'accès Azure Storage, consultez la page [Affichage, copie et régénération de clés d'accès de stockage](http://www.windowsazure.com/en-us/documentation/articles/storage-manage-storage-account/#regeneratestoragekeys).
    -   **Site diagnostics** : définissez des options pour la collecte d'informations de diagnostic pour votre site Web, dont :
        -   **Web Server Logging** : indiquez si la journalisation de serveur Web doit être activée pour le site Web. Les journaux de serveur Web sont enregistrés au format de fichier journal étendu W3C. Vous pouvez les enregistrer dans Azure Storage ou dans le système de fichiers.
            **Conseil** : la taille de stockage maximale des journaux dans le système de fichiers est de 100 Mo. Si vous devez conserver davantage de données historiques, optez pour Azure Storage.
            -   Pour enregistrer des journaux de serveur dans un compte Azure Storage, sélectionnez **Stockage**, puis **Gérer le stockage**. Dans la boîte de dialogue **Manage Storage for Site Diagnostics**, utilisez l'option **Compte de stockage** pour sélectionner le compte Azure Storage relatif au conteneur où seront hébergés les journaux. Utilisez l'option **Azure Blob Container** pour sélectionner le conteneur où seront hébergés les journaux, ou sélectionnez **Create a new blob container** pour activer le champ **Nom d'objet blob** dans lequel vous pouvez indiquer le nom du nouveau conteneur.
                **Remarque** : si vous ne disposez pas encore de compte de stockage, accédez à la section **Stockage** du portail Azure. Vous pourrez alors cliquer sur **Nouveau** pour créer un compte.
            -   Si vous sélectionnez **Système de fichiers**, les journaux sont enregistrés sur le site FTP indiqué sous **FTP Diagnostic Logs** sur la page de gestion du Tableau de bord. L'activation du stockage de système de fichiers active également la zone **Quota** dans laquelle vous pouvez définir l'espace disque maximal réservé aux fichiers journaux. L'espace est compris entre 20 Mo et 100 Mo. La valeur par défaut est de 35 Mo. Lorsque le quota est atteint, les fichiers les plus anciens sont remplacés par les plus récents.
            -   Par défaut, les journaux de serveur Web d'un compte Azure Storage ne sont jamais supprimés. Pour définir un délai après lequel les journaux sont automatiquement supprimés, sélectionnez **Set Retention** et entrez le nombre de jours de conservation des journaux dans la zone **Retention Period**. Vous pouvez également utiliser l'option **Set Retention** pour le stockage du système de fichiers.
        -   **Detailed Error Messages** : indiquez si vous souhaitez consigner des messages d'erreur détaillés pour le site Web. Si cette option est activée, des messages d'erreur détaillés sont enregistrés au format .htm sur le site FTP indiqué sous FTP Diagnostic Logs sur la page de gestion du Tableau de bord. Après vous être connecté au site FTP indiqué, accédez à /LogFiles/DetailedErrors/ pour récupérer les fichiers .htm contenant des messages d'erreur détaillés.
        -   **Suivi des demandes ayant échoué** : indiquez si le suivi des demandes ayant échoué doit être activé. Si cette option est activée, le résultat de suivi des demandes ayant échoué est écrit dans des fichiers XML et enregistré sur le site FTP indiqué sous FTP Diagnostic Logs sur la page de gestion du Tableau de bord. Après vous être connecté au site FTP indiqué, accédez à /LogFiles/W3SVC########\# (où ########\# représente un identificateur unique du site Web) afin de récupérer les fichiers XML contenant le résultat du suivi des demandes ayant échoué.
            **Important**
            Le dossier LogFiles/W3SVC########\#/ contient un fichier XSL et un ou plusieurs fichiers XML. Assurez-vous de télécharger le fichier XSL dans le même répertoire que le(s) fichier(s) XML, car le fichier XSL possède des attributs permettant de formater et de filtrer le contenu de fichiers XML lorsqu'ils sont affichés dans Internet Explorer.
        -   **Débogage distant** : définissez cette option sur **On** pour activer le débogage distant pour Visual Studio 2012 ou Visual Studio 2013. Lorsque cette option est activée, vous pouvez utiliser le débogueur distant de Visual Studio pour vous connecter directement à votre site Web Azure.
             **Remarque** : le débogage distant n'est activé que pour une période de 48 heures. Il ne fonctionne pas avec un nom de site ou d'utilisateur comportant plus de 20 caractères.
    -   **Contrôle** : pour les sites Web en mode Standard, testez la disponibilité des points de terminaison HTTP ou HTTPS. Vous pouvez tester un point de terminaison à partir de trois emplacements géo-distribués au maximum. Un test de surveillance échoue si le code de réponse HTTP est supérieur ou égal à 400 ou si la réponse prend plus de 30 secondes. Un point de terminaison est considéré comme disponible s'il passe avec succès les différents tests de surveillance à partir de tous les emplacements spécifiés.
    -   **Developer Analytics** : sélectionnez **Module** pour choisir un module d'analyse dans une liste ou accédez à l'Azure Store pour en choisir un. Sélectionnez **Personnalisé** pour choisir un fournisseur d'analyses dans une liste ; New Relic, par exemple. Si vous utilisez un fournisseur personnalisé, vous devez entrer la clé de licence dans la zone **Clé de fournisseur**.
         **Remarque** : pour plus d'informations sur l'utilisation de New Relic avec des sites Web Azure, consultez la page [Gestion des performances de l'application New Relic sur des sites Web Azure](http://www.windowsazure.com/en-us/documentation/articles/store-new-relic-web-sites-dotnet-application-performance-management/).
    -   **Paramètres d'application** : indiquez les paires nom/valeur qui sont chargées par votre application Web au démarrage. Dans le cas des sites .NET, ces paramètres sont inclus dans votre configuration .NET AppSettings au moment de l'exécution, en remplacement des paramètres existants. Pour les sites PHP et Node, ces paramètres sont disponibles en tant que variables d'environnement au moment de l'exécution.
    -   **Chaînes de connexion** : affiche les chaînes de connexion relatives aux ressources liées. Pour les sites .NET, ces chaînes de connexion sont incluses dans les paramètres connectionStrings de votre configuration .NET au moment de l'exécution, en remplacement des entrées existantes où la clé est identique à celle du nom de la base de données liée. Pour les sites PHP et Node, ces paramètres sont disponibles en tant que variables d'environnement au moment de l'exécution, avec le type de connexion comme préfixe. Les préfixes de la variable d'environnement sont les suivants :
        -   SQL Server : SQLCONNSTR\_
        -   MySQL : MYSQLCONNSTR\_
        -   Base de données SQL : SQLAZURECONNSTR\_
        -   Personnalisé : CUSTOMCONNSTR\_

        Par exemple, si une chaîne de connexion MySql se nomme connectionstring1, elle est accessible par le biais de la variable d'environnement `MYSQLCONNSTR_connectionString1`.
        **Remarque** : des chaînes de connexion sont créées lorsque vous liez une ressource de base de données à un site Web. Elles sont en lecture seule lorsque vous les visualisez sur la page de gestion de la configuration.
    -   **Default Documents** : le cas échéant, ajoutez le document par défaut de votre site Web à cette liste. Le document par défaut d'un site Web est la page Web affichée lorsqu'un utilisateur accède à un site Web et n'indique pas de page spécifique. Par exemple, avec le site Web http://contoso.com, si le document par défaut est défini sur default.htm, l'utilisateur est redirigé vers http://www.contoso.com/default.htm s'il entre http://www.contoso.com dans la barre d'adresse de son navigateur. Si votre site Web contient plusieurs fichiers de la liste, assurez-vous que le document par défaut de votre site Web apparaît en haut de la liste en changeant l'ordre des fichiers dans la liste.
    -   **Mappages de gestionnaires** : ajoutez des processeurs de script personnalisés afin de gérer les demandes portant sur des extensions de fichier spécifiques. Indiquez l'extension de fichier à traiter dans la zone **Extension** (\*.php ou handler.fcgi, par exemple). Les requêtes vers des fichiers qui répondent à ce modèle sont traitées par le processeur de script indiqué dans le champ **Script Processor Path**. Un chemin absolu est requis pour le processus de script (le chemin D:\\home\\site\\wwwroot peut être utilisé pour faire référence au répertoire racine de votre site). Des arguments de ligne de commande supplémentaires peuvent être spécifiés pour le processeur de script dans la zone **Arguments supplémentaires (facultatif)**.
    -   **virtual applications and directories**: pour configurer des applications et des répertoires virtuels associés à votre site Web, indiquez chaque répertoire virtuel et le chemin physique correspondant par rapport à la racine du site. Vous pouvez éventuellement activer la case à cocher **Application** pour marquer un répertoire virtuel comme application dans la configuration du site.

4 .  Cliquez sur **Enregistrer** au bas de la page de gestion **Configurer** pour enregistrer les modifications apportées à la configuration.

## Configuration d'un site Web afin d'utiliser une base de données SQL

Pour lier un site Web à une base de données SQL, procédez comme suit :

1.  Dans le [portail de gestion](http://manage.windowsazure.com), sélectionnez **Sites Web** pour afficher la liste des sites Web créés par le compte connecté.

2.  Sélectionnez un site Web dans la liste pour ouvrir les pages **Gestion** correspondantes.

3.  Cliquez sur l'onglet **Linked Resources**. Le message suivant s'affiche alors sur la page **Linked Resources** : **You have no linked resources**.

4.  Cliquez sur **Link a Resource** pour ouvrir l'Assistant **Link a Resource**.

5.  Cliquez sur **Create a new resource** pour afficher la liste des types de ressources qu'il est possible de lier à votre site Web.

6.  Cliquez sur **Base de données SQL** pour afficher l'Assistant **Link Database**.

7.  Renseignez les champs obligatoires sur les pages 3 et 4 de l'Assistant **Link Database**, puis cliquez sur la coche **Terminer** sur la page 4.

Azure va alors créer une base de données SQL avec les paramètres spécifiés et la lier au site Web.

## Configuration d'un site Web afin d'utiliser une base de données MySQL
Pour configurer un site Web de manière à utiliser une base de données MySQL, suivez la procédure d'utilisation d'une base de données SQL, mais choisissez **Base de données MySQL** au lieu de **Base de données SQL** dans l'Assistant **Link a Resource**.

Une autre solution consiste à créer le site Web avec l'option **Custom Create**. Dans la liste déroulante **Base de données**, sélectionnez **Create a new MySQL database** ou **Use an existing MySQL database**.

Configuration d'un nom de domaine personnalisé
----------------------------------------------

Pour plus d'informations sur la configuration de votre site Web de manière à utiliser un nom de domaine personnalisé, consultez la page [Configuration d'un nom de domaine personnalisé pour un site Web Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).

Configuration d'un site Web afin d'utiliser SSL
-----------------------------------------------

Pour plus d'informations sur la configuration du protocole SSL pour un domaine personnalisé sur Azure, consultez l'article [Activation du protocole HTTPS pour un site Web Azure](http://www.windowsazure.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/).

Étapes suivantes
----------------

-   [Mise à l'échelle de sites Web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/)

-   [Contrôle de sites Web](http://www.windowsazure.com/en-us/documentation/articles/web-sites-monitor/)


