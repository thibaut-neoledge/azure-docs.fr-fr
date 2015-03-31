<properties 
	pageTitle="Didacticiel IPython Notebook - Azure" 
	description="Didacticiel qui explique le déploiement de l'interpréteur IPython Notebook sur Azure en utilisant des machines virtuelles Linux ou Windows." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="huvalo"/>





# IPython Notebook sur Azure

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Le <a href="http://ipython.org">projet IPython</a> offre un ensemble d'outils destiné au calcul scientifique qui inclut des interpréteurs de commandes interactifs et puissants, des bibliothèques parallèles haute performance et conviviales, ainsi qu'un environnement Web nommé IPython Notebook. L'interpréteur IPython Notebook offre un environnement de travail pour le calcul interactif qui associe l'exécution de code à la création d'un document de calcul dynamique. Ces fichiers de l'interpréteur contiennent du texte arbitraire, des formules mathématiques, un code d'entrée, des résultats, des graphiques, des vidéos et d'autres sortes de support qu'un navigateur Web moderne peut afficher.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="label">visionner le didacticiel</a> <a style="background-image: url('/media/devcenter/python/ipy-youtube2.png') !important;" href="http://go.microsoft.com/fwlink/?LinkID=254535&amp;clcid=0x409" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">05:22</span></div>
</div>

Que vous soyez complètement novice sur Python et que vous souhaitiez en savoir plus dans un environnement interactif ou que vous souhaitiez effectuer certains calculs parallèles/techniques sérieux, l'interpréteur IPython Notebook est un bon choix. En guise d'illustration de ses capacités, la capture d'écran suivante montre l'interpréteur IPython Notebook utilisé avec les packages SciPy et matplotlib, pour analyser la structure d'un enregistrement sonore :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Ce document vous montre comment déployer l'interpréteur IPython Notebook sur Microsoft Azure, à l'aide des machines virtuelles (VM) Windows ou Linux.  À l'aide de l'interpréteur IPython Notebook sur Azure, vous pouvez fournir facilement une interface accessible par le web à des ressources de calcul extensibles avec toute la puissance de Python et ses nombreuses bibliothèques.  Dans la mesure où toute l'installation est effectuée dans le cloud, les utilisateurs peuvent accéder à ces ressources sans recourir à une configuration locale hormis un navigateur web moderne.

