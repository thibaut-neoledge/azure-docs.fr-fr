<properties
   pageTitle="Installer .NET sur un rôle de service cloud"
   description="Cet article explique comment installer manuellement le .NET Framework sur les rôles web et les rôles de travail de Cloud Services."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/11/2015"
   ms.author="saurabh"/>

# Installer .NET sur un rôle de service cloud 

Cet article explique comment installer le .NET Framework sur les rôles web et les rôles de travail de Cloud Services. Vous pouvez utiliser cette procédure pour installer le .NET Framework 4.5.2 ou .NET 4.6 sur la famille 4 de systèmes d’exploitation invités Azure. Pour obtenir les dernières informations sur les versions du système d'exploitation invité, consultez [Versions du système d'exploitation invité d'Azure et matrice de compatibilité du Kit de développement logiciel (SDK)](cloud-services-guestos-update-matrix.md).

Le processus d'installation de .NET sur les rôles web et les rôles de travail nécessite que vous incluiez le package du programme d'installation de .NET dans le cadre de votre projet cloud et que vous lanciez le programme d'installation dans le cadre des tâches de démarrage du rôle.

## Ajouter le programme d'installation de .NET à votre projet
1. Téléchargez le programme d'installation Web du .NET Framework que vous souhaitez installer
	- [Programme d’installation Web .NET 4.5.2](http://go.microsoft.com/fwlink/p/?LinkId=397703)
	- [Programme d’installation Web .NET 4.6](http://go.microsoft.com/fwlink/?LinkId=528259)
	- [Programme d’installation Web .NET 4.6.1](http://go.microsoft.com/fwlink/?LinkId=671729)
2. Pour un rôle web
  1. Dans l'**Explorateur de solutions**, dans le projet de service cloud, sous **Rôles**, cliquez avec le bouton droit sur votre rôle, puis sélectionnez **Ajouter > Nouveau dossier**. Créez un dossier intitulé *bin*.
  2. Cliquez avec le bouton droit sur le dossier **bin**, puis sélectionnez **Ajouter > Élément existant**. Sélectionnez le programme d'installation de .NET, puis ajoutez-le au dossier bin.
3. Pour un rôle de travail
  1. Cliquez avec le bouton droit sur votre rôle, puis sélectionnez **Ajouter > Élément existant**. Sélectionnez le programme d'installation de .NET, puis ajoutez-le au rôle. 

Les fichiers ajoutés de cette façon dans le dossier de contenu du rôle seront automatiquement ajoutés au package de service cloud et déployés dans un emplacement similaire sur la machine virtuelle. Répétez ce processus pour tous les rôles web et tous les rôles de travail de Cloud Services, pour que tous les rôles disposent d'une copie du programme d'installation.

![Contenu du rôle avec les fichiers d'installation][1]

## Définir des tâches de démarrage pour vos rôles
Les tâches de démarrage permettent d'effectuer des opérations avant le démarrage d'un rôle. L'installation du .NET Framework dans le cadre de la tâche de démarrage permet de garantir qu'il sera installé avant l'exécution du code de votre application. Pour plus d'informations sur les tâches de démarrage, consultez [Exécuter des tâches de démarrage dans Azure](https://msdn.microsoft.com/library/azure/hh180155.aspx).

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
        </Environment>
      </Task>
    </Startup>
	```

	La configuration ci-dessus exécutera la commande de console *install.cmd* avec des privilèges d'administrateur pour pouvoir installer le .NET Framework. La configuration crée également un LocalStorage portant le nom *NETFXInstall*. Le script de démarrage définira le dossier temporaire pour l’utilisation de cette ressource de stockage local pour que le programme d’installation de .NET framework soit téléchargé et installé à partir de cette ressource. Il est important que la taille de cette ressource soit paramétrée, au minimum, sur 1 024 Mo pour garantir l’installation correcte du framework. Pour plus d'informations, consultez [Utilisation du stockage local pour stocker des fichiers au démarrage](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. Créez un fichier **install.cmd** et ajoutez-le à tous les rôles en cliquant avec le bouton droit sur le rôle, puis en sélectionnant **Ajouter > Élément existant…**. Tous les rôles doivent maintenant avoir le fichier du programme d'installation de .NET, ainsi que le fichier install.cmd.
	
	![Contenu du rôle avec tous les fichiers][2]

	> [AZURE.NOTE]Utilisez un simple éditeur de texte tel que le Bloc-notes pour créer ce fichier. Si vous utilisez Visual Studio pour créer un fichier texte, puis le renommez « .cmd », le fichier peut toujours contenir une marque d'ordre d'octet UTF-8, et l'exécution de la première ligne du script générera une erreur. Si vous devez utiliser Visual Studio pour créer le fichier, ajoutez un REM (remarque) à la première ligne du fichier pour que celle-ci soit ignorée pendant l'exécution.

3. Ajoutez le script suivant au fichier **install.cmd** :

	```
	REM Set the value of netfx to install appropriate .NET Framework. 
	REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
	REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
	REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
	set netfx="NDP46"
		
	
	REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
	set TMP=%PathToNETFXInstall%
	set TEMP=%PathToNETFXInstall%
	
		
	REM ***** Setup .NET filenames and registry keys *****
	if %netfx%=="NDP461" goto NDP461
	if %netfx%=="NDP46" goto NDP46
	    set netfxinstallfile="NDP452-KB2901954-Web.exe"
	    set netfxregkey="0x5cbf5"
	    goto logtimestamp
		
	:NDP46
	set netfxinstallfile="NDP46-KB3045560-Web.exe"
	set netfxregkey="0x60051"
	goto logtimestamp
		
	:NDP461
	set netfxinstallfile="NDP461-KB3102438-Web.exe"
	set netfxregkey="0x6041f"
		
	:logtimestamp
	REM ***** Setup LogFile with timestamp *****
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	md "%PathToNETFXInstall%\log"
	set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
	set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
	
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo TMP set to: %TMP% >> %startuptasklog%
	echo TEMP set to: %TEMP% >> %startuptasklog%
	
	REM ***** Check if .NET is installed *****
	echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
	if %ERRORLEVEL%== 0 goto end
		
	REM ***** Installing .NET *****
	echo Installing .NET: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog% 2>>&1
		
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%

	```
	
	> [AZURE.IMPORTANT]Mettez à jour la valeur de la variable *netfx* dans le script pour qu’elle corresponde à la version du Framework que vous souhaitez installer. Pour installer .NET 4.5.2 la variable *netfx* doit être définie sur *NDP452*, pour installer .NET 4.6 la variable *netfx* doit être définie sur *NDP46* et pour installer .NET 4.6.1 la variable *netfx* doit^être définie sur *NDP461*
		
	Le script d'installation vérifie si la version de .NET Framework spécifiée est déjà installée sur l'ordinateur en interrogeant le Registre. Si la version de .NET n'est pas installée, le programme d'installation Web de .NET est lancé. Pour résoudre les éventuels problèmes, le script enregistre toutes les activités dans un fichier nommé *startuptasklog-(currentdatetime).txt* qui est stocké dans le stockage local *InstallLogs*.
 
      

## Configurer les diagnostics pour transférer les journaux des tâches de démarrage vers le stockage d'objets blob (facultatif)
Vous pouvez, si vous le souhaitez, configurer Azure Diagnostics de façon à transférer tous les fichiers journaux générés par le script de démarrage ou le programme d'installation de .NET vers le stockage d'objets blob. Grâce à cette approche, vous pouvez afficher les journaux en téléchargeant les fichiers journaux depuis le stockage d'objets blob au lieu d'accéder au rôle via le Bureau à distance.

Pour configurer les diagnostics, ouvrez le fichier *diagnostics.wadcfgx*, puis ajoutez-y le code suivant sous le nœud **Directories** :

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Cela configurera Azure Diagnostics de façon à transférer tous les fichiers du répertoire *log* de la ressource *NETFXInstall* vers le compte de stockage Diagnostics dans le conteneur d’objets blob *netfx-install*.

## Déploiement du service 
Quand vous déployez un service, les tâches de démarrage s'exécutent et installent le .NET Framework, s'il n'est pas déjà installé. Vos rôles seront à l'état Occupé pendant l'installation du .NET Framework et peuvent même être redémarrés si l'installation le nécessite.


## Ressources supplémentaires

- [Installation du .NET Framework][]
- [Comment : déterminer les versions .NET Framework installées][]
- [Résolution des problèmes liés aux installations .NET Framework][]

[Comment : déterminer les versions .NET Framework installées]: https://msdn.microsoft.com/library/hh925568.aspx
[Installation du .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Résolution des problèmes liés aux installations .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=AcomDC_1217_2015-->