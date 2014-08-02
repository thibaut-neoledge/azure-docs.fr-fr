<properties linkid="develop-python-ipython-notebook" urlDisplayName="IPython Notebook" pageTitle="IPython Notebook - Azure tutorial" metaKeywords="" description="A tutorial that shows how to deploy the IPython Notebook on Azure, using Linux or Windows virtual machines (VMs)." metaCanonical="" services="virtual-machines" documentationCenter="Python" title="IPython Notebook on Azure" authors="" solutions="" manager="" editor="" />

IPython Notebook sur Azure
==========================

Le [projet Python](http://ipython.org) offre un ensemble d'outils destiné au calcul scientifique qui inclut des interpréteurs de commandes interactifs et puissants, des bibliothèques parallèles haute performance et conviviales, ainsi qu'un environnement Web nommé IPython Notebook. L'interpréteur IPython Notebook offre un environnement de travail pour le calcul interactif qui associe l'exécution de code à la création d'un document de calcul dynamique. Ces fichiers de l'interpréteur contiennent du texte arbitraire, des formules mathématiques, un code d'entrée, des résultats, des graphiques, des vidéos et d'autres sortes de support qu'un navigateur Web moderne peut afficher.

[regarder le didacticiel](http://go.microsoft.com/fwlink/?LinkID=254535&clcid=0x409) [Lire la vidéo](http://go.microsoft.com/fwlink/?LinkID=254535&clcid=0x409) 05:22:00

Que vous soyez complètement novice sur Python et que vous souhaitiez apprendre à vous en servir dans un environnement divertissant et interactif ou que vous souhaitiez effectuer d'importants calculs parallèles/techniques, l'interpréteur IPython Notebook est un choix optimal. La capture d'écran suivante présente les fonctionnalités de l'interpréteur IPython Notebook, et pour ce faire, en montre l'utilisation en association avec les packages SciPy et matplotlib, afin d'analyser la structure d'un enregistrement sonore :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-spectral.png)

Ce document vous expliquera comment déployer l'interpréteur IPython Notebook sur Windows Azure, à l'aide des machines virtuelles Linux ou Windows. En utilisant l'interpréteur IPython Notebook sur Azure, vous pouvez facilement fournir une interface accessible par le Web à des ressources de calcul évolutives avec toute la puissance de Python et de ses nombreuses bibliothèques. Comme toute l'installation est réalisée dans le cloud, les utilisateurs peuvent accéder à ces ressources sans recourir à une configuration locale hormis un navigateur Web moderne.

[WACOM.INCLUDE [create-account-and-vms-note](../includes/create-account-and-vms-note.md)]

Création et configuration d'une machine virtuelle sur Azure
-----------------------------------------------------------

La première étape consiste à créer une machine virtuelle s'exécutant sur Azure. Cette machine virtuelle est un système d'exploitation complet dans le cloud et sera utilisée pour exécuter l'interpréteur IPython Notebook. Azure peut exécuter à la fois des machines virtuelles Linux et Windows et nous allons étudier la configuration d'IPython sur les deux types de machines.

### Machine virtuelle Linux

Suivez les instructions se trouvant [ici](/en-us/manage/linux/tutorials/virtual-machine-from-gallery/) pour créer une machine virtuelle de la distribution *OpenSUSE* ou *Ubuntu*. Ce didacticiel utilise OpenSUSE 12.3 et Ubuntu Server 13.04. Nous présumerons que le nom d'utilisateur par défaut est *azureuser*.

### Machine virtuelle Windows

Suivez les instructions se trouvant [ici](/en-us/manage/windows/tutorials/virtual-machine-from-gallery/) pour créer une machine virtuelle de la distribution *Windows Server 2012 Datacenter*. Dans ce didacticiel, nous présumerons que le nom d'utilisateur est *azureuser*.

Création d'un point de terminaison pour l'interpréteur IPython Notebook
-----------------------------------------------------------------------

Cette étape s'applique à la fois à la machine virtuelle Linux et à la machine virtuelle Windows. Ultérieurement, nous configurerons IPython pour exécuter son serveur d'interpréteur sur le port 9999. Pour rendre ce port disponible publiquement, nous devons créer un point de terminaison dans le portail de gestion Azure. Celui-ci ouvre un port dans le pare-feu Azure et mappe le port public (HTTPS, 443) sur le port privé de la machine virtuelle (9999).

Pour créer un point de terminaison, accédez au tableau de bord de la machine virtuelle, cliquez sur « Points de terminaison », puis sur « Ajouter un point de terminaison » (nommé `ipython_nb` dans cet exemple). Sélectionnez TCP comme protocole, 443 pour le port public et 9999 pour le port privé :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-005.png)

Après cette étape, l'onglet « Points de terminaison » du tableau de bord apparaîtra ainsi :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-azure-linux-006.png)

Installation des logiciels requis sur la machine virtuelle
----------------------------------------------------------

Pour exécuter l'interpréteur IPython Notebook sur votre machine virtuelle, vous devez tout d'abord installer IPython et ses dépendances.

### Linux (OpenSUSE)

Pour installer IPython et ses dépendances, utilisez le protocole SSH dans la machine virtuelle Linux et procédez comme suit.

Installez [NumPy](http://www.numpy.org/ "NumPy"), [Matplotlib](http://matplotlib.sourceforge.net/ "Matplotlib"), [Tornado](http://www.tornadoweb.org/ "Tornado") et les autres dépendances IPython en procédant comme suit :

    sudo zypper install python-matplotlib
    sudo zypper install python-tornado
    sudo zypper install ipython

### Linux (Ubuntu)

Pour installer IPython et ses dépendances, utilisez le protocole SSH dans la machine virtuelle Linux et procédez comme suit.

Installez [NumPy](http://www.numpy.org/ "NumPy"), [Matplotlib](http://matplotlib.sourceforge.net/ "Matplotlib"), [Tornado](http://www.tornadoweb.org/ "Tornado") et les autres dépendances IPython en procédant comme suit :

    sudo apt-get install python-matplotlib
    sudo apt-get install python-tornado
    sudo apt-get install ipython
    sudo apt-get install ipython-notebook

### Windows

Pour installer IPython et ses dépendances sur la machine virtuelle Windows, utilisez le Bureau à distance pour la connexion à la machine virtuelle. Puis procédez comme suit, en utilisant Windows PowerShell pour exécuter toutes les actions de ligne de commande.

**Remarque** : afin de pouvoir télécharger à l'aide d'Internet Explorer, vous devez modifier certains paramètres de sécurité. Depuis **Gestionnaire de serveur**, cliquez sur **Serveur local**, puis sur **Configuration de sécurité renforcée d'Internet Explorer**, puis désactivez-la pour les administrateurs. Vous pourrez la réactiver une fois que vous aurez terminé l'installation d'IPython.

1.  Installez Python 2.7.5 (32 bits) depuis [python.org](http://www.python.org/download). Vous devrez également ajouter `C:\Python27` et `C:\Python27\Scripts` à votre variable d'environnement `PATH`.

2.  Installez distribute en téléchargeant le fichier **distribute\_setup.py** depuis [python-distribute.org](http://python-distribute.org/), puis en exécutant la commande :

        python distribute_setup.py

3.  Installez [Tornado](http://www.tornadoweb.org/ "Tornado") et [PyZMQ](https://github.com/zeromq/pyzmq "PyZMQ") en exécutant les commandes :

        easy_install tornado
        easy_install pyzmq

4.  Téléchargez et installez [NumPy](http://www.numpy.org/ "NumPy") en utilisant le programme d'installation binaire `.exe` disponible sur leur site Web. Lors de la rédaction de ce document, la dernière version était **numpy-1.7.1-win32-superpack-python2.7.exe**.

5.  Téléchargez et installez [Matplotlib](http://matplotlib.sourceforge.net/ "Matplotlib") en utilisant le programme d'installation binaire `.exe` disponible sur leur site Web. Lors de la rédaction de ce document, la dernière version était **matplotlib-1.2.1.win32-py2.7.exe**.

6.  Téléchargez et installez OpenSSL. Vous pouvez trouver les versions Windows d'OpenSSL à la page <http://slproweb.com/products/Win32OpenSSL.html>.
    -   Si vous installez une version **Légère**, vous devez également installer **Visual C++ 2008 Redistributable** (également disponible depuis cette page).
    -   Vous devrez ajouter `C:\OpenSSL-Win32\bin` à votre variable d'environnement `PATH`.

    > [WACOM.NOTE] Lors de l'installation d'OpenSSL, utilisez la version 1.0.1g ou supérieure, car elle inclut un correctif pour la faille de sécurité Heartbleed.

7.  Installez l'IPython en utilisant la commande :

        easy_install ipython

8.  Ouvrez un port dans le pare-feu Windows. Sur Windows Server 2012, le pare-feu bloque les connexions entrantes par défaut. Pour ouvrir le port 9999, procédez comme suit :

    -   Démarrez **Pare-feu Windows avec sécurité avancée** dans l'écran Démarrer.

    -   Cliquez sur **Règles de trafic entrant** dans le panneau de gauche.

    -   Cliquez sur **Nouvelle règle...** dans le panneau Actions.

    -   Dans l'Assistant Nouvelle règle de trafic entrant, sélectionnez **Port**.

    -   Dans l'écran suivant, sélectionnez **TCP** et saisissez **9999** dans **Ports locaux spécifiques**.

    -   Acceptez les valeurs par défaut, donnez à la règle un nom et cliquez sur Terminer.

### Configuration de l'interpréteur IPython Notebook

Ensuite, nous configurerons l'interpréteur IPython Notebook. La première étape consiste à créer un profil de configuration IPython personnalisé afin d'encapsuler les informations de configuration :

    ipython profile create nbserver

Ensuite, nous utilisons la commande `cd` pour accéder au répertoire de profil afin de créer notre certificat SSL et de modifier le fichier de configuration des profils.

Sur Linux (OpenSUSE) :

    cd ~/.config/ipython/profile_nbserver/

Sur Linux (Ubuntu) :

    cd ~/.ipython/profile_nbserver/

Sur Windows :

    cd \users\azureuser\.ipython\profile_nbserver

Sur les deux plateformes, créez le certificat SSL comme suit :

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Notez que puisque nous créons un certificat SSL auto-signé, lors de la connexion à l'interpréteur, votre navigateur affiche un message d'avertissement. Pour une utilisation à long terme, vous souhaiterez utiliser un certificat correctement signé associé à votre organisation. Comme la gestion des certificats ne figure pas dans cette démonstration, nous nous en tiendrons à un certificat auto-signé pour l'instant.

En plus de l'utilisation d'un certificat, vous devez également fournir un mot de passe pour protéger votre interpréteur contre toute utilisation non autorisée. Pour des raisons de sécurité, IPython utilise des mots de passe chiffrés dans son fichier de configuration. De ce fait, vous devez tout d'abord chiffrer votre mot de passe. Pour ce faire, IPython fournit un utilitaire ; à l'exécution d'une invite de commandes :

    python -c "import IPython;print IPython.lib.passwd()"

Vous serez invité à fournir un mot de passe et une confirmation, puis le mot de passe s'affichera comme suit :

    Enter password: 
    Verify password: 
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Ensuite, nous modifierons le fichier de configuration du profil, qui est le fichier `ipython_notebook_config.py` du répertoire de profil dans lequel vous vous trouvez. Le fichier comporte plusieurs champs et, par défaut, tous sont commentés. Vous pouvez ouvrir ce fichier avec l'éditeur de texte de votre choix et vous devez vous assurer qu'il contient au moins le contenu suivant :

    c = get_config()

    # Cela commence toujours la prise en charge du tracé avec matplotlib
    c.IPKernelApp.pylab = 'inline'

    # Vous devez fournir le chemin vers le fichier de certificat.

    # Si vous utilisez une machine virtuelle Linux (OpenSUSE) :
    c.NotebookApp.certfile = u'/home/azureuser/.config/ipython/profile_nbserver/mycert.pem'

    # Si vous utilisez une machine virtuelle Linux (Ubuntu) :
    c.NotebookApp.certfile = u'/home/azureuser/.ipython/profile_nbserver/mycert.pem'

    # Et si vous utilisez une machine virtuelle Windows :
    c.NotebookApp.certfile = r'C:\Users\azureuser\.ipython\profile_nbserver\mycert.pem'

    # Créez votre propre mot de passe comme indiqué ci-dessus
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Détails du réseau et du navigateur. Nous utilisons un port fixe (9999) donc il correspond
    # à notre configuration Azure, où nous avons autorisé le trafic sur ce port

    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### Exécution de l'interpréteur IPython Notebook

À présent, nous sommes prêts à démarrer l'interpréteur IPython. Pour ce faire, accédez au répertoire où vous voulez stocker les interpréteurs et démarrez le serveur IPython Notebook :

    ipython notebook --profile=nbserver

Vous devez à présent être en mesure d'accéder à votre interpréteur IPython Notebook à l'adresse `https://[nom que vous avez choisi].cloudapp.net`.

Lorsque vous accédez pour la première fois à votre interpréteur, la page de connexion demande votre mot de passe :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-001.png)

Une fois que vous êtes connecté, « IPython Notebook Dashboard » s'affiche. Il s'agit du centre de commande de toutes les opérations de l'interpréteur. À partir de cette page, vous pouvez créer des interpréteurs, ouvrir ceux existants etc. :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-002.png)

Si vous cliquez sur le bouton « New Notebook », une page d'ouverture s'affiche comme suit :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-003.png)

La zone marquée par une invite `In []:` est la zone d'entrée. Vous pouvez y saisir tout code Python valide qui s'exécutera lorsque vous appuyez sur les touches `Maj-Entrée` ou cliquez sur l'icône « Lire » (triangle pointant vers la droite dans la barre d'outils).

Puisque nous avons configuré l'interpréteur pour lancer la prise en charge de NumPy et matplotlib automatiquement, vous pouvez même produire des figures, par exemple :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-004.png)

Un puissant paradigme : documents de calcul dynamique avec des supports puissants
---------------------------------------------------------------------------------

L'interpréteur lui-même doit sembler très naturel d'utilisation pour toute personne ayant utilisé Python et un traitement de texte car, par certains aspects, il s'agit d'un mélange des deux : vous pouvez exécuter des blocs de code Python mais vous pouvez également conserver des notes et autres textes en modifiant le style d'une cellule de « Code » à « Texte » en utilisant le menu déroulant de la barre d'outils :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-005.png)

Mais il s'agit bien plus que d'un traitement de texte, car l'interpréteur IPython permet l'association de calculs et de supports puissants (texte, graphiques, vidéos et, virtuellement, tout ce qu'un navigateur Web moderne peut afficher). Par exemple, vous pouvez associer des vidéos d'explication à des calculs à des fins de formation :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-006.png)

ou intégrer des sites Web externes, qui restent actifs et utilisables, dans un fichier de l'interpréteur :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-007.png)

Et avec la puissance des nombreuses bibliothèques d'excellence de Python pour le calcul scientifique et technique, un simple calcul peut être effectué avec la même facilité qu'une analyse complexe de réseau, le tout dans un même environnement :

![Capture d'écran](./media/virtual-machines-python-ipython-notebook/ipy-notebook-008.png)

Ce paradigme d'association de la puissance du Web avec le calcul dynamique offre de nombreuses possibilités et est idéalement approprié au cloud. L'interpréteur peut être utilisé :

-   en tant que bloc-notes de calcul afin d'enregistrer le travail exploratoire relatif à un problème ;

-   pour partager les résultats avec des collègues, sous la forme d'un calcul « dynamique » ou aux formats de sortie papier (HTML, PDF) ;

-   pour distribuer et présenter des supports de formation en direct impliquant le calcul afin que les participants puissent immédiatement utiliser le code réel, le modifier et le réexécuter interactivement ;

-   pour fournir d'autres « papiers exécutables » qui présentent les résultats de recherches d'une manière qui peut être immédiatement reproduite, validée et étendue par d'autres ;

-   en tant que plateforme pour le calcul collaboratif : plusieurs utilisateurs peuvent se connecter au même serveur d'interpréteur afin de partager une session de calcul en direct ;

-   et bien plus...

Si vous accédez au référentiel de code source IPython, vous trouverez un répertoire complet avec des [exemples d'interpréteur](https://github.com/ipython/ipython/tree/master/examples/notebooks), que vous pouvez télécharger, puis expérimenter avec votre propre machine virtuelle Azure IPython. Téléchargez simplement les fichiers `.ipynb` depuis le site et chargez-les sur le tableau de bord de la machine virtuelle Azure de l'interpréteur (ou téléchargez-les directement dans la machine virtuelle).

Conclusion
----------

L'interpréteur IPython Notebook offre une interface haute performance pour accéder interactivement à la puissance de l'écosystème Python sur Azure. Il couvre une large gamme de cas d'utilisation, notamment les simples exploration et apprentissage de Python, l'analyse et la visualisation des données, le calcul de simulation et parallèle. Les documents d'interpréteur résultant contiennent un enregistrement complet des calculs qui sont effectués et peuvent être partagés avec d'autres utilisateurs IPython. L'interpréteur IPython Notebook peut être utilisé comme application locale mais est idéalement adapté pour des déploiements dans le cloud sur Azure.

Les fonctionnalités principales d'IPython sont également disponibles dans Visual Studio par l'intermédiaire des [outils Python pour Visual Studio](http://pytools.codeplex.com) (PTVS). PTVS est un plug-in open source gratuit de Microsoft qui transforme Visual Studio en environnement de développement Python avancé qui inclut un éditeur avancé avec IntelliSense, ainsi qu'une intégration de débogage, de profilage et de calcul parallèle.

