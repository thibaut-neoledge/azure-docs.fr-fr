<properties 
	pageTitle="Sites web Python avec Flask - Didacticiel Azure" 
	description="Un didacticiel qui vous présente l'exécution d'un site web Python sur Azure." 
	services="app-service\web" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Création de sites web avec Flask

Ce didacticiel décrit la prise en main de l'exécution de Python sur Sites Web Azure.  Sites Web Azure fournit un hébergement gratuit limité ainsi qu'un déploiement rapide. Vous pouvez également utiliser Python.  À mesure que votre application croît, vous pouvez passer à un hébergement payant, et vous pouvez également intégrer l'application à tous les autres services Azure.

Vous allez créer une application à l'aide de la structure web Flask (consulter les autres versions de ce logiciel pour [Django](web-sites-python-create-deploy-django-app.md) et [Bottle](web-sites-python-create-deploy-bottle-app.md)).  Vous allez créer le site web à partir de la galerie Azure, configurer le déploiement Git et cloner le référentiel localement.  Ensuite, vous exécuterez l'application localement, puis vous apporterez des modifications que vous validerez et transmettrez à Azure.  Ce didacticiel vous explique comment procéder depuis Windows ou Mac/Linux.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


## Conditions préalables

- Windows, Mac ou Linux
- Python 2.7 ou 3.4
- setuptools, pip, virtualenv (Python 2.7 uniquement)
- Git
- [Python Tools 2.1 pour Visual Studio][] (facultatif)

**Remarque** : la publication TFS n'est actuellement pas prise en charge pour les projets Python.

### Windows

Si vous n'avez pas encore installé Python 2.7 ou 3.4 (32 bits), nous vous recommandons d'installer le [Kit de développement logiciel (SDK) Azure pour Python 2.7][] ou [le Kit de développement logiciel (SDK) Azure pour Python 3.4][] à l'aide de Web Platform Installer.  Cette opération installe la version 32 bits de Python, setuptools, pip, virtualenv, etc. (Python 32 bits est installé sur les machines hôtes Azure).  Vous pouvez également vous procurer Python à partir du site [python.org][].

Pour Git, nous vous recommandons d'utiliser [Git pour Windows][] ou [GitHub pour Windows][].  Si vous travaillez avec Visual Studio, vous pouvez utiliser la prise en charge intégrée de Git.

Nous vous recommandons également d'installer [Python Tools 2.1 pour Visual Studio][].  Cette opération est facultative, mais si vous disposez de [Visual Studio][], y compris de la version gratuite de Visual Studio Community 2013 ou de Visual Studio Express 2013 pour le Web, vous bénéficierez d'un formidable environnement de développement intégré Python.

### Mac/Linux

Vous devez avoir installé Python et Git, mais vérifiez que vous disposez de Python 2.7 ou 3.4.


## Création d'un site Web sur le portail

La première étape à suivre pour créer votre application consiste à créer le site Web via le portail de gestion Azure.  Pour ce faire, vous devez vous connecter au portail et cliquer sur le bouton **NOUVEAU** dans le coin inférieur gauche. Une fenêtre s'affiche. Cliquez sur **CALCULER**, sur **SITE WEB**, puis sur **À PARTIR DE LA GALERIE**.

![](./media/web-sites-python-create-deploy-flask-app/portal-create-site.png)

Le portail affiche une fenêtre répertoriant les applications disponibles dans la galerie. Cliquez sur la catégorie **INFRASTRUCTURES D'APPLICATION**, présente sur la gauche, puis sélectionnez **Flask**.

![](./media/web-sites-python-create-deploy-flask-app/portal-gallery-flask.png)

Dans la page suivante, entrez un nom et une région pour votre site, puis cliquez sur le bouton Terminé.

Le site est configuré rapidement. Vous pouvez cliquer sur le bouton **PARCOURIR** de la barre d'outils inférieure pour découvrir votre nouvelle application Flask s'exécutant sur Azure.

![](./media/web-sites-python-create-deploy-flask-app/portal-website-flask.png)
 
Ensuite, vous allez ajouter la prise en charge de la publication via Git.  Pour ce faire, choisissez **Configurer le déploiement à partir du contrôle de code source**.

![](./media/web-sites-python-create-deploy-flask-app/portal-site-created.png)