[AZURE.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

## Création et configuration d'une machine virtuelle sur Azure

La première étape consiste à créer une machine virtuelle s'exécutant sur Azure. Cette machine virtuelle est un système d'exploitation complet dans le cloud et sera utilisée pour exécuter l'interpréteur IPython Notebook. Azure peut exécuter à la fois des machines virtuelles Linux et Windows et nous allons étudier la configuration d'IPython sur les deux types de machine.

### Machine virtuelle Linux

Suivez les instructions fournies [ici][portal-vm-linux] pour créer une machine virtuelle de la distribution *OpenSUSE* ou *Ubuntu*. Ce didacticiel utilise OpenSUSE 13.2 et Ubuntu Server 14.04 LTS. Nous présumerons que le nom d'utilisateur par défaut est *azureuser*.

### Machine virtuelle Windows

Suivez les instructions fournies [ici][portal-vm-windows] afin de créer une machine virtuelle de la distribution *Windows Server 2012 R2 Datacenter*. Dans ce didacticiel, nous présumerons que le nom d'utilisateur est *azureuser*.

## Création d'un point de terminaison pour l'interpréteur IPython Notebook

Cette étape s'applique à la fois à la machine virtuelle Linux et à la machine virtuelle Windows. Plus tard, nous configurerons IPython pour exécuter son serveur d'interpréteur sur le port 9999. Pour rendre ce port disponible publiquement, nous devons créer un point de terminaison dans le portail de gestion Azure. Ce point de terminaison ouvre un port dans le pare-feu Azure et mappe le port public (HTTPS, 443) vers le port privé de la machine virtuelle (9999).

Pour créer un point de terminaison, accédez au tableau de bord de la machine virtuelle, cliquez sur " Points de terminaison ", puis sur " Ajouter un point de terminaison " et créez un nouveau point de terminaison (appelé `ipython_nb` dans cet exemple). Sélectionnez TCP comme protocole, 443 pour le port public et 9999 pour le port privé :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Après cette étape, l'onglet " Points de terminaison " du tableau de bord apparaîtra ainsi :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

## Installation des logiciels requis sur la machine virtuelle

Pour exécuter l'interpréteur IPython Notebook sur votre machine virtuelle, vous devez tout d'abord installer IPython et ses dépendances.

### Linux (OpenSUSE)

Pour installer IPython et ses dépendances, utilisez le protocole SSH dans la machine virtuelle Linux et procédez comme suit.

Installez [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] et les autres dépendances IPython en procédant comme suit :

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install python-jinja2
    sudo zypper install ipython

### Linux (Ubuntu)

Pour installer IPython et ses dépendances, utilisez le protocole SSH dans la machine virtuelle Linux et procédez comme suit.

Récupérez tout d'abord les nouvelles listes de packages :

    sudo apt-get update

Installez [NumPy][numpy], [Matplotlib][matplotlib], [Tornado][tornado] et les autres dépendances IPython en procédant comme suit :

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Pour installer IPython et ses dépendances sur la machine virtuelle Windows, utilisez le Bureau à distance pour la connexion à la machine virtuelle. Puis exécutez les étapes suivantes, en utilisant Windows PowerShell pour exécuter toutes les actions de ligne de commande.

**Remarque** : afin de pouvoir télécharger à l'aide d'Internet Explorer, vous devez modifier certains paramètres de sécurité.  Depuis **Gestionnaire de serveur**, cliquez sur **Serveur local**, puis sur **Configuration de sécurité renforcée d'Internet Explorer**, puis désactivez-la pour les administrateurs.  Vous pourrez la réactiver une fois que vous aurez terminé l'installation d'IPython.

1.  Téléchargez et installez la dernière version 32 bits de [Python 2.7][].  Vous devrez ajouter `C:\Python27` et `C:\Python27\Scripts` à votre variable d'environnement `PATH`.

1.  Installez [Tornado][tornado] et [PyZMQ][pyzmq] et les autres dépendances IPython en procédant comme suit :

        easy_install tornado
        easy_install pyzmq
        easy_install jinja2
        easy_install six
        easy_install python-dateutil
        easy_install pyparsing

1.  Téléchargez et installez [NumPy][numpy] en utilisant le programme d'installation binaire `.exe` disponible sur leur site Web.  Lors de la rédaction de ce document, la dernière version était **numpy-1.9.1-win32-superpack-python2.7.exe**.

1.  Installez [Matplotlib][matplotlib] en procédant comme suit :

        pip install matplotlib==1.4.2

1.  Téléchargez et installez [OpenSSL][].

	* Vous trouverez le composant **Visual C++ 2008 Redistributable** sur la même page de téléchargement.

	* Vous devrez ajouter `C:\OpenSSL-Win32\bin` à votre variable d'environnement `PATH`.

	> [AZURE.NOTE] Lors de l'installation d'OpenSSL, utilisez la version 1.0.1g ou supérieure, car elle inclut un correctif pour la faille de sécurité Heartbleed.

1.  Installez IPython en utilisant la commande  suivante:

        pip install ipython==2.4

1.  Ouvrez un port dans le pare-feu Windows.  Sur Windows Server 2012, le pare-feu bloque les connexions entrantes par défaut.  Pour ouvrir le port 9999, procédez comme suit :

    - Démarrez le **Pare-feu Windows avec sécurité avancée** dans l'écran Démarrer.

    - Cliquez sur **Règles de trafic entrant** dans le volet gauche.

	- Cliquez sur **Nouvelle règle...** dans le volet Actions.

	- Dans l'Assistant Nouvelle règle de trafic entrant, sélectionnez **Port**.

	- Dans l'écran suivant, sélectionnez **TCP** et saisissez **9999** dans **Ports locaux spécifiques**.

	- Acceptez les valeurs par défaut, donnez à la règle un nom et cliquez sur Terminer.

### Configuration de l'interpréteur IPython Notebook

Ensuite, nous configurerons l'interpréteur IPython Notebook. La première étape consiste à créer un profil de configuration IPython personnalisé pour encapsuler les informations de configuration :

    ipython profile create nbserver

Ensuite, nous utilisons la commande `cd` pour accéder au répertoire de profil afin de créer notre certificat SSL et de modifier le fichier de configuration de profils.

Sur Linux :

    cd ~/.ipython/profile_nbserver/

Sur Windows :

    cd \users\azureuser\.ipython\profile_nbserver

Créez le certificat SSL comme suit (Linux et Windows) :

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Notez que puisque nous créons un certificat SSL auto-signé, lors de la connexion à l'intégrateur votre navigateur affichera un avertissement de sécurité.  Pour une utilisation à long terme, vous souhaiterez utiliser un certificat correctement signé associé à votre organisation.  Étant donné que la gestion des certificats est abordée dans dans cette démonstration, nous nous en tiendrons à un certificat auto-signé pour l'instant.

En plus de l'utilisation d'un certificat, vous devez également fournir un mot de passe pour protéger votre interpréteur contre toute utilisation non autorisée.  Pour des raisons de sécurité, IPython utilise des mots de passe chiffrés dans son fichier de configuration, et vous devrez donc tout d'abord chiffrer votre mot de passe.  Pour ce faire, IPython fournit un utilitaire ; à l'exécution d'une invite de commandes :

    python -c "import IPython;print IPython.lib.passwd()"

Vous serez invité à fournir un mot de passe et une confirmation, puis le mot de passe s'affichera comme suit :

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)
    
