<properties 
	pageTitle="Déploiement d'une application web Ruby on Rails sur une machine virtuelle Azure en utilisant Capistrano - Didacticiel" 
	description="Apprenez à déployer une application Ruby on Rails sur une machine virtuelle Azure en utilisant Capistrano, Unicorn et Nginx." 
	authors="wadepickett" 
	manager="wadepickett" 
	editor="" 
	services="virtual-machines" 
	documentationCenter=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="web" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="wpickett"/>


#Déploiement d'une application Web Ruby on Rails sur une machine virtuelle Azure à l'aide de Capistrano

Ce didacticiel montre comment déployer un site web Ruby on Rails sur une machine virtuelle Azure à l'aide de [Capistrano 3](https://github.com/capistrano/capistrano/). Une fois le déploiement terminé, vous utiliserez [Nginx](http://nginx.org/) et [Unicorn](https://github.com/blog/517-unicorn) pour héberger le site web. [PostgreSQL](https://www.postgresql.org) stockera les données de l'application déployée.

Ce didacticiel part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure, mais que vous êtes rompu à l'utilisation de Ruby, Rails, Git et Linux. Une fois ce didacticiel terminé, vous aurez une application basée sur Ruby on Rails installée et fonctionnelle dans le cloud.

Vous apprendrez à :

* configurer vos environnements de développement et de production ;

* installer Ruby et Ruby on Rails ;

* installer Unicorn, Nginx et PostgreSQL ;

* Créer une application Rails

* créer un déploiement Capistrano et l'utiliser pour déployer l'application ;

La capture d'écran suivante présente l'application terminée :

![navigateur affichant Listing Posts][blog-rails-cloud]

> [AZURE.NOTE]L'application utilisée pour ce didacticiel inclut des composants binaires natifs. Des erreurs peuvent être générées lors du déploiement sur la machine virtuelle si votre environnement de développement n'est pas basé sur Linux. Le fichier Gemfile.lock utilisé en cours de déploiement contient des maillons spécifiques à la plateforme, lesquels ne comportent pas nécessairement d'entrées pour les versions Linux natives des maillons requis sur la machine virtuelle.
> 
> Des étapes spécifiques sont exigées avec un environnement de développement Windows. Toutefois, si vous êtes confronté, pendant ou après le déploiement, à des erreurs qui ne sont pas traitées dans cet article, vous pouvez reproduire les étapes de cet article dans un environnement de développement Linux.

##Dans cet article

