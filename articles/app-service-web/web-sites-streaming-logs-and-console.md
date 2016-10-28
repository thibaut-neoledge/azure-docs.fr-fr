<properties 
	pageTitle="Journaux de diffusion en continu et console" 
	description="Vue d'ensemble des journaux et de la console de diffusion en continu" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/26/2016" 
	ms.author="byvinyal"/>

#Journaux en continu et console

### Journaux en continu ###

Le portail Microsoft Azure intègre une fonction d’affichage des journaux de diffusion en continu qui vous permet de suivre les événements de vos applications App Service en temps réel.

La configuration de cette fonction se fait en quelques étapes simples :

- Écriture des suivis dans votre code
- Activation du diagnostic d’application à partir du portail Azure
- Clic sur la partie des journaux en continu sur le volet de l’application web

### Écriture des suivis dans votre code ###

L'écriture des suivis dans votre code est simple. Dans C#, il suffit d'écrire le code suivant :

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La classe Trace se trouve dans l'espace de noms System.Diagnostics

Dans une application node.js, vous pouvez écrire le code suivant pour atteindre le même résultat :

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Activation et affichage des journaux en continu ###
![][BrowseSitesScreenshot] Les diagnostics sont activés par application web. Dans le [portail](https://portal.azure.com), naviguez jusqu'au site pour lequel vous souhaitez activer cette fonctionnalité.
  
![][DiagnosticsLogs] Puis cliquez sur **(1) Paramètres** > **(2) Journaux de diagnostic** et **(3) activez** le **Journal des applications (système de fichiers)** ou le **Journal des applications (blob)**. L’option **Niveau** vous permet de changer le niveau de gravité des suivis à capturer. Si vous souhaitez simplement vous familiariser avec la fonctionnalité, définissez-la sur **Verbose** pour être sûr que toutes vos instructions de suivi seront ajoutées.

Cliquez sur **SAVE** en haut du volet pour afficher les journaux.

**REMARQUE :** plus le **niveau de gravité** est élevé, plus les ressources consommées dans le fichier journal sont importantes et plus vous obtiendrez de suivis. Assurez-vous qu'il est défini au niveau approprié lors de l'utilisation de cette fonctionnalité pour un trafic élevé / site de production.

![][StreamingLogsScreenshot] Pour afficher les journaux de diffusion sur le portail, cliquez sur **(1) Outils** > **(2) Flux de journaux**. Si des instructions de suivi sont en cours d’écriture sur votre application, vous devez les voir presque en temps réel dans la fenêtre de résultat **(3)**.

## Console ##
Le portail Azure fournit un accès à la console à votre environnement d’application web. Vous pouvez explorer le système de fichiers de l’application web et exécuter les scripts powershell/cmd. Lorsque vous exécutez des commandes de la console, vous dépendez des mêmes autorisations que pour votre code d’application web en cours d’exécution. Vous ne pouvez pas accéder aux répertoires protégés ni exécuter des scripts qui demandent des autorisations élevées.

![][ConsoleScreenshot] Pour accéder à la console, ouvrez une application web comme décrit dans la section ci-dessus. Cliquez sur **(1) Outils**>**(2) Console** pour ouvrir la console **(3)**.

Pour vous familiariser avec la console, commencez par les commandes élémentaires suivantes :

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=AcomDC_0727_2016-->