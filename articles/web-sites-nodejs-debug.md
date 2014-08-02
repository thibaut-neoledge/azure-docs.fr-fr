<properties linkid="dev-nodejs-how-to-debug-website" urlDisplayName="Debug Websites (Node)" pageTitle="How to Debug Azure Websites in Node.js" metaKeywords="debug website azure, debugging azure, troubleshooting azure web site, troubleshoot azure website node" description="Learn how to debug an Azure website in Node.js." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="How to debug a Node.js application in Azure Web Sites" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Débogage d'une application Node.js dans Sites Web Azure
=======================================================

Azure fournit un outil de diagnostic intégré qui vous aide à déboguer les applications Node.js hébergées dans Sites Web Azure. Cet article vous explique comment activer la journalisation de stdout et de stderr, comment afficher les informations sur l'erreur dans le navigateur et comment télécharger et afficher les fichiers journaux.

Le diagnostic des applications Node.js hébergées sur Azure est fourni par [IISNode](https://github.com/tjanczuk/iisnode). Bien que les paramètres les plus courants pour la collecte des informations de diagnostic soient abordés, cet article ne fournit pas une référence complète sur l'utilisation de IISNode. Pour plus d'informations sur l'utilisation de IISNode, consultez le [fichier Lisez-moi IISNode](https://github.com/tjanczuk/iisnode#readme) sur GitHub.

Activation de la journalisation
-------------------------------

Par défaut, Sites Web Azure capture uniquement les informations de diagnostic concernant les déploiements, comme lorsque vous déployez un site Web à l'aide de Git. Cette information est utile si vous rencontrez un problème lors du déploiement, comme un échec lors de l'installation d'un module référencé dans **package.json** ou si vous utilisez un script de déploiement personnalisé.

Pour activer la journalisation des flux stdout et stderr, vous devez créer un fichier **IISNode.yml** à la racine de votre application Node.js et ajouter ce qui suit :

    loggingEnabled: true

Cela active la journalisation de stderr et stdout à partir de votre application Node.js.

Le fichier **IISNode.yml** peut également être utilisé pour contrôler si les erreurs conviviales ou les erreurs de développement sont renvoyées au navigateur lorsqu'un échec se produit. Pour activer les erreurs de développement, ajoutez la ligne suivante au fichier **IISNode.yml** :

    devErrorsEnabled: true

Une fois cette option activée, IISNode renvoie les derniers 64 Ko d'informations envoyées à stderr plutôt qu'une erreur conviviale comme « une erreur de serveur interne s'est produite ».

**Remarque**

Bien que devErrorsEnabled soit utile pour diagnostiquer les problèmes lors du développement, l'activer dans un environnement de production peut entraîner l'envoi d'erreurs de développement aux utilisateurs finaux.

Si le fichier **IISNode.yml** n'existe pas dans votre application, vous devez redémarrer votre site Web après la publication de l'application mise à jour. Si vous modifiez simplement les paramètres dans un fichier **IISNode.yml** existant qui a été publié précédemment, aucun redémarrage n'est nécessaire.

**Remarque**

Si votre site Web a été créé à l'aide des outils en ligne de commande Azure ou des cmdlets Azure PowerShell, un fichier **IISNode.yml** par défaut est automatiquement créé.

Vous pouvez redémarrer le site Web en le sélectionnant dans le [portail de gestion Azure](https://manage.windowsazure.com/), puis en sélectionnant le bouton **REDÉMARRER** :

![bouton redémarrer](./media/web-sites-nodejs-debug/restartbutton.png)

Si les outils en ligne de commande Azure sont installés dans votre environnement de développement, vous pouvez utiliser la commande suivante pour redémarrer le site Web :

    azure site restart [sitename]

**Remarque**

Bien que loggingEnabled et devErrorsEnabled soient les options de configuration IISNode.yml les plus fréquemment utilisées pour la capture des informations de diagnostic, IISNode.yml peut être utilisé pour configurer diverses options pour votre environnement d'hébergement. Pour une liste complète des options de configuration, consultez le fichier [iisnode\_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml).

Accès aux journaux
------------------

Il existe trois manières d'accéder aux journaux de diagnostic : à l'aide du protocole FTP, en téléchargeant une archive ZIP ou via un flux live mis à jour du journal (également appelé « tail »). Le téléchargement de l'archive ZIP des fichiers journaux ou l'affichage du flux live requièrent les outils en ligne de commande Azure, que vous pouvez installer à l'aide de la commande suivante :

    npm install azure-cli -g

Une fois installés, ils sont accessibles à l'aide de la commande « azure ». Les outils en ligne de commande doivent d'abord être configurés pour utiliser votre abonnement Azure. Pour plus d'informations sur la réalisation de cette tâche, consultez la section **Téléchargement et importation des paramètres de publication** de l'article [Utilisation des outils en ligne de commande Azure](https://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/).

### FTP

Pour accéder aux informations de diagnostic via FTP, visitez le [portail Azure], sélectionnez votre site Web, puis sélectionnez le **TABLEAU DE BORD**. Dans la section **liens rapides**, les liens **FTP DIAGNOSTIC LOGS** et **FTPS DIAGNOSTIC LOGS** permettent d'accéder aux journaux à l'aide du protocole FTP.

**Remarque**

Si vous n'avez pas déjà configuré un nom d'utilisateur et un mot de passe pour le FTP ou le déploiement, vous pouvez le faire à partir de la page de gestion **Démarrage rapide** en sélectionnant **Set up deployment credentials**.

L'URL FTP renvoyée dans le tableau de bord concerne le répertoire **LogFiles**, qui contient les sous-répertoires suivants :

-   [Méthode de déploiement] : si vous utilisez une méthode de déploiement comme Git, un répertoire portant le même nom est créé et contient les informations relatives aux déploiements.

-   nodejs : les informations stdout et stderr capturées à partir de toutes les instances de votre application (lorsque loggingEnabled est défini sur true).

### Archive ZIP

Pour télécharger une archive ZIP des journaux de diagnostic, utilisez la commande suivante à partir des outils en ligne de commande Azure :

    azure site log download [sitename]

Cela télécharge un fichier **diagnostics.zip** dans le répertoire actuel. Cette archive contient la structure de répertoires suivante :

-   deployments : un journal des informations concernant les déploiements de votre application

-   LogFiles

    -   [Méthode de déploiement] : si vous utilisez une méthode de déploiement comme Git, un répertoire portant le même nom est créé et contient les informations relatives aux déploiements.

    -   nodejs : les informations stdout et stderr capturées à partir de toutes les instances de votre application (lorsque loggingEnabled est défini sur true).

### Flux live (tail)

Pour afficher un flux live des informations du journal de diagnostic, utilisez la commande suivante à partir des outils en ligne de commande Azure :

    azure site log tail [sitename]

Cela renvoie un flux d'événements de journal qui sont mis à jour lorsqu'ils se produisent sur le serveur. Ce flux renvoie les informations de déploiement ainsi que les informations relatives à stdout et stderr (lorsque loggingEnabled est défini sur true).

Étapes suivantes
----------------

Cet article vous a montré comment activer les informations de diagnostic pour Azure et comment y accéder. Bien que ces informations soient utiles pour comprendre les problèmes qui surviennent avec votre application, elles peuvent mettre en évidence un problème avec un module que vous utilisez ou avec la version de Node.js utilisée par Sites Web Azure qui est différente de celle utilisée dans votre environnement de déploiement.

Pour plus d'informations sur l'utilisation des modules sur Azure, consultez la page [Utilisation des modules Node.js avec les applications Azure](https://www.windowsazure.com/en-us/develop/nodejs/common-tasks/working-with-node-modules/).

Pour plus d'informations sur la spécification d'une version Node.js de votre application, consultez la page [Spécification d'une version de Node.js dans une application Azure](https://www.windowsazure.com/en-us/develop/nodejs/common-tasks/specifying-a-node-version/).

