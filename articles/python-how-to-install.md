<properties linkid="develop-python-install-python" urlDisplayName="Install Python" pageTitle="Install Python and the SDK - Azure" metaKeywords="Azure Python SDK" description="Learn how to install Python and the SDK to use with Azure." metaCanonical="" services="" documentationCenter="Python" title="Installing Python and the SDK" authors="" solutions="" manager="" editor="" />

Installation de Python et du Kit de développement logiciel (SDK)
================================================================

La configuration de Python sur Windows s'effectue en toute simplicité. La solution est préinstallée sur Mac et Linux. Ce guide décrit les étapes d'installation et de préparation de votre système à l'utilisation d'Azure. Il répond aux questions concernant les sujets suivants :

-   Que contient le Kit de développement logiciel (SDK) Python Azure ?
-   Quelle solution Python et quelle version utiliser ?
-   Installation sur Windows
-   Installation sur Mac et Linux

Que contient le Kit de développement logiciel (SDK) Python Azure ?
------------------------------------------------------------------

Le Kit de développement logiciel (SDK) Azure pour Python contient les composants qui vous permettent de développer, de déployer et de gérer des applications Python pour Azure. Il inclut plus précisément les éléments suivants :

-   **Bibliothèques clientes Python pour Azure**. L'interface de ces bibliothèques de classes permet d'accéder aux fonctionnalités Azure, telles que les services de gestion des données et Cloud Services.
-   **Outils en ligne de commande Azure pour Mac et Linux**. Ces outils en ligne de commande permettent de déployer et de gérer des services Azure, tels que Sites Web Azure et Azure Virtual Machines. Ces outils fonctionnent sur toutes les plateformes, dont Mac, Linux et Windows.
-   **Azure PowerShell (Windows uniquement)**. Cet ensemble de cmdlets PowerShell permet de déployer et de gérer les services Azure, tels que Cloud Services et Virtual Machines.
-   **Émulateurs Azure (Windows uniquement)**. Les émulateurs de calcul et de stockage sont des émulateurs locaux Cloud Services et de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure fonctionnent uniquement sur Windows.

Les principaux scénarios pour cette version sont les suivants :

-   **Windows** : service cloud, par exemple un site Django utilisant des éléments Webroles
-   **Mac/Linux** : IaaS. Exécutez les logiciels de votre choix dans une machine virtuelle ; consommez les services Azure par le biais de Python

Quelle solution Python et quelle version utiliser ?
---------------------------------------------------

Il existe différentes versions des interpréteurs Python, par exemple :

-   CPython : interpréteur Python standard le plus couramment utilisé
-   IronPython : interpréteur Python qui s'exécute sur .Net/CLR
-   Jython : interpréteur Python qui s'exécute sur JVM

Dans le cadre de cette version, l'interpréteur **CPython** est le seul à avoir été testé et à être pris en charge. Par ailleurs, nous recommandons d'utiliser au moins la version 2.7. **IronPython** sera également pris en charge dans un proche avenir.

Où télécharger Python ?
-----------------------

Il existe plusieurs façons de télécharger CPython :

-   Directement sur www.python.org
-   Auprès d'un distributeur fiable comme www.enthought.com ou www.ActiveState.com
-   Génération à partir de la source !

Sauf en cas de nécessité particulière, nous vous recommandons d'opter pour l'une des deux premières options en suivant les indications ci-dessous.

Installation sur Windows
------------------------

Pour Windows, vous pouvez utiliser le programme d'installation [Web Platform Installer (WebPI)](http://go.microsoft.com/fwlink/?LinkId=254281&clcid=0x409) fourni depuis le Centre de développement Python principal afin de faciliter l'installation (téléchargement du logiciel CPython depuis www.python.org).

**Remarque :** pour télécharger WebPI installer sur Windows Server, vous devez peut-être paramétrer la Configuration de sécurité renforcée d'Internet Explorer (Démarrer/Outils d'administration/Gestionnaire de serveur, puis cliquez sur **Paramétrer la Configuration de sécurité renforcée d’Internet Explorer** et désactivez l'option).

![how-to-install-python-webpi-1](./media/python-how-to-install/how-to-install-python-webpi-1.png)

WebPI procure tous les éléments nécessaires à l'installation des applications Python Azure ainsi qu'une prise en charge spécifique pour les applications Django :

![how-to-install-Python-webpi-2](./media/python-how-to-install/how-to-install-python-webpi-2.png)

Une fois l'installation terminée, cet écran s'affiche et confirme les options d'installation sélectionnées :

![how-to-install-python-webpi-3](./media/python-how-to-install/how-to-install-python-webpi-3.png)

Après la finalisation de l'installation, tapez python dans l'invite de commandes pour vérifier que tout s'est bien déroulé. Selon la méthode d'installation choisie, il peut être nécessaire de définir la variable « chemin d'accès » pour trouver (la bonne version de) Python :

![how-to-install-python-win-run](./media/python-how-to-install/how-to-install-python-win-run.png)

