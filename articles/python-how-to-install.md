<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Installation de Python et du Kit de développement logiciel (SDK)

La configuration de Python sur Windows s'effectue en toute simplicité. La solution est préinstallée sur Mac et Linux. Ce guide décrit les étapes d'installation et de préparation de votre système à l'utilisation d'Azure. Il répond aux questions concernant les sujets suivants :

-   Que contient le Kit de développement logiciel (SDK) Python Azure ?
-   Quelle solution Python et quelle version utiliser ?
-   Installation sur Windows
-   Installation sur Mac et Linux

## Que contient le Kit de développement logiciel (SDK) Python Azure ?

Le Kit de développement logiciel (SDK) Azure pour Python contient les composants qui vous permettent de développer, déployer et gérer des applications Python pour Azure. Il inclut plus précisément les éléments suivants :

-   **Bibliothèques clientes Python pour Azure**. L'interface de ces bibliothèques de classes permet d'accéder aux fonctionnalités Azure, telles que les services de gestion des données et les services cloud.
-   **Émulateurs Azure (Windows uniquement)**. Les émulateurs de calcul et de stockage sont des émulateurs locaux Cloud Services et de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure fonctionnent uniquement sur Windows.

Les principaux scénarios pour cette version sont les suivants :

-   **Windows** : service cloud, site web, par exemple un site Django utilisant des rôles web
-   **Mac/Linux** : IaaS. Exécutez les logiciels de votre choix dans une machine virtuelle ; consommez les services Azure par le biais de Python

## Quelle solution Python et quelle version utiliser ?

Il existe différentes versions des interpréteurs Python, par exemple :

-   CPython : interpréteur Python standard le plus couramment utilisé
-   IronPython : interpréteur Python qui s'exécute sur .Net/CLR
-   Jython : interpréteur Python qui s'exécute sur JVM

Dans le cadre de cette version, l'interpréteur **CPython** est le seul à avoir été testé et à être pris en charge. Nous recommandons la version 2.7 ou 3.4.

## Où télécharger Python ?

Il existe plusieurs façons de télécharger CPython :

-   Directement sur www.python.org
-   Auprès d'un distributeur fiable comme www.enthought.com, www.ActiveState.com, www.continuum.io
-   Génération à partir de la source !

Sauf en cas de nécessité particulière, nous vous recommandons d'opter pour l'une des deux premières options en suivant les indications ci-dessous.

## Installation sur Windows

Pour Windows, vous pouvez utiliser le programme d'installation [Web Platform Installer (WebPI)][] fourni depuis le Centre de développement Python principal afin de faciliter l'installation (téléchargement du logiciel CPython depuis www.python.org).

**Remarque :** pour télécharger le programme d'installation de WebPI sur Windows Server, vous devrez peut-être configurer les paramètres de configuration de sécurité renforcée (ESC) d'Internet Explorer (Démarrer/Outils d'administration/Gestionnaire de serveur, puis cliquez sur **Paramétrer la Configuration de sécurité renforcée d’Internet Explorer** et désactivez l'option).

Vous pouvez choisir d'installer Python 2.7 et/ou Python 3.4 à l'aide de WebPI.

### Python 2.7

Le programme d'installation de WebPI fournit tout ce dont vous avez besoin pour développer des applications Python Azure.

![how-to-install-python-webpi-27-1][]

Une fois l'installation terminée, cet écran s'affiche et confirme les options d'installation sélectionnées :

![how-to-install-python-webpi-27-2][]

Après la finalisation de l'installation, tapez python dans l'invite de commandes pour vérifier que tout s'est bien déroulé. Selon la méthode d'installation choisie, il peut être nécessaire de définir la variable « chemin d'accès » pour trouver (la bonne version de) Python :

![how-to-install-python-win-run-27][]

Après l'installation, Python et les bibliothèques clientes sont disponibles à l'emplacement par défaut suivant :

        C:\Python27\Lib\site-packages\azure

### Python 3.4

