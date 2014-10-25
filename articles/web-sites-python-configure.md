<properties linkid="develop-python-tutorials-web-sites-configuration" urlDisplayName="Configuring Python with Azure Websites" pageTitle="Configuring Python with Azure Websites" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Websites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Websites" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo"></tags>

# Configuration de Python avec Sites Web Azure

Ce didacticiel décrit les options relatives à la création et à la configuration d'une application Python compatible WSGI (Web Server Gateway Interface) de base dans Sites Web Azure. La prise en main de Sites Web Azure est facile et votre application Python dispose d'espace pour être mise à l'échelle et étendue à d'autres services Azure. La plateforme Sites Web Azure comprend Python (2.7.3 ou 3.4.0, selon votre choix) et le gestionnaire générique FastCGI wfastcgi.py pour Python. Il vous suffit de configurer votre site web pour utiliser le gestionnaire Python.

> [WACOM.NOTE] Vous pouvez sélectionner la version de Python que vous souhaitez utiliser dans le portail Sites Web Azure en ouvrant l'onglet Configurer de votre site web et en modifiant le paramètre **Version de Python**.

Pour obtenir un exemple plus complexe de configuration de l'infrastructure Django sur Sites Web Azure, consultez le didacticiel suivant :
[][]<http://www.windowsazure.com/fr-fr/develop/python/tutorials/web-sites-with-django></a>.

## Prise en charge de WSGI

WSGI est une norme Python décrite par [PEP 3333][] qui définit une interface entre le serveur Web et Python. Elle fournit une interface normalisée pour la rédaction de diverses applications et infrastructures Web à l'aide de Python. Des infrastructures Web Python connues utilisent aujourd'hui WSGI. Sites Web Azure vous offre la prise en charge de ces infrastructures ; en outre, les utilisateurs experts peuvent même créer leur propre infrastructure à condition que le gestionnaire personnalisé suive les recommandations de la spécification WSGI.

## Création de site web

Ce didacticiel part du principe que vous disposez d'un abonnement Azure et d'un accès au portail de gestion Azure. Vous trouverez des recommandations détaillées sur la création d'un site web dans la rubrique [][1]<http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-create-websites></a>.

En résumé, si vous ne disposez pas de site web, vous pouvez en créer un à partir du portail de gestion Azure. Sélectionnez la fonctionnalité SITES WEB et utilisez l'option CRÉATION RAPIDE, en spécifiant une URL pour votre site web.

![][]

## Publication Git

Utilisez les onglets CRÉATION RAPIDE ou TABLEAU DE BORD pour le site web que vous venez de créer afin de configurer la publication Git. Ce didacticiel utilise Git pour créer, gérer et publier notre site web Python dans Sites Web Azure.

![][2]

Une fois la publication Git configurée, un référentiel Git est créé et associé à votre site web. L'URL du référentiel est affichée et peut désormais être utilisée pour envoyer des données depuis l'environnement de développement local vers le cloud. Pour publier des applications via Git, vérifiez qu'un client Git est également installé et suivez les instructions fournies pour envoyer le contenu de votre site web vers Sites Web Azure.

## Contenu de site web

À titre d'exemple, nous utilisons une application Python de base avec un gestionnaire WSGI de base qui illustre la quantité minimale de travail nécessaire pour bénéficier de la prise en charge Python dans Sites Web Azure. Cette application Python de base peut ensuite être utilisée pour commencer à créer un éventail de solutions, offrant un niveau de complexité allant de l'exemple ci-dessous jusqu'à une infrastructure Web à part entière.

Vous trouverez ci-dessous le code pour le gestionnaire WSGI de base. Il s'apparente à celui suggéré par la spécification [PEP 3333][] comme point de départ pour une application compatible WSGI. Nous avons enregistré ce contenu dans un fichier nommé ConfigurePython.py créé dans un dossier ConfigurePython à la racine du site web :

    def application(environ, start_response):
        status = '200 OK'
        response_headers = [('Content-type', 'text/plain')]
        start_response(status, response_headers)
        yield 'Hello from Azure Websites\n'

*application* peut être appelé par Python et fera office de point d'entrée appelé par le serveur compatible WSGI. Cet objet qui peut être appelé accepte deux arguments de position :

-   *environ* : dictionnaire avec plusieurs variables d'environnement ;
-   *start\_response* : argument pouvant être appelé qui est fourni par le serveur Web pour le transfert de l'état HTTP et l'en-tête de réponse.

Ce gestionnaire renvoie le texte brut « Hello from Azure Websites » à chaque demande qui lui est faite.

## Options de configuration

Il existe deux options différentes pour configurer votre application Python dans Sites Web Azure.

### Option 1 : portail

1.1. Inscrivez le gestionnaire FastCGI via l'onglet CONFIGURER dans le portail.
Pour cet exemple, nous utilisons le gestionnaire FastCGI pour Python qui est inclus dans Sites Web Azure. Vous pouvez faire de même en utilisant les chemins suivants pour votre processeur de script et l'argument du gestionnaire FastCGI :

-   Chemin d'accès du processeur de script Python : D:\\python27\\python.exe
-   Chemin d'accès du gestionnaire FastCGI Python : D:\\python27\\scripts\\wfastcgi.py

![][3]

