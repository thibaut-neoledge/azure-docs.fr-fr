---
title: Tâches de démarrage courantes pour Cloud Services | Microsoft Docs
description: Fournit des exemples courants de tâches de démarrage que vous pouvez effectuer dans votre rôle de travail ou web de services cloud.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: adegeo

---
# Tâches courantes de démarrage dans le service cloud
Cet article fournit des exemples courants de tâches de démarrage que vous pouvez effectuer dans votre service cloud. Vous pouvez utiliser des tâches de démarrage pour exécuter des opérations avant le démarrage d’un rôle. Parmi les opérations que vous pouvez effectuer figurent l’installation d’un composant, l’enregistrement de composants COM, la définition des clés du Registre ou le démarrage d’un processus de longue durée.

Consultez [cet article](cloud-services-startup-tasks.md) pour comprendre comment fonctionnent les tâches de démarrage et, en particulier, comment créer des entrées qui définissent une tâche de démarrage.

De nombreuses tâches ici utilisent le

> [!NOTE]
> Les tâches de démarrage ne s’appliquent pas aux rôles de machine virtuelle ; elles ne concernent que les rôles web de service cloud et de travail.
> 
> 

## Définir des variables d’environnement avant le démarrage d’un rôle
Si des variables d’environnement doivent être définies pour une tâche spécifique, utilisez l’élément [Environment] à l’intérieur de l’élément [Task].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Les variables peuvent également utiliser une [valeur Azure XPath valide](cloud-services-role-config-xpath.md) pour faire référence à des informations sur le déploiement. Au lieu d’utiliser l’attribut `value`, définissez un élément enfant [RoleInstanceValue].

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## Configurer le démarrage d’IIS avec AppCmd.exe
L’outil en ligne de commande [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) peut être utilisé pour gérer les paramètres IIS au démarrage sur Azure. *AppCmd.exe* fournit un accès en ligne de commande pratique aux paramètres de configuration à utiliser dans les tâches de démarrage sur Azure. *AppCmd.exe* permet d’ajouter, de modifier ou de supprimer des paramètres de site web pour les applications et les sites.

Toutefois, tenez compte des points suivants quand vous utilisez *AppCmd.exe* en tant que tâche de démarrage :

* Les tâches de démarrage peuvent être exécutées plusieurs fois entre les redémarrages. Cela peut se produire si le rôle est recyclé, par exemple.
* Certaines actions *AppCmd.exe* peuvent générer des erreurs si elles sont exécutées plusieurs fois. Ajouter une section à *Web.config* deux fois peut générer une erreur.
* Les tâches de démarrage échouent si elles retournent un code de sortie non nul ou **errorlevel**. Cela peut se produire si *AppCmd.exe* génère une erreur.

Pour les raisons indiquées, il est souvent judicieux de vérifier **errorlevel** après avoir appelé *AppCmd.exe*, opération que vous pouvez effectuez facilement si vous encapsulez l’appel à *AppCmd.exe* avec un fichier *.cmd*. Si vous détectez une réponse **errorlevel** connue, vous pouvez l’ignorer, ou bien la retourner. Cela est illustré dans l’exemple ci-dessous.

Les codes errorlevel retournés par *AppCmd.exe* sont répertoriés dans le fichier winerror.h, et peuvent également être consultés sur [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### Exemple
Cet exemple ajoute une section de compression et une entrée de compression pour JSON au fichier *Web.config* , avec gestion et journalisation des erreurs.

Les sections pertinentes du fichier [ServiceDefinition.csdef] sont indiquées ici, avec notamment l’attribut [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) défini sur `elevated` pour qu’ *AppCmd.exe* soit autorisé à modifier les paramètres du fichier *Web.config* :

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Le fichier de commandes Startup.cmd utilise AppCmd.exe pour ajouter une section de compression et une entrée de compression pour JSON au fichier *Web.config*. Le code **errorlevel** attendu 183 est défini sur zéro à l’aide du programme en ligne de commande VERIFY.EXE. Les codes errorlevel inattendus sont enregistrés dans StartupErrorLog.txt.

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )

    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )

    REM   *** Exit batch file. ***
    EXIT /b 0

    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%


## Ajouter des règles de pare-feu
Dans Azure, il existe en fait deux pare-feu. Le premier pare-feu contrôle les connexions entre la machine virtuelle et le monde extérieur. L’élément [EndPoints] du fichier [ServiceDefinition.csdef] est chargé de ce contrôle.

