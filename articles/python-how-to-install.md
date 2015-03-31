<properties 
	pageTitle="Installation de Python et du Kit de développement logiciel (SDK) - Azure" 
	description="Découvrez comment installer Python et le Kit de développement logiciel (SDK) à utiliser avec Azure." 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>




# Installation de Python et du Kit de développement logiciel (SDK)

La configuration de Python sur Windows s'effectue en toute simplicité. La solution est préinstallée sur Mac et Linux.  Ce guide décrit les étapes d'installation et de préparation de votre système à l'utilisation d'Azure.  Il répond aux questions concernant les sujets suivants :

* Que contient le Kit de développement logiciel (SDK) Python Azure ?
* Quelle solution Python et quelle version utiliser ?
* Installation sur Windows
* Installation sur Mac et Linux

## Que contient le Kit de développement logiciel (SDK) Python Azure ?

Le Kit de développement logiciel (SDK) Azure pour Python contient les composants qui vous permettent de développer, déployer et gérer des applications Python pour Azure. Il inclut plus précisément les éléments suivants :

* **Bibliothèques clientes Python pour Azure**. Ces bibliothèques de classes fournissent une interface pour accéder aux fonctionnalités Azure, comme le stockage et le bus de service, la gestion des ressources Azure, comme les comptes de stockage, les machines virtuelles, etc.
* **Émulateurs Azure (Windows uniquement)**. Les émulateurs de calcul et de stockage sont des émulateurs locaux Cloud Services et de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure fonctionnent uniquement sur Windows.

## Quelle solution Python et quelle version utiliser ?

Il existe différentes versions des interpréteurs Python, par exemple :

* CPython : interpréteur Python standard le plus couramment utilisé
* IronPython : interpréteur Python qui s'exécute sur .Net/CLR
* Jython : interpréteur Python qui s'exécute sur JVM

Seul **CPython** est testé et pris en charge pour les services Azure et les services du Kit de développement logiciel (SDK) Python Azure tels que les sites web et les services cloud.  Nous recommandons la version 2.7 ou 3.4.

## Où télécharger Python ?

Il existe plusieurs façons de télécharger CPython :

* Directement depuis [www.python.org][]
* Auprès d'un distributeur fiable comme [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Génération à partir de la source !

Sauf en cas de nécessité particulière, nous vous recommandons d'opter pour l'une des deux premières options en suivant les indications ci-dessous.

## Installation sur Windows

Pour Windows, vous pouvez utiliser Web Platform Installer pour simplifier l'installation. Elle comprend CPython depuis [www.python.org][].

* [Kit de développement logiciel (SDK) Microsoft Azure pour Python 2.7][]
* [Kit de développement logiciel (SDK) Microsoft Azure pour Python 3.4][]

