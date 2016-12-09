---
title: "Rôles web et de travail PHP | Microsoft Docs"
description: "Guide pour la création de rôles web et de travail PHP dans un service cloud Azure et pour la configuration du runtime PHP."
services: 
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 1f6691944205de9e59070b3ffedb3572e8eff21d


---
# <a name="how-to-create-php-web-and-worker-roles"></a>Création de rôles web et de travail PHP
## <a name="overview"></a>Vue d'ensemble
Ce guide vous montre comment créer des rôles web et de travail PHP dans un environnement de développement Windows, choisir une version spécifique de PHP à partir des versions « intégrées » disponibles, modifier la configuration de PHP, activer les extensions et effectuer un déploiement dans Azure. Il décrit également la façon de configurer un rôle web et de travail afin d'utiliser un runtime PHP (avec une configuration et des extensions personnalisées) que vous fournissez.

## <a name="what-are-php-web-and-worker-roles"></a>Présentation des rôles web et de travail PHP
Azure propose trois modèles de calcul pour l’exécution d’applications : Azure App Service, Azure Virtual Machines et Azure Cloud Services. Ils prennent tous les trois en charge PHP. Cloud Services, qui inclut les rôles web et de travail, offre une *plateforme PaaS (platform as a service)*. Dans un service cloud, un rôle web fournit un serveur web IIS (Internet Information Services) dédié pour héberger les applications web frontales. Un rôle de travail peut exécuter des tâches asynchrones, sur le long terme ou perpétuelles, indépendamment des entrées ou des interactions de l’utilisateur.

Pour plus d’informations, consultez [Calcul des options d’hébergement fournies par Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP
Le [Kit de développement logiciel (SDK) Azure pour PHP] est constitué de plusieurs composants. Cet article fait référence à deux d’entre eux : Azure PowerShell et les émulateurs Azure. Ceux-ci peuvent être installés par le biais de Microsoft Web Platform Installer : Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).

## <a name="create-a-cloud-services-project"></a>Création d'un projet Cloud Services
La première étape de la création d’un rôle web ou de travail PHP consiste à créer un projet de service Azure. Celui-ci fait office de conteneur logique pour les rôles web et de travail, et contient les fichiers de [définition de service (.csdef)] et de [configuration de service (.cscfg)] du projet.

Pour créer un projet de service Azure, exécutez Azure PowerShell en tant qu’administrateur et exécutez la commande suivante :

    PS C:\>New-AzureServiceProject myProject

Cette commande crée un répertoire (`myProject`) dans lequel vous pouvez ajouter des rôles web et de travail.

## <a name="add-php-web-or-worker-roles"></a>Ajout de rôles web et de travail PHP
Pour ajouter un rôle web PHP à un projet, exécutez la commande suivante à partir du répertoire racine du projet :

    PS C:\myProject> Add-AzurePHPWebRole roleName

Pour un rôle de travail, utilisez la commande suivante :

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> Le paramètre `roleName` est facultatif. S'il est omis, le nom du rôle est généré automatiquement. Le premier rôle web créé est `WebRole1`, le second `WebRole2`, et ainsi de suite. Le premier rôle de travail créé est `WorkerRole1`, le second `WorkerRole2`, et ainsi de suite.
>
>

## <a name="specify-the-built-in-php-version"></a>Spécification de la version intégrée de PHP
Lorsque vous ajoutez un rôle Web ou de travail PHP à un projet, les fichiers de configuration du projet sont modifiés de façon à ce que PHP soit installé sur chaque instance Web ou de travail de votre application lors de son déploiement. Pour afficher la version de PHP qui sera installée par défaut, exécutez la commande suivante :

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Le résultat de la commande ci-dessus sera similaire à celui qui figure ci-dessous. Dans cet exemple, l’indicateur `IsDefault` est défini sur `true` pour PHP 5.3.17, ce qui indique qu’il s’agit de la version PHP par défaut installée.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Vous pouvez définir la version exécutable de PHP sur l'une des versions PHP qui sont répertoriées. Par exemple, pour configurer la version de PHP (pour un rôle avec le nom `roleName`) sur 5.4.0, utilisez la commande suivante :

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Les versions PHP disponibles peuvent changer à l’avenir.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Personnalisation du runtime PHP intégré
Vous disposez d’un contrôle total sur la configuration du runtime PHP qui est installée lorsque vous suivez les étapes ci-dessus, y compris la modification des paramètres `php.ini` et l’activation des extensions.

Pour personnaliser le runtime PHP intégré, procédez comme suit :

1. Ajoutez un nouveau dossier, intitulé `php`, dans le répertoire `bin` de votre rôle web. Pour un rôle de travail, ajoutez-le dans le répertoire racine du rôle.
2. Dans le dossier `php`, créez un autre dossier intitulé `ext`. Insérez dans ce dossier tous les fichiers d’extension `.dll` (par exemple, `php_mongo.dll`) que vous voulez activer.
3. Ajoutez un fichier `php.ini` dans le dossier `php`. Activez les extensions personnalisées et définissez les directives PHP que vous voulez dans ce fichier. Par exemple, si vous voulez activer `display_errors` et l’extension `php_mongo.dll`, le contenu de votre fichier `php.ini` se présente comme suit :

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Tout paramètre non explicitement défini dans le fichier `php.ini` que vous fournissez est automatiquement défini sur sa valeur par défaut. Toutefois, gardez à l’esprit que vous pouvez ajouter un fichier `php.ini` complet.
>
>

