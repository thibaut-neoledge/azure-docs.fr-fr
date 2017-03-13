---
title: "Installer .NET sur un rôle de service cloud | Microsoft Docs"
description: "Cet article explique comment installer manuellement le .NET Framework sur les rôles web et les rôles de travail de Cloud Services."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 0ac8ca0c5407925728ed0431294a3234b58d6e63
ms.openlocfilehash: 04506596ba21c3ebef7237eaad8c5d786ad672fe
ms.lasthandoff: 02/27/2017

---

# <a name="install-net-on-a-cloud-service-role"></a>Installer .NET sur un rôle de service cloud
Cet article décrit comment installer différentes versions de .NET Framework sur les rôles web de service cloud et de travail associés au SE invité. Par exemple, vous pouvez appliquer cette procédure pour installer .NET 4.6.1 sur le SE invité Azure Famille 4, qui n’est fourni avec aucune des versions de .NET 4.6. Pour obtenir les dernières informations sur les versions de SE invité, consultez les [Actualités concernant les versions de SE invité Azure](cloud-services-guestos-update-matrix.md).

>[!NOTE]
>Le SE invité 5 comprend .NET 4.6.

>[!IMPORTANT]
>Azure SDK 2.9 contient une restriction sur le déploiement de .NET 4.6 sur le SE invité 4 ou version inférieure. Un correctif est disponible [ici](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

Le processus d'installation de .NET sur les rôles web et les rôles de travail nécessite que vous incluiez le package du programme d'installation de .NET dans le cadre de votre projet cloud et que vous lanciez le programme d'installation dans le cadre des tâches de démarrage du rôle.  

## <a name="add-the-net-installer-to-your-project"></a>Ajouter le programme d'installation de .NET à votre projet
* Téléchargez le programme d'installation Web du .NET Framework que vous souhaitez installer
  * [Programme d’installation Web .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)
* Pour un rôle web
  1. Dans l’**Explorateur de solutions**, dans le projet de service cloud, sous **Rôles**, cliquez avec le bouton droit sur votre rôle, puis sélectionnez **Ajouter > Nouveau dossier**. Créez un dossier intitulé *bin*
  2. Cliquez avec le bouton droit sur le dossier **bin**, puis sélectionnez **Ajouter > Élément existant**. Sélectionnez le programme d'installation de .NET, puis ajoutez-le au dossier bin.
* Pour un rôle de travail
  1. Cliquez avec le bouton droit sur votre rôle, puis sélectionnez **Ajouter > Élément existant**. Sélectionnez le programme d'installation de .NET, puis ajoutez-le au rôle. 

Les fichiers ajoutés de cette façon dans le dossier de contenu du rôle seront automatiquement ajoutés au package de service cloud et déployés dans un emplacement similaire sur la machine virtuelle. Répétez ce processus pour tous les rôles web et tous les rôles de travail de Cloud Services, pour que tous les rôles disposent d'une copie du programme d'installation.

> [!NOTE]
> Même si votre application cible .NET 4.6, nous vous conseillons d’installer .NET 4.6.1 sur votre rôle de service cloud. Le système d’exploitation invité Azure inclut les mises à jour [3098779](https://support.microsoft.com/kb/3098779) et [3097997](https://support.microsoft.com/kb/3097997). Installer .NET 4.6 en plus de ces mises à jour peut causer des problèmes lors de l’exécution de vos applications .NET. C’est pourquoi nous vous conseillons d’installer directement .NET 4.6.1 au lieu de .NET 4.6. Pour plus d’informations, consultez [cet article de la base de connaissances](https://support.microsoft.com/kb/3118750).
> 
> 

![Contenu du rôle avec les fichiers d'installation][1]

## <a name="define-startup-tasks-for-your-roles"></a>Définir des tâches de démarrage pour vos rôles
Les tâches de démarrage permettent d'effectuer des opérations avant le démarrage d'un rôle. L'installation du .NET Framework dans le cadre de la tâche de démarrage permet de garantir qu'il sera installé avant l'exécution du code de votre application. Pour plus d'informations sur les tâches de démarrage, consultez [Exécuter des tâches de démarrage dans Azure](cloud-services-startup-tasks.md). 

1. Ajoutez le code suivant au fichier *ServiceDefinition.csdef* sous le nœud **WebRole** ou **WorkerRole** pour tous les rôles :
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    La configuration ci-dessus exécutera la commande de console *install.cmd* avec des privilèges d'administrateur pour pouvoir installer le .NET Framework. La configuration crée également un LocalStorage portant le nom *NETFXInstall*. Le script de démarrage définira le dossier temporaire pour l’utilisation de cette ressource de stockage local pour que le programme d’installation de .NET framework soit téléchargé et installé à partir de cette ressource. Il est important que la taille de cette ressource soit paramétrée, au minimum, sur 1 024 Mo pour garantir l’installation correcte du framework. Pour plus d’informations sur les tâches de démarrage, consultez [Tâches courantes de démarrage dans le service cloud](cloud-services-startup-tasks-common.md) 
2. Créez un fichier **install.cmd** et ajoutez-le à tous les rôles en cliquant avec le bouton droit sur chaque rôle, puis en sélectionnant **Ajouter > Élément existant**. Tous les rôles doivent maintenant avoir le fichier du programme d'installation de .NET, ainsi que le fichier install.cmd.
   
    ![Contenu du rôle avec tous les fichiers][2]
   
   > [!NOTE]
   > Utilisez un simple éditeur de texte tel que le Bloc-notes pour créer ce fichier. Si vous utilisez Visual Studio pour créer un fichier texte, puis le renommez « .cmd », le fichier peut toujours contenir une marque d'ordre d'octet UTF-8, et l'exécution de la première ligne du script générera une erreur. Si vous devez utiliser Visual Studio pour créer le fichier, ajoutez un REM (remarque) à la première ligne du fichier pour que celle-ci soit ignorée pendant l'exécution. 
   > 
   > 
3. Ajoutez le script suivant au fichier **install.cmd** :
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
   
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
   
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
   
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
   
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
   
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
   
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
   
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
   
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
   
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
   
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
   
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
   
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
    :exit
    EXIT /B 0
    ```
   
    Le script d'installation vérifie si la version de .NET Framework spécifiée est déjà installée sur l'ordinateur en interrogeant le Registre. Si la version de .NET n'est pas installée, le programme d'installation Web de .NET est lancé. Pour résoudre les éventuels problèmes, le script enregistre toutes les activités dans un fichier nommé *startuptasklog-(currentdatetime).txt* qui est stocké dans le stockage local *InstallLogs*.
   
   > [!NOTE]
   > Le script vous indique toujours comment installer .NET 4.5.2 ou .NET 4.6 à des fins de continuité. Il est inutile d’installer .NET 4.5.2 manuellement, car il est déjà disponible sur le système d’exploitation invité Azure. Au lieu d’installer .NET 4.6, nous vous conseillons d’installer directement .NET 4.6.1 en raison du problème décrit [ici](https://support.microsoft.com/kb/3118750).
   > 
   > 

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurer les diagnostics pour transférer les journaux des tâches de démarrage vers le stockage d’objets blob
Pour simplifier la résolution des problèmes d’installation, vous pouvez configurer Azure Diagnostics de façon à transférer tous les fichiers journaux générés par le script de démarrage ou le programme d’installation de .NET vers le stockage d’objets blob. Grâce à cette approche, vous pouvez afficher les journaux en téléchargeant les fichiers journaux depuis le stockage d'objets blob au lieu d'accéder au rôle via le Bureau à distance.

Pour configurer les diagnostics, ouvrez le fichier *diagnostics.wadcfgx* , puis ajoutez-y le code suivant sous le nœud **Directories** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Cela configurera Azure Diagnostics de façon à transférer tous les fichiers du répertoire *log* de la ressource *NETFXInstall* vers le compte de stockage Diagnostics dans le conteneur d’objets blob *netfx-install*.

## <a name="deploying-your-service"></a>Déploiement du service
Quand vous déployez un service, les tâches de démarrage s'exécutent et installent le .NET Framework, s'il n'est pas déjà installé. Vos rôles seront à l'état Occupé pendant l'installation du .NET Framework et peuvent même être redémarrés si l'installation le nécessite. 

## <a name="additional-resources"></a>Ressources supplémentaires
* [Installation du .NET Framework][Installing the .NET Framework]
* [Comment : déterminer les versions .NET Framework installées][How to: Determine Which .NET Framework Versions Are Installed]
* [Résolution des problèmes liés aux installations .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: https://msdn.microsoft.com/library/hh925568.aspx
[Installing the .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Troubleshooting .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



