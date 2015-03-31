<properties 
    pageTitle="Création d'une application web avec Python et Flask à l'aide de DocumentDB | Azure" 
    description="Découvrez comment utiliser DocumentDB pour stocker des données et y accéder à partir d'une application web Python et Flask (MVC) hébergée sur Azure." 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# Création d'une application web avec Python et Flask (MVC) à l'aide de DocumentDB
------------------------------------------------------------------------

Pour démontrer la manière dont les clients peuvent tirer parti d'Azure DocumentDB pour stocker et interroger des documents JSON, ce document propose une procédure pas à pas complète détaillant la création d'une application web de vote à l'aide d'Azure DocumentDB.

Cette procédure pas à pas indique comment utiliser le service DocumentDB fourni par Azure pour stocker les données et y accéder à partir d'une application web Python hébergée sur Azure. Elle suppose que avez déjà une expérience de l'utilisation de Python et du service Sites Web Azure.

Cette procédure pas à pas inclut les étapes suivantes :

1. Création et configuration d'un compte DocumentDB

2. Création d'une application Python MVC

3. Connexion à Azure DocumentDB pour une utilisation à partir de votre application web

4. Déploiement de l'application web vers Sites Web Azure

Dans ce didacticiel, vous allez créer une application de vote simple
vous permettant de voter lors d'un sondage.

![Alt text](./media/documentdb-python-application/image1.png)


## Conditions préalables

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

- Visual Studio 2013 (ou la version gratuite [Visual Studio Express][])

- Python Tools pour Visual Studio, disponible [ici][]

- Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, version 2.4 ou ultérieure, disponible [ici][1]

- Python 2.7 disponible [ici][2]

- Compilateur Microsoft Visual C++ pour Python 2.7 disponible [ici][3]

## Étape 1 : Création d'un compte de base de données DocumentDB

Pour configurer un compte de base de données DocumentDB dans Azure, ouvrez le [portail de gestion Azure][], puis cliquez sur la vignette Galerie Azure sur la page d'accueil ou sur " + " dans le coin inférieur gauche de l'écran.

![Alt text](./media/documentdb-python-application/image2.png)


Cette opération ouvre Azure Marketplace, où vous pouvez sélectionner l'un des nombreux services Azure disponibles. Dans Marketplace, sélectionnez " Données + analyses " dans la liste des catégories.

![Alt text](./media/documentdb-python-application/image3.png)

Recherchez ensuite documentdb et sélectionnez l'option DocumentDB.

![Alt text](./media/documentdb-python-application/image4.png)

Sélectionnez ensuite " Créer " en bas de l'écran.

![Alt text](./media/documentdb-python-application/image5.png)

Le panneau " Nouveau DocumentDB " s'ouvre. Vous pouvez y spécifier le nom, la région, l'échelle et le groupe de ressources, ainsi que d'autres paramètres de votre nouveau compte.

![Alt text](./media/documentdb-python-application/image6.png)

