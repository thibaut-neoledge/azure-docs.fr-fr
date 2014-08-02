<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="" />

Déploiement d'une application Web Ruby on Rails sur une machine virtuelle Azure à l'aide de Capistrano
======================================================================================================

Ce didacticiel montre comment déployer un site Web basé sur Ruby on Rails sur une machine virtuelle Azure à l'aide de [Capistrano](https://github.com/capistrano/capistrano/). Il montre également comment utiliser [Nginx](http://nginx.org/) et [Unicorn](https://github.com/blog/517-unicorn) pour héberger l'application sur la machine virtuelle.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. Une fois ce didacticiel terminé, vous aurez une application basée sur Ruby on Rails installée et fonctionnelle dans le cloud.

Vous apprendrez à :

-   configurer votre environnement de développement ;

-   installer Ruby et Ruby on Rails ;

-   installer et configurer Nginx et Unicorn ;

-   créer une application Rails ;

-   déployer une application Rails sur une machine virtuelle Azure à l'aide de Capistrano.

La capture d'écran suivante présente l'application terminée :

![navigateur affichant Listing Posts](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png)

**Remarque**

L'application utilisée pour ce didacticiel inclut des composants binaires natifs. C'est pour cette raison que vous pouvez rencontrer des problèmes si votre environnement de développement n'est pas basé sur Linux, car il se peut que le fichier Gemfile.lock produit sur l'ordinateur de développement n'inclue pas les entrées de la version compatible de Linux des maillons requis.

Des étapes spécifiques sont exigées avec un environnement de développement Windows, car cela représente le delta le plus important avec l'environnement de déploiement cible. Toutefois, si vous êtes confronté à des erreurs pendant ou après le déploiement qui ne sont pas traitées dans les étapes décrites dans cet article, vous pouvez reproduire les étapes de cet article dans un environnement de développement Linux.

Dans cet article
----------------

