<properties
	pageTitle="Gestion des appareils IoT Hub - Requêtes sur les représentations d’appareils | Microsoft Azure"
	description="Didacticiel sur la gestion des appareils Azure IoT Hub décrivant l’utilisation des requêtes pour rechercher des représentations d’appareils."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Didacticiel : Recherche de représentations d’appareil physique à l’aide de requêtes avec C# (version préliminaire)

[AZURE.INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

La gestion des appareils IoT Azure vous permet de trouver des représentations d’appareils, la représentation sous forme de service d’un appareil physique, à l’aide de requêtes. Vous pouvez interroger selon les propriétés de l’appareil, les propriétés du service ou les balises de la représentation de l’appareil. Vous pouvez interroger à l’aide de balises et de propriétés :

-   Pour rechercher des représentations d’appareils à l’aide de balises, vous transmettez un tableau de chaînes et la requête retourne l’ensemble des appareils qui sont balisés avec l’ensemble de ces chaînes.

-   Pour rechercher des représentations d’appareils à l’aide des propriétés du service ou des propriétés de l’appareil, vous utilisez une expression de requête JSON.

Pour plus d’informations sur les représentations d’appareils et les requêtes, consultez [Vue d’ensemble de la gestion des appareils Azure IoT Hub][lnk-dm-overview].

## Exécution de l’exemple d’interrogation de l’appareil

L’exemple suivant étend la fonctionnalité du didacticiel [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started]. En commençant avec les différentes simulations d’appareils en cours d’exécution, il utilise une requête pour rechercher des appareils spécifiques.

### Composants requis 

Avant d’exécuter cet exemple, vous devez avoir terminé les étapes de [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started]. Cela signifie que vos appareils simulés doivent être en cours d’exécution. Si vous avez terminé le processus auparavant, redémarrez immédiatement vos appareils simulés.

### Démarrage de l’exemple

Pour démarrer l’exemple, vous devez exécuter le processus **Query.exe**. Ceci permet d’exécuter plusieurs requêtes différentes. Procédez comme suit pour démarrer l’exemple :

1.  Laissez les simulations d’appareils s’exécuter pendant au moins 1 minute. Ceci permet de s’assurer que les propriétés de la représentation d’appareil sont synchronisées avec l’appareil physique. Consultez [Didacticiel : Utilisation d’une représentation d’appareil][lnk-twin-tutorial] pour plus d’informations sur la synchronisation.

2.  À partir du dossier racine dans lequel vous avez cloné le référentiel **azure-iot-sdks**, accédez au dossier **azure-iot-sdks\\csharp\\service\\samples\\bin**.

3.  Exécutez `Query.exe <IoT Hub Connection String>`.

Dans la fenêtre de ligne de commande, une sortie doit indiquer le résultat des requêtes pour les objets appareils à l’aide de balises, des propriétés du service et des propriétés d’appareil.

## Structure de requête (JSON)

Les requêtes sur les propriétés du service et les propriétés d’appareils sont exécutées avec une chaîne JSON pour représenter la requête elle-même. La chaîne JSON est composé de 4 parties. Voici une explication de chaque partie et l’objet C# qui peut être sérialisé dans la chaîne JSON correcte.

- **Project** : l’expression qui désigne les champs à partir de l’objet appareil à inclure dans le jeu de résultats de la requête (équivalent à SELECT dans SQL) :

  ```
	  var query = JsonConvert.SerializeObject(
		  new
		  {
			  project = new
			  {
				  all = false,
				  properties = new[]
				  {
					  new
					  {
					  name = "CustomerId",
					  type = "service"
					  },
					  new
					  {
					  name = "Weight",
					  type = "service"
					  }
				  }
			  }
		  }
	  );
	```

- **Filter** : l’expression qui limite les objets appareils inclus dans le jeu de résultats de la requête (équivalent à WHERE dans SQL) :

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
        filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = "123456",
          comparisonOperator = "eq",
          type = "comparison"
        }
      }
  );
  ```

- **Aggregate** : l’expression qui détermine comment regrouper le jeu de résultats de la requête (équivalent à GROUP BY dans SQL) :

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
      filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = (string)null,
          comparisonOperator = "ne",
          type = "comparison"
        },
        aggregate = new
        {
          keys = new[]
          {
            new
            {
              name = "CustomerId",
              type = "service"
            }
          },
          properties = new[]
          {
            new
            {
              @operator = "avg",
              property = new
              {
                name = "Weight",
                type = "service"
              },
              columnName = "TotalWeight"
            }
          }
        }
      }
  );
  ```

