---
title: Glossaire Azure IoT Hub | Microsoft Docs
description: "Guide du développeur : un glossaire des termes courants relatifs à Azure IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1f795dd5b3d99731b2e683085d889ed7943d6281
ms.openlocfilehash: 3661ad7374fd91cf7bbc485b8ce0805b78518899


---
# <a name="glossary-of-iot-hub-terms"></a>Glossaire des termes d’IoT Hub
Cet article répertorie certains termes courants utilisés dans les articles relatifs à IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Advanced Message Queuing Protocol
[Advanced Message Queuing Protocol (AMQP)](https://www.amqp.org/) est l’un des protocoles de messagerie que [IoT Hub](#iot-hub) prend en charge pour communiquer avec des appareils. Pour plus d’informations sur les protocoles de messagerie pris en charge par IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="azure-cli"></a>Interface de ligne de commande Azure
L’[interface de ligne de commande Azure (Azure CLI)](../xplat-cli-install.md) est un outil de commande multiplateforme, open source, basé sur un interpréteur de commandes, servant à créer et à gérer des ressources dans Microsoft Azure.

## <a name="azure-iot-device-sdks"></a>Kits de développement logiciel (SDK) d’appareils Azure IoT
Des _Kits de développement logiciel (SDK) d’appareils_ sont disponibles pour plusieurs langages. Ils permettent de créer des [applications pour appareils](#device-app), qui interagissent avec un IoT Hub. Les didacticiels relatifs à IoT Hub expliquent comment utiliser ces Kits de développement logiciel (SDK) d’appareils. Le code source et des informations supplémentaires sur les Kits de développement logiciel d’appareils sont disponibles dans ce [référentiel](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-iot-gateway-sdk"></a>Kit de développement logiciel (SDK) de la passerelle Azure IoT
Ce Kit de développement logiciel (SDK) sert à écrire des applications permettant aux appareils connectés à la passerelle de communiquer avec [IoT Hub](#iot-hub). Les didacticiels relatifs à la passerelle IoT Hub expliquent comment utiliser ce Kit de développement Logiciel (SDK). Le code source et des informations supplémentaires sur le Kit de développement Logiciel (SDK) de passerelle Azure IoT sont disponibles dans ce [référentiel](https://github.com/Azure/azure-iot-gateway-sdk) GitHub.

## <a name="azure-iot-service-sdks"></a>Kits de développement logiciel (SDK) de services Azure IoT
Des _Kits de développement logiciel (SDK) de services_ sont disponibles pour plusieurs langages. Ils permettent de créer des [applications principales](#back-end-app) qui interagissent avec un IoT Hub. Les didacticiels relatifs à IoT Hub expliquent comment utiliser ces Kits de développement logiciel (SDK) de services. Le code source et des informations supplémentaires sur les Kits de développement logiciel de services sont disponibles dans ce [référentiel](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-portal"></a>Portail Azure
Le [portail Microsoft Azure](https://portal.azure.com) permet de configurer et de gérer l’ensemble de vos ressources Azure. Son contenu est organisé à l’aide de _panneaux_. Dans certains didacticiels concernant IoT Hub, vous pouvez être invité à utiliser le [portail Azure Classic](https://manage.windowsazure.com).

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azureps-cmdlets-docs) est une collection d’applets de commande permettant de gérer Azure avec Windows PowerShell. Ces applets de commande permettent de créer, de tester, de déployer et de gérer des solutions et des services fournis via la plateforme Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de travailler avec les ressources de votre solution en tant que groupe. Vous pouvez déployer, mettre à jour ou supprimer les ressources pour votre solution dans le cadre d’une seule opération coordonnée.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) offre une communication cloud avec une messagerie d’entreprise et une communication relayée qui vous aide à connecter des solutions locales au cloud. Certains didacticiels concernant IoT Hub utilisent des [files d’attente](../service-bus-messaging/service-bus-messaging-overview.md) Service Bus.

## <a name="azure-storage"></a>Azure Storage
Le [Stockage Azure](../storage/storage-introduction.md) est une solution de stockage cloud. Elle inclut le service Stockage Blob qui permet de stocker des données d’objet non structurées. Certains didacticiels concernant IoT Hub utilisent le service Stockage Blob.

## <a name="back-end-app"></a>Application principale
Dans le contexte de [IoT Hub](#iot-hub), une application principale est une application qui se connecte à l’un points de terminaison côté service sur un IoT Hub. Par exemple, une application principale peut récupérer des messages [appareil-à-cloud](#device-to-cloud) ou gérer le [registre des identités](#identity-registry). En règle générale, une application principale s’exécute dans le cloud mais, dans de nombreux didacticiels, les applications principales sont des applications console qui s’exécutent sur votre ordinateur de développement local.

## <a name="cloud-gateway"></a>Passerelle cloud
Une passerelle cloud assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Une passerelle cloud est hébergée dans le cloud, contrairement à un [passerelle de champ](#field-gateway) qui s’exécute localement sur vos appareils. Un cas d’utilisation classique d’une passerelle cloud est l’implémentation d’une traduction de protocole pour vos appareils.

## <a name="cloud-to-device"></a>Cloud-à-appareil
Fait référence aux messages envoyés à partir d’un IoT Hub à un appareil connecté. Souvent, ces messages sont des commandes qui donnent pour instruction à l’appareil d’effectuer une action. Pour plus d’informations, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="connection-string"></a>Chaîne de connexion
Vous utilisez des chaînes de connexion dans votre code d’application afin d’encapsuler les informations requises pour se connecter à un point de terminaison. Une chaîne de connexion inclut généralement l’adresse du point de terminaison et des informations de sécurité, mais les formats de chaîne de connexion varient selon les services. Il existe deux types de chaîne de connexion associés au service IoT Hub :
- Les *Chaînes de connexion d’appareil* permettent aux périphériques de se connecter aux points de terminaison côté appareil sur un IoT Hub.
- Les *Chaînes de connexion IoT Hub* activent des applications principales pour se connecter aux points de terminaison côté service sur un IoT Hub.

## <a name="custom-gateway"></a>Passerelle personnalisée
Une passerelle assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Le [Kit de développement logiciel (SDK) de passerelle Azure IoT](#azure-iot-gateway-sdk) permet de construire des passerelles personnalisées qui implémentent une logique personnalisée pour gérer des messages et des conversions de protocole personnalisées.

## <a name="data-point-message"></a>Message de point de données
Un message de point de données est un message d’[appareil-à-cloud](#device-to-cloud) qui contient des données de [télémétrie](#telemetry) telles que la vitesse du vent ou la température.

## <a name="desired-configuration"></a>Configuration souhaitée
Dans le contexte d’un [représentation d’appareil](iot-hub-devguide-device-twins.md), une configuration souhaitée fait référence à l’ensemble complet des propriétés et métadonnées dans la représentation d’appareil, qui doivent être synchronisées avec l’appareil.

## <a name="desired-properties"></a>Propriétés souhaitées
Dans le contexte d’une [représentation d’appareil](iot-hub-devguide-device-twins.md), les propriétés souhaitées sont une sous-section de la représentation d’appareil qui est utilisée avec des [propriétés signalées](#reported-properties) pour synchroniser une configuration ou une condition d’appareil. Des propriétés souhaitées ne peuvent être définies que par une [application principale](#back-end-app), et sont observées par l’[application pour appareil](#device-app).

## <a name="device-to-cloud"></a>Appareil-à-cloud
Qualifie des messages envoyés à partir d’un appareil connecté vers [IoT Hub](#iot-hub). Ces messages peuvent être des messages de [point de données](#data-point-message) ou [interactifs](#interactive-message). Pour plus d’informations, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Appareil
Dans le contexte d’IoT, un appareil est généralement un petit dispositif informatique autonome qui peut collecter des données ou contrôler d’autres appareils. Par exemple, un appareil peut être un dispositif de surveillance de l’environnement ou un contrôleur pour les systèmes de ventilation et d’arrosage dans une serre. Le [catalogue d’appareils](https://catalog.azureiotsuite.com/) fournit la liste des appareils certifiés pour fonctionner avec [IoT Hub](#iot-hub).

## <a name="device-app"></a>Application pour appareil
Une application pour appareil s’exécute sur votre [appareil](#device) et gère la communication avec votre [IoT Hub](#iot-hub). En règle générale, vous utilisez l’un des [Kits de développement logiciel (SDK) d’appareils Azure IoT](#azure-iot-device-sdks) lorsque vous implémentez une application pour appareil. Dans de nombreux didacticiels concernant IoT, pour des raisons pratiques, vous utilisez un [appareil simulé](#simulated-device).

## <a name="device-condition"></a>Condition d’appareil
Fait référence aux informations d’état d’un appareil, telles que la méthode de connectivité utilisée, signalée par une [application pour appareil](#device-app). Des [applications pour appareil](#device-app) peuvent également signaler leurs capacités. Vous pouvez interroger des informations de condition et de capacité en utilisant des représentations d’appareil.

## <a name="device-data"></a>Données d’appareil
Les données d’appareil font référence aux données stockées pour chaque appareil dans le [registre des identités](#identity-registry) d’IoT Hub. Il est possible d’importer et d’exporter ces données.

## <a name="device-explorer"></a>Explorateur d’appareils
L’[Explorateur d’appareils](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md) est un outil s’exécutant sur Windows, qui vous permet de gérer vos appareils dans la [registre des identités](#identity-registry), ainsi que d’échanger des messages avec vos appareils.

## <a name="device-identities-rest-api"></a>API REST des identités des appareils
L’[API REST des identités des appareils](https://docs.microsoft.com/rest/api/iothub/device-identities-rest) vous permet de gérer vos appareils inscrits dans le [registre des identités](#identity-registry) à l’aide d’une API REST. En règle générale, vous devez utiliser l’un des [Kits de développement logiciel (SDK) de services](#azure-iot-service-sdks) comme indiqué dans les didacticiels concernant IoT Hub.

## <a name="device-identity"></a>Identité d’appareil
L’identité d’appareil est l’identificateur unique attribué à chaque appareil inscrit dans le [registre des identités](#identity-registry).

## <a name="device-management"></a>Gestion des appareils
La gestion des appareils couvre le cycle de vie complet associé à la gestion des appareils dans votre solution IoT, comprenant la planification, l’approvisionnement, la configuration, la surveillance et la mise hors service.

## <a name="device-management-patterns"></a>Modèle de gestion des appareils
[Hub IoT](#iot-hub) permet d’effectuer les opérations courantes de gestion des appareils, dont le redémarrage, les réinitialisations aux paramètres d’usine et les mises à jour de microprogramme.

## <a name="device-messaging-rest-api"></a>API REST de messagerie des appareils
Vous pouvez utiliser l’[API REST de messagerie des appareils](https://docs.microsoft.com/rest/api/iothub/device-messaging-rest-apis) d’un appareil pour envoyer des messages appareil-à-cloud à un IoT Hub, et recevoir des messages [cloud-à-appareil](#cloud-to-device) d’un IoT Hub. En règle générale, vous devez utiliser l’un des [Kits de développement logiciel (SDK) d’appareils](#azure-iot-device-sdks) comme indiqué dans les didacticiels concernant IoT Hub.

## <a name="device-provisioning"></a>Approvisionnement des appareils
L’approvisionnement des appareils est le processus d’ajout des [données d’appareil](#device-data) initiales aux magasins dans votre solution. Pour permettre à un nouvel appareil de se connecter à votre hub, vous devez ajouter un ID et des clés d’appareil au [registre des identités](#identity-registry) d’IoT Hub. Dans le cadre du processus d’approvisionnement, vous devrez peut-être initialiser les données spécifiques à l’appareil dans d’autres magasins de la solution.

## <a name="device-twin"></a>Représentation d’appareil physique
Une [représentation d’appareil](iot-hub-devguide-device-twins.md) est un document JSON contenant des informations d’état d’appareil telles que des métadonnées, des configurations et des conditions. [IoT Hub](#iot-hub) conserve une représentation d’appareil pour chaque appareil que vous configurez dans votre IoT Hub. Les représentations d’appareil vous permettent de synchroniser des [conditions d’appareil](#device-condition) et des configurations entre l’appareil et le serveur principal de solution. Vous pouvez interroger des représentations d’appareil pour localiser des appareils spécifiques et déterminer l’état d’opérations longues.

## <a name="device-twin-queries"></a>Requêtes de représentations d’appareil
Les [requêtes de représentation d’appareil](iot-hub-devguide-query-language.md) utilisent le langage de requête similaire à SQL d’IoT Hub pour extraire des informations de vos représentations d’appareil. Vous pouvez utiliser ce même langage de requête d’IoT Hub pour extraire des informations sur des [travaux](#job) en cours d’exécution dans votre IoT Hub.

## <a name="device-twin-synchronization"></a>Synchronisation de représentation d’appareil
Une synchronisation de représentation d’appareil utilise les [propriétés souhaitées](#desired-properties) de vos représentations d’appareil pour configurer vos appareils et extraire de ceux-ci des [propriétés signalées](#reported-properties) à stocker dans la représentation d’appareil.

## <a name="direct-method"></a>Méthode directe
Une [méthode directe](iot-hub-devguide-direct-methods.md) est un moyen de déclencher une méthode à exécuter sur un appareil en appelant une API sur votre hub IoT.

## <a name="endpoint"></a>Point de terminaison
Un IoT Hub expose plusieurs [points de terminaison](iot-hub-devguide-endpoints.md) qui permettent à vos applications de se connecter à l’IoT Hub. Des points de terminaison côté appareil permettent aux appareils d’effectuer des opérations telles que l’envoi de messages [appareil-à-cloud](#device-to-cloud) et la réception de messages [cloud-à-appareil](#cloud-to-device). Des points de terminaison côté service permettent aux [applications principales](#back-end-app) d’effectuer des opérations telles que la gestion d’[identité d’appareil](#device-identity) et de représentation d’appareil.

## <a name="event-hubs-service"></a>Service Event Hubs
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) est un service d’entrée de données hautement extensible, capable d’ingérer des millions d’événements par seconde. Il vous permet de traiter et d’analyser des volumes conséquents de données produites par vos applications et appareils connectés. Pour une comparaison avec le service IoT Hub, voir [Comparaison entre Azure IoT Hub et Azure Event Hub](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Point de terminaison compatible Event Hub
Pour lire des messages [appareil-à-cloud](#device-to-cloud) envoyés à votre IoT Hub, vous pouvez vous connecter à un point de terminaison sur votre hub, et utiliser n’importe quelle méthode compatible Event Hub pour lire ces messages. Les méthodes compatibles Event Hub comprennent l’utilisation des [Kits de développement logiciel (SDK) Event Hub](../event-hubs/event-hubs-programming-guide.md) et d’[Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Passerelle de champ
Une passerelle de champ assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub), et est généralement déployée localement avec vos appareils. Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Hub ?](iot-hub-what-is-iot-hub.md).

## <a name="free-account"></a>Compte gratuit
Vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/) pour suivre les didacticiels concernant IoT Hub et tester le service IoT Hub (ainsi que d’autres services Azure).

## <a name="gateway"></a>Passerelle
Une passerelle assure la connectivité d’appareils qui ne peuvent pas se connecter directement à [IoT Hub](#iot-hub). Voir aussi [Passerelle de champ](#field-gateway), [Passerelle cloud](#cloud-gateway) et [Passerelle personnalisée](#custom-gateway).

## <a name="identity-registry"></a>Registre des identités
Le [registre des identités](iot-hub-devguide-identity-registry.md) est le composant intégré d’un IoT Hub, qui stocke des informations sur les appareils autorisés à se connecter à un IoT Hub.

## <a name="interactive-message"></a>Message interactif
Un message interactif est un message [cloud-à-appareil](#cloud-to-device) qui déclenche une action immédiate dans le serveur principal de solution. Par exemple, un appareil peut envoyer une alarme concernant une défaillance qui devrait être journalisée automatiquement dans un système CRM.

## <a name="iot-hub"></a>IoT Hub
IoT Hub est un service Azure entièrement géré qui permet des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Hub ?](iot-hub-what-is-iot-hub.md). Votre [abonnement Azure](#subscription) vous permet de créer des IoT Hubs pour gérer vos charges de travail de messagerie IoT.

## <a name="iot-hub-metrics"></a>Métriques d’IoT Hub
Les [métriques d’IoT Hub](iot-hub-metrics.md) fournissent des données sur l’état des IoT Hubs dans votre [abonnement Azure](#subscription). Grâce aux métriques IoT Hub, vous pouvez évaluer l’intégrité globale du service et des appareils connectés à ce dernier. Les métriques IoT Hub peuvent vous aider à voir l’état de votre IoT Hub et à examiner des problèmes constituant des causes premières sans devoir contacter le support Azure.

## <a name="iot-hub-query-language"></a>Langage de requête d’IoT Hub
Le [langage de requête d’IoT Hub](iot-hub-devguide-query-language.md) est un langage similaire à SQL, qui vous permet d’interroger votre [travaux](#job) et représentations d’appareil.

## <a name="iot-hub-resource-provider-rest-api"></a>API REST de fournisseur de ressources IoT Hub
L’[API REST de fournisseur de ressources IoT Hub](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest) vous permet de gérer les IoT Hubs dans votre [abonnement Azure](#subscription), qui effectuent des opérations telles que la création, la mise à jour et la suppression de hubs.

## <a name="iot-suite"></a>IoT Suite
Azure IoT Suite inclut plusieurs services Azure et des solutions préconfigurées. Ces solutions préconfigurées vous permettent de démarrer rapidement avec des implémentations de bout en bout de scénarios IoT courants. Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Suite ?](../iot-suite/iot-suite-overview.md).

## <a name="iothub-explorer"></a>iothub-explorer
L’[iothub-explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) est un outil en ligne de commande multiplateforme. Cet outil vous permet de gérer vos appareils dans le [registre des identités](#identity-registry), d’envoyer et de recevoir des messages et des fichiers sur vos appareils, et de surveiller les opérations de votre IoT Hub.

## <a name="job"></a>Travail
Votre serveur principal de solution peut utiliser des [travaux](iot-hub-devguide-jobs.md) pour planifier et suivre des activités sur un ensemble d’appareils inscrits auprès de votre IoT Hub. Ces activités comprennent la mise à jour des [propriétés souhaitées](#desired-properties) de l’appareil, la mise à jour des [balises](#tags) de représentation d’appareil et l’appel de [méthodes directes](#direct-method). [IoT Hub](#iot-hub) utilise également des travaux pour [importer dans et exporter](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) à partir du [registre des identités](#identity-registry).

## <a name="module"></a>Module
Dans le [Kit de développement logiciel (SDK) de passerelle Azure IoT](iot-hub-linux-gateway-sdk-get-started.md), un [module](iot-hub-linux-gateway-sdk-get-started.md#azure-iot-gateway-sdk-concepts) est un composant qui effectue une tâche spécifique. Les tâches incluent l’ingestion de messages provenant d’appareils, la transformation de messages, ou l’envoi de messages à un IoT Hub. Un répartiteur est chargé du transfert des messages entre les modules. La Kit de développement logiciel (SDK) de passerelle Azure IoT inclut un ensemble d’exemples de modules. Vous pouvez également créer vos propres modules personnalisés.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) est l’un des protocoles de messagerie qu’[IoT Hub](#iot-hub) prend en charge pour communiquer avec des appareils. Pour plus d’informations sur les protocoles de messagerie pris en charge par IoT Hub, consultez [Envoyer et recevoir des messages avec IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Surveillance des opérations
La surveillance des opérations d’[IoT Hub](iot-hub-operations-monitoring.md) vous permet de surveiller l’état des opérations sur votre IoT Hub en temps réel. [IoT Hub](#iot-hub) suit les événements liés à différentes catégories d’opérations. Vous pouvez opter pour l’envoi des événements d’une ou plusieurs catégories à un point de terminaison d’IoT Hub en vue de leur traitement. Vous pouvez surveiller les données des erreurs ou configurer un traitement plus complexe basé sur des modèles de données.

## <a name="physical-device"></a>Appareil physique
Un appareil physique est un appareil réel, tel un Raspberry Pi, qui se connecte à un IoT Hub. Pour des raisons de commodité, bon nombre des didacticiels concernant IoT Hub utilisent des [appareils simulés](#simulated-device) pour vous permettre d’exécuter les exemples sur votre ordinateur local.

## <a name="primary-and-secondary-keys"></a>Clés primaires et secondaires
Lorsque vous vous connectez à un point de terminaison côté appareil ou côté service sur un IoT Hub, votre [chaîne de connexion](#connection-string) inclut la clé vous permettant d’accéder. Lorsque vous ajoutez un appareil au [registre des identités](#identity-registry) ou une [stratégie d’accès partagé](#shared-access-policy) à votre hub, le service génère une clé primaire et une clé secondaire. Le fait de disposer de deux clés vous permet de passer d’une clé à un autre lorsque vous mettez à jour une clé sans perdre l’accès à l’IoT Hub.

## <a name="protocol-gateway"></a>Passerelle de protocole
Une passerelle de protocole est généralement déployée dans le cloud. Elle fournit des services de traduction de protocole pour les appareils qui se connectent à [IoT Hub](#iot-hub). Pour plus d’informations, voir [Qu’est-ce qu’Azure IoT Hub ?](iot-hub-what-is-iot-hub.md).

## <a name="quotas-and-throttling"></a>Quotas et limitation
Divers [quotas](iot-hub-devguide-quotas-throttling.md) s’appliquent à votre utilisation d’[IoT Hub](#iot-hub). Bon nombre d’entre eux varient en fonction du niveau de l’IoT Hub. [IoT Hub](#iot-hub) applique également des [limitations](iot-hub-devguide-quotas-throttling.md) à votre utilisation du service au moment de l’exécution.

## <a name="reported-configuration"></a>Configuration signalée
Dans le contexte d’un [représentation d’appareil](iot-hub-devguide-device-twins.md), une configuration souhaitée fait référence à l’ensemble complet des propriétés et métadonnées dans la représentation d’appareil, qui doivent être signalées par l’appareil au serveur principal de solution.

## <a name="reported-properties"></a>Propriétés signalées
Dans le contexte d’une [représentation d’appareil](iot-hub-devguide-device-twins.md), les propriétés signalées sont une sous-section de la représentation d’appareil qui est utilisée avec des [propriétés souhaitées](#desired-properties) pour synchroniser une configuration ou une condition d’appareil. Les propriétés signalées peuvent être définies uniquement par l’[application d’appareil](#device-app), et peuvent être lues et interrogées par une [application principale](#back-end-app).

## <a name="resource-group"></a>Groupe de ressources
[Azure Resource Manager](#azure-resource-manager) utilise des groupes de ressources pour regrouper des ressources associées. Vous pouvez utiliser un groupe de ressources pour effectuer des opérations simultanément sur toutes les ressources de ce groupe.

## <a name="retry-policy"></a>Stratégie de nouvelle tentative
Vous utilisez une stratégie de nouvelle tentative pour gérer des [erreurs temporaires](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) lorsque vous vous connectez à un service cloud.

## <a name="sasl-plain"></a>SAPL PLAIN
SAPL PLAIN est un protocole que le protocole [AMQP](#advanced-message-queue-protocol) utilise pour transférer des jetons de sécurité.

## <a name="shared-access-signature"></a>Signature d’accès partagé
Les signatures d’accès partagé (SAP) sont des mécanismes d’authentification basés sur des hachages sécurisés SHA-256 ou des URI. Une authentification par SAP comporte deux composants : une _stratégie d’accès partagé_ et une _signature d’accès partagé_ (souvent appelée jeton). Un appareil utilise une SAP pour s’authentifier auprès d’un IoT Hub. Les [applications principales](#back-end-app) utilisent également une SAP pour s’authentifier auprès des points de terminaison côté service sur un IoT Hub. En règle générale, vous incluez le jeton SAP dans la [chaîne de connexion](#connection-string) qu’une application utilise pour établir une connexion à un IoT Hub.

## <a name="shared-access-policy"></a>Stratégie d’accès partagé
Une stratégie d’accès partagé définit les autorisations accordées à toute personne disposant d’une [clé primaire ou secondaire](#primary-and-secondary-keys) valide associée à cette stratégie. Vous pouvez gérer les stratégies d’accès partagé et les clés de votre hub via le [portail](#azure-portal).

## <a name="simulated-device"></a>Appareil simulé
Pour des raisons de commodité, bon nombre des didacticiels concernant IoT Hub utilisent des appareils simulés pour vous permettre d’exécuter les exemples sur votre ordinateur local. En revanche, un [appareil physique](#physical-device) est un appareil réel, tel un Raspberry Pi, qui se connecte à un IoT Hub.

## <a name="solution"></a>Solution
Une _solution_ peut être une solution Visual Studio incluant un ou plusieurs projets. Une _solution_ peut également être une solution IoT incluant des éléments tels que des appareils, [des applications pour appareil](#device-app), un IoT Hub, d’autres services Azure, et des [applications principales](#back-end-app).

## <a name="subscription"></a>Abonnement
L’abonnement est le mode de facturation d’Azure. Chaque ressource ou service Azure que vous créez ou utilisez est associé à un abonnement unique. De nombreux quotas s’appliquent également au niveau d’un abonnement.

## <a name="system-properties"></a>Propriétés système
Dans le contexte d’une [représentation d’appareil](iot-hub-devguide-device-twins.md), les propriétés système sont en lecture seule et incluent des informations sur l’utilisation de l’appareil, telles que l’heure et l’état de connexion de la dernière activité.

## <a name="tags"></a>Balises
Dans le contexte d’une [représentation d’appareil](iot-hub-devguide-device-twins.md), les balises sont des métadonnées d’appareil stockées et récupérées par le serveur principal de solution sous la forme d’un document JSON. Les balises ne sont pas visibles pour les applications sur un appareil.

## <a name="telemetry"></a>Télémétrie
Les appareils collectent des données de télémétrie, telles que la vitesse du vent ou la température, et utilisent des [messages de point de données](#data-point-messages) pour envoyer les données de télémétrie à un IoT Hub.

## <a name="token-service"></a>Service d’émission de jeton
Vous pouvez utiliser un service d’émission de jeton pour implémenter un mécanisme d’authentification pour vos appareils. Il utilise une [stratégie d’accès partagé](#shared-access-policy) IoT Hub avec des autorisations **DeviceConnect** pour créer des jetons *device-scoped*. Ces jetons permettent à un appareil de se connecter à votre IoT Hub. Un appareil utilise un mécanisme d’authentification personnalisé pour s’authentifier auprès du service d’émission de jeton. Si l’appareil est correctement authentifié, le service d’émission de jeton lui fournit un jeton SAP grâce auquel il peut accéder à votre IoT Hub.

## <a name="x509-client-certificate"></a>Certificat client X.509
Un appareil peut utiliser un certificat X.509 pour s’authentifier auprès du [IoT Hub](#iot-hub). L’utilisation d’un certificat X.509 est une alternative à l’utilisation d’un [jeton SAP](#shared-access-signature).


<!--HONumber=Dec16_HO1-->


