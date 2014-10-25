<properties pageTitle="Streaming logs and console" description="Streaming logs and console overview" title="Streaming logs and console" authors="adamab" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab"></tags>

## Journaux en continu et console

### Journaux en continu

La version préliminaire du portail Azure intègre une fonction de diffusion de journaux en continu qui vous permet de suivre les événements de votre site web en temps réel.

La configuration de cette fonction se fait en quelques étapes simples :

-   Écriture des suivis dans votre code
-   Activation du diagnostic d'application à partir de la version préliminaire du portail Azure
-   Clic sur la partie des journaux en continu sur le volet du site web

### Écriture des suivis dans votre code

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

    console.log("My trace statement").

### Activation et affichage des journaux en continu

Les diagnostics sont activés par site web. Dans le [portail][], cliquez sur le bouton **Browse** dans la barre de menus à gauche et cliquez sur **Websites** pour afficher la liste de tous vos sites web.

![][]

Cliquez sur le nom du site web que vous souhaitez configurer. Cliquez ensuite sur la partie **DIAGNOSTIC LOGS** et définissez le commutateur **Application Logging (Filesystem)** sur **ON**. L'option **Level** s'affiche et vous permet de modifier le niveau de gravité des suivis à capturer. Si vous souhaitez simplement vous familiariser avec la fonctionnalité, définissez-la sur **Verbose** pour être sûr que toutes vos instructions de suivi seront ajoutées.

Cliquez sur **SAVE** en haut du volet pour afficher les journaux.

Pour afficher les journaux en continu sur le portail, cliquez sur la partie **STREAMING LOGS** du volet du site web. Si des instructions de suivi sont en cours d'écriture sur votre site, vous devez les voir presque en temps réel dans la fenêtre de résultat.

![][1]

## Console

Le portail Azure Preview fournit un accès à la console à votre environnement de site web. Vous pouvez explorer le système de fichiers du site web et exécuter les scripts powershell/cmd. Lorsque vous exécutez des commandes de la console, vous dépendez des mêmes autorisations que pour votre code de site web en cours d'exécution. Vous ne pouvez pas accéder aux répertoires protégés ni exécuter des scripts qui demandent des autorisations élevées.

Pour accéder à la console, ouvrez un site web comme décrit dans la section ci-dessus. Cliquez sur la partie **Console** pour ouvrir la console.

![][2]

Pour vous familiariser avec la console, commencez par les commandes élémentaires suivantes :

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->

  [portail]: https://portal.azure.com
  []: ./media/web-sites-streaming-logs-and-console/browse-sites.png
  [1]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
  [2]: ./media/web-sites-streaming-logs-and-console/console.png
