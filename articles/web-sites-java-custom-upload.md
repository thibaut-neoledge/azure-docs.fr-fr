<properties 
	pageTitle="Télécharger un site Web Java personnalisé vers Azure" 
	description="Ce didacticiel vous montre comment télécharger un site Web Java personnalisé vers Azure." 
	services="web-sites" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="robmcm"/>

# Télécharger un site Web Java personnalisé vers Azure

Cette rubrique explique comment télécharger un site Web Java personnalisé dans Azure. Vous y trouverez des informations s'appliquant à tous les sites Web Java, ainsi que quelques exemples d'applications spécifiques.

Notez qu'Azure permet de créer des sites Web Java en utilisant l'interface utilisateur de configuration du portail Azure, ainsi que la galerie d'applications Azure, comme indiqué dans la page [Prise en main d'Azure Web Sites et de Java
](web-sites-java-get-started.md). Ce didacticiel est consacré aux scénarios pour lesquels vous ne souhaitez pas utiliser l'interface utilisateur de configuration Azure ni la galerie d'applications Azure.  

# Instructions de configuration

Voici les paramètres prévus pour les sites Web Java personnalisés dans Azure :

- Le port HTTP utilisé par le processus Java est attribué dynamiquement.  Le processus doit utiliser le port à partir de la variable d'environnement `HTTP_PLATFORM_PORT`.
- Tous les ports d'écoute autres que le seul écouteur HTTP doivent être désactivés.  Dans Tomcat, cela inclut le port d'arrêt ainsi que les ports HTTPS et AJP.
- Le conteneur doit être configuré pour le trafic IPv4 uniquement.
- La commande **startup** pour l'application doit être définie dans la configuration.
- Les applications qui nécessitent des répertoires avec autorisation d'accès en écriture doivent être situées dans le répertoire de contenu du site Web Azure, c'est-à-dire à l'emplacement **D:\home**.  La variable d'environnement `HOME` fait référence à D:\home.  

Vous pouvez définir les variables d'environnement comme requis dans le fichier Web.config.

# Configuration de httpPlatform dans Web.config

Les informations suivantes décrivent le format **httpPlatform** dans Web.config.
                                 
**arguments** (par défaut =""). Arguments de l'exécutable ou du script spécifiés dans le paramètre **processPath**.

Exemples (avec **processPath** inclus) :

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** : chemin d'accès à l'exécutable ou au script qui démarre un processus d'écoute des requêtes HTTP.


Exemples :


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (par défaut = 10) : nombre de fois par minute où le processus spécifié dans **processPath** est autorisé à se bloquer. Si cette limite est dépassée, **HttpPlatformHandler** arrête de démarrer le processus pendant le restant de la minute.
                                    
**requestTimeout** (par défaut = " 00:02:00 ") : durée pendant laquelle **HttpPlatformHandler** attend une réponse du processus à l'écoute sur " %HTTP_PLATFORM_PORT% ".

**startupRetryCount** (par défaut = 10) : nombre de fois où **HttpPlatformHandler** tente de démarrer le processus spécifié dans **processPath**. Pour plus d'informations, consultez la section **startupTimeLimit**.

**startupTimeLimit** (par défaut = 10 secondes) : durée pendant laquelle **HttpPlatformHandler** attend que l'exécutable/le script démarre un processus d'écoute du port.  Si cette limite de temps est dépassée, **HttpPlatformHandler** tue le processus et tente de le redémarrer **startupRetryCount** fois.
                                                                                      
**stdoutLogEnabled** (par défaut = " true ") : Si true, **stdout** et **stderr** pour le processus spécifié dans le **processPath** paramètre sera redirigé vers le fichier spécifié dans **stdoutLogFile** (voir **stdoutLogFile** section).
                                    
**stdoutLogFile** (par défaut = " d:\home\LogFiles\httpPlatformStdout.log ") : le chemin d'accès absolu au fichier pour lequel **stdout** et **stderr** sont journalisés à partir du processus spécifié dans **processPath**.
                                    
> [AZURE.NOTE] `%HTTP_PLATFORM_PORT%` est un espace réservé spécial qui doit être spécifié, soit dans **arguments**, soit dans la liste **environmentVariables** de **httpPlatform**. Un port généré en interne par **HttpPlatformHandler** le remplace afin que le processus spécifié par **processPath** puisse écouter ce port.

# Déploiement

Les sites Web basés sur Java peuvent être déployés facilement via la plupart des moyens identiques à ceux utilisés avec les applications Web basées sur IIS (Internet Information Services).  FTP, Git et Kudu sont pris en charge en tant que mécanismes de déploiement, tout comme la fonctionnalité intégrée SCM pour sites Web. WebDeploy fonctionne comme un protocole, mais étant donné que Java n'est pas développé dans Visual Studio, WebDeploy n'est pas adapté à l'utilisation du déploiement des sites Web Java.

# Exemples de configuration d'application

Les applications suivantes utilisent un fichier Web.config et la configuration de l'application comme exemples pour montrer comment activer votre application Java sur des sites Web Azure.

