<properties 
	pageTitle="Configuration de Python avec Sites Web Azure" 
	description="Ce didacticiel décrit les options relatives à la création et à la configuration d'une application Python compatible WSGI (Web Server Gateway Interface) de base dans Sites Web Azure." 
	services="web-sites" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="12/17/2014" 
	ms.author="huvalo"/>




# Configuration de Python avec Sites Web Azure

Ce didacticiel décrit les options relatives à la création et à la configuration d'une application Python compatible WSGI (Web Server Gateway Interface) de base dans Sites Web Azure.

Il présente également les fonctionnalités supplémentaires de déploiement Git, telles que l'environnement virtuel et l'installation des packages à l'aide de requirements.txt.

+ [Bottle, Django ou Flask ?](#bottle-django-flask)
+ [Création d'un site Web sur le portail](#website-creation-on-portal)
+ [Publication Git](#git-publishing)
+ [Vue d'ensemble des applications](#application-overview)
+ [Gestionnaire WSGI](#wsgi-handler)
+ [Environnement virtuel](#next-steps)
+ [Gestion des packages](#next-steps)
+ [Version de Python](#next-steps)
+ [Proxy de l'environnement virtuel](#virtual-environment-proxy)
+ [Personnalisation du déploiement Git](#customize-git-deployment)
+ [Dépannage - Déploiement](#troubleshooting-deployment)
+ [Dépannage - Installation des packages](#troubleshooting-package-installation)
+ [Dépannage - Environnement virtuel](#troubleshooting-virtual-environment)


<h2><a name="bottle-django-flask"></a>Bottle, Django ou Flask ?</h2>

La galerie Azure contient des modèles pour les infrastructures Bottle, Django et Flask.  Si vous ne maîtrisez pas l'utilisation de Git ou que vous n'avez jamais créé de site Web Azure auparavant, nous vous recommandons de suivre un de ces didacticiels, qui vous fournit des instructions détaillées qui vous permettront de développer une application fonctionnelle à partir de la galerie via le déploiement de Git sous Windows ou Mac :

- [Création de sites Web avec Bottle][]
- [Création de sites Web avec Django][]
- [Création de sites Web avec Flask][]


<h2><a name="website-creation-on-portal"></a>Création d'un site Web sur le portail</h2>

Ce didacticiel part du principe que vous disposez d'un abonnement Azure et d'un accès au portail de gestion Azure.

Si vous ne disposez pas de site Web, vous pouvez en créer un à partir du portail de gestion Azure.  Cliquez sur le bouton NOUVEAU dans le coin inférieur gauche. Une fenêtre s'affiche. Cliquez sur CALCUL, SITE WEB, puis sur CRÉATION RAPIDE.

![](./media/web-sites-python-configure/configure-python-create-website.png)


<h2><a name="git-publishing"></a>Publication Git</h2>

Utilisez les onglets CRÉATION RAPIDE ou TABLEAU DE BORD pour le site Web que vous venez de créer afin de configurer la publication Git.  Ce didacticiel utilise Git pour créer, gérer et publier notre site Web Python dans Sites Web Azure.

![](./media/web-sites-python-configure/configure-python-git.png)

Une fois la publication Git configurée, un référentiel Git est créé et associé à votre site Web.  L'URL du référentiel est affichée et peut désormais être utilisée pour envoyer des données depuis l'environnement de développement local vers le cloud. Pour publier des applications via Git, vérifiez qu'un client Git est également installé et suivez les instructions fournies pour envoyer le contenu de votre site Web vers Sites Web Azure.


<h2><a name="application-overview"></a>Vue d'ensemble des applications</h2>

Dans les sections suivantes, les fichiers suivants sont créés.  Ils doivent être placés à la racine du référentiel Git.

    app.py
    requirements.txt
    runtime.txt
    web.config
    ptvs_virtualenv_proxy.py


<h2><a name="wsgi-handler"></a>Gestionnaire WSGI</h2>

WSGI est une norme Python décrite par [PEP 3333](http://www.python.org/dev/peps/pep-3333/) qui définit une interface entre le serveur Web et Python. Elle fournit une interface normalisée pour la rédaction de diverses applications et infrastructures Web à l'aide de Python.  Des infrastructures Web Python connues utilisent aujourd'hui WSGI.  Sites Web Azure vous offre la prise en charge de ces infrastructures ; en outre, les utilisateurs experts peuvent même créer leur propre infrastructure à condition que le gestionnaire personnalisé suive les recommandations de la spécification WSGI.

Voici l'exemple d'une `app.py` qui définit un gestionnaire personnalisé :

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

Vous pouvez exécuter localement cette application via `python app.py`, puis accéder à `http://localhost:5555` à l'aide de votre navigateur.


<h2><a name="virtual-environment"></a>Environnement virtuel</h2>

Bien que l'exemple d'application ci-dessus ne nécessite aucun package externe, il est probable que votre application en nécessite certaines.

Pour faciliter la gestion des dépendances externes de packages, le déploiement d'Azure Git prend en charge la création d'environnements virtuels.

Lorsqu'Azure détecte un requirements.txt à la racine du référentiel, il crée automatiquement un environnement virtuel nommé `env`.  Cela s'effectue uniquement lors du premier déploiement ou bien au cours d'un déploiement réalisé après avoir modifié l'exécution du Python sélectionné.

Vous souhaiterez probablement créer un environnement virtuel en local pour le développement, mais ne pas l'inclure dans votre référentiel Git.


<h2><a name="package-management"></a>Gestion des packages</h2>

Les packages répertoriés dans requirements.txt seront automatiquement installés dans l'environnement virtuel à l'aide de pip.  Cela s'effectue lors de chaque déploiement, mais pip ignorera l'installation si un package est déjà installé.

Exemple `requirements.txt` :

    azure==0.8.4


<h2><a name="python-version"></a>Version de Python</h2>

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-runtime.md)]

Exemple `runtime.txt` :

    python-2.7


<h2><a name="web-config"></a>Web.config</h2>

Vous devez créer un fichier web.config pour définir comment le serveur doit traiter les demandes.

Notez que si vous disposez d'un fichier web.x.y.config dans votre référentiel, dans lequel x.y correspond à l'exécution du Python sélectionné, Azure copiera automatiquement le fichier approprié en tant que fichier web.config.

Les exemples suivants de web.config utilisent un script du proxy de l'environnement virtuel qui est décrit dans la section suivante.  Ils s'exécutent via le gestionnaire WSGI utilisé dans l'exemple `app.py` ci-dessus.

Exemple `web.config` pour Python 2.7 :

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


Exemple `web.config` pour Python 3.4 :

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


Les fichiers statiques sont gérés directement via le serveur Web, sans utiliser de code Python, pour améliorer les performances.

Dans les exemples ci-dessus, l'emplacement des fichiers statiques sur le disque doit correspondre à l'emplacement défini dans l'URL.  Cela signifie qu'une requête de `http://pythonapp.azurewebsites.net/static/site.css` traitera le fichier sur le disque à l'emplacement `\static\site.css`.

Il est possible de configurer la règle `Static Files` pour traiter des fichiers à partir d'un emplacement sur le disque qui diffère de l'emplacement défini dans l'URL.  Dans la définition de la règle suivante, une requête de `http://pythonapp.azurewebsites.net/static/site.css` traitera le fichier sur le disque à l'emplacement `\FlaskWebProject\static\site.css`, au lieu de l'emplacement `\static\site.css`.

    <rule name="Static Files" stopProcessing="true">
      <match url="^/static/.*" ignoreCase="true" />
      <action type="Rewrite" url="^/FlaskWebProject/static/.*" appendQueryString="true" />
    </rule>

`WSGI_ALT_VIRTUALENV_HANDLER` vous permet de définir le gestionnaire WSGI.  Dans les exemples ci-dessus, il s'agit de `app.wsgi_app`, car le gestionnaire est une fonction nommée `wsgi_app` dans `app.py` dans le dossier racine.

`PYTHONPATH` peut être personnalisé, mais vous devrez le modifier.si vous installez toutes les dépendances dans l'environnement virtuel en les définissant dans requirements.txt.


<h2><a name="virtual-environment-proxy"></a>Proxy de l'environnement virtuel</h2>

Le script suivant est utilisé pour récupérer le gestionnaire WSGI, activer l'environnement virtuel et les erreurs de journaux. Il est conçu pour être utilisé de manière générique, sans la moindre modification.

Contenu de `ptvs_virtualenv_proxy.py` :

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


<h2><a name="customize-git-deployment"></a>Personnalisation du déploiement Git</h2>

[AZURE.INCLUDE [web-sites-python-customizing-runtime](../includes/web-sites-python-customizing-deployment.md)]


<h2><a name="troubleshooting-deployment"></a>Dépannage - Déploiement</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-deployment](../includes/web-sites-python-troubleshooting-deployment.md)]


<h2><a name="troubleshooting-package-installation"></a>Dépannage - Installation des packages</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-package-installation](../includes/web-sites-python-troubleshooting-package-installation.md)]


<h2><a name="troubleshooting-virtual-environment"></a>Dépannage - Environnement virtuel</h2>

[AZURE.INCLUDE [web-sites-python-troubleshooting-virtual-environment](../includes/web-sites-python-troubleshooting-virtual-environment.md)]



[Création de sites Web avec Bottle]: ../web-sites-python-create-deploy-bottle-app
[Création de sites Web avec Django]: ../web-sites-python-create-deploy-django-app
[Création de sites Web avec Flask]: ../web-sites-python-create-deploy-flask-app



<!--HONumber=42-->
