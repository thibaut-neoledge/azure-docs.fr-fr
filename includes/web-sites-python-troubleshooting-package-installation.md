Il est possible que certains packages ne soient pas installés via pip sous Azure.  Cela peut signifier simplement que le package n’est pas disponible dans l’index de packages Python.  Ou qu’un compilateur est nécessaire (aucun compilateur n’est disponible sur l’ordinateur exécutant l’application web dans Azure App Service).

Dans cette section, nous étudierons les différentes façons de résoudre ce problème.

### <a name="request-wheels"></a>Demande de roues
Si l’installation du package nécessite un compilateur, tentez de contacter le propriétaire du package afin qu’il mette à disposition des roues pour le package.

Avec [Microsoft Visual C++ Compiler 2013 pour Python 2.7][Microsoft Visual C++ Compiler for Python 2.7], il est désormais plus facile de créer des packages avec du code natif pour Python 2.7.

### <a name="build-wheels-requires-windows"></a>Création de roues (Windows requis)
Remarque : lorsque vous utilisez cette option, veillez à compiler le package à l’aide d’un environnement Python correspondant à la plateforme/version/architecture utilisée sur l’application web dans Azure Web Service (Windows/32 bits/2.7 ou 3.4).

Si l’installation du package échoue car un compilateur est requis, vous pouvez installer le compilateur sur votre machine locale et créer une roue pour le package, que vous inclurez ensuite dans votre référentiel.

Utilisateurs de Mac/Linux : si vous n’avez pas accès à un ordinateur Windows, consultez la rubrique [Créer une machine virtuelle exécutant Windows][Create a Virtual Machine Running Windows] pour savoir comment créer une machine virtuelle sur Azure.  Vous pouvez alors utiliser cette machine virtuelle pour générer des roues et les ajouter au référentiel, puis la supprimer si vous le souhaitez. 

Pour Python 2.7, vous pouvez installer [Microsoft Visual C++ Compiler 2013 pour Python 2.7][Microsoft Visual C++ Compiler for Python 2.7].

Pour Python 3.4, vous pouvez installer [Microsoft Visual C++ 2010 Express][Microsoft Visual C++ 2010 Express].

Pour générer des roues, vous aurez besoin du package de roue :

    env\scripts\pip install wheel

Vous allez utiliser `pip wheel` pour compiler une dépendance :

    env\scripts\pip wheel azure==0.8.4

Cette commande crée un fichier .whl dans le dossier \wheelhouse.  Ajoutez les fichiers de roue et ceux du dossier \wheelhouse à votre référentiel.

Modifiez votre fichier requirements.txt en ajoutant l’option `--find-links` en haut. Ceci indique à pip de rechercher une correspondance exacte dans le dossier local, avant de passer à l’index de packages Python.

    --find-links wheelhouse
    azure==0.8.4

Si vous souhaitez inclure toutes vos dépendances dans le dossier \wheelhouse et ne pas utiliser l’index de packages Python, forcez pip à ignorer l’index de packages en ajoutant `--no-index` en haut du fichier requirements.txt.

    --no-index

### <a name="customize-installation"></a>Installation personnalisée
Vous pouvez personnaliser le script de déploiement afin d’installer un package dans l’environnement virtuel, à l’aide d’un autre programme d’installation, comme easy\_install.  Reportez-vous à deploy.cmd pour obtenir un exemple commenté.  Assurez-vous que ces packages ne sont pas répertoriés dans le fichier requirements.txt, pour empêcher pip de les installer.

Ajoutez cette instruction au script de déploiement :

    env\scripts\easy_install somepackage

Vous pouvez également utiliser easy\_install pour effectuer l’installation à partir d’un exécutable d’installation (certains sont compatibles zip et easy\_install les prend en charge).  Ajoutez le programme d’installation à votre référentiel et appelez easy\_install en transmettant le chemin au fichier exécutable.

Ajoutez cette instruction au script de déploiement :

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Inclure l’environnement virtuel dans le référentiel (Windows requis)
Remarque : lorsque vous choisissez cette option, veillez à utiliser un environnement virtuel correspondant à la plateforme/version/architecture utilisée sur l’application web dans Azure App Service (Windows/32 bits/2.7 ou 3.4).

Si vous incluez l’environnement virtuel dans le référentiel, vous pouvez empêcher le script de déploiement d’effectuer la gestion de l’environnement virtuel sur Azure en créant un fichier vide :

    .skipPythonDeployment

Nous vous recommandons de supprimer l’environnement virtuel existant sur l’application, afin d’éviter que des fichiers soient conservés lorsque l’environnement virtuel était géré automatiquement.

[Create a Virtual Machine Running Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949