Cette version est principalement axée sur les applications Web avec Django, mais nous vous invitons à parcourir le [Python Package Index (PyPI)](http://pypi.python.org/pypi) où vous trouverez une sélection variée d'autres logiciels. Si vous avez installé une version fournie par un distributeur, vous disposez déjà de la plupart des éléments nécessaires pour divers scénarios, du développement Web au calcul technique.

Pour consulter les packages Python installés dans **site-packages**, entrez le texte suivant pour trouver son emplacement :

![how-to-install-python-win-site](./media/python-how-to-install/how-to-install-python-win-site.png)

Une liste des packages installés sur votre système s'affiche.

Après l'installation, Python, Django et les bibliothèques clientes sont disponibles à l'emplacement par défaut suivant :

     C:\Python27\Lib\site-packages\windowsazure
        C:\Python27\Lib\site-packages\django

### Python Tools for Visual Studio

Python Tools for Visual Studio est un plug-in gratuit/OSS de Microsoft qui transforme Visual Studio en un environnement de développement intégré (IDE) à part entière :

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

L'utilisation de Python Tools for Visual Studio est facultative, mais elle est recommandée, car elle vous permet de bénéficier d'une prise en charge des projets/solutions Python et Django, de fonctionnalités de débogage, de profilage, de modification de modèles et d'Intellisense, du déploiement vers le cloud, etc. Ce complément est compatible avec votre installation existante de Visual Studio 2010. Si vous ne disposez pas de ce logiciel, WebPI installe l'interface intégrée + PTVS qui vous permet de bénéficier d'un environnement de développement intégré « Visual Studio Python Express » **complètement gratuit**. Pour plus d'informations, consultez la section [Python Tools for Visual Studio sur CodePlex](http://pytools.codeplex.com).

Remarque : Si le plug-in PTVS est peu volumineux, le téléchargement de l'interface intégrée peut prendre du temps. Par ailleurs, l'interface intégrée ne prend actuellement pas en charge la fonctionnalité Add Azure Deployment Project.

Désinstallation sur Windows
---------------------------

Le produit WebPI **Azure SDK for Python June 2012** n'est pas une application au sens classique du terme, mais plutôt un ensemble de produits distincts regroupés, par exemple Python 2.7 32 bits, les API clientes Azure pour Python, Django, etc. Par conséquent, ce produit ne comporte aucun programme de désinstallation qui lui est propre et vous devez passer par le Panneau de configuration Windows pour supprimer les programmes installés un à un.

Si vous souhaitez réinstaller **Azure SDK for Python**, ouvrez tout simplement une invite de commandes PowerShell en tant qu'administrateur et exécutez la commande suivante :

    rm -force "HKLM:\SOFTWARE\Microsoft\Windows Azure SDK for Python - June 2012"

Ensuite, réexécutez WebPI.

Installation sur Linux et MacOS
-------------------------------

Python est sans doute déjà installé sur votre ordinateur de développement. Pour vérifier, entrez la commande suivante :

![how-to-install-python-linux-run](./media/python-how-to-install/how-to-install-python-linux-run.png)

Comme nous pouvons le voir, CPython 2.7.2 est installé sur cette machine virtuelle Azure Suse, ce qui convient parfaitement à l'exécution des didacticiels Azure et des exemples Django. Si vous devez procéder à une mise à niveau, suivez les instructions de mise à niveau du package recommandées pour votre système d'exploitation. Notez cependant qu'en règle générale, il est préférable de conserver uniquement la version de Python présente sur le système (d'autres systèmes peuvent dépendre de cette version) et d'installer une version plus récente en passant par [Virtualenv](http://pypi.python.org/pypi/virtualenv).

Pour installer les bibliothèques clientes Python pour Azure, utilisez **pip** et téléchargez-les depuis **PyPI** :

    curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | sudo python

La commande ci-dessus lance une invite en mode silencieux vous demandant le mot de passe racine. Tapez-le et appuyez sur Entrée. Suivant :

    sudo /usr/local/bin/pip-2.7 install azure

Les bibliothèques clientes doivent normalement s'afficher dans **site-packages**. Sur MacOS :

![Site-packages sous MacOS](./media/python-how-to-install/how-to-install-python-mac-site.png)

Pour les développements sur les systèmes d'exploitation Mac/Linux, cette version prend en charge deux scénarios principaux :

1.  Consommation des services Azure par le biais des bibliothèques clientes pour Python

2.  Exécution de votre application sur une machine virtuelle Linux

Le premier scénario vous permet de créer des applications Web enrichies qui tirent parti des fonctionnalités PaaS d'Azure comme le stockage d'objets blob, les files d'attente, etc. par le biais de wrappers Python pour les API REST d'Azure. Le fonctionnement de ces derniers est identique sur Windows, Mac et Linux. Pour obtenir des exemples, consultez les didacticiels et les manuels d'utilisation. Vous pouvez également utiliser ces bibliothèques clientes à partir d'une machine virtuelle Linux.

Pour le scénario nécessitant une machine virtuelle, vous pouvez tout simplement démarrer une machine virtuelle Linux de votre choix (Ubuntu, CentOS, Suse) et exécuter/gérer les logiciels souhaités. Par exemple, vous pouvez exécuter [IPython](http://ipython.org) REPL/Notebook sur votre machine Windows/Mac/Linux et pointer votre navigateur vers une machine virtuelle multiprocesseur Linux ou Windows exécutant le moteur IPython Engine sur Azure. Pour plus d'informations sur l'installation de IPython, consultez le didacticiel qui lui est consacré.

Pour plus d'informations sur la configuration d'une machine virtuelle Linux, consultez la [rubrique Gestion de Linux.](/en-us/manage/linux/)

Logiciels et ressources supplémentaires :
-----------------------------------------

-   [Distribution Enthought Python](http://www.enthought.com)
-   [Distribution ActiveState Python](http://www.activestate.com)
-   [SciPy : un vaste panel de bibliothèques Scientific Python](http://www.scipy.org)
-   [NumPy : une bibliothèque de valeurs numériques pour Python](http://www.numpy.org)
-   [Projet Django : une structure Web/un système de gestion du contenu arrivé à maturité](http://www.djangoproject.com)
-   [IPython : une solution REPL/Notebook avancée pour Python](http://ipython.org)
-   [IPython Notebook sur Azure](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-ipython-notebook)
-   [Python Tools for Visual Studio sur CodePlex](http://pytools.codeplex.com)
-   [Virtualenv](http://pypi.python.org/pypi/virtualenv)

