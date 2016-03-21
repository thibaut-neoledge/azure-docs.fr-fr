<properties
   pageTitle="Présentation du cache local d’Azure App Service"
   description="Cet article explique comment activer, redimensionner la fonctionnalité de cache local d’Azure App Service et connaître son état"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords="Pour une utilisation par les seuls spécialistes de l’optimisation du référencement d’un site auprès d’un moteur de recherche (SEO). Séparez les termes par des virgules. Consultez votre spécialiste SEO avant de modifier le contenu de cet article où se trouvent ces termes."/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# Présentation du cache local d’Azure App Service

Le contenu des applications web Azure est stocké sur Azure Storage et est exposé de manière durable en tant que partage de contenu. Conçue pour fonctionner avec de nombreuses applications, cette conception présente les caractéristiques suivantes :

* Le contenu est partagé entre plusieurs instances de machine virtuelle de l’application web. 
* Le contenu est durable et peut être modifié en exécutant des applications web. 
* Les fichiers journaux et les fichiers de données de diagnostic sont disponibles sous le même dossier de contenu partagé. 
* La publication d’un nouveau contenu met directement à jour le dossier de contenu ; vous pouvez le constater immédiatement par le biais du site web SCM et de l’application web en cours d’exécution (généralement, pour sélectionner le contenu le plus récent, certaines technologies, comme ASP.NET, lancent un redémarrage de l’application web quand des modifications de fichier sont effectuées). 

Bien que de nombreuses applications web utilisent une seule ou la totalité de ces fonctionnalités, certaines applications web ont uniquement besoin d’un magasin de contenu en lecture seule hautement performant à partir duquel elles peuvent s’exécuter avec une haute disponibilité. Ces applications peuvent bénéficier d’une copie du contenu spécifique d’une instance de machine virtuelle, appelée ici « cache local ». Le _cache local_ fournit une vue de rôle web de votre contenu, et ce contenu est un cache d’écriture avec rejet de votre contenu de stockage qui est créé de façon asynchrone au démarrage du site. Quand le cache est prêt, le site est basculé pour s’exécuter sur le contenu mis en cache. Les applications web qui s’exécutent sur le cache local bénéficient des avantages suivants :

* Elles sont protégées contre les latences liées à l’accès au contenu sur Azure Storage. 
* Elles ne sont pas affectées par les mises à jour planifiées ou temps morts imprévus des serveurs qui fournissent le partage de contenu, ni par toute autre perturbation liée à Azure Storage. 
* Les modifications apportées au partage de stockage entraînent moins de redémarrages d’application. 

## Impact du cache local sur le comportement d’App Service

* Le cache local est une copie des dossiers /site et /siteextensions de l’application web, et est créé sur l’instance de machine virtuelle locale au démarrage de l’application web. La taille du cache local par application web est limitée à 300 Mo par défaut, mais peut être augmentée jusqu’à 1 Go. 
* Le cache local est accessible en lecture-écriture ; cependant, toute modification est ignorée quand l’application web déplace des machines virtuelles ou est redémarrée. Le cache local ne doit pas être utilisé pour des applications qui conservent des données critiques dans le magasin de contenu. 
* Les applications web peuvent continuer à écrire des fichiers journaux et des données de diagnostic comme elles le font actuellement. Toutefois, les fichiers journaux et les données sont stockés localement sur la machine virtuelle, puis sont régulièrement copiés dans le magasin de contenu partagé. Malgré la copie sur le magasin de contenu partagé, les écritures en différé risquent d’être perdues en cas de panne soudaine d’une instance de machine virtuelle. 
* La structure des dossiers LogFiles et Data est modifiée pour les applications web qui utilisent le cache local. Ces dossiers de stockage contiennent désormais des sous-dossiers qui suivent le modèle d’affectation de noms « identificateur » + horodatage. Chacun des sous-dossiers correspond à une instance de machine virtuelle sur laquelle l’application web est en cours d’exécution ou s’est exécutée.  
* La publication des modifications apportées à l’application web, quel que soit le mécanisme de publication utilisé, s’effectue conceptuellement dans le magasin de contenu partagé. Ainsi, le contenu publié est durable. Pour actualiser le cache local de l’application web, vous devez le redémarrer. Si cette étape vous semble excessive, vous verrez plus loin comment rendre le cycle de vie transparent.
* D:\\Home pointe vers le cache local. D:\\Local continue de pointer sur le stockage spécifique de machine virtuelle temporaire. 
* L’affichage de contenu par défaut du site SCM continue à être celui du magasin de contenu partagé. Pour voir à quoi ressemble le dossier de votre cache local, vous pouvez accéder à https://[site-name].scm.azurewebsites.net/VFS/LocalSiteRoot/LocalCache.

## Comment activer le cache local dans Azure App Service

Le cache local est configuré à l’aide d’une combinaison de paramètres d’application réservés. Ces paramètres d’application peuvent être configurés à l’aide des méthodes suivantes :

