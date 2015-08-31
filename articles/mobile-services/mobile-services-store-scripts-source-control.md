<properties
	pageTitle="Stockage du code de projet dans le contrôle de code source | Microsoft Azure"
	description="Découvrez comment stocker vos fichiers et modules de script serveur dans un référentiel Git local sur votre ordinateur."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/18/2015"
	ms.author="ggailey777"/>

# Stockage du code de projet dans le contrôle de code source

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-store-code-source-control.md)
- [(Any | Javascript)](mobile-services-store-scripts-source-control.md)

Cette rubrique montre comment utiliser le contrôle du code source fourni par Azure Mobile Services pour stocker vos scripts serveur. Les scripts et les autres fichiers de code du backend JavaScript peuvent être promus à partir de votre référentiel Git local sur votre service mobile de production. Cette rubrique indique également comment définir du code partagé requis par plusieurs scripts et comment utiliser le fichier package.json pour ajouter des modules Node.js à votre service mobile.

Pour suivre ce didacticiel, vous devez avoir créé un service mobile en suivant l'un des deux didacticiels [Prise en main de Mobile Services] ou [Ajout de Mobile Services à une application existante].

##<a name="enable-source-control"></a>Activer le contrôle de code source dans votre service mobile

[AZURE.INCLUDE [mobile-services-enable-source-control](../../includes/mobile-services-enable-source-control.md)]

##<a name="clone-repo"></a>Installer Git et créer le référentiel local

1. Installez Git sur votre ordinateur local.

	La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installation de Git] pour accéder aux distributions et consignes d'installation propres aux différents systèmes d'exploitation.

	> [AZURE.NOTE]Sur certains systèmes d'exploitation, une version en ligne de commande et une version avec interface utilisateur graphique sont toutes deux disponibles. Les instructions fournies dans cet article utilisent la version en ligne de commande.

2. Ouvrez une ligne de commande, telle que **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, la ligne de commande est accessible depuis l'application **Terminal**.

3. À partir de la ligne de commande, accédez au répertoire dans lequel vous allez stocker vos scripts. Par exemple, `cd SourceControl`.

4. Utilisez la commande suivante pour créer une copie locale de votre nouveau référentiel Git, en remplaçant `<your_git_URL>` par l’URL du référentiel Git pour votre service mobile :

		git clone <your_git_URL>

5. Lorsque vous y êtes invité, tapez le nom d'utilisateur et le mot de passe que vous avez définis lorsque vous avez activé le contrôle de code source dans votre service mobile. À l'issue de l'authentification, une série de réponses similaires à ce qui suit s'affiche :

		remote: Counting objects: 8, done.
		remote: Compressing objects: 100% (4/4), done.
		remote: Total 8 (delta 1), reused 0 (delta 0)
		Unpacking objects: 100% (8/8), done.

6. Accédez au répertoire à partir duquel vous avez exécuté la commande `git clone` et remarquez la structure de répertoire suivante :

	![4][4]

	En l'occurrence, un nouveau répertoire est créé avec le nom du service mobile, qui correspond au référentiel local du service de données.

7. Ouvrez le sous-dossier .\\service\\table et remarquez qu'il contient un fichier TodoItem.json, qui est une représentation JSON des autorisations d'opération sur la table TodoItem.

	Lorsque des scripts serveur ont été définis sur cette table, vous avez également un ou plusieurs fichiers nommés <code>TodoItem._&lt;operation&gt;_.js</code> contenant les scripts pour l'opération de table donnée. Les scripts de Scheduler et de l'API personnalisée sont conservés dans des dossiers séparés avec ces noms respectifs. Pour plus d'informations, consultez la page [Contrôle du code source].

À présent que vous avez créé votre référentiel local, vous pouvez apporter des modifications aux scripts serveur et transmettre ces modifications au service mobile.

##<a name="deploy-scripts"></a>Déployer les fichiers de script mis à jour sur votre service mobile

1. Accédez au sous-dossier .\\service\\table et si le fichier todoitem.insert.js n'existe pas, créez-le maintenant.

2. Ouvrez le nouveau fichier todoitem.insert.js dans un éditeur de texte, collez le code suivant et enregistrez vos modifications :

		function insert(item, user, request) {
		    request.execute();
		    console.log(JSON.stringify(item, null, 4));
		}

	Ce code écrit simplement l'élément inséré dans le journal. Si ce fichier contient déjà du code, ajoutez-lui simplement un extrait de code JavaScript valide, tel qu'un appel à `console.log()`, puis enregistrez vos modifications.

