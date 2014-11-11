<properties linkid="develop-mobile-tutorials-store-scripts-in-source-control" urlDisplayName="Store server scripts in source control" pageTitle="Store server scripts in source control - Azure Mobile Services" metaKeywords="" description="Learn how to store your server script files and modules in a local Git repo on your computer." metaCanonical="" services="" documentationCenter="Mobile" title="Store server scripts in source control" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Stockage de scripts serveur dans un contrôle de code source

Cette rubrique explique comment configurer un contrôle de code source pour la première fois dans Azure Mobile Services pour stocker vos scripts serveur dans un référentiel Git. Les scripts et les autres fichiers de code JavaScript peuvent être promus à partir de votre référentiel local sur votre service mobile de production. Cette rubrique indique également comment définir du code partagé requis par plusieurs scripts et comment télécharger des modules Node.js.

Ce didacticiel vous accompagne tout au long des étapes suivantes :

1.  [Activation du contrôle de code source dans votre service mobile][Activation du contrôle de code source dans votre service mobile]
2.  [Installation de Git et création du référentiel local][Installation de Git et création du référentiel local]
3.  [Déploiement des fichiers de script mis à jour sur votre service mobile][Déploiement des fichiers de script mis à jour sur votre service mobile]
4.  [Utilisation du code partagé et des modules Node.js dans vos scripts serveur][Utilisation du code partagé et des modules Node.js dans vos scripts serveur]

Pour suivre ce didacticiel, vous devez avoir créé un service mobile au moyen du didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données].

## <a name="enable-source-control"></a><span class="short-header">Activation du contrôle de code source</span>Activation du contrôle de code source dans votre service mobile

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur **Mobile Services**, puis sur le service mobile.

    ![][0]

2.  Cliquez sur l'onglet **Tableau de bord**, puis sous **Aperçu rapide**, cliquez sur **Set up source control** et sur **Oui** pour confirmer.

    ![][1]

    > [WACOM.NOTE]
    > Le contrôle de code source est une fonctionnalité préliminaire. Nous vous recommandons de sauvegarder vos fichiers de script régulièrement, même s'ils sont stockés dans Mobile Services.

3.  Fournissez un **Nom d'utilisateur**, un **Nouveau mot de passe**, confirmez le mot de passe, puis cliquez sur le bouton de vérification.

    ![][2]

    Le référentiel Git est créé dans votre service mobile. Notez les informations d'identification que vous venez de fournir, car elles vont seront utiles pour accéder à ce référentiel.

4.  Cliquez sur l'onglet Configurer et examinez les nouveaux champs **Contrôle de code source**.

    ![][3]

    L'URL du référentiel Git s'affiche. Vous l'utiliserez pour cloner le référentiel sur votre ordinateur local.

À présent que vous avez activé le contrôle de code source dans votre service mobile, il est temps d'utiliser Git pour cloner le référentiel sur votre ordinateur local.

## <a name="clone-repo"></a><span class="short-header">Clonage du référentiel</span>Installation de Git et création du référentiel local

1.  Installez Git sur votre ordinateur local.

    La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installation de Git][Installation de Git] pour accéder aux distributions et consignes d'installation propres aux différents systèmes d'exploitation.

    > [WACOM.NOTE]
    > Sur certains systèmes d'exploitation, une version en ligne de commande et une version avec interface utilisateur graphique sont toutes deux disponibles. Les instructions fournies dans cet article utilisent la version en ligne de commande.

2.  Ouvrez une ligne de commande, telle que **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, la ligne de commande est accessible depuis l'application **Terminal**.

3.  À partir de la ligne de commande, accédez au répertoire dans lequel vous allez stocker vos scripts. Par exemple : `cd SourceControl`.

4.  Utilisez la commande suivante pour créer une copie locale de votre nouveau référentiel Git, en remplaçant `<your_git_URL>` par l'URL du référentiel Git pour votre service mobile :

        git clone <your_git_URL>

5.  Lorsque vous y êtes invité, tapez le nom d'utilisateur et le mot de passe que vous avez définis lorsque vous avez activé le contrôle de code source dans votre service mobile. À l'issue de l'authentification, une série de réponses similaires à ce qui suit s'affiche :

        remote: Counting objects: 8, done.
        remote: Compressing objects: 100% (4/4), done.
        remote: Total 8 (delta 1), reused 0 (delta 0)
        Unpacking objects: 100% (8/8), done.

6.  Accédez au répertoire à partir duquel vous avez exécuté la commande `git clone` et remarquez la structure de répertoires suivante :

    ![4][4]

    En l'occurrence, un nouveau répertoire est créé avec le nom du service mobile, qui correspond au référentiel local du service de données.

7.  Ouvrez le sous-dossier .\\service\\table et remarquez qu'il contient un fichier TodoItem.json, qui est une représentation JSON des autorisations d'opération sur la table TodoItem.

    Lorsque des scripts serveur ont été définis sur cette table, vous avez également un ou plusieurs fichiers nommés `TodoItem.<operation>.js` contenant les scripts pour l'opération de table donnée. Les scripts de Scheduler et de l'API personnalisée sont conservés dans des dossiers séparés avec ces noms respectifs. Pour plus d'informations, consultez la page [Contrôle du code source][Contrôle du code source].

À présent que vous avez créé votre référentiel local, vous pouvez apporter des modifications aux scripts serveur et transmettre ces modifications au service mobile.

## <a name="deploy-scripts"></a><span class="short-header">Déploiement de scripts</span>Déploiement des fichiers de script mis à jour sur votre service mobile

1.  Accédez au sous-dossier .\\service\\table et si le fichier todoitem.insert.js n'existe pas, créez-le maintenant.

