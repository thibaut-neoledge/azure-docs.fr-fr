<properties
	pageTitle="Azure Event Hubs Archive | Microsoft Azure"
	description="Présentation de la fonctionnalité Azure Event Hubs Archive."
	services="event-hubs"
	documentationCenter=""
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="darosa;sethm"/>

# Azure Event Hubs Archive

Azure Event Hubs Archive vous permet de fournir automatiquement les données en continu dans vos Event Hubs vers le compte de stockage Blob de votre choix avec une souplesse accrue pour spécifier un intervalle personnalisé de temps ou de taille. La configuration de l’outil Archive est rapide : il n’existe aucun coût d’administration pour son exécution et il s’adapte automatiquement à vos [unités de débit](event-hubs-overview.md#capacity-and-security) Event Hubs. Event Hubs Archive représente le moyen le plus simple de charger les données pour la diffusion en continu dans Azure, et vous permet de vous concentrer sur le traitement des données plutôt que sur la capture de données.

Azure Event Hubs Archive vous permet de traiter des pipelines basés sur des lots et en temps réel sur le même flux. Cela vous permet de créer des solutions capables d’évoluer avec vos besoins au fil du temps. Que vous créiez des systèmes basés sur des lots dès aujourd’hui en pensant au traitement en temps réel à l’avenir, ou que vous souhaitiez ajouter un chemin à froid efficace vers une solution existante en temps réel, Event Hubs Archive facilite la tâche avec les données diffusées en continu.

## Fonctionnement de l’outil Event Hubs Archive

Event Hubs est une mémoire tampon durable de rétention temporelle pour l’entrée de télémétrie, similaire à un journal distribué. La clé pour diminuer la taille des instances Event Hubs est le [modèle de consommateur partitionné](event-hubs-overview.md#partition-key). Chaque partition est un segment de données indépendant, et est utilisée de manière indépendante. Au fil du temps ces données vieillissent, en fonction de la période de rétention configurable. Résultat : un Event Hub donné n’est jamais « saturé ».

Event Hubs Archive vous permet de spécifier votre propre compte de stockage Blob Azure, ainsi qu’un conteneur qui sera utilisé pour stocker les données archivées. Ce compte peut se trouver dans la même région que votre Event Hub ou dans une autre région, ce qui ajoute à la flexibilité de la fonctionnalité Event Hubs Archive.

Les données archivées sont écrites au format [Apache Avro][] ; un format compact, rapide et binaire qui fournit des structures de données riches avec un schéma inclus. Ce format est largement utilisé dans l’écosystème Hadoop, ainsi que par Stream Analytics et Azure Data Factory. Vous trouverez plus d’informations sur l’utilisation d’Avro plus loin dans cet article.

### Fenêtrage de l’archivage

Event Hubs Archive vous permet de définir une fenêtre pour contrôler l’archivage. Cette fenêtre présente une taille minimale et une configuration temporelle avec une « stratégie de premier gagnant », ce qui signifie que le premier déclencheur rencontré entraîne une opération d’archivage. Si vous avez une fenêtre d’archivage de quinze minutes/100 Mo et si vous envoyez 1 Mo/s, la fenêtre de taille se déclenche avant la fenêtre temporelle. Chaque partition archive indépendamment et écrit un objet blob de blocs complet au moment de l’archivage, nommé d’après l’heure à laquelle l’intervalle d’archivage a été rencontré. La convention d’affectation de noms est la suivante :

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### Mise à l’échelle des unités de débit

Le trafic Event Hubs est contrôlé par les [unités de débit](event-hubs-overview.md#capacity-and-security). Une unité de débit autorise 1 Mo/s ou 1 000 événements par seconde d’entrée et 2 000 événements par seconde de sortie. Les concentrateurs d’événements Standard peuvent être configurés avec 1 à 20 unités de débit, et vous pouvez en acheter plus via une [demande de support][] d’augmentation de quota. L’utilisation au-delà des unités de débit achetées est limitée. Event Hubs Archive copie les données directement depuis le stockage Event Hubs interne, en contournant les quotas de sortie des unités de débit et en enregistrant votre sortie pour d’autres lecteurs de traitement tels que Stream Analytics ou Spark.

Une fois configuré, l’outil Event Hubs Archive s’exécute automatiquement dès que vous envoyez votre premier événement. Il continue à s’exécuter en permanence. Pour que votre traitement en aval sache plus facilement que le processus fonctionne, les Event Hubs écrivent des fichiers vides en l’absence de données. Cela fournit une cadence prévisible et un marqueur qui peuvent alimenter vos processeurs de traitement par lots.

## Configuration de l’outil Event Hubs Archive

Event Hubs Archive peut être configuré au moment de la création d’Event Hubs via le portail ou Azure Resource Manager. Il suffit d’activer Archive en cliquant sur le bouton **Activé**. Configurez un compte de stockage et un conteneur en cliquant sur la section **Conteneur** du panneau. Étant donné qu’Event Hubs Archive utilise l’authentification de service à service avec le stockage, il est inutile de spécifier une chaîne de connexion de stockage. Le sélecteur de ressources sélectionne automatiquement l’URI de ressource pour votre compte de stockage. Si vous utilisez Azure Resource Manager, vous devez fournir explicitement cet URI en tant que chaîne.

La fenêtre temporelle par défaut est de cinq minutes. La valeur minimum est 1, la valeur maximum 15. La fenêtre **Taille** présente une plage de 10 à 500 Mo.

![][1]

## Ajout d’Archive à un Event Hub existant

Archive peut être configuré sur les Event Hubs existants qui se trouvent dans un espace de noms Event Hubs. La fonctionnalité n’est pas disponible sur les espaces de noms de type Messagerie ou Mixte plus anciens. Pour activer Archive sur un Event Hub existant, ou pour modifier les paramètres d’Archive, cliquez sur votre espace de noms pour charger le panneau **Essentials**, puis cliquez sur l’Event Hub pour lequel vous souhaitez activer ou modifier la configuration d’Archive. Enfin, cliquez sur la section **Propriétés** du panneau ouvert comme indiqué dans l’illustration suivante.

![][2]

Vous pouvez également configurer Event Hubs Archive via des modèles Azure Resource Manager. Pour plus d’informations, consultez [cet article](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## Exploration d’Archive et utilisation d’Avro

Une fois configuré, Event Hubs Archive crée des fichiers dans le compte de stockage Azure et le conteneur fourni dans la fenêtre temporelle configurée. Vous pouvez afficher ces fichiers à l’aide de n’importe quel outil tel que [l’Explorateur de stockage Azure][]. Vous pouvez télécharger les fichiers localement pour les utiliser.

Les fichiers générés par Event Hubs Archive présentent le schéma Avro suivant :

![][3]

Un moyen facile d’explorer les fichiers Avro consiste à utiliser la boîte à [outils Avro][] d’Apache. Après avoir téléchargé cette boîte, vous pouvez voir le schéma d’un fichier Avro spécifique en exécutant la commande suivante :

```
java -jar avro-tools-1.8.1.jar getschema <name of archive file>
```

Cette commande renvoie

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Vous pouvez également utiliser les outils Avro pour convertir le fichier au format JSON et effectuer d’autres traitements.

Pour effectuer un traitement plus avancé, téléchargez et installez Avro pour la plateforme de votre choix. Au moment de la rédaction de cet article, les implémentations sont disponibles pour C, C++, C#, Java, NodeJS, Perl, PHP, Python et Ruby.

Apache Avro propose des guides de mise en route complets pour [Java][] et [Python][]. Vous pouvez également lire l’article [Prise en main des Event Hubs](event-hubs-archive-python.md).

## Facturation de l’outil Event Hubs Archive

Event Hubs Archive est mesuré de la même façon que les unités de débit, en tant que tarif horaire. La facturation est directement proportionnelle au nombre d’unités de débit achetées pour l’espace de noms. Tandis que les unités de débit sont augmentées et diminuées, Event Hubs Archive augmente et diminue pour fournir des performances correspondantes. Les compteurs existent en parallèle. Les frais d’Event Hubs Archive sont de 0,10 $ par heure et par unité de débit, et sont assortis d’une remise de 50 % pendant la période d’évaluation.

Event Hubs Archive est véritablement le moyen le plus simple pour récupérer des données dans Azure. À l’aide d’Azure Data Lake, d’Azure Data Factory et d’Azure HDInsight, vous pouvez effectuer un traitement par lots, ainsi que d’autres analyses de votre choix en utilisant des outils et des plateformes que vous connaissez, à l’échelle requise.

## Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

- Un [exemple d'application complet qui utilise des hubs d’événements][].
- L’exemple de [montée en puissance du traitement des événements avec Event Hubs][].
- [Vue d’ensemble des concentrateurs d’événements][]

[Apache Avro]: http://avro.apache.org/
[demande de support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[l’Explorateur de stockage Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[outils Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Vue d’ensemble des concentrateurs d’événements]: event-hubs-overview.md
[exemple d'application complet qui utilise des hubs d’événements]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->