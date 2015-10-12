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
	ms.date="08/31/2015"
	ms.author="huvalo"/>

# Installation de Python et du Kit de développement logiciel (SDK)

La configuration de Python sur Windows s'effectue en toute simplicité. La solution est préinstallée sur Mac et Linux. Ce guide décrit les étapes d'installation et de préparation de votre système à l'utilisation d'Azure.

## Que contient le Kit de développement logiciel (SDK) Python Azure ?

Le Kit de développement logiciel (SDK) Azure pour Python contient les composants qui vous permettent de développer, déployer et gérer des applications Python pour Azure. Il inclut plus précisément les éléments suivants :

* **Bibliothèques clientes Python pour Azure**. Ces bibliothèques de classes fournissent une interface pour accéder aux fonctionnalités Azure, comme le stockage et le bus de service, la gestion des ressources Azure, comme les comptes de stockage, les machines virtuelles, etc.
* **Émulateurs Azure (Windows uniquement)**. Les émulateurs de calcul et de stockage sont des émulateurs locaux Cloud Services et de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure fonctionnent uniquement sur Windows.

## Quelle solution Python et quelle version utiliser ?

Il existe différentes versions des interpréteurs Python, par exemple :

* CPython : interpréteur Python standard le plus couramment utilisé
* IronPython : interpréteur Python qui s'exécute sur .Net/CLR
* Jython : interpréteur Python qui s'exécute sur JVM

Seul **CPython** est testé et pris en charge pour les services du Kit de développement logiciel (SDK) Python Azure et pour les services Azure tels que Sites Web et Cloud Services. Nous recommandons la version 2.7 ou 3.4.

## Où télécharger Python ?

Il existe plusieurs façons de télécharger CPython :

