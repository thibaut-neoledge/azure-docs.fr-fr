---
title: "Interface de ligne de commande CLI Azure Service Fabric : sfctl application | Microsoft Docs"
description: "Décrit les commandes sfctl application de l’interface CLI Azure Service Fabric."
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
ms.openlocfilehash: dc57c813a6aecabc21ac3931b7294bce909778d6
ms.contentlocale: fr-fr
ms.lasthandoff: 09/26/2017

---

# <a name="sfctl-application"></a>sfctl application
Permet de créer, de supprimer et de gérer les applications et les types d’application.

## <a name="commands"></a>Commandes

|Commande|Description|
| --- | --- |
| create       | Permet de créer une application Service Fabric à l’aide de la description spécifiée.|
| delete       | Supprime une application Service Fabric existante.|
| deployed     | Permet d’obtenir les informations relatives à une application déployée sur un nœud Service Fabric.|
| deployed-health | Permet d’obtenir les informations relatives à l’intégrité d’une application déployée sur un nœud Service
                      Fabric.|
| deployed-list| Permet d’obtenir la liste des applications déployées sur un nœud Service Fabric.|
| health       | Permet d’obtenir l’intégrité de l’application Service Fabric.|
| info         | Permet d’obtenir des informations sur une application Service Fabric.|
| list         | Permet d’obtenir la liste des applications créées dans le cluster Service Fabric qui correspondent aux
                      filtres spécifiés comme paramètre.|
| load | Permet d’obtenir les informations relatives à une application Service Fabric. |
| manifest     | Permet d’obtenir le manifeste qui décrit un type d’application.|
| provision    | Permet d’annuler l’inscription d’un type d’application Service Fabric avec le cluster.|
| report-health| Permet d’envoyer un rapport d’intégrité sur l’application Service Fabric.|
| type         | Permet d’obtenir la liste des types d’applications du cluster Service Fabric qui correspondent
                      exactement au nom spécifié.|
| type-list    | Permet d’obtenir la liste des types d’applications du cluster Service Fabric.|
| unprovision  | Permet de supprimer ou d’annuler l’inscription d’un type d’application Service Fabric dans le cluster.|
| mettre à niveau      | Commence la mise à niveau d’une application dans le cluster Service Fabric.|
| upgrade-resume  | Commence la mise à niveau d’une application dans le cluster Service Fabric.|
| upgrade-rollback| Démarre la restauration de la mise à niveau en cours d’une application dans le
                      cluster Service Fabric.|
| upgrade-status  | Permet d’obtenir les détails de la dernière mise à jour effectuée sur cette application.|
| upload       | Copie un package d’application Service Fabric dans le magasin d’images.|

## <a name="sfctl-application-create"></a>sfctl application create
Permet de créer une application Service Fabric à l’aide de la description spécifiée.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-name    [Requis]| Nom de l’application, y compris le schéma d’URI « fabric: ».|
| --app-type    [Requis]| Nom du type d’application trouvé dans le manifeste d’application.|
| --app-version [Requis]| Version des types d’applications comme défini dans le manifeste d’application.|
| --max-node-count     | Nombre maximal de nœuds pour lesquels Service Fabric réserve de la capacité pour cette application. Cela ne signifie pas que les services de cette application sont placés sur tous les nœuds.|
| --metrics            | Liste encodée au format JSON des descriptions des mesures de capacité de l’application. Une mesure est définie comme un nom, associé à un ensemble de capacités pour chaque nœud sur lequel l’application existe.|
| --min-node-count     | Nombre minimal de nœuds pour lesquels Service Fabric réserve de la capacité pour cette application. Cela ne signifie pas que les services de cette application sont placés sur tous les nœuds.|
| --parameters         | Liste encodée au format JSON des remplacements de paramètres de l’application à appliquer lors de la création de l’application.|
| --timeout -t         | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug              | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h            | Affiche ce message d’aide et quitte.|
| --output -o          | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :     json.|
| --query              | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose            | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-delete"></a>sfctl application delete
Supprime une application Service Fabric existante.