2.  Ouvrez le nouveau fichier todoitem.insert.js dans un éditeur de texte, collez le code suivant et enregistrez vos modifications :

        function insert(item, user, request) {
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Ce code écrit simplement l'élément inséré dans le journal. Si ce fichier contient déjà du code, ajoutez simplement un extrait de code JavaScript valide dans ce fichier, tel qu'un appel à `console.log()`, puis enregistrez vos modifications.

3.  Dans l'invite de commandes Git, tapez la commande suivante pour commencer le suivi du nouveau fichier de script :

        $ git add .

4.  Tapez la commande suivante pour valider les modifications :

        $ git commit -m "updated the insert script"

5.  Tapez la commande suivante pour télécharger les modifications sur le référentiel distant :

        $ git push origin master

    Une série de commandes indiquant le déploiement de la validation sur le service mobile doit s'afficher.

6.  De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][5]

7.  Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

    ![][6]

    Remarquez que le script de l'opération d'insertion affiché est identique au code JavaScript que vous venez de télécharger dans le référentiel.

## <a name="use-npm"></a><span class="short-header">Code partagé et modules</span>Utilisation du code partagé et des modules Node.js dans vos scripts serveur

Mobile Services fournit un accès au jeu complet de modules principaux de Node.js, que vous pouvez utiliser dans votre code au moyen de la fonction **require**. Votre service mobile peut également utiliser les modules Node.js ne faisant pas partie du package Node.js, et vous pouvez même définir votre propre code partagé sous forme de modules Node.js. Pour plus d'informations sur la création de modules, consultez la page [Modules][Modules] dans la documentation de référence de l'API Node.js.

Vous allez ensuite ajouter le module Node.js [node-uuid][node-uuid] à votre service mobile en utilisant le contrôle de code source et le gestionnaire de package Node.js (NPM). Ce module est ensuite utilisé pour générer une nouvelle valeur GUID pour la propriété **uuid** sur les éléments insérés.

1.  Si ce n'est pas déjà fait, installez Node.js sur votre ordinateur local en suivant la procédure figurant sur le [site Web Node.js][site Web Node.js].

2.  Accédez au dossier `.\service` de votre référentiel Git local, puis, à partir de l'invite de commandes, exécutez la commande suivante :

        npm install node-uuid

    NPM crée le répertoire `node_modules` à l'emplacement actuel et installe le module [node-uuid][node-uuid] dans le sous-répertoire `\node-uuid`.

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Lorsque <code data-inline="1">node_modules</code> existe d&eacute;j&agrave; dans la hi&eacute;rarchie de r&eacute;pertoires, NPM cr&eacute;e le sous-r&eacute;pertoire <code data-inline="1">\node-uuid</code> plut&ocirc;t que de cr&eacute;er un nouveau <code data-inline="1">node_modules</code> dans le r&eacute;f&eacute;rentiel. Dans ce cas, supprimez simplement le r&eacute;pertoire <code data-inline="1">node_modules</code> existant.</p>
</div>

3.  Maintenant, accédez au sous-dossier .\\service\\table, ouvrez le fichier todoitem.insert.js et modifiez-le comme suit :

        function insert(item, user, request) {
            var uuid = require('node-uuid');
            item.uuid = uuid.v1();
            request.execute();
            console.log(JSON.stringify(item, null, 4));
        }

    Ce code ajoute une colonne uuid à la table et la remplit avec des identificateurs GUID uniques.

4.  Comme dans la section précédente, tapez la commande suivante dans l'invite de commandes Git :

        $ git add .
        $ git commit -m "added node-uuid module"
        $ git push origin master

    Ce code ajoute le nouveau fichier, valide vos modifications, transfère le nouveau module node-uuid et les modifications vers le script todoitem.insert.js sur votre service mobile.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, vous savez comment stocker vos scripts dans un contrôle de code source. Pour plus d'informations sur l'utilisation de scripts serveur et d'API personnalisées :

-   [Utilisation des scripts serveur dans Mobile Services][Utilisation des scripts serveur dans Mobile Services]

    Explique comment utiliser les scripts serveur, le planificateur de travaux et les API personnalisées.

-   [Définition d'une API personnalisée qui prend en charge les notifications de réception][Définition d'une API personnalisée qui prend en charge les notifications de réception]

     Explique comment utiliser les API personnalisées pour prendre en charge des notifications périodiques mettant à jour les vignettes dynamiques dans une application Windows Store.

  [Activation du contrôle de code source dans votre service mobile]: #enable-source-control
  [Installation de Git et création du référentiel local]: #clone-repo
  [Déploiement des fichiers de script mis à jour sur votre service mobile]: #deploy-scripts
  [Utilisation du code partagé et des modules Node.js dans vos scripts serveur]: #use-npm
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-store-scripts-source-control/mobile-services-selection.png
  [1]: ./media/mobile-services-store-scripts-source-control/mobile-setup-source-control.png
  [2]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-credentials.png
  [3]: ./media/mobile-services-store-scripts-source-control/mobile-source-control-configure.png
  [Installation de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
  [Contrôle du code source]: http://msdn.microsoft.com/fr-fr/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
  [5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
  [6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png
  [Modules]: http://nodejs.org/api/modules.html
  [node-uuid]: https://npmjs.org/package/node-uuid
  [site Web Node.js]: http://nodejs.org/
  [Utilisation des scripts serveur dans Mobile Services]: /fr-fr/develop/mobile/how-to-guides/work-with-server-scripts
  [Définition d'une API personnalisée qui prend en charge les notifications de réception]: /fr-fr/develop/mobile/tutorials/create-pull-notifications-dotnet
