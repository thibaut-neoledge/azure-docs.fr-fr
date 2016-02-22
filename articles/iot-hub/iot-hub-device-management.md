<properties
 pageTitle="Gérer des périphériques IoT | Microsoft Azure"
 description="Une vue d’ensemble de l’utilisation d’IoT Hub et IoT Suite pour gérer vos appareils IoT"
 services="iot-hub,iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="01/21/2016"
 ms.author="dobett"/>

# Gestion de périphérique IoT à l’aide d’Azure IoT Suite et d’Azure IoT Hub

[Azure IoT Suite][lnk-iot-suite] et Azure IoT Hub fournissent les fonctionnalités de base qui permettent la gestion des périphériques de solutions IoT à l’échelle et d’un ensemble varié de périphériques et topologies de périphériques. Dans cet article, les références à la gestion des périphériques concernent spécifiquement la gestion de périphériques IoT.

## Introduction

Les prestataires et les entreprises de services, et toutes les organisations qui assurent la gestion d’un ensemble de périphériques IoT utilisent les capacités de gestion des périphériques pour exécuter les processus métiers suivants :

* Inscrire et détecter les périphériques IoT.
* Activez la connectivité.
* Assurer la configuration à distance et mettre à jour les logiciels sur les périphériques. Par exemple, pour la gestion de périphériques au sein d’usines de production ou de champs pétrolifères, des stratégies de configuration et de mise à jour des périphériques distants sont mises en place, avec des chaînes d’approbation, des exigences d’audit réglementaires, la présence de mesures de protection physique, entre autres.

Lorsque vous activez la gestion de périphériques IoT de votre solution IoT, vous devez prendre en compte les fonctionnalités suivantes et déterminer l’importance de chacune d’elles en fonction des objectifs de votre entreprise :

