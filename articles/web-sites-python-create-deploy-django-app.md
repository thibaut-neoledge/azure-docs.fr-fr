<properties 
	pageTitle="Sites Web Python avec Django - Didacticiel Azure" 
	description="Un didacticiel qui vous présente l'exécution d'un site Web Python sur Azure." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Création de sites Web avec Django

Ce didacticiel décrit la prise en main de l'exécution de Python sur Sites Web Azure.  Sites Web Azure fournit un hébergement gratuit limité ainsi qu'un déploiement rapide. Vous pouvez également utiliser Python !  À mesure que votre application croît, vous pouvez passer à un hébergement payant, et vous pouvez également intégrer l'application à tous les autres services Azure.

Vous créerez une application à l'aide de l'infrastructure Web Django (voir les autres versions de ce didacticiel pour [Flask](../web-sites-python-create-deploy-flask-app) et [Bottle](../web-sites-python-create-deploy-bottle-app)).  Vous créerez le site Web à partir de la galerie Azure, vous configurerez le déploiement Git et vous clonerez le référentiel localement.  Puis, vous exécuterez l'application localement, vous apporterez des modifications, et vous les validerez et les transmettrez par push vers Azure.  Ce didacticiel vous explique comment effectuer cette opération à partir de Windows ou Mac/Linux.

> [AZURE.NOTE]
> Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Vous pouvez <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">activer les avantages de votre abonnement MSDN</a> ou <a href="http://azure.microsoft.com/pricing/free-trial/">obtenir une version d'évaluation gratuite</a>.
> 
> Si vous voulez prendre en main Sites Web Azure avant de créer un compte, accédez à <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, où vous pouvez immédiatement et gratuitement créer un site de départ ASP.NET de courte durée dans Sites Web Azure. Aucun numéro de carte de crédit, ni engagement de quelque sorte n'est exigé.