Supprime une application Service Fabric existante. Une application doit être créée avant de pouvoir être supprimée. En supprimant une application, vous supprimez également tous les services qui font partie de l’application. Par défaut, Service Fabric essaie de fermer les réplicas de service sans perte de données, puis supprime le service. Toutefois, si le service rencontre des problèmes de fermeture normale des réplicas, l’opération de suppression peut prendre un certain temps ou bloquer. Utilisez l’indicateur ForceRemove facultatif pour ignorer la séquence de fermeture normale et forcer la suppression de l’application et de tous ses services.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --force-remove          | Supprime de force une application ou un service Service Fabric sans passer par la séquence d’arrêt normale. Ce paramètre permet de forcer la suppression d’une application ou d’un service pour lesquels le délai de suppression est arrivé à expiration en raison de problèmes intervenant dans le code de service qui empêchent la fermeture normale des réplicas.| | --timeout -t            | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :        json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Permet d’obtenir les informations relatives à une application déployée sur un nœud Service Fabric.|
|     
### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --node-name      [Requis]| Nom du nœud.| | --timeout -t            | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h               | Affiche ce message d’aide et quitte.|
| --output -o             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :        json.|
| --query                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-health"></a>sfctl application health
Permet d’obtenir l’intégrité de l’application Service Fabric.

