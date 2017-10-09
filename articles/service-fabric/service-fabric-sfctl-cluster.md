---
title: Interface de ligne de commande CLI Azure Service Fabric - sfctl cluster | Microsoft Docs
description: "Décrit les commandes sfctl cluster de l’interface CLI Azure Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: dc2ed59d6adaca97b23dddcb7ec968d90171b483
ms.contentlocale: fr-fr
ms.lasthandoff: 09/26/2017

---
# <a name="sfctl-cluster"></a>sfctl cluster
Permet de sélectionner, de gérer et d’utiliser les clusters Service Fabric.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
|    code-versions| Obtient une liste des versions de code fabric approvisionnées dans un cluster Service Fabric.|
|    config-versions | Obtient une liste des versions de configuration fabric approvisionnées dans un cluster Service Fabric.|
|    health       | Obtient l’intégrité d’un cluster Service Fabric.|
|    manifest     | Obtient le manifeste du cluster Service Fabric.|
|    operation-cancel| Annule une opération d’erreur induite par l’utilisateur.|
|    operationgit | Obtient une liste des opérations d’erreurs induites par l’utilisateur filtrées par l’entrée fournie.|
|    provision     | Approvisionner le code ou les packages de configuration d’un cluster Service Fabric.|
|    recover-system  | Indique au cluster Service Fabric qu’il doit tenter de récupérer les services système qui sont actuellement bloqués dans la perte de quorum.|
|report-health   | Envoie un rapport d’intégrité sur le cluster Service Fabric.|
|    select       | Se connecte à un point de terminaison du cluster Service Fabric.|
| unprovision     | Annule l’approvisionnement du code ou des packages de configuration d’un cluster Service Fabric.|
|    mettre à niveau         | Commence la mise à niveau du code ou de la version de configuration d’un cluster Service Fabric.|
|    upgrade-resume  | Effectue le déplacement de la mise à niveau de cluster vers le domaine de mise à niveau suivant.|
|    upgrade-rollback| Restaure la mise à niveau d’un cluster Service Fabric.|
|    upgrade-status  | Obtient la progression de la mise à niveau de cluster actuelle.|
|upgrade-update  | Met à jour les paramètres de mise à niveau d’une mise à niveau du cluster Service Fabric.|


## <a name="sfctl-cluster-health"></a>sfctl cluster health
Obtient l’intégrité d’un cluster Service Fabric.

Obtient l’intégrité d’un cluster Service Fabric. EventsHealthStateFilter permet de filtrer la collecte d’événements de contrôle d’intégrité signalés sur le cluster en fonction de l’état d’intégrité. De même, utilisez NodesHealthStateFilter et ApplicationsHealthStateFilter pour filtrer la collection de nœuds et d’applications renvoyées en fonction de leur état d’intégrité agrégé. 

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --applications-health-state-filter| Permet le filtrage de l’état d’intégrité de l’application
                                                    objects returned in the result of cluster health
                                                    query based on their health state. The possible
                                                    values for this parameter include integer value
                                                    obtained from members or bitwise operations on
                                                    members of HealthStateFilter enumeration. Only
                                                    applications that match the filter are returned.
                                                    All applications are used to evaluate the
                                                    aggregated health state. If not specified, all
                                                    entries are returned. The state values are flag
                                                    based enumeration, so the value could be a
                                                    combination of these values obtained using
                                                    bitwise 'OR' operator. For example, if the
                                                    provided value is 6 then health state of
                                                    applications with HealthState value of OK (2)
                                                    and Warning (4) are returned. - Default -
                                                    Default value. Matches any HealthState. The
                                                    value is zero. - None - Filter that doesn't
                                                    match any HealthState value. Used in order to
                                                    return no results on a given collection of
                                                    states. The value is 1. - Ok - Filter that
                                                    matches input with HealthState value Ok. The
                                                    value is 2. - Warning - Filter that matches
                                                    input with HealthState value Warning. The value
                                                    is 4. - Error - Filter that matches input with
                                                    HealthState value Error. The value is 8. - All -
                                                    Filter that matches input with any HealthState value. The value is 65535.|