Dans la boîte de dialogue **Configurer le déploiement**, faites défiler l'écran vers le bas et sélectionnez l'option **Référentiel Git local**. Cliquez sur la flèche droite pour continuer.

![](./media/web-sites-python-create-deploy-flask-app/portal-setup-deployment.png)

Après avoir configuré la publication Git, une page s'affiche momentanément pour vous indiquer que le référentiel est en cours de création. Une fois que le référentiel est prêt, des instructions de connexion apparaissent.  

![](./media/web-sites-python-create-deploy-flask-app/portal-repo-created.png)

Nous suivrons ces instructions dans les sections suivantes.


## Vue d'ensemble de l'application

### Contenu du référentiel Git

Voici une vue d'ensemble des fichiers que vous trouverez dans le référentiel Git initial, que nous allons cloner dans la section suivante.

    \FlaskWebProject\__init__.py
    \FlaskWebProject\views.py
    \FlaskWebProject\static\content\
    \FlaskWebProject\static\fonts\
    \FlaskWebProject\static\scripts\
    \FlaskWebProject\templates\about.html
    \FlaskWebProject\templates\contact.html
    \FlaskWebProject\templates\index.html
    \FlaskWebProject\templates\layout.html

Sources principales de l'application.  Correspondent à 3 pages (index, à propos de, contact) dotées d'une mise en page principale.  Le contenu statique et les scripts comprennent bootstrap, jquery, modernizr et respond.

    \runserver.py

Prise en charge du serveur de développement local. Utilisez cette option pour exécuter l'application localement.

    \FlaskWebProject.pyproj
    \FlaskWebProject.sln

Fichiers de projet à utiliser avec [Python Tools pour Visual Studio](http://pytools.codeplex.com).

    \ptvs_virtualenv_proxy.py

Proxy IIS pour les environnements virtuels et prise en charge du débogage à distance PTVS.

    \requirements.txt

Packages externes requis par cette application. Le script de déploiement installera à l'aide de pip les packages répertoriés dans ce fichier.
 
    \web.2.7.config
    \web.3.4.config

Fichiers de configuration IIS.  Le script de déploiement utilisera le fichier web.x.y.config approprié et le copiera sous le nom web.config.

### Fichiers facultatifs - Personnalisation du déploiement

[AZURE.INCLUDE [web-sites-python-customizing-deployment](../includes/web-sites-python-customizing-deployment.md)]

### Fichiers facultatifs - Runtime Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

### Fichiers supplémentaires sur le serveur

Certains fichiers existent sur le serveur, mais ne sont pas ajoutés au référentiel Git.  Ils sont créés par le script de déploiement.

    \web.config

Fichier de configuration IIS.  Créé à partir du fichier web.x.y.config lors de chaque déploiement.

    \env\

Environnement virtuel Python.  Créé lors du déploiement si un environnement virtuel compatible n'existe pas déjà sur le site.  Les packages répertoriés dans requirements.txt sont installés à l'aide de pip. Si les packages sont déjà installés, pip ignorera l'installation.

Les trois sections suivantes expliquent comment poursuivre le développement de sites web sous trois environnements différents :

- Windows, avec Python Tools pour Visual Studio ;
- Windows, avec la ligne de commande ;
- Mac/Linux, avec la ligne de commande.


## Développement de sites web - Windows - Python Tools pour Visual Studio

### Cloner le référentiel

Commencez par cloner le référentiel à l'aide de l'URL fournie sur le portail Azure.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-clone.png)

Ouvrez le fichier solution (.sln) inclus dans la racine du référentiel.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-solution-flask.png)

### Créer un environnement virtuel

Nous allons à présent créer un environnement virtuel pour le développement local.  Cliquez avec le bouton droit sur **Environnements Python** et sélectionnez **Ajouter un environnement virtuel...**.

- Vérifiez que l'environnement porte le nom `env`.

- Sélectionnez l'interpréteur de base.  Veillez à utiliser la même version de Python que celle sélectionnée pour votre site (dans runtime.txt ou sur la page de configuration du site).

- Vérifiez que l'option permettant de télécharger et d'installer les packages est cochée.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-add-virtual-env-27.png)

Cliquez sur **Créer**.  Cette opération créera l'environnement virtuel et installera les dépendances répertoriées dans requirements.txt.

### Exécuter à l'aide d'un serveur de développement

Appuyez sur F5 pour lancer le débogage. Votre navigateur ouvrira automatiquement la page s'exécutant localement.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