+ [Configuration requise](#prerequisites)
+ [Création d'un site Web sur le portail](#website-creation-on-portal)
+ [Vue d'ensemble des applications](#application-overview)
+ Développement d'un site Web
  + [Windows - Python Tools pour Visual Studio](#website-development-windows-ptvs)
  + [Windows - Ligne de commande](#website-development-windows-command-line)
  + [Mac/Linux - Ligne de commande](#website-development-mac-linux-command-line)
+ [Dépannage - Déploiement](#troubleshooting-deployment)
+ [Dépannage - Installation des packages](#troubleshooting-package-installation)
+ [Dépannage - Environnement virtuel](#troubleshooting-virtual-environment)
+ [Dépannage - Fichiers statiques](#troubleshooting-static-files)
+ [Dépannage - Paramètres](#troubleshooting-settings)
+ [Utilisation d'une base de données](#using-a-database)
+ [Interface d'administration Django](#django-admin-interface)
+ [Étapes suivantes](#next-steps)


<h2><a name="prerequisites"></a>Configuration requise</h2>

- Windows, Mac ou Linux
- Python 2.7 ou 3.4
- setuptools, pip, virtualenv (Python 2.7 uniquement)
- Git
- Python Tools pour Visual Studio (facultatif)

**Remarque** : la publication TFS n'est actuellement pas prise en charge pour les projets Python.

### Windows

Si vous ne disposez pas de Python 2.7 ou 3.4 installé (32 bits), nous vous recommandons d'installer [Azure SDK pour Python 2.7](http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409) ou [Azure SDK pour Python 3.4](http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409) à l'aide de Web Platform Installer.  Ceci installe la version 32 bits de Python, setuptools, pip, virtualenv, etc. (la version 32 bits de Python est celle qui est installée sur les ordinateurs hôtes Azure).  Vous pouvez également obtenir une version de Python sur [python.org](http://www.python.org/).

Pour Git, nous vous recommandons la version [Git pour Windows](http://msysgit.github.io/). Ce didacticiel utilise Git Shell à partir de Git pour Windows.  Si vous utilisez Visual Studio, vous pouvez utiliser la prise en charge de Git intégré.

Nous vous recommandons également d'installer [Python Tools pour Visual Studio](http://pytools.codeplex.com).  Ceci est facultatif, mais si vous disposez de [Visual Studio](http://www.visualstudio.com/), y compris la version gratuite de Visual Studio Express 2013 pour le Web, cela vous permettra d'avoir accès à un très bon environnement de développement intégré (IDE) Python.

### Mac/Linux

Python et Git doivent déjà être installés sur votre ordinateur, mais assurez-vous que vous disposez bien de la version 2.7 ou 3.4 de Python.


<h2><a name="website-creation-on-portal"></a>Création d'un site Web sur le portail</h2>

La première étape à suivre pour créer votre application consiste à créer le site Web via le portail de gestion Azure.  Pour ce faire, vous devez vous connecter au portail et cliquer sur le bouton **NOUVEAU** dans le coin inférieur gauche. Une fenêtre s'affiche. Cliquez sur **CALCUL**, **SITE WEB**, puis sur **À PARTIR DE LA GALERIE**.

![](./media/web-sites-python-create-deploy-django-app/portal-create-site.png)

Une fenêtre répertoriant les applications disponibles dans la galerie apparaît. Cliquez sur la catégorie **INFRASTRUCTURES D'APPLICATIONS** située sur le côté gauche, et sélectionnez **Django**.

![](./media/web-sites-python-create-deploy-django-app/portal-gallery-django.png)

Sur la page suivante, saisissez le nom et la région de votre site, puis cliquez sur le bouton Terminé.

La configuration du site s'effectue très rapidement. Cliquez sur le bouton **PARCOURIR** situé en bas de la barre d'outils pour découvrir votre nouvelle application Django s'exécutant sur Azure.

![](./media/web-sites-python-create-deploy-django-app/portal-website-django.png)
 
Ensuite, vous allez ajouter la prise en charge de la publication via Git.  Pour ce faire, choisissez **Configurer le déploiement à partir du contrôle de code source**.

![](./media/web-sites-python-create-deploy-django-app/portal-site-created.png)

Dans la boîte de dialogue **Configurer le déploiement**, faites défiler vers le bas et sélectionnez l'option **Référentiel Git local**. Cliquez sur la flèche droite pour continuer.

![](./media/web-sites-python-create-deploy-django-app/portal-setup-deployment.png)

Après avoir configuré la publication Git, une page s'affiche pendant un moment pour vous indiquer que le référentiel est en cours de création. Lorsqu'il est prêt, des instructions de connexion s'affichent.  

![](./media/web-sites-python-create-deploy-django-app/portal-repo-created.png)

Nous suivrons ces instructions dans les sections suivantes.


<h2><a name="application-overview"></a>Vue d'ensemble des applications</h2>

### Contenu du référentiel Git

Voici une vue d'ensemble des fichiers que vous trouverez dans le référentiel Git initial, que nous clonerons dans la section suivante.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Principales sources de l'application.  3 pages (index, à propos de, contact) dotées d'une mise en page maître.  Parmi les contenus statiques et les scripts, on y trouve notamment bootstrap, jquery, modernizr et respond.

    \manage.py

Gestion et prise en charge locales du serveur de développement. Utilisez ceci pour exécuter l'application localement, synchroniser la base de données, etc.

    \db.sqlite3

Base de données par défaut. Inclut les tables nécessaires à l'exécution de l'application, mais ne contient pas tous les utilisateurs (synchronisez la base de données pour créer un utilisateur).

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Fichiers projet à utiliser avec [Python Tools pour Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Proxy IIS des environnements virtuels et prise en charge du débogage à distance des PTVS.

    \requirements.txt

Packages externes requis par cette application. Le script de déploiement installera, à l'aide de pip, les packages répertoriés dans ce fichier.
 
    \web.2.7.config
    \web.3.4.config

Fichiers de configuration IIS.  Le script de déploiement utilisera le fichier web.x.y.config approprié et le copiera en tant que fichier web.config.

### Fichiers facultatifs - Personnalisation du déploiement

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### Fichiers facultatifs - Exécution de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Fichiers supplémentaires sur le serveur

Certains fichiers existent sur le serveur, mais ne sont pas ajoutés au référentiel Git.  Ils sont créés via le script de déploiement.

    \web.config

Fichier de configuration IIS.  Créé à partir du fichier web.x.y.config lors de chaque déploiement.

    \env\

Environnement virtuel Python.  Créé lors du déploiement si un environnement virtuel compatible n'existe pas déjà sur le site.  Les packages répertoriés dans requirements.txt sont installés à l'aide de pip, mais celui-ci ignorera l'installation si les packages sont déjà installés.

Les trois sections suivantes vous expliquent comment poursuivre la création de sites Web sous 3 environnements différents :

- Windows, avec Python Tools pour Visual Studio
- Windows, avec la ligne de commande
- Mac/Linux, avec la ligne de commande


<h2><a name="website-development-windows-ptvs"></a>Développement de sites Web - Windows - Python Tools pour Visual Studio</h2>

### Clonez le référentiel

Clonez tout d'abord le référentiel à l'aide de l'URL fournie sur le portail Azure.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-clone.png)

Ouvrez le fichier solution (.sln) qui est inclus dans la racine du référentiel.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### Créez un environnement virtuel

Nous allons maintenant créer un environnement virtuel pour le développement local.  Cliquez avec le bouton droit sur **Environnements Python** et sélectionnez **Ajouter environnement virtuel...**.

- Veillez à ce que l'environnement s'intitule bien `env`.

- Sélectionnez l'interpréteur de base.  Assurez-vous d'utiliser la même version de Python que celle qui est sélectionnée pour votre site (dans runtime.txt ou sur la page de configuration du site).

- Veillez à ce que l'option permettant de télécharger et d'installer des packages soit bien activée.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Cliquez sur **Créer**.  Ceci créera l'environnement virtuel et installera les dépendances répertoriées dans requirements.txt.

### Créez un super-utilisateur

Aucun super-utilisateur n'a été défini dans la base de données de l'application.  Pour utiliser la fonctionnalité de connexion de l'application ou l'interface d'administration Django (si vous décidez de l'activer), vous devrez créer un super-utilisateur.

Exécutez-le depuis la ligne de commande du dossier de votre projet :

    env\scripts\python manage.py createsuperuser

Suivez les invites pour définir votre nom d'utilisateur, votre mot de passe, etc.

### Exécutez à l'aide du serveur de développement

Appuyez sur F5 pour lancer le débogage et votre navigateur Web ouvrira automatiquement la page s'exécutant en local.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Vous pouvez définir des points d'arrêt dans les codes sources, utiliser les fenêtres Espion, etc.  Pour plus d'informations sur les différentes fonctionnalités, consultez la [documentation de PTVS](http://pytools.codeplex.com/documentation).

### Apportez des modifications

Vous pouvez à présent tenter d'apporter des modifications aux codes sources de l'application et/ou des modèles.

Une fois ces modifications effectuées, validez-les sur le référentiel Git :

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### Installez d'autres packages

Votre application peut comporter des dépendances au-delà de Python et Django.

Vous pouvez installer des packages supplémentaires à l'aide de pip.  Pour installer un package, cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Installer le package Python**.

Par exemple, pour installer le Kit de développement logiciel (SDK) Azure pour Python, qui vous permet d'accéder au stockage Azure, au bus de services et à d'autres services Azure, entrez `azure` :

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Générer requirements.txt** pour mettre à jour requirements.txt.

Puis, validez les modifications apportées à requirements.txt au référentiel Git.

### Déployez sur Azure

Pour déclencher un déploiement, cliquez sur **Synchroniser** ou bien sur **Push**.  La synchronisation effectue à la fois une transmission par push et une extraction.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

Le premier déploiement mettra un certain temps à s'effectuer, car il créera un environnement virtuel, installera les packages d'installation, etc.

Visual Studio n'affiche pas la progression du déploiement.  Si vous souhaitez vérifier la sortie, consultez la section [Dépannage - Déploiement](#troubleshooting-deployment).

Accédez à l'URL d'Azure pour découvrir les modifications qui ont été apportées.


<h2><a name="website-development-windows-command-line"></a>Développement de sites Web - Windows - Ligne de commande</h2>

### Clonez le référentiel

Clonez tout d'abord le référentiel à l'aide de l'URL fournie sur le portail Azure et ajoutez l'espace de stockage Azure comme un référentiel distant.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Créez un environnement virtuel

Nous créerons un nouvel environnement virtuel pour le développement (ne l'ajoutez pas dans le référentiel).  Les environnements virtuels dans Python ne sont pas transférables. De ce fait, chaque développeur travaillant sur l'application créera localement ses propres applications.

Veillez à utiliser la même version de Python que celle qui est sélectionnée pour votre site (dans runtime.txt ou sur la page de configuration du site)

Pour Python 2.7 :

    c:\python27\python.exe -m virtualenv env

Pour Python 3.4 :

    c:\python34\python.exe -m venv env

Installez tous les packages externes requis par votre application. Vous pouvez utiliser le fichier requirements.txt à la racine du référentiel pour installer les packages de votre environnement virtuel :

    env\scripts\pip install -r requirements.txt

### Créez un super-utilisateur

Aucun super-utilisateur n'a été défini dans la base de données de l'application.  Pour utiliser la fonctionnalité de connexion de l'application ou l'interface d'administration Django (si vous décidez de l'activer), vous devrez créer un super-utilisateur.

Exécutez-le depuis la ligne de commande du dossier de votre projet :

    env\scripts\python manage.py createsuperuser

Suivez les invites pour définir votre nom d'utilisateur, votre mot de passe, etc.

### Exécutez à l'aide du serveur de développement

Vous pouvez lancer l'application sous un serveur de développement avec la commande suivante :

    env\scripts\python manage.py runserver

La console affichera l'URL et le port que le serveur écoute :

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Ouvrez ensuite votre navigateur Web à cette URL.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### Apportez des modifications

Vous pouvez à présent tenter d'apporter des modifications aux codes sources de l'application et/ou des modèles.

Une fois ces modifications effectuées, validez-les sur le référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installez d'autres packages

Votre application peut comporter des dépendances au-delà de Python et Django.

Vous pouvez installer des packages supplémentaires à l'aide de pip.  Par exemple, pour installer le Kit de développement logiciel (SDK) Azure pour Python, qui vous permet d'accéder au stockage Azure, au bus de services et à d'autres services Azure, entrez :

    env\scripts\pip install azure

Veillez à mettre à jour requirements.txt :

    env\scripts\pip freeze > requirements.txt

Validez les modifications :

    git add requirements.txt
    git commit -m "Added azure package"

### Déployez sur Azure

Pour déclencher un déploiement, transmettez par push les modifications vers Azure :

    git push azure master

Vous découvrirez le script de déploiement, y compris la création d'un environnement virtuel, l'installation des packages et la création du fichier web.config.

Accédez à l'URL d'Azure pour découvrir les modifications qui ont été apportées.


<h2><a name="website-development-mac-linux-command-line"></a>Développement de sites Web - Mac/Linux - Ligne de commande</h2>

### Clonez le référentiel

Clonez tout d'abord le référentiel à l'aide de l'URL fournie sur le portail Azure et ajoutez l'espace de stockage Azure comme un référentiel distant.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Créez un environnement virtuel

Nous créerons un nouvel environnement virtuel pour le développement (ne l'ajoutez pas dans le référentiel).  Les environnements virtuels dans Python ne sont pas transférables. De ce fait, chaque développeur travaillant sur l'application créera localement ses propres applications.

Assurez-vous d'utiliser la même version de Python que celle qui est sélectionnée pour votre site (dans runtime.txt ou sur la page de configuration du site).

Pour Python 2.7 :

    python -m virtualenv env

Pour Python 3.4 :

    python -m venv env

Installez tous les packages externes requis par votre application. Vous pouvez utiliser le fichier requirements.txt à la racine du référentiel pour installer les packages de votre environnement virtuel :

    env/bin/pip install -r requirements.txt

### Créez un super-utilisateur

Aucun super-utilisateur n'a été défini dans la base de données de l'application.  Pour utiliser la fonctionnalité de connexion de l'application ou l'interface d'administration Django (si vous décidez de l'activer), vous devrez créer un super-utilisateur.

Exécutez-le depuis la ligne de commande du dossier de votre projet :

    env/bin/python manage.py createsuperuser

Suivez les invites pour définir votre nom d'utilisateur, votre mot de passe, etc.

### Exécutez à l'aide du serveur de développement

Vous pouvez lancer l'application sous un serveur de développement avec la commande suivante :

    env/bin/python manage.py runserver

La console affichera l'URL et le port que le serveur écoute :

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Ouvrez ensuite votre navigateur Web à cette URL.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### Apportez des modifications

Vous pouvez à présent tenter d'apporter des modifications aux codes sources de l'application et/ou des modèles.

Une fois ces modifications effectuées, validez-les sur le référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installez d'autres packages

Votre application peut comporter des dépendances au-delà de Python et Django.

Vous pouvez installer des packages supplémentaires à l'aide de pip.  Par exemple, pour installer le Kit de développement logiciel (SDK) Azure pour Python, qui vous permet d'accéder au stockage Azure, au bus de services et à d'autres services Azure, entrez :

    env/bin/pip install azure

Veillez à mettre à jour requirements.txt :

    env/bin/pip freeze > requirements.txt

Validez les modifications :

    git add requirements.txt
    git commit -m "Added azure package"

### Déployez sur Azure

Pour déclencher un déploiement, transmettez par push les modifications vers Azure :

    git push azure master

Vous découvrirez le script de déploiement, y compris la création d'un environnement virtuel, l'installation des packages et la création du fichier web.config.

Accédez à l'URL d'Azure pour découvrir les modifications qui ont été apportées.


<h2><a name="troubleshooting-deployment"></a>Dépannage - Déploiement</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>Dépannage - Installation des packages</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>Dépannage - Environnement virtuel</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


<h2><a name="troubleshooting-static-files"></a>Dépannage - Fichiers statiques</h2>

Django se charge de récupérer les fichiers statiques.  Il récupère tous ces fichiers depuis leur emplacement d'origine et les copie dans un dossier unique.  Pour cette application, ils sont copiés vers `/static`.

Ils ont été définis de cette manière car ils peuvent provenir de différentes 'apps' Django.  Les fichiers statiques des interfaces d'administration Django sont, par exemple, situés dans un sous-dossier de la bibliothèque Django dans l'environnement virtuel.  Les fichiers statiques définis par cette application sont situés dans `/app/static`.  Plus vous utiliserez  'apps'Django, plus vous disposerez de fichiers statiques situés à des emplacements différents.

Lorsque l'application s'exécute en mode débogage, celle-ci traite les fichiers statiques depuis leur emplacement d'origine.

Alors qu'en mode publication, elle n'en utilise **aucun**.  Le serveur Web se charge de traiter ces fichiers.  Pour cette application, IIS traitera les fichiers statiques depuis `/static`.

Ces fichiers sont automatiquement récupérés dans le script de déploiement et suppriment ainsi les fichiers précédemment collectés.  Cette récupération est effectuée lors de chaque déploiement, ce qui le ralentit quelque peu, mais elle permet d'éliminer des fichiers obsolètes qui représentent un danger potentiel. 

Si vous ne souhaitez pas effectuer cette récupération de fichiers statiques pour votre application Django :

    \.skipDjango 

Vous devrez alors effectuer manuellement cette récupération sur votre ordinateur local :

    env\scripts\python manage.py collectstatic

Supprimez le dossier `\static` à partir de `.gitignore` et ajoutez-le au référentiel Git.


<h2><a name="troubleshooting-settings"></a>Dépannage - Paramètres</h2>

Différents paramètres d'application peuvent être modifiés dans `DjangoWebProject/settings.py`.

Pour simplifier le développement, le mode débogage est activé.  Ce mode vous permettra de visualiser les images et d'autres contenus statiques exécutés en local, sans avoir à récupérer de fichiers statiques.

Pour désactiver le mode de débogage :

    DEBUG = False

Lorsque le débogage est désactivé, la valeur `ALLOWED_HOSTS` doit être mise à jour pour y inclure le nom d'hôte Azure.  Par exemple :

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

ou en activer d'autres :

    ALLOWED_HOSTS = (
        '*',
    )
 
Dans la pratique, vous pouvez également opter pour une solution un peu plus complexe, qui vous permet de basculer entre le mode débogage et le mode publication, et d'obtenir un nom d'hôte.

Vous pouvez définir les variables d'environnement via la page **CONFIGURER** du portail Azure, dans la section **Paramètres de l'application**.  Ceci peut vous permettre de définir des valeurs que vous ne souhaitez pas voir apparaître dans les codes sources (chaînes de connexion, mots de passe, etc.), ou que vous souhaitez définir différemment sur Azure et votre ordinateur local.  Dans `settings.py`, vous pouvez interroger les variables d'environnement à l'aide de `os.getenv`.


<h2><a name="using-a-database"></a>Utilisation d'une base de données</h2>

La base de données de l'application est une base de données sqlite.  Cette base de données par défaut est très utile pour le développement, car elle n'a presque pas besoin d'être configurée.  Elle est stockée dans le fichier db.sqlite3 situé dans le dossier de votre projet.

Azure fournit des services de base de données qui sont faciles à utiliser à partir d'une application Django.  Des didacticiels vous expliquant comment utiliser [Base de données SQL](../web-sites-python-ptvs-django-sql) et [MySQL](../web-sites-python-ptvs-django-mysql) à partir d'une application Django, vous indiquent la procédure à suivre pour créer le service de base de données, modifier les paramètres de base de données dans `DjangoWebProject/settings.py`, ainsi que les différentes bibliothèques à installer.

Si vous préférez gérer vos propres serveurs de base de données, vous pouvez utiliser les machines virtuelles Windows ou Linux s'exécutant sur Azure.


<h2><a name="django-admin-interface"></a>Interface d'administration Django</h2>

En créant vos modèles, vous souhaiterez probablement remplir la base de données avec quelques données.  Pour ajouter et modifier très facilement du contenu de manière interactive, n'hésitez pas à utiliser l'interface d'administration de Django.

Le code de l'interface d'administration est mentionné dans les codes sources de l'application, mais il est clairement indiqué de manière à ce que vous puissiez facilement l'activer (rechercher 'admin').

Une fois activé, synchronisez la base de données, exécutez l'application et accédez à `/admin`.


<h2><a name="next-steps"></a>Étapes suivantes</h2>

Suivez ces liens pour en savoir plus sur Django et Python Tools pour Visual Studio : 
 
- [Documentation de Django][]
- [Documentation de Python Tools pour Visual Studio][] 

Pour plus d'informations sur l'utilisation de la base de données SQL et MySQL :

- [Django et la base de données SQL sur Azure avec Python Tools 2.1 pour Visual Studio][]
- [Django et MySQL sur Azure avec Python Tools 2.1 pour Visual Studio][]


<!--Link references-->
[Django et MySQL sur Azure avec Python Tools 2.1 pour Visual Studio]: ../web-sites-python-ptvs-django-mysql
[Django et la base de données SQL sur Azure avec Python Tools 2.1 pour Visual Studio]: ../web-sites-python-ptvs-django-sql

<!--External Link references-->
[Documentation de Python Tools pour Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentation de Django]: https://www.djangoproject.com/



<!--HONumber=42-->
