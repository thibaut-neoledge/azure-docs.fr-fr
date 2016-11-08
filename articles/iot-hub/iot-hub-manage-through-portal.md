---
title: Utilisation du portail Azure pour gérer IoT Hub | Microsoft Docs
description: Une vue d'ensemble de la façon de créer et gérer les hubs IoT Azure via le portail Azure
services: iot-hub
documentationcenter: ''
author: nasing
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: nasing

---
# Configuration de hubs IoT par le biais du portail Azure
## Introduction
Cet article décrit comment démarrer avec Azure IoT Hub via le portail Azure, comment trouver les hubs IoT et comment créer et gérer des hubs IoT.

## Où trouver les hubs IoT
Il existe divers emplacements où vous pouvez trouver des hubs IoT.

1. **+ New** : **Azure IoT Hub** est un service IoT qui se trouve dans la catégorie **Internet des objets** sous **+ New**, comme d'autres services.
2. Les hubs IoT sont également accessibles via Marketplace en tant que héros de service sous **Internet des objets**.

## Créer un hub IoT
Vous pouvez créer un hub IoT à l'aide des méthodes suivantes.

1. La création d'un hub IoT via l'option **+ New** vous amène au panneau illustré dans la capture d'écran suivante. Les étapes de création du hub IoT via cette méthode et via le marketplace sont identiques.
2. Création d'un hub IoT via Marketplace : un clic sur **Créer** ouvre un panneau identique au panneau précédent correspondant à l'expérience **+ New**. Les sections suivantes répertorient les différentes étapes nécessaire à la création d’un hub IoT.

### Choisir le nom du hub IoT
Pour créer un hub IoT, vous devez nommer le hub. Veuillez noter que le nom doit être unique parmi les hubs. Aucune duplication de hubs n'est autorisée sur le serveur principal et il est donc recommandé de nommer ce hub de la façon la plus unique possible.

### Choisir le niveau tarifaire.
Vous pouvez choisir entre quatre niveaux : **Gratuit**, **Standard S1**, **Standard S2** et**Standard S3**. Le niveau gratuit permet la connexion de seulement 500 appareils au IoT Hub, avec jusqu’à 8 000 messages par jour.

**Standard S1** : l’édition IoT Hubs S1 est conçue pour les solutions IoT qui ont un grand nombre d’appareils générant de petites quantités de données par appareil. Chaque unité de l’édition S1 permet de transmettre au maximum 400 000 messages par jour sur l’ensemble des appareils connectés.

**Standard S2** : l’édition IoT Hub S2 est conçue pour des solutions IoT dans lesquelles les appareils génèrent de grandes quantités de données. Chaque unité de l’édition S2 permet de transmettre au maximum 6 millions de messages par jour sur l’ensemble des appareils connectés.

**Standard S3** : l’édition IoT Hub S3 est conçue pour les solutions IoT générant de gros volumes de données. Chaque unité de l’édition S3 permet de transmettre au maximum 300 millions de messages par jour sur l’ensemble des appareils connectés.

![][4]

> [!NOTE]
> IoT Hub ne permet qu’un hub gratuit par abonnement.
> 
> 

### Unités de hub IoT
Une unité IoT Hub comprend un certain nombre de messages par jour. Le choix du nombre d’unités IoT signifie donc que le nombre total de messages pris en charge dans ce hub correspond au nombre d’unités multiplié par le nombre de messages par jour pour ce niveau. Par exemple, si vous souhaitez qu’IoT Hub prenne en charge l’arrivée de 700 000 messages, vous choisissez deux unités de niveau S1.

### Les partitions appareil-à-cloud et groupe de ressources
Vous pouvez modifier le nombre de partitions pour un hub IoT. Les partitions par défaut sont définies sur 4 ; toutefois, vous pouvez choisir un nombre de partitions différent dans la liste déroulante.

Pour les groupes de ressources, vous n’avez pas besoin de créer explicitement un groupe de ressources vide. Lorsque vous créez une ressource, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant.

![][5]

### Choisir des abonnements
IoT Hub Azure montre automatiquement la liste des abonnements auquel le compte utilisateur est lié. Vous pouvez choisir l’une des options présentes ici pour associer le hub IoT à cet abonnement.

