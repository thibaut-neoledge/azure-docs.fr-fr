<properties
   pageTitle="Présentation du cache local d’Azure App Service | Microsoft Azure"
   description="Cet article décrit comment activer et redimensionner le cache local d’Azure App Service, puis comment interroger l’état de cette fonctionnalité."
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# Présentation du cache local d’Azure App Service

Le contenu des applications web Azure est stocké sur Azure Storage et est exposé de manière durable en tant que partage de contenu. Destinée à fonctionner avec de nombreuses applications, cette conception présente les caractéristiques suivantes :

* Le contenu est partagé entre plusieurs instances de machine virtuelle de l’application web.
* Le contenu est durable et peut être modifié en exécutant des applications web.
* Les fichiers journaux et les fichiers de données de diagnostic sont disponibles sous le même dossier de contenu partagé.
* La publication d’un nouveau contenu met directement à jour le dossier de contenu, que vous pouvez consulter tout de suite via le site web SCM et l’application web en cours d’exécution (pour obtenir le contenu le plus récent, certaines technologies, comme ASP.NET, lancent généralement un redémarrage de l’application web quand des modifications de fichier sont effectuées).

Tandis que de nombreuses applications web utilisent une seule ou la totalité de ces fonctionnalités, certaines autres ont uniquement besoin d’un magasin de contenu en lecture seule très performant à partir duquel elles peuvent s’exécuter avec une haute disponibilité. Ces applications peuvent tirer profit d’une instance de machine virtuelle sur un cache local spécifique.

La fonctionnalité de cache local d’Azure App Service fournit une vue de rôle web de votre contenu. Ce contenu est un cache d’écriture avec rejet de votre contenu de stockage qui est créé de façon asynchrone au démarrage du site. Quand le cache est prêt, le site est basculé pour s’exécuter sur le contenu mis en cache. Les applications web qui s’exécutent sur le cache local bénéficient des avantages suivants :

* Elles sont protégées contre les latences qui se produisent quand elles accèdent au contenu sur Azure Storage.
* Elles ne sont pas affectées par les mises à niveau planifiées ou les temps d’arrêt imprévus, ni par d’autres interruptions éventuelles d’Azure Storage sur les serveurs qui fournissent le partage de contenu.
* Elles ne redémarrent pas systématiquement après des modifications du partage de stockage.

## Impact du cache local sur le comportement d’App Service

* Le cache local est une copie des dossiers /site et /siteextensions de l’application web. Il est créé sur l’instance de machine virtuelle locale au démarrage de l’application web. La taille du cache local par application web est limitée à 300 Mo par défaut, mais vous pouvez augmenter cette taille jusqu’à 1 Go.
* Le cache local est en lecture-écriture. Toutefois, les modifications sont ignorées quand l’application web change de machines virtuelles ou est redémarrée. N’utilisez pas le cache local pour des applications qui stockent des données stratégiques dans le magasin de contenu.
* Les applications web peuvent continuer à écrire des fichiers journaux et des données de diagnostic comme elles le font habituellement. Toutefois, les fichiers journaux et les données sont stockés localement sur la machine virtuelle. Ils sont ensuite régulièrement copiés dans le magasin de contenu partagé. Malgré la copie dans le magasin de contenu partagé, les écritures différées risquent d’être perdues en cas d’arrêt soudain d’une instance de machine virtuelle.
* La structure des dossiers LogFiles et Data est modifiée pour les applications web qui utilisent le cache local. Ces dossiers de stockage contiennent désormais des sous-dossiers dont le nom est formé d’un identificateur unique et d’un horodatage. Chaque sous-dossier correspond à une instance de machine virtuelle sur laquelle l’application web est en cours d’exécution ou s’est exécutée.  
* La publication des modifications apportées à l’application web s’effectue dans le magasin de contenu partagé, quel que soit le mécanisme de publication utilisé. Cette conception garantit la durabilité du contenu publié. Pour actualiser le cache local de l’application web, vous devez redémarrer l’application. Si cette étape vous semble de trop, vous pouvez rendre le cycle de vie transparent. Pour plus d’informations, consultez la suite de cet article.
* D:\\Home pointe vers le cache local. D:\\Local continue de pointer sur le stockage spécifique de machine virtuelle temporaire.
* L’affichage de contenu par défaut du site SCM continue à être celui du magasin de contenu partagé.

## Activer le cache local dans App Service

Configurez le cache local à l’aide d’une combinaison de paramètres d’application réservés. Pour configurer ces paramètres d’application, vous pouvez utiliser les méthodes suivantes :