* [Configurer votre environnement de développement](#setup)

* [Créer une application Rails](#create)

* [Test de l'application](#test)

* [Créer un référentiel source](#repository)

* [Créer une machine virtuelle Azure](#createvm)

* [Tester Nginx](#nginx)

* [Préparer le déploiement](#capify)

* [Déployer](#deploy)

* [Étapes suivantes](#next)

##<a id="setup"></a>Configurer votre environnement de développement

1. Installez Ruby dans votre environnement de développement. En fonction de votre système d'exploitation, les étapes peuvent varier.

	* **Apple OS X** - Il existe plusieurs distributions Ruby pour OS X. Ce didacticiel a été validé sur OS X en utilisant [Homebrew](http://brew.sh/) pour installer **rbenv**, **ruby-build** et **Ruby 2.0.0-p451**. Vous trouverez des informations d'installation à la page [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - Utilisez le système de gestion des packages de distributions. Ce didacticiel a été validé sur Ubuntu 12.10 à l'aide de **rbenv**, **ruby-build** et **Ruby 2.0.0-p451**.

	* **Windows** - Il existe plusieurs distributions Ruby pour Windows. Ce didacticiel a été validé en utilisant [RubyInstaller](http://RubyInstaller.org/) pour installer **Ruby 2.0.0-p451**. Les commandes ont été émises à l'aide de l'utilitaire de ligne de commande **GitBash** disponible avec [Git for Windows](http://git-scm.com/download/win).

2. Ouvrez une nouvelle ligne de commande ou session terminal et saisissez la commande suivante pour installer Ruby on Rails :

		gem install rails --no-rdoc --no-ri

	> [AZURE.NOTE]Cela peut nécessiter des privilèges administrateur ou racine sur certains systèmes d’exploitation. Si vous recevez une erreur lors de l'exécution de cette commande, essayez d'utiliser « sudo » comme suit :
	> 
	> `sudo gem install rails`

	> [AZURE.NOTE]La version 4.0.4 du maillon Rails a été utilisée pour ce didacticiel.

3. Vous devez également installer un interpréteur JavaScript, qui sera utilisé par Rails pour compiler des ressources CoffeeScript utilisées par votre application Rails. Une liste des interpréteurs pris en charge est disponible à la page [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	> [AZURE.NOTE] [Node.js](http://nodejs.org/) a été utilisé pour ce didacticiel, car il est disponible pour les systèmes d’exploitation OS X, Linux et Windows.

##<a id="create"></a>Créer une application Rails

1. Depuis la ligne de commande ou la session terminal, créez une application Rails nommée « blog_app » à l'aide la commande suivante :

		rails new blog_app

	Cette commande permet de créer un répertoire nommé **blog_app** et de le remplir avec les fichiers et sous-répertoires requis par une application Rails.

	> [AZURE.NOTE]Cette commande peut prendre une minute ou plus. Elle effectue une installation sans assistance des maillons requis pour une application par défaut. Elle ne répond pas au cours de cette période.

2. Remplacez les répertoires par le répertoire **blog_app**, puis utilisez la commande suivante pour créer une structuration de blog de base :

		rails generate scaffold Post name:string title:string content:text

	Cette commande créera les migrations de contrôleurs, de vues, de modèles et de bases de données utilisées pour contenir les publications sur le blog. Chaque publication comporte un nom d'auteur, un titre et un contenu textuel.

3. Pour créer la base de données qui stockera les publications de blog, utilisez la commande suivante :

		rake db:migrate

	Cela a pour effet de créer le schéma de base de données pour stocker des publications à l'aide du fournisseur de base de données par défaut pour Rails, à savoir la [base de données SQLite3][sqlite3].

4. Pour afficher un index des publications en tant que page d’accueil, modifiez le fichier **config/routes.rb** et ajoutez ce qui suit après la ligne `resources :posts`.

		root 'posts#index'

	Cela aura pour effet d'afficher une liste de publications lorsque des utilisateurs visiteront le site web.

##<a id="test"></a>Testez l’application

1. Remplacez les répertoires par le répertoire **blog_app** si vous ne vous y trouvez pas déjà, puis lancez le serveur Rails à l'aide de la commande suivante :

		rails s

	Le résultat ressemble à ce qui suit. Notez le port que le serveur Web écoute. Dans l'exemple ci-dessous, il s'agit du port 3000.

		=> Booting WEBrick
		=> Rails 4.0.4 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Ouvrez votre navigateur web et accédez à http://localhost:3000/. Une page semblable à celle ci-dessous doit s'afficher.

	![page listant les publications][blog-rails]

	Pour arrêter le processus de serveur, entrez CTRL+C dans la ligne de commande

##<a id="repository"></a>Créer un référentiel source

Lors du déploiement d'une application à l'aide de Capistrano, les fichiers sont extraits d'un référentiel. Dans le cadre de ce didacticiel, vous utiliserez [Git](http://git-scm.com/) pour le contrôle de version et [GitHub](https://github.com/) pour le répertoire.

1.	Créez un référentiel sur [GitHub](https://github.com/). Si vous ne possédez pas de compte GitHub, vous pouvez vous inscrire pour en obtenir un gratuitement. Les étapes suivantes partent du principe que le nom du référentiel est **blog_app**.

	> [AZURE.NOTE]Pour prendre en charge des déploiements automatisés de votre application, vous devez utiliser des clés SSH afin de vous authentifier auprès de GitHub. Pour plus d'informations à ce sujet, consultez la documentation GitHub traitant de la [génération des clés SSH](https://help.github.com/articles/generating-ssh-keys).

2.	À partir de l'invite de commandes, remplacez les répertoires par le répertoire **blog_app** et exécutez les commandes suivantes pour télécharger l'application sur votre référentiel GitHub. Remplacez **YourGitHubName** par le nom de votre compte GitHub.

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin git@github.com:YourGitHubName/blog-azure.git
		git push -u origin master

Dans la section suivante, vous créerez la machine virtuelle sur laquelle cette application sera déployée.

##<a id="createvm"></a>Créer une machine virtuelle Azure

Suivez les instructions fournies [ici][vm-instructions] afin de créer une machine virtuelle Azure qui héberge Linux.

1. Connectez-vous au [portail de gestion][management-portal] Azure. Dans la barre de commandes, sélectionnez **Nouveau**.

2. Sélectionnez **Machine virtuelle** et **À partir de la galerie**.

3. Dans **Choisir une image**, sélectionnez **Ubuntu**, puis sélectionnez la version **12.04 LTS**. Sélectionnez la flèche pour continuer.

	> [AZURE.NOTE]Les étapes de ce didacticiel ont été réalisées sur une machine virtuelle Azure hébergeant Ubuntu 12.04 LTS. Si vous utilisez une distribution Linux différente, des étapes différentes peuvent être requises pour accomplir les mêmes tâches.

4. Dans **Nom de la machine virtuelle**, tapez le nom que vous souhaitez utiliser. Ce nom sera utilisé pour créer le nom de domaine de cette machine virtuelle.

5. Dans **Nouveau nom d'utilisateur**, tapez le nom du compte administrateur de cette machine.

	> [AZURE.NOTE]Dans le cadre de ce didacticiel, le compte administrateur est également utilisé pour déployer l’application. Pour plus d'informations sur la création d'un compte distinct en vue du déploiement, consultez la documentation de [Capistrano][capistrano].

6. Sous **Authentification**, cochez **Télécharger une clé SSH compatible pour l'authentification**, puis recherchez et sélectionnez le fichier **.pem** contenant le certificat. Sélectionnez ensuite la flèche pour continuer.

	> [AZURE.NOTE]Si vous ne savez pas comment générer ou utiliser une clé SSH, consultez l’article [Utilisation de SSH avec Linux sur Azure][ssh-on-azure] pour plus d’informations à ce sujet.
	> 
	> Vous pouvez également activer l'authentification par mot de passe. Cependant, la clé SSH doit, elle aussi, être fournie, dans la mesure où elle est utilisée pour automatiser le déploiement.

7. Sous **Points de terminaison**, utilisez la liste déroulante **Entrez/choisissez une valeur** pour sélectionner **HTTP**. Vous pouvez laisser les valeurs par défaut dans les autres champs de cette page. Prenez note du **Nom du cloud Service DNS**, car cette valeur sera utilisée au cours des étapes suivantes. Sélectionnez ensuite la flèche pour continuer.

8. Sur la dernière page, activez la case à cocher pour créer la machine virtuelle.

###Installation de Git, Ruby et Nginx

Une fois la machine virtuelle créée, vous pouvez vous y connecter à l'aide de SSH et utiliser les commandes suivantes pour préparer l'environnement d'hébergement de votre application Ruby.

	sudo apt-get update
	sudo apt-get -y upgrade
	sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
	git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
	echo 'export RBENV_ROOT=~/.rbenv' > ~/.bash_profile
	echo 'export PATH="$RBENV_ROOT/bin:$PATH"' > ~/.bash_profile
	echo 'eval "$(rbenv init -)"' > ~/.bash_profile
	source ~/.bash_profile
	eval "$(rbenv init -)"
	git clone git://github.com/sstephenson/ruby-build.git
	cd ruby-build
	sudo ./install.sh
	~/.rbenv/bin/rbenv install 2.0.0-p451
	~/.rbenv/bin/rbenv global 2.0.0-p451
	gem install bundler
	~/.rbenv/bin/rbenv rehash

> [AZURE.NOTE]Vous pouvez enregistrer les lignes ci-dessus dans un script (fichier .sh) afin d’éviter les fautes de frappe lors de l’exécution des commandes.
> 
> L’exécution de la commande **~/.rbenv/bin/rbenv install 2.0.0-p451** peut prendre quelques minutes.

La commande ci-dessus effectue les opérations suivantes :
	
* Mise à jour et mise à niveau des packages installés
* Installation des outils de génération
* Installation de Git
* Installation de Node.js
* Installation de Nginx
* Installation d'outils de génération et d'autres utilitaires requis par Ruby et Rails
* Configuration d’un déploiement (utilisateur) local de [rbenv]
* Installation de Ruby 2.0.0-p451
* Installation du maillon [Bundler]

Une fois l'installation terminée, utilisez la commande suivante pour vérifier que Ruby a bien été installé :

	ruby -v

La version `ruby 2.0.0p451` doit normalement être renvoyée comme réponse.

###Installation de PostgreSQL

SQLite est la base de données par défaut utilisée par Rails à des fins de développement. En règle générale, vous utiliserez un élément différent dans le cadre de la production. Les étapes suivantes installent PostgreSQL sur la machine virtuelle, puis créent un utilisateur et une base de données. Les étapes ultérieures configurent l'application Rails en vue d'utiliser PostgreSQL en cours de déploiement.

1. Installez PostgreSQL et les éléments de développement à l'aide de la commande suivante.

		sudo apt-get -y install postgresql postgresql-contrib libpq-dev

6. Utilisez les commandes suivantes pour créer un utilisateur et une base de données pour votre nom de connexion. Remplacez **my_username** et **my_database** par votre nom de connexion. Par exemple, si le nom de connexion utilisé pour votre machine virtuelle est **deploy**, remplacez **my_username** et **my_username** par **deploy**.

		sudo -u postgres createuser -D -A -P my_username
		sudo -u postgres createdb -O my_username my_database

	> [AZURE.NOTE]Indiquez également le nom d’utilisateur comme nom de base de données. Cela est requis pour le maillon capistrano-postgresql utilisé par cette application.

	Lorsque vous y êtes invité, entrez le mot de passe de l'utilisateur. Lorsque vous êtes invité à autoriser l'utilisateur à créer des rôles, sélectionnez **y**, dans la mesure où cet utilisateur servira, au cours du déploiement, à créer la base de données et le nom de connexion utilisés par votre application Rails.

7. Pour vérifier qu'il est possible de se connecter à la base de données à l'aide du compte utilisateur, utilisez la commande suivante. Remplacez **my_username** et **my_database** par les valeurs utilisées précédemment.

		psql -U my_username -W my_database

	Vous devez normalement accéder à une invite `database=>`. Pour quitter l’utilitaire psql, entrez `\q` à l’invite de commandes.

###<a id="nginx"></a>Tester Nginx

Le point de terminaison HTTP ajouté lors de la création de la machine virtuelle lui permettra d’accepter les demandes HTTP sur le port 80. Procédez comme suit pour vérifier que le site par défaut créé par Nginx est accessible.

2.	À partir de la session SSH de la machine virtuelle, démarrez le service Nginx :

		sudo service nginx start

	Cela démarre le service Nginx qui écoute le trafic entrant sur le port 80.

6. Testez votre application en accédant au nom DNS de votre machine virtuelle. Le site web qui apparaît doit être semblable à l'illustration ci-dessous :

	![page d'accueil nginx][nginx-welcome]

	> [AZURE.NOTE]Avec les scripts de déploiement utilisés ultérieurement dans ce didacticiel, le site Web par défaut blog_app sera servi par Nginx.

À ce stade, vous disposez d'une machine virtuelle Azure avec Ruby, Nginx et PostgreSQL qui est prête pour le déploiement. Dans la section suivante, vous modifierez votre application Rails afin d'ajouter les scripts et informations qui exécutent le déploiement.

##<a id="capify"></a>Préparer le déploiement

Modifiez l'application dans votre environnement de développement afin d'utiliser le serveur web Unicorn, PostgreSQL, et d'activer Capistrano pour le déploiement, et créez les scripts utilisés pour déployer et démarrer l'application.

1. Sur votre ordinateur de développement, modifiez le fichier **Gemfile** et ajoutez les lignes suivantes afin d'ajouter des maillons pour Unicorn, PostgreSQL, Capistrano et des maillons connexes à votre application Rails.

		# Use Unicorn
		gem 'unicorn'
		# Use PostgreSQL
		gem 'pg', group: :production

		group :development do
		  # Use Capistrano for deployment
		  gem 'capistrano', '~> 3.1'
		  gem 'capistrano-rails', '~> 1.1.1'
		  gem 'capistrano-bundler'
		  gem 'capistrano-rbenv', '~> 2.0'
		  gem 'capistrano-unicorn-nginx', '~> 2.0'
		  gem 'capistrano-postgresql', '~> 3.0'
		end

	> [AZURE.NOTE]Unicorn n'est pas disponible sur Windows. Si vous utilisez Windows comme environnement de développement, modifiez __Gemfile__ pour vous assurer qu'il tente uniquement d'installer Unicorn lors du déploiement sur la machine virtuelle en utilisant la ligne suivante lors de l'indication du maillon unicorn :
	> 
	> `platforms :ruby do` `  gem 'unicorn'` `end`

	La plupart des maillons capistrano-* sont des programmes d'assistance qui fonctionnent avec des éléments spécifiques que nous utilisons sur le serveur de production (rbenv) ou l'infrastructure (rails).

	Le maillon capistrano-unicorn-nginx crée automatiquement des scripts utilisés avec Unicorn et Nginx lors du déploiement, de sorte que nous ne devions pas les créer manuellement. Le maillon capistrano-postgresql génère automatiquement une base de données, un utilisateur et un mot de passe dans PostgreSQL pour votre application. Bien que vous ne deviez pas les utiliser, ils simplifient considérablement le processus de déploiement.
 
5.	Utilisez les commandes suivantes pour installer les nouveaux maillons.
		
		bundle

6. Utilisez la commande suivante pour créer des fichiers de déploiement par défaut utilisés par Capistrano.

		cap install

	Une fois la commande `cap install` exécutée, les fichiers et répertoires ci-après sont ajoutés à votre application.

		├── Capfile
		├── config
		│   ├── deploy
		│   │   ├── production.rb
		│   │   └── staging.rb
		│   └── deploy.rb
		└── lib
		    └── capistrano
		            └── tasks

	Le fichier **deploy.rb** fournit une configuration et des actions génériques pour tous les types de déploiements, tandis que **production.rb** et **staging.rb** contiennent une configuration supplémentaire pour les déploiements intermédiaires et de production.

	Le dossier **capistrano** contient des tâches et d'autres fichiers utilisés dans le cadre du processus de déploiement.

5. Modifiez le fichier **Capfile** à la racine de votre application et annulez les marques de commentaire dans les lignes suivantes en supprimant le caractère __#__ initial.

		require 'capistrano/rbenv'
		require 'capistrano/bundler'
		require 'capistrano/rails/assets'
		require 'capistrano/rails/migrations'

	Après avoir annulé les marques de commentaire dans les lignes précédentes, ajoutez les lignes suivantes.

		require 'capistrano/unicorn_nginx'
		require 'capistrano/postgresql'

	Ces lignes indiquent à Capistrano d'utiliser les maillons qui ont été ajoutés précédemment au fichier Gemfile.

	Après avoir effectué les modifications ci-dessus, enregistrez le fichier.

6.  Modifiez le fichier **config/deploy.rb** et remplacez son contenu par les lignes suivantes. Remplacez **YourApplicationName** par le nom de votre application, et la chaîne **https://github.com/YourGitHubName/YourRepoName.git** par l’URL du référentiel GitHub de ce projet.

		lock '3.1.0'
		# application name and the github repository
		set :application, 'YourApplicationName'
		set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'
		
		# describe the rbenv environment we are deploying into
		set :rbenv_type, :user
		set :rbenv_ruby, '2.0.0-p451'
		set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
		set :rbenv_map_bins, %w{rake gem bundle ruby rails}
		
		# dirs and files we want symlinked to the shared folder
		# during deployment
		set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}
		set :linked_files, %w{config/database.yml config/unicorn.rb}
		
		namespace :deploy do

		  desc 'Restart application'
		  task :restart do
		    on roles(:app), in: :sequence, wait: 5 do
		      # Your restart mechanism here, for example:
		      # execute :touch, release_path.join('tmp/restart.txt')
              #
			  # The capistrano-unicorn-nginx gem handles all this
			  # for this example
		    end
		  end
		
		  after :publishing, :restart
		
		  after :restart, :clear_cache do
		    on roles(:web), in: :groups, limit: 3, wait: 10 do
		      # Here we can do anything such as:
		      # within release_path do
		      #   execute :rake, 'cache:clear'
		      # end
		    end
		  end
		
		end

	Ce fichier fournit des informations et des tâches génériques communes à tous les types de déploiement.

5.  Modifiez le fichier **config/deploy/production.rb** et remplacez son contenu par les lignes suivantes. Remplacez **YourVm.cloudapp.net** par le nom de domaine de votre machine virtuelle. Remplacez **YourAzureVMUserName** par le compte de connexion créé précédemment pour votre machine virtuelle Azure.

		# production deployment
		set :stage, :production
		# use the master branch of the repository
		set :branch, "master"
		# the user login on the remote server
		# used to connect and deploy
		set :deploy_user, "YourAzureVMUserName"
		# the 'full name' of the application
		set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
		# the server(s) to deploy to
		server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
		# the path to deploy to
		set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
		set :rails_env, :production

	Ce fichier fournit des informations propres aux déploiements de production.

8.	Exécutez les commandes suivantes pour valider les modifications apportées aux fichiers lors des étapes précédentes, puis téléchargez les changements vers GitHub.

		git add .
		git commit -m "adding config files"
		git push

En principe, l'application doit maintenant être prête pour le déploiement.

> [AZURE.NOTE]Pour une application plus complexe, ou pour un autre serveur d’applications ou de base de données, il se peut que vous ayez besoin de scripts de déploiement ou de configuration supplémentaires.

##<a id="deploy"></a>Déployer

2.	Sur votre ordinateur de développement local, utilisez la commande suivante pour déployer les fichiers de configuration utilisés par l'application sur la machine virtuelle.

		bundle exec cap production setup

	Capistrano va se connecter à la machine virtuelle à l'aide de SSH, puis créer le répertoire (~/apps) sur lequel l'application sera déployée. S'il s'agit du premier déploiement, le maillon capistrano-postgresql créera également un rôle et une base de données dans PostgreSQL sur le serveur. Il créera également un fichier de configuration database.yml que Rails utilisera pour se connecter à la base de données.

	> [AZURE.NOTE]Si vous recevez le message **Erreur lors de la lecture de la longueur de réponse du socket d’authentification** lors du déploiement, vous devrez peut-être démarrer l’agent SSH sur votre environnement de développement à l’aide de la commande `ssh-agent`, par exemple en ajoutant `eval $(ssh-agent)` à votre fichier ~/.bash_profile.
	> 
	> Vous pouvez également avoir besoin d’ajouter la clé SSH au cache de l’agent à l’aide de la commande `ssh-add`.

4.	Effectuez un déploiement de production à l'aide de la commande suivante. Cela aura pour effet de déployer l'application sur la machine virtuelle, de démarrer le service Unicorn et de configurer Nginx de manière à acheminer le trafic vers Unicorn.

		bundle exec cap production deploy

	Cette commande déploie l'application sur la machine virtuelle, installe les maillons requis, puis démarre/redémarre Unicorn et Nginx.

	> [AZURE.NOTE]Il se peut que la procédure soit interrompue pendant plusieurs minutes lors du traitement.

	> [AZURE.NOTE]Certaines parties du déploiement peuvent renvoyer le message « Statut de sortie : 1 (échec) ». En règle générale, vous pouvez l'ignorer tant que le déploiement se déroule correctement.

	> [AZURE.NOTE]Sur certains systèmes, il peut arriver que l’agent SSH ne puisse pas transférer des informations d’identification à la machine virtuelle distante lors de l’authentification auprès de GitHub. Si tel est le cas, vous pouvez contourner l’erreur en modifiant le fichier **config/deploy.rb** et modifier la ligne `set :repo_url` afin d’utiliser HTTPS lors de l’accès à Github. Lors de l'utilisation du protocole HTTPS, vous devez spécifier votre nom d'utilisateur et votre mot de passe GitHub (ou votre jeton d'authentification) dans l'URL. Par exemple :
	> 
	> `set :repo_url, ’https://you:yourpassword@github.com/You/yourrepository.git’
	> 
	> Cela devrait normalement vous permettre de contourner l'erreur et de terminer ce didacticiel. Cette solution est toutefois déconseillée pour un déploiement de production, dans la mesure où elle stocke vos informations d'identification d'authentification en texte brut dans l'application. Il est conseillé de consulter la documentation de votre système d'exploitation traitant du transfert avec l'agent SSH.

À ce stade, votre application Ruby on Rails doit s'exécuter sur votre machine virtuelle Azure. Pour le vérifier, entrez le nom DNS de votre machine virtuelle dans votre navigateur Web. Par exemple, http://railsvm.cloudapp.net. L’index des publications doit s’afficher et vous devez être en mesure de créer, de modifier et de supprimer des publications.

##<a id="next"></a>Étapes suivantes

Dans cet article, vous avez appris à créer et à publier une application Rails de base sur une machine virtuelle Azure à l'aide de Capistrano. L'utilisation d'une application de base, telle que celle décrite dans cet article, n'est que la partie visible de ce qu'il est possible d'effectuer à l'aide de Capistrano dans le cadre du déploiement. Pour plus d'informations sur l'utilisation de Capistrano, consultez :

* [Capistranorb.com](http://capistranorb.com) : site web de Capistrano.
* [Azure, Ruby on Rails, Capistrano 3, & PostgreSQL](http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql) : autre méthode de déploiement sur Azure qui implique des scripts de déploiement personnalisés.
* [Didacticiel Capistrano 3](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/) : didacticiel traitant de l'utilisation de Capistrano 3.

Pour un exemple plus basique de création et de déploiement d'une application Rails sur une machine virtuelle Azure à l'aide du protocole SSH uniquement, consultez la page [Hébergement d'une application Web Ruby on Rails à l'aide d'une machine virtuelle Linux][ruby-vm].

Si vous souhaitez en savoir plus sur Ruby on Rails, consultez les [guides Ruby on Rails][rails-guides].

Pour apprendre à utiliser le Kit de développement logiciel (SDK) Azure pour Ruby afin d'accéder aux services Azure depuis votre application Ruby, consultez les pages suivantes :

* [Utilisation du stockage d’objets blob à partir de Ruby][blobs]

* [Utilisation du stockage de tables à partir de Ruby][tables]

* [Distribution de contenu à bande passante élevée avec le CDN][cdn-howto]

[vm-instructions]: /manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /develop/ruby/how-to-guides/blob-storage/
[tables]: /develop/ruby/how-to-guides/table-service/
[cdn-howto]: /develop/ruby/app-services/
[ruby-vm]: /develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/
[ssh-on-azure]: http://azure.microsoft.com/documentation/articles/linux-use-ssh-key/
[capistrano]: http://capistranorb.com

<!---HONumber=58-->