Le second pare-feu contrôle les connexions entre la machine virtuelle et les processus au sein de cette dernière. L’outil en ligne de commande `netsh advfirewall firewall` est chargé de ce contrôle, qui constitue l’essentiel de cet article.

Azure crée des règles de pare-feu pour les processus démarrés au sein de vos rôles. Par exemple, quand vous démarrez un service ou un programme, Azure crée automatiquement les règles de pare-feu nécessaires pour permettre à ce service de communiquer avec Internet. Toutefois, si vous créez un service qui est démarré par un processus en dehors de votre rôle (par exemple, un service COM+ ou un programme qui démarre en utilisant le Planificateur de Windows), vous devez créer manuellement une règle de pare-feu pour autoriser l’accès à ce service. Ces règles de pare-feu peuvent être créées à l’aide d’une tâche de démarrage.

Une tâche de démarrage qui crée une règle de pare-feu doit avoir [executionContext][Task] défini sur **elevated**. Ajoutez la tâche de démarrage suivante au fichier [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Pour ajouter la règle de pare-feu, vous devez utiliser les commandes `netsh advfirewall firewall` appropriées dans votre fichier de commandes de démarrage. Dans cet exemple, la tâche de démarrage nécessite les fonctionnalités de sécurité et de chiffrement pour le port TCP 80.

    REM   Add a firewall rule in a startup task.

    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


## Bloquer une adresse IP spécifique
Vous pouvez restreindre l’accès d’un rôle web Azure à un ensemble d’adresses IP spécifiques en modifiant votre fichier IIS **web.config** et en créant un fichier de commandes qui déverrouille la section **ipSecurity** du fichier **ApplicationHost.config**.

Tout d’abord, créez un fichier de commandes qui s’exécute au démarrage de votre rôle et qui permet de déverrouiller la section **ipSecurity** du fichier **ApplicationHost.config**. Créez un dossier à la racine de votre rôle web appelé **startup** et, dans ce dossier, créez un fichier de commandes appelé **startup.cmd**. Définissez les propriétés de ce fichier sur **Toujours copier** pour vous assurer qu’il sera déployé.

Ajoutez la tâche de démarrage suivante au fichier [ServiceDefinition.csdef].

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Ajoutez cette commande au fichier **startup.cmd** :

    @echo off
    @echo Installing "IPv4 Address and Domain Restrictions" feature 
    powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
    @echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

Ainsi, le fichier de commandes **startup.cmd** s’exécute chaque fois que le rôle web est initialisé et la section **ipSecurity** nécessaire est systématiquement déverrouillée.

Enfin, modifiez la [section system.webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) du fichier **web.config** de votre rôle web pour ajouter une liste d’adresses IP bénéficiant d’un droit d’accès, comme illustré dans l’exemple suivant :

Cet exemple de configuration **permet** à toutes les adresses IP d’accéder au serveur, sauf aux deux adresses IP définies.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Cet exemple de configuration **refuse** à toutes les adresses IP le droit d’accéder au serveur, sauf aux deux adresses IP définies.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## Créer une tâche de démarrage PowerShell
Les scripts Windows PowerShell ne peuvent pas être appelés directement à partir du fichier [ServiceDefinition.csdef], mais ils peuvent être appelés à partir d’un fichier de commandes de démarrage.

PowerShell, par défaut, n’exécute aucun script non signé. Sauf si vous signez vos scripts, vous devez configurer Windows PowerShell pour exécuter les scripts non signés. Pour exécuter les scripts non signés, **ExecutionPolicy** doit avoir la valeur **Unrestricted**. Le paramètre **ExecutionPolicy** que vous utilisez est basé sur la version de Windows PowerShell.

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%


Si vous utilisez un SE invité qui exécute PowerShell 2.0 ou 1.0, vous pouvez forcer la version 2 à s’exécuter et, si elle n’est pas disponible, utiliser la version 1.

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (

       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )

    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## Créer des fichiers dans le stockage local à partir d’une tâche de démarrage
Vous pouvez utiliser une ressource de stockage local pour stocker les fichiers créés par votre tâche de démarrage afin de les rendre accessibles à votre application.

Pour créer la ressource de stockage local, ajoutez une section [LocalResources] au fichier [ServiceDefinition.csdef], puis ajoutez l’élément enfant [LocalStorage] . Donnez à la ressource de stockage local un nom unique et une taille appropriée pour votre tâche de démarrage.

Pour utiliser une ressource de stockage local dans votre tâche de démarrage, vous devez créer une variable d’environnement pour faire référence à l’emplacement de la ressource de stockage local. Ensuite, la tâche de démarrage et l’application sont en mesure de lire et d’écrire des fichiers dans la ressource de stockage local.

Les sections appropriées du fichier **ServiceDefinition.csdef** sont indiquées ici :

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...

        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>

        ...

        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>

        ...

        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Par exemple, ce fichier de commandes **Startup.cmd** emploie la variable d’environnement **PathToStartupStorage** pour créer le fichier **MyTest.txt** à l’emplacement du stockage local.

    REM   Create a simple text file.

    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

    REM   Exit the batch file with ERRORLEVEL 0.

    EXIT /b 0

Vous pouvez accéder au stockage local à partir du Kit de développement logiciel (SDK) Azure à l’aide de la méthode [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Les opérations de lecture et d’écriture de fichier standard permettent alors de lire et d’écrire le contenu de la ressource de stockage local.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## Faire la différence entre l’exécution dans l’émulateur et l’exécution dans le cloud
Si vous le souhaitez, les étapes suivies par votre tâche de démarrage peuvent différer selon qu’elle s’exécute dans le cloud ou dans l’émulateur de calcul. Par exemple, vous pouvez utiliser une nouvelle copie de vos données SQL uniquement quand l’exécution a lieu dans l’émulateur. De même, vous pouvez effectuer des opérations d’optimisation de performances pour le cloud qui sont superflues dans l’émulateur.

Pour pouvoir effectuer ainsi différentes actions sur l’émulateur de calcul et sur le cloud, vous devez créer une variable d’environnement dans le fichier [ServiceDefinition.csdef], puis tester la variable d’environnement dans votre tâche de démarrage.

Pour créer la variable d’environnement, ajoutez l’élément [Variable]/[RoleInstanceValue] et créez une valeur XPath `/RoleEnvironment/Deployment/@emulated`. La valeur de la variable d’environnement **ComputeEmulatorRunning %** est `"true"` si l’exécution a lieu sur l’émulateur de calcul et `"false"` sur le cloud.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...

        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

Toute exécution de la tâche peut désormais utiliser la variable d’environnement **ComputeEmulatorRunning %** pour effectuer différentes actions selon que le rôle s’exécute ou non dans le cloud ou l’émulateur. Voici un script shell .cmd qui vérifie cette variable d’environnement.

    REM   Check if this task is running on the compute emulator.

    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.

    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.

    )


## Détecter que la tâche a déjà été exécutée
Si le rôle se recycle sans qu’un redémarrage ait lieu, vos tâches de démarrage sont de nouveau exécutées. Un indicateur permet de signaler qu’une tâche a déjà été exécutée sur la machine virtuelle hôte. Il est possible que certaines tâches s’exécutent plusieurs fois sans engendrer de problème. Toutefois, dans certaines situations, vous devez éviter qu’une tâche s’exécute plusieurs fois.

La façon la plus simple de détecter qu’une tâche a déjà été exécutée consiste à créer un fichier dans le dossier **%TEMP%** quand la tâche réussit et à le rechercher au démarrage de la tâche. Voici un exemple de script shell cmd qui effectue cette opération.

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )

    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.

      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"

    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.

      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

      EXIT %ERRORLEVEL%
    )

    :Finish

    REM   Exit normally.
    EXIT /B 0

