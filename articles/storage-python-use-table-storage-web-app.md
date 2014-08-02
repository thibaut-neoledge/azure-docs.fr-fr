<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Web App with Blob Storage" pageTitle="Python web app with table storage | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="A tutorial that teaches you how to create a Python web application using the Azure Client Libraries. Django is used as the web framework." metaCanonical="" services="storage" documentationCenter="Python" title="Python Web Application using Table Storage" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />

Application Web Python avec le stockage de tables
=================================================

Dans ce didacticiel, vous allez apprendre à créer une application qui utilise le stockage de tables avec les bibliothèques clientes Azure pour Python. S'il s'agit de votre première application Azure pour Python, vous pouvez consulter la page [Application Web Django Hello World](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) au préalable.

Pour ce guide, vous allez créer une application de liste de tâches Web que vous pouvez déployer sur Azure. La liste de tâches permet à un utilisateur d'extraire des tâches, d'en ajouter de nouvelles et de marquer celles qui sont terminées. Nous allons utiliser Django en tant qu'infrastructure Web.

Les éléments de tâches sont stockés dans Azure Storage, qui offre le stockage de données non structurées à tolérance de panne et haute disponibilité. Azure Storage inclut plusieurs structures de données dans lesquelles vous pouvez stocker des données et y accéder. Vous pouvez également exploiter les services de stockage à partir des API incluses dans le Kit de développement logiciel (SDK) Azure pour Python ou via les API REST. Pour plus d'informations, consultez la page [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx).

Vous apprendrez à effectuer les opérations suivantes :

-   utiliser le service de stockage de tables Azure.

Une capture d'écran de l'application terminée ressemblera à celle qui s'affiche ci-dessous (les éléments de tâches ajoutés seront différents) :

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Configuration de l'environnement de développement
-------------------------------------------------

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python](http://windowsazure.com/en-us/documentation/articles/python-how-to-install).

*Remarque pour Windows* : si vous avez utilisé le programme d'installation WebPI Windows, Django et les bibliothèques clientes sont déjà installés.

Création d'un compte de stockage dans Azure
-------------------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Création d'un projet Django
---------------------------

Voici les étapes de création de l'application :

-   Créez un projet Django par défaut nommé « TableserviceSample ».
-   À partir de la ligne de commande, à l'aide de la commande cd, accédez au répertoire dans lequel vous souhaitez stocker votre code, puis exécutez la commande suivante :

        django-admin.py startproject TableserviceSample

-   Ajoutez un nouveau fichier Python **views.py** au projet.
-   Ajoutez le code suivant à **views.py** pour importer la prise en charge nécessaire de Django :

        from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   Créez un dossier nommé **templates** sous le dossier **TableserviceSample/TableserviceSample**.
-   Modifiez les paramètres de l'application afin que vos modèles puissent être localisés. Ouvrez **settings.py** et ajoutez l'entrée suivante à INSTALLED\_APPS :

        'TableserviceSample',

-   Ajoutez un nouveau fichier de modèle Django **mytasks.html** au dossier **templates**, puis ajoutez-y le code suivant :

``` {}
    <html>
    <head><title></title></head>
    <body>
    <h2>My Tasks</h2> <br>
    <table border="1"> 
    <tr>
    <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
    {% for entity in entities %}
    <form action="update_task" method="GET">
    <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
    <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
    <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
    <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

    <td><input type="submit" value="Complete"></td>
    </tr>
    </form>
    {% endfor %}
    </table>
    <br>
    <hr>
    <table border="1">
    <form action="add_task" method="GET">
    <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
    <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
    <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
    <tr><td><input type="submit" value="add task"></input></td></tr>
    </form>
    </table>
    </body>
    </html>    
```

Importation du module de stockage windowsazure
----------------------------------------------

Ajoutez le code suivant en haut de **views.py** directement après les importations Django.

    from azure.storage import TableService

Obtention du nom de compte de stockage et de la clé d'accès
-----------------------------------------------------------

Ajoutez le code suivant à **views.py** juste après l'importation windowsazure, puis remplacez « youraccount » et « yourkey » par le nom et la clé réels du compte. Vous pouvez obtenir un nom de compte et une clé à partir du portail de gestion Azure.

    account_name = 'youraccount'
    account_key = 'yourkey'

Création de TableService
------------------------

Ajoutez le code suivant après **account\_name** :

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

Énumération de tâches
---------------------

Ajoutez la fonction list\_tasks à **views.py** :

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Ajout d'une tâche
-----------------

Ajoutez la fonction add\_task à **views.py** :

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Mise à jour de l'état de la tâche
---------------------------------

Ajoutez la fonction update\_task à **views.py** :

    def update_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        partition_key = name + category
        row_key = date
        table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

Mappage des URL
---------------

Vous devez à présent mapper les URL dans l'application Django. Ouvrez **urls.py** et ajoutez les mappages suivants à urlpatterns :

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

Exécution de l'application
--------------------------

-   Accédez au répertoire **TableserviceSample** si ce n'est pas déjà le cas, puis exécutez la commande :

    python manage.py runserver

-   Dans votre navigateur, accédez à : `http://127.0.0.1:8000/`. Remplacez 8000 par le numéro de port réel.

Vous pouvez désormais cliquer sur **Add Task** pour créer une tâche, puis cliquer sur le bouton **Complete** pour mettre à jour la tâche et définir son état sur « Yes ».

Exécution de l'application dans l'émulateur de calcul, publication et arrêt/suppression de votre application
------------------------------------------------------------------------------------------------------------

À présent que votre application s'exécute correctement sur le serveur Django intégré, vous pouvez la tester en la déployant sur l'émulateur Azure (Windows uniquement) et en la publiant sur Azure. Pour obtenir des instructions générales sur la procédure à suivre, consultez l'article [Application Web Hello World Django](http://windowsazure.com/en-us/documentation/articles/virtual-machines-python-django-web-app-windows-server) qui aborde ces étapes en détail.

Étapes suivantes
----------------

Maintenant que vous avez appris les principes de base du service de stockage de tables Azure, suivez ces liens pour découvrir des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   Accédez au blog de l'équipe Azure Storage : &lt;http://blogs.msdn.com/b/windowsazurestorage/&gt;