* **[Approvisionnement et détection de périphérique](#device-provisioning-and-discovery)** : processus permettant d’inscrire un périphérique dans le système.
* **[Registre de périphérique et modèles de périphérique](#device-registry-and-device-models)** : manière dont les modèles de périphérique représentent l’utilisation structurée de métadonnées dans les relations de périphérique, leurs rôles dans le système et les méthodes de validation.
* **[Gestion d’accès au périphérique](#device-access-management)** : manière dont les périphériques contrôlent l’accès aux ressources depuis les services cloud.
* **[Contrôle à distance](#remote-control)** : manière dont les utilisateurs distants accèdent aux périphériques et indiquent qu’ils doivent être changés.
* **[Administration à distance](#remote-administration-and-monitoring)** : processus par lequel un administrateur détermine l’état de santé de l’ensemble des appareils.  
* **[Configuration à distance](#remote-configuration)** : méthode utilisée par les administrateurs pour modifier la configuration du périphérique.
* **[Mise à jour à distance de microprogramme et de logiciel](#remote-firmware-and-software-update)** : processus permettant aux administrateurs de mettre à jour les microprogrammes et logiciels du périphérique.

Les sections qui suivent offrent un examen approfondi de chacune des fonctionnalités de gestion de périphérique et décrivent un modèle de haut niveau permettant de mettre en œuvre les fonctionnalités à l’aide d’Azure IoT Hub.

## Approvisionnement et détection de périphérique

Vous configurez un périphérique avec Azure IoT Hub à l’aide de l’API de registre. Lorsque vous enregistrez votre périphérique et fournissez ou recevez une clé, vous pouvez permettre à votre périphérique de se connecter à IoT Hub en utilisant cette clé. Azure IoT Hub communique uniquement avec les périphériques inscrits présentant les informations d’identification autorisées. Les administrateurs peuvent désactiver l’accès à un périphérique Azure IoT Hub via le portail d’administration de l’appareil.

Il est possible d’utiliser un processus de démarrage en fonction de la façon dont les périphériques IoT sont fabriqués, configurés et déployés. Vous pouvez créer un service de démarrage dans le cadre de votre solution pour faciliter la connectivité et retarder le processus d’attribution d’un périphérique à un IoT Hub spécifique. L’emplacement où l’appareil va fonctionner peut être inconnu au moment de sa fabrication. Ceci est un exemple des nombreux flux de travail potentiellement complexes pouvant permettre à Azure IoT Hub de détecter un appareil et de l’intégrer à des processus métiers existants.

Lorsque vous utilisez un service de démarrage, un périphérique IoT démarre et émet une requête, qui peut finalement fournir un accès à un Azure IoT Hub affecté. La demande doit inclure des informations d’identification de démarrage et toutes les autres données du périphérique autorisé pouvant s’avérer nécessaires. Pour les périphériques autorisés, le service de démarrage doit inscrire l’appareil avec un Azure IoT Hub affecté et fournir les détails de connectivité au périphérique demandant le démarrage. IoT Hub fournit les détails de connectivité au périphérique demandant un démarrage.

## Registre de périphérique et modèles de périphérique

L’expression *modèle de périphérique* fait référence au modèle incluant des propriétés de périphériques susceptibles d’être lues ou écrites par un service cloud. Elle inclut également des commandes de périphérique pouvant être exécutées à distance par un service cloud. Dans le modèle piloté par service décrit dans la section suivante, le périphérique ne connaît pas le modèle, mais les services cloud peuvent toujours suivre et utiliser des métadonnées pour les périphériques.

Le stockage des informations de périphérique et des métadonnées associées est essentiel pour le système IoT et le rôle du registre du périphérique. Cela est particulièrement vrai pour les périphériques existants ne pouvant pas être modifiés ou pour les périphériques n’utilisant pas de modèle de périphérique. Un modèle piloté par service peut activer un service IoT afin d’interagir avec un ensemble de périphériques. Lorsque les périphériques fonctionnent en utilisant un modèle défini, le registre de périphérique permet l’affichage cohérent des données du périphérique au niveau duquel le périphérique sert le maître. Dans ce cas, le service informe l’appareil des modifications souhaitées, qui ne sont valides qu’une fois que l’appareil a confirmé la modification.

### Modèle piloté par service

Si un périphérique se connecte à Azure IoT Hub et ne fournit pas de modèle de périphérique, il est important de mettre en œuvre un registre de périphérique permettant d’assurer le suivi des périphériques inscrits et des métadonnées qui leur sont associées. Dans ce cas, le registre de périphérique est l’unique emplacement de stockage des métadonnées du périphérique. Parmi les métadonnées du périphérique dont on peut effectuer le suivi se trouvent la topologie logique (par exemple, étage 4, immeuble 109), la fonction de périphérique (par exemple, les capteurs de portail) ou toute autre information de balise.

### Modèle piloté par périphérique

Pour activer votre solution IoT et exploiter les capacités de votre périphérique IoT, les périphériques doivent se décrire eux-mêmes dans le cloud une fois qu’ils ont été connectés à IoT Hub. Voici les deux types de modèles d’appareil qui peuvent être utilisés dans une solution IoT :

#### Modèle d’appareil à définition automatique

Un ingénieur de périphérique (ou un développeur utilisant un simulateur de périphérique) utilise le modèle de périphérique autodéfini par l’itération du périphérique lors de la génération de celui-ci. Un ingénieur de périphérique peut commencer par créer un périphérique prenant en charge seulement quelques propriétés et commandes, puis en ajouter par la suite. De même, l’ingénieur de périphérique peut disposer de plusieurs périphériques fournissant chacun des fonctionnalités uniques et utilisant le modèle autodéfini. Dans ce cas, l’ingénieur n’a pas à enregistrer la structure du modèle de périphérique. Les importantes variantes existant dans les modèles automatiquement définis peuvent s’avérer problématiques lors de la mise à l’échelle sur de nombreux appareils.

#### Modèle de périphérique prédéfini

Un déploiement IoT de production fonctionnant avec des contraintes réseau et d’alimentation ou de traitement tire de nombreux avantages d’un modèle de périphérique prédéfini permettant de réduire capacité de traitement et d’alimentation électrique. De même, un trafic réseau minimal permet aux périphériques de transmettre via des réseaux hétérogènes (Wi-Fi, 2G/3G/4G, BLE, Sat, etc.), en particulier lorsque vous utilisez une infrastructure limitée et onéreuse (par exemple, le satellite). Lorsqu’il implémente un modèle prédéfini, un ingénieur d’appareil peut envoyer des informations de périphérique codées sur un ou deux octets servant de clé dans le modèle de périphérique prédéfini. La simplicité de cette approche entraîne une efficacité accrue sur un ou deux ordres de grandeur par rapport au modèle de périphérique défini par l’utilisateur.

### Solution préconfigurée de surveillance à distance et modèle de périphérique

La [solution préconfigurée de surveillance à distance][lnk-remote-monitoring] d’Azure IoT Suite met en œuvre un modèle de périphérique défini par l’utilisateur. L’utilisation de ce modèle permet une itération rapide au fur et à mesure que vous définissez et modifiez les capacités de votre périphérique.

Vous trouverez le code source pour cette solution préconfigurée dans le référentiel GitHub [azure-iot-solution][lnk-azure-iot-solution].

Le code dans lequel la solution préconfigurée de surveillance à distance crée un objet périphérique et l’envoie au service se trouve dans le fichier **Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs**. Dans les méthodes **SendDeviceInfo** et **GetDeviceInfo**, vous pouvez voir comment le modèle de périphérique autodescriptif est créé et envoyé à Azure IoT Hub.

Le code dans lequel le service cloud traite les événements associés au modèle de périphérique se trouve dans le fichier **EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs**. Une grande partie du travail impliqué dans l’action sur les messages associés au modèle de périphérique qui sont envoyés côté service de la solution préconfigurée survient dans la méthode **ProcessJToken**.

Après la réception du message associé au modèle de périphérique, les méthodes **UpdateDeviceFromSimulatedDeviceInfoPacketAsync** et **UpdateDeviceFromRealDeviceInfoPacketAsync** du fichier **DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs** sont en charge de la mise à jour du registre de périphériques. Les API de registre de périphérique de la solution préconfigurée de surveillance à distance se trouvent dans le fichier **DeviceManagement/Web/WebApiControllers/DeviceApiController.cs**.

### Modèles de périphérique de passerelle de champ

Les passerelles de champ servent souvent à activer la connectivité et la conversion de protocole des appareils qui ne peuvent pas ou ne doivent pas se connecter directement à Internet. Si le périphérique que vous créez est une passerelle de champ, il peut représenter les périphériques connectés via la passerelle de champ dans toutes les interactions avec Azure IoT Hub. En votre qualité de fabricant de passerelle de champ, il vous incombe de mettre en œuvre la conversion entre les protocoles de périphériques et les protocoles pris en charge par le service IoT. Si vous souhaitez permettre à votre passerelle de champ de se connecter aux périphériques Bluetooth Low Energy (BLE), vous devez mettre en œuvre l’interface BLE sur les périphériques et sur Azure IoT Hub.

## Gestion d’accès au périphérique

La solution IoT Suite préconfigurée contrôle l’accès à différents aspects du périphérique, notamment aux droits d’accès en lecture et en écriture pour les propriétés du périphérique et l’exécution des droits pour les commandes du périphérique. Dans les solutions préconfigurées, cet accès est contrôlé par le biais d’Azure Active Directory (AAD) dans le portail d’administration de périphérique. Vous pouvez activer la sécurité d’accès basée sur les rôles en étendant l’utilisation d’AAD dans la solution préconfigurée.

## Contrôle à distance

Le contrôle à distance n’est pas un scénario de solutions préconfigurées Azure IoT Suite. Toutefois, voici une analyse de haut niveau des options pour l’activation du contrôle à distance dans votre solution IoT.

Dans les scénarios informatiques, la commande à distance est souvent utilisée pour assister les utilisateurs distants, ou pour configurer à distance des serveurs distants. Dans les scénarios IoT, la plupart des périphériques n’ont pas d’utilisateur engagé et par conséquent, la commande à distance est utilisée dans les scénarios de configuration et de diagnostic à distance. La commande à distance peut être mise en œuvre selon deux modèles différents :

* **Connexion directe** Pour activer le contrôle à distance via le raccordement direct à un périphérique (par exemple, SSH sur Linux, Bureau à distance sur Windows ou par le biais des outils de débogage à distance), vous devez être en mesure de créer une connexion au périphérique. Étant donné les risques que prend un périphérique exposé à Internet ouvert, nous vous conseillons d’utiliser un service de relais (par exemple, le [service de relais Service Bus][service-bus-relay] Azure) pour activer la connexion et acheminer le trafic. Étant donné qu’une connexion relais est une connexion sortante du périphérique, elle permet de limiter la surface d’attaque de ports TCP ouverts sur le périphérique.

* **Commande de périphérique** Le contrôle à distance des commandes de périphérique met à profit la connexion et le canal de communication établis entre le périphérique et Azure IoT Hub. Pour activer le contrôle à distance des commandes de périphérique, vous devez satisfaire les exigences suivantes :
  * Le logiciel qui s’exécute sur le périphérique doit informer le service IoT que les commandes du périphérique sont disponibles sur ce dernier. Il est généralement défini en tant que modèle de périphérique.
  * Le logiciel qui s’exécute sur le périphérique doit mettre en œuvre les commandes de contrôle à distance. Ces commandes de périphérique doivent suivre la demande (du service IoT au périphérique) et un modèle de réponse (du périphérique au service IoT). L’exécution de commandes du périphérique peut affecter l’état du périphérique et le nouvel état doit être mis à jour dans le registre de périphérique.

Lorsque le périphérique constitue l’unité de stockage principale de la configuration et de l’état du périphérique, le registre de périphérique utilise un modèle cohérent et les modifications de l’état du périphérique doivent être transmises au registre du périphérique. La mise à jour de l’état de registre du périphérique peut être forcée par une demande du service IoT au périphérique. Ce dernier peut aussi mettre automatiquement à jour le service lorsqu’il détecte une modification de l’état du système. La mise à jour automatique du service à partir du périphérique peut générer un trafic réseau important et accroître le taux d’utilisation du processeur du périphérique et l’alimentation disponible.

## Gestion et surveillance à distance

La plupart des périphériques IoT différencient les rôles opérationnels et les rôles de gestion utilisateur. L’administration distante est la méthode grâce à laquelle les administrateurs peuvent surveiller l’état des périphériques, configurer le flux de données des périphériques dans les processus et applications (par exemple, CRM, ERP, systèmes de maintenance, etc.) et mettre à jour l’état ou la configuration des périphériques en utilisant leurs commandes.

Les solutions préconfigurées d’Azure IoT Suite incluent un site web Azure qui crée un point de départ pour une expérience d’administration de périphérique que vous pouvez étendre aux scénarios dans votre solution IoT verticale.

Les administrateurs peuvent définir l’état des périphériques en consultant les données opérationnelles du périphérique (déplacement généralement rapide) et les métadonnées de périphérique (déplacement généralement lent). Pour activer le système d’alerte d’état de périphérique, vous pouvez utiliser des règles mises en œuvre par le biais d’un moteur de traitement de flux de données (par exemple, [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)), contrairement à la stratégie d’interrogation de règles.

## Configuration distante

La modification à distance de la configuration d’un périphérique peut être importante à différents stades du cycle de vie du périphérique : configuration, diagnostic ou intégration aux processus métiers. Les modifications de configuration à distance sont activées via une combinaison de modèle de périphérique et la possibilité de mettre à jour à distance l’état d’une instance de modèle d’un périphérique du service IoT.

Dans la solution de surveillance à distance préconfigurée, le périphérique indique qu’il prend en charge les commandes suivantes pour la configuration à distance :

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Ces commandes de périphérique n’apparaissent pas comme disponibles dans le portail d’administration de la solution préconfigurée. En effet, elles sont exécutées à distance par le portail dans des circonstances particulières. Lorsqu’un périphérique reçoit une commande de périphérique, l’appareil envoie un accusé de réception au service. Après le traitement de la commande, le périphérique envoie une réponse informant le service de la réussite (ou de l’échec de la commande par un code d’erreur). À ce stade, l’état souhaité pour cet appareil est confirmé sur ce dernier et le registre de périphérique est mis à jour.

## Mise à jour à distance des microprogrammes et logiciels

Les mises à jour distantes des microprogrammes et logiciels sont une fonctionnalité importante d’un système IoT. Elles permettent à des périphériques d’exécuter le logiciel le plus récent et le plus sûr. La mise à jour à distance des microprogrammes et des logiciels sur un périphérique constitue un exemple de processus à long terme distribué qui implique généralement des interactions avec d’autres processus métiers. Par exemple, la mise à jour du microprogramme sur un périphérique contrôlant une pompe à carburant haute pression peut nécessiter des interventions dans les autres systèmes pour réacheminer le carburant au moment de l’exécution et de la vérification de la mise à jour.

### Analyse des mises à jour du microprogramme

Voici un moyen possible de mettre en œuvre des mises à jour de microprogramme susceptibles de répondre à vos besoins métiers. L’objectif de cet exemple est de souligner les considérations du processus, et non d’imposer une implémentation spécifique. La conception de vos exigences de mise à jour pour tenir compte des facteurs métiers ou techniques est en dehors du domaine d’application de cet article.

Les mises à jour du périphérique sont initiées au niveau du service IoT et les périphériques sont informés à un moment déterminé de la commande de périphérique. Lorsqu’un périphérique prend explicitement en charge la mise à jour distante d’un microprogramme ou d’un logiciel, le service IoT doit fournir les commandes de mise à jour distante en se basant sur le processus et les stratégies métiers. Lors de la réception de la commande de périphérique pour mettre à jour, l’appareil doit :

1. Télécharger et déployer le package de mise à jour.
2. Redémarrer l’ordinateur pour déployer la nouvelle la configuration (dans le cas d’une mise à jour du microprogramme) ou démarrer le nouveau package logiciel.
3. Vérifier que le nouveau microprogramme ou le logiciel s’exécute comme prévu.

Tout au long de ce processus en plusieurs étapes, le périphérique doit informer le service IoT de l’état du périphérique au fur et à mesure qu’il exécute les opérations.

Un service de stockage de données tel qu’Azure Storage ou un réseau de distribution de contenu (CDN) peut fournir le package de mise à jour. Il est important de vérifier que le package téléchargé est intègre et bien issu de la source attendue avant l’application de la mise à jour du microprogramme ou du logiciel.

Après avoir effectué une mise à jour du microprogramme, le périphérique doit être en mesure de vérifier et d’identifier un état correct. Si le périphérique ne prend pas l’état correct, le logiciel sur le périphérique doit lancer une restauration à un état correct connu. L’état correct connu peut être le dernier état correct connu ou une image de microprogramme de périphérique connu sous le nom *golden state* dans une partition de stockage.

## Étapes suivantes

Pour en savoir plus sur Azure IoT Hub, consultez ces liens :

* [Prise en main d’IoT Hub][]
* [Qu’est-ce qu’Azure IoT Hub ?][]
* [Connexion de votre appareil][]

[Prise en main d’IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Qu’est-ce qu’Azure IoT Hub ?]: iot-hub-what-is-iot-hub.md
[service-bus-relay]: ../service-bus/service-bus-relay-overview.md
[Connexion de votre appareil]: https://azure.microsoft.com/develop/iot/
[lnk-azure-iot-solution]: https://github.com/Azure/azure-iot-solution
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-remote-monitoring]: ../iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md

<!---HONumber=AcomDC_0211_2016-->