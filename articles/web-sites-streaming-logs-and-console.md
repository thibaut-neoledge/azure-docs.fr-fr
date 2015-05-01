<properties 
	pageTitle="Journaux de diffusion en continu et console" 
	description="Vue d'ensemble des journaux et de la console de diffusion en continu" 
	authors="adamabdelhamed" 
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
	ms.date="11/17/2014" 
	ms.author="adamab"/>

#Journaux en continu et console

### Journaux de diffusion en continu ###

Le portail Microsoft Azure intègre une fonction d'affichage des journaux de diffusion en continu qui vous permet de suivre les événements de vos applications web Azure App Service en temps réel.  

La configuration de cette fonction se fait en quelques étapes simples :

- Écriture des suivis dans votre code
- Activation du diagnostic d'application à partir du portail Azure
- Clic sur la partie des journaux en continu sur le volet de l'application web

### Écriture des suivis dans votre code ###

L'écriture des suivis dans votre code est simple.  Dans C#, il suffit d'écrire le code suivant :

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La classe Trace réside dans l’espace de noms System.Diagnostics.

In a node.js app you can write this code to achieve the same result:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Activation et affichage des journaux de diffusion en continu ###

Les diagnostics sont activés par application web.  Au sein du [portail](https://portal.azure.com), cliquez sur le bouton **Parcourir**, situé dans la barre de menus à gauche, puis cliquez sur **Applications Web** pour afficher la liste de toutes vos applications web.  

![][BrowseSitesScreenshot]

Cliquez sur le nom de l'application web que vous souhaitez configurer.  Cliquez ensuite sur la partie **JOURNAUX DE DIAGNOSTIC** et définissez le commutateur **Journal des applications (système de fichiers)** sur **ON**.  L'option **Niveau** s'affiche et vous permet de modifier le niveau de gravité des suivis à capturer.  Si vous souhaitez simplement vous familiariser avec la fonctionnalité, définissez-la sur **Détaillée** pour être sûr que toutes vos instructions de suivi seront ajoutées.

Cliquez sur **ENREGISTRER** en haut du volet pour afficher les journaux.

Pour afficher les journaux en continu sur le portail, cliquez sur la partie **JOURNAUX DE DIFFUSION** du volet du site web.  Si des instructions de suivi sont en cours d'écriture sur votre application, vous devez les voir presque en temps réel dans la fenêtre de résultat.

![][StreamingLogsScreenshot]

## Console ##

Le portail Azure fournit un accès à la console à votre environnement d'application web. Vous pouvez explorer le système de fichiers de l'application web et exécuter les scripts powershell/cmd.  Lorsque vous exécutez des commandes de la console, vous dépendez des mêmes autorisations que pour votre code d'application web en cours d'exécution. Vous ne pouvez pas accéder aux répertoires protégés ni exécuter des scripts qui demandent des autorisations élevées.  

Pour accéder à la console, ouvrez une application web comme décrit dans la section ci-dessus.  Cliquez sur la partie **Console** pour ouvrir la console.

![][ConsoleScreenshot]

Pour vous familiariser avec la console, commencez par les commandes élémentaires suivantes :



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png


<!--HONumber=52-->