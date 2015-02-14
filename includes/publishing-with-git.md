# Publication de sites web sur Azure avec Git

Le service Sites Web Azure prend en charge le déploiement continu à partir d'outils de contrôle du code source et de référentiel comme BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial et TFS. Ces outils permettent de mettre à jour le contenu et le code de votre site web, puis d'envoyer rapidement et facilement à votre convenance les modifications de votre site.

Dans cet article, vous allez apprendre à utiliser Git pour publier directement à partir de votre ordinateur local sur un site web Azure (dans Azure, cette méthode de publication est appelée **Git local**). Vous allez également découvrir comment activer le déploiement continu à partir de sites web de référentiel, comme BitBucket, CodePlex, DropBox, GitHub ou Mercurial. Pour plus d'informations sur l'utilisation de TFS dans le cadre d'un déploiement continu, consultez la page [Livraison continue sur Azure au moyen de Visual Studio Online].

> [AZURE.NOTE] Plusieurs des commandes Git décrites dans cet article sont exécutées automatiquement lors de la création d'un site web à l'aide des <a href="/fr-fr/develop/nodejs/how-to-guides/command-line-tools/">outils en ligne de commande Azure pour Mac et Linux</a>.

La tâche inclut les étapes suivantes :

* [Installation de Git](#Step1)
* [Création d'un référentiel local](#Step2)
* [Ajout d'une page web](#Step3)
* [Activation du référentiel de sites web](#Step4)
* [Déploiement du projet](#Step5)
	* [Envoi des fichiers locaux vers Azure (Git local)](#Step6)
	* [Déploiement de fichiers à partir d'un site Web de référentiel tel que BitBucket, CodePlex, Dropbox, GitHub ou Mercurial](#Step7)
	* [Déploiement d'une solution Visual Studio depuis BitBucket, CodePlex, Dropbox, GitHub ou Mercurial](#Step75)
* [Résolution des problèmes](#Step8)

<h2><a id="Step2"></a>Installation de Git</h2>

La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installation de Git] pour accéder aux distributions et consignes d'installation propres aux différents systèmes d'exploitation.

> [AZURE.NOTE] Sur certains systèmes d'exploitation, une version en ligne de commande et une version avec interface utilisateur graphique sont toutes deux disponibles. Les instructions fournies dans cet article utilisent la version en ligne de commande.

<h2><a id="Step2"></a>Création d'un référentiel local</h2>

Effectuez les tâches suivantes pour créer un nouveau référentiel Git.

1. Créez un répertoire nommé MyGitRepository qui contiendra votre référentiel Git et vos fichiers de site web.

2. Ouvrez une ligne de commande, telle que **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, vous pouvez accéder à la ligne de commande via l'application **Terminal**.

3. À partir de la ligne de commande, indiquez le répertoire MyGitRepository.

		cd MyGitRepository

4. Utilisez la commande suivante pour initialiser un nouveau référentiel Git :

		git init

	Cette commande doit renvoyer un message similaire au suivant : " **Initialized empty Git repository in [chemin d'accès]** ".

<h2><a id="Step3"></a>Ajout d'une page web</h2>

Le service Sites Web Azure prend en charge les applications créées dans différents langages de programmation. Pour les besoins de cet exemple, vous allez utiliser un fichier .html statique. Pour plus d'informations sur la publication de sites web dans d'autres langages de programmation sur Azure, consultez le [Centre de développement Azure].

1. Au moyen d'un éditeur de texte, créez un fichier nommé **index.html** à la racine du référentiel Git (répertoire MyGitRepository créé plus tôt).

2. Ajoutez le texte suivant dans le fichier index.html et enregistrez ce dernier.

		Hello Git!

3. Dans la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git. Utilisez ensuite la commande suivante pour ajouter le fichier **index.html** au référentiel :

		git add index.html 

	> [AZURE.NOTE] Pour obtenir de l'aide concernant les commandes git, tapez -help ou --help à la suite de la commande. Par exemple, pour les options de paramètre de la commande add, tapez 'git add -help' pour obtenir de l'aide sur la ligne de commande ou 'git add --help' pour accéder à une aide plus détaillée.

4. Ensuite, validez les modifications apportées au référentiel au moyen de la commande suivante :

		git commit -m "Adding index.html to the repository"

	Le résultat ressemble à ce qui suit :

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Activation du référentiel de sites web</h2>

Effectuez la procédure suivante pour activer un référentiel Git pour votre site web au moyen du portail Azure :

1. Connectez-vous au [portail Azure].

2. Cliquez sur le bouton NOUVEAU pour créer un site web pour lequel vous activerez un référentiel.

2. Attendez que le processus de création du site web soit terminé dans la vue **sites web**, puis sélectionnez le site web.

	![An image displaying a selected web site][portal-select-website]

3. Sélectionnez l'onglet **TABLEAU DE BORD**.

4. Dans la section **Aperçu rapide**, sélectionnez **Configurer le déploiement à partir du contrôle de code source**.  La boîte de dialogue **CONFIGURER LE DÉPLOIEMENT** suivante s'affiche.

	![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

4. Sélectionnez **Git local**, puis cliquez sur la flèche **Suivant**.

4. Si vous configurez un référentiel pour la première fois dans Azure, vous devez créer des informations d'identification de connexion pour ce référentiel. Vous les utiliserez pour vous connecter au référentiel Azure et pour envoyer les modifications depuis votre référentiel Git local. 

	![](./media/publishing-with-git/git_credentials.png)
	
5. Quelques secondes plus tard, un message vous indique que votre référentiel est prêt. 

	![git-instructions][git-instructions]

<h2><a id="Step5"></a>Déploiement du projet</h2>

<h3><a id="Step6"></a>Envoi des fichiers locaux vers Azure (Git local)</h3>

À ce stade, le portail affiche des instructions sur l'initialisation d'un référentiel local et l'ajout de fichiers. Vous avez déjà effectué ces opérations lors des étapes citées plus haut dans cette même rubrique. Cependant, si vous n'avez pas configuré vos informations d'identification de déploiement, vous devez revenir à l'onglet **TABLEAU DE BORD** dans le portail et cliquer sur **Réinitialisez vos informations d'identification de déploiement**.

Pour publier votre site web sur Azure à l'aide de Git local, procédez comme suit :

1. Dans la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git local contenant le fichier index.html créé précédemment.

2. Copiez la commande git remote add indiquée à l'étape 3 des instructions renvoyées par le portail. Elle ressemble à la commande suivante :

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE] La commande **remote** ajoute une référence nommée dans un référentiel distant. Dans cet exemple, elle crée une référence nommée 'azure' pour votre référentiel de sites web Azure.

1. Utilisez la commande suivante dans la ligne de commande pour transmettre le contenu du référentiel actuel du référentiel local vers le 'azure' distant :

		git push azure master

	Vous êtes invité à indiquer le mot de passe créé précédemment lorsque vous réinitialisez vos informations d'identification de déploiement dans le portail. Entrez le mot de passe (notez que Gitbash ne génère pas d'astérisques dans la console lorsque vous tapez votre mot de passe). Le résultat ressemble à ce qui suit :

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [AZURE.NOTE] Le référentiel créé pour votre site web Azure s'attend à ce que les demandes d'envoi ciblent la branche <strong>principale</strong> de son référentiel, qui sera dès lors utilisée comme contenu du site web.

2. Dans la partie inférieure du portail, cliquez sur le lien **PARCOURIR** pour vérifier que la page **index.html** a été déployée. Une page contenant " Hello Git! " s'affiche.

	![A webpage containing 'Hello Git!'][hello-git]

3. Au moyen d'un éditeur de texte, modifiez le fichier **index.html** de façon à ce qu'il contienne " Super ! ", puis enregistrez le fichier.

4. Utilisez les commandes suivantes à partir de la ligne de commande pour **ajouter** et **valider** les modifications, puis les **envoyer** au référentiel distant :

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Une fois que la commande **push** a été exécutée, actualisez le navigateur (vous devrez peut-être appuyer sur Ctrl+F5 pour qu'il soit actualisé correctement). Notez que le contenu de la page reflète à présent la dernière modification validée.

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>Déploiement de fichiers à partir d'un site web de référentiel tel que BitBucket, CodePlex, Dropbox, GitHub ou Mercurial</h3>

L'envoi de fichiers locaux vers Azure au moyen du Git local vous permet d'envoyer manuellement des mises à jour d'un projet local vers votre site web Azure, alors qu'un déploiement depuis BitBucket, CodePlex, Dropbox, GitHub ou Mercurial donne lieu à un processus de déploiement continu au cours duquel Azure va récupérer les mises à jour les plus récentes de votre projet.

Bien que les deux méthodes aboutissent à un déploiement de votre projet vers un site web Azure, le déploiement continu est utile quand plusieurs personnes travaillent sur un projet et qu'elles veulent s'assurer que la version la plus récente est toujours publiée, quel qu'en soit l'auteur. Le déploiement continu s'avère également utile si vous vous servez de l'un des outils mentionnés ci-dessus en tant que référentiel central pour votre application.

Le déploiement de fichiers à partir de GitHub, CodePlex ou BitBucket requiert que vous ayez publié votre projet local sur l'un de ces services. Pour plus d'informations sur la publication de votre projet à ces services, consultez les rubriques [Création d'un référentiel (GitHub)], [Utilisation de Git avec CodePlex], [Création d'un référentiel (BitBucket)], [Utilisation de Dropbox pour partager des référentiels Git] et [Démarrage rapide - Mercurial].

1. Commencez par placer les fichiers de votre site web dans le référentiel sélectionné qui sera utilisé pour un déploiement continu.

2. Dans le portail Azure de votre site web, accédez à l'onglet **TABLEAU DE BORD**. Dans la section **Aperçu rapide**, sélectionnez **Configurer le déploiement à partir du contrôle de code source**.  La boîte de dialogue **Configurer le déploiement** s'affiche avec le message suivant **Où est votre code source ?**. 

2. Choisissez la méthode de contrôle du code source que vous souhaitez utiliser pour le déploiement continu.
	
3. Lorsque vous y êtes invité, entrez les informations d'identification du service que vous avez sélectionné.

4. Une fois que vous avez autorisé Azure à accéder à votre compte, une liste de référentiels s'affiche. 

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. Sélectionnez le référentiel que vous voulez associer à votre site web Azure. Cliquez sur la coche pour continuer.

	> [AZURE.NOTE] Lors de l'activation du déploiement continu avec GitHub ou BitBucket, les projets publics et privés sont tous deux affichés.

6. Azure crée une association avec le référentiel sélectionné et récupère les fichiers à partir de la branche principale. Au terme de ce processus, l'**historique de déploiement** de la page **Déploiements** affiche un message **Déploiement actif** similaire au suivant :

	![git-githubdeployed][git-githubdeployed]

7. À ce stade, votre projet a été déployé à partir du référentiel de votre choix vers votre site web Azure. Pour vérifier que le site est actif, cliquez sur le lien **Parcourir** dans la partie inférieure du portail. Le navigateur doit accéder au site web.

8. Pour vérifier que le déploiement continu a bien lieu, effectuez une modification dans votre projet, puis envoyez la mise à jour vers le référentiel que vous avez associé à ce site web. Votre site web doit se mettre à jour de façon à refléter les modifications peu après l'envoi vers le référentiel. Vous pouvez vérifier que les mises à jour ont été récupérées sur la page **Déploiements** de votre site web.

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

<h3><a id="Step75"></a>Déploiement d'une solution Visual Studio depuis BitBucket, CodePlex, Dropbox, GitHub ou Mercurial</h3>

L'envoi d'une solution Visual Studio vers un site web Azure est aussi facile qu'envoyer un simple fichier index.html. Le processus de déploiement des sites web Azure simplifie tous les détails, y compris restaurer les dépendances NuGet et générer les fichiers binaires de l'application. Vous pouvez suivre les meilleures pratiques de contrôle du code source pour la maintenance du code seulement dans votre référentiel Git et laisser le déploiement de sites web Azure s'occuper du reste.

Les étapes pour pousser votre solution Visual Studio sur un site Web Azure est identique à celui de la [section précédente](#Step7), si vous configurez votre solution et le référentiel comme suit :

-	À la racine de votre référentiel, ajoutez un fichier `.gitignore`, puis spécifiez tous les fichiers et dossiers que vous souhaitez exclure de votre référentiel, tels que les dossiers `Obj`, `Bin` et `packages` (voir [gitignore documentation](http://git-scm.com/docs/gitignore) pour la mise en forme). Par exemple :

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE] Si vous utilisez GitHub, vous pouvez éventuellement générer un fichier .gitignore propre à Visual Studio quand vous créez votre référentiel, qui comprend tous les fichiers temporaires, les résultats de la génération, etc. Vous pouvez ensuite le personnaliser pour l'adapter à vos besoins spécifiques.

-	Ajoutez l'arborescence des répertoires de toute la solution à votre référentiel, avec le fichier .sln dans la racine du référentiel.

-	Dans votre solution Visual Studio, [activez la restauration de packages NuGet](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages) pour que Visual Studio restaure automatiquement les packages manquants.

Une fois que vous avez configuré votre référentiel comme indiqué ainsi que votre site web Azure pour la publication en continu depuis un des référentiels Git en ligne, vous pouvez développer votre application ASP.NET localement dans Visual Studio et déployer en continu votre code, en envoyant simplement les modifications apportées à votre référentiel Git en ligne.

<h4>Fonctionnement du déploiement continu</h4>
Le déploiement continu fonctionne comme suit : il fournit l'**URL DU DÉCLENCHEUR DU DÉPLOIEMENT** dans la section **Déploiements** de l'onglet **Configurer** de votre site.

![git-DeploymentTrigger][git-DeploymentTrigger]

Quand des mises à jour sont effectuées dans votre référentiel, une demande POST est envoyée à cette URL, notifiant votre site web Azure que le référentiel a été mis à jour. À ce moment, la mise à jour est récupérée et déployée vers votre site web.

Pour plus d'informations sur le moteur sous -jacent au processus de déploiement Git pour des sites web Azure, consultez la page [Projet Kudu](https://github.com/projectkudu/kudu/wiki).

<h4>Spécification de la branche à utiliser</h4>

Lorsque vous activez le déploiement continu, la branche **principale** du référentiel. Si vous voulez utiliser une autre branche, procédez comme suit :

1. Dans le portail, sélectionnez votre site web, puis  **CONFIGURER**.

2. Dans la section **Déploiements** de la page, entrez la branche que vous voulez utiliser dans le champ **BRANCHE À DÉPLOYER**, puis appuyez sur Entrée. Enfin, cliquez sur **ENREGISTRER**.

	Azure commence immédiatement à effectuer la mise à jour sur la base des modifications apportées à la nouvelle branche.

<h4>Désactivation du déploiement continu</h4>

Le déploiement continu peut être désactivé à partir du **Tableau de bord** Azure. Dans la section **Aperçu rapide**, choisissez l'option de déconnexion du référentiel que vous utilisez :

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

Après avoir répondu **Oui** au message de confirmation, vous pouvez revenir à la section **Aperçu rapide** et cliquer sur **Configurer le déploiement à partir du contrôle de code source** si vous voulez configurer la publication à partir d'une autre source.

<h2><a id="Step8"></a>Résolution des problèmes</h2>

Voici des erreurs ou des problèmes fréquemment rencontrés lors de l'utilisation de Git pour publier vers un site web Azure :

****

**Symptôme **: Impossible d'accéder à " [URL_du_site] " : Impossible de se connecter à [Adresse_scm]

**Cause** : Cette erreur se produit si le site web n'est pas actif et en cours d'exécution.

**Résolution** : Démarrez le site web dans le portail Azure. Le déploiement Git ne fonctionnera pas tant que le site web n'est pas en cours d'exécution. 


****

**Symptôme **: Impossible de résoudre l'hôte 'hostname'

**Cause** : Cette erreur peut se produire si les informations d'adresse entrées lors de la création du référentiel 'azure' distant étaient incorrectes.

**Résolution** : Utilisez la commande `git remote -v` pour répertorier tous les référentiels distants avec l'URL associée. Vérifiez que l'URL du référentiel 'azure' distant est correcte. Si nécessaire, supprimez et recréez ce référentiel distant au moyen de l'URL correcte.

****

**Symptôme **: No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.

**Cause** : cette erreur peut se produire si vous ne spécifiez pas de branche lors de l'exécution d'une opération git push et que vous n'avez pas défini la valeur push.default utilisée par Git.

**Résolution** : réexécutez l'opération Push, en spécifiant la branche principale. Par exemple :

	git push azure master

****

**Symptôme** : src refspec [branchname] ne correspond à aucun élément.

**Cause** : cette erreur peut se produire si vous tentez d'effectuer une opération Push sur une autre branche que la branche principale sur le référentiel 'azure' distant.

**Résolution** : réexécutez l'opération Push, en spécifiant la branche principale. Par exemple :

	git push azure master

****

**Symptôme **: Erreur : des modifications ont été validées dans le référentiel distant, mais votre site web n'a pas été mis à jour.

**Cause** : cette erreur peut se produire si vous déployez une application Node.js contenant un fichier package.json spécifiant des modules obligatoires supplémentaires.

**Résolution** : des messages supplémentaires contenant " npm ERR! " doivent être consignés avant cette erreur et peuvent fournir davantage de contexte sur la défaillance. Voici les causes connues de cette erreur et le message " npm ERR! " correspondant :

* **Fichier package.json incorrect **: npm ERR! Couldn't read dependencies.

* **Native module that does not have a binary distribution for Windows**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		OU

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## Ressources supplémentaires

* [Utilisation de PowerShell pour Azure]
* [Utilisation des outils en ligne de commande Azure pour Mac et Linux]
* [Documentation Git]
* [Projet Kudu](https://github.com/projectkudu/kudu/wiki)

[Centre de développement Azure]: http://www.windowsazure.com/fr-fr/develop/overview/
[Portail Azure]: http://manage.windowsazure.com
[Site web Git]: http://git-scm.com
[Installation de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Utilisation de PowerShell pour Azure]: http://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/
[Utilisation des outils en ligne de commande Azure pour Mac et Linux]: /fr-fr/develop/nodejs/how-to-guides/command-line-tools/
[Documentation Git]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Création d'un référentiel (GitHub)]: https://help.github.com/articles/create-a-repo
[Utilisation de Git avec CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Création d'un référentiel (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Démarrage rapide - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Utilisation de Dropbox pour partager les référentiels Git]: https://gist.github.com/trey/2722927
[Livraison continue sur Azure au moyen de Visual Studio Online]: http://www.windowsazure.com/fr-fr/develop/net/common-tasks/publishing-with-tfs/
<!--HONumber=42-->
