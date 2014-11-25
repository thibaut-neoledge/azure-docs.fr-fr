<properties title="Build a web application with Python and Flask (MVC) using DocumentDB" pageTitle="Build a web app with Python and Flask using DocumentDB | Azure" description="Learn how to use DocumentDB to store and access data from an Python and Flask (MVC) web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="hawong" />

# <a name="_Toc395888515"></a><a name="_Toc395809324">Création d'une application web avec Python et Flask (MVC) à l'aide de DocumentDB</a>

<a name="_Toc395809324">

# <a name="_Toc395888516"></a><a name="_Toc395809325"></a><a name="_Toc389865467"></a><a name="_Toc389828008">Vue d'ensemble</a>

## <a name="_Toc395888517"></a><a name="_Toc395809326">Scénario</a>

Pour démontrer la manière dont les clients peuvent tirer parti d'Azure DocumentDB pour
stocker et interroger des documents JSON, ce document propose une
procédure pas à pas complète détaillant la création d'une application web de vote à l'aide d'Azure Document
DB.

Cette procédure pas à pas indique comment utiliser le service DocumentDB fourni par
Azure pour stocker les données et y accéder à partir d'une application web Python hébergée sur
Azure. Elle suppose que avez déjà une expérience de l'utilisation de Python et du service
Sites Web Azure.

Vous apprendrez à effectuer les opérations suivantes :

1\. Création et configuration d'un compte DocumentDB

2\. Création d'une application Python MVC

3\. Connexion à Azure DocumentDB pour une utilisation à partir de votre application web

4\. Déploiement de l'application web vers Sites Web Azure

Dans ce didacticiel, vous allez créer une application de vote simple
vous permettant de voter lors d'un sondage.

![Alt text](./media/documentdb-python-application/image1.png)

<a name="_Toc395888520"></a><a name="_Toc395809329">Configuration requise</a>

Avant de suivre les instructions de cet article, vérifiez que les éléments
suivants sont installés :

Visual Studio 2013 (ou la version gratuite [Visual Studio Express][Visual Studio Express])

Python Tools pour Visual Studio, disponible [ici][ici]

Kit de développement logiciel (SDK) Azure pour Visual Studio 2013, version 2.4 ou ultérieure, disponible
[ici][1]

Outils en ligne de commande multiplateforme Azure, disponibles via [Microsoft
Web Platform Installer][Microsoft
Web Platform Installer]

# <a name="_Toc395888519"></a><a name="_Toc395809328">Création d'un compte de base de données DocumentDB</a>

Pour configurer un compte de base de données DocumentDB dans Azure, ouvrez le
portail de gestion Azure, puis cliquez sur la vignette Galerie Azure sur la
page d'accueil ou sur « + » dans le coin inférieur gauche de l'écran.

![Alt text](./media/documentdb-python-application/image2.png)

Cette opération ouvre la Galerie Azure, dans laquelle vous pouvez sélectionner
l'un des nombreux services Azure disponibles. Dans la Galerie, sélectionnez « Données, stockage et
sauvegarde » dans la liste des catégories.

![Alt text](./media/documentdb-python-application/image3.png)

Sélectionnez l'option pour Azure DocumentDB.

![Alt text](./media/documentdb-python-application/image4.png)

Sélectionnez ensuite « Créer » en bas de l'écran.

![Alt text](./media/documentdb-python-application/image5.png)

Le volet « Nouveau DocumentDB » s'ouvre. Vous pouvez y spécifier
le nom, la région, l'échelle, le groupe de ressources et d'autres paramètres pour votre
nouveau compte.

![Alt text](./media/documentdb-python-application/image6.png)

