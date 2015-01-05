<properties urlDisplayName="Create Web and Worker Roles" pageTitle="Créer des rôles web et de travail" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="PHP" title="How to create PHP web and worker roles" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/28/2014" ms.author="tomfitz" />

#Création de rôles web et de travail PHP

Ce guide vous montre comment créer des rôles web et de travail PHP dans un environnement de développement Windows, choisir une version spécifique de PHP à partir des versions " intégrées " disponibles, modifier la configuration de PHP, activer les extensions et effectuer un déploiement vers Azure. Il décrit également la façon de configurer un rôle web et de travail afin d'utiliser un runtime PHP (avec une configuration et des extensions personnalisées) que vous fournissez.

##<a name="TableOfContents"></a>Sommaire
* [Présentation des rôles web et de travail PHP](#WhatIs)
* [Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP](#DownloadSdk)
* [Procédure : Création d'un projet Cloud Services](#CreateProject)
* [Procédure : Ajout de rôles web et de travail PHP](#AddRole)
* [Procédure : Spécification de la version intégrée de PHP](#SpecifyPHPVersion)
* [Procédure : Personnalisation du runtime PHP intégré](#CustomizePHP)
* [Procédure : Utilisation de votre propre runtime PHP](#OwnPHP)
* [Procédure : Exécution de votre application dans les émulateurs de calcul et de stockage](#Emulators)
* [Procédure : Publication de votre application](#Publish) 

##<a name="WhatIs"></a>Présentation des rôles web et de travail PHP
Azure propose trois modèles de calcul pour l'exécution d'applications : [Sites Web Azure][execution model-web sites], [Azure Virtual Machines][execution model-vms] et [Azure Cloud Services][execution model-cloud services]. Ils prennent tous les trois en charge PHP. Cloud Services, qui inclut les rôles web et de travail, fournit *PaaS (Platform as a Service)*. Au sein d'un service cloud, un rôle web fournit un serveur web IIS (Internet Information Services) dédié permettant d'héberger des applications web frontales, tandis qu'un rôle de travail peut exécuter des tâches asynchrones, de longue durée ou perpétuelles indépendamment des interactions ou saisies des utilisateurs.

Pour plus d'informations, consultez la page [Présentation d'un service cloud].

##<a name="DownloadSdk"></a>Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP

Le [Kit de développement logiciel (SDK) Azure pour PHP] est constitué de plusieurs composants. Cet article en utilise deux : Azure PowerShell et les émulateurs Azure. Ceux-ci peuvent être installés par le biais de Microsoft Web Platform Installer ici : [Installer Azure PowerShell et les émulateurs Azure][install ps and emulators].

##<a name="CreateProject"></a>Utilisation Création d'un projet Cloud Services

La première étape de la création d'un rôle web ou de travail PHP consiste à créer un projet de service Azure. Celui-ci fait office de conteneur logique pour les rôles web et de travail, et contient les fichiers de [définition de service (.csdef)] et de [configuration de service (.cscfg)] du projet. 

Pour créer un projet de service Azure, exécutez la commande suivante :

	PS C:\>New-AzureServiceProject myProject

Cette commande crée un répertoire (" myProject ") dans lequel vous pouvez ajouter des rôles web et de travail.

##<a name="AddRole"></a>Utilisation Ajout de rôles web et de travail PHP

Pour ajouter un rôle web PHP à un projet, exécutez la commande suivante à partir du répertoire racine du projet :

	PS C:\myProject> Add-AzurePHPWebRole roleName

Pour un rôle de travail, utilisez la commande suivante :

	PS C:\myProject> Add-AzurePHPWorkerRole roleName

<div class="dev-callout"> 
<b>Remarque</b> 
<p>Le paramètre <code>roleName</code> est facultatif. S'il est omis, le nom du rôle est généré automatiquement. Le premier rôle web créé sera <code>Rôleweb1</code>, le second <code>Rôleweb2</code>et ainsi de suite. Le premier rôle de travail créé sera <code>Rôletravail1</code>, le second <code>Rôletravail2</code>et ainsi de suite.</p> 
</div>

##<a name="SpecifyPHPVersion"></a>Spécification de la version intégrée de PHP

Lorsque vous ajoutez un rôle web ou de travail PHP à un projet, les fichiers de configuration du projet sont modifiés de façon à ce que PHP soit installé sur chaque instance web ou de travail de votre application lors de son déploiement. Pour afficher la version de PHP qui sera installée par défaut, exécutez la commande suivante :

	PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Le résultat de la commande ci-dessus sera similaire à celui qui figure ci-dessous. Dans cet exemple, l'indicateur " IsDefault " est défini sur " true " pour PHP 5.3.17, ce qui indique qu'il s'agit de la version PHP par défaut installée. 

	Runtime Version		PackageUri						IsDefault
	------- ------- 	----------  					---------
   	Node 0.6.17      	http://nodertncu.blob.core...   False
   	Node 0.6.20         http://nodertncu.blob.core...   True
   	Node 0.8.4          http://nodertncu.blob.core...   False
	IISNode 0.1.21      http://nodertncu.blob.core...   True
  	Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

Vous pouvez définir la version exécutable de PHP sur l'une des versions PHP qui sont répertoriées. Par exemple, pour configurer la version de PHP (pour un rôle avec le nom " roleName ") sur 5.4.0, utilisez la commande suivante :

	PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

<div class="dev-callout"> 
<b>Remarque</b> 
<p>D'autres versions de PHP seront disponibles ultérieurement et les versions disponibles sont susceptibles de changer.</p> 
</div>

##<a name="CustomizePHP"></a>Utilisation Personnalisation du runtime PHP intégré

Vous disposez d'un contrôle total sur la configuration du runtime PHP qui est installée lorsque vous suivez les étapes ci-dessus, y compris la modification des paramètres " php.ini " et l'activation des extensions.

Pour personnaliser le runtime PHP intégré, procédez comme suit :

1. Ajoutez un nouveau dossier, intitulé " php ", dans le répertoire " bin " de votre rôle web. Pour un rôle de travail, ajoutez-le dans le répertoire racine du rôle.
2. Dans le dossier " php ", créez un autre dossier intitulé " ext ". Insérez dans ce dossier tous les fichiers d'extension " .dll " (par exemple, " php_mongo.dll ") que vous voulez activer.
3. Ajoutez un fichier " php.ini " dans le dossier " php ". Activez les extensions personnalisées et définissez les directives PHP que vous voulez dans ce fichier. Par exemple, si vous vouliez activer " display_errors " et l'extension " php_mongo.dll ", le contenu de votre fichier " php.ini " se présenterait comme suit :

		display_errors=On
		extension=php_mongo.dll

<div class="dev-callout"> 
<b>Remarque</b> 
<p>Tout paramètre que vous ne définissez pas de façon explicite dans le fichier <code>php.ini</code> que vous fournissez est automatiquement défini sur sa valeur par défaut. Toutefois, gardez à l'esprit que vous pouvez ajouter un fichier  <code>php.ini</code> complet. </p> 
</div>

##<a name="OwnPHP"></a>Utilisation de votre propre runtime PHP
Dans certains cas, au lieu de sélectionner un runtime PHP intégré et de le configurer comme indiqué ci-dessus, vous pouvez fournir votre propre runtime PHP. Par exemple, vous pouvez utiliser le même runtime PHP dans un rôle web ou de travail que vous utilisez dans votre environnement de développement, ce qui permet de s'assurer que l'application ne changera pas de comportement dans votre environnement de production.

<h3><a name="OwnPHPWebRole"></a>Configuration d'un rôle web pour utiliser votre propre runtime PHP</h3>

Pour configurer un rôle web pour utiliser un runtime PHP que vous fournissez, procédez comme indiqué ci-dessous.

1. Créez un projet de service Azure et ajoutez un rôle web PHP comme indiqué dans les sections [ Création d'un projet de services cloud](#CreateProject) et [ Ajout de rôles web et de travail PHP](#AddRole) ci-dessus.
2. Créez un dossier " php dans le dossier " bin " se trouvant dans le répertoire racine de votre rôle web, puis ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) dans le dossier " php ".
3. (FACULTATIF) Si votre runtime PHP utilise les [pilotes Microsoft SQL Server pour PHP][sqlsrv drivers], une fois le rôle web mis en service, il vous faudra le configurer pour installer [SQL Server Native Client 2012][sql native client]. Pour cela, ajoutez le programme d'installation " sqlncli.msi " dans le dossier " bin " du répertoire racine de votre rôle web. Vous pouvez télécharger ce dernier ici : [programme d'installation sqlncli.msi x64]. Le script de démarrage décrit à l'étape suivante exécutera en silence le programme d'installation lorsque le rôle sera mis en service. Si votre runtime PHP n'utilise pas les pilotes Microsoft SQL Server pour PHP, vous pouvez supprimer la ligne suivante dans le script figurant à l'étape suivante :

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. L'étape suivante consiste à définir une tâche de démarrage configurant [Internet Information Services (IIS)][iis.net] afin d'utiliser votre runtime PHP pour gérer les demandes de pages " .php ". Pour cela, ouvrez le fichier " setup_web.cmd " (dans le fichier " bin " du répertoire racine de votre rôle web) dans un éditeur de texte et remplacez son contenu par le script suivant :

		@ECHO ON
		cd "%~dp0"
		
		if "%EMULATED%"=="true" exit /b 0
		
		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
		
		SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
		SET NEW_PATH=%PATH%;%RoleRoot%\base\x86
		
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
		%WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Ajoutez vos fichiers d'application dans le répertoire racine de votre rôle web. Il s'agira du répertoire racine du serveur web.

6. Publiez votre application en procédant comme indiqué dans la section[ Publication de votre application](#Publish) ci-dessous.

<div class="dev-callout"> 
<b>Remarque</b> 
<p>Le script <code>download.ps1</code> dans le dossier <code>bin</code> du répertoire racine du rôle web peut être supprimé après avoir suivi les étapes ci-dessus permettant d'utiliser votre propre runtime PHP.</p> 
</div>

<h3><a name="OwnPHPWorkerRole"></a>Configuration d'un rôle de travail pour utiliser votre propre runtime PHP</h3>

Pour configurer un rôle de travail pour utiliser un runtime PHP que vous fournissez, procédez comme indiqué ci-dessous.

1. Créez un projet de service Azure et ajoutez un rôle de travail PHP comme indiqué dans les sections [ Création d'un projet de services cloud](#CreateProject) et [ Ajout de rôles web et de travail PHP](#AddRole) ci-dessus.
2. Créez un dossier " php " dans le répertoire racine du rôle de travail, puis ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) dans le dossier " php ".
3. (FACULTATIF) Si votre runtime PHP utilise les [pilotes Microsoft SQL Server pour PHP][sqlsrv drivers], une fois le rôle de travail mis en service, il vous faudra le configurer pour installer [SQL Server Native Client 2012][sql native client]. Pour cela, ajoutez le programme d'installation " sqlncli.msi " dans le répertoire racine du rôle de travail. Vous pouvez télécharger ce dernier ici : [programme d'installation sqlncli.msi x64]. Le script de démarrage décrit à l'étape suivante exécutera en silence le programme d'installation lorsque le rôle sera mis en service. Si votre runtime PHP n'utilise pas les pilotes Microsoft SQL Server pour PHP, vous pouvez supprimer la ligne suivante dans le script figurant à l'étape suivante :

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. L'étape suivante consiste à définir une tâche de démarrage ajoutant votre exécutable " php.exe " à l'environnement PATH du rôle de travail une fois ce dernier mis en service. Pour cela, ouvrez le fichier " setup_worker.cmd " (dans le répertoire racine de votre rôle de travail) dans un éditeur de texte et remplacez son contenu par le script suivant :

		@echo on

		cd "%~dp0"

		echo Granting permissions for Network Service to the web root directory...
		icacls ..\ /grant "Network Service":(OI)(CI)W
		if %ERRORLEVEL% neq 0 goto error
		echo OK

		if "%EMULATED%"=="true" exit /b 0

		msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

		setx Path "%PATH%;%~dp0php" /M

		if %ERRORLEVEL% neq 0 goto error

		echo SUCCESS
		exit /b 0

		:error

		echo FAILED
		exit /b -1	

5. Ajoutez vos fichiers d'application dans le répertoire racine de votre rôle de travail.

6. Publiez votre application en procédant comme indiqué dans la section[ Publication de votre application](#Publish) ci-dessous.

##<a name="Emulators"></a>Utilisation Exécution de votre application dans les émulateurs de calcul et de stockage

Les émulateurs de calcul et de stockage Azure fournissent un environnement local dans lequel vous pouvez tester votre application Azure avant de la déployer sur le cloud. Il y a quelques différences entre les émulateurs et l'environnement Azure. Pour mieux comprendre, consultez les pages [Différences entre l'émulateur de calcul et Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg432960.aspx) et [Différences entre l'émulateur de stockage et Azure Storage Services](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433135.aspx).

Notez que PHP doit être installé en local pour pouvoir utiliser l'émulateur de calcul. Ce dernier utilise votre installation locale de PHP pour exécuter votre application.

Pour exécuter votre projet dans les émulateurs, exécutez la commande suivante à partir du répertoire racine de votre projet :

	PS C:\MyProject> Start-AzureEmulator

Un résultat similaire au suivant doit s'afficher :

	Creating local package...
	Starting Emulator...
	Role is running at http://127.0.0.1:81
	Started

Pour voir votre application exécutée dans l'émulateur, ouvrez un navigateur web et accédez à l'adresse locale illustrée dans le résultat (" http://127.0.0.1:81 " dans l'exemple ci-dessus).

Pour arrêter les émulateurs, exécutez la commande suivante :

	PS C:\MyProject> Stop-AzureEmulator

##<a name="Publish"></a>Utilisation Publication de votre application

Pour publier votre application, vous devez d'abord importer vos paramètres de publication au moyen de la cmdlet **Import-PublishSettingsFile**, puis vous pouvez publier votre application au moyen de la cmdlet **Publish-AzureServiceProject**. Pour plus d'informations sur chacune de ces cmdlets, consultez respectivement les rubriques[ Importation de paramètres de publication] et [ Déploiement d'un service cloud sur Azure].

[execution model-web sites]: /fr-fr/develop/net/fundamentals/compute/#WebSites
[execution model-vms]: /fr-fr/develop/net/fundamentals/compute/#VMachine
[execution model-cloud services]: /fr-fr/develop/net/fundamentals/compute/#CloudServices
[Kit de développement logiciel (SDK) Azure pour PHP]: /fr-fr/develop/php/common-tasks/download-php-sdk/
[installer ps et les émulateurs]: http://go.microsoft.com/fwlink/?LinkId=253447&clcid=0x409
[Présentation d'un service cloud]: /fr-fr/manage/services/cloud-services/what-is-a-cloud-service/
[définition de service (.csdef)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758711.aspx
[configuration de service (.cscfg)]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[client natif sql]: http://msdn.microsoft.com/fr-fr/sqlserver/aa937733.aspx
[pilotes sqlsrv]: http://php.net/sqlsrv
[programme d'installation sqlncli.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
[Procédure : Importation des paramètres de publication]: /fr-fr/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
[Procédure : Déploiement d'un service cloud sur Azure].: /fr-fr/develop/php/how-to-guides/powershell-cmdlets/#Deploy

<!--HONumber=35.1-->
