[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) prend en charge le déploiement continu à partir d'outils de contrôle de code source et de référentiel tels que GitHub, BitBucket et Visual Studio Team Services. Ces outils permettent de mettre à jour le contenu et le code de votre application, puis d'envoyer rapidement et facilement les modifications à votre application App Service.

Dans cet article, vous allez apprendre à utiliser Git pour publier directement de votre ordinateur local vers Web Apps (dans Azure, cette méthode de publication est appelée **Git local**). Vous allez également découvrir comment activer le déploiement continu à partir de sites de référentiel tels que BitBucket et GitHub. Pour plus d'informations sur l'utilisation de VSTS dans le cadre d'un déploiement continu, consultez la page [Diffusion continue sur Azure au moyen de Visual Studio Team Services].

> [!NOTE]
> La plupart des commandes Git décrites dans cet article sont exécutées automatiquement pendant la création d'une application web avec les [outils en ligne de commande Azure pour Mac et Linux](/develop/nodejs/how-to-guides/command-line-tools/).
> 
> 

## <a id="Step1"></a>Étape 1 : Installation de Git
La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installation de Git] pour accéder aux distributions et consignes d'installation propres aux différents systèmes d'exploitation.

> [!NOTE]
> Sur certains systèmes d'exploitation, une version en ligne de commande et une version avec interface utilisateur graphique sont toutes deux disponibles. Les instructions fournies dans cet article utilisent la version en ligne de commande.
> 
> 

## <a id="Step2"></a>Étape 2 : création d'un référentiel local
Effectuez les tâches suivantes pour créer un nouveau référentiel Git.

1. Créez un répertoire nommé MyGitRepository qui contiendra votre référentiel Git et vos fichiers d’application web.
2. Ouvrez un outil de ligne de commande, tel que **GitBash** (Windows) ou **Bash** (Unix Shell). Sur les systèmes OS X, la ligne de commande est accessible depuis l'application **Terminal**.
3. À partir de la ligne de commande, indiquez le répertoire MyGitRepository.
   
        cd MyGitRepository
