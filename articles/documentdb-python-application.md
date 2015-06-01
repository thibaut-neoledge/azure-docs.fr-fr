<properties 
    pageTitle="Création d'une application web avec Python et Flask à l'aide de DocumentDB | Azure" 
    description="Découvrez comment utiliser DocumentDB pour stocker des données et y accéder à partir d'une application web Python et Flask (MVC) hébergée sur Azure." 
    services="documentdb" 
    documentationCenter="python" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="03/23/2015" 
    ms.author="crwilcox"/>

# Création d'une application web avec Python et Flask (MVC) à l'aide de DocumentDB
------------------------------------------------------------------------

Pour démontrer la manière dont les clients peuvent tirer parti d'Azure DocumentDB pour stocker et interroger des documents JSON, ce document propose un didacticiel complet qui explique comment créer une application web de vote à l'aide d'Azure DocumentDB.

Ce didacticiel indique comment utiliser le service DocumentDB fourni par Azure pour stocker les données et y accéder à partir d'une application web Python hébergée sur Azure. Il suppose que avez déjà une expérience de l'utilisation de Python et du service Sites Web Azure.

Ce didacticiel contient les sections suivantes :

1. Création et configuration d'un compte DocumentDB
2. Création d'une application Python MVC
3. Connexion à Azure DocumentDB pour une utilisation à partir de votre application web
4. Déploiement de l'application web vers Sites Web Azure

Dans ce didacticiel, vous allez créer une application de vote simple vous permettant de voter lors d'un sondage.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-python-application/image1.png)


## Conditions préalables

Avant de suivre les instructions de cet article, vérifiez que les éléments suivants sont installés :

- [Visual Studio 2013](http://www.visualstudio.com/) ou une version ultérieure, ou [Visual Studio Express], qui est la version gratuite.
- Python Tools pour Visual Studio, disponible [ici][]
- Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, version 2.4 ou ultérieure, disponible [ici][1]
- Python 2.7, disponible [ici][2]
- Compilateur Microsoft Visual C++ pour Python 2.7, disponible [ici][3]

## Étape 1 : Création d'un compte de base de données DocumentDB

Commençons par créer un compte DocumentDB. Si vous avez déjà un compte, vous pouvez passer à l'[étape 2 : Création d'une application web Python Flask](#Step-2:-Create-a-new-Python-Flask-Web-Application).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
Voyons à présent comment créer une application web Python Flask de A à Z.

## Étape 2 : Création d'une application web Python Flask

1. Ouvrez Visual Studio, puis cliquez sur **Fichier** -> **Nouveau projet** -> **Python** ->, **Projet web Flask**. Créez un projet nommé **tutorial**. 

	Si vous ne connaissez pas bien Flask, sachez qu'il s'agit d'une infrastructure web qui permet d'accélérer le développement d'applications dans Python. [Cliquez ici pour accéder aux didacticiels sur Flask][].

	![Screen shot of the New Project window in Vidual Studio with Python highlighted on the left, Flask Web Project selected in the middle, and the name tutorial in the Name box](./media/documentdb-python-application/image9.png)

2. Un message vous demandera où installer les packages externes. Cliquez sur **Installer dans un environnement virtuel**. Veillez à utiliser Python 2.7 comme environnement de base. PyDocumentDB ne prend en effet pas Python 3.x en charge pour le moment.  Cela configure l'environnement virtuel Python requis pour votre projet.

	![Screen shot of the tutorial - Python Tools for Visual Studio window](./media/documentdb-python-application/image10.png)


## Étape 3 : Modification de l'application web Python Flask

### Ajout de packages Flask à votre projet

Une fois votre projet configuré, vous devez ajouter certains packages Flask requis, dont pydocumentdb, le package Python pour DocumentDB. 

1. Ouvrez le fichier appelé **requirements.txt** et remplacez le contenu par le suivant.

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

2. Cliquez avec le bouton droit sur **env**, puis cliquez sur **install from requirements.txt**.

	![Screen shot showing env (Python 2.7) selected with Install from requirements.txt highlighted in the list](./media/documentdb-python-application/image11.png)

> [AZURE.NOTE] Dans de rares cas, la fenêtre de résultat peut afficher une erreur. Si cela se produit, vérifiez si l'erreur n'est pas liée au nettoyage. Parfois, le nettoyage peut échouer, alors que l'installation sera réussie (faites défiler le contenu de la fenêtre de résultat pour le vérifier). Si cela se produit, vous pouvez continuer.

### <a name="verify-the-virtual-environment"></a>Vérification de l'environnement virtuel

Vérifiez que l'installation est réussie. 

- Démarrez le site web en appuyant sur **F5**. Cette action démarre le serveur de développement Flask, ainsi que votre navigateur web. La page suivante doit s'afficher.

	![The empty flask project displayed in a browser](./media/documentdb-python-application/image12.png)

### Création des définitions pour la base de données, la collection et le document

Créons maintenant notre application de vote.

- Ajoutez un fichier Python en cliquant avec le bouton droit sur le dossier appelé **tutorial** dans l'Explorateur de solutions.  Nommez le fichier **forms.py**.  

    	from flask.ext.wtf import Form
    	from wtforms import RadioField

    	class VoteForm(Form):
        	deploy_preference  = RadioField('Deployment Preference', choices=[
            	('Web Site', 'Web Site'),
            	('Cloud Service', 'Cloud Service'),
            	('Virtual Machine', 'Virtual Machine')], default='Web Site')

### Ajoutez les instructions import requises à views.py

- Ajoutez les instructions import suivantes au début de **views.py**. Elles vont importer les packages Flask et PythonSDK de DocumentDB.

    	from forms import VoteForm
    	import config
    	import pydocumentdb.document_client as document_client
    

### Création de base de données, de collection et de document

- Ajoutez le code suivant à **views.py**. Ce code crée la  base de données utilisée par le formulaire. Ne supprimez pas le code existant dans  **views.py**. Il vous suffit d'ajouter le code à la fin.

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

### Lecture de la base de données, de la collection et du document, et envoi du formulaire

- Ajoutez le code suivant à **views.py**. Ce code configure le formulaire, et lit la base de données, la collection et le document. Ne supprimez pas le code existant dans **views.py**. Il vous suffit d'ajouter le code à la fin.
    
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

1. Ajoutez le code suivant à **create.html**. Ce code affiche le message indiquant la création d'une base de données, d'une collection et d'un document.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>{{ title }}.</h2>
    	<h3>{{ message }}</h3>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}

