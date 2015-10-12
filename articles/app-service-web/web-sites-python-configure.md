<properties 
	pageTitle="Configuration de Python dans Azure App Service Web Apps" 
	description="Ce didacticiel décrit les options relatives à la création et à la configuration d’une application Python compatible WSGI (Web Server Gateway Interface) de base dans Azure App Service Web Apps." 
	services="app-service" 
	documentationCenter="python" 
	tags="python"
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="huguesv"/>




# Configuration de Python dans Azure App Service Web Apps

Ce didacticiel décrit les options relatives à la création et à la configuration d’une application Python compatible WSGI (Web Server Gateway Interface) de base dans [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Il décrit les fonctions supplémentaires associées au déploiement Git, telles que l’environnement virtuel et l’installation de packages à l’aide de fichier requirements.txt.


## Bottle, Django ou Flask ?

Azure Marketplace comporte des modèles pour les infrastructures Bottle, Django et Flask. Si vous développez votre première application web dans Azure App Service ou que vous n’êtes pas familiarisé avec Git, nous vous invitons à suivre l’un des didacticiels suivants, lesquels présentent des instructions pas à pas pour générer une application à partir de la galerie à l’aide du déploiement Git sur Windows ou Mac :

- [Création d’applications web avec Bottle](web-sites-python-create-deploy-bottle-app.md)
- [Création d’applications web avec Django](web-sites-python-create-deploy-django-app.md)
- [Création d’applications web avec Flask](web-sites-python-create-deploy-flask-app.md)


## Création d’applications web sur le portail Azure en version préliminaire

Ce didacticiel part du principe que vous disposez d'un abonnement Azure et d'un accès au portail Azure en version préliminaire.

Si vous ne disposez pas d’une application web existante, vous pouvez en créer une à partir du [portail Azure en version préliminaire](https://portal.azure.com). Cliquez sur le bouton NOUVEAU en bas à gauche, puis cliquez sur **Web + mobile** > **Application web**.

## Publication Git

Configurez la publication Git de votre nouvelle application web en suivant les instructions dans [Déploiement continu à l’aide de GIT dans Azure App Service](web-sites-publish-source-control.md). Ce didacticiel utilise Git pour créer, gérer et publier notre application web Python dans Azure App Service.

Une fois la publication Git configurée, un référentiel Git est créé et associé à votre application web. L’URL du référentiel est affichée et peut désormais être utilisée pour envoyer des données depuis l’environnement de développement local vers le cloud. Pour publier des applications via Git, vérifiez qu’un client Git est également installé et suivez les instructions fournies pour envoyer le contenu de votre application web vers Azure App Service.


## Vue d’ensemble de l’application

Dans les sections suivantes, les fichiers qui suivent sont créés. Ils doivent être placés à la racine du référentiel Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


## Gestionnaire WSGI

WSGI est une norme Python décrite par [PEP 3333](http://www.python.org/dev/peps/pep-3333/) qui définit une interface entre le serveur Web et Python. Elle fournit une interface normalisée pour la rédaction de diverses applications et infrastructures Web à l'aide de Python. Des infrastructures Web Python connues utilisent aujourd’hui WSGI. Azure App Service Web Apps vous offre la prise en charge de ces infrastructures. En outre, les utilisateurs experts peuvent même créer leur propre infrastructure à condition que le gestionnaire personnalisé suive les instructions de la spécification WSGI.

Voici un exemple de `app.py` définissant un gestionnaire personnalisé :

    def wsgi_app(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        response_body = 'Hello World'
        yield response_body.encode()

    if __name__ == '__main__':
        from wsgiref.simple_server import make_server

        httpd = make_server('localhost', 5555, wsgi_app)
        httpd.serve_forever()

Vous pouvez exécuter cette application localement à l’aide de `python app.py`, puis accéder à `http://localhost:5555` dans votre navigateur web.


## Environnement virtuel

Bien que l’exemple d’application précédent ne requière aucun package externe, il est probable que votre application en requiert un.

Pour faciliter la gestion des dépendances des packages externes, le déploiement Azure Git prend en charge la création d’environnements virtuels.

Lorsqu’Azure détecte un fichier requirements.txt à la racine du référentiel, il crée automatiquement un environnement virtuel nommé `env`. Cette opération intervient uniquement lors du premier déploiement ou lors de tout déploiement faisant suite à la modification du runtime Python sélectionné.

Vous souhaiterez probablement créer un environnement virtuel local pour le développement, sans pour autant l’inclure à votre référentiel Git.


## Gestion des packages

Les packages répertoriés dans le fichier requirements.txt seront automatiquement installés au sein de l’environnement virtuel à l’aide de pip. Cette opération intervient à chaque déploiement, mais pip ignorera l’installation si un package est déjà installé.

Exemple `requirements.txt` :

    azure==0.8.4


## Version de Python

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

Exemple `runtime.txt` :

    python-2.7


## Web.config

Vous devez créer un fichier web.config pour spécifier de quelle manière le serveur doit gérer les demandes.

Si vous disposez d’un fichier web.X.Y.config dans votre référentiel, où X.Y correspond au runtime Python sélectionné, Azure copie automatiquement le fichier approprié en tant que fichier web.config.

Les exemples de fichier web.config suivants concernent un script proxy d’environnement virtuel, lequel est décrit dans la section suivante. Ces fichiers fonctionnent avec le gestionnaire WSGI utilisé dans l’exemple `app.py` précédent.

Exemple `web.config` pour Python 2.7 :

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\activate_this.py" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_virtualenv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python27\python.exe|D:\Python27\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite"
                      url="handler.fcgi/{R:1}"
                      appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Exemple `web.config` pour Python 3.4 :

    <?xml version="1.0"?>
    <configuration>
      <appSettings>
        <add key="WSGI_ALT_VIRTUALENV_HANDLER" value="app.wsgi_app" />
        <add key="WSGI_ALT_VIRTUALENV_ACTIVATE_THIS"
             value="D:\home\site\wwwroot\env\Scripts\python.exe" />
        <add key="WSGI_HANDLER"
             value="ptvs_virtualenv_proxy.get_venv_handler()" />
        <add key="PYTHONPATH" value="D:\home\site\wwwroot" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.0" />
      </system.web>
      <system.webServer>
        <modules runAllManagedModulesForAllRequests="true" />
        <handlers>
          <remove name="Python273_via_FastCGI" />
          <remove name="Python340_via_FastCGI" />
          <add name="Python FastCGI"
               path="handler.fcgi"
               verb="*"
               modules="FastCgiModule"
               scriptProcessor="D:\Python34\python.exe|D:\Python34\Scripts\wfastcgi.py"
               resourceType="Unspecified"
               requireAccess="Script" />
        </handlers>
        <rewrite>
          <rules>
            <rule name="Static Files" stopProcessing="true">
              <conditions>
                <add input="true" pattern="false" />
              </conditions>
            </rule>
            <rule name="Configure Python" stopProcessing="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
              </conditions>
              <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>


Les fichiers statiques sont directement gérés par le serveur web, sans passer par le code Python, afin d’améliorer les performances.

Dans les exemples précédents, l’emplacement des fichiers statiques sur le disque doit correspondre à celui présent dans l’URL. Cela signifie qu’une demande pour `http://pythonapp.azurewebsites.net/static/site.css` renverra au fichier présent sur le disque à l’emplacement `\static\site.css`.

Il est possible de configurer la règle `Static Files` pour renvoyer des fichiers depuis un emplacement du disque différent de l’emplacement présent dans l’URL. Dans la définition de règle suivante, une demande pour `http://pythonapp.azurewebsites.net/static/site.css` renverra le fichier présent sur le disque à l’emplacement `\FlaskWebProject\static\site.css` au lieu de `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` est l’emplacement où vous spécifiez le gestionnaire WSGI. Dans les exemples précédents, il s’agit de `app.wsgi_app`, car le gestionnaire correspond à une fonction nommée `wsgi_app` dans `app.py`, dans le dossier racine.

Vous pouvez personnaliser `PYTHONPATH`, mais si vous installez toutes vos dépendances dans l’environnement virtuel en les spécifiant dans le fichier requirements.txt, il n’est pas nécessaire de les modifier.


## Proxy d’environnement virtuel

Le script suivant permet de récupérer le gestionnaire WSGI, d’activer l’environnement virtuel et de journaliser les erreurs. Il est conçu pour être générique et utilisé sans modification.

Contenu de `ptvs_virtualenv_proxy.py` :

     # ############################################################################
     #
     # Copyright (c) Microsoft Corporation. 
     #
     # This source code is subject to terms and conditions of the Apache License, Version 2.0. A 
     # copy of the license can be found in the License.html file at the root of this distribution. If 
     # you cannot locate the Apache License, Version 2.0, please send an email to 
     # vspython@microsoft.com. By using this source code in any fashion, you are agreeing to be bound 
     # by the terms of the Apache License, Version 2.0.
     #
     # You must not remove this notice, or any other, from this software.
     #
     # ###########################################################################

    import datetime
    import os
    import sys

    if sys.version_info[0] == 3:
        def to_str(value):
            return value.decode(sys.getfilesystemencoding())

        def execfile(path, global_dict):
            """Execute a file"""
            with open(path, 'r') as f:
                code = f.read()
            code = code.replace('\r\n', '\n') + '\n'
            exec(code, global_dict)
    else:
        def to_str(value):
            return value.encode(sys.getfilesystemencoding())

    def log(txt):
        """Logs fatal errors to a log file if WSGI_LOG env var is defined"""
        log_file = os.environ.get('WSGI_LOG')
        if log_file:
            f = open(log_file, 'a+')
            try:
                f.write('%s: %s' % (datetime.datetime.now(), txt))
            finally:
                f.close()

    ptvsd_secret = os.getenv('WSGI_PTVSD_SECRET')
    if ptvsd_secret:
        log('Enabling ptvsd ...\n')
        try:
            import ptvsd
            try:
                ptvsd.enable_attach(ptvsd_secret)
                log('ptvsd enabled.\n')
            except: 
                log('ptvsd.enable_attach failed\n')
        except ImportError:
            log('error importing ptvsd.\n');

    def get_wsgi_handler(handler_name):
        if not handler_name:
            raise Exception('WSGI_HANDLER env var must be set')
        
        if not isinstance(handler_name, str):
            handler_name = to_str(handler_name)

        module_name, _, callable_name = handler_name.rpartition('.')
        should_call = callable_name.endswith('()')
        callable_name = callable_name[:-2] if should_call else callable_name
        name_list = [(callable_name, should_call)]
        handler = None

        while module_name:
            try:
                handler = __import__(module_name, fromlist=[name_list[0][0]])
                for name, should_call in name_list:
                    handler = getattr(handler, name)
                    if should_call:
                        handler = handler()
                break
            except ImportError:
                module_name, _, callable_name = module_name.rpartition('.')
                should_call = callable_name.endswith('()')
                callable_name = callable_name[:-2] if should_call else callable_name
                name_list.insert(0, (callable_name, should_call))
                handler = None

        if handler is None:
            raise ValueError('"%s" could not be imported' % handler_name)

        return handler

    activate_this = os.getenv('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS')
    if not activate_this:
        raise Exception('WSGI_ALT_VIRTUALENV_ACTIVATE_THIS is not set')

    def get_virtualenv_handler():
        log('Activating virtualenv with %s\n' % activate_this)
        execfile(activate_this, dict(__file__=activate_this))

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler

    def get_venv_handler():
        log('Activating venv with executable at %s\n' % activate_this)
        import site
        sys.executable = activate_this
        old_sys_path, sys.path = sys.path, []
        
        site.main()
        
        sys.path.insert(0, '')
        for item in old_sys_path:
            if item not in sys.path:
                sys.path.append(item)

        log('Getting handler %s\n' % os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        handler = get_wsgi_handler(os.getenv('WSGI_ALT_VIRTUALENV_HANDLER'))
        log('Got handler: %r\n' % handler)
        return handler


## Personnalisation du déploiement Git

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-deployment.md)]


## Résolution des problèmes - Installation des packages

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]


## Résolution des problèmes - Environnement virtuel

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## Étapes suivantes

Pour plus d’informations, consultez le [Centre pour développeurs Python](/develop/python/).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).





 

<!---HONumber=Oct15_HO1-->