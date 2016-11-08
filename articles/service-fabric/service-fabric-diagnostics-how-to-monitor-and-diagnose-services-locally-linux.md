---
title: Analyser et diagnostiquer localement les services écrits avec Azure Service Fabric | Microsoft Docs
description: Découvrez comment analyser et diagnostiquer vos services écrits à l’aide de Microsoft Azure Service Fabric sur un ordinateur de développement local.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2016
ms.author: subramar

---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Surveillance et diagnostic des services dans une configuration de développement d’ordinateur local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

L’analyse, la détection, le diagnostic et la résolution des problèmes permettent aux services de fonctionner avec une interruption minimale de l’expérience utilisateur. L’analyse et le diagnostic sont essentiels dans un environnement de production réel déployé. L’adoption d’un modèle similaire pendant le développement de services garantit le fonctionnement du pipeline de diagnostic lors du passage à un environnement de production. Service Fabric facilite pour les développeurs de service l’implémentation de diagnostics qui peuvent fonctionner parfaitement aussi bien sur une configuration de développement d’ordinateur local unique que sur une configuration réelle de cluster de production.

## <a name="debugging-service-fabric-java-applications"></a>Débogage des applications Java Service Fabric
Pour les applications Java, [plusieurs frameworks de journalisation](http://en.wikipedia.org/wiki/Java_logging_framework) sont disponibles. Comme `java.util.logging` est l’option par défaut avec l’environnement JRE, elle est également utilisée pour les [exemples de code dans GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  La suite de cette section explique comment configurer le framework `java.util.logging` . 

À l’aide de java.util.logging, vous pouvez rediriger vos journaux d’application vers la mémoire, des flux de sortie, des fichiers de console ou des sockets. Pour chacune de ces options, des gestionnaires par défaut sont déjà fournis dans le framework. Vous pouvez créer un fichier `app.properties` pour configurer le gestionnaire de fichiers de votre application de sorte qu’il redirige tous les journaux dans un fichier local. 

L’extrait de code suivant contient un exemple de configuration : 

```java 
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Le dossier vers lequel pointe le fichier `app.properties` doit exister. Une fois le fichier `app.properties` créé, vous devez également modifier votre script de point d’entrée, `entrypoint.sh`, dans le dossier `<applicationfolder>/<servicePkg>/Code/` afin de définir la propriété `java.util.logging.config.file` sur le fichier `app.propertes`. L’entrée doit se présenter comme l’extrait de code suivant :

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Cette configuration se traduit par la collecte des journaux suivant une rotation dans `/tmp/servicefabric/logs/`. **%u** et **%g** permettent de créer des fichiers supplémentaires avec les noms de fichier mysfapp0.log, mysfapp1.log, etc. Si aucun gestionnaire n’est configuré explicitement, le gestionnaire de la console est inscrit. Les journaux sont accessible sous /var/log/syslog.

Pour plus d’informations, consultez les [exemples de code dans GitHub](http://github.com/Azure-Samples/service-fabric-java-getting-started).  

## <a name="next-steps"></a>Étapes suivantes
Le code de suivi ajouté à votre application fonctionne également pour le diagnostic de votre application sur un cluster Microsoft Azure. Consultez ces articles qui traitent des différentes options pour les outils et décrivent comment vous pouvez les configurer.

* [Collecte des journaux avec Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)

<!--HONumber=Oct16_HO2-->