Le programme d'installation de WebPI fournit tout ce dont vous avez besoin pour développer des applications Python Azure.

![how-to-install-python-webpi-34-1][]

Une fois l'installation terminée, cet écran s'affiche et confirme les options d'installation sélectionnées :

![how-to-install-python-webpi-34-2][]

Après la finalisation de l'installation, tapez python dans l'invite de commandes pour vérifier que tout s'est bien déroulé. Selon la méthode d'installation choisie, il peut être nécessaire de définir la variable « chemin d'accès » pour trouver (la bonne version de) Python :

![how-to-install-python-win-run-34][]

Après l'installation, Python et les bibliothèques clientes sont disponibles à l'emplacement par défaut suivant :

        C:\Python34\Lib\site-packages\azure

### Téléchargement d'autres packages

Cette version est principalement axée sur les applications web avec Django, mais nous vous invitons à parcourir la page [Python Package Index (PyPI)][] qui présente une sélection variée d'autres logiciels. Si vous avez installé une version fournie par un distributeur, vous disposez déjà de la plupart des éléments nécessaires pour divers scénarios, du développement Web au calcul technique.

### Python Tools for Visual Studio

Python Tools for Visual Studio (PTVS) est un plug-in gratuit/OSS de Microsoft qui transforme Visual Studio en un environnement de développement intégré (IDE) Python à part entière :

![how-to-install-python-ptvs][]

Nous vous recommandons d'utiliser PTVS même s'il est facultatif, car il vous apporte pour les projets et solutions Python et Django les fonctionnalités suivantes : support technique, débogage, profilage, fenêtre interactive, modification des modèles et Intellisense.

PTVS simplifie aussi le déploiement sur Microsoft Azure, avec une prise en charge du [déploiement sur les services cloud et les sites web][].

PTVS fonctionne avec votre installation existante de Visual Studio 2010, 2012 ou 2013. Pour plus d'informations et pour télécharger le logiciel, consultez la page [Python Tools for Visual Studio sur CodePlex][].

## Désinstallation sur Windows

Les produits WebPI **Azure SDK for Python** ne sont pas des applications au sens classique du terme, mais plutôt le regroupement d'un ensemble de produits distincts, comme Python 2.7/3.4 32 bits, les bibliothèques clientes Azure pour Python, etc. Par conséquent, ce produit ne comporte aucun programme de désinstallation qui lui est propre et vous devez passer par le Panneau de configuration Windows pour supprimer les programmes installés un à un.

Si vous souhaitez réinstaller **Azure SDK for Python**, ouvrez tout simplement une invite de commandes PowerShell en tant qu'administrateur et exécutez la commande suivante :

    rm -force "HKLM:\SOFTWARE\Microsoft\Python Tools for Azure"

Ensuite, réexécutez WebPI.

## Installation sur Linux et MacOS

Python est sans doute déjà installé sur votre ordinateur de développement. Pour vérifier, entrez la commande suivante :

![how-to-install-python-linux-run][]

Nous pouvons observer que CPython 2.7.6 est installé sur cet ordinateur virtuel Ubuntu Server 14.04 LTS qui s'exécute sur Azure. Si vous devez procéder à une mise à niveau, suivez les instructions de mise à niveau du package recommandées pour votre système d'exploitation.

Pour installer les bibliothèques clientes Python pour Azure, utilisez **pip** et téléchargez-les depuis **PyPI** :

    curl https://bootstrap.pypa.io/get-pip.py | sudo python

La commande ci-dessus lance une invite en mode silencieux vous demandant le mot de passe racine. Tapez-le et appuyez sur Entrée. Suivant :

    sudo pip install azure

Les bibliothèques clientes doivent normalement s'afficher dans **site-packages**. Sur MacOS :

![how-to-install-python-mac-site][]

Pour les développements sur les systèmes d'exploitation Mac/Linux, deux scénarios principaux sont pris en charge :

1.  Consommation des services Azure par le biais des bibliothèques clientes pour Python

2.  Exécution de votre application sur une machine virtuelle Linux