3. Dans l'invite de commandes Git, tapez la commande suivante pour commencer le suivi du nouveau fichier de script :

		$ git add .


4. Tapez la commande suivante pour valider les modifications :

		$ git commit -m "updated the insert script"

5. Tapez la commande suivante pour télécharger les modifications sur le référentiel distant :

		$ git push origin master

	Une série de commandes indiquant le déploiement de la validation sur le service mobile doit s'afficher.

6. De retour dans le portail de gestion, cliquez sur l’onglet **Données**, puis sur la table **TodoItem**, cliquez sur **Script**, puis sélectionnez l’opération **Insérer**.
7. 
	Remarquez que le script de l'opération d'insertion affiché est identique au code JavaScript que vous venez de télécharger dans le référentiel.

##<a name="use-npm"></a>Exploiter le code partagé et les modules Node.js dans vos scripts serveur

Mobile Services fournit un accès au jeu complet de modules principaux de Node.js, que vous pouvez utiliser dans votre code au moyen de la fonction **require**. Votre service mobile peut également utiliser les modules Node.js ne faisant pas partie du package Node.js, et vous pouvez même définir votre propre code partagé sous forme de modules Node.js. Pour plus d'informations sur la création de modules, consultez la page [Modules] dans la documentation de référence de l'API Node.js.

Pour ajouter des modules Node.js à votre service mobile, il est recommandé d'ajouter des références au fichier package.json du service. Ajoutez ensuite le module Node.js [node-uuid] à votre service mobile en mettant à jour le fichier package.json. Une fois la mise à jour transmise à Azure, le service mobile est redémarré et le module est installé. Ce module est ensuite utilisé pour générer une nouvelle valeur GUID pour la propriété **uuid** sur les éléments insérés.

2. Accédez au dossier `.\service` du référentiel Git local, puis ouvrez le fichier package.json dans un éditeur de texte et ajoutez le champ suivant à l'objet **dependencies** :

		"node-uuid": "~1.4.3"

	>[AZURE.NOTE]Cette mise à jour du fichier package.json provoque un redémarrage dans votre service mobile après que la validation est envoyée.

4. Maintenant, accédez au sous-dossier .\\service\\table, ouvrez le fichier todoitem.insert.js et modifiez-le comme suit :

		function insert(item, user, request) {
		    var uuid = require('node-uuid');
		    item.uuid = uuid.v1();
		    request.execute();
		    console.log(item);
		}

	Ce code ajoute une colonne uuid à la table et la remplit avec des identificateurs GUID uniques.

5. Comme dans la section précédente, tapez la commande suivante dans l'invite de commandes Git :

		$ git add .
		$ git commit -m "added node-uuid module"
		$ git push origin master

	Ce code ajoute le nouveau fichier, valide vos modifications, transfère le nouveau module node-uuid et les modifications vers le script todoitem.insert.js sur votre service mobile.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, vous savez comment stocker vos scripts dans un contrôle de code source. Pour plus d'informations sur l'utilisation de scripts serveur et d'API personnalisées :

+ [Utilisation des scripts serveur dans Mobile Services] <br/>
Explique comment utiliser les scripts serveur, le planificateur de travaux et les API personnalisées.

+ [Appel d'une API personnalisée à partir du client] <br/>
 Explique comment créer des API personnalisées pouvant être appelées à partir du client.

<!-- Anchors. -->
[Enable source control in your mobile service]: #enable-source-control
[Install Git and create the local repository]: #clone-repo
[Deploy updated script files to your mobile service]: #deploy-scripts
[Leverage shared code and Node.js modules in your server scripts]: #use-npm

<!-- Images. -->
[4]: ./media/mobile-services-store-scripts-source-control/mobile-source-local-repo.png
[5]: ./media/mobile-services-store-scripts-source-control/mobile-portal-data-tables.png
[6]: ./media/mobile-services-store-scripts-source-control/mobile-insert-script-source-control.png

<!-- URLs. -->
[Git website]: http://git-scm.com
[Contrôle du code source]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[Installation de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Prise en main de Mobile Services]: mobile-services-ios-get-started.md
[Ajout de Mobile Services à une application existante]: mobile-services-ios-get-started-data.md
[Utilisation des scripts serveur dans Mobile Services]: mobile-services-how-to-use-server-scripts.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Appel d'une API personnalisée à partir du client]: mobile-services-ios-call-custom-api.md
[Modules]: http://nodejs.org/api/modules.html
[node-uuid]: https://npmjs.org/package/node-uuid

<!---HONumber=August15_HO8-->