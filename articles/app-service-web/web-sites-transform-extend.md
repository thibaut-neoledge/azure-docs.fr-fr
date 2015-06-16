<properties
	pageTitle="Configuration avancée et extensions des applications web Azure App Service"
	description="Utilisez les déclarations XDT (XML Document Transformation) pour transformer le fichier ApplicationHost.config dans votre application web Azure App Service et ajouter des extensions privées permettant d’effectuer des actions d’administration personnalisées."
	authors="cephalin"
	writer="cephalin"
	editor="mollybos"
	manager="wpickett"
	services="app-service\web"
	documentationCenter=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="cephalin"/>

# Configuration avancée et extensions des applications web Azure App Service

En utilisant les déclarations [XML Document Transformation](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), vous pouvez transformer le fichier [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) de votre application web dans Azure App Service. Vous pouvez également utiliser les déclarations XDT pour ajouter des extensions privées autorisant des actions d’administration d’application web personnalisées. Le présent article inclut un exemple d’extension d’application web PHP Manager, qui permet de gérer les paramètres PHP par le biais d’une interface web.

##<a id="transform"></a>Configuration avancée via ApplicationHost.config
La plateforme App Service apporte flexibilité et contrôle à la configuration de l’application web. Bien que le fichier de configuration ApplicationHost.config IIS standard ne puisse pas être modifié directement dans App Service, la plateforme prend en charge un modèle de transformation ApplicationHost.config déclaratif basé sur XDT.

Pour tirer parti de cette fonctionnalité de transformation, vous créez un fichier ApplicationHost.xdt avec du contenu XDT et le placez à la racine de l’application web. Vous devrez peut-être redémarrer l’application web pour que les modifications soient appliquées.

L’exemple applicationHost.xdt suivant montre comment ajouter une nouvelle variable d’environnement personnalisée à une application web utilisant PHP 5.4.

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.webServer>
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
         				</environmentVariables>
      				</application>
    			</fastCgi>
  		</system.webServer>
	</configuration>


Un fichier journal avec le statut et les détails de transformation est disponible à la racine FTP sous LogFiles\\Transform.

Pour d'autres exemples, consultez la page [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

**Remarque**<br /> Les éléments de la liste de modules sous `system.webServer` ne peuvent être ni supprimés ni réorganisés, mais des ajouts à la liste sont possibles.


##<a id="extend"></a> Étendre votre application web

###<a id="overview"></a> Vue d’ensemble des extensions privées d’application web

App Service prend en charge les extensions d’application web comme point d’extensibilité des actions d’administration. En fait, certaines fonctionnalités de la plateforme App Service sont implémentées en tant qu’extensions préinstallées. Bien qu’il ne soit pas possible de modifier ces dernières, vous pouvez créer et configurer des extensions privées pour vos propres applications web. Cette fonctionnalité repose également sur les déclarations XDT. Les principales étapes de création d’une extension d’application web privée sont les suivantes :

1. **Contenu** de l’extension d’application web : créez une application web prise en charge par App Service.
2. **Déclaration** de l’extension d’application web : créez un fichier ApplicationHost.xdt.
3. **Déploiement** de l’extension d’application web : placez le contenu dans le dossier SiteExtensions de `root`.

Les liens internes de l’application web doivent pointer vers un chemin d’accès relatif au chemin d’accès de l’application spécifié dans le fichier ApplicationHost.xdt. Toute modification apportée au fichier ApplicationHost.xdt requiert un recyclage de l’application web.