Une fois que vous avez fourni les valeurs pour votre compte, cliquez sur
« Créer » : le processus de configuration commence alors la création de
votre compte de base de données. Lorsque ce processus est terminé,
une notification apparaît dans la zone des notifications du portail
et la vignette sur l'écran d'accueil (si vous avez choisi d'en créer une)
affiche l'action terminée.

![Alt text](./media/documentdb-python-application/image7.png)

Une fois la configuration terminée, un clic sur la vignette DocumentDB
dans l'écran d'accueil affiche le volet principal correspondant au
nouveau compte DocumentDB.

![Alt text](./media/documentdb-python-application/image8.png)
![Alt text](./media/documentdb-python-application/image9.png)

À l'aide du bouton « Clés », accédez à votre URL de point de terminaison et à la clé primaire.
Copiez-les dans le Presse-papiers et gardez-les à portée de main.
Nous les utiliserons dans l'application web que nous allons créer.

</a>

# <a name="_Toc395888520"></a><a name="_Toc395809329">Création d'une application web Python Flask</a>

Ouvrez Visual Studio, puis accédez successivement à Fichier -\> Nouveau projet -\> Python -\> Projet web Flask
 et nommez le projet **didacticiel**. Un message vous demandera où
installer les packages externes. Cliquez sur Installer dans un environnement virtuel,
puis sur Créer. Cela configure l'environnement virtuel Python
requis pour votre projet.

Si vous ne connaissez pas bien Flask, sachez qu'il s'agit d'une infrastructure web qui permet d'accélérer
le développement d'applications dans Python. [Cliquez ici pour accéder aux didacticiels sur Flask][Cliquez ici pour accéder aux didacticiels sur Flask].

![Alt text](./media/documentdb-python-application/image10.png)

# <a name="_Toc395888520"></a><a name="_Toc395809329">Ajout de packages Flask à votre projet</a>

Une fois votre projet configuré, vous devez ajouter certains packages Flask
requis, comme par exemple, les formulaires. Cliquez avec le bouton droit sur **env**,
puis sur **Installer les packages Python suivants (respecter cet ordre est
important)** :

    flask==0.9
    flask-login
    flask-openid
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup

![Alt text](./media/documentdb-python-application/image11.png)

**Remarque :** dans de rares cas, la fenêtre de résultat peut afficher une erreur. Si
cela se produit, vérifiez si l'erreur n'est pas liée au nettoyage. Parfois, le
nettoyage peut échouer, alors que l'installation sera réussie (faites défiler
le contenu de la fenêtre de résultat pour le vérifier).
<a name="verify-the-virtual-environment"></a> Si cela se produit, vous pouvez
continuer.

</h1>
# <a name="_Toc395888522"></a><a name="_Toc395809331">Vérification de l'environnement virtuel</a>

Vérifiez que l'installation est réussie. Démarrez le site
web en cliquant sur **F5**. Cette action démarre le serveur de développement Flask
ainsi que votre navigateur web. La page suivante doit s'afficher :

![Alt text](./media/documentdb-python-application/image12.png)

# <a name="_Toc395888523"></a><a name="_Toc395809332">Ajout de DocumentDB à votre projet</a>

Le Kit de développement logiciel (SDK) Python DocumentDB est hébergé dans PyPi. Vous pouvez l'installer avec
pip.

Dans l'Explorateur de solutions, développez le nœud Environnements Python, cliquez avec le bouton droit sur
votre environnement et sélectionnez « Installer le package Python… »

![Alt text](./media/documentdb-python-application/image13.png)

Tapez le nom du package PyPi : « --pre pydocumentdb ». Si vous voulez
choisir la version installée, vous pouvez l'indiquer.
Ne pas le faire permet d'installer la version stable
la plus récente. Veuillez noter que vous devez entrer
l'intégralité du nom « --pre pydocumentdb ».

![Alt text](./media/documentdb-python-application/image14.png)

Le système lance le téléchargement et l'installation du package pydocumentdb dans votre
environnement. Une fois ceci terminé, pydocumentdb est répertorié en tant que
module sous votre environnement.

</h1>
# <a name="_Toc395888524"></a><a name="_Toc395809333">Création de base de données, de collection et de définition de document</a>

Ajoutez le fichier Python **forms.py** au dossier
nommé « didacticiel » dans votre Explorateur de solutions. Ajoutez le code suivant à
forms.py. Nous créons notre application de sondage. Pour ce faire, nous
créons trois champs booléens qui seront permutés selon les entrées de l'utilisateur.

</h1>

    #forms.py
    from flask.ext.wtf import Form
    from wtforms import TextField, BooleanField
    from wtforms.validators import Required
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors
    class LoginForm(Form):
        openid = TextField('openid')
        remember_me = BooleanField('remember_me', default = False)
        remember_me1 = BooleanField('remember_me1', default = False)
        remember_me2 = BooleanField('remember_me2', default = False)

# <a name="_Toc395888520"></a>

### <a name="_Toc395809338">Création de base de données, de collection et de document</a>

\#Remarque : il est plus sûr de conserver vos informations d'identification d'authentification
dans un fichier de configuration plutôt que dans votre application. Pour simplifier, nous les
plaçons ici dans le code source. Créez une fonction dans views.py et nommez-la
« create ». Ajoutez le code suivant à **views.py**. Ne supprimez pas le
code existant.

