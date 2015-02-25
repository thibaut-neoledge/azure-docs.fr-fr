<properties 
	pageTitle="Sites Web Python avec Flask - Didacticiel Azure" 
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




# Création de sites Web avec Flask

Ce didacticiel décrit la prise en main de l'exécution de Python sur Sites Web Azure.  Sites Web Azure fournit un hébergement gratuit limité ainsi qu'un déploiement rapide. Vous pouvez maintenant également utiliser Python !  À mesure que votre application croît, vous pouvez passer à un hébergement payant, et vous pouvez également intégrer l'application à tous les autres services Azure.

Vous créerez une application à l'aide de l'infrastructure Web Flask (voir les autres versions de ce didacticiel pour [Django](../web-sites-python-create-deploy-django-app) et [Bottle](../web-sites-python-create-deploy-bottle-app)).  Vous créerez le site Web à partir de la galerie Azure, vous configurerez le déploiement Git et vous clonerez le référentiel localement.  Puis, vous exécuterez l'application localement, vous apporterez des modifications, et vous les validerez et les transmettrez par push vers Azure.  Ce didacticiel vous explique comment effectuer cette opération à partir de Windows ou Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


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

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

Une fenêtre répertoriant les applications disponibles dans la galerie apparaît. Cliquez sur la catégorie **INFRASTRUCTURES D'APPLICATIONS**située sur le côté gauche, et sélectionnez **Flask**.

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

Sur la page suivante, saisissez le nom et la région de votre site, puis cliquez sur le bouton Terminé.

La configuration du site s'effectue très rapidement. Cliquez sur le bouton **PARCOURIR** situé en bas de la barre d'outils pour découvrir votre nouvelle application Flask s'exécutant sur Azure.

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
Ensuite, vous allez ajouter la prise en charge de la publication via Git.  Pour ce faire, choisissez **Configurer le déploiement à partir du contrôle de code source**.

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

Dans la boîte de dialogue **Configurer le déploiement**, faites défiler vers le bas et sélectionnez l'option **Référentiel Git local**. Cliquez sur la flèche droite pour continuer.

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

Après avoir configuré la publication Git, une page s'affiche pendant un moment pour vous indiquer que le référentiel est en cours de création. Lorsqu'il est prêt, des instructions de connexion s'affichent.  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

Nous suivrons ces instructions dans les sections suivantes.


<h2><a name="application-overview"></a>Vue d'ensemble des applications</h2>

### Contenu du référentiel Git

Voici une vue d'ensemble des fichiers que vous trouverez dans le référentiel Git initial, que nous clonerons dans la section suivante.

    \FlaskWebProject\__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Principales sources de l'application.  3 pages (index, à propos de, contact) dotées d'une mise en page maître.  Parmi les contenus statiques et les scripts, on y trouve notamment bootstrap, jquery, modernizr et respond.

    \runserver.py

Prise en charge du serveur de développement local. Utilisez ceci pour exécuter localement l'application.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

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

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

Ouvrez le fichier solution (.sln) qui est inclus dans la racine du référentiel.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Créez un environnement virtuel

Nous allons maintenant créer un environnement virtuel pour le développement local.  Cliquez avec le bouton droit sur **Environnements Python** et sélectionnez **Ajouter environnement virtuel...**.

- Veillez à ce que l'environnement s'intitule bien `env`.

- Sélectionnez l'interpréteur de base.  Assurez-vous d'utiliser la même version de Python que celle qui est sélectionnée pour votre site (dans runtime.txt ou sur la page de configuration du site).

- Veillez à ce que l'option permettant de télécharger et d'installer des packages soit bien activée.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Cliquez sur **Créer**.  Ceci créera l'environnement virtuel et installera les dépendances répertoriées dans requirements.txt.

### Exécutez à l'aide du serveur de développement

Appuyez sur F5 pour lancer le débogage et votre navigateur Web ouvrira automatiquement la page s'exécutant en local.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Vous pouvez définir des points d'arrêt dans les codes sources, utiliser les fenêtres Espion, etc.  Pour plus d'informations sur les différentes fonctionnalités, consultez la [documentation de PTVS](http://pytools.codeplex.com/documentation).

### Apportez des modifications

Vous pouvez à présent tenter d'apporter des modifications aux codes sources de l'application et/ou des modèles.

Une fois ces modifications effectuées, validez-les sur le référentiel Git :

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Installez d'autres packages

Votre application peut comporter des dépendances au-delà de Python et Flask.

Vous pouvez installer des packages supplémentaires à l'aide de pip.  Pour installer un package, cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Installer le package Python**.

Par exemple, pour installer le Kit de développement logiciel (SDK) Azure pour Python, qui vous permet d'accéder au stockage Azure, au bus de services et à d'autres services Azure, entrez `azure` :

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Générer requirements.txt** pour mettre à jour requirements.txt.

Puis, validez les modifications apportées à requirements.txt au référentiel Git.

### Déployez sur Azure

Pour déclencher un déploiement, cliquez sur **Synchroniser** ou bien sur **Push**.  La synchronisation effectue à la fois une transmission par push et une extraction.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

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

### Exécutez à l'aide du serveur de développement

Vous pouvez lancer l'application sous un serveur de développement avec la commande suivante :

    env\scripts\python runserver.py

La console affichera l'URL et le port que le serveur écoute :

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Ouvrez ensuite votre navigateur Web à cette URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Apportez des modifications

Vous pouvez à présent tenter d'apporter des modifications aux codes sources de l'application et/ou des modèles.

Une fois ces modifications effectuées, validez-les sur le référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installez d'autres packages

Votre application peut comporter des dépendances au-delà de Python et Flask.

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

### Exécutez à l'aide du serveur de développement

Vous pouvez lancer l'application sous un serveur de développement avec la commande suivante :

    env/bin/python runserver.py

La console affichera l'URL et le port que le serveur écoute :

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Ouvrez ensuite votre navigateur Web à cette URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Apportez des modifications

Vous pouvez à présent tenter d'apporter des modifications aux codes sources de l'application et/ou des modèles.

Une fois ces modifications effectuées, validez-les sur le référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installez d'autres packages

Votre application peut comporter des dépendances au-delà de Python et Flask.

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


<h2><a name="next-steps"></a>Étapes suivantes</h2>

Suivez ces liens pour en savoir plus sur Flask et Python Tools pour Visual Studio : 
 
- [Documentation de Flask][]
- [Documentation de Python Tools pour Visual Studio][] 

Pour plus d'informations sur l'utilisation du stockage de tables Azure et MongoDB :

- [Flask et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio][]
- [Flask et le stockage de tables Azure sur Azure avec Python Tools 2.1 pour Visual Studio][]


<!--Link references-->
[Flask et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio]: ../web-sites-python-ptvs-flask-table-storage
[Flask et le stockage de tables Azure sur Azure avec Python Tools 2.1 pour Visual Studio]: ../web-sites-python-ptvs-flask-mongodb

<!--External Link references-->
[Documentation de Python Tools pour Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentation de Flask]: http://flask.pocoo.org/ 


<!--HONumber=42-->
