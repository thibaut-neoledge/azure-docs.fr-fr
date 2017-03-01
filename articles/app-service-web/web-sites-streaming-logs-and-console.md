---
title: Journaux de diffusion en continu et console
description: Vue d&quot;ensemble des journaux et de la console de diffusion en continu
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: 3e50a287-781b-4c6a-8c53-eec261889d7a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 120ce6b115820728b9f853e9ff349beb0ef29c9d
ms.lasthandoff: 03/01/2017


---
# <a name="streaming-logs-and-the-console"></a>Journaux en continu et console
## <a name="streaming-logs"></a>Journaux en continu
Le **portail Azure** intègre une fonction d’affichage des journaux de diffusion en continu qui vous permet de suivre les événements de vos applications **App Service** en temps réel.  

La configuration de cette fonction se fait en quelques étapes simples :

* Écriture des suivis dans votre code
* Activation des **journaux de diagnostic** pour votre application
* Affichage du flux à partir de l’interface utilisateur **Journaux de streaming** dans le **portail Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Écriture des suivis dans votre code
L'écriture des suivis dans votre code est simple.  Dans C#, il suffit d'écrire le code suivant :

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

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Activation et affichage des journaux en continu
![][BrowseSitesScreenshot] Les diagnostics sont activés par application. Commencez par naviguer jusqu’au site pour lequel vous souhaitez activer cette fonctionnalité.  

![][DiagnosticsLogs] À partir du menu Paramètres, faites défiler jusqu’à la section **Analyse** et cliquez sur **(1) Journaux de diagnostic**. Puis **(2) activez ** **Journal des applications (Filesystem)** ou **Journal des applications (Blob)** L’option **Niveau** vous permet de modifier le niveau de gravité des suivis à capturer. Si vous souhaitez simplement vous familiariser avec la fonctionnalité, définissez-la sur **Verbose** pour être sûr que toutes vos instructions de suivi seront collectées.

Cliquez sur **SAVE** en haut du volet pour afficher les journaux.

> [!NOTE]
> Plus le **niveau de gravité** est élevé, plus les ressources consommées dans le journal sont importantes et plus vous obtiendrez de suivis. Vérifiez que le **niveau de gravité** est configuré sur le niveau de détail approprié pour un site de production ou ayant un fort trafic. 
> 
> 

![][StreamingLogsScreenshot] Pour afficher les **journaux de streaming** au sein du portail Azure, cliquez sur **(1) Flux de journaux** également dans la section **Analyse** du menu Paramètres. Si des instructions de suivi sont en cours d’écriture sur votre application, vous devez les voir presque en temps réel dans les **(2) journaux de streaming**.

## <a name="console"></a>Console
Le **portail Azure** fournit un accès à la console à votre application. Vous pouvez explorer le système de fichiers de l’application et exécuter les scripts powershell/cmd. Lorsque vous exécutez des commandes de la console, vous dépendez des mêmes autorisations que pour votre code d’application en cours d’exécution. Vous ne pouvez pas accéder aux répertoires protégés ni exécuter des scripts qui demandent des autorisations élevées.  

![][ConsoleScreenshot] À partir du menu Paramètres, faites défiler jusqu’à la section **Outils de développement** et cliquez sur **Console (1)**. La **(2) console** s’ouvre alors à droite.

Pour vous familiariser avec la **console**, commencez par les commandes élémentaires suivantes :

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