### Choisir l’emplacement
L’option d’emplacement fournit une liste des régions dans lesquelles IoT Hub est proposé. IoT Hub est disponible pour le déploiement dans les régions suivantes : Est de l’Australie, Sud-Est de l’Australie, Asie de l’Est, Asie du Sud-Est, Europe du Nord, Europe de l’Ouest, Est du Japon, Ouest du Japon, Est des États-Unis, Ouest des États-Unis.

### Créer le hub IoT
Lorsque toutes les étapes précédentes sont terminées, le hub IoT est prêt à être créé. Cliquez sur **Créer** pour démarrer le processus principal de création du concentrateur IoT avec des options spécifiques et le déployer à l'emplacement spécifié.

Notez que la création du hub IoT peut prendre quelques minutes, car le déploiement principal peut se produire dans les serveurs d’emplacement appropriés.

## Modifier les paramètres du hub IoT
Vous pouvez modifier les paramètres d’un hub IoT existant après sa création, dans le panneau IoT Hub.

![][8]

**Stratégies d'accès partagé** : ces stratégies définissent les autorisations pour que les périphériques et services se connectent au hub IoT. Vous pouvez accéder à ces stratégies en cliquant sur **Stratégies d'accès partagé** sous **Général**. Dans ce panneau, vous pouvez soit modifier les stratégies existantes, soit ajouter une nouvelle stratégie.

### Créer une nouvelle stratégie
* Cliquez sur **Ajouter** pour ouvrir un panneau dans lequel vous pouvez saisir le nouveau nom de stratégie, et les autorisations que vous voulez associer à la stratégie, comme illustré dans la figure suivante.
  
    Il existe plusieurs autorisations qui peuvent être associées à ces stratégies partagées. Les deux premières stratégies, **Lecture de registre** et **Écriture de registre**, accordent les autorisations en lecture et en écriture au stockage d’identité d’appareil ou au registre d’identité. Notez que le choix de l'option en écriture choisit automatiquement l'option en lecture également.
  
     La stratégie de **connexion de service** octroie des autorisations aux points de terminaison côté cloud, tels que le groupe de consommateurs pour les services connectés à IoT Hub. La stratégie de **connexion d’appareil** accorde des autorisations pour envoyer et recevoir des messages sur les points de terminaison côté périphérique du hub IoT.
* Cliquez sur **Créer** pour ajouter la stratégie créée à la liste existante.

![][10]

## Messagerie
Cliquez **Messagerie** pour afficher une liste des messages de propriétés pour le concentrateur IoT en cours de modification. Il existe deux types principaux de propriétés que vous pouvez modifier ou copier : **Cloud vers appareil** et **Appareil vers cloud**.

* Paramètres **Cloud vers appareil** : ce paramètre contient deux configurations secondaires : **Cloud vers appareil TTL** (durée de vie) et **Durée de rétention** pour les messages. Lorsque le hub IoT est créé, ces deux paramètres sont créés avec une valeur par défaut d’une heure. Pour ajuster ces valeurs, utilisez les curseurs ou tapez les valeurs.
* Paramètres **Appareil vers cloud** : ce paramètre contient plusieurs paramètres secondaires, dont certains sont nommés/affectés lorsque le concentrateur IoT est créé et ne peut être copié vers d'autres paramètres secondaires qui sont personnalisables. Ces paramètres sont répertoriés dans la section suivante.

**Partitions** : cette valeur est définie lorsque le concentrateur IoT est créé et peut être modifié via ce paramètre.

**Nom compatible et point de terminaison Concentrateur d'événements** : lorsque le concentrateur IoT est créé, un Concentrateur d'événements est créé en interne, et vous pouvez nécessiter l'accès dans certaines circonstances. Ce nom Event Hub et le point de terminaison ne peuvent pas être personnalisés, mais sont disponibles à l’utilisation via le bouton **Copier**.

**Durée de rétention** : la valeur par défaut est un jour, mais elle peut être personnalisée sur d’autres valeurs à l’aide de la liste déroulante. Notez que pour appareil-à-cloud, la valeur est exprimée en jours et non en heures, de même que le paramètre similaire pour cloud-à-appareil.

