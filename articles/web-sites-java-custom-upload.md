<properties 
	pageTitle="Charger une application web Java personnalisée dans Azure" 
	description="Ce didacticiel explique comment charger une application web Java personnalisée dans Azure App Service Web Apps." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="robmcm"/>

# Charger une application web Java personnalisée dans Azure

Cette rubrique explique comment charger une application web Java personnalisée dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps. Vous y trouverez des informations s’appliquant à tous les sites web ou application web Java, ainsi que quelques exemples d’applications spécifiques.

Notez qu’Azure permet de créer des applications web Java en utilisant l’interface utilisateur de configuration de la version préliminaire du portail Azure ainsi qu’Azure Marketplace, comme explique dans la page [Créer une application web Java dans Azure Web Service](web-sites-java-get-started.md). Ce didacticiel concerne les scénarios dans lesquels vous ne souhaitez pas utiliser l’interface utilisateur de configuration du portail, ni Azure Marketplace.

## Instructions de configuration

Voici les paramètres prévus pour les applications web Java personnalisées dans Azure :

- Le port HTTP utilisé par le processus Java est attribué dynamiquement. Le processus doit utiliser le port à partir de la variable d’environnement `HTTP_PLATFORM_PORT`.
- Tous les ports d'écoute autres que le seul écouteur HTTP doivent être désactivés. Dans Tomcat, cela inclut le port d'arrêt ainsi que les ports HTTPS et AJP.
- Le conteneur doit être configuré pour le trafic IPv4 uniquement.
- La commande **startup** pour l'application doit être définie dans la configuration.
- Les applications qui requièrent des répertoires accessibles en écriture doivent être situées dans le répertoire de contenu de l’application web Azure, soit **D:\\home**. La variable d’environnement `HOME` fait référence à D:\\home.  

Vous pouvez définir les variables d'environnement comme requis dans le fichier web.config.

## Configuration de httpPlatform dans web.config

Les informations suivantes décrivent le format **httpPlatform** dans web.config :
                                 
**arguments** (par défaut ="") : arguments de l'exécutable ou du script spécifiés dans le paramètre **processPath**.

Exemples (avec **processPath** inclus) :

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** : chemin d'accès à l'exécutable ou au script qui démarre un processus d'écoute des requêtes HTTP.


Exemples :


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (par défaut = 10) : nombre de fois par minute où le processus spécifié dans **processPath** est autorisé à se bloquer. Si cette limite est dépassée, **HttpPlatformHandler** arrête de démarrer le processus pendant le restant de la minute.
                                    
