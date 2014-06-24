# Publication à partir du contrôle de code source dans Sites Web Azure

Le service Sites Web Azure prend en charge le déploiement continu à
partir du contrôle de code source et d'outils de référentiel tels que
BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial et TFS. Ces outils
permettent de mettre à jour le contenu et le code de votre site Web,
puis d'envoyer (par opération Push) rapidement et facilement les
modifications à votre convenance.

Dans cet article, vous allez apprendre à utiliser Git pour publier
directement à partir de votre ordinateur local sur un site Web Azure
(dans Azure, cette méthode de publication est appelée **Git local**).
Vous allez également découvrir comment activer le déploiement continu à
partir de sites Web de référentiel tels que BitBucket, CodePlex,
DropBox, GitHub ou Mercurial. Pour plus d'informations sur
l'utilisation de TFS dans le cadre d'un déploiement continu, consultez
la page [Livraison continue sur Azure au moyen de Visual Studio
Online][1].

> [WACOM.NOTE] Plusieurs des commandes Git décrites dans cet article
> sont exécutées automatiquement lors de la création d'un site Web à
> l'aide des [outils en ligne de commande Azure pour Mac et
> Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

La tâche inclut les étapes suivantes :

* [Installation de Git](#Step1)
* [Création d'un référentiel local](#Step2)
* [Ajout d'une page Web](#Step3)
* [Activation du référentiel de sites Web](#Step4)
* [Déploiement du projet](#Step5)
	* [Envoi des fichiers locaux vers Azure (Git local)](#Step6)
	* [Déploiement de fichiers à partir d'un site Web de référentiel tel    que BitBucket, CodePlex, Dropbox, GitHub ou Mercurial](#Step7)
* [Résolution des problèmes](#Step8)

<h2><a  id="Step2" ></a>Installation de Git</h2>


La procédure requise pour installer Git diffère selon les systèmes
d'exploitation. Consultez la rubrique [Installation de Git][2] pour
accéder aux distributions et consignes d'installation propres aux
différents systèmes d'exploitation.

> [WACOM.NOTE] Sur certains systèmes d'exploitation, une version en
> ligne de commande et une version avec interface utilisateur graphique
> sont toutes deux disponibles. Les instructions fournies dans cet
> article utilisent la version en ligne de commande.

<h2><a  id="Step2" ></a>Création d'un référentiel local</h2>


Effectuez les tâches suivantes pour créer un nouveau référentiel Git.

1.  Créez un répertoire nommé MyGitRepository qui contiendra votre
    référentiel Git et vos fichiers de site Web.

2.  Ouvrez une ligne de commande, telle que **GitBash** (Windows) ou
    **Bash** (Unix Shell). Sur les systèmes OS X, la ligne de commande
    est accessible depuis l'application **Terminal**.

3.  À partir de la ligne de commande, indiquez le répertoire
    MyGitRepository.
    
         cd MyGitRepository

4.  Utilisez la commande suivante pour initialiser un nouveau
    référentiel Git :
    
         git init
    
    Cette commande doit renvoyer un message similaire au suivant
    **Initialized empty Git repository in [chemin d'accès]**.

<h2><a  id="Step3" ></a>Ajout d'une page Web</h2>


Le service Sites Web Azure prend en charge les applications créées dans
divers langages de programmation. Pour les besoins de cet exemple, vous
allez utiliser un fichier .html statique. Pour plus d'informations sur
la publication de sites Web dans d'autres langages de programmation
sous Azure, consultez le [Centre de développement Azure][3].

1.  Au moyen d'un éditeur de texte, créez un fichier nommé
    **index.html** à la racine du référentiel Git (répertoire
    MyGitRepository créé plus tôt).

2.  Ajoutez le texte suivant dans le fichier index.html et enregistrez
    ce dernier.
    
         Hello Git!

3.  Dans la ligne de commande, vérifiez que vous êtes bien à la racine
    de votre référentiel Git. Utilisez ensuite la commande suivante pour
    ajouter le fichier **index.html** au référentiel :
    
         git add index.html 
    
    > [WACOM.NOTE] Pour obtenir de l'aide concernant les commandes
    > git, tapez -help ou --help à la suite de la commande. Par exemple,
    > pour les options de paramètre de la commande add, tapez ˜git add
    > -help˜ obtenir de l'aide sur la ligne de commande ou ˜git add
    > --help˜ pour accéder à une aide plus détaillée.

4.  Ensuite, validez les modifications apportées au référentiel au moyen
    de la commande suivante :
    
         git commit -m "Adding index.html to the repository"
    
    Le résultat ressemble à ce qui suit :
    
         [master (root-commit) 369a79c] Adding index.html to the repository
          1 file changed, 1 insertion(+)
          create mode 100644 index.html

<h2><a  id="Step4" ></a>Activation du référentiel de sites Web</h2>


Effectuez les étapes suivantes pour activer un référentiel Git pour
votre site Web au moyen du portail Azure :

1.  Connectez-vous au [portail Azure][4].

2.  Dans la partie gauche de la page, sélectionnez **Sites Web**, puis
    sélectionnez le site Web pour lequel vous voulez activer un
    référentiel.
    
    ![Image affichant un site Web
    sélectionné](./media/publishing-with-git/git-select-website.png)

3.  Sélectionnez l'onglet **Tableau de bord**.

4.  Dans la section **aperçu rapide**, sélectionnez **Configurer le
    déploiement à partir du contrôle de code source**. La boîte de
    dialogue **Configurer le déploiement** suivante apparaît.
    
    ![git-WhereIsYourSourceCode](./media/publishing-with-git/git-WhereIsYourSourceCode.png)

5.  Choisissez **Local Git**, puis cliquez sur la flèche **Suivant**.

6.  Quelques secondes plus tard, un message vous indique que votre
    référentiel est prêt.
    
    ![Instructions
    Git](./media/publishing-with-git/git-instructions.png)

<h2><a  id="Step5" ></a>Déploiement du projet</h2>


<h3><a  id="Step6" ></a>Envoi des fichiers locaux vers Azure (Git local)</h3>


À ce stade, le portail affiche des instructions sur l'initialisation
d'un référentiel local et l'ajout de fichiers. Vous avez déjà effectué
ces opérations lors des étapes citées plus haut dans cette même
rubrique. Toutefois, si vous n'avez pas configuré vos informations
d'identification de déploiement; suivez l'étape 3 figurant dans les
instructions. Celle-ci vous indique de suivre un lien nommé
**Réinitialisez vos informations d'identification de déploiement**.

Pour publier votre site Web dans Azure à l'aide de Git locat, procédez
comme suit :

1.  Dans la ligne de commande, vérifiez que vous êtes bien à la racine
    de votre référentiel Git local contenant le fichier index.html créé
    précédemment.

2.  Copiez la commande git remote add indiquée à l'étape 3 des
    instructions renvoyées par le portail. Elle ressemble à la commande
    suivante :
    
         git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
    
    > [WACOM.NOTE] La commande **remote** ajoute une référence nommée
    > dans un référentiel distant. Dans cet exemple, elle crée une
    > référence nommée << azure >> pour votre référentiel de sites Web
    > Azure.

3.  Entrez la commande suivante à partir de la ligne de commande pour
    envoyer le contenu de référentiel actuel du référentiel local au
    référentiel distant << azure >> :
    
        git push azure master
    
    Vous êtes invité à indiquer le mot de passe créé précédemment
    lorsque vous réinitialisez vos informations d'identification de
    déploiement dans le portail. Entrez le mot de passe (notez que
    Gitbash ne génère pas d'astérisques dans la console lorsque vous
    tapez votre mot de passe). Le résultat ressemble à ce qui suit :
    
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
    
    > [WACOM.NOTE] Le référentiel créé pour votre site Web Azure
    > s'attend à ce que les demandes d'opération Push ciblent la
    > branche principale (**master**) de son
    > référentiel, qui sera dès lors utilisée comme contenu du site Web.

4.  Dans la partie inférieure du portail, cliquez sur le lien
    **PARCOURIR** pour vérifier que la page **index.html** a été
    déployée. Une page contenant << Hello Git! >> s'affiche.
    
    ![Page Web contenant « Hello
    Git! »](./media/publishing-with-git/git-hello-git.png)

5.  Au moyen d'un éditeur de texte, modifiez le fichier **index.html**
    de façon à ce qu'il contienne << Super ! >>, puis enregistrez le
    fichier.

6.  Utilisez les commandes suivantes à partir de la ligne de commande
    pour **ajouter** et **valider** les modifications, puis **les
    envoyer** vers le référentiel distant :
    
         git add index.html
         git commit -m "Celebration"
         git push azure master
    
    Une fois que la commande **push** a été exécutée, actualisez le
    navigateur (vous devrez peut-être appuyer sur Ctrl+F5 pour qu'il
    soit actualisé correctement). Notez que le contenu de la page
    reflète à présent la dernière modification validée.
    
    ![Page Web contenant
    « Super ! »](./media/publishing-with-git/git-yay.png)

<h3><a  id="Step7" ></a>Déploiement de fichiers à partir d'un site Web de référentiel tel que BitBucket, CodePlex, Dropbox, GitHub ou Mercurial</h3>


L'envoi par opération Push de fichiers locaux vers Azure au moyen de
Local Git vous permet d'envoyer manuellement par Push des mises à jour
d'un projet local vers votre site Web Azure, alors qu'un déploiement à
partir de BitBucket, CodePlex, Dropbox, GitHub ou Mercurial donne lieu à
un processus de déploiement continu à l'occasion duquel Azure va
récupérer les mises à jour les plus récentes de votre projet.

Bien que les deux méthodes donnent lieu à un déploiement de votre projet
sur un site Web Azure, le déploiement continu est utile lorsque
plusieurs personnes travaillent sur un projet et qu'elles veulent
s'assurer que la version plus récente est toujours publiée quel qu'en
soit l'auteur. Le déploiement continu s'avère également utile si vous
vous servez de l'un des outils mentionnés ci-dessus en tant que
référentiel central pour votre application.

Le déploiement de fichiers à partir de GitHub, CodePlex ou BitBucket
requiert que vous ayez publié votre projet local sur l'un de ces
services. Pour plus d'informations sur la publication de votre projet
sur ces services, consultez les pages [Création d'un référentiel
(GitHub)][5], [Utilisation de Git avec CodePlex][6], [Création d'un
référentiel (BitBucket)][7], [Utilisation de Dropbox pour partager des
référentiels Git][8] et [Démarrage rapide - Mercurial][9].

1.  Commencez par placer les fichiers de site Web dans le référentiel
    sélectionné qui sera utilisé dans le cadre d'un déploiement
    continu.

2.  Dans le portail Azure de votre site Web, accédez à l'onglet
    **Tableau de bord**. Dans la section **aperçu rapide**, sélectionnez
    **Configurer le déploiement à partir du contrôle de code source**.
    La boîte de dialogue **Configurer le déploiement** s'affiche avec
    le message suivant **Où est votre code source ?**.

3.  Choisissez la méthode de contrôle du code source que vous souhaitez
    utiliser pour le déploiement continu.

4.  Lorsque vous y êtes invité, entrez les informations
    d'identification du service que vous avez sélectionné.

5.  Une fois que vous avez autorisé Azure à accéder à votre compte, une
    liste de référentiels s'affiche.
    
    ![git-ChooseARepositoryToDeploy](./media/publishing-with-git/git-ChooseARepositoryToDeploy.png)

6.  Sélectionnez le référentiel que vous voulez associer à votre site
    Web Azure. Cliquez sur la coche pour continuer.
    
    > [WACOM.NOTE] Lors de l'activation du déploiement continu avec
    > GitHub ou BitBucket, les projets publics et privés sont tous deux
    > affichés.

7.  Azure crée une association avec le référentiel sélectionné et
    récupère les fichiers à partir de la branche principale. Au terme de
    ce processus, l'**historique de déploiement** dans la page
    **Déploiements** affiche un message **Déploiement actif** similaire
    au suivant :
    
    ![git-githubdeployed](./media/publishing-with-git/git-GitHubDeployed.png)

8.  À ce stade, votre projet a été déployé à partir du référentiel de
    votre choix sur votre site Web Azure. Pour vérifier que le site est
    actif, cliquez sur le lien **Parcourir** dans la partie inférieure
    du portail. Le navigateur doit accéder au site Web.

9.  Pour vérifier que le déploiement continu a bien lieu, effectuez une
    modification dans votre projet, puis envoyez par Push la mise à jour
    vers le référentiel que vous avez associé à ce site Web. Votre site
    Web doit se mettre à jour pour prendre en compte les modifications,
    dans un court délai suivant la fin de l'envoi par Push vers le
    référentiel. Vous pouvez vérifier que les mises à jour ont été
    récupérées sur la page **Déploiements** de votre site Web.
    
    ![git-GitHubDeployed-Updated](./media/publishing-with-git/git-GitHubDeployed-Updated.png)

<h4>Fonctionnement du déploiement continu</h4>


Le déploiement continu fonctionne comme suit : il fournit l'**URL du
déclencheur du déploiement** dans la section **déploiements** de
l'onglet **Configurer** de votre site.

![git-DeploymentTrigger](./media/publishing-with-git/git-DeploymentTrigger.png)

Lorsque des mises à jour sont effectuées dans votre référentiel, une
demande POST est envoyée à cette URL, notifiant votre site Web Azure que
le référentiel a été mis à jour. À ce moment, la mise à jour est
récupérée et déployée sur votre site Web.

<h4>Spécification de la branche à utiliser</h4>


Lorsque vous activez le déploiement continu, la branche principale
(**master**) du référentiel est utilisée par défaut. Si vous voulez
utiliser une autre branche, procédez comme suit :

1.  Dans le portail, sélectionnez votre site Web, puis **CONFIGURER**.

2.  Dans la section **déploiements** de la page, entrez la branche que
    vous voulez utiliser dans le champ **Branche à déployer**, puis
    appuyez sur Entrée. Pour finir, cliquez sur **Enregistrer**.
    
    Azure commence immédiatement à effectuer la mise à jour sur la base
    des modifications apportées à la nouvelle branche.

<h4>Désactivation du déploiement continu</h4>


Le déploiement continu peut être désactivé à partir du **Tableau de
bord** Azure. Sous la section **aperçu rapide**, choisissez l'option de
déconnexion du référentiel que vous utilisez :

![git-DisconnectFromGitHub](./media/publishing-with-git/git-DisconnectFromGitHub.png)

Après avoir répondu **Oui** au message de confirmation, vous pouvez
revenir à la section **aperçu rapide** et cliquer sur **Configurer le
déploiement à partir du contrôle de code source** si vous voulez
configurer la publication à partir d'une autre source.

<h2><a  id="Step8" ></a>Résolution des problèmes</h2>


Voici des erreurs ou des problèmes fréquemment rencontrés lors de
l'utilisation de Git pour publier sur un site Web Azure :

* * *

**Symptôme** : Couldn't resolve host 'hostname'

**Cause** : cette erreur peut se produire si les informations
d'adresse entrées lors de la création du référentiel distant
<< azure >> sont incorrectes.

**Résolution** : utilisez la commande `git remote -v` pour répertorier
tous les référentiels distants avec l'URL associée. Vérifiez que l'URL
du référentiel distant << azure >> est correcte. Si nécessaire,
supprimez et recréez ce référentiel distant au moyen de l'URL correcte.

* * *

**Symptôme** : No refs in common and none specified; doing nothing.
Perhaps you should specify a branch such as 'master'.

**Cause** : cette erreur peut se produire si vous ne spécifiez pas de
branche lors de l'exécution d'une opération git push et que vous
n'avez pas défini la valeur push.default utilisée par Git.

**Résolution** : réexécutez l'opération Push, en spécifiant la branche
principale. Par exemple :

    git push azure master

* * *

**Symptôme** : src refspec [branchname] does not match any.

**Cause** : cette erreur peut se produire si vous tentez d'effectuer
une opération Push sur une autre branche que la branche principale sur
le référentiel distant << azure >>.

**Résolution** : réexécutez l'opération Push, en spécifiant la branche
principale. Par exemple :

    git push azure master

* * *

**Symptôme** : Error - Changes commited to remote repository but your
web site not updated.

**Cause** : cette erreur peut se produire si vous déployez une
application Node.js contenant un fichier package.json spécifiant des
modules obligatoires supplémentaires.

**Résolution** : des messages supplémentaires contenant << npm ERR! >>
doivent être consignés avant cette erreur et peuvent fournir davantage
de contexte sur la défaillance. Voici les causes connues de cette erreur
et le message << npm ERR! >> correspondant :

* **Malformed package.json file**: npm ERR! Couldn't read
  dependencies.

* **Native module that does not have a binary distribution for
  Windows**:
  
  * npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1
    
    OU
  
  * npm ERR! [modulename@version] preinstall: \`make || gmake\`
## Ressources supplémentaires

* [Utilisation de PowerShell pour Azure][10]
* [Utilisation des outils en ligne de commande Azure pour Mac et
  Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/)
* [Documentation Git][11]



[1]: http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-tfs/
[2]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[3]: http://www.windowsazure.com/en-us/develop/overview/
[4]: http://manage.windowsazure.com
[5]: https://help.github.com/articles/create-a-repo
[6]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[7]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[8]: https://gist.github.com/trey/2722927
[9]: http://mercurial.selenic.com/wiki/QuickStart
[10]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/
[11]: http://git-scm.com/documentation