2. Ajoutez le code suivant à **results.html**. Ce code affiche les résultats du sondage.

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

3. Ajoutez le code suivant à **vote.html**. Il se charge de l'affichage du sondage et de l'approbation des votes. Lors de l'enregistrement des votes, le contrôle est transmis à views.py où nous allons identifier le vote associé et ajouter le document correspondant.

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>What is your favorite way to host an application on Azure?</h2>
    	<form action="" method="post" name="vote">
        	{{form.hidden_tag()}}
        	{{form.deploy_preference}}
        	<button class="btn btn-primary" type="submit">Vote</button>
    	</form>
    	{% endblock %}

4. Remplacez le contenu de **index.html** par le code suivant. Ce code définit une page d'accueil pour votre application

    	{% extends "layout.html" %}
    	{% block content %}
    	<h2>Python + DocumentDB Voting Application.</h2>
    	<h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    	<p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    	<p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    	{% endblock %}


### Ajout d'un fichier de configuration et modification de __init__.py

1. Cliquez avec le bouton droit sur le projet nommé tutorial et ajoutez le fichier **config.py**.
Cette configuration est requise par les formulaires dans Flask. Vous pouvez l'utiliser pour fournir une clé secrète. Cela n'est cependant pas nécessaire dans le cadre de ce didacticiel. 

2. Ajoutez le code suivant à config.py. Modifiez les valeurs de **DOCUMENTDB_HOST** et **DOCUMENTDB_KEY**.

    	CSRF_ENABLED = True
    	SECRET_KEY = 'you-will-never-guess'
    
    	DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    	DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    	DOCUMENTDB_DATABASE = 'voting database'
    	DOCUMENTDB_COLLECTION = 'voting collection'
    	DOCUMENTDB_DOCUMENT = 'voting document'

3. De même, remplacez le contenu de **__init__.py** par le contenu suivant.

    	from flask import Flask
    	app = Flask(__name__)
    	app.config.from_object('config')
    	import tutorial.views

4. Une fois ces étapes terminées, l'Explorateur de solutions doit ressembler à ceci.

	![Screen shot of the Visual Studio Solution Explorer window](./media/documentdb-python-application/image15.png)


## Étape 4 : Exécution locale de l'application

1. Appuyez sur F5 ou sur le bouton Démarrer dans Visual Studio. Votre écran doit afficher le contenu suivant.

	![Screen shot of the Python + DocumentDB Voting Application displayed in a web browser](./media/documentdb-python-application/image16.png)

2. Cliquez sur **Créer/effacer la base de données de vote** pour générer la base de données.

	![Screen shot of the Create Page of the web application](./media/documentdb-python-application/image17.png)

3. Cliquez ensuite sur **Vote** et sélectionnez votre option.

	![Screen shot of the web application with a voting question posed](./media/documentdb-python-application/image18.png)

4. Chaque vote associé augmentera le compteur adéquat.

	![Screen shot of the Results of the vote page shown](./media/documentdb-python-application/image19.png)


## Étape 5 : déploiement de l'application vers des sites web Azure

Maintenant que l'application terminée fonctionne correctement avec DocumentDB, nous allons la déployer vers Sites Web Azure. 

1. Cliquez avec le bouton droit sur le projet dans l'Explorateur de solutions (assurez-vous de ne pas l'exécuter localement), puis sélectionnez Publier.  Sélectionnez ensuite Sites Web Microsoft Azure.

 	![Screen shot of the tutorial selected in Solution Explorer, with the Publish option highlighted](./media/documentdb-python-application/image20.png)

2. Configurez votre site web Azure en fournissant vos informations d'identification et cliquez sur **Publier**.

	![Screen shot of the Publish Web window](./media/documentdb-python-application/image21.png)

3. En quelques secondes, Visual Studio achève la publication de votre application web et lance un navigateur dans lequel vous pouvez voir votre réalisation exécutée dans Azure !

## Étapes suivantes

Félicitations ! Vous venez de créer votre première application Python avec Azure DocumentDB et de la publier dans Sites Web Azure.

Pour ajouter des fonctionnalités supplémentaires à votre application, passez en revue les API disponibles dans le [Kit de développement logiciel (SDK) Python pour DocumentDB](https://pypi.python.org/pypi/pydocumentdb).

  [Cliquez ici pour accéder aux didacticiels sur Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [ici]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [Portail de gestion Azure]: http://portal.azure.com

<!--HONumber=49-->