Le premier scénario vous permet de créer des applications Web enrichies qui tirent parti des fonctionnalités PaaS d'Azure comme le stockage d'objets blob, les files d'attente, etc. par le biais de wrappers Python pour les API REST d'Azure. Le fonctionnement de ces derniers est identique sur Windows, Mac et Linux. Pour obtenir des exemples, consultez les didacticiels et les manuels d'utilisation. Vous pouvez également utiliser ces bibliothèques clientes à partir d'une machine virtuelle Linux.

Pour le scénario nécessitant une machine virtuelle, vous pouvez tout simplement démarrer une machine virtuelle Linux de votre choix (Ubuntu, CentOS, Suse) et exécuter/gérer les logiciels souhaités. Par exemple, vous pouvez exécuter [IPython][] REPL/Notebook sur votre machine Windows/Mac/Linux et pointer votre navigateur vers une machine virtuelle multiprocesseur Linux ou Windows exécutant le moteur IPython Engine sur Azure. Pour plus d'informations sur l'installation de IPython, consultez le didacticiel qui lui est consacré.

Pour plus d'informations sur la configuration d'une machine virtuelle Linux, consultez la [rubrique Gestion de Linux.][]

## Logiciels et ressources supplémentaires :

-   [Distribution Enthought Python][]
-   [Distribution ActiveState Python][]
-   [SciPy : un vaste panel de bibliothèques Scientific Python][]
-   [NumPy : une bibliothèque de valeurs numériques pour Python][]
-   [Projet Django : une structure Web/un système de gestion du contenu arrivé à maturité][]
-   [IPython : une solution REPL/Notebook avancée pour Python][IPython]
-   [IPython Notebook sur Azure][]
-   [Python Tools for Visual Studio sur CodePlex][]
-   [Virtualenv][]

  [Web Platform Installer (WebPI)]: http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409
  [how-to-install-python-webpi-27-1]: ./media/python-how-to-install/how-to-install-python-webpi-27-1.png
  [how-to-install-python-webpi-27-2]: ./media/python-how-to-install/how-to-install-python-webpi-27-2.png
  [how-to-install-python-win-run-27]: ./media/python-how-to-install/how-to-install-python-win-run-27.png
  [how-to-install-python-webpi-34-1]: ./media/python-how-to-install/how-to-install-python-webpi-34-1.png
  [how-to-install-python-webpi-34-2]: ./media/python-how-to-install/how-to-install-python-webpi-34-2.png
  [how-to-install-python-win-run-34]: ./media/python-how-to-install/how-to-install-python-win-run-34.png
  [Python Package Index (PyPI)]: http://pypi.python.org/pypi
  [how-to-install-python-ptvs]: ./media/python-how-to-install/how-to-install-python-ptvs.png
  [déploiement sur les services cloud et les sites web]: /fr-fr/documentation/articles/cloud-services-web-sites-python-django-app-with-ptvs/
  [Python Tools for Visual Studio sur CodePlex]: http://pytools.codeplex.com
  [how-to-install-python-linux-run]: ./media/python-how-to-install/how-to-install-python-linux-run.png
  [how-to-install-python-mac-site]: ./media/python-how-to-install/how-to-install-python-mac-site.png
  [IPython]: http://ipython.org
  [rubrique Gestion de Linux.]: /fr-fr/manage/linux/
  [Distribution Enthought Python]: http://www.enthought.com
  [Distribution ActiveState Python]: http://www.activestate.com
  [SciPy : un vaste panel de bibliothèques Scientific Python]: http://www.scipy.org
  [NumPy : une bibliothèque de valeurs numériques pour Python]: http://www.numpy.org
  [Projet Django : une structure Web/un système de gestion du contenu arrivé à maturité]: http://www.djangoproject.com
  [IPython Notebook sur Azure]: /fr-fr/documentation/articles/virtual-machines-python-ipython-notebook
  [Virtualenv]: http://pypi.python.org/pypi/virtualenv
