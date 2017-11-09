---
title: "Vue d’ensemble d’Azure Event Grid"
description: "Détaille Azure Event Grid et ses concepts."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 10/20/2017
ms.author: babanisa
ms.openlocfilehash: e798a1e751cb9e789bc479a6f842fb7f8f703bbf
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2017
---
# <a name="an-introduction-to-azure-event-grid"></a>Présentation d’Azure Event Grid

Azure Event Grid vous permet de créer facilement des applications avec les architectures basées sur des événements. Sélectionnez la ressource Azure à laquelle vous souhaitez vous abonner et donnez un gestionnaire d’événements ou un point de terminaison WebHook auquel envoyé l’événement. Event Grid dispose d’une prise en charge intégrée pour les événements provenant des services Azure, tels que les objets BLOB de stockage et les groupes de ressources. Event Grid dispose également d’une prise en charge personnalisée pour les événements d’application et de tiers, à l’aide des rubriques et webhooks personnalisés. 

Vous pouvez utiliser des filtres pour acheminer des événements spécifiques à différents points de terminaison, multidiffuser vers des points de terminaison multiples et vous assurez que vos événements sont correctement livré. Event Grid dispose également d’une prise en charge intégrée pour des événements personnalisés et tiers.

Dans sa version préliminaire, Event Grid prend en charge les emplacements **westus2** et **westcentralus**. D’autres régions seront ajoutées.

Cet article fournit une vue d’ensemble d’Azure Event Grid. Pour bien démarrer avec Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).

![Modèle de grille d’événement fonctionnel](./media/overview/event-grid-functional-model.png)

## <a name="concepts"></a>Concepts

Il existe cinq concepts dans Azure Event Grid qui vous permettent de démarrer :

* **Événements** : ce qu’il s’est passé.
* **Sources/éditeurs d’événements** : où l’événement a eu lieu.
* **Rubriques** : le point de terminaison où les éditeurs envoient des événements.
* **Abonnements aux événements** : le mécanisme de point de terminaison ou intégré pour acheminer des événements, parfois à plusieurs gestionnaires. Les abonnements sont également utilisés par des gestionnaires pour filtrer intelligemment les événements entrants.
* **Gestionnaires d’événements** : l’application ou le service réagissant à l’événement.