## Meilleures pratiques pour les tâches
Voici quelques meilleures pratiques à suivre quand vous configurez la tâche pour votre rôle web ou de travail.

### Toujours consigner les activités de démarrage
Comme Visual Studio ne fournit pas de débogueur pour parcourir les fichiers de commandes, il est préférable de récupérer autant de données que possible sur le fonctionnement des fichiers de commandes. La consignation de la sortie des fichiers de commandes, **stdout** et **stderr**, peut fournir des informations importantes au moment du débogage et de la correction des fichiers de commandes. Pour consigner **stdout** et **stderr** dans le fichier StartupLog.txt dans le répertoire indiqué par la variable d’environnement **%TEMP%**, ajoutez le texte `>>  "%TEMP%\\StartupLog.txt" 2>&1` à la fin des lignes que vous souhaitez enregistrer. Par exemple, pour exécuter setup.exe dans le répertoire **% PathToApp1Install** :

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Si vous souhaitez consigner toute la sortie de la tâche de démarrage sans ajouter `>> "%TEMP%\StartupLog.txt" 2>&1`à la fin de chaque ligne, deux fichiers de commandes de démarrage sont nécessaires. Le premier fichier de commandes appelle le second fichier de commandes avec une redirection pour consigner toutes les activités du second fichier de commandes. Cette opération est nécessaire pour que la redirection appropriée se produise.