* Directement depuis [www.python.org][]
* Auprès d’un distributeur fiable comme [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Génération à partir de la source !

Sauf en cas de nécessité particulière, nous vous recommandons d'opter pour l'une des deux premières options en suivant les indications ci-dessous.

## Installation sur Windows, Linux et MacOS (bibliothèques client uniquement)

Si vous avez déjà installé Python, vous pouvez utiliser pip pour installer un lot de toutes les bibliothèques client dans votre environnement Python 2.7 ou Python 3.3+ existant. Cela télécharge les packages à partir du [Python Package Index][] (PyPI).

Notez que vous devrez peut-être utiliser la commande `sudo` sur Linux et MacOS, soit `sudo pip install azure`.

	pip install azure

À partir de la version 1.0.0, les bibliothèques ont été séparées en plusieurs packages. Vous pouvez désormais installer uniquement les packages dont vous avez besoin, ou le lot.

Pour installer les bibliothèques clientes Azure Storage Runtime :

	pip install azure-storage

Pour installer les bibliothèques clientes Azure Service Bus Runtime :

	pip install azure-servicebus

Pour installer les bibliothèques clientes Azure Resource Manager (ARM) :

	pip install azure-mgmt

Pour installer les bibliothèques clientes Azure Service Management (ASM) :

	pip install azure-servicemanagement-legacy


## Installation sur Windows (Python, émulateurs Azure et bibliothèques clientes)

Vous pouvez utiliser Web Platform Installer pour simplifier l'installation. Celle-ci comprend CPython, accessible sur [www.python.org][].

* [Kit de développement logiciel (SDK) Microsoft Azure pour Python 2.7][]
* [Kit de développement logiciel (SDK) Microsoft Azure pour Python 3.4][]

**Remarque** : pour télécharger le programme d’installation de WebPI sur Windows Server, vous devrez peut-être configurer les paramètres de configuration de sécurité renforcée (ESC) d’Internet Explorer (sélectionnez Démarrer/Outils d’administration/Gestionnaire de serveur/Serveur local, puis cliquez sur **Configuration de sécurité renforcée d’Internet Explorer** et désactivez cette option).

### Python 2,7

Le programme d'installation de WebPI fournit tout ce dont vous avez besoin pour développer des applications Python Azure.

![how-to-install-python-webpi-27-1](./media/python-how-to-install/how-to-install-python-webpi-27-1.png)

Une fois l’installation terminée, tapez `python` à l’invite pour vérifier que tout s’est bien déroulé. Selon la méthode d'installation choisie, il peut être nécessaire de définir la variable « chemin d'accès » pour trouver (la bonne version de) Python :

![how-to-install-python-win-run-27](./media/python-how-to-install/how-to-install-python-win-run-27.png)

Après l'installation, Python et les bibliothèques clientes sont disponibles à l'emplacement par défaut suivant :

		C:\Python27\Lib\site-packages\azure


### Python 3.4

Le programme d'installation de WebPI fournit tout ce dont vous avez besoin pour développer des applications Python Azure.

![how-to-install-python-webpi-34-1](./media/python-how-to-install/how-to-install-python-webpi-34-1.png)

Après la finalisation de l'installation, tapez python dans l'invite de commandes pour vérifier que tout s'est bien déroulé. Selon la méthode d'installation choisie, il peut être nécessaire de définir la variable « chemin d'accès » pour trouver (la bonne version de) Python :

![how-to-install-python-win-run-34](./media/python-how-to-install/how-to-install-python-win-run-34.png)

Après l'installation, Python et les bibliothèques clientes sont disponibles à l'emplacement par défaut suivant :

		C:\Python34\Lib\site-packages\azure

### Désinstallation sur Windows

Les produits WebPI **Azure SDK for Python** ne sont pas des applications au sens classique du terme, mais plutôt le regroupement d'un ensemble de produits distincts, comme Python 2.7/3.4 32 bits, les bibliothèques clientes Azure pour Python, etc. Par conséquent, ce produit ne comporte aucun programme de désinstallation qui lui est propre et vous devez passer par le Panneau de configuration Windows pour supprimer les programmes installés un à un.

Si vous souhaitez réinstaller **Azure SDK for Python**, ouvrez tout simplement une invite de commandes PowerShell en tant qu'administrateur et exécutez la commande suivante :

	rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

Ensuite, réexécutez WebPI.

## Téléchargement d'autres packages

[Python Package Index][] (PyPI) comporte une sélection étoffée de bibliothèques Python. Si vous avez installé une version fournie par un distributeur, vous disposez déjà de la plupart des éléments nécessaires pour divers scénarios, du développement web au calcul technique.


## Python Tools pour Visual Studio

[Python Tools for Visual Studio][] (PTVS) est un plug-in gratuit/OSS de Microsoft qui transforme Visual Studio en un environnement de développement intégré (IDE) Python à part entière :

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Nous vous recommandons d'utiliser PTVS même s'il est facultatif, car il vous apporte pour les projets/solutions Python et web les fonctionnalités suivantes : support technique, débogage, profilage, fenêtre interactive, modification des modèles et Intellisense.

PTVS simplifie également le déploiement sur Microsoft Azure, avec une prise en charge du déploiement sur [Cloud Services][] et [Sites Web][].

PTVS fonctionne avec votre installation existante de Visual Studio 2013 ou 2015. Pour accéder à la documentation, aux téléchargements et aux discussions, consultez le site [Python Tools pour Visual Studio].

## Scénarios Python Azure pour Linux et MacOS

Pour Linux ou MacOS, les principaux scénarios Azure pris en charge sont les suivants :

1. Consommation des services Azure par le biais des bibliothèques clientes pour Python

2. Exécution de votre application sur une machine virtuelle Linux

3. Développement et publication de sites web Azure à l'aide de Git

Le premier scénario vous permet de créer des applications web enrichies qui tirent parti des fonctionnalités PaaS d’Azure comme le [stockage d’objets blob][], le [stockage de files d’attente][], le [stockage de tables][], etc. par le biais de wrappers Python pour les API REST d’Azure. Le fonctionnement de ces derniers est identique sur Windows, Mac et Linux. Vous pouvez également utiliser ces bibliothèques clientes à partir de votre ordinateur de développement local ou une machine virtuelle Linux s'exécutant sur Azure.

Pour le scénario nécessitant une machine virtuelle, vous pouvez tout simplement démarrer une machine virtuelle Linux de votre choix (Ubuntu, CentOS, Suse) et exécuter/gérer les logiciels souhaités. Par exemple, vous pouvez exécuter [IPython][] REPL/Notebook sur votre machine Windows/Mac/Linux et pointer votre navigateur vers une machine virtuelle multiprocesseur Linux ou Windows exécutant le moteur IPython Engine sur Azure. Pour plus d’informations, voir le didacticiel [IPython Notebook sur Azure][].

Pour plus d’informations sur la configuration d’une machine virtuelle Linux, voir le didacticiel [Création d’une machine virtuelle exécutant Linux][].

À l'aide du déploiement Git, vous pouvez développer une application web Python et la publier sur un site web Azure à partir de n'importe quel système d'exploitation. Quand vous placez votre référentiel sur Azure, il crée automatiquement un environnement virtuel et utilise pip pour installer vos packages requis.

Pour plus d’informations sur le développement et la publication de sites web Azure, voir les didacticiels [Création de sites web avec Django][], [Création de sites web avec Bottle][] et [Création de sites web avec Flask][]. Pour des informations plus générales sur l’utilisation de n’importe quelle infrastructure compatible WSGI, voir [Configuration de Python avec Sites Web Azure][].


## Logiciels et ressources supplémentaires :

* [Distribution de Python par Continuum Analytics][] (en anglais)
* [Distribution de Python par Enthought][] (en anglais)
* [Distribution de Python par ActiveState][] (en anglais)
* [SciPy : un vaste panel de bibliothèques Scientific Python][] (en anglais)
* [NumPy : une bibliothèque de valeurs numériques pour Python][] (en anglais)
* [Projet Django : une infrastructure web/un système de gestion du contenu arrivés à maturité][] (en anglais)
* [IPython : une solution REPL/Notebook avancée pour Python][] (en anglais)
* [IPython Notebook sur Azure][]
* [Python Tools pour Visual Studio sur GitHub][]
* [Centre de développement Python](/develop/python/)

[Distribution de Python par Continuum Analytics]: http://continuum.io
[Distribution de Python par Enthought]: http://www.enthought.com
[Distribution de Python par ActiveState]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy : un vaste panel de bibliothèques Scientific Python]: http://www.scipy.org
[NumPy : une bibliothèque de valeurs numériques pour Python]: http://www.numpy.org
[Projet Django : une infrastructure web/un système de gestion du contenu arrivés à maturité]: http://www.djangoproject.com
[IPython : une solution REPL/Notebook avancée pour Python]: http://ipython.org
[IPython]: http://ipython.org
[IPython Notebook sur Azure]: virtual-machines-python-ipython-notebook.md
[Cloud Services]: cloud-services-python-ptvs.md
[Sites Web]: web-sites-python-ptvs-django-mysql.md
[Python Tools for Visual Studio]: http://aka.ms/ptvs
[Python Tools pour Visual Studio]: http://aka.ms/ptvs
[Python Tools pour Visual Studio sur GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Kit de développement logiciel (SDK) Microsoft Azure pour Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Kit de développement logiciel (SDK) Microsoft Azure pour Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Création d’une machine virtuelle exécutant Linux]: virtual-machines-linux-tutorial.md
[Création de sites web avec Django]: web-sites-python-create-deploy-django-app.md
[Création de sites web avec Bottle]: web-sites-python-create-deploy-bottle-app.md
[Création de sites web avec Flask]: web-sites-python-create-deploy-flask-app.md
[Configuration de Python avec Sites Web Azure]: web-sites-python-configure.md
[stockage de tables]: storage-python-how-to-use-table-storage.md
[stockage de files d’attente]: storage-python-how-to-use-queue-storage.md
[stockage d’objets blob]: storage-python-how-to-use-blob-storage.md

<!---HONumber=Oct15_HO1-->