Pour plus d’informations sur ces concepts, consultez [Concepts dans Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Fonctionnalités

Voici les principales fonctionnalités d’Azure Event Grid :

* **Simplicité** : pointez et cliquez pour chercher des événements depuis votre ressource Azure vers tout gestionnaire ou point de terminaison d’événement.
* **Filtrage avancé** : filtrez le type d’événement ou le chemin de publication de l’événement pour s’assurer que les gestionnaires d’événements ne reçoivent que des événements pertinents.
* **Distribution ramifiée** : abonnez-vous à plusieurs points de terminaison pour le même événement pour envoyer des copies de l’événement à autant de places que nécessaire.
* **Fiabilité** : utiliser la nouvelle tentative de 24 heures avec interruption exponentielle pour garantir la livraison des événements.
* **Payer par événement** : payez uniquement pour le temps d’utilisation d’Event Grid.
* **Débit élevé** : générez des charges de travail élevées sur Event Grid avec prise en charge de millions d’événements par seconde.
* **Événements intégrés** : préparez et soyez rapidement opérationnel avec des événements intégrés définis par la ressource.
* **Événements personnalisés** : utilisez le routage Event Grid, le filtrage et les événements personnalisés livré de manière fiable dans votre application.

## <a name="built-in-publisher-and-handler-integration"></a>Intégration du gestionnaire et de l’éditeur intégrés

Azure propose la prise en charge de l’événement intégré à l’aide de nombreux services, y compris les éditeurs et les gestionnaires.

### <a name="publishers"></a>Éditeurs

Actuellement, les services Azure suivants ont une prise en charge de l’éditeur intégré pour Event Grid :

* Groupes de ressources (opérations de gestion)
* Abonnements Azure (opérations de gestion)
* Event Hubs
* Storage Blob
* Rubriques personnalisées

D’autres services Azure seront ajoutés cette année.

### <a name="handlers"></a>Gestionnaires

Actuellement, les services Azure suivants ont une prise en charge du gestionnaire intégré pour Event Grid : 

* Azure Functions
* Logic Apps
* Azure Automation
* WebHooks
* Microsoft Flow

D’autres services Azure seront ajoutés cette année.

## <a name="what-can-i-do-with-event-grid"></a>Que puis-je faire avec Event Grid ?

Azure Event Grid fournit plusieurs fonctionnalités qui améliorent considérablement l’automatisation d’opérations sans serveur et le travail d’intégration : 

### <a name="serverless-application-architectures"></a>Architectures d’application sans serveur

![Application sans serveur](./media/overview/serverless_web_app.png)

La grille d’événement connecte des sources de données et des gestionnaires d’événements. Par exemple, utilisez la grille d’événement pour déclencher instantanément une fonction sans serveur afin d’exécuter une analyse d’image chaque fois qu’une nouvelle photo est ajoutée à un conteneur de stockage d’objets blob. 

### <a name="ops-automation"></a>Automatisation des opérations

![Automatisation des opérations](./media/overview/Ops_automation.png)

Event Grid vous permet d’accélérer l’automatisation et de simplifier l’application de la stratégie. Par exemple, Event Grid peut informer Azure Automation lors de la création d’une machine virtuelle ou de l’entrée en service d’une base de données SQL. Ces événements permettent de vérifier automatiquement que les configurations de service sont conformes, placent des métadonnées dans les outils d’exploitation, étiquètent les machines virtuelles ou archivent des éléments de travail.

### <a name="application-integration"></a>Intégration d’applications

![Intégration d’applications](./media/overview/app_integration.png)

Event Grid connecte votre application à d’autres services. Par exemple, créez une rubrique d’application pour envoyer les données d’événement de votre application à Event Grid et profitez de sa livraison fiable, de son routage avancé et de son intégration directe à Azure. Vous pouvez également utiliser Event Grid avec Logic Apps pour traiter des données en tout lieu sans écrire de code. 

## <a name="how-is-event-grid-different-from-other-azure-integration-services"></a>En quoi Event Grid est différent d’autres services d’intégration Azure ?

Event Grid est un fond de panier de gestion des événements qui permet la programmation réactive, pilotée par événements. Elle est intégrée en profondeur aux services Azure et peut être intégrée à des services tiers. Le message d’événement contient les informations dont vous avez besoin pour réagir aux modifications apportées aux services et applications. Event Grid n’est pas un pipeline de données et ne fournit pas l’objet réel qui a été mis à jour.

Service Bus est parfaitement adapté aux applications d’entreprise traditionnelles qui nécessitent des transactions, des classements, la détection des doublons et une cohérence instantanée. Event Grid est conçu pour la vitesse, la mise à l’échelle, l’étendue et le bas coût dans un modèle réactif. Elle est parfaitement adaptée à une architecture sans serveur.

Event Grid complète d’autres services Azure tels que Logic Apps et Event Hubs. Event Grid déclenche l’application logique pour commencer son flux de travail. Event Hubs fonctionne avec Event Grid en vous permettant de réagir aux événements depuis Event Hubs Capture et crée une entrée de données et des pipelines de transformation.

## <a name="how-much-does-event-grid-cost"></a>Combien coûte Event Grid ?

Azure Event Grid utilise un modèle de tarification de paie par événement, afin que vous ne payez que ce que vous utilisez.

Event Grid coûte 0,60 $ par million d’opérations (0,30 $ pendant l’aperçu) et les premières 100 000 opérations par mois sont gratuites. Les opérations sont définies comme entrée d’événement, correspondance avancée, tentative de livraison et appels de gestion.  Pour plus de détails, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Étapes suivantes

* [Router les événements d’objet blob de stockage](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Répondez aux événements d’objet blob de stockage à l’aide d’Event Grid.
* [Créer des événements personnalisés et s’y abonner](custom-event-quickstart.md)  
  Démarrez et commencez à envoyer vos propres événements personnalisés vers tout point de terminaison à l’aide du guide de démarrage rapide Azure Event Grid.
* [Utilisation de Logic Apps en tant que gestionnaire d’événements](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Didacticiel sur la génération d’une application à l’aide de Logic Apps pour réagir aux événements envoyés par Event Grid.
* [Diffuser en continu des données volumineuses dans un entrepôt de données](event-grid-event-hubs-integration.md)  
  Didacticiel qui utilise Azure Functions pour diffuser en continu des données à partir d’Event Hubs vers SQL Data Warehouse.
* [Référence de l'API REST Event Grid](/rest/api/eventgrid)  
  Fournit des informations techniques supplémentaires sur Azure Event Grid et une référence pour la gestion des abonnements aux événements, le routage et le filtrage.