| --events-health-state-filter   | Permet de filtrer la collection d’objets HealthEvent retournés en fonction de l’état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les événements qui correspondent au filtre sont renvoyés. Tous les événements sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState.
La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant n’importe quelle valeur HealthState. La valeur est 65535. | |--exclude-health-statistics                   | Indique si les statistiques d’intégrité doivent être renvoyées en tant que partie du résultat de la requête. False par défaut. Les statistiques indiquent le nombre d’entités enfants dans l’état d’intégrité Ok, Warning et Error. | |   --include-system-application-health-statistics| Indique si les statistiques d’intégrité doivent inclure les statistiques d’intégrité de l’application fabric:/System. False par défaut. Si IncludeSystemApplicationHealthStatistics est défini sur true, les statistiques d’intégrité des statistiques incluent les entités qui appartiennent à l’application fabric:/System. Dans le cas contraire, le résultat de la requête inclut uniquement les statistiques d’intégrité pour les applications utilisateur. Les statistiques d’intégrité doivent être incluses dans le résultat de la requête pour ce paramètre à appliquer. | | --nodes-health-state-filter    | Permet de filtrer les objets d’état d’intégrité du nœud renvoyés dans le résultat de la requête d’intégrité du cluster en fonction de leur état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les nœuds qui correspondent au filtre sont renvoyés. Tous les nœuds sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, l’état d’intégrité des nœuds dont la valeur HealthState est OK (2) et Warning (4) est renvoyé. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState.
La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant n’importe quelle valeur HealthState. La valeur est 65535. | | --timeout -t                   | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                        | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                      | Affiche ce message d’aide et quitte.|
| --output -o                    | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                    Valeur par défaut : json.|
| --query                        | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                      | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
Obtient le manifeste du cluster Service Fabric.