**Groupes de consommateurs** : les groupes de consommateurs sont similaires aux systèmes de messagerie qui peuvent être utilisés pour extraire des données de façon spécifique afin de connecter des applications ou des services IoT Hub. Chaque hub IoT est créé avec un groupe de consommateurs par défaut. Toutefois, vous pouvez ajouter ou supprimer des groupes de consommateurs de vos hubs IoT.

> [!NOTE]
> Le groupe de consommateurs par défaut ne peut être ni modifié ni supprimé.
> 
> 

![][11]

## Chargement de fichiers
Pour utiliser la fonctionnalité de chargement de fichiers dans IoT Hub, vous devez d’abord associer un compte Azure Storage à votre concentrateur. Sélectionnez les paramètres de **chargement de fichiers** pour afficher une liste de propriétés de fichier pour l’IoT Hub en cours de modification.

**Conteneur de stockage** : utilisez le portail pour sélectionner un conteneur d’objets blob dans un compte de stockage de votre abonnement actuel à associer à votre IoT Hub. Si nécessaire, vous pouvez créer un compte de stockage dans le panneau **Comptes de stockage** et un conteneur d’objets blob dans le panneau **Conteneurs**. IoT Hub génère automatiquement des URI SAS avec des autorisations d’écriture pour ce conteneur d’objets blob pour les appareils à utiliser lorsqu’ils chargent des fichiers.

![][14]

**Recevoir des notifications pour les fichiers chargés** : activez ou désactivez les notifications de chargement de fichiers.

**SAS TTL** : ce paramètre est la durée de vie des URI de signature d’accès partagé renvoyés à l’appareil par IoT Hub. Il est défini par défaut sur une heure, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

**Durée de vie par défaut des paramètres de notification de fichiers** : la durée de vie d’une notification de chargement avant son expiration. Il est défini par défaut sur un jour, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

**Nombre maximal de remises de notifications de fichier** : le nombre de tentatives de remise d’une notification de chargement de fichier par l’IoT Hub. Il est défini par défaut sur 10, mais il peut être personnalisé avec d’autres valeurs à l’aide du curseur.

![][13]

## Tarification et mise à l'échelle
La tarification d'un concentrateur IoT existant peut être modifiée via les paramètres de **tarification** avec les exceptions suivantes :

* Dans l’implémentation actuelle, un hub IoT avec une référence gratuite ne peut pas changer de niveau pour une référence payante, ou vice-versa.
* Il ne peut y avoir qu’un niveau gratuit de hub IoT par abonnement.

![][12]

Le déplacement d’un niveau élevé (S2 ou S3) vers un niveau inférieur (S1 ou S2) est autorisé uniquement lorsque le nombre de messages envoyés pour cette journée n’est pas en conflit. Par exemple, si le nombre de messages par jour dépasse 400 000, le niveau pour IoT Hub peut être modifié, mais si vous changez pour le niveau S1, le concentrateur est limité pour cette journée.

## Supprimez IoT Hub
Vous pouvez accéder au concentrateur IoT Hub en cliquant sur **Parcourir**, puis en choisissant le concentrateur à supprimer. Cliquez sur le bouton **Supprimer** situé sous le nom du concentrateur pour supprimer le concentrateur.

## Étapes suivantes
Suivez ces liens pour en savoir plus sur la gestion de Azure IoT Hub :

* [Gestion en bloc des appareils IoT][lnk-bulk]
* [Mesures d’utilisation][lnk-metrics]
* [Surveillance des opérations][lnk-monitor]
* [Gérer l’accès à IoT Hub][lnk-itpro]

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Conception de votre solution][lnk-design]
* [Guide du développeur][lnk-devguide]
* [Exploration de la gestion des appareils à l’aide de l’exemple d’interface utilisateur][lnk-dmui]
* [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]
* [Sécuriser votre solution IoT de bout en bout][lnk-securing]

[4]: ./media/iot-hub-manage-through-portal/create-iothub.png
[5]: ./media/iot-hub-manage-through-portal/location1.png
[8]: ./media/iot-hub-manage-through-portal/portal-settings.png
[10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
[12]: ./media/iot-hub-manage-through-portal/pricing-error.png
[13]: ./media/iot-hub-manage-through-portal/file-upload-settings.png
[14]: ./media/iot-hub-manage-through-portal/file-upload-container-selection.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0817_2016-->