**Remarque :** pour télécharger le programme d'installation de WebPI sur Windows Server, vous devrez peut-être configurer les paramètres de configuration de sécurité renforcée (ESC) d'Internet Explorer (Démarrer/Outils d'administration/Gestionnaire de serveur, puis cliquez sur **Paramétrer la Configuration de sécurité renforcée d'Internet Explorer**, et désactivez l'option).


### Python 2.7

Le programme d'installation de WebPI fournit tout ce dont vous avez besoin pour développer des applications Python Azure.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Une fois l'installation terminée, cet écran s'affiche et confirme les options d'installation sélectionnées :

![how-to-install-python-webpi-27-2](./media/python-how-to-install/how-to-install-python-webpi-27-2.png)

Après la finalisation de l'installation, tapez  `python` à l'invite de commandes pour vérifier que tout s'est bien déroulé.  Selon la méthode d'installation choisie, il peut être nécessaire de définir la variable " chemin d'accès " pour trouver (la bonne version de) Python :

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Après l'installation, Python et les bibliothèques clientes sont disponibles à l'emplacement par défaut suivant :

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Le programme d'installation de WebPI fournit tout ce dont vous avez besoin pour développer des applications Python Azure.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)
 
Une fois l'installation terminée, cet écran s'affiche et confirme les options d'installation sélectionnées :

![how-to-install-python-webpi-34-2](./media/python-how-to-install/how-to-install-python-webpi-34-2.png)

Après la finalisation de l'installation, tapez python dans l'invite de commandes pour vérifier que tout s'est bien déroulé.  Selon la méthode d'installation choisie, il peut être nécessaire de définir la variable " chemin d'accès " pour trouver (la bonne version de) Python :

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Après l'installation, Python et les bibliothèques clientes sont disponibles à l'emplacement par défaut suivant :

		C:\Python34\Lib\site-packages\azure


### Téléchargement d'autres packages

[Python Package Index][] (PyPI) comporte une sélection étoffée de bibliothèques Python.  Si vous avez installé une version fournie par un distributeur, vous disposez déjà de la plupart des éléments nécessaires pour divers scénarios, du développement web au calcul technique.


### Python Tools for Visual Studio

[Python Tools for Visual Studio (PTVS)][] est un plug-in gratuit/OSS de Microsoft qui transforme Visual Studio en un environnement de développement intégré (IDE) Python à part entière :

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Nous vous recommandons d'utiliser PTVS même s'il est facultatif, car il vous apporte pour les projets/solutions Python et web les fonctionnalités suivantes : support technique, débogage, profilage, fenêtre interactive, modification des modèles et Intellisense.

PTVS simplifie aussi le déploiement sur Microsoft Azure, avec une prise en charge du déploiement sur les [services cloud][] et les [sites web][].

PTVS fonctionne avec votre installation existante de Visual Studio 2010, 2012 ou 2013.  Pour accéder à la documentation, aux téléchargements et aux discussions, consultez [Python Tools for Visual Studio sur CodePlex][].  

## Désinstallation sur Windows

Les produits WebPI **Azure SDK for Python** ne sont pas des applications au sens classique du terme, mais plutôt le regroupement d'un ensemble de produits distincts, comme Python 2.7/3.4 32 bits, les bibliothèques clientes Azure pour Python, etc.  Par conséquent, ce produit ne comporte aucun programme de désinstallation qui lui est propre et vous devez passer par le Panneau de configuration Windows pour supprimer les programmes installés un à un.  

Si vous souhaitez réinstaller **Azure SDK for Python**, ouvrez tout simplement une invite de commandes PowerShell en tant qu'administrateur et exécutez la commande suivante :

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

Ensuite, réexécutez WebPI.

## Installation sur Linux et MacOS

Python est sans doute déjà installé sur votre ordinateur de développement.  Pour vérifier, entrez la commande suivante :

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Nous pouvons observer que CPython 2.7.6 est installé sur cet ordinateur virtuel Ubuntu Server 14.04 LTS qui s'exécute sur Azure. Si vous devez procéder à une mise à niveau, suivez les instructions de mise à niveau du package recommandées pour votre système d'exploitation.

Pour installer les bibliothèques clientes Python pour Azure, utilisez **pip** et téléchargez-les depuis **PyPI** :

Si vous n'avez pas **pip** installé, utilisez cette commande pour l'installer :

	curl https://bootstrap.pypa.io/get-pip.py | sudo python
	
La commande ci-dessus lance une invite en mode silencieux vous demandant le mot de passe racine. Tapez-le et appuyez sur Entrée.  

Ensuite, utilisez la commande  `pip` pour installer les bibliothèques clientes Azure pour Python :
	
	sudo pip install azure

Les bibliothèques clientes installées doivent normalement s'afficher dans **site-packages**.  Sur MacOS :

![how-to-install-python-mac-site](./media/python-how-to-install/how-to-install-python-mac-site.png)

## Scénarios Python Azure pour Linux et MacOS

Pour Linux ou MacOS, les principaux scénarios Azure pris en charge sont les suivants :

1. Consommation des services Azure par le biais des bibliothèques clientes pour Python

2. Exécution de votre application sur une machine virtuelle Linux

3. Développement et publication de sites web Azure à l'aide de Git

Le premier scénario vous permet de créer des applications web enrichies qui tirent parti des fonctionnalités PaaS d'Azure comme le [stockage d'objets blob][], le [stockage de files d'attente][], le [stockage de tables][], etc. par le biais de wrappers Python pour les API REST d'Azure.  Le fonctionnement de ces derniers est identique sur Windows, Mac et Linux.  Vous pouvez également utiliser ces bibliothèques clientes à partir de votre ordinateur de développement local ou une machine virtuelle Linux s'exécutant sur Azure.

Pour le scénario nécessitant une machine virtuelle, vous pouvez tout simplement démarrer une machine virtuelle Linux de votre choix (Ubuntu, CentOS, Suse) et exécuter/gérer les logiciels souhaités.  Par exemple, vous pouvez exécuter [IPython][] REPL/Notebook sur votre machine Windows/Mac/Linux et pointer votre navigateur vers une machine virtuelle multiprocesseur Linux ou Windows exécutant le moteur IPython Engine sur Azure. Consultez le didacticiel [IPython Notebook sur Azure][] pour plus d'informations.

Pour plus d'informations sur la configuration d'une machine virtuelle Linux, consultez le didacticiel [Création d'une machine virtuelle exécutant Linux][].

À l'aide du déploiement Git, vous pouvez développer une application web Python et la publier sur un site web Azure à partir de n'importe quel système d'exploitation.  Quand vous placez votre référentiel sur Azure, il crée automatiquement un environnement virtuel et utilise pip pour installer vos packages requis.

Pour plus d'informations sur le développement et la publication de sites web Azure, consultez les didacticiels [Création de sites web avec Django][], [Création de sites web avec Bottle][] et [Création de sites web avec Flask][]. Pour plus d'informations sur l'utilisation de n'importe quelle infrastructure compatible WSGI, consultez [Configuration de Python avec des sites web Azure][].


## Logiciels et ressources supplémentaires :

* [Distribution de Python par Continuum Analytics][]
* [Distribution de Python par Enthought][]
* [Distribution de Python par ActiveState][]
* [SciPy : un vaste panel de bibliothèques Scientific Python][]
* [NumPy : une bibliothèque de valeurs numériques pour Python][]
* [Projet Django : une structure Web/un système de gestion du contenu arrivé à maturité][]
* [IPython : une solution REPL/Notebook avancée pour Python][]
* [IPython Notebook sur Azure][]
* [Python Tools for Visual Studio sur CodePlex][]


[Distribution de Python par Continuum Analytics]: http://continuum.io
[Distribution de Python par Enthought]: http://www.enthought.com
[Distribution de Python par ActiveState]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy : un vaste panel de bibliothèques Scientific Python]: http://www.scipy.org
[NumPy : une bibliothèque de valeurs numériques pour Python]: http://www.numpy.org
[Projet Django : une structure Web/un système de gestion du contenu arrivé à maturité]: http://www.djangoproject.com 
[IPython : une solution REPL/Notebook avancée pour Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook sur Azure]: ../virtual-machines-python-ipython-notebook/
[Services cloud]: ../cloud-services-python-ptvs/
[Sites Web]: ../web-sites-python-ptvs-django-mysql
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools for Visual Studio sur CodePlex]: http://pytools.codeplex.com 
[Python Package Index]: http://pypi.python.org/pypi
[Kit de développement logiciel (SDK) Microsoft Azure pour Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
[Kit de développement logiciel (SDK) Microsoft Azure pour Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990&clcid=0x409
[Configuration d'une machine virtuelle Linux via le portail Azure]: ../../../shared/chunks/create-and-configure-opensuse-vm-in-portal
[Utilisation des outils en ligne de commande Azure pour Mac et Linux]: ../../shared/chunks/crossplat-cmd-tools
[Création d'une machine virtuelle exécutant Linux]: ../virtual-machines-linux-tutorial/
[Création de sites web avec Django]: ../web-sites-python-create-deploy-django-app/
[Création de sites web avec Bottle]: ../web-sites-python-create-deploy-bottle-app/
[Création de sites web avec Flask]: ../web-sites-python-create-deploy-flask-app/
[Configuration de Python avec sites web Azure]: ../web-sites-python-configure/
[stockage de tables]: ../storage-python-how-to-use-table-storage/
[stockage de files d'attente]: ../storage-python-how-to-use-queue-storage/
[stockage d'objets blob]: ../storage-python-how-to-use-blob-storage/

<!--HONumber=47-->