Obtient le manifeste du cluster Service Fabric. Le manifeste du cluster contient les propriétés du cluster qui incluent des types de nœuds différents sur le cluster, les configurations de sécurité, l’erreur et les topologies des domaines de mise à niveau etc. Ces propriétés sont spécifiées dans le fichier ClusterConfig.JSON lors du déploiement d’un cluster autonome. Toutefois, la plupart des informations du manifeste du cluster sont générées en interne par service fabric durant le déploiement de cluster dans d’autres scénarios de déploiement (par exemple, lorsque vous utilisez le [portail Azure](https://portal.azure.com)). Le contenu du manifeste de cluster est à titre d’information uniquement et les utilisateurs ne sont pas supposés dépendre du format du contenu du fichier ou de son interprétation. 

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --timeout -t| Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug  | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h| Affiche ce message d’aide et quitte.|
| --output -o | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query  | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose| Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Approvisionner le code ou les packages de configuration d’un cluster Service Fabric.

        Validate and provision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
|--cluster-manifest-file-path| Chemin d’accès au fichier du manifeste de cluster.|
|    --code-file-path            | Chemin d’accès au fichier du package de code de cluster.|
|    --timeout -t                | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h  | Affiche ce message d’aide et quitte.|
| --output -o| Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose  | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Se connecte à un point de terminaison du cluster Service Fabric.

Si vous vous connectez au cluster sécurisé, vous devez spécifier un fichier de certificat (.crt) et de clé (.key) ou un seul fichier comportant les deux (.pem). Ne spécifiez pas les deux. Si vous le souhaitez, lorsque vous vous connectez à un cluster sécurisé, spécifiez également un chemin d’accès à un fichier de groupement d’autorités de certification ou à un répertoire de certificats d’autorités de certification approuvés.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --endpoint [Requis]| URL de point de terminaison du cluster, y compris le port et le préfixe HTTP ou HTTPS.|
| --aad             | Utilisez Azure Active Directory pour l’authentification.|
| --ca              | Chemin d’accès au répertoire de certificats d’autorités de certification à traiter comme étant valides ou au fichier de groupement d’autorités de certification.|
| --cert            | Chemin d’accès à un fichier de certificat client.|
| --key             | Chemin d’accès à un fichier de clé de certificat client.|
| --no-verify       | Désactivez la vérification des certificats lors de l’utilisation de HTTPS. Remarque : il s’agit d’une option non sécurisée à ne pas utiliser pour les environnements de production.|
| --pem             | Chemin d’accès au certificat du client, sous forme de fichier .pem.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug           | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h         | Affiche ce message d’aide et quitte.|
| --output -o       | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query           | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose         | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Annule l’approvisionnement du code ou des packages de configuration d’un cluster Service Fabric.

        Unprovision the code or configuration packages of a Service Fabric cluster.

### <a name="arguments"></a>Arguments
|Argument|Description|
| --- | --- |
|--code-version  | Version du package de code de cluster.|
|    --config-version| Version du manifeste de cluster.|
|    --timeout -t    | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux
|Argument|Description|
| --- | --- |
|--debug         | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
 |   --help -h       | Affiche ce message d’aide et quitte.|
 |   --output -o     | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
 |   --query         | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/
                      .|
 |   --verbose       | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|


## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Commence la mise à niveau du code ou de la version de configuration d’un cluster Service Fabric.

        Validate the supplied upgrade parameters and start upgrading the code or configuration
        version of a Service Fabric cluster if the parameters are valid.

### <a name="arguments"></a>Arguments
|Argument|Description|
| --- | --- |
|    --app-health-map                      | Dictionnaire encodé au format JSON de paires de noms d’applications et
                                            pourcentage maximal de défaillance sur le plan de l’intégrité avant de déclencher une erreur.|
 |   --app-type-health-map                 | Dictionnaire encodé au format JSON de paires de types d’applications
                                            name and maximum percentage unhealthy before raising
                                            error.|
 |   --code-version                        | Version de code de cluster. | |   --config-version                      | Version de configuration de cluster. | |   --delta-health-evaluation             | Active l’évaluation d’intégrité delta plutôt que l’évaluation d’intégrité absolue après l’achèvement de chaque domaine de mise à niveau. | |   --delta-unhealthy-nodes               | Pourcentage maximal autorisé de dégradation de l’intégrité des nœuds autorisée au cours d’une mise à niveau de cluster.  Par défaut : 10.
Le delta est mesuré entre l’état des nœuds au début de la mise à niveau et l’état des nœuds au moment de l’évaluation de l’intégrité. La vérification est effectuée suite à l’achèvement de chaque mise à niveau du domaine de mise à niveau pour s’assurer que l’état global du cluster est compris dans les limites tolérées.| |   --failure-action                      | Les valeurs possibles sont les suivantes :’Invalid’,’Rollback’,’Manual’.| |   --force-restart                       | Redémarrage forcé.| |   --health-check-retry                  | Délai d’expiration de la nouvelle tentative de contrôle d’intégrité en millisecondes.| |   --health-check-stable                 | Durée stable du contrôle d’intégrité mesuré en millisecondes.| |  --health-check-wait                   | Durée d’attente du contrôle d’intégrité mesurée en millisecondes.| |  --replica-set-check-timeout           | Délai d’expiration du contrôle de l’ensemble de réplica de mise à niveau mesuré en millisecondes.| |   --rolling-upgrade-mode                | Les valeurs possibles sont les suivantes :’Invalid’,’UnmonitoredAuto’,’UnmonitoredManual’,’Monitored’.  Valeur par défaut : UnmonitoredAuto.| |  --timeout -t                          | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.| |  --unhealthy-applications              | Le pourcentage maximal autorisé d’applications pouvant être défectueuses avant signalement d’une erreur.
Par exemple, pour autoriser 10 % des applications pouvant être défectueuses, cette valeur serait de 10. Il s’agit du pourcentage maximum toléré d’applications pouvant être défectueuses avant que l’intégrité du cluster ne soit considérée comme étant à l’état Error. Si le pourcentage est respecté mais qu’il existe au moins une application pouvant être défectueuse, l’état d’intégrité est Warning. Ce calcul est effectué en divisant le nombre d’applications pouvant être défectueuses par le nombre total d’instances de l’application dans le cluster, à l’exception des types d’applications inclus dans le ApplicationTypeHealthPolicyMap. Le calcul arrondit le nombre obtenu de manière à tolérer une défaillance sur un petit nombre d’applications. | |   --unhealthy-nodes                     | Pourcentage maximal autorisé de nœuds pouvant être défectueux avant signalement d’une erreur.
Par exemple, pour autoriser 10 % de nœuds défectueux, cette valeur serait de 10. Il s’agit du pourcentage maximum toléré de nœuds pouvant être défectueux avant que l’intégrité du cluster ne soit considérée comme étant à l’état Error. Si le pourcentage est respecté mais qu’il existe au moins un nœud pouvant être défectueux, l’état d’intégrité est Warning. Le pourcentage est calculé en divisant le nombre de nœuds défectueux par le nombre total de nœuds du cluster. Le calcul est arrondi pour tolérer une défaillance sur un petit nombre de nœuds. Dans les clusters de grande taille, certains nœuds sont toujours défectueux ou en réparation, si bien que ce pourcentage doit être configuré pour tolérer cela. | |   --upgrade-domain-delta-unhealthy-nodes| Pourcentage maximal autorisé de dégradation d’intégrité de nœuds de domaine de mise à niveau autorisé pendant une mise à niveau de cluster.
Valeur par défaut : 15.
Le delta est mesuré entre l’état des nœuds de domaine de mise à niveau au début de la mise à niveau et leur état au moment de l’évaluation de l’intégrité. La vérification est effectuée suite à l’achèvement de chaque mise à niveau de domaine de mise à niveau pour tous les domaines de mise à niveau réalisés pour s’assurer que l’état des domaines de mise à niveau est compris dans les limites tolérées.| |   --upgrade-domain-timeout              | Délai d’expiration de domaine de mise à niveau mesuré en millisecondes.| |   --upgrade-timeout                     | Délai d’expiration de mise à niveau mesuré en millisecondes.| |   --warning-as-error                    | Les avertissements sont traités avec le même niveau de gravité que les erreurs.|

### <a name="global-arguments"></a>Arguments globaux
    |Argument|Description|
| --- | --- |
|--debug                               | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
|    --help -h                             | Affiche ce message d’aide et quitte.|
|    --output -o                           | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.
                                            Valeur par défaut : json.|
|    --query                               | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples
                                            , consultez le site à l’adresse http://jmespath.org/.|
|    --verbose                             | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage
                                            complets.|

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