**requestTimeout** (par défaut = « 00:02:00 ») : durée pendant laquelle **HttpPlatformHandler** attend une réponse du processus à l’écoute sur `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (par défaut = 10) : nombre de fois où **HttpPlatformHandler** tente de démarrer le processus spécifié dans **processPath**. Pour plus d'informations, consultez la section **startupTimeLimit**.

**startupTimeLimit** (par défaut = 10 secondes) : durée pendant laquelle **HttpPlatformHandler** attend que l'exécutable/le script démarre un processus d'écoute du port. Si cette limite de temps est dépassée, **HttpPlatformHandler** tue le processus et tente de le redémarrer **startupRetryCount** fois.
                                                                                      
**stdoutLogEnabled** (par défaut = « true ») : si true, **stdout** et **stderr** pour le processus spécifié dans le paramètre **processPath** sont redirigés vers le fichier spécifié dans **stdoutLogFile** (consultez la section **stdoutLogFile**).
                                    
**stdoutLogFile** (par défaut = « d:\\home\\LogFiles\\httpPlatformStdout.log ») : le chemin d'accès absolu au fichier pour lequel **stdout** et **stderr** sont journalisés à partir du processus spécifié dans **processPath**.
                                    
> [AZURE.NOTE]`%HTTP_PLATFORM_PORT%` est un espace réservé spécial qui doit être spécifié soit dans les **arguments**, soit dans la liste **environmentVariables** de **httpPlatform**. Un port généré en interne par **HttpPlatformHandler** le remplace afin que le processus spécifié par **processPath** puisse écouter ce port.

## Déploiement

Les applications web Java peuvent être déployées facilement via la plupart des outils utilisés pour les applications web IIS (Internet Information Services). Les mécanismes de déploiement FTP, Git et Kudu sont pris en charge, tout comme la fonctionnalité intégrée SCM pour les applications Web. WebDeploy fonctionne comme un protocole, mais étant donné que Java n’est pas développé dans Visual Studio, WebDeploy n’est pas adapté au déploiement d’applications web Java.

## Exemples de configuration d'application

Les applications suivantes utilisent un fichier web.config et la configuration de l’application comme exemples pour montrer comment activer votre application Java sur App Service Web Apps.

### Tomcat
Bien qu’il existe deux variantes Tomcat fournies avec App Service Web Apps, il est également possible de charger des instances spécifiques du client. Voici un exemple d'installation de Tomcat avec une JVM différente :

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Du côté de Tomcat, quelques modifications de configuration sont à effectuer. server.xml doit être modifié pour définir :

-	Port d'arrêt = -1
-	Port du connecteur HTTP = {port.http}
-	Adresse du connecteur HTTP = « 127.0.0.1 »
-	Placement des connecteurs HTTPS et AJP en commentaires
-	Le paramètre IPv4 peut également être défini dans le fichier catalina.properties, où vous pouvez ajouter `java.net.preferIPv4Stack=true`.
    
App Service Web Apps ne prend pas en charge les appels Direct3d. Pour les désactiver, ajoutez l’option Java suivante si votre application passe de tels appels : `-Dsun.java2d.d3d=false`

### Jetty

Comme pour Tomcat, les clients peuvent charger leurs propres instances pour Jetty. En cas d'exécution de l'installation complète de Jetty, la configuration ressemblerait à ce qui suit :

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

La configuration Jetty doit être changée dans le fichier start.ini pour définir `java.net.preferIPv4Stack=true`.

### Hudson

Notre test a utilisé le war Hudson 3.1.2 et l'instance Tomcat 7.0.50 par défaut, mais pas l'interface utilisateur pour la configuration. Comme Hudson est un outil de génération de logiciel, il est recommandé de l’installer sur des instances dédiées où l’indicateur **AlwaysOn** peut être défini sur l’application web.

1. À la racine de votre application web, c’est-à-dire **d:\\home\\site\\wwwroot**, créez un répertoire **webapps** (si ce n’est déjà fait), puis placez le fichier Hudson.war dans **d:\\home\\site\\wwwroot\\webapps**.
2. Téléchargez apache maven 3.0.5 (compatible avec Hudson) et placez-le dans **d:\\home\\site\\wwwroot**.
3. Créez web.config dans **d:\\home\\site\\wwwroot** et collez-y le contenu suivant :
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    L’application web peut maintenant redémarrer pour prendre en compte les modifications. Connectez-vous à http://yourwebapp/hudson pour démarrer Hudson.

4. Une fois que Hudson s'est configuré lui-même, l'écran suivant doit s'afficher :

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Pour accéder à la page de configuration de Hudson, cliquez sur **Manage Hudson** puis **Configure System**.
6. Configurez le JDK comme indiqué ci-dessous :

	![Configuration de Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Configurez Maven comme indiqué ci-dessous :

	![Configuration de Maven](./media/web-sites-java-custom-upload/maven.png)

8. Enregistrez les paramètres. Hudson est désormais configuré et prêt à l'emploi.

Pour plus d'informations sur Hudson, consultez la page [http://hudson-ci.org](http://hudson-ci.org).

### Liferay

App Service Web Apps prend en charge Liferay. Étant donné que Liferay peut nécessiter une mémoire importante, l’application web doit s’exécuter sur un processus de travail dédié moyen ou important, capable de fournir une mémoire suffisante. Le démarrage de Liferay prend également plusieurs minutes. Pour cette raison, il est recommandé de configurer l’application web sur **AlwaysOn**.

En utilisant Liferay 6.1.2 Community Edition GA3 avec Tomcat, les fichiers suivants ont été modifiés après le téléchargement de Liferay :

**Server.xml**

- Modifiez le port d'arrêt sur -1.
- Modifiez le connecteur HTTP sur `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Placez le connecteur AJP en commentaires.

Dans le dossier **liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes**, créez un fichier nommé **portal-ext.properties**. Ce fichier doit contenir une ligne, comme illustrée ici :

    liferay.home=%HOME%/site/wwwroot/liferay

Au même niveau d'arborescence que le dossier tomcat-7.0.40, créez un fichier nommé **web.config** avec le contenu suivant :

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Sous le bloc **httpPlatform**, **requestTimeout** a la valeur « 00:10:00 ». Vous pouvez réduire cette valeur, mais certaines erreurs de délai d'attente sont alors susceptibles d'apparaître lors de l'amorçage de Liferay. Si cette valeur est modifiée, **connectionTimeout** doit également être modifié dans tomcat server.xml.

Il est intéressant de remarquer que la variable d'environnement JRE_HOME est spécifiée dans web.config ci-dessus pour pointer vers le JDK 64 bits. Il est en 32 bits par défaut, mais étant donné que Liferay peut nécessiter d'importants niveaux de mémoire, il est recommandé d'utiliser le JDK 64 bits.

Une fois ces changements effectués, redémarrez votre application web exécutant Liferay, puis ouvrez http://yourwebapp. Le portail Liferay est disponible depuis la racine de l’application web.

Pour plus d'informations sur Liferay, consultez la page [http://www.liferay.com](http://www.liferay.com).

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]
 

<!--HONumber=54-->