<properties
 pageTitle="Gérer vos appareils IoT | Microsoft Azure"
 description="Une vue d’ensemble de l’utilisation d’IoT Hub et IoT Suite pour gérer vos appareils IoT"
 services="iot-hub"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="juanpere"/>

# Gestion de périphérique IoT à l’aide d’Azure IoT Suite et d’Azure IoT Hub

## Introduction
Azure IoT Suite et Azure IoT Hub fournissent les fonctionnalités de base pour la gestion des périphériques de solutions IoT à l’échelle et pour un ensemble varié de périphériques et topologies de périphériques. Dans cet article, la référence à la gestion des périphériques concerne spécifiquement la gestion de périphériques IoT.

Les prestataires et les entreprises de services, et toutes les organisations qui assurent la gestion d’un ensemble d’appareils IoT utilisent les capacités de gestion de l’appareil pour exécuter des processus métiers suivants : inscrire et détecter les périphériques IoT, activer la connectivité, assurer la configuration à distance et mettre à jour les logiciels sur les périphériques. Par exemple, pour la gestion d’appareils au sein d’usines de production ou de champs pétrolifères, des stratégies de configuration et de mise à jour des d’appareils distants sont mises en place, avec des chaînes d’approbation, des exigences d’audit réglementaires, la présence de mesures de protection physique, entre autres.

Lorsque vous activez la gestion de périphériques IoT de votre solution IoT, vous devez prendre en compte les fonctionnalités suivantes et déterminer l’importance de chacune d’elles en fonction des objectifs de votre entreprise :

* Approvisionnement et détection de périphérique : processus permettant d’inscrire un périphérique dans le système.
* Registre de périphérique et modèles de périphérique : méthode selon laquelle les modèles de périphérique représentent l’utilisation structurée de métadonnées dans les relations de périphérique, leur rôle dans le système et les méthodes de validation.
* Gestion des accès au périphérique : méthode selon laquelle les appareils contrôlent l’accès aux ressources depuis les services cloud.
* Contrôle à distance : méthode suivant laquelle les utilisateurs distants accèdent aux périphériques et indiquent qu’ils doivent être changés.
* Administration à distance : processus par lequel un administrateur détermine l’état de santé de l’ensemble des appareils.  
* Configuration à distance : méthode utilisée par les administrateurs pour modifier la configuration de l’appareil.
* Mise à jour à distance de microprogramme et de logiciel : processus permettant aux administrateurs de mettre à jour les microprogrammes et logiciels de l’appareil.

Les sections qui suivent offrent un examen approfondi de chacune des fonctionnalités de gestion de périphérique et fournissent un modèle de haut niveau permettant de mettre en œuvre les fonctionnalités à l’aide d’Azure IoT Hub.

## Approvisionnement et détection de périphérique
La configuration d’un périphérique avec Azure IoT Hub s’effectue via l’API de registre. Lorsque vous enregistrez votre périphérique et fournissez ou recevez une clé, vous pouvez permettre à votre périphérique de se connecter à IoT Hub en utilisant cette clé. Azure IoT Hub communique uniquement avec les appareils inscrits présentant les informations d’identification autorisées. Les administrateurs peuvent désactiver l’accès à un périphérique Azure IoT Hub via le portail d’administration de l’appareil.

Il est possible d’utiliser un processus de démarrage en fonction de la façon dont les périphériques IoT sont fabriqués, configurés et déployés. Vous pouvez créer un service de démarrage dans le cadre de votre solution pour faciliter la connectivité et retarder le processus d’attribution d’un appareil spécifique à Azure IoT Hub. L’emplacement où l’appareil va fonctionner peut être inconnu au moment de sa fabrication. Ceci est un exemple des nombreux flux de travail potentiellement complexes pouvant permettre à Azure IoT Hub de détecter un appareil et de l’intégrer à des processus métiers existants.

Lorsque vous utilisez un service de démarrage, un périphérique IoT démarre et peut finalement fournir un accès à un concentrateur IoT Azure Hub affecté. La demande doit inclure des informations d’identification de démarrage et toutes les autres données de l’appareil autorisé pouvant s’avérer nécessaires. Le service de démarrage doit inscrire le périphérique avec un concentrateur Azure IoT Hub affecté et doit fournir les détails de connectivité à l’appareil demandant un démarrage. IoT Hub fournit les détails de connectivité au périphérique demandant un démarrage. Pour les périphériques autorisés, le service de démarrage doit inscrire l’appareil avec un Azure IoT Hub affecté et fournir les détails de connectivité au périphérique demandant le démarrage. IoT Hub fournit les détails de connectivité au périphérique demandant un démarrage.