1.2. Configurez les paramètres de l'application via l'onglet CONFIGURER dans le portail.
Ces paramètres sont convertis en variables d'environnement. Ce mécanisme vous permet de définir les valeurs de configuration requises pour votre application Python. Pour cet exemple d'application de base, nous avons configuré le texte suivant :

-   PYTHONPATH indique à Python le répertoire dans lequel rechercher les modules. Sites Web Azure fournit D:\\home\\site\\wwwroot comme syntaxe pointant sur la racine de votre site web.
-   WSGI\_HANDLER enregistre un nom de module ou de package et l'attribut à utiliser.

![][4]

### Option 2 : web.config

L'autre solution de configuration consiste à utiliser un fichier web.config à la racine du site web pour les actions décrites ci-dessous. L'option web.config permet une meilleure portabilité d'une application Web. Il existe deux approches pour acheminer les demandes à l'application Web : soit définir un gestionnaire chargé de gérer le chemin d'accès \*, qui indique à IIS (Internet Information Services) d'acheminer chaque demande entrante via Python ; soit définir un chemin d'accès spécifique géré par Python, puis utiliser la réécriture d'URL afin de rediriger différentes URL vers le chemin d'accès sélectionné. En réalité, nous recommandons la deuxième approche, à savoir l'utilisation d'un fichier de gestionnaire vide à la racine du site web pour servir de cible de demande (handler.fcgi dans notre exemple), afin d'obtenir de meilleures performances. Dans le premier scénario, toutes les demandes, y compris celles relatives au contenu statique (par exemple, les fichiers image et les feuilles de style), devront passer par Python, en bouleversant les optimisations du serveur Web pour accéder aux fichiers statiques. La dernière approche permet de servir du contenu statique de manière efficace et d'appeler Python uniquement si cela s'avère nécessaire.

2.1. Spécifiez la variable PYTHONPATH.

> Elle indique à Python l'endroit où rechercher le code de l'application. D:\\home\\site\\wwwroot est également utilisé ici en tant que chemin d'accès absolu au site web.

2.2. Définissez la variable WSGI\_HANDLER.

> Sites Web Azure utilise cette valeur pour indiquer à Python d'appeler notre gestionnaire WSGI. La valeur de cette variable est une expression Python qui devrait, lorsqu'elle est exécutée, renvoyer un objet pouvant être appelé et qui représente un gestionnaire WSGI.

2.3. Ajoutez un gestionnaire pour Python.

> Sites Web Azure est ainsi averti que Python doit traiter les demandes effectuées au chemin d'accès handler.fcgi. Il est important que la syntaxe du gestionnaire ressemble exactement au contenu de la balise \<handlers\> dans l'exemple ci-dessous, à moins que vous n'utilisiez votre propre gestionnaire FastCGI ou pile de développement Python.

2.4. Réécrivez les URL vers handler.fcgi.

> Le fait de tout le temps demander handler.fcgi n'est peut-être pas la meilleure solution. Pour sélectionner le chemin d'accès des fichiers que le gestionnaire Python doit traiter, nous avons utilisé la réécriture d'URL afin que toutes les URL soient gérées par notre gestionnaire Python.

    <configuration>
        <appSettings>
            <add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
            <add key="WSGI_HANDLER" value="ConfigurePython.application" />
        </appSettings>
        <system.webServer>
            <handlers>
                <add name="PythonHandler" 
                verb="*" path="handler.fcgi" 
                modules="FastCgiModule" 
                scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
                resourceType="Either" />
            </handlers>
            <rewrite>
                <rules>
                    <rule name="Configure Python" stopProcessing="true">
                        <match url="(.*)" ignoreCase="false" />
                        <conditions>
                            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                        </conditions>
                        <action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration> 

La structure du dossier de l'exemple se déroulant à la racine du site web est la suivante (la casse des noms de dossiers et fichiers Python est importante et reflétée dans le fichier web.config) :

-   ConfigurePython\\ConfigurePython.py
-   web.config
-   handler.fcgi

Comme nous réécrivons toutes les URL afin qu'elles pointent vers handler.fcgi et communiquons ce chemin d'accès à Python via FastCGI, nous devons créer un fichier d'espace réservé portant le même nom de sorte qu'IIS ne renvoie pas une erreur HTTP 404. Le comportement interne du module FastCGI d'IIS impose en fait que le fichier demandé existe avant d'être transféré à l'application du processeur de script spécifié.

Accédez à votre site web pour tester la configuration correcte. Dans le cas de cet exemple, le message « Hello from Azure Websites » apparaît lors de l'accès.

![][5]

  []: http://www.windowsazure.com/fr-fr/develop/python/tutorials/web-sites-with-django
  [PEP 3333]: http://www.python.org/dev/peps/pep-3333/
  [1]: http://www.windowsazure.com/fr-fr/manage/services/web-sites/how-to-create-websites
  []: ./media/web-sites-python-configure/configure-python-create-website.png
  [2]: ./media/web-sites-python-configure/configure-python-git.png
  [3]: ./media/web-sites-python-configure/configure-python-handler-mapping.png
  [4]: ./media/web-sites-python-configure/configure-python-app-settings.png
  [5]: ./media/web-sites-python-configure/configure-python-result.png
