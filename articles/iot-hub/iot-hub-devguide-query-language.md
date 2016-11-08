---
title: Guide du développeur - Langage de requête | Microsoft Docs
description: Guide du développeur Azure IoT Hub - Description du langage de requête utilisé pour récupérer des informations sur les représentations, les méthodes et les travaux à partir de votre IoT Hub
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda

---
# <a name="reference-query-language-for-twins-and-jobs"></a>Référence - Langage de requête pour les représentations et les travaux
## <a name="overview"></a>Vue d'ensemble
IoT Hub fournit un puissant langage de type SQL pour récupérer des informations concernant les [représentations d’appareil][lnk-twins] et les [travaux][lnk-jobs]. Cet article présente les éléments suivants :

* une introduction aux principales fonctionnalités du langage de requête d’IoT Hub ;
* la description détaillée du langage.

## <a name="getting-started-with-twin-queries"></a>Prise en main des requêtes de représentation
Des [représentations d’appareil][lnk-twins] peuvent contenir des objets JSON arbitraires tels que des balises (Tags) et des propriétés. IoT Hub permet d’interroger une représentation d’appareil en tant que simple document JSON contenant toutes les informations relatives à la représentation.
Par exemple, supposons que vos représentations IoT Hub présentent la structure suivante :

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT Hub expose le représentations d’appareil en tant que collection de documents appelée **appareils**.
Par conséquent, la requête suivante récupère l’ensemble des représentations d’appareil :

        SELECT * FROM devices

> [!NOTE]
> Les [Kits de développement logiciel (SDK) IoT Hub][lnk-hub-sdks] prennent en charge la pagination des résultats volumineux.
> 
> 

IoT Hub permet de récupérer les représentations en les filtrant avec des conditions arbitraires. Par exemple,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

récupère les représentations dont la balise **location.region** est définie sur **US**.
Les opérateurs booléens et les comparaisons arithmétiques sont également pris en charge, par exemple,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

récupère toutes les représentations situées aux États-Unis qui sont configurées pour envoyer des télémesures moins souvent qu’à chaque minute. Pour des raisons pratiques, il est également possible d’utiliser des constantes de matrice en conjonction avec les opérateurs **IN** (dans) et **NIN** (pas dans). Par exemple,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

récupère toutes les représentations ayant signalé une connectivité Wi-Fi ou câblée. Pour une référence complète sur les fonctionnalités de filtrage, voir la section [clause WHERE][lnk-query-where].

Le regroupement et les agrégations sont également pris en charge. Par exemple,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

retourne le nombre d’appareils dans chaque état de configuration de télémétrie.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

L’exemple ci-dessus illustre une situation où trois appareils ont signalé une configuration réussie, deux d’entre eux appliquant toujours la configuration et le troisième ayant signalé une erreur.