## Registre de périphérique, modèles de périphérique et registre de périphérique

L’utilisation de l’expression modèle de périphérique fait référence au modèle incluant des propriétés de périphériques susceptibles d’être lues/écrites par un service cloud et des commandes d’appareil pouvant être exécutées à distance par un service cloud. Dans le service piloté par modèle décrit ci-dessous, l’appareil ne connaît pas le modèle, mais les services cloud peuvent toujours suivre et utiliser des métadonnées sur les périphériques.

Le stockage des informations de périphérique et des métadonnées associées est essentiel pour le système IoT et le rôle du registre du périphérique. Dans les cas où les appareils existants ne pouvant pas être modifiés ou n’utilisent pas de modèle de périphérique, un modèle piloté par service peut activer un service IoT afin d’interagir avec un ensemble d’appareils. Lorsque les appareils fonctionnent en utilisant un modèle défini, le registre de périphérique permet l’affichage cohérent des données de l’appareil au niveau duquel le périphérique sert le maître. Dans ce cas, le service informe l’appareil des modifications souhaitées, qui ne sont valides qu’une fois que l’appareil a confirmé la modification.

### Modèle piloté par service
Lorsqu’un périphérique se connecte à Azure IoT Hub et ne fournit pas de modèle de périphérique, il est important de mettre en œuvre un registre de périphérique permettant d’assurer le suivi des appareils inscrits et des métadonnées qui leur sont associées. Dans ce cas, le registre de périphérique n’est que l’emplacement de stockage des métadonnées de l’appareil. Parmi les métadonnées de l’appareil dont on peut effectuer le suivi se trouvent la topologie logique (par exemple, étage 4, immeuble 109), la fonction de périphérique (par exemple, les capteurs de portail) ou toute autre information de balise.

### Modèle piloté par périphérique
Pour activer votre solution IoT et exploiter les capacités de votre périphérique IoT, les appareils doivent se décrire eux-mêmes dans le cloud une fois qu’ils ont été connectés à Azure IoT Hub. Voici les deux types de modèles d’appareil qui peuvent être utilisés dans une solution IoT :

#### Modèle d’appareil à définition automatique
Un ingénieur d’appareil (ou un développeur utilisant un simulateur d’appareil) utilise le modèle de périphérique défini automatiquement par le processus d’itération de l’appareil lors de la génération de ce dernier. Un ingénieur d’appareil peut commencer par créer un périphérique prenant en charge seulement quelques propriétés et commandes, puis en ajouter par la suite. De même, l’ingénieur d’appareil peut disposer de plusieurs appareils fournissant chacun des fonctionnalités uniques et utilisant le modèle de définition automatique. Alors, il n’a pas à enregistrer la structure du modèle d’appareil. Les importantes variantes existant dans les modèles automatiquement définis peuvent s’avérer problématiques lors de la mise à l’échelle sur un grand nombre d’appareils.

#### Modèle de périphérique prédéfini
Un déploiement de production IoT fonctionnant avec des contraintes réseau et d’alimentation/traitement tire de nombreux avantages d’un modèle de périphérique prédéfini utilisant un minimum de capacité de traitement et d’alimentation électrique. De même, un trafic réseau minimal permet aux périphériques de transmettre via des réseaux hétérogènes (Wi-Fi, 2G/3G /4G, BLE, Sat, etc.), en particulier lorsque vous utilisez une infrastructure limitée et onéreuse (par exemple, le Satellite). Lorsqu’il implémente un modèle prédéfini, un ingénieur d’appareil peut envoyer des informations de périphérique codées sur un ou deux octets servant de clé dans le modèle de périphérique prédéfini. La simplicité de cette approche débouche sur un ou deux ordres de grandeur par rapport au modèle d’appareil défini par l’utilisateur.

### Solution préconfigurée de surveillance à distance et modèle de périphérique
La solution de surveillance à distance d’Azure IoT Suite met en œuvre un modèle de périphérique défini par l’utilisateur. L’utilisation de ce modèle permet une itération rapide au fur et à mesure que vous définissez et modifiez les capacités de votre périphérique.

Vous trouverez le code à l’endroit où la solution préconfigurée de surveillance à distance crée un objet de périphérique et l’envoie au service dans le répertoire Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs. En examinant les méthodes SendDeviceInfo et GetDeviceInfo, vous pouvez voir comment le modèle de périphérique autodescriptif est créé et envoyé à Azure IoT Hub.

Vous trouverez le code à l’endroit où le service cloud traite le modèle de périphérique associé aux événements dans le répertoire EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs. La méthode nommée ProcessJToken est au cœur de l’action sur les messages associés au modèle de périphérique envoyé côté service de la solution préconfigurée.

