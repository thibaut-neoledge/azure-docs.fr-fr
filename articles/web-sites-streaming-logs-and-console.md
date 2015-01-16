<properties pageTitle="Journaux de diffusion en continu et console" description="Vue d'ensemble des journaux et de la console de diffusion en continu" title="Streaming logs and console" authors="adamab" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab" />

#Journaux en continu et console

### Journaux de diffusion en continu ###

La version préliminaire du portail Microsoft Azure intègre une fonction d'affichage des journaux de diffusion en continu qui vous permet de suivre les événements de votre site web en temps réel.  

La configuration de cette fonction se fait en quelques étapes simples :

- Écriture des suivis dans votre code
- Activation du diagnostic d'application à partir de la version préliminaire du portail Azure
- Clic sur la partie des journaux en continu sur le volet du site web

### Écriture des suivis dans votre code ###

L'écriture des suivis dans votre code est simple.  En C#, il suffit d'écrire le code suivant :

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

Dans une application node.js, vous pouvez écrire le code suivant pour atteindre le même résultat :

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Activation et affichage des journaux de diffusion en continu ###

Les diagnostics sont activés par site web.  Dans le [portail](https://portal.azure.com), cliquez sur le bouton **Browse** dans la barre de menus à gauche et cliquez sur **Websites** pour afficher la liste de tous vos sites web.  

![][BrowseSitesScreenshot]

Cliquez sur le nom du site web que vous souhaitez configurer.  Cliquez ensuite sur la partie **DIAGNOSTIC LOGS** et définissez le commutateur **Application Logging (Filesystem)** sur **ON**.  L'option **Level** s'affiche et vous permet de modifier le niveau de gravité des suivis à capturer.  Si vous souhaitez simplement vous familiariser avec la fonctionnalité, définissez-la sur **Commentaires** pour être sûr que toutes vos instructions de suivi seront ajoutées.

Cliquez sur **ENREGISTRER** en haut du panneau pour afficher les journaux.

Pour afficher les journaux de diffusion en continu sur le portail, cliquez sur la partie **JOURNAUX EN CONTINU** du panneau du site web.  Si des instructions de suivi sont en cours d'écriture sur votre site, vous devez les voir presque en temps réel dans la fenêtre de résultat.

![][StreamingLogsScreenshot]

## Console ##

La version préliminaire du portail Azure fournit un accès à la console à votre environnement de site web. Vous pouvez explorer le système de fichiers du site web et exécuter les scripts powershell/cmd.  Lorsque vous exécutez des commandes de la console, vous dépendez des mêmes autorisations que pour votre code de site web en cours d'exécution. Vous ne pouvez pas accéder aux répertoires protégés ni exécuter des scripts qui demandent des autorisations élevées.  

Pour accéder à la console, ouvrez un site web comme décrit dans la section ci-dessus.  Cliquez dans la partie **Console** pour ouvrir la console.

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
