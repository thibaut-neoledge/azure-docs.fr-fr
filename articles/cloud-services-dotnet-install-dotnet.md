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
   ms.date="04/23/2015"
   ms.author="saurabh"/>

# Installer .NET sur un rôle de service cloud 

Cet article explique comment installer le .NET Framework sur les rôles web et les rôles de travail de Cloud Services. Vous pouvez utiliser ces étapes pour installer une version du .NET Framework qui n'est pas présente sur le système d'exploitation invité Azure par défaut. Pour obtenir les dernières informations sur les versions du système d'exploitation invité, consultez [Versions du système d'exploitation invité d'Azure et matrice de compatibilité du Kit de développement logiciel (SDK)](cloud-services-guestos-update-matrix.md).

Le processus d'installation de .NET sur les rôles web et les rôles de travail nécessite que vous incluiez le package du programme d'installation de .NET dans le cadre de votre projet cloud et que vous lanciez le programme d'installation dans le cadre des tâches de démarrage du rôle.

## Ajouter le programme d'installation de .NET à votre projet
1. Téléchargez le [programme d'installation web de .NET 4.5.2](http://www.microsoft.com/fr-fr/download/details.aspx?id=42643).
2. Pour un rôle web
  1. Dans l'**Explorateur de solutions**, dans le projet de service cloud, sous **Rôles**, cliquez avec le bouton droit sur votre rôle, puis sélectionnez **Ajouter > Nouveau dossier**. Créez un dossier intitulé *bin*.
  2. Cliquez avec le bouton droit sur le dossier **bin**, puis sélectionnez **Ajouter > Élément existant**. Sélectionnez le programme d'installation de .NET, puis ajoutez-le au dossier bin.
3. Pour un rôle de travail
  1. Cliquez avec le bouton droit sur votre rôle, puis sélectionnez **Ajouter > Élément existant**. Sélectionnez le programme d'installation de .NET, puis ajoutez-le au rôle. 

Les fichiers ajoutés de cette façon dans le dossier de contenu du rôle seront automatiquement ajoutés au package de service cloud et déployés dans un emplacement similaire sur la machine virtuelle. Répétez ce processus pour tous les rôles web et tous les rôles de travail de Cloud Services, pour que tous les rôles disposent d'une copie du programme d'installation.

![Contenu du rôle avec les fichiers d'installation][1]

## Définir des tâches de démarrage pour vos rôles
Les tâches de démarrage permettent d'effectuer des opérations avant le démarrage d'un rôle. L'installation du .NET Framework dans le cadre de la tâche de démarrage permet de garantir qu'il sera installé avant l'exécution du code de votre application. Pour plus d'informations sur les tâches de démarrage, consultez [Exécuter des tâches de démarrage dans Azure](https://msdn.microsoft.com/library/azure/hh180155.aspx).

1. Ajoutez le code suivant au fichier *ServiceDefinition.csdef* sous le nœud *WebRole* ou *WorkerRole* pour tous les rôles :
	
	```xml
	 <LocalResources>
	    <LocalStorage name="InstallLogs" sizeInMB="5" cleanOnRoleRecycle="false" />
	 </LocalResources>
	 <Startup>
	    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
	        <Environment>
	        <Variable name="PathToInstallLogs">
	        <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='InstallLogs']/@path" />
	        </Variable>
	        </Environment>
	    </Task>
	 </Startup>
	```

	La configuration ci-dessus exécutera la commande de console *install.cmd* avec des privilèges d'administrateur pour pouvoir installer le .NET Framework. La configuration crée également un stockage local intitulé *InstallLogs* pour stocker les informations de journalisation créées par le script d'installation. Pour plus d'informations, consultez [Utilisation du stockage local pour stocker des fichiers au démarrage](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. Créez le fichier install.cmd avec le contenu suivant :

	```
	REM install.cmd to install .NET Framework
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set startuptasklog=%PathToInstallLogs%\startuptasklog-%timestamp%.txt
	set netfxinstallerlog = %PathToInstallLogs%\NetFXInstallerLog-%timestamp%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo Checking if .NET 4.5.2 is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find "0x5cbf5"
	if %ERRORLEVEL%== 0 goto end
	echo Installing .NET 4.5.2. Log: %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0NDP452-KB2901954-Web.exe /q /serialdownload /log %netfxinstallerlog%
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	```

	Le script d'installation vérifie si .NET 4.5.2 est déjà installé sur l'ordinateur en interrogeant le Registre. Si .NET 4.5.2 n'est pas installé, le programme d'installation web de .NET est lancé. Pour résoudre les éventuels problèmes, le script enregistre toutes les activités dans un fichier nommé *startuptasklog-(currentdatetime).txt* qui est stocké dans le stockage local *InstallLogs*.

	> [AZURE.NOTE]Utilisez un simple éditeur de texte tel que le Bloc-notes pour créer ce fichier. Si vous utilisez Visual Studio pour créer un fichier texte, puis le renommez « .cmd », le fichier peut toujours contenir une marque d'ordre d'octet UTF-8, et l'exécution de la première ligne du script générera une erreur. Si vous devez utiliser Visual Studio pour créer le fichier, ajoutez un REM (remarque) à la première ligne du fichier pour que celle-ci soit ignorée pendant l'exécution.
      
3. Ajoutez le fichier install.cmd pour tous les rôles en cliquant avec le bouton droit sur chaque rôle, puis en sélectionnant **Ajouter > Élément existant**. Tous les rôles doivent maintenant avoir le fichier du programme d'installation de .NET, ainsi que le fichier install.cmd.
	
	![Contenu du rôle avec tous les fichiers][2]

## Configurer les diagnostics pour transférer les journaux des tâches de démarrage vers le stockage d'objets blob (facultatif)
Vous pouvez, si vous le souhaitez, configurer Azure Diagnostics de façon à transférer tous les fichiers journaux générés par le script de démarrage ou le programme d'installation de .NET vers le stockage d'objets blob. Grâce à cette approche, vous pouvez afficher les journaux en téléchargeant les fichiers journaux depuis le stockage d'objets blob au lieu d'accéder au rôle via le Bureau à distance.

Pour configurer les diagnostics, ouvrez le fichier *diagnostics.wadcfgx*, puis ajoutez-y le code suivant sous le nœud *<Directories>* :

```xml 
<DataSources>
    <DirectoryConfiguration containerName="netfx-install">
    <LocalResource name="InstallLogs" relativePath="."/>
    </DirectoryConfiguration>
</DataSources>
```

Cela configurera Azure Diagnostics de façon à transférer tous les fichiers de la ressource *InstallLogs* vers le compte de stockage Diagnostics dans le conteneur d'objets blob *netfx-install*.

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


<!--HONumber=52-->