4. Utilisez la commande suivante pour initialiser un nouveau référentiel Git :
   
        git init
   
    Cette commande doit renvoyer un message similaire au suivant **Initialized empty Git repository in [chemin d'accès]**.

## <a id="Step3"></a>Étape 3 : ajout d'une page web
Web Apps prend en charge des applications créées dans différents langages de programmation. Pour les besoins de cet exemple, vous allez utiliser un fichier .html statique.

1. Au moyen d'un éditeur de texte, créez un fichier nommé **index.html** à la racine du référentiel Git (répertoire MyGitRepository créé plus tôt).
2. Ajoutez le texte suivant dans le fichier index.html et enregistrez ce dernier.
   
        Hello Git!
3. Dans la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git. Utilisez ensuite la commande suivante pour ajouter le fichier **index.html** au référentiel :
   
        git add index.html 
   
   > [!NOTE]
   > Pour obtenir de l’aide concernant les commandes git, tapez -help ou --help à la suite de la commande. Par exemple, pour les options de paramètre de la commande add, tapez « git add -help » pour obtenir de l'aide sur la ligne de commande ou « add --help » pour accéder à une aide plus détaillée.
   > 
   > 
4. Ensuite, validez les modifications apportées au référentiel au moyen de la commande suivante :
   
        git commit -m "Adding index.html to the repository"
   
    Le résultat ressemble à ce qui suit :
   
        [master (root-commit) 369a79c] Adding index.html to the repository
         1 file changed, 1 insertion(+)
         create mode 100644 index.html

## <a id="Step4"></a>Activer le référentiel d’application web
Pour activer un référentiel Git pour votre application Web, procédez comme suit.

1. Connectez-vous au [portail Azure].
2. Dans le panneau de l’application Web, cliquez sur **Paramètres > Déploiement continu**. Cliquez successivement sur **Choisir une source**, **Référentiel Git local** et **OK**.
   
    ![Référentiel Git local](./media/publishing-with-git/azure1-local-git.png)
3. Si vous configurez un référentiel pour la première fois dans Azure, vous devez créer des informations d’identification de connexion pour ce référentiel. Vous les utiliserez pour vous connecter au référentiel Azure et pour envoyer les modifications depuis votre référentiel Git local. Dans le panneau de votre application web, cliquez sur **Paramètres > Informations d’identification du déploiement**, puis configurez le nom d’utilisateur et le mot de passe de votre déploiement. Quand vous avez terminé, cliquez sur **OK**.
   
    ![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>Déploiement du projet
* [Envoi des fichiers locaux vers Azure (Git local)](#Step6)
* [Déployer des fichiers à partir d'un site web de référentiel tel que BitBucket, CodePlex, Dropbox, GitHub ou Mercurial](#Step7)
* [Déployer une solution Visual Studio depuis BitBucket, CodePlex, Dropbox, GitHub ou Mercurial](#Step75)

Pour publier votre application Web vers Azure à l’aide de Git local, procédez comme suit :

1. Dans le panneau de l’application Web, cliquez sur **Paramètres > Propriétés** pour l’**URL Git**.
   
    ![](./media/publishing-with-git/azure3-repo-details.png)
   
    **URL Git** est la référence hors programme vers laquelle vous effectuez le déploiement à partir de votre référentiel local. Vous utiliserez cette URL dans la procédure suivante.
2. Dans la ligne de commande, vérifiez que vous êtes bien à la racine de votre référentiel Git local contenant le fichier index.html créé précédemment.
3. Utilisez `git remote` pour ajouter la référence hors programme répertoriée dans **URL Git** à l’étape 1. Votre commande ressemble à ce qui suit :
   
        git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
   
   > [!NOTE]
   > La commande **remote** ajoute une référence nommée dans un référentiel distant. Dans cet exemple, une référence nommée « azure » est créée pour le référentiel de votre application web.
   > 
   > 
4. Entrez la commande suivante à partir de la ligne de commande pour envoyer le contenu de référentiel actuel du référentiel local au référentiel distant « azure » :
   
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
        * [new branch]        master -> master
   
   > [!NOTE]
   > Le référentiel créé pour votre application web s'attend à ce que les requêtes de transmission de type push ciblent la branche <strong>principale</strong> de son référentiel, qui sera dès lors utilisée comme contenu de l'application web.
   > 
   > 
5. Revenez au panneau de votre application web dans le portail Azure. L'option **Déploiement introuvable** doit être remplacée par **Déploiement actif** avec une entrée de journal de votre dernière transmission de type push.
   
    ![](./media/publishing-with-git/azure4-deployed.png)
6. Dans la partie supérieure du panneau de l’application web, cliquez sur le lien situé sous l’**URL** afin de vérifier que l’élément **index.html** a été déployé. Une page contenant « Hello Git! » s’affiche.
   
    ![Page Web contenant « Hello Git! »][hello-git]
7. Au moyen d'un éditeur de texte, modifiez le fichier **index.html** de façon à ce qu'il contienne « Super ! », puis enregistrez le fichier.
8. Utilisez les commandes suivantes à partir de la ligne de commande pour **ajouter** et **valider** les modifications, puis **les envoyer** vers le référentiel distant :
   
        git add index.html
        git commit -m "Celebration"
        git push azure master
   
    Une fois que la commande **push** a été exécutée, actualisez le navigateur (vous devrez peut-être appuyer sur Ctrl+F5 pour qu'il soit actualisé correctement). Notez que le contenu de la page reflète à présent la dernière modification validée.

### <a id="Step7"></a>Déployer des fichiers à partir d’un site référentiel comme BitBucket, CodePlex, Dropbox, GitHub ou Mercurial
La transmission de type push des fichiers locaux vers Azure au moyen de Git local vous permet d’envoyer manuellement des mises à jour d’un projet local vers votre application web Azure, alors qu’un déploiement depuis BitBucket, CodePlex, Dropbox, GitHub ou Mercurial donne lieu à un processus de déploiement continu au cours duquel Azure va récupérer les mises à jour les plus récentes de votre projet.

Bien que les deux méthodes aboutissent au déploiement de votre projet vers Web Apps, le déploiement continu est utile quand plusieurs personnes travaillent sur un projet et qu’elles veulent s’assurer que la version la plus récente est toujours publiée, quel qu’en soit l’auteur. Le déploiement continu s’avère également utile si vous vous servez de l’un des outils mentionnés ci-dessus en tant que référentiel central pour votre application.

Le déploiement de fichiers à partir de GitHub, CodePlex ou BitBucket requiert que vous ayez publié votre projet local sur l'un de ces services. Pour plus d'informations sur la publication de votre projet sur ces services, consultez les pages [Création d'un référentiel (GitHub)], [Utilisation de Git avec CodePlex], [Création d'un référentiel (BitBucket)], [Utilisation de Dropbox pour partager des référentiels Git] et [Démarrage rapide - Mercurial].

1. Commencez par placer vos fichiers d’application web dans le référentiel sélectionné qui sera utilisé dans le cadre d’un déploiement continu.
2. Dans le panneau de l’application Web, dans le portail, cliquez sur **Paramètres > Livraison continue**. Cliquez sur **Choisir une source**, puis sur **GitHub** par exemple.
   
    ![](./media/publishing-with-git/azure6-setup-github.png)
3. Dans le panneau **Déploiement continu**, cliquez sur **Autorisation**, puis sur **Autoriser**. Le portail Azure va vous rediriger vers le site référentiel afin de terminer la procédure d’autorisation.
4. Quand vous avez terminé, revenez au portail Azure, puis cliquez sur **OK** dans le panneau **Autorisation**.
5. Dans le panneau **Déploiement continu**, choisissez l'organisation, le projet et la branche à partir desquels vous souhaitez effectuer le déploiement. Quand vous avez terminé, cliquez sur **OK**.
   
    ![](./media/publishing-with-git/azure7-setup-github-configure.png)
   
   > [!NOTE]
   > Lors de l’activation du déploiement continu avec GitHub ou BitBucket, les projets publics et privés sont tous deux affichés.
   > 
   > 

Azure crée une association avec le référentiel sélectionné et récupère les fichiers à partir de la branche spécifiée. À l'issue de cette procédure, la section **Déploiement** du panneau de votre application web affiche un message **Déploiement actif** qui indique la réussite du déploiement.

1. À ce stade, votre projet a été déployé à partir du référentiel de votre choix vers votre application web. Pour vérifier que cette application web est active, cliquez sur l’**URL** en haut du portail. Le navigateur doit accéder à l’application web.
2. Pour vérifier que le déploiement continu s’effectue à partir du référentiel de votre choix, envoyez une modification vers le référentiel par le biais d’une transmission de type push. Votre application web doit se mettre à jour pour prendre en compte les modifications, peu de temps après la transmission de type push vers le référentiel. Vous pouvez vérifier que la mise à jour a été récupérée dans le panneau **Déploiements** de votre application web.

### <a id="Step75"></a>Déployer une solution Visual Studio depuis BitBucket, CodePlex, Dropbox, GitHub ou Mercurial
La transmission de type push d’une solution Visual Studio vers Web Apps dans Azure App Service est tout aussi simple que celle d’un simple fichier index.html. Le processus de déploiement de Web Apps simplifie tous les détails, notamment la restauration des dépendances NuGet et la génération des fichiers binaires de l’application. Vous pouvez suivre les meilleures pratiques de contrôle de code source pour la maintenance du code seulement dans votre référentiel Git et laisser le déploiement de Web Apps s’occuper du reste.

Les étapes pour envoyer votre solution Visual Studio vers Web Apps sont les mêmes que celles de la [section précédente](#Step7), à condition de configurer votre solution et votre référentiel de la façon suivante :

* À la racine de votre référentiel, ajoutez un fichier `.gitignore`, puis spécifiez tous les fichiers et dossiers que vous souhaitez exclure de votre référentiel, tels que les dossiers `Obj`, `Bin` et `packages` (voir la [documentation de gitignore](http://git-scm.com/docs/gitignore) pour la mise en forme). Par exemple :
  
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
  
  > [!NOTE]
  > Si vous utilisez GitHub, vous pouvez éventuellement générer un fichier .gitignore propre à Visual Studio quand vous créez votre référentiel, qui comprend tous les fichiers temporaires, les résultats de la génération, etc. Vous pouvez ensuite le personnaliser pour l'adapter à vos besoins spécifiques.
  > 
  > 
* Ajoutez l’arborescence des répertoires de toute la solution à votre référentiel, avec le fichier .sln dans la racine du référentiel.

Une fois que vous avez configuré votre référentiel comme indiqué ainsi que votre application web dans Azure pour la publication en continu depuis l’un des référentiels Git en ligne, vous pouvez développer votre application ASP.NET localement dans Visual Studio et déployer en continu votre code, en envoyant simplement les modifications apportées à votre référentiel Git en ligne par une transmission de type push.

## Désactiver le déploiement continu
Le déploiement continu peut être désactivé dans le panneau **Déploiements**. Dans le panneau de l’application Web, cliquez sur **Paramètres > Déploiement continu**. puis sur **Déconnecter**.

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)

Après avoir répondu **Oui** au message de confirmation, vous pouvez revenir au panneau de votre application web et cliquer sur **Paramètre > Déploiement continu** si vous souhaitez configurer la publication à partir d’une autre source.

## <a id="Step8"></a>Résolution des problèmes
Voici les erreurs ou les problèmes rencontrés couramment lors de l’utilisation de Git pour publier vers une application web dans Azure :

- - -
**Symptôme** : Impossible d'accéder à '[URL\_du\_site]' : Impossible de se connecter à [Adresse\_scm]

**Cause** : Cette erreur peut se produire si l'application web n'est pas opérationnelle.

**Résolution** : démarrez l’application web dans le portail Azure. Le déploiement Git ne fonctionne pas tant que l’application web n’est pas en cours d’exécution.

- - -
**Symptôme** : Impossible de résoudre l'hôte « nom\_hôte »

**Cause** : Cette erreur peut se produire si les informations d'adresse entrées au moment de la création du référentiel distant « azure » sont incorrectes.

**Résolution** : Utilisez la commande `git remote -v` pour répertorier tous les référentiels distants avec l'URL associée. Vérifiez que l'URL du référentiel distant « azure » est correcte. Si nécessaire, supprimez et recréez ce référentiel distant au moyen de l’URL correcte.

- - -
**Symptôme** : aucune référence en commun, ni spécifiée ; aucune action effectuée. Perhaps you should specify a branch such as 'master'.

**Cause** : Cette erreur peut se produire si vous ne spécifiez pas de branche au moment de l'exécution d'une opération git push et que vous n'avez pas défini la valeur push.default utilisée par Git.

**Résolution** : Exécutez de nouveau l'opération Push, en spécifiant la branche principale. Par exemple :

    git push azure master

- - -
**Symptôme** : src refspec [nom\_branche] ne correspond à aucun élément.

**Cause** : Cette erreur peut se produire si vous tentez d'effectuer une opération Push sur une autre branche que la branche principale sur le référentiel distant « azure ».

**Résolution** : Exécutez de nouveau l'opération Push, en spécifiant la branche principale. Par exemple :

    git push azure master

- - -
**Symptôme** : erreur : des modifications ont été validées dans le référentiel distant, mais votre application web n’a pas été mise à jour.

**Cause** : Cette erreur peut se produire si vous déployez une application Node.js contenant un fichier package.json spécifiant des modules obligatoires supplémentaires.

**Résolution** : Des messages supplémentaires contenant « npm ERR! » doivent être consignés avant cette erreur et peuvent fournir davantage de contexte sur la défaillance. Voici les causes connues de cette erreur et le message « npm ERR! » correspondant :

* **Fichier package.json incorrect** : npm ERR! Couldn’t read dependencies.
* **Native module that does not have a binary distribution for Windows**:
  
  * npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1
    
      OU
  * npm ERR! [nom-module@version] preinstall: `make || gmake`

## Ressources supplémentaires
* [Comment utiliser PowerShell pour Azure]
* [Comment utiliser les outils en ligne de commande Azure pour Mac et Linux]
* [Documentation Git]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).

[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[portail Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installation de Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Comment utiliser PowerShell pour Azure]: ../articles/install-configure-powershell.md
[Comment utiliser les outils en ligne de commande Azure pour Mac et Linux]: ../articles/xplat-cli-install.md
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
[Utilisation de Dropbox pour partager des référentiels Git]: https://gist.github.com/trey/2722927
[Diffusion continue sur Azure au moyen de Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=AcomDC_0406_2016-->