Voici comment rediriger toute la sortie d’un fichier de commandes de démarrage. Dans cet exemple, le fichier ServerDefinition.csdef crée une tâche de démarrage qui appelle Startup1.cmd. Startup1.cmd appelle Startup2.cmd, en redirigeant toute la sortie vers % TEMP%\\StartupLog.txt.

ServiceDefinition.cmd :

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd :

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.

    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1

    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1

    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd :

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### Définir executionContext convenablement pour les tâches de démarrage
Définissez les privilèges convenablement pour la tâche de démarrage. Parfois, les tâches de démarrage doivent s’exécuter avec des privilèges élevés même si le rôle s’exécute avec des privilèges normaux.

L’attribut [executionContext][Task] définit le niveau de privilège de la tâche de démarrage. Si `executionContext="limited"` est utilisé, la tâche de démarrage a le même niveau de privilège que le rôle. Si `executionContext="elevated"` est utilisé, la tâche de démarrage a des privilèges d’administrateur, ce qui lui permet d’effectuer des tâches d’administrateur sans accorder de privilèges d’administrateur à votre rôle.

Un exemple de tâche de démarrage qui nécessite des privilèges élevés est une tâche de démarrage qui utilise **AppCmd.exe** pour configurer IIS. **AppCmd.exe** nécessite `executionContext="elevated"`.

### Utiliser l’attribut taskType approprié
L’attribut [taskType][Task] détermine la façon dont la tâche de démarrage doit être exécutée. Trois valeurs sont possibles : **simple**, **background** et **foreground**. Les tâches en arrière-plan et de premier plan sont lancées de manière asynchrone, puis les tâches simples sont exécutées de façon synchrone une par une.

Dans le cas des tâches de démarrage **simple**, vous pouvez définir l’ordre dans lequel les tâches doivent être exécutées en fonction de l’ordre dans lequel elles sont répertoriées dans le fichier ServiceDefinition.csdef. Si une tâche **simple** se termine par un code de sortie différent de zéro, la procédure de démarrage s’arrête et le rôle ne démarre pas.

La différence entre une tâche de démarrage **background** et une tâche de démarrage **foreground** est que dans le cas d’une tâche **foreground** le rôle continue de s’exécuter jusqu’au terme de la tâche **foreground**. Cela signifie également qu’en cas de blocage ou de défaillance de la tâche **foreground**, le rôle n’est pas recyclé tant que la tâche **foreground** n’a pas fait l’objet d’une fermeture forcée. Pour cette raison, les tâches **background** sont recommandées pour les tâches de démarrage asynchrones sauf si vous avez besoin de cette fonctionnalité de la tâche **foreground**.

### Terminer des fichiers de commandes par EXIT /B 0
Le rôle ne démarre que si le code **errorlevel** de chacune de vos tâches de démarrage simples est égal à zéro. Comme tous les programmes ne définissent pas correctement le code **errorlevel** (code de sortie), le fichier de commandes doit se terminer par un `EXIT /B 0` si tout s’est exécuté correctement.

L’absence d’un `EXIT /B 0` à la fin d’un fichier de commandes de démarrage est une cause courante du non-démarrage des rôles.

### Prévoir plusieurs exécutions des tâches de démarrage
Tous les recyclages de rôle n’incluent pas un redémarrage, mais ils comprennent tous l’exécution de toutes les tâches de démarrage. Cela signifie que les tâches de démarrage doivent être en mesure de s’exécuter plusieurs fois entre les redémarrages sans problème. Cet aspect est abordé [plus haut](#detect-that-your-task-has-already-run).

### Utiliser le stockage local pour stocker les fichiers qui doivent être accessibles dans le rôle
Si vous souhaitez copier ou créer un fichier pendant votre tâche de démarrage qui soit accessible à votre rôle, ce fichier doit être placé dans le stockage local. Consultez la [section](#create-files-in-local-storage-from-a-startup-task) plus haut.

## Étapes suivantes
Examiner [le modèle et le package de service cloud](cloud-services-model-and-package.md)

En savoir plus sur le fonctionnement des [tâches](cloud-services-startup-tasks.md).

[Créez et déployez](cloud-services-how-to-create-deploy-portal.md) votre package de service cloud.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue

<!---HONumber=AcomDC_0914_2016-->