</h1>

    @app.route('/create')
    def create():
        """Renders the contact page."""
        host = 'https://meetdemo.documents.azure.com:443/'
        masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
        client = document_client.DocumentClient(host, {'masterKey': masterKey})
        databases = client.ReadDatabases().ToArray()
        #delete any existing databases for simplicity
        for database in databases:
            client.DeleteDatabase(database['_self'])
        #create database
        db = client.CreateDatabase({ 'id': 'sample database' })
        # create collection
        collection = client.CreateCollection(db['_self'],{ 'id': 'sample collection' })
        # create document
        document = client.CreateDocument(collection['_self'],
        { 'id': 'sample document',
        'Web Site': '0 votes',
        'Cloud Service': '0 votes',
        'Virtual Machine': '0 votes',
        'name': 'sample document' })
        return render_template('create.html',title='Create Page',year=datetime.now().year,
        message='You just created a new database - sample database a collection - sample collection and a document - sample document that has your votes. Your old votes have been deleted')

# <a name="_Toc395888529"></a><a name="_Toc395809338">Approbation du vote des utilisateurs et mise à jour du document</a>

### <a name="_Toc395809338">Ajout des instructions import requises

# <a name="_Toc395888529"></a>

Ajoutez les instructions import suivantes au début de **views.py**. Elles
vont importer les packages Flask et PythonSDK de DocumentDB.

    from wtforms import Form, BooleanField, TextField, PasswordField, validators
    from forms import LoginForm
    import pydocumentdb.documents as documents
    import pydocumentdb.document_client as document_client
    import pydocumentdb.errors as errors

### <a name="_Toc395809338">Lecture de la base de données, de la collection et du document</a>

Ajoutez le code suivant à **views.py**. Il se charge de la configuration du
formulaire, de la lecture de la base de données, de la collection et du document. Ne supprimez pas
le code existant dans views.py. Contentez-vous d'y ajouter ce code à la fin.

    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = LoginForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            host = 'https://meetdemo.documents.azure.com:443/'
            masterKey = '7xPDjHxJSuAUPI2BEWXF2VNVO6c3MN4q+941NAQwPphIqOGW/gE+pB1CQNp4K2F9/4T1bTl040t6JDeyCBmj3A=='
            client = document_client.DocumentClient(host, {'masterKey': masterKey})
            databases = client.ReadDatabases().ToArray()   #Read database
            db =databases[0] #For simplicity we are assuming there is only one database
            collections = client.ReadCollections(db['_self']).ToArray() #Read collection
            coll =collections[0] #For simplicity we are assuming there is only one collection
            documents = client.ReadDocuments(coll['_self']).ToArray() #Read document
            doc = documents[0]   #For simplicity we are assuming there is only document

### <a name="_Toc395809338">Enregistrement du vote et modification du document</a>

            replaced_document = doc
            if form.remember_me.data:
                print 'choice 1'             
                setvar = doc['Web Site']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Web Site'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
                print replaced_document
            elif form.remember_me1.data:
                print 'choice 2'             
                setvar = doc['Cloud Service']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Cloud Service'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)
            else:
                print 'choice 2'             
                setvar = doc['Virtual Machine']
                finalvar = setvar.split();
                votes = int(finalvar[0])
                votes = votes+1
                doc['Virtual Machine'] = str(votes)+' '+finalvar[1]
                replaced_document = client.ReplaceDocument(doc['_self'],
                                                       doc)

### <a name="_Toc395809338">Affichage des résultats</a>

    return render_template('results.html', 
            title = 'Website = ' + replaced_document['Web Site'] + '\n' +'Cloud Service = ' + replaced_document['Cloud Service'] + '\n' + 'Virtual Machine = ' + replaced_document['Virtual Machine'])
        else :        
            return render_template('vote.html', 
            title = 'Vote',
            form = form,
            providers = app.config['OPENID_PROVIDERS'])

    @app.route('/results')
    def results():
        """Renders the results page."""
        return render_template(
            'results.html',
            title='Results',
            year=datetime.now().year,
            message='Your application description page.')

### <a name="_Toc395809338">Création des fichiers html</a>

Ajoutez les fichiers .html suivants sous le dossier des modèles :
create.html, results.html, vote.html

Ajoutez le code suivant à **create.html**. Il se charge de l'affichage du message
indiquant la création d'une base de données, d'une collection et d'un document.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Click here to Vote &raquo;</a></p>
    {% endblock %}