* [Portail Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### Configurer le cache local à l’aide du portail Azure
<a name="Configure-Local-Cache-Portal"></a>

Activez le cache local pour chaque application web en utilisant ce paramètre d’application : `WEBSITE_LOCAL_CACHE_OPTION` = `Always`

![Paramètres d’application du portail Azure : cache local](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### Configurer le cache local à l’aide d’Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## Modifier le paramètre de taille dans le cache local

Par défaut, la taille du cache local est de **300 Mo**. Elle inclut les dossiers /site et /siteextensions qui sont copiés à partir du magasin de contenu, ainsi que tous les dossiers de journaux et de données créés localement. Pour augmenter cette limite, utilisez le paramètre d’application `WEBSITE_LOCAL_CACHE_SIZEINMB`. Vous pouvez augmenter la taille jusqu’à **1 Go** (1 000 Mo) par application web.

## Bonnes pratiques pour utiliser le cache local d’App Service

Nous vous recommandons d’utiliser le cache local conjointement avec la fonctionnalité [Environnements de préproduction](../app-service-web/web-sites-staged-publishing.md).

* Ajoutez le paramètre d’application _associé_ `WEBSITE_LOCAL_CACHE_OPTION` avec la valeur `Always` à votre emplacement de **production**. Si vous utilisez le paramètre d’application `WEBSITE_LOCAL_CACHE_SIZEINMB`, ajoutez-le également comme paramètre associé à votre emplacement de production.
* Créez un emplacement de **préproduction** pour la publication. En règle générale, si vous utilisez le cache local pour l’emplacement de production, vous n’avez pas à définir l’emplacement de préproduction pour utiliser le cache local en vue d’implémenter un cycle de vie build-déploiement-test transparent.
*	Testez votre site par rapport à votre emplacement de préproduction.  
*	Quand vous êtes prêt, lancez une [opération d’échange](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) entre vos emplacements de préproduction et de production.  
*	Les paramètres associés incluent un nom et sont rattachés à un emplacement. Ainsi, quand l’emplacement de préproduction est échangé avec l’emplacement de production, il hérite les paramètres d’application du cache local. L’emplacement de production qui vient d’être échangé s’exécute sur le cache local après quelques minutes. Il est ensuite initialisé dans le cadre de l’initialisation des emplacements après l’échange. Une fois l’échange des emplacements terminé, votre emplacement de production s’exécute sur le cache local.

## Forum Aux Questions (FAQ)

### Comment savoir si mon application web peut bénéficier de la fonctionnalité de cache local ?

Utilisez la fonctionnalité de cache local si votre application web a besoin d’un magasin de contenu fiable et très performant, si elle n’utilise pas le magasin de contenu pour écrire des données stratégiques au moment de l’exécution et si elle a une taille totale inférieure à 1 Go. Vous pouvez obtenir la taille totale de vos dossiers /site et /siteextensions en utilisant l’extension de site Utilisation du disque d’Azure Web Apps.

### Comment savoir si mon site a basculé pour utiliser le cache local ?

Si vous utilisez la fonctionnalité de cache local avec des environnements de préproduction, l’opération d’échange prend fin seulement après l’initialisation du cache local. Pour vérifier si votre site s’exécute sur le cache local, examinez la variable d’environnement de processus de travail `WEBSITE_LOCALCACHE_READY`. Suivez les instructions fournies dans la page de la [variable d’environnement de processus de travail](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) pour accéder à cette variable sur plusieurs instances.

### Je viens de publier de nouvelles modifications, mais mon application web ne semble pas les avoir intégrées. Pourquoi ?

Si votre application web utilise le cache local, vous devez redémarrer votre site pour voir les dernières modifications. Si vous ne voulez pas publier les modifications sur un site de production, consultez les options d’emplacement décrites dans la section sur les bonnes pratiques, plus haut dans cet article.

### Où sont mes journaux ?

Avec le cache local, vos dossiers de données et de journaux se présentent un peu différemment. Toutefois, la structure de vos sous-dossiers reste la même, excepté que les sous-dossiers se trouvent sous un sous-dossier dont le nom est formé d’un identificateur de machine virtuelle unique et d’un horodatage.

### J’ai activé le cache local, mais mon application web redémarre systématiquement. Pourquoi ? Je pensais que le cache local évitait les redémarrages d’application fréquents.

En effet, le cache local contribue à limiter les redémarrages d’application web liés au stockage. Toutefois, des redémarrages de votre application web peuvent toujours être nécessaires pendant les mises à niveau planifiées de l’infrastructure de la machine virtuelle. Quand le cache local est activé, les redémarrages d’application globaux sont normalement moins nombreux.

<!---HONumber=AcomDC_0330_2016-->