## Tomcat
Bien qu'il existe sur Tomcat deux variations fournies avec Sites Web Azure, il est également possible de télécharger des instances spécifiques du client.  Voici un exemple d'installation de Tomcat avec une JVM différente :

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

Du côté de Tomcat, quelques modifications de configuration sont à effectuer.  server.xml doit être modifié pour définir :

-	Port d'arrêt = -1
-	Port du connecteur HTTP = {port.http}
-	Adresse du connecteur HTTP = " 127.0.0.1 "
-	Placement des connecteurs HTTPS et AJP en commentaires
-	Le paramètre IPv4 peut également être défini dans le fichier catalina.properties dans lequel vous pouvez ajouter     `java.net.preferIPv4Stack=true`
    
Les appels Direct3d ne sont pas pris en charge par les sites Web Azure. Pour les désactiver, ajoutez l'option Java suivante si votre application passe de tels appels : '-Dsun.java2d.d3d=false'

## Jetty

Comme pour Tomcat, les clients peuvent charger leurs propres instances pour Jetty. En cas d'exécution de l'installation complète de Jetty, la configuration ressemblerait à ce qui suit :

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

La configuration Jetty doit être modifiée dans start.ini pour définir  `java.net.preferIPv4Stack=true`.

## Hudson

Notre test a utilisé le war Hudson 3.1.2 et l'instance Tomcat 7.0.50 par défaut, mais pas l'interface utilisateur pour la configuration.  Comme Hudson est un outil de génération de logiciel, il est recommandé de l'installer sur des instances dédiées où l'indicateur **AlwaysOn** peut être défini sur le site.

1. À la racine de votre site Web Azure, c'est-à-dire **d:\home\site\wwwroot**, créez un répertoire **Webapps** (s'il n'y en a pas déjà un), puis placez Hudson.war dans **d:\home\site\wwwroot\Webapps**.
2. Téléchargez apache maven 3.0.5 (compatible avec Hudson) et placez-le dans **d:\home\site\wwwroot**.
3. Créez Web.config dans **d:\home\site\wwwroot** et collez-y le contenu suivant :
	
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

    À ce stade, vous pouvez redémarrer le site Web pour que les changements soient pris en compte.   Connectez-vous à http://yoursite/hudson pour démarrer Hudson.

4. Une fois que Hudson s'est configuré lui-même, l'écran suivant doit s'afficher :

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Accédez à la page de configuration de Hudson : Cliquez sur **Manage Hudson**, puis sur **Configure System**.
6. Configurez le JDK comme indiqué ci-dessous :

	![Hudson configuration](./media/web-sites-java-custom-upload/hudson2.png)

7. Configurez Maven comme indiqué ci-dessous :

	![Maven configuration](./media/web-sites-java-custom-upload/maven.png)

8. Enregistrez les paramètres. Hudson est désormais configuré et prêt à l'emploi.

Pour plus d'informations sur Hudson, consultez [http://hudson-ci.org](http://hudson-ci.org).

## Liferay

Liferay est pris en charge sur les sites Web Azure. Étant donné que Liferay peut nécessiter une mémoire importante, le site doit être exécuté sur un processus de travail dédié moyen ou grand capable de fournir une mémoire suffisante. Le démarrage de Liferay prend également plusieurs minutes. Pour cette raison, il vous est recommandé de définir le site sur **Always On**.  

En utilisant Liferay 6.1.2 Community Edition GA3 avec Tomcat, les fichiers suivants ont été modifiés après le téléchargement de Liferay :

**Server.xml**

- Modifiez le port d'arrêt sur -1.
- Changez le connecteur HTTP en 
		`<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Placez le connecteur AJP en commentaires.

Dans le dossier **liferay\tomcat-7.0.40\Webapps\ROOT\WEB-INF\classes**, créez un fichier nommé **portal-ext.properties**. Ce fichier doit contenir une ligne, comme illustrée ici :

    liferay.home=%HOME%/site/wwwroot/liferay

Au même niveau d'arborescence que le dossier tomcat-7.0.40, créez un fichier nommé **Web.config** avec le contenu suivant :

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

Sous le bloc **httpPlatform**, **requestTimeout** a la valeur " 00:10:00 ".  Vous pouvez réduire cette valeur, mais certaines erreurs de délai d'attente sont alors susceptibles d'apparaître lors de l'amorçage de Liferay.  Si cette valeur est modifiée, **connectionTimeout** doit également être modifié dans tomcat server.xml.  

Il est intéressant de remarquer que la variable d'environnement JRE_HOME est spécifiée dans Web.config ci-dessus pour pointer vers le JDK 64 bits. Il est en 32 bits par défaut, mais étant donné que Liferay peut nécessiter d'importants niveaux de mémoire, il est recommandé d'utiliser le JDK 64 bits.

Une fois ces changements effectués, redémarrez votre site Web exécutant Liferay puis ouvrez la page http://yoursite.  Le portail Liferay est disponible depuis la racine du site Web. 

Pour plus d'informations sur Liferay, consultez la page [http://www.liferay.com](http://www.liferay.com).









<!--HONumber=42-->