Ajoutez le code suivant à **results.html**. Il se charge de l'affichage
des résultats du sondage.

    {% extends "layout.html" %}
    {% block content %}
    <h3>Results of the vote</h3>
    <pre>{{ title }}</pre>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote Again &raquo;</a></p>
    {% endblock %}

Ajoutez le code suivant à **vote.html**. Il se charge de l'affichage
du sondage et de l'approbation des votes. Lors de l'enregistrement des votes, le contrôle est
transmis à views.py où nous allons identifier le vote associé et
ajouter le document correspondant.

    {% extends "layout.html" %}
    {% block content %}
    <h1>What is your favorite way to host an application on Azure?</h1>
    <form action="" method="post" name="login">
    {{form.hidden_tag()}}
    <p> </p>
        {{form.remember_me}} Website
        {{form.remember_me1}} Cloud
        {{form.remember_me2}} Virtual Machine
        <p><input type="submit" value="Cast your vote"></p>
    </form>
    {% endblock %}

# <a name="_Toc395888529"></a>

Supprimez le code dans **layout.html** et remplacez-le par celui-ci : Il
se charge de la configuration de la barre de navigation et des URL respectives pour l'acheminement

Supprimez le code dans index.html et remplacez-le par celui-ci : Ce
code définit une page d'accueil pour votre application

</h1>
## <a name="_Toc395888532"></a><a name="_Toc395809341">Ajout d'un fichier de configuration et modification de \_\_init\_\_.py</a>.

Cliquez avec le bouton droit sur le projet nommé Didacticiel et ajoutez le fichier **config.py**.
Cette configuration est requise par les formulaires dans Flask. Vous pouvez l'utiliser pour fournir une
clé secrète. Ajoutez le code suivant à config.py :

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    OPENID_PROVIDERS = [
       ]

De même, ajoutez le **fichier \_\_init\_\_.py**. Recherchez-le dans le
dossier nommé Didacticiel. Remplacez le code d'origine par celui-ci : Il
se charge de la connexion entre les vues et le fichier config.py.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

Une fois ces étapes terminées, votre Explorateur de
solutions doit ressembler à ceci :

![Alt text](./media/documentdb-python-application/image15.png)

# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Exécution locale de l'application</a>

Appuyez sur F5 ou sur le bouton Démarrer dans Visual Studio.
Votre écran doit afficher le contenu suivant.

![Alt text](./media/documentdb-python-application/image16.png)

Cliquez sur « vote » et sélectionnez votre option.

![Alt text](./media/documentdb-python-application/image17.png)

Chaque vote associé augmentera le compteur adéquat. Lors
du prochain vote, vous pourrez afficher les résultats.

![Alt text](./media/documentdb-python-application/image18.png)

</h1>
# <a name="_Toc395888534"></a><a name="_Toc395809343"></a><a name="_Toc395637774">Déploiement de l'application dans Sites Web Azure</a>

Maintenant que l'application terminée fonctionne correctement avec
DocumentDB, nous allons la déployer vers le service Sites Web Azure. Cliquez avec le bouton droit sur
le projet dans l'Explorateur de solutions (assurez-vous de ne pas l'exécuter
localement), puis sélectionnez Publier.

![Alt text](./media/documentdb-python-application/image19.png)

Configurez votre site web Azure en fournissant vos informations d'identification et en créant
un site web ou en réutilisant un site web existant.

![Alt text](./media/documentdb-python-application/image20.png)

En quelques secondes, Visual Studio achève la publication de votre
application web et lance un navigateur dans lequel vous pouvez voir
votre réalisation exécutée dans Azure !

</h1>
# <a name="_Toc395809338">Conclusion</a>

Félicitations ! Vous venez de créer votre première application Python avec
Azure DocumentDB et de la publier dans Sites Web Azure.

Pour obtenir la solution complète, [cliquez ici][cliquez ici]. Remarque : vous devrez
toujours ajouter l'environnement virtuel, installer les outils Python et
les packages, comme indiqué plus haut.

</h1>

  [Alt text]: ./media/documentdb-python-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/fr-fr/products/visual-studio-express-vs.aspx
  [ici]: https://pytools.codeplex.com/releases/view/123624
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [cliquez ici]: http://go.microsoft.com/fwlink/?LinkID=509840&clcid=0x409
  [Cliquez ici pour accéder aux didacticiels sur Flask]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world
  [Visual Studio Express]: http://www.visualstudio.com/fr-fr/products/visual-studio-express-vs.aspx
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