Après la réception du message associé au modèle de périphérique, les méthodes UpdateDeviceFromSimulatedDeviceInfoPacketAsync et UpdateDeviceFromRealDeviceInfoPacketAsync à DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs sont en charge de la mise à jour du registre de périphériques. L’API de registre de périphériques de la solution préconfigurée se trouve dans DeviceManagement/Web/WebApiControllers/DeviceApiController.cs.

### Modèles de périphérique de passerelle de champ
Les passerelles de champ servent souvent à activer la connectivité et la conversion de protocole des appareils qui ne peuvent pas ou ne doivent pas se connecter directement à Internet. Si le périphérique que vous créez est une passerelle de champ, il peut représenter les périphériques connectés via cette dernière (la passerelle de champ) dans toutes les interactions avec Azure IoT Hub. En votre qualité de fabricant de passerelle de champ, il vous incombe de mettre en œuvre la conversion entre les protocoles de l’appareil périphériques et les protocoles pris en charge par le service IoT. Si vous souhaitez permettre à votre passerelle de champ de se connecter aux périphériques BLE (Bluetooth faible énergie), vous devez mettre en face l’interface BLE sur les périphériques et l’interface à Azure IoT Hub.

## Gestion d’accès au périphérique
La solution Azure IoT Suite préconfigurée contrôle l’accès à différents aspects du périphérique, notamment aux droits d’accès en lecture et en écriture pour les propriétés du périphérique et l’exécution des droits pour les commandes du périphérique. Dans les solutions préconfigurées, cet accès est contrôlé par le biais d’Azure Active Directory (AAD) dans le portail d’administration de périphérique. Vous pouvez activer la sécurité d’accès basée sur les rôles en étendant l’utilisation d’AAD dans la solution préconfigurée.

## Contrôle à distance
Le contrôle à distance n’est pas un scénario de solutions préconfigurées Azure IoT Suite. Toutefois, voici une analyse de haut niveau des options qui vous permettent d’activer le contrôle à distance dans votre solution IoT.

Dans les scénarios informatiques, la commande à distance est souvent utilisée pour assister les utilisateurs distants, ou pour configurer à distance des serveurs distants. Dans les scénarios IoT, la plupart des périphériques n’ont pas d’utilisateur engagé et par conséquent, la commande à distance est utilisée dans les scénarios de configuration et de diagnostic à distance. La commande à distance peut être mise en œuvre selon deux modèles différents :

* La connexion directe permettant d’activer le contrôle à distance via le raccordement direct à un appareil (par exemple, SSH sur Linux, Bureau à distance sur Windows ou par le biais des outils de débogage à distance), vous devez être en mesure de créer une connexion à l’appareil. Étant donné les risques que prend un périphérique exposé à l’Internet ouvert, il est conseillé d’utiliser un service de relais (par exemple, un service de relais Service Bus Azure) pour activer la connexion et le trafic vers/depuis ce périphérique. Comme une connexion relais est une connexion sortante de l’appareil, elle permet de limiter la surface d’attaque de ports TCP ouverts sur le périphérique.

* Le contrôle à distance de la commande de périphérique met à profit la connexion et le canal de communication établis entre le périphérique et Azure IoT Hub. Pour activer le contrôle à distance des commandes de périphérique, vous devez mettre en œuvre les exigences qui suivent :
  * Le logiciel qui s’exécute sur l’appareil doit informer le service IoT que les commandes de l’appareil sont disponibles sur ce dernier. Il est généralement défini en tant que modèle de périphérique.
  * Le logiciel qui s’exécute sur l’appareil doit mettre en œuvre les commandes de contrôle à distance. Ces commandes de périphérique doivent suivre une demande (du service IoT au périphérique) et un modèle de réponse (du périphérique au service IoT). L’exécution de commandes de l’appareil peut affecter l’état de l’appareil et le nouvel état doit être mis à jour dans le registre de périphérique.

Lorsque le périphérique constitue l’unité de stockage principale de la configuration et de l’état du périphérique, le registre de périphérique utilise un modèle cohérent et les modifications de l’état de l’appareil doivent être transmises au registre du périphérique. La mise à jour de l’état de registre du périphérique peut être imposée par un service IoT de l’appareil. Ce dernier peut aussi mettre automatiquement à jour le service lorsqu’il détecte une modification de l’état du système. La mise à jour automatique du service à partir de l’appareil peut générer un trafic réseau et accroître le taux d’utilisation du processeur du périphérique et l’alimentation disponible.