Ensuite, nous allons modifier le fichier de configuration du profil, qui est le fichier `ipython_notebook_config.py` dans le répertoire de profil où vous vous trouvez. Notez que ce fichier n'existe pas, il suffit de le créer.  Ce fichier comporte de nombreux champs, tous commentés par défaut.  Vous pouvez ouvrir ce fichier avec un éditeur de texte de votre choix et vous devez vous assurer qu'il comporte au moins le contenu suivant :

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

À présent, nous sommes prêts à démarrer l'interpréteur IPython. Pour ce faire, accédez au répertoire où vous voulez stocker les interpréteurs et démarrez le serveur IPython Notebook :

    ipython notebook --profile=nbserver

Vous devez à présent être en mesure d'accéder à votre interpréteur IPython à l'adresse `https://[Nom choisi ici].cloudapp.net`.

Lorsque vous accédez pour la première fois à votre interpréteur, la page de connexion demande votre mot de passe :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Une fois que vous êtes connecté, " Tableau de bord IPython Notebook " s'affiche.
Il s'agit du centre de contrôle de toutes les opérations de l'intégrateur.  À partir de cette page, vous pouvez créer de nouveaux intégrateurs, ouvrir des intégrateurs existants etc. :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Si vous cliquez sur le bouton " Nouvel intégrateur ", une page d'ouverture s'affiche comme suit :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

La zone marquée par une invite `In []:` est la zone d'entrée. Vous pouvez y taper tout code Python valide et il s'exécutera lorsque vous appuyez sur `Shift-Enter` ou cliquez sur l'icône de lecture (le triangle pointant vers la droite dans la barre d'outils).

Puisque nous avons configuré l'intégrateur pour démarrer avec la prise en charge de NumPy et matplotlib automatiquement, vous pouvez même produire des figures, par exemple :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

## Un puissant paradigme : documents de calcul dynamique avec des supports puissants

L'interpréteur lui-même doit sembler très naturel à toute personne ayant utilisé Python et un traitement de texte, car il est une combinaison des deux à certains égards : vous pouvez exécuter des blocs de code Python, mais vous pouvez également conserver des notes et autres textes en modifiant le style d'une cellule de " Code " à " Texte ", à l'aide du menu déroulant de la barre d'outils :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)


