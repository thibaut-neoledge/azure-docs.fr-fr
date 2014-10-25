<properties linkid="develop-python-django-with-visual-studio" urlDisplayName="Django with Visual Studio" pageTitle="Django with Visual Studio (Python) - Azure tutorial" metaKeywords="Azure Django web app, Azure Django virtual machine" description="A tutorial that teaches you how to build a Django web application hosted in an Azure virtual machine." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools for Visual Studio 1.5" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Création d'applications Django avec Python Tools for Visual Studio 1.5

**Remarque :** ce didacticiel est également encapsulé dans une [vidéo YouTube][].

**Remarque :** un [didacticiel plus récent et exhaustif][] relatif à PTVS 2.0 Beta est désormais disponible.

Il est très facile de développer pour Azure quand vous utilisez les outils disponibles.
Ce didacticiel suppose que vous n'avez jamais utilisé Azure.
À la fin de ce guide, vous aurez une application Django opérationnelle dans le cloud.

Vous apprendrez à effectuer les opérations suivantes :

-   création d'une application Django de base ;
-   exécution et débogage de votre application Django localement grâce au serveur de test Django ;
-   exécution de votre application Django localement dans l'émulateur de calcul ;
-   publication et republication de votre application dans Azure.

Dans ce didacticiel, vous allez créer une application web
Hello World simple. L'application sera hébergée dans une instance de rôle web
qui, pendant l'exécution dans Azure, est elle-même hébergée sur
un ordinateur virtuel dédié.

Voici une capture d'écran de l'application terminée :

![][]

[WACOM.INCLUDE [create-account-note][]]

## <span id="setup"></span> </a>Configuration de l'environnement de développement

Avant de commencer à développer votre application Azure, vous devez obtenir des outils et configurer votre environnement de développement. Pour plus de détails sur l'obtention et l'installation du Kit de développement logiciel (SDK) Azure pour Python, consultez la section [Installation de Python][].

**Remarque :** ce didacticiel nécessite Python 2.7 et Django 1.4. Ces versions sont comprises dans le Kit de développement logiciel (SDK) Azure pour Python actuel.

**Remarque :** le déploiement sur l'émulateur de calcul et/ou Azure nécessite la version complète de Visual Studio (l'interpréteur de commandes intégré n'est pas pris en charge).

## Création d'une application Django

Pour créer une application Django, lancez Visual Studio et créez un projet grâce à l'option **Fichier -\> Nouveau projet**. Recherchez l'onglet Python (disponible dans la zone du haut ou la zone Autres langages) et sélectionnez le modèle d'application Django :

![Modèles de nouveau projet Python][]

**Cliquez sur OK**. Vous avez créé votre première application Django.

![Visual Studio ouvert avec votre premier projet Django][]

Ensuite, commencez à développer la première application Django. Vous pouvez cliquer avec le bouton droit sur le nœud de module et sélectionner **Ajouter une application Django**... pour configurer une nouvelle application dans votre projet :

![Élément de menu d'ajout d'une nouvelle application][]

Vous pouvez ensuite entrer le nom de votre nouvelle application :