## Gestion et surveillance à distance
Comme la plupart des périphériques IoT font la différence entre les rôles d’exploitation et les rôles de gestion utilisateur, l’administration distante permet aux administrateurs de surveiller l’état des appareils, de configurer le flux de données dans les processus et applications (par exemple, CRM, ERP, systèmes de maintenance, etc.) et de mettre à jour l’état ou la configuration des appareils en utilisant leurs commandes.

Les solutions préconfigurées d’Azure IoT Suite fournissent un Site Web Azure qui active un point de départ pour une expérience d’administration de périphérique que vous pouvez étendre aux scénarios dans votre solution IoT verticale.

Les administrateurs définissent les limites de l’état du périphérique en tant que fonction des données opérationnelles de l’appareil (déplacement généralement rapide) et les métadonnées de périphérique (déplacement généralement lent). Une option permettant d’activer le système d’alerte d’état de périphérique utilise les règles mises en œuvre par le biais d’un moteur de traitement de flux de données (par exemple, Stream Analytics Azure) contrairement à la stratégie d’interrogation de règles.

## Configuration distante
La modification à distance de la configuration d’un périphérique peut être importante à différents stades du cycle de vie d’un périphérique : configuration, diagnostic ou intégration aux processus métiers. Les modifications de configuration à distance sont activées via une combinaison de modèle de périphérique et la possibilité de mettre à jour l’état de l’instance de modèle d’un périphérique du service IoT.

Dans la solution de surveillance à distance préconfigurée, l’appareil indique qu’il prend en charge les commandes d’appareils suivantes pour la configuration à distance :

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Ces commandes de périphérique ne s’affichent pas comme disponibles dans le portail d’administration de la solution préconfigurée. En effet, il s’agit de commandes de périphérique et elles sont exécutées à distance par des éléments spécifiques du portail. Une fois la commande reçue par l’appareil, un accusé de réception est envoyé au service par l’appareil. Après le traitement de la commande, l’appareil envoie une réponse informant le service de la réussite ou de l’échec de la commande par un code d’erreur. À ce stade, l’état souhaité pour cet appareil est confirmé sur ce dernier et le registre de périphérique est mis à jour.

## Mise à jour à distance des microprogrammes et logiciels
La mise à jour distante des microprogrammes et logiciels est une fonctionnalité importante d’un système IoT, car elle permet à des périphériques d’exécuter le logiciel le plus récent et le plus sûr. La mise à jour à distance des microprogrammes et des logiciels sur un périphérique constitue un exemple de processus à long terme distribué qui implique généralement des processus métiers. Par exemple, la mise à jour du microprogramme sur un périphérique contrôlant une pompe à carburant haute pression peut nécessiter des interventions dans les systèmes adjacents pour le réacheminer le carburant au moment de l’exécution et de la vérification de la mise à jour.

### Analyse de la mise à jour du microprogramme
Voici un moyen possible de mettre en œuvre une mise à jour de microprogramme susceptible de répondre à vos besoins métiers. L’objectif de cet exemple est de souligner les considérations du processus, et non d’imposer une implémentation spécifique. La conception de votre mise à jour doit mettre en œuvre les facteurs métiers ou techniques en dehors du domaine d’application de cet article.

Les mises à jour du périphérique sont initiées au niveau du service IoT et les périphériques sont informés à un moment déterminé de la commande de périphérique. Lorsqu’un périphérique prend explicitement en charge la mise à jour distante d’un microprogramme ou d’un logiciel, le service IoT doit fournir les commandes de mise à jour distante en se basant sur le processus et les stratégies métiers. À la réception de la commande du périphérique à mettre à jour, le périphérique doit télécharger le package de mise à jour, le déployer, redémarrer le système (dans le cas d’une mise à jour de microprogramme) ou vérifier que le nouveau microprogramme ou le nouveau logiciel fonctionne correctement. Tout au long de ce processus en plusieurs étapes, le périphérique doit informer le service IoT de l’état du périphérique au fur et à mesure qu’il exécute les différentes opérations.

La livraison du package de mise à jour peut s’effectuer par le biais d’un service de stockage de type stockage Azure ou via un réseau de diffusion de contenu (CDN). La vérification de l’intégrité du package téléchargé avant l’application de la mise à jour du microprogramme ou du logiciel est importante pour garantir que le package est bien issu de la source attendue.

Après avoir effectué une mise à jour du microprogramme, le périphérique doit être en mesure de vérifier et d’identifier un état correct. Si le périphérique ne prend pas l’état correct, le logiciel sur le périphérique doit lancer une restauration à un état correct connu. L’état connu doit être le dernier état connu ou une image de microprogramme de périphérique connu sous le nom « Golden State » dans une partition de stockage.

<!---HONumber=Oct15_HO3-->