### <a name="c-example"></a>Exemple en code C
La fonctionnalité de requête est exposée par le [Kit de développement logiciel (SDK)du service C#][lnk-hub-sdks] dans la classe **RegistryManager**.
Voici un exemple de requête simple :

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Notez comment l’objet **query** est instancié avec une taille de page (jusqu’à 1 000), puis comment plusieurs pages peuvent être récupérées en appelant la méthode **GetNextAsTwinAsync** plusieurs fois.
Il est important de noter que l’objet query expose plusieurs **Next\***, selon l’option de désérialisation requise par la requête, c’est-à-dire des objets twin ou job, ou un Json simple à utiliser en cas d’utilisation de projections.

### <a name="node-example"></a>Exemple de nœud
La fonctionnalité de requête est exposée par le [Kit de développement logiciel (SDK) du service de nœud][lnk-hub-sdks] dans l’objet **Registry**.
Voici un exemple de requête simple :

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Notez comment l’objet **query** est instancié avec une taille de page (jusqu’à 1000), puis comment plusieurs pages peuvent être récupérées en appelant la méthode **nextAsTwin** plusieurs fois.
Il est important de noter que l’objet query expose plusieurs **Next\***, selon l’option de désérialisation requise par la requête, c’est-à-dire des objets twin ou job, ou un Json simple à utiliser en cas d’utilisation de projections.

### <a name="limitations"></a>Limitations
Actuellement, les projections sont prises en charge uniquement en cas d’utilisation d’agrégations, c’est-à-dire que des requêtes non agrégées peuvent utiliser uniquement `SELECT *`. En outre, les agrégations sont prises en charge uniquement conjointement avec un regroupement.

## <a name="getting-started-with-jobs-queries"></a>Prise en main des requêtes de travaux
Les [travaux][lnk-jobs] constituent un moyen d’exécuter des opérations sur des ensembles d’appareils. Chaque représentation d’appareil contient les informations des travaux auxquels elle participe dans un regroupement nommé **travaux**.
Sous forme logique,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Actuellement, ce regroupement peut être interrogé en tant que **devices.jobs** dans le langage de requête d’IoT Hub.

Par exemple, pour obtenir tous les travaux (passés et planifiés) qui affectent un appareil donné, vous pouvez utiliser la requête suivante :

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Notez comment cette requête fournit l’état spécifique de l’appareil (et éventuellement la réponse de méthode directe) pour chaque travail retourné.
Il est également possible de filtrer avec des conditions booléennes arbitraires sur toutes les propriétés des objets du regroupement **devices.jobs**.
Par exemple, la requête suivante :

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

récupère tous les travaux de mise à jour de représentation pour l’appareil **myDeviceId**, qui ont été créés après le mois de septembre 2016.

Il est également possible de récupérer les résultats d’un travail unique par appareil.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitations
Actuellement, les requêtes sur **devices.jobs** ne prennent pas en charge :

* les projections, c’est-à-dire que seul `SELECT *` est possible ;
* les conditions faisant référence à la représentation d’appareil en plus des propriétés du travail, comme indiqué ci-dessus ;
* l’exécution d’agrégations, par exemple, count, avg, group by.

## <a name="basics-of-an-iot-hub-query"></a>Principes de base d’une requête IoT Hub
Chaque requête IoT Hub se compose de clauses SELECT et FROM et, en option, de clauses WHERE et GROUP BY. Chaque requête est exécutée sur un regroupement de documents JSON, par exemple, des représentations d’appareil. La clause FROM indique le regroupement de documents sur lequel elle doit être itérée (**devices** ou **devices.jobs**). Ensuite, le filtre dans la clause WHERE est appliqué. Dans le cas d’agrégations, les résultats de cette étape sont regroupés comme spécifié dans la clause GROUP BY, puis, pour chaque groupe, une ligne est générée comme spécifié dans la clause SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Clause FROM
La clause **FROM <from_specification>** clause ne peut supposer que deux valeurs : **FROM devices** pour interroger les représentations d’appareil, ou **FROM devices.jobs** pour interroger les détails de travaux par appareil.

## <a name="where-clause"></a>Clause WHERE
La clause **WHERE <filter_condition>** est facultative. Elle indique les conditions que les documents JSON du regroupement FROM doivent remplir pour être inclus dans le résultat. Pour être inclus dans le résultat, chaque document JSON doit évaluer les conditions spécifiées comme « true ».

Les conditions autorisées sont décrites dans la section [Expressions et conditions][lnk-query-expressions].

## <a name="select-clause"></a>Clause SELECT
La clause SELECT (**SELECT <select_list>**) est obligatoire. Elle spécifie les valeurs qui sont récupérées de la requête. Elle spécifie les valeurs JSON à utiliser pour générer de nouveaux objets JSON. Pour chaque élément du sous-ensemble filtré (et éventuellement groupé) du regroupement FROM, la phase de projection génère un nouvel objet JSON, construit avec les valeurs spécifiées dans la clause SELECT.

La grammaire de la clause SELECT est la suivante :

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

où **attribute_name** fait référence à n’importe quelle propriété du document JSON dans le regroupement FROM. Vous trouverez des exemples de clauses SELECT dans la section [Prise en main des requêtes de représentation][lnk-query-getstarted].

Actuellement, les clauses de sélection autres que **SELECT\*** sont prises en charge uniquement dans des requêtes d’agrégation sur des représentations.

## <a name="group-by-clause"></a>Clause GROUP BY
La clause **GROUP BY <group_specification>** est une étape facultative qui peut être exécutée après le filtre spécifié dans la clause WHERE, et avant la projection spécifiée dans la clause SELECT. Elle groupe des documents en fonction de la valeur d’un attribut. Ces groupes sont utilisés pour générer des valeurs agrégées comme spécifié dans la clause SELECT.

Voici un exemple de requête utilisant la clause GROUP BY :

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

La syntaxe formelle de la clause GROUP BY est la suivante :

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

où **attribute_name** fait référence à n’importe quelle propriété du document JSON dans le regroupement FROM. 

Actuellement, la clause GROUP BY est prise en charge uniquement lors de l’interrogation de représentations.

## <a name="expressions-and-conditions"></a>Expressions et conditions
À un niveau élevé, une *expression* :

* prend la valeur d’une instance d’un type JSON (Boolean, number, string, array, or object) ;
* est définie en manipulant des données provenant du document JSON de l’appareil et des constantes à l’aide de fonctions et d’opérateurs intégrés.

Les *conditions* sont des expressions qui prennent une valeur booléenne, c’est-à-dire que toute constante autre que la valeur booléenne **true** est considérée comme ayant la valeur **false** (y compris **null**, **undefined**, toute instance d’objet ou de tableau, toute chaîne et clairement la valeur booléenne **false**).

La syntaxe des expressions est la suivante :

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

où :

| Symbole | Définition |
| --- | --- |
| attribute_name |Toute propriété du document JSON dans le regroupement FROM. |
| unary_operator |Tout opérateur unaire tel que défini dans la section Operators. |
| binary_operator |Tout opérateur binaire tel que défini dans la section Operators. |
| decimal_literal |Variable exprimée en notation décimale. |
| hexadecimal_literal |Nombre exprimé par la chaîne « 0x » suivi d’une chaîne de chiffres hexadécimaux. |
| string_literal |Les littéraux de chaîne sont des chaînes Unicode représentées par une séquence de zéro ou plusieurs caractères Unicode ou séquences d’échappement. Les littéraux de chaîne sont placés entre guillemets simples (apostrophes, ’) ou guillemets doubles ("). Échappements autorisés : `\'`, `\"`, `\\`, `\uXXXX` pour les caractères Unicode définis par 4 chiffres hexadécimaux. |

### <a name="operators"></a>Opérateurs
Les opérateurs suivants sont pris en charge :

| Famille | Opérateurs |
| --- | --- |
| Opérateurs arithmétiques |+,-,*,/,% |
| Opérateurs logiques |AND, OR, NOT |
| Opérateurs de comparaison |=, !=, <, >, <=, >=, <> |

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment exécuter des requêtes dans vos applications à l’aide des [Kits de développement logiciel (SDK) IoT Hub][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md


<!---HONumber=Oct16_HO2-->