* [Portail Azure](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### Procédure : configuration du cache local à l’aide du portail Azure
<a name="Configure-Local-Cache-Portal"></a>

Le cache local est activé en fonction des applications web, à l’aide d’un paramètre d’application. `WEBSITE_LOCAL_CACHE_OPTION` = `Always`

![Paramètres d’application du portail Azure : cache local](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### Procédure : configuration du cache local à l’aide d’Azure Resource Manager
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

## Comment modifier la taille du cache local d’App Service ?

Par défaut, la taille du cache local est **300 Mo**. Cela inclut les dossiers Site et SiteExtensions qui sont copiés à partir du magasin de contenu, ainsi que tous les dossiers de journaux et de données créés localement. Pour augmenter cette limite, utilisez le paramètre d’application `WEBSITE_LOCAL_CACHE_SIZEINMB`. La taille peut être augmentée jusqu’à **1 Go** (1 000 Mo) par application web.

## Meilleures pratiques pour utiliser le cache local d’App Service

Nous vous recommandons d’utiliser le cache local conjointement avec la fonctionnalité [Environnements intermédiaires](../app-service-web/web-sites-staged-publishing.md).

* Ajoutez un paramètre d’application _associé_ `WEBSITE_LOCAL_CACHE_OPTION` avec la valeur `Always` à votre **emplacement de production**. Si vous utilisez `WEBSITE_LOCAL_CACHE_SIZEINMB`, ajoutez-le également comme paramètre associé à votre emplacement de production. 
* Créez un emplacement intermédiaire et publiez dans celui-ci. En règle générale, l’emplacement intermédiaire n’utilise pas le cache local pour activer un cycle de vie génération-déploiement-test transparent pour le développement intermédiaire, mais il bénéficie des avantages du cache local pour l’emplacement de production. 
*	Testez votre site par rapport à votre emplacement intermédiaire.  
*	Une fois que vous êtes prêt, lancez une [opération d’échange](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) entre vos emplacements **intermédiaire** et **de production**.  
*	Les paramètres associés sont accessibles par leur nom et sont rattachés à un emplacement. Ainsi, quand l’emplacement intermédiaire gagne l’environnement de production, il hérite les paramètres d’application du cache local. L’emplacement de production qui vient d’être échangé s’exécute sur le cache local après quelques minutes et est initialisé dans le cadre de l’initialisation des emplacements après l’échange. Ainsi, une fois terminé l’échange d’emplacements, votre emplacement de production s’exécute sur le cache local.

## Forum Aux Questions (FAQ)

### Comment savoir si mon application web peut bénéficier du cache local ? 

Si votre application web a besoin d’un magasin de contenu fiable et hautement performant, qu’elle n’utilise pas le magasin de contenu pour écrire des données critiques au moment de l’exécution et que sa taille totale est inférieure à 1 Go, la réponse est Oui ! Pour obtenir la taille totale de vos dossiers « site » et « siteextensions », vous pouvez recourir à l’utilitaire d’extension de site Azure Web Apps Disk Usage.
 
### Comment activer le cache local ? 

Consultez plus haut la section sur les meilleures pratiques relatives à l’utilisation du cache local.
 
### Comment puis-je savoir si mon site a basculé pour utiliser le cache local ? 

Si vous utilisez la fonctionnalité de cache local avec des environnements intermédiaires, l’opération d’échange ne prend fin qu’une fois le cache local initialisé. Pour vérifier si votre site s’exécute sur le cache local, vous pouvez consulter la variable d’environnement de processus de travail `WEBSITE_LOCALCACHE_READY`. Utilisez les instructions indiquées pour accéder à la variable d’environnement de processus de travail sur plusieurs instances.
 
### Je viens de publier de nouvelles modifications, mais mon application web ne semble pas les avoir intégrées. Pourquoi ? 
Si votre application web utilise un cache local, vous devez redémarrer votre site pour qu’il obtienne les dernières modifications. Vous ne voulez pas effectuer cette procédure sur un site de production ? Consultez les options d’emplacement plus haut.
 
### Où sont mes journaux ? 

Avec le cache local, l’aspect de vos dossiers de données et de journaux diffère en effet quelque peu. Toutefois, la structure de vos sous-dossiers demeure la même, la différence étant qu’ils se trouvent sous un sous-dossier dont le format est « identificateur de machine virtuelle unique » + horodatage.
 
### J’ai activé le cache local, mais mon application web est systématiquement redémarrée. Pourquoi ? Je pensais que le cache local évitait les redémarrages d’application fréquents. 

Le cache local peut en effet aider à empêcher les redémarrages d’application web liés au stockage. Toutefois, votre application web peut toujours subir un redémarrage pendant les mises à niveau planifiées de l’infrastructure de la machine virtuelle. Les redémarrages globaux de l’application alors que le cache local est activé sont normalement moins fréquents.

<!---HONumber=AcomDC_0309_2016-->