Permet d’obtenir l’état d’intégrité de l’application Service Fabric. La réponse indique l’état d’intégrité Ok, Error ou Warning. Si l’entité est introuvable dans le magasin d’intégrité, il renvoie des erreurs.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id                 [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». À partir de la version 6.0,
                                                 hierarchical names are delimited with the "~"
                                                 character. For example, if the application name is
                                                 "fabric://myapp/app1", the application identity
                                                 would be "myapp~app1" in 6.0+ and "myapp/app1" in
                                                 previous versions.|
| --deployed-applications-health-state-filter| Permet de filtrer les objets d’état d’intégrité des applications déployées renvoyés dans le résultat de la requête d’intégrité de l’application en fonction de leur état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seules les applications déployées qui correspondent au filtre sont renvoyées. Toutes les applications déployées sont utilisées pour évaluer l’état d’intégrité agrégé . Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit.                        Par exemple, si la valeur indiquée est 6, l’état d’intégrité des applications déployées dont la valeur HealthState est OK (2) et Warning (4) est renvoyé. -                        Default - Default value. Correspond à toute valeur HealthState.                        La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états.                        La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est 65535.| | --events-health-state-filter            | Permet de filtrer la collection d’objets HealthEvent renvoyés en fonction de l’état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les événements qui correspondent au filtre sont renvoyés. Tous les événements sont utilisés pour évaluer l’état d’intégrité agrégé. Si cet argument n’est pas spécifié, toutes les entrées sont retournées.                        Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, tous les événements dont la valeur HealthState est OK (2) et Warning (4) sont retournés. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro. - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est 65535. | |--exclude-health-statistics                   | Indique si les statistiques d’intégrité doivent être renvoyées en tant que partie du résultat de la requête. False par défaut. Les statistiques indiquent le nombre d’entités enfants dans l’état d’intégrité Ok, Warning et Error.| | --services-health-state-filter          | Permet de filtrer les objets d’état d’intégrité services renvoyés dans le résultat de la requête d’intégrité des services en fonction de leur état d’intégrité. Les valeurs possibles de ce paramètre incluent la valeur entière de l’un des états d’intégrité suivants. Seuls les services qui correspondent au filtre sont renvoyés. Tous les services sont utilisés pour évaluer l’état d’intégrité agrégé.                        Si cet argument n’est pas spécifié, toutes les entrées sont retournées. Les valeurs d’état sont une énumération basée sur des indicateurs. La valeur peut donc être une combinaison de ces valeurs obtenue à l’aide de l’opérateur « OR » au niveau du bit. Par exemple, si la valeur indiquée est 6, l’état d’intégrité des services dont la valeur HealthState est OK (2) et Warning (4) est renvoyé. - Default : valeur par défaut. Correspond à toute valeur HealthState. La valeur est égale à zéro.                        - None : filtre qui ne correspond à aucune valeur HealthState. Permet de ne retourner aucun résultat sur une collection donnée d’états. La valeur est égale à 1. - OK : filtre qui correspond à l’entrée ayant OK comme valeur HealthState. La valeur est égale à 2. - Warning : filtre qui correspond à l’entrée ayant Warning comme valeur HealthState. La valeur est égale à 4. - Error : filtre qui correspond à l’entrée ayant Error comme valeur HealthState. La valeur est égale à 8. - All : filtre qui correspond à l’entrée ayant toute valeur HealthState. La valeur est 65535. | | --timeout -t                   | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                                 | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                               | Affiche ce message d’aide et quitte.|
| --output -o                             | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query                                 | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                               | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-info"></a>sfctl application info
Permet d’obtenir des informations sur une application Service Fabric.

Renvoie les informations sur l’application qui a été créée ou en cours de création dans le cluster Service Fabric et dont le nom correspond à celui spécifié comme paramètre. La réponse comprend le nom, le type, l’état, les paramètres et d’autres détails sur l’application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-id      [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés
                                      with the "~" character. For example, if the application name
                                      is "fabric://myapp/app1", the application identity would be
                                      "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --exclude-application-parameters| Indicateur qui spécifie si les paramètres de l’application sont exclus du résultat. | | --timeout -t                 | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                      | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                    | Affiche ce message d’aide et quitte.|
| --output -o                  | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.             Valeur par défaut : json.|
| --query                      | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                    | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-list"></a>sfctl application list
Permet d’obtenir la liste des applications créées dans le cluster Service Fabric qui correspondent aux filtres spécifiés comme paramètre.

Renvoie les informations sur les applications qui ont été créées ou sont en cours de création dans le cluster Service Fabric et qui correspondent aux filtres spécifiés comme paramètre. La réponse comprend le nom, le type, l’état, les paramètres et d’autres détails sur l’application. Si les applications ne tiennent pas dans une page, une page de résultats est renvoyée, ainsi que d’un jeton de liaison, qui peut être utilisé pour obtenir la page suivante.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
|--application-definition-kind-filter| Permet de filtrer sur ApplicationDefinitionKind pour
                                          application query operations. - Default - Default value.
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 0. - All -
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 65535. -
                                          ServiceFabricApplicationDescription - Filter that matches
                                          input with ApplicationDefinitionKind value
                                          ServiceFabricApplicationDescription. The value is 1. -
                                          Compose - Filter that matches input with
                                          ApplicationDefinitionKind value Compose. The value is 2.
                                          Default: 65535.|
| --application-type-name      | Nom du type d’application utilisé pour filtrer les applications à interroger. Cette valeur ne doit pas contenir la version de type application. | | --continuation-token         | Le paramètre de jeton de liaison est utilisé pour obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être encodée au niveau de l’URL.| --exclude-application-parameters| Indicateur qui spécifie si les paramètres de l’application sont exclus du résultat. | | --timeout -t                 | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                      | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                    | Affiche ce message d’aide et quitte.|
| --output -o                  | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.             Valeur par défaut : json.|
| --query                      | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                    | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-load"></a>sfctl application load
Permet d’obtenir les informations relatives à une application Service Fabric.

        Returns the load information about the application that was created or in the process of
        being created in the Service Fabric cluster and whose name matches the one specified as the
        parameter. The response includes the name, minimum nodes, maximum nodes, the number of nodes
        the app is occupying currently, and application load metric information about the
        application.

### <a name="arguments"></a>Arguments
|Argument|Description|
| --- | --- |
|--application-id [Requis]| Identité de l’application. Il s’agit généralement du nom complet de
                                 the application without the 'fabric:' URI scheme. Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions. |
| --timeout -t                   | Délai d’expiration du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux
|Argument|Description|
| --- | --- |
|--debug                    | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
    --help -h                  | Affiche ce message d’aide et quitte.|
    --output -o                | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut :
                                 json.|
    --query                    | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples
                                 , consultez le site à l’adresse http://jmespath.org/.|
    --verbose                  | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Permet d’obtenir le manifeste qui décrit un type d’application.
        
Permet d’obtenir le manifeste qui décrit un type d’application. La réponse contient le code XML du manifeste de l’application sous forme de chaîne.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-name    [Requis]| Nom du type d’application.|
| --application-type-version [Requis]| Version du type d’application.|
| --timeout -t                      | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                           | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                         | Affiche ce message d’aide et quitte.|
| --output -o                       | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                  Valeur par défaut : json.|
| --query                           | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                         | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-provision"></a>sfctl application provision
Permet d’annuler l’inscription d’un type d’application Service Fabric avec le cluster.
        
Permet d’annuler l’inscription d’un type d’application Service Fabric avec le cluster. Nécessaire avant toute instanciation d’une nouvelle application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-build-path [Requis]| Chemin d’accès du magasin d’images relatif au package d’application.|
| --timeout -t                         | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                              | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                            | Affiche ce message d’aide et quitte.|
| --output -o                          | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query                              | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                            | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-type"></a>sfctl application type

Permet d’obtenir la liste des types d’applications du cluster Service Fabric qui correspondent exactement au nom spécifié.

Renvoie les informations sur les types d’application qui sont approvisionnés ou en cours d’approvisionnement dans le cluster Service Fabric. Ces résultats sont des types d’applications dont le nom correspond exactement à celui spécifié comme paramètre et qui sont conformes aux paramètres de requête donnés. Toutes les versions du type d’application correspondant au nom de type d’application sont renvoyées avec chaque version renvoyée sous la forme d’un type d’application. La réponse comprend le nom, la version, l’état et d’autres informations sur le type d’application. Il s’agit d’une requête paginée, ce qui signifie que si les applications ne tiennent pas dans une page, une page de résultats est renvoyée, ainsi que d’un jeton de liaison, qui peut être utilisé pour obtenir la page suivante. Par exemple, si 10 types d’application ne tiennent pas sur une page qui ne peut en contenir que 3 ou si le maximum de résultats est défini sur 3, alors la valeur 3 est renvoyée. Pour accéder au reste des résultats, récupérez les pages suivantes en utilisant le jeton de liaison renvoyé dans la requête suivante. Un jeton de liaison vide est renvoyé s’il n’existe aucune autre page.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-name [Requis]| Nom du type d’application.|
| --continuation-token           | Le paramètre de jeton de liaison permet d’obtenir le jeu de résultats suivant. Un jeton de liaison pourvu d’une valeur non vide est inclus dans la réponse de l’API si les résultats du système ne tiennent pas dans une seule réponse. Lorsque cette valeur est transmise à l’appel d’API suivant, l’API retourne le jeu de résultats suivant. S’il n’existe pas de résultats supplémentaires, le jeton de liaison ne contient pas de valeur. La valeur de ce paramètre ne doit pas être codée URL.|
| --exclude-application-parameters  | Indicateur qui spécifie si les paramètres de l’application doivent être exclus du résultat.|
| --max-results                  | Nombre maximal de résultats à renvoyer dans le cadre des requêtes paginées. Ce paramètre définit la limite supérieure du nombre de résultats renvoyés. Le nombre de résultats renvoyés peut être inférieur au nombre maximal de résultats spécifié s’ils ne tiennent pas dans le message conformément aux restrictions de taille maximale définies dans la configuration. Si ce paramètre est égal à zéro ou n’est pas spécifié, la requête paginée comprend le nombre maximal de résultats pouvant tenir dans le message renvoyé.|
| --timeout -t                   | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                        | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                      | Affiche ce message d’aide et quitte.|
| --output -o                    | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.               Valeur par défaut : json.|
| --query                        | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                      | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Permet de supprimer ou d’annuler l’inscription d’un type d’application Service Fabric dans le cluster.

Permet de supprimer ou d’annuler l’inscription d’un type d’application Service Fabric dans le cluster. Cette opération n’est possible que si toutes les instances d’application du type d’application ont été supprimées. Une fois l’enregistrement du type d’application annulé, aucune nouvelle instance de l’application ne peut être créée pour ce type d’application particulier.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --application-type-name    [Requis]| Nom du type d’application.|
| --application-type-version [Requis]| Version du type d’application.|
| --timeout -t                      | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                           | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                         | Affiche ce message d’aide et quitte.|
| --output -o                       | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.                  Valeur par défaut : json.|
| --query                           | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                         | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Commence la mise à niveau d’une application dans le cluster Service Fabric.

Valide les paramètres de mise à niveau d’application fournis et commence la mise à niveau de l’application si les paramètres sont valides. Veuillez noter que la description de la mise à niveau remplace la description de l’application existante. Cela signifie que si les paramètres ne sont pas spécifiés, les paramètres existants sur les applications seront remplacés par la liste de paramètres vide. Cela se traduit par l’utilisation par l’application de la valeur par défaut des paramètres à partir du manifeste d’application.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --app-id             [Requis]| Identité de l’application. Il s’agit généralement du nom complet de l’application sans le schéma d’URI « fabric: ». Depuis la version 6.0, les noms hiérarchiques sont séparés par le caractère « ~ ». Concernant l'option
        example, if the application name is 'fabric://myapp/app1', the application identity would be
        'myapp~app1' in 6.0+ and 'myapp/app1' in previous versions.|
| --app-version        [Requis]| Version de l’application cible.| | --parameters         [Requis]| Liste encodée au format JSON des remplacements de paramètres de l’application à appliquer lors de la mise à niveau de l’application.| | --default-service-health-policy| Spécification encodée au format JSON de la stratégie d’intégrité utilisée par défaut pour évaluer l’intégrité d’un type de service.| | --failure-action            | Action à effectuer lorsqu’une mise à niveau surveillée est confrontée à des violations d’une stratégie de surveillance ou d’intégrité.| | --force-restart             | Redémarrage forcé au cours d’une mise à niveau même en l’absence de changement de version du code.| | --health-check-retry-timeout| Nombre de nouvelles tentatives d’évaluation de l’intégrité lorsque l’application ou le cluster est défectueux avant l’exécution de l’action d’échec. Mesurée en millisecondes.  Valeur par défaut : PT0H10M0S.| | --health-check-stable-duration | Durée pendant laquelle l’application ou le cluster doit rester intègre avant que la mise à niveau ne passe au domaine de mise à niveau suivant.            Mesurée en millisecondes.  Valeur par défaut : PT0H2M0S.| | --health-check-wait-duration| Délai d’attente avant l’achèvement d’un domaine de mise à niveau avant l’application de stratégies d’intégrité. Mesurée en millisecondes.            Valeur par défaut : 0.| | --max-unhealthy-apps        | Pourcentage maximal autorisé d’applications déployées défectueuses. Représenté sous forme d’un nombre compris entre 0 et 100.| | --mode                      | Mode utilisé pour surveiller l’intégrité au cours du déploiement d’une mise à niveau.            Valeur par défaut : UnmonitoredAuto.| | --replica-set-check-timeout | Durée maximale pour bloquer le traitement d’un domaine de mise à niveau et éviter la perte de disponibilité en cas de problèmes inattendus. Mesurée en secondes.| | --service-health-policy     | Mappage encodé au format JSON avec stratégie d’intégrité de type de service par nom de type de service. Le mappage est vide par défaut. | | --timeout -t                | Délai d’attente du serveur en secondes.  Valeur par défaut : 60.| | --upgrade-domain-timeout    | Durée d’exécution de chaque domaine de mise à niveau avant l’exécution de FailureAction. Mesurée en millisecondes.  Valeur par défaut :            P10675199DT02H48M05.4775807S.| | --upgrade-timeout           |Durée d’exécution de l’ensemble de la mise à niveau avant l’exécution de FailureAction. Mesurée en millisecondes.  Valeur par défaut :            P10675199DT02H48M05.4775807S.| | --warning-as-error          | Traitement de tous les avertissements d’évaluation d’intégrité avec le même niveau de gravité que les erreurs.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug                     | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h                   | Affiche ce message d’aide et quitte.|
| --output -o                 | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.            Valeur par défaut : json.|
| --query                     | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/.|
| --verbose                   | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="sfctl-application-upload"></a>sfctl application upload
Copie un package d’application Service Fabric dans le magasin d’images.

Permet d’afficher éventuellement la progression du chargement pour chaque fichier du package. La progression du chargement est envoyée à `stderr`.

### <a name="arguments"></a>Arguments

|Argument|Description|
| --- | --- |
| --path [Requis]| Chemin d’accès au package d’application local.|
|--imagestore-string| Magasin d’images de destination dans lequel charger le package d’application.  Valeur par défaut :
                         fabric:ImageStore.|
| --show-progress  | Permet d’afficher la progression du chargement pour les packages volumineux.|

### <a name="global-arguments"></a>Arguments globaux

|Argument|Description|
| --- | --- |
| --debug       | Augmente le détail de la journalisation pour afficher tous les journaux de débogage.|
| --help -h     | Affiche ce message d’aide et quitte.|
| --output -o   | Format de sortie.  Valeurs autorisées : json, jsonc, table, tsv.  Valeur par défaut : json.|
| --query       | Chaîne de requête JMESPath. Pour obtenir plus d’informations et d’exemples, consultez le site à l’adresse http://jmespath.org/
                       .|
| --verbose     | Augmente le détail de la journalisation. Utilisez --debug pour les journaux de débogage complets.|

## <a name="next-steps"></a>Étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande (CLI) Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).