Vous pouvez définir des points d'arrêt dans les sources, utiliser les fenêtres Espion, etc. Pour plus d'informations sur les différentes fonctionnalités, voir la [documentation de PTVS][].

### Apporter des modifications

Vous pouvez à présent tenter d'apporter des modifications aux sources de l'application et/ou aux modèles.

Après avoir testé vos modifications, validez-les sur le référentiel Git :

![](./media/web-sites-python-create-deploy-flask-app/ptvs-commit-flask.png)

### Installer d'autres packages

Votre application peut comporter des dépendances au-delà de Python et de Flask.

Vous pouvez installer des packages supplémentaires à l'aide de pip.  Pour installer un package, cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Installer un package Python**.

Par exemple, pour installer le Kit de développement logiciel (SDK) Azure pour Python, qui vous permet d'accéder au stockage Azure, au bus des services et à d'autres services Azure, entrez `azure` :

![](./media/web-sites-python-create-deploy-flask-app/ptvs-install-package-dialog.png)

Cliquez avec le bouton droit sur l'environnement virtuel et sélectionnez **Générer requirements.txt** pour mettre à jour requirements.txt.

Puis validez les modifications apportées à requirements.txt dans le référentiel Git.

### Déployer dans Azure

Pour déclencher un déploiement, cliquez sur **Synchroniser** ou sur **Notifications push**.  La synchronisation effectue à la fois une transmission de type push et une transmission de type pull.

![](./media/web-sites-python-create-deploy-flask-app/ptvs-git-push.png)

Le premier déploiement mettra un certain temps à s'effectuer, car il créera un environnement virtuel, installera les packages, etc.

Visual Studio n'affiche pas la progression du déploiement.  Si vous souhaitez vérifier la sortie, consultez la section [Résolution des problèmes - Déploiement](#troubleshooting-deployment).

Accédez à l'URL Azure pour visualiser les modifications que vous avez apportées.


## Développement de sites web - Windows - Ligne de commande

### Cloner le référentiel

Commencez par cloner le référentiel à l'aide de l'URL fournie sur le portail Azure, puis ajoutez le référentiel Azure en tant que référentiel distant.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Créer un environnement virtuel

