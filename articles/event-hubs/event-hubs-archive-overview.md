---
title: "Présentation de l’archivage des données de télémétrie avec Azure Event Hubs Archive | Microsoft Docs"
description: "Présentation de la fonctionnalité Azure Event Hubs Archive."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: f86336de4e1d5bda1eba12f0f95079b950963bde
ms.lasthandoff: 04/18/2017


---
# <a name="azure-event-hubs-archive"></a>Azure Event Hubs Archive
Azure Event Hubs Archive vous permet de fournir automatiquement les données en continu dans Event Hubs vers le compte de stockage Blob de votre choix avec une souplesse accrue pour spécifier un intervalle personnalisé de temps ou de taille. La configuration de l’outil Archive est rapide : il n’existe aucun coût d’administration pour son exécution et il s’adapte automatiquement à vos [unités de débit](event-hubs-what-is-event-hubs.md#capacity) Event Hubs. Event Hubs Archive représente le moyen le plus simple de charger les données pour la diffusion en continu dans Azure, et vous permet de vous concentrer sur le traitement des données plutôt que sur la capture de données.

Event Hubs Archive vous permet de traiter des pipelines basés sur des lots et en temps réel sur le même flux. Cela vous permet de créer des solutions capables d’évoluer avec vos besoins au fil du temps. Que vous créiez des systèmes basés sur des lots dès aujourd’hui en pensant au traitement en temps réel à l’avenir, ou que vous souhaitiez ajouter un chemin à froid efficace vers une solution existante en temps réel, Event Hubs Archive facilite la tâche avec les données diffusées en continu.

## <a name="how-event-hubs-archive-works"></a>Fonctionnement de l’outil Event Hubs Archive
Event Hubs est une mémoire tampon durable de rétention temporelle pour l’entrée de télémétrie, similaire à un journal distribué. La clé de la mise à l’échelle dans Event Hubs est le [modèle de consommateur partitionné](event-hubs-what-is-event-hubs.md#partitions). Chaque partition est un segment de données indépendant, et est utilisée de manière indépendante. Au fil du temps ces données vieillissent, en fonction de la période de rétention configurable. Par conséquent, un hub d’événements donné n’est jamais « saturé ».

Event Hubs Archive vous permet de spécifier votre propre compte de stockage Blob Azure, ainsi qu’un conteneur qui sera utilisé pour stocker les données archivées. Ce compte peut se trouver dans la même région que votre hub d’événements ou dans une autre région, ce qui ajoute à la flexibilité de la fonctionnalité Event Hubs Archive.

Les données archivées sont écrites au format [Apache Avro][Apache Avro] : un format compact, rapide et binaire qui fournit des structures de données riches avec un schéma inclus. Ce format est largement utilisé dans l’écosystème Hadoop, ainsi que par Stream Analytics et Azure Data Factory. Vous trouverez plus d’informations sur l’utilisation d’Avro plus loin dans cet article.

### <a name="archive-windowing"></a>Fenêtrage de l’archivage
Event Hubs Archive vous permet de définir une « fenêtre » pour contrôler l’archivage. Cette fenêtre présente une taille minimale et une configuration temporelle avec une « stratégie de premier gagnant », ce qui signifie que le premier déclencheur rencontré entraîne une opération d’archivage. Si vous avez une fenêtre d’archivage de quinze minutes et de 100 Mo, et si vous envoyez 1 Mo par seconde, la fenêtre de taille se déclenche avant la fenêtre temporelle. Chaque partition archive indépendamment et écrit un objet blob de blocs complet au moment de l’archivage, nommé d’après l’heure à laquelle l’intervalle d’archivage a été rencontré. La convention d’affectation de noms est la suivante :

```
[Namespace]/[EventHub]/[Partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Mise à l’échelle des unités de débit
Le trafic Event Hubs est contrôlé par les [unités de débit](event-hubs-what-is-event-hubs.md#capacity). Une unité de débit autorise 1 Mo/s ou 1 000 événements par seconde d’entrée et 2 000 événements par seconde de sortie. Les concentrateurs d’événements Standard peuvent être configurés avec 1 à 20 unités de débit, et vous pouvez en acheter d’autres en soumettant une [demande de support][support request] d’augmentation de quota. L’utilisation au-delà des unités de débit que vous avez achetées est limitée. Event Hubs Archive copie les données directement depuis le stockage Event Hubs interne, en contournant les quotas de sortie des unités de débit et en enregistrant votre sortie pour d’autres lecteurs de traitement tels que Stream Analytics ou Spark.

Une fois configuré, l’outil Event Hubs Archive s’exécute automatiquement dès que vous envoyez votre premier événement. Il continue à s’exécuter en permanence. Pour que votre traitement en aval sache plus facilement que le processus fonctionne, les Event Hubs écrivent des fichiers vides en l’absence de données. Cela fournit une cadence prévisible et un marqueur qui peuvent alimenter vos processeurs de traitement par lots.

## <a name="setting-up-event-hubs-archive"></a>Configuration de l’outil Event Hubs Archive
Vous pouvez configurer Archive au moment de la création du hub d’événements via le portail ou Azure Resource Manager. Il suffit d’activer Archive en cliquant sur le bouton **Activé** . Configurez un compte de stockage et un conteneur en cliquant sur la section **Conteneur** du panneau. Étant donné qu’Event Hubs Archive utilise l’authentification de service à service avec le stockage, il est inutile de spécifier une chaîne de connexion de stockage. Le sélecteur de ressources sélectionne automatiquement l’URI de ressource pour votre compte de stockage. Si vous utilisez Azure Resource Manager, vous devez fournir explicitement cet URI en tant que chaîne.

La fenêtre temporelle par défaut est de cinq minutes. La valeur minimum est 1, la valeur maximum 15. La fenêtre **Taille** présente une plage de 10 à 500 Mo.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Ajout d’Archive à un hub d’événements existant
Archive peut être configuré sur des hubs d’événements existants qui se trouvent dans un espace de noms Event Hubs. La fonctionnalité n’est pas disponible sur les espaces de noms plus anciens de type **Messagerie** ou **Mixte**. Pour activer Archive sur un hub d’événements existant, ou pour modifier les paramètres d’Archive, cliquez sur votre espace de noms afin de charger le panneau **Essentials**, puis cliquez sur le hub d’événements pour lequel vous souhaitez activer ou modifier la configuration d’Archive. Enfin, cliquez sur la section **Propriétés** du panneau ouvert comme indiqué dans l’illustration suivante.

![][2]

Vous pouvez également configurer Event Hubs Archive via des modèles Azure Resource Manager. Pour plus d’informations, consultez [cet article](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Exploration d’Archive et utilisation d’Avro
Une fois configuré, Event Hubs Archive crée des fichiers dans le compte de stockage Azure et le conteneur fourni dans la fenêtre temporelle configurée. Vous pouvez afficher ces fichiers à l’aide de n’importe quel outil tel que [l’Explorateur de stockage Azure][Azure Storage Explorer]. Vous pouvez télécharger les fichiers localement pour les utiliser.

Les fichiers générés par Event Hubs Archive présentent le schéma Avro suivant :

![][3]

Un moyen facile d’explorer les fichiers Avro consiste à utiliser la boîte à [outils Avro][Avro Tools] d’Apache. Après avoir téléchargé cette boîte, vous pouvez voir le schéma d’un fichier Avro spécifique en exécutant la commande suivante :

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
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

Pour effectuer un traitement plus avancé, téléchargez et installez Avro pour la plateforme de votre choix. Au moment de la rédaction de cet article, les implémentations sont disponibles pour C, C++, C\#, Java, NodeJS, Perl, PHP, Python et Ruby.

Apache Avro propose des guides de mise en route complets pour [Java][Java] et [Python][Python]. Vous pouvez également lire l’article [Prise en main des Event Hubs](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Facturation de l’outil Event Hubs Archive
Event Hubs Archive est mesuré de la même façon que les unités de débit, en tant que tarif horaire. La facturation est directement proportionnelle au nombre d’unités de débit achetées pour l’espace de noms. Tandis que les unités de débit sont augmentées et diminuées, Event Hubs Archive augmente et diminue pour fournir des performances correspondantes. Les compteurs se produisent en même temps. Les frais d’Event Hubs Archive sont de 0,10 $ par heure et par unité de débit, et sont assortis d’une remise de 50 % pendant la période d’évaluation.

Event Hubs Archive est le moyen le plus simple pour récupérer des données dans Azure. À l’aide d’Azure Data Lake, d’Azure Data Factory et d’Azure HDInsight, vous pouvez effectuer un traitement par lots, ainsi que d’autres analyses de votre choix en utilisant des outils et des plateformes que vous connaissez, à l’échelle requise.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* Un [exemple d'application complet qui utilise des hubs d’événements][sample application that uses Event Hubs].
* L’exemple de [montée en puissance du traitement des événements avec Event Hubs][Scale out Event Processing with Event Hubs].
* [Vue d’ensemble des hubs d’événements][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