Mais l'intégrateur est bien plus qu'un traitement de texte, car il permet la combinaison de calculs et de supports puissants (texte, graphiques, vidéo et pratiquement tout ce qu'un navigateur web moderne peut afficher). Par exemple, vous pouvez combiner des vidéos explicatives à un calcul à des fins pédagogiques :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

ou intégrer des sites web externes, qui restent actifs et utilisables, dans un fichier de l'interpréteur :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Et grâce à la puissance des nombreuses bibliothèques d'excellence de Python pour le calcul scientifique et technique, un calcul simple peut être effectué avec la même facilité qu'une analyse de réseau complexe, dans un environnement unique :

![Screenshot](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Ce paradigme d'association de la puissance du web moderne avec le calcul dynamique offre de nombreuses possibilités et est idéal pour le cloud. L'intégrateur peut être utilisé :

* en tant que bloc-notes de calcul afin d'enregistrer le travail exploratoire relatif à un problème ;

* pour partager les résultats avec des collègues, sous la forme d'un calcul 'live' ou aux formats de sortie papier (HTML, PDF) ;

* pour distribuer et présenter des supports de formation en direct impliquant le calcul pour que les étudiants puissent immédiatement utiliser le code réel, le modifier et le réexécuter de façon interactive ;

* pour fournir des " papiers exécutables " qui présentent les résultats de la recherche d'une manière qui peut être immédiatement reproduite, validée et étendue par d'autres utilisateurs,

* en tant que plate-forme pour le calcul collaboratif : plusieurs utilisateurs peuvent se connecter au même serveur d'interpréteur afin de partager une session de calcul en direct,

* et bien plus...

Si vous accédez au code source IPython [référentiel][repository], vous trouverez tout un répertoire avec des exemples d'intégrateur que vous pouvez télécharger et expérimenter sur votre propre machine virtuelle Azure IPython. Téléchargez simplement les fichiers `.ipynb` depuis le site et chargez-les sur le tableau de bord de la machine virtuelle Azure de l'interpréteur (ou téléchargez-les directement dans la machine virtuelle).

## Conclusion

L'interpréteur IPython Notebook fournit une interface puissante pour accéder interactivement à la puissance de l'écosystème Python sur Azure.  Il couvre une large gamme d'exemples d'utilisation, notamment la simple exploration et l'apprentissage Python, l'analyse des données et la visualisation, la simulation et le calcul parallèle. Les documents d'intégrateur résultant contiennent un enregistrement complet des calculs qui sont effectués et peuvent être partagés avec d'autres utilisateurs IPython.  L'interpréteur IPython Notebook peut être utilisé comme une application locale mais est idéalement adapté pour des déploiements dans le cloud sur Azure

Les fonctionnalités principales d'IPython sont également disponibles dans Visual Studio par l'intermédiaire des [outils Python pour Visual Studio][Python Tools for Visual Studio] (PTVS). PTVS est un plug-in gratuit et open source de Microsoft qui transforme Visual Studio en un environnement de développement Python avancé qui inclut un éditeur avancé avec IntelliSense, le débogage, le profilage et l'intégration de l'informatique parallèle.



[tornado]:      http://www.tornadoweb.org/          "Tornado"
[PyZMQ]:        https://github.com/zeromq/pyzmq     "PyZMQ"
[NumPy]:        http://www.numpy.org/               "NumPy"
[Matplotlib]:   http://matplotlib.sourceforge.net/  "Matplotlib"

[portal-vm-windows]: /manage/windows/tutorials/virtual-machine-from-gallery/
[portal-vm-linux]: /manage/linux/tutorials/virtual-machine-from-gallery/

[repository]: https://github.com/ipython/ipython
[Python Tools for Visual Studio]: http://aka.ms/ptvs

[Python 2.7]: http://www.python.org/download
[OpenSSL]: http://slproweb.com/products/Win32OpenSSL.html

<!--HONumber=47-->