-   [Configuration de l'environnement de développement](#setup)

-   [Création d'une application Rails](#create)

-   [Test de l'application](#test)

-   [Création d'un référentiel source](#repository)

-   [Création d'une machine virtuelle Azure](#createvm)

-   [Test de Nginx](#nginx)

-   [Préparation du déploiement](#capify)

-   [Déploiement](#deploy)

-   [Étapes suivantes](#next)

Configuration de l'environnement de développement
-------------------------------------------------

1.  Installez Ruby dans votre environnement de développement. En fonction de votre système d'exploitation, les étapes peuvent varier.

    -   **Apple OS X** : il existe plusieurs distributions Ruby pour OS X. Ce didacticiel a été validé sur OS X en utilisant [Homebrew](http://brew.sh/) pour installer **rbenv** et **ruby-build**. Pour plus d'informations sur l'installation, consultez la page <https://github.com/sstephenson/rbenv/>.

    -   **Linux** : utilisez le système de gestion des packages de distributions. Ce didacticiel a été validé sur Ubuntu 12.10 à l'aide des packages ruby1.9.1 et ruby1.9.1-dev.

    -   **Windows** : il existe plusieurs distributions Ruby pour Windows. Ce didacticiel a été validé en utilisant [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392.

2.  Ouvrez une nouvelle ligne de commande ou session terminal et saisissez la commande suivante pour installer Ruby on Rails :

         gem install rails --no-rdoc --no-ri

    **Remarque**

    Cette commande peut nécessiter des privilèges administrateur ou racine sur certains systèmes d'exploitation. Si vous recevez une erreur lors de l'exécution de cette commande, essayez d'utiliser « sudo » comme suit :

    ``` {.prettyprint}
    sudo gem install rails
    ```

    **Remarque**

    La version 3.2.12 du maillon Rails a été utilisée pour ce didacticiel.

3.  Vous devez également installer un interpréteur JavaScript, qui sera utilisé par Rails pour compiler des ressources CoffeeScript utilisées par votre application Rails. Une liste des interpréteurs pris en charge est disponible à la page <https://github.com/sstephenson/execjs#readme>.

    [Node.js](http://nodejs.org/) a été utilisé au cours de la validation de ce didacticiel, car il est disponible pour les systèmes d'exploitation OS X, Linux et Windows.

Création d'une application Rails
--------------------------------

1.  Depuis la ligne de commande ou la session terminal, créez une application Rails nommée « blog\_app » à l'aide la commande suivante :

         rails new blog_app

    Cette commande permet de créer un répertoire nommé **blog\_app** et le remplit avec les fichiers et sous-répertoires requis par une application Rails.

    **Remarque**

    Cette commande peut prendre une minute ou plus. Elle effectue une installation sans assistance des maillons pour une application par défaut et, durant cette installation, elle peut apparaître en suspens.

2.  Remplacez les répertoires par le répertoire **blog\_app**, puis utilisez la commande suivante pour créer une structuration de blog de base :

         rails generate scaffold Post name:string title:string content:text

    Cette commande créera les migrations de contrôleurs, de vues, de modèles et de bases de données utilisées pour contenir les publications sur le blog. Chaque publication comporte un nom d'auteur, un titre et un contenu textuel.

3.  Pour créer la base de données qui stockera les publications de blog, utilisez la commande suivante :

         rake db:migrate

    Elle utilise le fournisseur de base de données par défaut pour Rails, qui est la [base de données SQLite3](http://www.sqlite.org/). Bien qu'il soit également possible d'utiliser une autre base de données pour une application de production, SQLite est suffisant pour les objectifs de ce didacticiel.

Test de l'application
---------------------

Pour démarrer le serveur Rails dans votre environnement de développement, procédez comme suit :

1.  Remplacez les répertoires par le répertoire **blog\_app** si vous ne vous y trouvez pas déjà, puis lancez le serveur Rails à l'aide de la commande suivante :

         rails s

    Le résultat ressemble à ce qui suit. Notez le port que le serveur Web écoute. Dans l'exemple ci-dessous, il s'agit du port 3000.

         => Booting WEBrick
         => Rails 3.2.12 application starting in development on http://0.0.0.0:3000
         => Call with -d to detach
         => Ctrl-C to shutdown server
         [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
         [2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
         [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  Ouvrez votre navigateur et accédez à la page http://localhost:3000/. Une page similaire à celle ci-dessous doit s'afficher :

    ![page rails par défaut](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png)

    Cette page est une page de bienvenue statique. Pour afficher les formes générées par la commande de structuration, accédez à http://localhost:3000/posts. Une page similaire à celle ci-dessous doit s'afficher :

    ![page listant les publications](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png)

    Pour arrêter le processus de serveur, entrez CTRL+C dans la ligne de commande

Création d'un référentiel source
--------------------------------

Pour ce didacticiel, nous utilisons [Git](http://git-scm.com/) et [GitHub](https://github.com/) pour le contrôle de version et comme emplacement central pour notre code.

1.  Créez un référentiel sur [GitHub](https://github.com/). Si vous ne possédez pas de compte GitHub, vous pouvez vous inscrire pour obtenir un compte gratuit. Les étapes de ce didacticiel partent du principe que le nom du référentiel est **blog\_app**.

    **Remarque**

    Les scripts créés dans les sections ultérieures de ce document contiennent l'adresse de votre machine virtuelle et le nom d'utilisateur servant à déployer l'application via SSH. Pour cette raison, il est recommandé d'utiliser un référentiel GitHub privé si possible.

2.  À partir de l'invite de commandes, remplacez les répertoires par le répertoire **blog\_app** et exécutez les commandes suivantes pour télécharger la version d'origine de l'application sur votre référentiel GitHub :

        git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
        git push -u origin master

Création d'une machine virtuelle Azure
--------------------------------------

Suivez les instructions fournies [ici](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/) afin de créer une machine virtuelle Azure qui héberge Linux.

**Remarque**

Les étapes de ce didacticiel ont été réalisées sur une machine virtuelle Azure hébergeant Ubuntu 12.10. Si vous utilisez une distribution Linux différente, des étapes différentes peuvent être requises pour accomplir les mêmes tâches.

**Remarque**

Vous devez **uniquement** créer la machine virtuelle. Arrêtez-vous après avoir appris comment connecter la machine virtuelle à l'aide du protocole SSH.

Après avoir créé la machine virtuelle Azure à l'aide du protocole SSH, procédez comme suit pour y installer Ruby et Rails :

1.  Connectez-vous à la machine virtuelle à l'aide du protocole SSH et utilisez les commandes suivantes pour mettre à jour les packages existants et installer votre environnement Ruby :

         sudo apt-get -y update
         sudo apt-get -y upgrade
         sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

    Une fois l'installation terminée, utilisez la commande suivante pour vérifier que Ruby a bien été installé :

         ruby -v

    Cela doit renvoyer la version de Ruby installée sur la machine virtuelle, qui peut être différente de 1.9.1. Par exemple, **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86\_64-linux]**.

2.  Utilisez la commande suivante pour installer Bundler :

         sudo gem install bundler

    Bundler permet d'installer les maillons requis par votre application Rails une fois qu'elle a été copiée sur le serveur.

Ouverture du port 80 et test de Nginx
-------------------------------------

Nginx fournit un site Web par défaut que nous pouvons utiliser pour vérifier si notre machine virtuelle accepte le trafic Web. Effectuez la procédure suivante pour activer le trafic sur le port 80 et tester le site Web Nginx par défaut.

1.  À partir de la session SSH de la machine virtuelle, démarrez le service Nginx :

        sudo service nginx start

    Cela démarre le service Nginx qui écoute le trafic entrant sur le port 80.

2.  Dans votre navigateur, accédez au [portail de gestion Azure](https://manage.windowsazure.com/) et sélectionnez votre machine virtuelle.

    ![liste des machines virtuelles](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png)

3.  Sélectionnez **ENDPOINTS** dans la partie supérieure de la page, puis cliquez sur **+ ADD ENDPOINT** dans la partie inférieure de la page.

    ![page des points de terminaison](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png)

4.  Dans la boîte de dialogue **ADD ENDPOINT**, cliquez sur la flèche dans la partie inférieure gauche afin d'accéder à la deuxième page, puis saisissez les informations suivantes dans le formulaire :

    -   **NOM** : HTTP

    -   **PROTOCOLE** : TCP

    -   **PORT PUBLIC** : 80

    -   **PORT PRIVÉ** : 80

    Ainsi, un port public 80 sera créé pour acheminer le trafic vers le port privé 80, où Nginx écoute.

    ![nouvelle boîte de dialogue de points de terminaison](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png)

5.  Cliquez sur la coche pour enregistrer le point de terminaison.

6.  Un message indiquant **UPDATE IN PROGRESS** doit s'afficher. Une fois que ce message disparaît, le point de terminaison est actif. Vous pouvez désormais tester votre application en accédant au nom DNS de votre machine virtuelle. Le site Web qui apparaît doit ressembler à ce qui suit :

    ![page d'accueil nginx](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png)

7.  Arrêtez et supprimez le site Web Nginx par défaut avec les commandes suivantes :

        sudo service nginx stop
        sudo rm /etc/nginx/sites-enabled/default

    Avec les scripts de déploiement exécutés ultérieurement dans ce didacticiel, le site Web par défaut blog\_app sera servi par Nginx.

Préparation du déploiement
--------------------------

Dans cette section, vous allez modifier l'application pour utiliser le serveur Web Unicorn, activer Capistrano pour le déploiement, activer l'accès GitHub à partir de la machine virtuelle et créer les scripts permettant de déployer et de démarrer l'application.

1.  Autorisez votre machine virtuelle à s'authentifier sur votre compte GitHub à l'aide d'un certificat en suivant la procédure décrite sur la page [Génération de clés SSH](https://help.github.com/articles/generating-ssh-keys#platform-all). Cela permettra d'accéder à votre référentiel GitHub à partir des scripts de déploiement.

    **Remarque**

    Même si la clé SSH doit être générée sur la machine virtuelle, vous pouvez l'ajouter à votre compte GitHub à l'aide du navigateur dans l'environnement de développement.

    Pour afficher le contenu du certificat SSH afin de le copier-coller sur GitHub, utilisez la commande suivante :

    ``` {}
    cat ~/.ssh/id_rsa.pub
    ```

2.  Sur votre ordinateur de développement, modifiez le **Gemfile** et annulez les marques de commentaire sur les lignes pour **Capistrano** et **Unicorn** en supprimant le caractère « \# » au début des lignes suivantes :

         # gem 'unicorn'
         # gem 'capistrano'

    **Remarque**

    Unicorn n'est pas disponible sur Windows. Si vous utilisez Windows comme environnement de développement, modifiez le **Gemfile** pour vous assurer qu'il essaye uniquement d'installer Unicorn lors du déploiement sur la machine virtuelle :

    ``` {.prettyprint}
    platforms :ruby do  gem 'unicorn'end
    ```

3.  Exécutez les commandes suivantes pour installer les nouveaux maillons et configurer Capistrano pour votre projet :

        bundle
        capify .

4.  Ajoutez les fichiers suivants dans le répertoire **blog\_app/config** et remplissez chaque fichier avec le contenu qui se trouve dans les liens ci-dessous :

    -   [nginx.conf](https://gist.github.com/cff582f4f970a95991e9) : configure Nginx pour utiliser Unicorn et servir les fichiers statiques inclus avec l'application Rails
    -   [unicorn\_init.sh](https://gist.github.com/3272994) : script shell utilisé pour démarrer le processus du serveur Unicorn
    -   [unicorn.rb](https://gist.github.com/3273014) : fichier de configuration pour Unicorn

    Dans chaque fichier, remplacez le mot **blogger** par le nom d'utilisateur utilisé pour se connecter à votre machine virtuelle. Il s'agit de l'utilisateur qui sera utilisé pour déployer l'application.

5.  Dans le répertoire **blog\_app/config**, modifiez le fichier **deploy.rb** en remplaçant le contenu existant par le suivant :

        require "bundler/capistrano"

        set :application, "blog_app"
        set :user, "blogger"

        set :scm, :git
        set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
        set :branch, "master"
        set :use_sudo, true

        server "VMDNSName", :web, :app, :db, primary: true

        set :deploy_to, "/home/#{user}/apps/#{application}"
        default_run_options[:pty] = true
        ssh_options[:forward_agent] = true
        ssh_options[:port] = SSHPort

        namespace :deploy do
          desc "Fix permissions"
          task :fix_permissions, :roles => [ :app, :db, :web ] do
            run "chmod +x #{release_path}/config/unicorn_init.sh"
          end

          %w[start stop restart].each do |command|
            desc "#{command} unicorn server"
            task command, roles: :app, except: {no_release: true} do
              run "service unicorn_#{application} #{command}"
            end
          end

          task :setup_config, roles: :app do
            sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
            sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
            sudo "mkdir -p #{shared_path}/config"
          end
          after "deploy:setup", "deploy:setup_config"

          task :symlink_config, roles: :app do
            # Add database config here
          end
          after "deploy:finalize_update", "deploy:fix_permissions"
          after "deploy:finalize_update", "deploy:symlink_config"
        end

    Dans le texte ci-dessus, remplacez ce qui suit :

    -   **YourGitHubAccount** par le nom de votre compte GitHub
    -   **VMDNSName** par le DNS de votre machine virtuelle Azure
    -   **blogger** par le nom d'utilisateur utilisé pour la connexion à votre machine virtuelle
    -   **SSHPort** par le port SSH externe pour votre machine virtuelle

    **Remarque**

    Si votre environnement de développement est un système Windows, vous devez ajouter la ligne suivante au fichier **deploy.rb**. Elle doit être placée avec les autres instructions **set** au début du fichier :

    ``` {}
    set :bundle_flags, "--no-deployment --quiet"
    ```

    Il ne s'agit pas d'une bonne pratique, car les maillons doivent alors être chargés à partir du Gemfile lors du déploiement et non pas à partir du Gemfile.lock, mais cela est nécessaire puisque le système cible (Linux) est différent du système de développement (Windows).

6.  Annulez les marques de commentaire sur la ligne assets dans le fichier **Capfile** situé dans le répertoire **blog\_app**.

        load 'deploy/assets'

7.  Exécutez les commandes suivantes pour valider les modifications apportées au projet et les charger sur GitHub.

        git add .
        git commit -m "adding config files"
        git push

Déploiement
-----------

1.  Sur votre ordinateur de développement local, utilisez la commande suivante pour configurer la machine virtuelle Azure distante pour le déploiement.

        cap deploy:setup

    Lorsque vous y êtes invité, entrez le mot de passe du compte d'utilisateur de la machine virtuelle. Capistrano se connecte à la machine virtuelle et crée un répertoire **apps** situé sous le répertoire de base du compte d'utilisateur.

2.  À partir d'une connexion SSH à la machine virtuelle Azure, modifiez les autorisations du répertoire **app** à l'aide de la commande suivante :

        sudo chmod -R 777 apps

    **Remarque**

    Cette procédure est déconseillée pour les environnements de production et nous l'utilisons ici uniquement à des fins de démonstration.

3.  Effectuez un déploiement à froid à l'aide de la commande suivante sur votre environnement de développement. Cela déploie l'application sur la machine virtuelle et démarre le service Unicorn.

        cap deploy:cold

4.  Démarrez le service Nginx, qui doit commencer à router le trafic vers Unicorn et à servir le contenu statique :

        sudo service nginx start

À ce stade, votre application Ruby on Rails doit s'exécuter sur votre machine virtuelle Azure. Pour le vérifier, entrez le nom DNS de votre machine virtuelle dans votre navigateur Web. Par exemple, http://railsvm.cloudapp.net. L'écran « Welcome aboard » doit s'afficher :

![page welcome aboard](./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png)

Si vous ajoutez « /posts » à l'URL, l'index des publications doit s'afficher et vous devez pouvoir créer, modifier et supprimer des publications.

Étapes suivantes
----------------

Dans cet article, vous avez appris à créer et à publier une application Rails de base sur une machine virtuelle Azure à l'aide de Capistrano. La machine virtuelle utilise Unicorn et Nginx pour gérer les requêtes Web de l'application.

Pour un exemple plus basique de création et de déploiement d'une application Rails sur une machine virtuelle Azure à l'aide du protocole SSH uniquement, consultez la page [Hébergement d'une application Web Ruby on Rails à l'aide d'une machine virtuelle Linux](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/).

Si vous souhaitez en savoir plus sur Ruby on Rails, consultez les [guides Ruby on Rails](http://guides.rubyonrails.org/).

Pour apprendre à utiliser le Kit de développement logiciel (SDK) Azure pour Ruby afin d'accéder aux services Azure depuis votre application Ruby, consultez les pages suivantes :

-   [Stockage de données non structurées à l'aide d'objets blob](/en-us/develop/ruby/how-to-guides/blob-storage/)

-   [Stockage de paires clé/valeur à l'aide de tables](/en-us/develop/ruby/how-to-guides/table-service/)

-   [Prise en charge d'une large bande passante avec le réseau de distribution de contenu](/en-us/develop/ruby/app-services/)


