---
title: "Utilisation de la configuration PM2 pour Node.js dans l’application web Azure sur Linux | Microsoft Docs"
description: "Utilisation de la configuration PM2 pour Node.js dans l’application web Azure sur Linux"
keywords: azure app service, application web, nodejs, pm2, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7ad48d42f8cc847ece199a2372c20430c4c8424e
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="use-pm2-configuration-for-nodejs-in-azure-web-app-on-linux"></a>Utiliser la configuration PM2 pour Node.js dans l’application web Azure sur Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


Si vous définissez la pile d’applications sur Node.js pour l’application web Azure sur Linux, vous avez la possibilité de définir un fichier de démarrage Node.js, comme indiqué dans l’image suivante :

![Définir un fichier de démarrage Node.js][1]

Vous pouvez utiliser cette option pour effectuer l’une des tâches suivantes :

* Spécifier le script de démarrage pour votre application Node.js (par exemple : /bin/server.js) ;
* Spécifier le fichier de configuration PM2 à utiliser pour votre application Node.js (par exemple : /foo/process.json).
  
  > [!NOTE]
  > Si vous souhaitez que vos processus Node.js redémarrent automatiquement lorsque certains fichiers sont modifiés, utilisez la configuration PM2. Sinon, votre application ne redémarrera pas lorsqu’elle recevra des notifications de modification (par exemple, en cas de modification du code de votre application).
  > 
  > 

Vous pouvez consulter la [documentation sur les fichiers de traitement](http://pm2.keymetrics.io/docs/usage/application-declaration/) pour connaître toutes les options, mais vous trouverez ci-dessous un exemple de ce que vous pouvez utiliser comme fichier process.json :

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Les éléments importants à prendre en compte dans cette configuration sont les suivants :

* La propriété « script » spécifie le script de démarrage de votre application.
* La propriété « instances » spécifie le nombre d’instances du processus de nœud à lancer. Si vous exécutez votre application sur des machines virtuelles plus importantes qui ont plusieurs cœurs, il est judicieux d’optimiser vos ressources en définissant une valeur plus élevée ici.
* Le groupe « watch » spécifie tous les fichiers pour lesquels vous souhaitez redémarrer le processus de nœud en cas de modification.
* Pour « watch_options », vous devez actuellement attribuer la valeur true à « usePolling » en raison de la façon dont le contenu de votre application est monté.

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce que l’application web Azure sur Linux ?](app-service-linux-intro.md)
* [FAQ Application web Azure App Service sur Linux](app-service-linux-faq.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png