Nous allons créer un environnement virtuel pour le développement (ne l'ajoutez pas au référentiel).  Les environnements virtuels dans Python ne sont pas déplaçables. De ce fait, chaque développeur travaillant sur l'application créera ses propres environnements localement.

Veillez à utiliser la même version de Python que celle sélectionnée pour votre site (dans runtime.txt ou sur la page de configuration du site).

Pour Python 2.7 :

    c:\python27\python.exe -m virtualenv env

Pour Python 3.4 :

    c:\python34\python.exe -m venv env

Installez tous les packages externes requis par votre application. Vous pouvez utiliser le fichier requirements.txt à la racine du référentiel pour installer les packages dans votre environnement virtuel :

    env\scripts\pip install -r requirements.txt

### Exécuter à l'aide d'un serveur de développement

Vous pouvez lancer l'application sous un serveur de développement à l'aide de la commande suivante :

    env\scripts\python runserver.py

La console affichera l'URL et le port que le serveur écoute :

![](./media/web-sites-python-create-deploy-flask-app/windows-run-local-flask.png)

Puis ouvrez votre navigateur web en accédant à cette URL.

![](./media/web-sites-python-create-deploy-flask-app/windows-browser-flask.png)

### Apporter des modifications

Vous pouvez à présent tenter d'apporter des modifications aux sources de l'application et/ou aux modèles.

Après avoir testé vos modifications, validez-les sur le référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installer d'autres packages

Votre application peut comporter des dépendances au-delà de Python et de Flask.

Vous pouvez installer des packages supplémentaires à l'aide de pip.  Par exemple, pour installer le Kit de développement logiciel (SDK) Azure pour Python, qui vous permet d'accéder au stockage Azure, au bus des services et à d'autres services Azure, entrez :

    env\scripts\pip install azure

Veillez à mettre à jour requirements.txt :

    env\scripts\pip freeze > requirements.txt

Validez les modifications :

    git add requirements.txt
    git commit -m "Added azure package"

### Déployer dans Azure

Pour déclencher un déploiement, transmettez les modifications à Azure :

    git push azure master

Vous découvrirez la sortie du script de déploiement, notamment la création de l'environnement virtuel, l'installation des packages et la création du fichier web.config.

Accédez à l'URL Azure pour visualiser les modifications que vous avez apportées.


## Développement de sites web - Mac/Linux - Ligne de commande

### Cloner le référentiel

Commencez par cloner le référentiel à l'aide de l'URL fournie sur le portail Azure, puis ajoutez le référentiel Azure en tant que référentiel distant.

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url> 

### Créer un environnement virtuel

Nous allons créer un environnement virtuel pour le développement (ne l'ajoutez pas au référentiel).  Les environnements virtuels dans Python ne sont pas déplaçables. De ce fait, chaque développeur travaillant sur l'application créera ses propres environnements localement.

Veillez à utiliser la même version de Python que celle sélectionnée pour votre site (dans runtime.txt ou sur la page de configuration du site).

Pour Python 2.7 :

    python -m virtualenv env

Pour Python 3.4 :

    python -m venv env

Installez tous les packages externes requis par votre application. Vous pouvez utiliser le fichier requirements.txt à la racine du référentiel pour installer les packages dans votre environnement virtuel :

    env/bin/pip install -r requirements.txt

### Exécuter à l'aide d'un serveur de développement

Vous pouvez lancer l'application sous un serveur de développement à l'aide de la commande suivante :

    env/bin/python runserver.py

La console affichera l'URL et le port que le serveur écoute :

![](./media/web-sites-python-create-deploy-flask-app/mac-run-local-flask.png)

Puis ouvrez votre navigateur web en accédant à cette URL.

![](./media/web-sites-python-create-deploy-flask-app/mac-browser-flask.png)

### Apporter des modifications

Vous pouvez à présent tenter d'apporter des modifications aux sources de l'application et/ou aux modèles.

Après avoir testé vos modifications, validez-les sur le référentiel Git :

    git add <modified-file>
    git commit -m "<commit-comment>"

### Installer d'autres packages

Votre application peut comporter des dépendances au-delà de Python et de Flask.

Vous pouvez installer des packages supplémentaires à l'aide de pip.  Par exemple, pour installer le Kit de développement logiciel (SDK) Azure pour Python, qui vous permet d'accéder au stockage Azure, au bus des services et à d'autres services Azure, entrez :

    env/bin/pip install azure

Veillez à mettre à jour requirements.txt :

    env/bin/pip freeze > requirements.txt

Validez les modifications :

    git add requirements.txt
    git commit -m "Added azure package"

### Déployer dans Azure

Pour déclencher un déploiement, transmettez les modifications à Azure :

    git push azure master

Vous découvrirez la sortie du script de déploiement, notamment la création de l'environnement virtuel, l'installation des packages et la création du fichier web.config.

Accédez à l'URL Azure pour visualiser les modifications que vous avez apportées.


## Troubleshooting - Deployment

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


## Résolution des problèmes - Installation des packages

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


## Résolution des problèmes - Environnement virtuel

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]


## Étapes suivantes

Pour plus d'informations sur Flask et Python Tools pour Visual Studio, consultez les liens suivants : 
 
- [Documentation relative à Flask][]
- [Documentation de Python Tools pour Visual Studio][] 

Pour obtenir des informations concernant l'utilisation du stockage de tables Azure et MongoDB :

- [Flask et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio][]
- [Flask et stockage des tables Azure sur Azure avec Python Tools 2.1 for Visual Studio][]


<!--Link references-->
[Flask et MongoDB sur Azure avec Python Tools 2.1 pour Visual Studio]: web-sites-python-ptvs-flask-table-storage.md
[Flask et stockage des tables Azure sur Azure avec Python Tools 2.1 for Visual Studio]: web-sites-python-ptvs-flask-mongodb.md

<!--External Link references-->
[Kit de développement logiciel (SDK) Azure pour Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[le Kit de développement logiciel (SDK) Azure pour Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git pour Windows]: http://msysgit.github.io/
[GitHub pour Windows]: https://windows.github.com/
[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 pour Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Visual Studio]: http://www.visualstudio.com/
[Documentation de PTVS]: http://pytools.codeplex.com/documentation
[Documentation de Python Tools pour Visual Studio]: http://pytools.codeplex.com/documentation 
[Documentation relative à Flask]: http://flask.pocoo.org/ 

<!--HONumber=52-->