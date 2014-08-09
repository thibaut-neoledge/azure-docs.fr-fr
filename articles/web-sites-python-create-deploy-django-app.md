<properties linkid="develop-python-web-site-with-django" urlDisplayName="Web Sites with Django" pageTitle="Python Web Sites with Django - Azure tutorial" metaKeywords="Azure django, django website" description="A tutorial that introduces you to running a Python web site on Azure." metaCanonical="" services="web-sites" documentationCenter="Python" title="Creating Web Sites with Django" authors="" solutions="" manager="" editor="" />

Création de sites Web avec Django
=================================

Ce didacticiel décrit la prise en main de l'exécution de Python sur Sites Web Azure. Sites Web Azure fournit un hébergement gratuit limité ainsi qu'un déploiement rapide. Vous pouvez maintenant également utiliser Python. À mesure que votre application croît, vous pouvez passer à un hébergement payant, et vous pouvez également intégrer l'application à tous les autres services Azure.

Ce didacticiel présente le déploiement d'une application basée sur l'infrastructure Web Django. Il décrit la procédure à suivre pour déployer votre application ainsi que les bibliothèques requises, y compris Django. Vous allez placer tous ces éléments dans un référentiel Git qui facilite et accélère l'envoi de mises à jour vers votre site Web. Enfin, vous allez configurer le nouveau site via Azure pour qu'il puisse exécuter votre application Python.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Ce didacticiel utilise Python 2.7 et Django 1.4. Vous pouvez les obtenir par vous-même ou les installer simplement et rapidement à l'aide du lien vers Windows Installer sur [http://www.windowsazure.com/fr-fr/develop/python/](http://www.windowsazure.com/fr-fr/develop/python/).

**Remarque** : le gestionnaire wfastcgi et Python 2.7 sont désormais préinstallés sur les sites Web Azure. Toutefois, les infrastructures Web comme Django ne sont pas incluses. Vous pouvez toujours utiliser un autre interpréteur Python si vous préférez. Vous devez simplement l'inclure dans le référentiel Git et configurer le site Web pour utiliser cet interpréteur plutôt que l'interpréteur Python 2.7 déjà installé.

Vous devez aussi installer une option de déploiement pour l'envoi du site sur Azure. Plusieurs outils de déploiement sont disponibles, mais ce didacticiel utilise Git. Nous vous recommandons [msysgit](http://code.google.com/p/msysgit/).

**Remarque** : la publication TFS n'est actuellement pas prise en charge pour les projets Python.

Une fois que Python, Django et Git sont installés, vous avez tout ce qu'il vous faut pour commencer.

Création d'un site Web sur le portail
-------------------------------------

La première étape à suivre pour créer votre application consiste à créer le site Web via le portail de gestion Azure. Pour ce faire, vous devez vous connecter au portail et cliquer sur le bouton NEW dans le coin inférieur gauche. Une fenêtre s'affiche. Cliquez sur **Quick Create**, entrez une URL, puis sélectionnez **Create Web Site**.

![](./media/web-sites-python-create-deploy-django-app/django-ws-003.png)

Le site est configuré rapidement. Ensuite, vous allez ajouter la prise en charge de la publication via Git. Pour ce faire, choisissez **Set up deployment from source control**.

![](./media/web-sites-python-create-deploy-django-app/django-ws-004.png)

Dans la boîte de dialogue **Set up deployment**, faites défiler vers le bas et sélectionnez l'option **Local Git**. Cliquez sur la flèche droite pour continuer.

![](./media/web-sites-python-create-deploy-django-app/django-ws-005.png)

Après avoir configuré la publication Git, une page s'affiche pendant un moment pour vous indiquer que le référentiel est en cours de création. Lorsque le référentiel est prêt, vous êtes dirigé vers l'onglet Déploiements. Celui-ci inclut des instructions sur la connexion.

![](./media/web-sites-python-create-deploy-django-app/django-ws-006.png)

Développement d'un site Web
---------------------------