**Remarque** : des informations supplémentaires sur ces éléments clés sont disponibles sur [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Un exemple détaillé est inclus pour illustrer les étapes de création et d’activation d’une extension d’application web privée. Le code source de l'exemple PHP Manager suivant peut être téléchargé sur [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a> Exemple d’extension d’application web : PHP Manager

PHP Manager est une extension d’application web permettant aux administrateurs d’application web d’afficher et de configurer facilement leurs paramètres PHP au moyen d’une interface web plutôt que d’avoir à modifier les fichiers .ini PHP directement. Les fichiers de configuration communs pour PHP incluent le fichier php.ini situé sous Program Files et le fichier .user.ini situé dans le dossier racine de votre application web. Étant donné que le fichier php.ini ne peut pas être modifié directement sur la plateforme App Service, l’extension PHP Manager utilise le fichier .user.ini pour appliquer les changements de paramètres.

####<a id="PHPwebapp"></a> Application web PHP Manager

Ceci est la page d’accueil du déploiement PHP Manager :

![TransformSitePHPUI][TransformSitePHPUI]

Comme vous pouvez le voir, une extension d’application web est similaire à une application web standard, à la différence près qu’un fichier ApplicationHost.xdt supplémentaire se trouve dans le dossier racine de l’application web (des informations supplémentaires sur le fichier ApplicationHost.xdt sont disponibles dans la section suivante de cet article).

L'extension PHP Manager a été créée au moyen du modèle d'application Web ASP.NET MVC 4 Visual Studio. L’affichage suivant de l’Explorateur de solutions illustre la structure de l’extension PHP Manager.

![TransformSiteSolEx][TransformSiteSolEx]

La seule logique spéciale requise pour l’E/S de fichier consiste à indiquer où se trouve le répertoire wwwroot de l’application web. Comme illustré dans l’exemple de code suivant, la variable d’environnement « HOME » indique le chemin d’accès de la racine de l’application web, et le chemin d’accès wwwroot peut être construit en ajoutant « site\\wwwroot » :

	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot.user.ini");
    		return userSettingsFile;
	}


Une fois que vous disposez du chemin d'accès au répertoire, vous pouvez utiliser des opérations d'E/S de fichier pour accéder en lecture et écriture aux fichiers.

Il convient d’exprimer un avertissement par rapport aux extensions d’application web et à la gestion des liens internes. Si vos fichiers HTML contiennent des liens fournissant des chemins d’accès absolus vers des liens internes de votre application web, vous devez vous assurer que ces liens sont précédés de votre nom d’extension en tant que racine de votre application. Ceci est nécessaire car la racine du site de votre extension est à présent « /`[your-extension-name]`/ » et non simplement « / ». Tous les liens internes doivent donc être mis à jour en conséquence. Par exemple, supposons que votre code inclut un lien vers ceci :

`"<a href="/Home/Settings">PHP Settings</a>"`

Lorsque le lien fait partie d’une extension d’application web, il doit se présenter sous la forme suivante :

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Vous pouvez contourner cette obligation en n’utilisant que des chemins d’accès relatifs dans votre application web ou, pour les applications ASP.NET, en utilisant la méthode `@Html.ActionLink` qui crée les liens appropriés pour vous.

####<a id="XDT"></a> Fichier applicationHost.xdt

Le code de votre extension d’application web figure sous %HOME%\\SiteExtensions[nom-votre-extension]. Nous appellerons cela la racine d'extension.

Pour inscrire votre extension d’application web dans le fichier applicationHost.config, vous devez placer un fichier intitulé ApplicationHost.xdt à la racine de l’extension. Le contenu du fichier ApplicationHost.xdt doit se présenter comme suit :

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

Le nom que vous sélectionnez comme nom de votre extension doit être identique au dossier racine de votre extension.

Cela a pour effet d’ajouter un nouveau chemin d’application dans la liste de sites `system.applicationHost` sous le site SCM. Ce dernier représente un point de terminaison d'administration de site avec des informations d'identification d'accès spécifiques. Son URL est `https://[your-site-name].scm.azurewebsites.net`.

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\SiteExtensions[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

###<a id="deploy"></a> Déploiement d’extension d’application web

Pour installer l’extension de votre application web, vous pouvez copier par FTP tous les fichiers de votre application web dans le dossier `\SiteExtensions[your-extension-name]` de l’application web où vous souhaitez installer l’extension. Veillez à copier le fichier ApplicationHost.xdt à cet emplacement également. Redémarrez votre application web pour activer l’extension.

Vous devez être en mesure de voir l’extension de votre application web à l’adresse :

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Notez que l’URL ressemble en tous points à l’URL de votre application web, sauf qu’elle utilise HTTPS et contient « .scm ».

Il est possible de désactiver toutes les extensions privées (non préinstallées) de votre application web pendant le développement et les tests en ajoutant les paramètres de l’application avec la clé `WEBSITE_PRIVATE_EXTENSIONS` et la valeur `0`.

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png

<!--HONumber=54--> 