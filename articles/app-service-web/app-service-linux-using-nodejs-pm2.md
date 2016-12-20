---
title: Utilisation de la configuration PM2 pour Node.js dans les applications Web sur Linux | Microsoft Docs
description: Utilisation de la configuration PM2 pour Node.js dans les applications Web sur Linux
keywords: azure app service, application web, nodejs, pm2, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a46f52198e956406c2384c45ed4323aff27259a0


---
# <a name="use-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Utiliser la configuration PM2 pour Node.js dans les applications Web sur Linux
Si vous définissez la pile d’applications sur Node.js pour les applications web sous Linux, vous avez la possibilité de définir un fichier de démarrage Node.js, comme indiqué dans l’image suivante.

![Définir un fichier de démarrage Node.js][1]

Vous pouvez utiliser cette action pour effectuer l’une des tâches suivantes :

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
* [Présentation d’App Service sur Linux](app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png



<!--HONumber=Nov16_HO3-->


