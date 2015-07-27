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
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#Journaux en continu et console

### Journaux en continu ###

La [version préliminaire du portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715) intègre une fonction d’affichage des journaux de diffusion en continu qui vous permet de suivre les événements de vos applications Web [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) en temps réel.

La configuration de cette fonction se fait en quelques étapes simples :

- Écriture des suivis dans votre code
- Activation du diagnostic d'application à partir de la version préliminaire du portail Azure
- Clic sur la vignette des journaux en continu sur le volet de l’application Web

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

### Activation et affichage des journaux de diffusion en continu ###

Les diagnostics sont activés par application web.

1. Depuis la [version préliminaire du portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Web Apps** pour obtenir la liste de toutes vos applications Web.  

2. Cliquez sur le nom de l’application web que vous souhaitez configurer.

3. Cliquez sur **Tous les paramètres** > **Journaux de diagnostic** et définissez le commutateur **Journal des applications (système de fichiers)** sur **ON**.

4. L'option **Level** s'affiche et vous permet de modifier le niveau de gravité des suivis à capturer. Si vous souhaitez simplement vous familiariser avec la fonctionnalité, définissez-la sur **Verbose** pour être sûr que toutes vos instructions de suivi seront ajoutées.

5. Cliquez sur **Enregistrer** en haut du volet pour afficher les journaux.

6. Pour afficher les journaux en continu sur le portail, cliquez sur la vignette **Journaux de diffusion** du volet du site Web. Si des instructions de suivi sont en cours d’écriture sur votre application, vous devez les voir presque en temps réel dans la fenêtre de résultat.

![][StreamingLogsScreenshot]

## Accès à la console ##

La version préliminaire du portail Azure fournit un accès à la console à votre application Web. Vous pouvez explorer le système de fichiers de l’application web et exécuter les scripts powershell/cmd. Lorsque vous exécutez des commandes de la console, vous dépendez des mêmes autorisations que pour votre code d’application web en cours d’exécution. Vous ne pouvez pas accéder aux répertoires protégés ni exécuter des scripts qui demandent des autorisations élevées.

1. Accédez au volet de l’application Web, comme décrit dans la section ci-dessus.

2. Cliquez sur la vignette **Console** pour ouvrir la console.

![][ConsoleScreenshot]

Pour vous familiariser avec la console, commencez par les commandes élémentaires suivantes :

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=July15_HO3-->