## <a name="use-your-own-php-runtime"></a>Utilisation de votre propre runtime PHP
Dans certains cas, au lieu de sélectionner un runtime PHP intégré et de le configurer comme indiqué ci-dessus, vous pouvez fournir votre propre runtime PHP. Par exemple, vous pouvez utiliser le même runtime PHP dans un rôle web ou de travail que vous utilisez dans votre environnement de développement, ce qui permet de s’assurer que l’application ne change pas de comportement dans votre environnement de production.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurer un rôle web pour utiliser votre propre runtime PHP
Pour configurer un rôle web pour utiliser un runtime PHP que vous fournissez, procédez comme suit :

1. Créez un projet de service Azure et ajoutez un rôle web PHP comme indiqué précédemment dans cette rubrique.
2. Créez un dossier `php` dans le dossier `bin` se trouvant dans le répertoire racine de votre rôle web, puis ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) dans le dossier `php`.
3. (FACULTATIF) Si votre runtime PHP utilise les [pilotes Microsoft SQL Server pour PHP][pilotes sqlsrv], une fois le rôle web mis en service, il vous faudra le configurer pour installer [SQL Server Native Client 2012][client natif sql]. Pour cela, ajoutez le [programme d’installation x64 sqlncli.msi] dans le dossier `bin` du répertoire racine de votre rôle web. Le script de démarrage décrit à l'étape suivante exécutera en silence le programme d'installation lorsque le rôle sera mis en service. Si votre runtime PHP n'utilise pas les pilotes Microsoft SQL Server pour PHP, vous pouvez supprimer la ligne suivante dans le script figurant à l'étape suivante :

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Définissez une tâche de démarrage configurant [Internet Information Services (IIS)][iis.net] afin d’utiliser votre runtime PHP pour gérer les demandes de pages `.php`. Pour cela, ouvrez le fichier `setup_web.cmd` (dans le fichier `bin` du répertoire racine de votre rôle web) dans un éditeur de texte et remplacez son contenu par le script suivant :

    ```cmd
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
    ```
5. Ajoutez vos fichiers d'application dans le répertoire racine de votre rôle web. Il s'agira du répertoire racine du serveur web.
6. Publiez votre application en procédant comme indiqué dans la section [Publication de votre application](#publish-your-application) ci-dessous.

> [!NOTE]
> Il est possible de supprimer le script `download.ps1` (dans le dossier `bin` du répertoire racine du rôle web) une fois que vous avez suivi les étapes ci-dessus permettant d’utiliser votre propre runtime PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurer un rôle de travail pour utiliser votre propre runtime PHP
Pour configurer un rôle de travail pour utiliser un runtime PHP que vous fournissez, procédez comme suit :

1. Créez un projet de service Azure et ajoutez un rôle de travail PHP comme indiqué précédemment dans cette rubrique.
2. Créez un dossier `php` dans le répertoire racine du rôle de travail, puis ajoutez votre runtime PHP (tous les fichiers binaires, fichiers de configuration, sous-dossiers, etc.) dans le dossier `php`.
3. (FACULTATIF) Si votre runtime PHP utilise les [pilotes Microsoft SQL Server pour PHP][pilotes sqlsrv], une fois le rôle de travail mis en service, il vous faudra le configurer pour installer [SQL Server Native Client 2012][client natif sql]. Pour cela, ajoutez le [programme d’installation x64 sqlncli.msi] dans le répertoire racine du rôle de travail. Le script de démarrage décrit à l'étape suivante exécutera en silence le programme d'installation lorsque le rôle sera mis en service. Si votre runtime PHP n'utilise pas les pilotes Microsoft SQL Server pour PHP, vous pouvez supprimer la ligne suivante dans le script figurant à l'étape suivante :

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Définissez une tâche de démarrage ajoutant votre exécutable `php.exe` à l’environnement PATH du rôle de travail une fois ce dernier mis en service. Pour cela, ouvrez le fichier `setup_worker.cmd` (dans le répertoire racine de votre rôle de travail) dans un éditeur de texte et remplacez son contenu par le script suivant :

    ```cmd
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
    ```
5. Ajoutez vos fichiers d'application dans le répertoire racine de votre rôle de travail.
6. Publiez votre application en procédant comme indiqué dans la section [Publication de votre application](#publish-your-application) ci-dessous.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Exécuter votre application dans les émulateurs de calcul et de stockage
Les émulateurs Azure fournissent un environnement local dans lequel vous pouvez tester votre application Azure avant de la déployer sur le cloud. Il y a quelques différences entre les émulateurs et l'environnement Azure. Pour mieux comprendre cela, consultez [Utilisation de l’émulateur de stockage Azure pour le développement et le test](storage/storage-use-emulator.md).

Notez que PHP doit être installé en local pour pouvoir utiliser l’émulateur de calcul. Ce dernier utilise votre installation locale de PHP pour exécuter votre application.

Pour exécuter votre projet dans les émulateurs, exécutez la commande suivante à partir du répertoire racine de votre projet :

    PS C:\MyProject> Start-AzureEmulator

Vous verrez une sortie semblable à ceci :

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Pour voir votre application exécutée dans l’émulateur, ouvrez un navigateur web et accédez à l’adresse locale illustrée dans le résultat (`http://127.0.0.1:81` dans l’exemple ci-dessus).

Pour arrêter les émulateurs, exécutez la commande suivante :

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publication de votre application
Pour publier votre application, vous devez d’abord importer vos paramètres de publication en utilisant l’applet de commande [Import-AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) . Vous pouvez ensuite publier votre application à l’aide de l’applet de commande [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Pour plus d’informations sur la connexion, consultez [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

[Kit de développement logiciel (SDK) Azure pour PHP]: /develop/php/common-tasks/download-php-sdk/
[installer ps et les émulateurs]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[définition de service (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuration de service (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[client natif sql]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[pilotes sqlsrv]: http://php.net/sqlsrv
[programme d’installation x64 sqlncli.msi]: http://go.microsoft.com/fwlink/?LinkID=239648



<!--HONumber=Nov16_HO3-->