- **Sort** : la définition d’expression de la propriété à utiliser pour trier le jeu de résultats de la requête (équivalent à ORDER BY dans SQL). Si le tri est null, **deviceID** est utilisé par défaut :

  ```
  var query = JsonConvert.SerializeObject(
    new
    {
      sort = new[]
      {
        new
        {
          property = new
          {
            name = "QoS",
            type = "service"
          },
          order = "asc"
        }
      }
    }
  );
  ```

### Limitations

Il existe certaines limitations dans l’implémentation de la version préliminaire publique des requêtes.

-   Il n’existe aucune validation sur l’expression de requête.

-   Les requêtes respectent la casse.

-   Seuls 100 appareils seront retournés lors de l’utilisation d’expressions de requête pour interroger en fonction des propriétés du service ou d’appareil. Un exemple montrant comment implémenter la pagination est disponible dans [notre bibliothèque de requêtes][lnk-query-samples].

Plus de détails sur la syntaxe et les champs disponibles pour JSON sont [disponibles][lnk-query-expression-guide]. Vous pouvez également voir des exemples de requêtes dans notre [bibliothèque d’expressions de requêtes][lnk-query-samples].

### Requêtes en fonction des propriétés du service ou d’appareil

Une fois que vous avez l’expression de requête JSON, vous pouvez interroger les représentations d’appareils. Appelez **QueryDeviceJsonAsync** et vérifiez le champ **AggregateResult** pour les requêtes d’agrégation et le champ **Result** pour toutes les autres requêtes. Le champ **Result** contient une liste d’objets appareils, qui représentent les représentations d’appareils correspondant à la requête. Le champ **AggregateResult** contient un tableau de dictionnaires, chacun contenant la ligne obtenue.

Ces requêtes sont utilisées dans **Program.cs** du projet **Query**.

```
var foundDevices = (await registryManager.QueryDevicesJsonAsync(query)).Result;

var results = (await registryManager.QueryDevicesJsonAsync(query)).AggregateResult;
```

### Requêtes par balises

L’interrogation par balises vous permet de rechercher des objets appareils sans l’aide d’une expression de requête JSON. Si plusieurs balises sont transmises, seuls les objets appareils avec toutes les balises seront affichés. Le deuxième paramètre est **maxCount**, le nombre maximum d’appareils à retourner. La valeur maximale de **maxCount** est 1 000.

```
var foundDevices = await registryManager.QueryDevicesAsync(new[] { "bacon" }, 100);
```

### Implémentation d’appareil

La requête est activée par la bibliothèque cliente de gestion des appareils Azure IoT Hub. Tant que les propriétés de votre appareil sont synchronisées (comme décrit dans le [Didacticiel : Utilisation d’une représentation d’appareil][lnk-twin-tutorial]), vous pouvez exécuter une interrogation sur ces propriétés. Les propriétés d’appareils sont disponibles uniquement lorsque l’appareil physique se connecte à IoT Hub et fournit les valeurs initiales.

## Étapes suivantes

Pour en savoir plus sur les fonctionnalités de la gestion des appareils Azure IoT Hub, vous pouvez parcourir les didacticiels suivants :

- [Utilisation des représentations d’appareil physique][lnk-twin-tutorial]
- [Utilisation de travaux d’appareils pour mettre à jour le microprogramme des appareils][lnk-jobs-tutorial]



<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

<!---HONumber=AcomDC_0622_2016-->