Maintenant que votre référentiel Git est créé dans Azure, vous pouvez commencer à remplir le site Web à partir de votre ordinateur local. La première étape consiste à cloner le site vide existant à l'aide de l'URL fournie :

![](./media/web-sites-python-create-deploy-django-app/django-ws-007.png)

Vous êtes alors prêt à configurer l'inscription avec le site Web. Vous devez faire ce qui suit :

1.  Incluez la bibliothèque Django et les autres bibliothèques que vous allez utiliser pour exécuter le site Web.
2.  Incluez le code d'application Django.

Commencez par inclure la bibliothèque Django. Pour ce faire, créez un répertoire nommé site-packages, puis copiez-y la version installée de Django à l'aide de ces commandes :

    mkdir site-packages
    cd site-packages
    xcopy /s C:\Python27\lib\site-packages\* .

Ces commandes copient toutes les bibliothèques situées dans site-packages, y compris Django. Si des bibliothèques ne sont pas utilisées par votre site Web, libre à vous de les supprimer.

![](./media/web-sites-python-create-deploy-django-app/django-ws-008.png)

Ensuite, créez votre application Django initiale. Vous pouvez le faire à partir de la ligne de commande comme pour créer une autre application Django ou en utilisant les [outils Python pour Visual Studio](http://pytools.codeplex.com/) pour créer le projet. Les deux options sont présentées ici.

**Option 1 :** pour créer le projet à partir de la ligne de commande, exécutez la commande suivante. Celle-ci crée l'application Django dans le dossier DjangoApplication :

     C:\Python27\python.exe -m django.bin.django-admin startproject DjangoApplication

![](./media/web-sites-python-create-deploy-django-app/django-ws-010.png)

**Option 2 :**
 Vous pouvez aussi créer votre site à l'aide des outils Python pour Visual Studio. Une fois que vous avez installé les outils Python, démarrez Visual Studio, puis sélectionnez **Fichier**-\>**Nouveau projet**. Sous **Autres langages**, explorez les projets Python et sélectionnez **Application Django**. Entrez **DjangoApplication** comme nom du projet, puis veillez à ce que l'option **Créer le répertoire pour la solution** soit désactivée pour obtenir exactement la même structure de répertoires que lorsque vous créez une application Django à partir de la ligne de commande. Cette option configure une solution Visual Studio et un fichier projet qui vous donnent une excellente expérience de développement local incluant le débogage des modèles et IntelliSense.

![](./media/web-sites-python-create-deploy-django-app/django-ws-011.png)

Maintenant, vous devez simplement ajouter tous les fichiers que vous venez d'ajouter et envoyer le site sur Git. Pour ce faire, exécutez les commandes suivantes :

    git add DjangoApplication site-packages
    git commit -m "Initial site"
    git remote add azure https://dinov@pythonwebsite.scm.azurewebsites.net/PythonWebSite.git
    git push azure master

La première commande ajoute les fichiers non suivis pour qu'ils soient suivis. La deuxième commande valide les fichiers que vous venez d'ajouter au référentiel. La troisième commande ajoute un référentiel distant nommé *azure* pour votre référentiel. La dernière commande prend les modifications et les envoie sur le référentiel distant. Elle démarre également le déploiement. Une fois que cela est fait, vous devez voir un résultat semblable à ceci :

![](./media/web-sites-python-create-deploy-django-app/django-ws-013.png)

Après avoir effectué la commande push, vous voyez le portail Azure s'actualiser et afficher le déploiement actif :

![](./media/web-sites-python-create-deploy-django-app/django-ws-014.png)

Configuration d'un site Web
---------------------------

Maintenant, vous devez configurer le site Web pour qu'il connaisse votre projet Django et qu'il utilise le gestionnaire wfastcgi. Pour ce faire, cliquez sur l'onglet Configurer en haut de l'écran, puis faites défiler jusqu'au milieu de la page qui contient les sections **app settings** et **handler mappings**.

L'ensemble des paramètres définis ici se transforme en variables d'environnement pendant la requête réelle. Cela signifie que vous pouvez les utiliser pour configurer la variable d'environnement DJANGO\_SETTINGS\_MODULE, ainsi que PYTHONPATH et WSGI\_HANDLER. Si votre application contient d'autres valeurs de configuration, vous pouvez les attribuer ici et les prélever de l'environnement. Parfois, vous pouvez définir un chemin d'accès à un fichier de votre site Web. Par exemple, vous pouvez faire cela pour PYTHONPATH. Lors de l'exécution en tant que site Web Azure, votre site Web est situé dans **D:\\home\\site\\wwwroot\**, pour que vous puissiez l'utiliser à n'importe quel emplacement où il est nécessaire d'avoir un chemin complet vers un fichier sur le disque.

Pour configurer une application Django, vous devez créer trois variables d'environnement. La première variable est DJANGO\_SETTINGS\_MODULE, qui fournit le nom du module de l'application Django utilisé pour la configuration. La deuxième variable est la variable d'environnement PYTHONPATH spécifiant le package dans lequel le module de paramètres se trouve. La troisième variable est WSGI\_HANDLER. Il s'agit d'un nom de module/package, suivi de l'attribut du module à utiliser (par exemple, mypackage.mymodule.handler). Ajoutez des parenthèses pour indiquer que l'attribut doit être appelé. Ainsi, configurez ces variables comme suit :

    DJANGO_SETTINGS_MODULE    DjangoApplication.settings
    PYTHONPATH                D:\home\site\wwwroot\DjangoApplication;D:\home\site\wwwroot\site-packages
    WSGI_HANDLER              django.core.handlers.wsgi.WSGIHandler()

![](./media/web-sites-python-create-deploy-django-app/django-ws-015.png)

Vous devez maintenant configurer le mappage de votre gestionnaire. Pour cela, inscrivez le gestionnaire pour toutes les extensions, à l'aide du chemin d'accès à l'interpréteur Python et du chemin d'accès au script wfastcgi.py :

    EXTENSION                 *
    SCRIPT PROCESSOR PATH     D:\python27\python.exe
    ADDITIONAL ARGUMENTS      D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-create-deploy-django-app/django-ws-016.png)

