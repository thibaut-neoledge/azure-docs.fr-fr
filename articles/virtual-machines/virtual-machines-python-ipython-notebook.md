<properties
	pageTitle="Création d’un bloc-notes IPython | Microsoft Azure"
	description="Découvrez comment déployer le bloc-notes IPython sur une machine virtuelle Linux ou Windows créée avec le modèle de déploiement classique dans Azure."
	services="virtua-lmachines"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""
	tags=“azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="python"
	ms.topic="article"
	ms.date="05/20/2015"
	ms.author="huvalo"/>

# IPython Notebook sur Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite du déploiement d’une bloc-notes sur une machine virtuelle créée avec le modèle de déploiement classique.

Le [projet Python](http://ipython.org) offre un ensemble d'outils destiné au calcul scientifique qui inclut des interpréteurs de commandes interactifs et puissants, des bibliothèques parallèles haute performance et conviviales, ainsi qu'un environnement Web nommé IPython Notebook. L'interpréteur IPython Notebook offre un environnement de travail pour le calcul interactif qui associe l'exécution de code à la création d'un document de calcul dynamique. Ces fichiers de l'interpréteur contiennent du texte arbitraire, des formules mathématiques, un code d'entrée, des résultats, des graphiques, des vidéos et d'autres sortes de support qu'un navigateur Web moderne peut afficher.

Que vous soyez complètement novice sur Python et que vous souhaitiez apprendre à vous en servir dans un environnement divertissant et interactif ou que vous souhaitiez effectuer d'importants calculs parallèles/techniques, l'interpréteur IPython Notebook est un choix optimal. La capture d’écran suivante présente les fonctionnalités de l’interpréteur IPython Notebook et en montre l’utilisation en association avec les packages SciPy et Matplotlib pour analyser la structure d’un enregistrement sonore.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Cet article vous montre comment déployer l’interpréteur IPython Notebook sur Microsoft Azure en utilisant des machines virtuelles Linux ou Windows. En utilisant l'interpréteur IPython Notebook sur Azure, vous pouvez facilement fournir une interface accessible par le Web à des ressources de calcul évolutives avec toute la puissance de Python et de ses nombreuses bibliothèques. Comme toute l'installation est réalisée dans le cloud, les utilisateurs peuvent accéder à ces ressources sans recourir à une configuration locale hormis un navigateur Web moderne.

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Créer et configurer une machine virtuelle sur Azure

La première étape consiste à créer une machine virtuelle s’exécutant sur Azure. Cette machine virtuelle est un système d'exploitation complet dans le cloud et sera utilisée pour exécuter l'interpréteur IPython Notebook. Azure peut exécuter à la fois des machines virtuelles Linux et Windows et nous allons étudier la configuration d'IPython sur les deux types de machines.

### Machine virtuelle Linux

Suivez les instructions se trouvant [ici][portal-vm-linux] pour créer une machine virtuelle de la distribution *OpenSUSE* ou *Ubuntu*. Ce didacticiel utilise OpenSUSE 13.2 et Ubuntu Server 14.04 LTS. Nous présumerons que le nom d'utilisateur par défaut est *azureuser*.

### Machine virtuelle Windows

Suivez [ces instructions][portal-vm-windows] pour créer une machine virtuelle de la distribution *Windows Server 2012 R2 Datacenter*. Dans ce didacticiel, nous présumerons que le nom d'utilisateur est *azureuser*.

## Création d’un point de terminaison pour l’interpréteur IPython Notebook

Cette étape s'applique à la fois à la machine virtuelle Linux et à la machine virtuelle Windows. Plus tard, nous configurerons IPython pour exécuter son serveur d'interpréteur sur le port 9999. Pour rendre ce port disponible publiquement, nous devons créer un point de terminaison dans le portail de gestion Azure. Celui-ci ouvre un port dans le pare-feu Azure et mappe le port public (HTTPS, 443) sur le port privé de la machine virtuelle (9999).

Pour créer un point de terminaison, accédez au tableau de bord de la machine virtuelle, cliquez sur **Points de terminaison**, puis sur **Ajouter un point de terminaison** et créez un point de terminaison (nommé `ipython_nb` dans cet exemple). Sélectionnez **TCP** comme protocole, **443** pour le port public et **9999** pour le port privé.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Après cette étape, l’onglet **Points de terminaison** du tableau de bord apparaît comme sur la capture d’écran suivante.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Installer les logiciels requis sur la machine virtuelle

Pour exécuter l'interpréteur IPython Notebook sur votre machine virtuelle, vous devez tout d'abord installer IPython et ses dépendances.

### Linux (OpenSUSE)

Pour installer IPython et ses dépendances, utilisez le protocole SSH dans la machine virtuelle Linux et procédez comme suit.

Installez [NumPy][NumPy], [Matplotlib][Matplotlib], [Tornado][Tornado] et les autres dépendances d’IPython avec les commandes suivantes.

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Pour installer IPython et ses dépendances, utilisez le protocole SSH dans la machine virtuelle Linux et procédez comme suit.

Récupérez d’abord les nouvelles listes de packages avec la commande suivante.

    sudo apt-get update

Installez [NumPy][NumPy], [Matplotlib][Matplotlib], [Tornado][Tornado] et les autres dépendances d’IPython avec les commandes suivantes.

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Pour installer IPython et ses dépendances sur la machine virtuelle Windows, utilisez le Bureau à distance pour la connexion à la machine virtuelle. Procédez ensuite comme suit, en utilisant Windows PowerShell pour exécuter toutes les actions de ligne de commande.

**Remarque** : pour pouvoir télécharger à l’aide d’Internet Explorer, vous devez changer certains paramètres de sécurité. Depuis **Gestionnaire de serveur**, cliquez sur **Serveur local**, puis cliquez sur **Configuration de sécurité renforcée d’Internet Explorer** et désactivez-la pour les administrateurs. Vous pouvez l’activer à nouveau après l’installation d’IPython.

1.  Téléchargez et installez la dernière version 32 bits de [Python 2.7][]. Vous devrez ajouter `C:\Python27` et `C:\Python27\Scripts` à votre variable d’environnement `PATH`.

1.  Installez [Tornado][Tornado], [PyZMQ][PyZMQ] et les autres dépendances d’IPython avec les commandes suivantes.

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Téléchargez et installez [NumPy][NumPy] en utilisant le programme d’installation binaire `.exe` disponible sur le site web associé. Lors de la rédaction de ce document, la dernière version était numpy-1.9.1-win32-superpack-python2.7.exe.

1.  Installez [Matplotlib][Matplotlib] avec la commande suivante.

        pip install matplotlib==1.4.2

1.  Téléchargez et installez [OpenSSL][].

	* Vous trouverez le composant Visual C++ 2008 Redistributable sur la même page de téléchargement.

	* Vous devrez ajouter `C:\OpenSSL-Win32\bin` à votre variable d’environnement `PATH`.

	> [AZURE.NOTE]Lors de l’installation d’OpenSSL, utilisez la version 1.0.1g ou ultérieure, car elle inclut un correctif pour la faille de sécurité Heartbleed.

1.  Installez IPython en utilisant la commande  suivante.

        pip install ipython==2.4

1.  Ouvrez un port dans le pare-feu Windows. Sur Windows Server 2012, le pare-feu bloque les connexions entrantes par défaut. Pour ouvrir le port 9999, procédez comme suit :

    - Démarrez le **Pare-feu Windows avec fonctions avancées de sécurité** à partir de l’écran d’accueil.

    - Cliquez sur **Règles de trafic entrant** dans le volet gauche.

	- Cliquez sur **Nouvelle règle** dans le volet Actions.

	- Dans l'Assistant Nouvelle règle de trafic entrant, sélectionnez **Port**.

	- Dans l'écran suivant, sélectionnez **TCP** et saisissez **9999** dans **Ports locaux spécifiques**.

	- Acceptez les valeurs par défaut, donnez un nom à la règle, puis cliquez sur **Terminer**.

### Configuration de l'interpréteur IPython Notebook

Ensuite, nous configurerons l'interpréteur IPython Notebook. La première étape consiste à créer un profil de configuration IPython personnalisé pour encapsuler les informations de configuration avec la commande suivante.

    ipython profile create nbserver

Ensuite, nous utilisons la commande `cd` pour accéder au répertoire de profil afin de créer notre certificat SSL et de modifier le fichier de configuration des profils.

Sur Linux, utilisez la commande suivante.

    cd ~/.ipython/profile_nbserver/

Sur Windows, utilisez la commande suivante.

    cd \users\azureuser\.ipython\profile_nbserver

Utilisez la commande suivante pour créer le certificat SSL (Linux et Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Notez que puisque nous créons un certificat SSL auto-signé, lors de la connexion à l'interpréteur, votre navigateur affiche un message d'avertissement. Pour une utilisation à long terme, vous souhaiterez utiliser un certificat correctement signé associé à votre organisation. Comme la gestion des certificats ne figure pas dans cette démonstration, nous nous en tiendrons à un certificat auto-signé pour l'instant.

En plus de l'utilisation d'un certificat, vous devez également fournir un mot de passe pour protéger votre interpréteur contre toute utilisation non autorisée. Pour des raisons de sécurité, IPython utilise des mots de passe chiffrés dans son fichier de configuration. De ce fait, vous devez tout d'abord chiffrer votre mot de passe. Pour ce faire, IPython fournit un utilitaire. À une invite de commandes, exécutez la commande suivante.

    python -c "import IPython;print IPython.lib.passwd()"

Vous serez invité à fournir un mot de passe et une confirmation, puis le mot de passe s’affichera comme suit.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Nous allons ensuite modifier le fichier de configuration du profil (fichier `ipython_notebook_config.py` du répertoire de profil dans lequel vous vous trouvez). Si ce fichier n’existe pas, créez-le. Ce fichier comporte de nombreux champs, tous commentés par défaut. Vous pouvez ouvrir ce fichier avec un éditeur de texte de votre choix et vous devez vous assurer qu’il a au moins le contenu suivant.

    c = get_config()

    # This starts plotting support always with matplotlib
    c.IPKernelApp.pylab = 'inline'

    # You must give the path to the certificate file.

    # If using a Linux VM:
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'

    # And if using a Windows VM:
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port

    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Exécution de l'interpréteur IPython Notebook

À présent, nous sommes prêts à démarrer l'interpréteur IPython. Pour ce faire, accédez au répertoire où vous voulez stocker les interpréteurs et démarrez le serveur IPython Notebook avec la commande suivante.

    ipython notebook --profile=nbserver

Vous devez à présent être en mesure d’accéder à votre interpréteur IPython Notebook à l’adresse `https://[Your Chosen Name Here].cloudapp.net`.

Quand vous accédez pour la première fois à votre interpréteur, la page de connexion demande votre mot de passe.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Une fois que vous êtes connecté, « IPython Notebook Dashboard » s'affiche. Il s'agit du centre de commande de toutes les opérations de l'interpréteur. Dans cette page, vous pouvez créer des interpréteurs et ouvrir des interpréteurs existants.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Si vous cliquez sur le bouton **Nouvel interpréteur**, la page d’ouverture suivante s’affiche.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

La zone marquée par une invite `In []:` est la zone d’entrée. Vous pouvez y taper tout code Python valide qui s’exécutera quand vous appuierez sur les touches `Shift-Enter` ou que vous cliquerez sur l’icône « Lire » (triangle pointant vers la droite dans la barre d’outils).

Comme nous avons configuré l’interpréteur pour démarrer avec la prise en charge automatique de NumPy et de Matplotlib, vous pouvez même produire des figures, comme le montre la capture d’écran suivante.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Un puissant paradigme : documents de calcul dynamique avec des supports puissants

L’interpréteur lui-même semble très naturel à toute personne ayant utilisé Python et un traitement de texte, car c’est une combinaison des deux à certains égards : vous pouvez exécuter des blocs de code Python, mais vous pouvez également conserver des notes et d’autres types de texte en changeant le style d’une cellule de « Code » en « Texte » à l’aide du menu déroulant dans la barre d’outils.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Mais il s'agit bien plus que d'un traitement de texte, car l'interpréteur IPython permet l'association de calculs et de supports puissants (texte, graphiques, vidéos et, virtuellement, tout ce qu'un navigateur Web moderne peut afficher). Par exemple, vous pouvez combiner des vidéos d’explication avec des calculs à des fins de formation.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

Comme le montre la capture d’écran suivante, vous pouvez aussi intégrer des sites web externes qui restent actifs et utilisables dans un fichier d’interpréteur.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

De même, avec la puissance des nombreuses et très performantes bibliothèques de Python pour le calcul scientifique et technique, un simple calcul peut être effectué avec la même facilité qu’une analyse complexe d’un réseau, le tout dans un même environnement.

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Ce paradigme d'association de la puissance du Web avec le calcul dynamique offre de nombreuses possibilités et est idéalement approprié au cloud. L'interpréteur peut être utilisé :

* en tant que bloc-notes de calcul pour enregistrer le travail exploratoire relatif à un problème ;

* pour partager des résultats avec des collègues, sous la forme d’un calcul « dynamique » ou de formats de sortie papier (HTML, PDF) ;

* pour distribuer et présenter des supports de formation en direct impliquant du calcul, afin que les participants puissent immédiatement utiliser le code réel, le modifier et le réexécuter interactivement ;

* pour fournir d’autres « papiers exécutables » qui présentent les résultats de recherches d’une manière qui peut être immédiatement reproduite, validée et étendue par d’autres ;

* comme plateforme pour le calcul collaboratif : plusieurs utilisateurs peuvent se connecter au même serveur d’interpréteur afin de partager une session de calcul en direct.



Si vous accédez au [référentiel][] de code source IPython, vous y trouverez un répertoire complet avec des exemples d’interpréteur, que vous pouvez télécharger, puis expérimenter avec votre propre machine virtuelle Azure IPython. Téléchargez simplement les fichiers `.ipynb` à partir du site et chargez-les sur le tableau de bord de la machine virtuelle Azure de l’interpréteur (ou téléchargez-les directement dans la machine virtuelle).

## Conclusion

L'interpréteur IPython Notebook offre une interface haute performance pour accéder interactivement à la puissance de l'écosystème Python sur Azure. Il couvre une large gamme de cas d'utilisation, notamment les simples exploration et apprentissage de Python, l'analyse et la visualisation des données, le calcul de simulation et parallèle. Les documents d'interpréteur résultant contiennent un enregistrement complet des calculs qui sont effectués et peuvent être partagés avec d'autres utilisateurs IPython. L'interpréteur IPython Notebook peut être utilisé comme application locale mais est idéalement adapté pour des déploiements dans le cloud sur Azure.

Les fonctionnalités principales d'IPython sont également disponibles dans Visual Studio par l'intermédiaire des [outils Python pour Visual Studio][] (PTVS). PTVS est un plug-in open source gratuit de Microsoft qui transforme Visual Studio en environnement de développement Python avancé qui inclut un éditeur avancé avec IntelliSense, ainsi qu'une intégration de débogage, de profilage et de calcul parallèle.




[Tornado]: http://www.tornadoweb.org/ "Tornado"
[PyZMQ]: https://github.com/zeromq/pyzmq "PyZMQ"
[NumPy]: http://www.numpy.org/ "NumPy"
[Matplotlib]: http://matplotlib.sourceforge.net/ "Matplotlib"
[portal-vm-windows]: /manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /manage/linux/tutorials/virtual-machine-from-gallery/
[référentiel]: https://github.com/ipython/ipython
[outils Python pour Visual Studio]: http://aka.ms/ptvs
[Python 2.7]: http://www.python.org/download
[OpenSSL]: http://slproweb.com/products/Win32OpenSSL.html

<!---HONumber=Sept15_HO4-->