Une fois que vous avez terminé de fournir les valeurs de votre compte, cliquez sur " Créer " et le processus de configuration entame la création de votre compte de base de données.
Une fois le processus de configuration terminé, une notification doit s'afficher dans la zone des notifications du portail et la vignette de votre écran d'accueil (si vous avez choisi d'en créer une) est modifiée pour refléter l'action terminée.

![Alt text](./media/documentdb-python-application/image7.png)

Une fois la configuration terminée, un clic sur la vignette DocumentDB de l'écran d'accueil affiche le panneau principal correspondant au compte DocumentDB nouvellement créé.

![Alt text](./media/documentdb-python-application/image8.png)

À l'aide du bouton " Clés ", accédez à votre URL de point de terminaison et à la clé primaire. Copiez-les dans le Presse-papiers et gardez-les à portée de main. Nous les utiliserons dans l'application web que nous allons créer.


## Étape 2 : Création d'une application web Python Flask

Ouvrez Visual Studio, puis accédez successivement à Fichier -\> Nouveau projet -\> Python -\> Projet web Flask et nommez le projet **tutorial**. 

Si vous ne connaissez pas bien Flask, sachez qu'il s'agit d'une infrastructure web qui permet d'accélérer le développement d'applications dans Python. [Cliquez ici pour accéder aux didacticiels sur Flask][].

![Alt text](./media/documentdb-python-application/image9.png)

Un message vous demandera où installer les packages externes. Cliquez sur **Installer dans un environnement virtuel**. Veillez à utiliser Python 2.7 comme environnement de base. PyDocumentDB ne prend en effet pas Python 3.x en charge pour le moment.  Cela configure l'environnement virtuel Python requis pour votre projet.

![Alt text](./media/documentdb-python-application/image10.png)


## Étape 3 : Modification de l'application web Python Flask


### Ajout de packages Flask à votre projet

Une fois votre projet configuré, vous devez ajouter certains packages Flask requis, dont pydocumentdb, le package Python pour DocumentDB. Ouvrez le fichier appelé **requirements.txt** et remplacez le contenu par le suivant :

    flask==0.9
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup
    pydocumentdb>=0.9.4-preview

Cliquez avec le bouton droit sur **env**, puis cliquez sur **install from requirements.txt**

![Alt text](./media/documentdb-python-application/image11.png)

**Remarque :** dans de rares cas, la fenêtre de sortie peut afficher une erreur. Vous devez alors déterminer si l'erreur est liée au nettoyage. Il arrive que le nettoyage échoue mais que l'installation soit correctement effectuée (faites défiler la fenêtre de sortie vers le haut pour vérifier).
<a name="verify-the-virtual-environment"></a> Si cela se produit, vous pouvez continuer.


### Vérification de l'environnement virtuel

Vérifiez que l'installation est réussie. Démarrez le site web en appuyant sur **F5**. Cette action démarre le serveur de développement Flask, ainsi que votre navigateur web. La page suivante doit s'afficher :

![Alt text](./media/documentdb-python-application/image12.png)

### Création de base de données, de collection et de définition de document

Ajoutez un fichier Python en cliquant avec le bouton droit sur le dossier appelé **tutorial** dans l'Explorateur de solutions.  Nommez le fichier **forms.py**.  Nous créons notre application de sondage.

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Ajoutez les instructions import requises à views.py

Ajoutez les instructions import suivantes au début de **views.py**. Elles vont importer les packages Flask et PythonSDK de DocumentDB.


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### Création de base de données, de collection et de document

Ajoutez le code suivant à **views.py**. Cela permet de créer la base de données utilisée par le formulaire. Ne supprimez pas le code existant dans **views.py**. Il vous suffit d'ajouter le code à la fin.

    @app.route('/create')
    def create():
        """Renders the contact page."""
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        # Attempt to delete the database.  This allows this to be used to recreate as well as create
        try:
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            client.DeleteDatabase(db['_self'])
        except:
            pass
    
        # Create database
        db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        # Create collection
        collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        # Create document
        document = client.CreateDocument(collection['_self'],
            { 'id': config.DOCUMENTDB_DOCUMENT,
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': config.DOCUMENTDB_DOCUMENT })
    
        return render_template(
            'create.html', 
            title='Create Page', 
            year=datetime.now().year,
            message='You just created a new database, collection, and document.  Your old votes have been deleted')


### Lecture de la base de données, de la collection et du document et envoi du formulaire

Ajoutez le code suivant à **views.py**. Il se charge de la configuration du formulaire, de la lecture de la base de données, de la collection et du document. Ne supprimez pas le code existant dans **views.py**. Il vous suffit d'ajouter le code à la fin.
    
    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = VoteForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            # Read databases and take first since id should not be duplicated.
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            # Read collections and take first since id should not be duplicated.
            coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            # Read documents and take first since id should not be duplicated.
            doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            # Take the data from the deploy_preference and increment our database
            doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            # Create a model to pass to results.html
            class VoteObject:
                choices = dict()
                total_votes = 0
    
            vote_object = VoteObject()
            vote_object.choices = {
                "Web Site" : doc['Web Site'],
                "Cloud Service" : doc['Cloud Service'],
                "Virtual Machine" : doc['Virtual Machine']
            }
            vote_object.total_votes = sum(vote_object.choices.values())
    
            return render_template(
                'results.html', 
                year=datetime.now().year, 
                vote_object = vote_object)
    
        else :
            return render_template(
                'vote.html', 
                title = 'Vote',
                year=datetime.now().year,
                form = form)


### Création des fichiers html

Sous le dossier Modèles, ajoutez les fichiers html suivants : create.html, results.html, vote.html.

Ajoutez le code suivant à **create.html**. Il se charge de l'affichage du message indiquant la création d'une base de données, d'une collection et d'un document.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

Ajoutez le code suivant à **results.html**. Il se charge de l'affichage des résultats du sondage.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
    <br />

    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
        <div class="col-sm-5">
            <div class="progress">
                <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    {{vote_object.choices[choice]}}
                </div>
            </div>
        </div>
    </div>
    {% endfor %}

    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}

Ajoutez le code suivant à **vote.html**. Il se charge de l'affichage du sondage et de l'approbation des votes. Lors de l'enregistrement des votes, le contrôle est transmis à views.py où nous allons identifier le vote associé et ajouter le document correspondant.

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

Remplacez le contenu de **index.html** par le code suivant. Ce code définit une page d'accueil pour votre application

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### Ajout d'un fichier de configuration et modification de \_\_init\_\_.py

Cliquez avec le bouton droit sur le projet nommé Tutorial et ajoutez le fichier **config.py**.
Cette configuration est requise par les formulaires dans Flask. Vous pouvez l'utiliser pour fournir une clé secrète. Cela n'est cependant pas nécessaire dans le cadre de ce didacticiel. Ajoutez le code suivant à config.py.   Modifiez les valeurs de **DOCUMENTDB\_HOST** et **DOCUMENTDB\_KEY**.

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


De même, remplacez le contenu de **\_\_init\_\_.py** par le contenu suivant.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Une fois ces étapes terminées, votre Explorateur de solutions doit
ressembler à ceci :

![Alt text](./media/documentdb-python-application/image15.png)


## Étape 4 : Exécution locale de l'application

Appuyez sur F5 ou sur le bouton Démarrer dans Visual Studio. Votre écran doit afficher le contenu suivant.

![Alt text](./media/documentdb-python-application/image16.png)

Cliquez sur " Créer/effacer la base de données de vote " pour générer la base de données.

![Alt text](./media/documentdb-python-application/image17.png)

Cliquez ensuite sur " Vote " et sélectionnez votre option.

![Alt text](./media/documentdb-python-application/image18.png)

Chaque vote associé augmentera le compteur adéquat.

![Alt text](./media/documentdb-python-application/image19.png)


## Étape 5 : Déploiement de l'application dans Sites Web Azure

Maintenant que l'application terminée fonctionne correctement avec DocumentDB, nous allons la déployer vers Sites Web Azure. Cliquez avec le bouton droit sur le projet dans l'Explorateur de solutions (assurez-vous de ne pas l'exécuter localement), puis sélectionnez Publier.  Sélectionnez ensuite Sites Web Microsoft Azure.

![Alt text](./media/documentdb-python-application/image20.png)


Configurez votre site web Azure en fournissant vos informations d'identification et cliquez sur Publier.

![Alt text](./media/documentdb-python-application/image21.png)

En quelques secondes, Visual Studio achève la publication de votre application web et lance un navigateur dans lequel vous pouvez voir votre réalisation exécutée dans Azure !


## Étapes suivantes

Félicitations ! Vous venez de créer votre première application Python avec Azure DocumentDB et de la publier dans Sites Web Azure.


  [Cliquez ici pour accéder aux didacticiels sur Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [ici]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Portail de gestion Azure]: http://portal.azure.com
<!--HONumber=47-->