À ce stade, vous pouvez cliquer sur le bouton **Enregistrer** en bas.

Enfin, revenez au tableau de bord. Accédez à **SITE URL** à gauche. Cliquez sur le lien et ouvrez le nouveau site Django qui doit ressembler à ceci :

![](./media/web-sites-python-create-deploy-django-app/django-ws-017.png)

Étapes suivantes
----------------

À partir d'ici, vous pouvez poursuivre le développement de votre application Django à l'aide des outils que vous utilisez déjà. Si vous utilisez les [outils Python pour Visual Studio](http://pytools.codeplex.com/) pour le développement, vous voudrez sans doute installer [VisualGit](http://code.google.com/p/visualgit/) pour obtenir l'intégration du contrôle du code source à partir de Visual Studio.

Votre application peut comporter des dépendances au-delà de Python et Django. Si vous avez installé Python à l'aide du programme d'installation <http://www.windowsazure.com/fr-fr/develop/python/>, PIP est déjà installé et peut être utilisé pour ajouter rapidement de nouvelles dépendances. Par exemple, pour installer le Natural Language Toolkit et toutes ses dépendances, tapez :

    pip install nltk

Vous devez alors mettre à jour le répertoire site-packages en copiant les fichiers à partir de C:\\Python27\\Lib\\site-packages dans votre répertoire local site-packages.

Après la copie des fichiers, exécutez la commande **git status** pour afficher les fichiers nouvellement ajoutés, puis la commande **git add** suivie de la commande **git commit** pour valider les modifications apportées au référentiel. Enfin, vous pouvez exécuter une commande **git push** qui déploie le site Web mis à jour sur Azure.

Maintenant, vous pouvez accéder au répertoire DjangoApplication et utiliser manage.py comme vous le faites habituellement pour commencer à ajouter de nouvelles applications à votre projet Django.