![Invite d'ajout de nom pour la nouvelle application][]

Après que vous avez saisi un nom pour l'application et cliqué sur **OK**, une nouvelle application est ajoutée à votre projet :

![Explorateur de solutions avec nouvelle application ajoutée][]

À présent, mettez à jour **settings.py** afin que votre application soit enregistrée. Cette action poussera Django à rechercher automatiquement les fichiers de modèle qui sont ajoutés dans votre répertoire Modèles d'applications. Ajoutez le nom de l'application dans la section INSTALLED\_APPS :

    'DjangoApplication.MyFirstApp',

![Ajout de l'application à views.py dans la section INSTALLED\_APPS][]

Ensuite, ajoutez du code à la partie **views.py** de l'application, afin de pouvoir renvoyer un fichier de modèle simple :

    from django.http import HttpResponse
    from django.template.loader import render_to_string
    def home(request):
        return HttpResponse(render_to_string(
                                            'index.html',
                                            {'content': 'Hello World'}
                                            ))

![Ajout de l'application à views.py dans la section INSTALLED\_APPS][1]

Ajoutez ensuite un fichier de modèle simple qui s'affichera lorsque vous accéderez à cette vue. Pour cela, cliquez avec le bouton droit sur le dossier Modèles et sélectionnez **Ajouter un nouvel élément** :

![Ajout d'un nouvel élément au dossier Modèles][]

Vous pouvez à présent sélectionner « Django HTML Template » dans la liste des modèles et entrer **index.html** en tant que nom de fichier :

![Ajout d'un nouvel élément au dossier Modèles][2]

Le modèle est ensuite ajouté au projet et ouvert. Vous pouvez voir ici le début d'une mise en surbrillance de syntaxe pour les balises de modèle :

![Modèle ajouté à l'explorateur de solutions][]

Vous pouvez ensuite commencer à mettre à jour le modèle pour modifier le code HTML affiché. Ce faisant, vous obtiendrez IntelliSense :

![Modèle IntelliSense pour filtres Django][]

Vous pouvez laisser la mise en majuscules activée ou non. Ce paramètre ne modifiera pas fortement les résultats de ce didacticiel. Enfin, vous devez simplement enregistrer votre vue avec les modèles URL dans **urls.py**. Ajoutez cette ligne dans **urlpatterns** :

    url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![Enregistrement de l'URL][]

## Exécution de votre application en local dans le serveur de test

À ce stade, vous avez créé votre toute première application Django. Vous pouvez à présent l'exécuter localement en **appuyant sur F5**.

![Django Hello World dans un navigateur et le serveur de test][]

Cette action démarrera votre interpréteur Python en exécutant le code **manage.py** de Django pour exécuter le serveur de test. Une fois le serveur de test correctement lancé, il ouvrira un navigateur web pour afficher le site web. Le lancement ayant été effectué avec F5, cet élément se lance sous le débogueur. Il est donc également possible de définir des points d'arrêt dans le code Python, dans le code de vue ou dans les fichiers de modèles eux-mêmes :

![Débogueur arrêté à un point d'arrêt de modèle][]

Vous pouvez à présent **cliquer sur le bouton Arrêter** et passer à l'exécution dans l'émulateur de calcul Azure.

## Exécution de votre application en local dans l'émulateur

Pour lancer l'exécution dans l'émulateur de calcul, vous devez simplement ajouter un projet de déploiement Azure à votre solution pour le projet Django.

**Remarque :** le déploiement sur l'émulateur de calcul et/ou Azure nécessite la version complète de Visual Studio (l'interpréteur de commandes intégré n'est pas pris en charge).

Pour cela, cliquez avec le bouton droit sur le nœud de projet Django dans l'Explorateur de solutions et sélectionnez **Add Azure Cloud Service Project** :

![Ajout d'un projet de déploiement][]

Après l'exécution de cette commande, vous verrez un projet nouvellement ajouté dans l'Explorateur de solutions :

![Après l'ajout d'un projet de déploiement][]

Ce nouveau projet est désormais également marqué comme étant le projet de démarrage de la solution. À ce stade, vous devrez **redémarrer Visual Studio en tant qu'administrateur** pour pouvoir lancer l'exécution dans l'émulateur de calcul. Cependant, vous pourrez ensuite **appuyer simplement sur F5**. L'application s'exécutera et se déploiera alors dans l'émulateur de calcul :

![Après l'ajout d'un projet de déploiement][3]

À présent, vous pouvez voir la même page Web, mais avec une URL légèrement différente. Vous pouvez également voir qu'aucun programme python.exe ne s'exécute sur le serveur de test Django. Au lieu de cela, vous exécutez Django via IIS grâce à une passerelle FastCGI qui est automatiquement incluse et configurée lorsque vous travaillez avec Visual Studio.

Lors de l'exécution dans l'émulateur de calcul, vous pouvez rapidement effectuer l'itération sur votre application : retournez dans Visual Studio, mettez à jour vos fichiers et actualisez le navigateur Web. Vous verrez immédiatement les résultats.

## Déploiement de l'application dans Azure

Vous pouvez désormais déployer le projet dans Azure. Pour cela, cliquez simplement avec le bouton droit sur le projet de déploiement Azure dans l'Explorateur de solutions et **sélectionnez Publier** :

![Menu application package][]

Après avoir sélectionné Publier, vous recevez une invite pour vous connecter à Azure. Vous pouvez importer vos informations d'identification existantes ou en configurer de nouvelles :

![Abonnement de package][]

Après avoir sélectionné les informations d'identification, l'écran Paramètres de publication Azure s'affiche. Vous pouvez sélectionner les différentes options sur le traitement de votre déploiement ou vous pouvez simplement **appuyer sur Publier** :

![Paramètres du package][]

Vous devez à présent attendre que l'application soit configurée et déployée.

![Déploiement du package][]

Une fois tous les éléments configurés, vous pouvez cliquer sur le lien situé sous le nom DNS pour afficher votre site web qui s'exécute dans le cloud :

![Votre application Django dans le cloud][]

  [vidéo YouTube]: http://www.youtube.com/watch?v=UsLti4KlgAY
  [didacticiel plus récent et exhaustif]: ../web-sites-python-create-deploy-django-app/
  []: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png
  [create-account-note]: ../includes/create-account-note.md
  [Installation de Python]: ../python-how-to-install/
  [Modèles de nouveau projet Python]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png
  [Visual Studio ouvert avec votre premier projet Django]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png
  [Élément de menu d'ajout d'une nouvelle application]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png
  [Invite d'ajout de nom pour la nouvelle application]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png
  [Explorateur de solutions avec nouvelle application ajoutée]: ./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png
  [Ajout de l'application à views.py dans la section INSTALLED\_APPS]: ./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png
  [1]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png
  [Ajout d'un nouvel élément au dossier Modèles]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png
  [2]: ./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png
  [Modèle ajouté à l'explorateur de solutions]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png
  [Modèle IntelliSense pour filtres Django]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png
  [Enregistrement de l'URL]: ./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png
  [Django Hello World dans un navigateur et le serveur de test]: ./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png
  [Débogueur arrêté à un point d'arrêt de modèle]: ./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png
  [Ajout d'un projet de déploiement]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png
  [Après l'ajout d'un projet de déploiement]: ./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png
  [3]: ./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png
  [Menu application package]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png
  [Abonnement de package]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png
  [Paramètres du package]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png
  [Déploiement du package]: ./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png
  [Votre application Django dans le cloud]: ./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png
