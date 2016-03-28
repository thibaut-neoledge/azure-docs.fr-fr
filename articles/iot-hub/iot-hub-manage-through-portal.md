<properties
	 pageTitle="Utilisation du portail Azure pour gérer IoT Hub | Microsoft Azure"
	 description="Une vue d'ensemble de la façon de créer et gérer les hubs IoT Azure via le portail Azure"
	 services="iot-hub"
	 documentationCenter=""
	 authors="nasing"
	 manager="timlt"
	 editor=""/>

<tags
	 ms.service="iot-hub"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.tgt_pltfrm="na"
	 ms.workload="na"
	 ms.date="03/14/2016"
	 ms.author="nasing"/>

# Configuration de hubs IoT par le biais du portail Azure

## Introduction

Cet article décrit comment démarrer avec Azure IoT Hub via le portail Azure, comment trouver les hubs IoT et comment créer et gérer des hubs IoT.

## Où trouver les hubs IoT

Vous pouvez trouver les hubs IoT à certains endroits.

1. **+ New** : **Azure IoT Hub** est un service IoT qui se trouve dans la catégorie **Internet des objets** sous **+ New**, comme d'autres services.

2. Les hubs IoT sont également accessibles via Marketplace en tant que héros de service sous **Internet des objets**.

## Créer un hub IoT

Vous pouvez créer un hub IoT à l'aide des méthodes suivantes.

1. La création d'un hub IoT via l'option **+ New** vous amène au panneau illustré dans la capture d'écran suivante. Les étapes de création du hub IoT via cette méthode et via le marketplace sont identiques.

2. Création d'un hub IoT via Marketplace : un clic sur **Créer** ouvre un panneau identique au panneau précédent correspondant à l'expérience **+ New**. Plusieurs opérations intervenant dans la création d'un hub IoT figurent dans les sections qui suivent.

### Choisir le nom du hub IoT

Pour créer un hub IoT, vous devez nommer le hub. Veuillez noter que le nom doit être unique parmi les hubs. Aucune duplication de hubs n'est autorisée sur le serveur principal et il est donc recommandé de nommer ce hub de la façon la plus unique possible.

### Choisir le niveau tarifaire.

Vous pouvez choisir entre 3 niveaux : **Gratuit**, **Standard 1** et **Standard 2**. Le niveau gratuit permet la connexion de seulement 500 appareils au IoT Hub, avec jusqu’à 8 000 messages par jour.

**S1 (basse fréquence)** : l'édition IoT Hubs S1 (faible fréquence) est conçue pour les solutions IoT qui ont un grand nombre d’appareils générant de petites quantités de données par appareil. Chaque unité de l’édition S1 (fréquence faible) permet de transmettre au maximum 400 000 messages par jour sur l’ensemble des appareils connectés.

**S2 (haute fréquence)** : l'édition IoT Hub S2 (haute fréquence) est conçue pour des solutions IoT dans lequel les appareils génèrent de grandes quantités de données. Chaque unité de l’édition S2 (fréquence élevée) permet de transmettre au maximum 6 millions de messages par jour sur l’ensemble des appareils connectés.

![][4]

> [AZURE.NOTE] IoT Hub ne permet qu’un hub gratuit par abonnement.

### Unités de hub IoT

Une unité IoT Hub comprend un certain nombre de messages par jour. Le choix du nombre d’unités IoT signifie donc que le nombre total de messages pris en charge dans ce hub correspond au nombre d’unités multiplié par le nombre de messages par jour pour ce niveau. Par exemple, si vous souhaitez qu’IoT Hub prenne en charge l’arrivée de 700 000 messages, vous choisissez 2 unités de niveau S1.

### Les partitions appareil-à-cloud et groupe de ressources

Vous pouvez modifier le nombre de partitions pour un hub IoT. Les partitions par défaut sont définies sur 4 ; toutefois, vous pouvez choisir un nombre de partitions différent dans la liste déroulante.

Pour les groupes de ressources, vous n’avez pas besoin de créer explicitement un groupe de ressources vide. Lorsque vous créez une ressource, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant.

![][5]

### Choisir des abonnements

IoT Hub Azure montre automatiquement la liste des abonnements auquel le compte utilisateur est lié. Vous pouvez choisir l’une des options présentes ici pour associer le hub IoT à cet abonnement.

### Choisir l’emplacement

L’option d’emplacement fournit une liste des régions dans lesquelles IoT Hub est proposé. IoT Hub peut être déployé aux emplacements suivants : Est des États-Unis, Ouest des États-Unis, Europe du Nord, Europe de l’Ouest, Asie de l’Est et Asie du Sud-Est.

### Créer le hub IoT

Lorsque toutes les étapes précédentes sont terminées, le hub IoT est prêt à être créé. Cliquez sur **Créer** pour démarrer le processus principal de création du hub IoT avec des options spécifiques et le déployer à l'emplacement spécifié.

Veuillez noter que la création du hub IoT peut prendre quelques minutes, car le déploiement principal peut se produire dans les serveurs d’emplacement appropriés.

## Modifier les paramètres du hub IoT

Vous pouvez modifier les paramètres d’un hub IoT existant après sa création. Cliquez sur le nom du hub IoT pour ouvrir la page Paramètres.

![][8]

**Stratégies d'accès partagé** : ce sont les stratégies qui définissent les autorisations pour que les appareils et services se connectent au hub IoT. Vous pouvez accéder à ces stratégies en cliquant sur **Stratégies d'accès partagé** sous **Paramètres**. Dans ce panneau, vous pouvez soit modifier les stratégies existantes, soit ajouter une nouvelle stratégie.

### Créer une nouvelle stratégie

- Cliquez sur **Ajouter** pour ouvrir un panneau dans lequel vous pouvez saisir le nouveau nom de stratégie, et les autorisations que vous voulez associer à la stratégie, comme illustré dans la figure suivante.

	Il existe plusieurs autorisations qui peuvent être associées à ces stratégies partagées. Les deux premières stratégies, **Lecture de registre** et **Écriture de registre**, accordent les autorisations en lecture et en écriture au stockage d'identité d’appareil ou au registre d'identité. Veuillez noter que le choix de l'option en écriture choisit automatiquement l'option en lecture également.

 	La stratégie de connexion octroie au groupe de consommateurs des services de connexion au hub IoT, tandis que la connexion d’appareil accorde des autorisations pour le côté appareil du hub IoT.

- Cliquez sur **Créer** pour ajouter la stratégie créée à la liste existante.

![][10]

## Messagerie

Cliquez sur les stratégies **Messagerie** pour afficher une liste des messages de propriétés pour le hub IoT en cours de modification. Il existe deux types principaux de propriétés que vous pouvez modifier ou copier : **cloud-à-appareil** et **appareil-à-cloud**.

- Paramètres **cloud-à-appareil** : contiennent 2 configurations secondaires : **cloud-à-appareil TTL** (durée de vie) et **Durée de rétention** pour les messages. Lorsque le hub IoT est créé, ces deux paramètres sont créés avec une valeur par défaut d’1 heure. Toutefois, vous pouvez les personnaliser en utilisant les curseurs ou en saisissant les valeurs.

- Paramètres **Appareil-à-cloud** : contiennent plusieurs paramètres secondaires, dont certains sont nommés/affectés lorsque le hub IoT est créé et ne peut être copié vers d'autres paramètres secondaires qui sont personnalisables. Ces paramètres sont répertoriés dans la section suivante.

**Partitions** : cette valeur est définie lorsque le hub IoT est créé et peut être modifié via ce paramètre.

**Nom compatible et point de terminaison de hub d'événements** : lorsque le hub IoT est créé, un hub d'événements est créé en interne auquel l’utilisateur peut devoir accéder dans certaines circonstances. Ce nom et ce point de terminaison de hub d’événements ne peuvent pas être personnalisés, mais peuvent être utilisés via le bouton **Copier**.

**Durée de rétention** : la valeur par défaut est 1, mais elle peut être personnalisée sur d’autres valeurs à l’aide de la liste déroulante. Veuillez noter que pour appareil-à-cloud, la valeur est exprimée en jours et non en heures, de même que le paramètre similaire pour cloud-à-appareil.

**Groupes de consommateurs** : les groupes de consommateurs sont similaires aux systèmes de messagerie qui peuvent être utilisés pour extraire des données de façon spécifique afin de connecter des applications ou des services IoT Hub. Chaque hub IoT est créé avec un groupe de consommateurs par défaut. Toutefois, vous pouvez ajouter ou supprimer des groupes de consommateurs de vos hubs IoT.

> [AZURE.NOTE] Le groupe de consommateurs par défaut ne peut être ni modifié ni supprimé.

![][11]

## Tarification et mise à l'échelle

La tarification d'un hub IoT existant peut être modifiée via les paramètres de **tarification** avec les exceptions suivantes :

- Dans l’implémentation actuelle, un hub IoT avec une référence gratuite ne peut pas changer de niveau pour une référence payante, ou vice-versa.
- Il ne peut y avoir qu’un niveau gratuit de hub IoT par abonnement.

![][12]

Le déplacement d’un niveau élevé (S2) vers un faible niveau (S1) est autorisé uniquement lorsque le nombre de messages envoyés pour cette journée n’est pas en conflit. Par exemple, si le nombre de messages par jour est supérieur à 400 000, le niveau correspondant au IoT Hub ne peut pas passer de S2 à S1.

## Supprimez IoT Hub

Vous pouvez accéder au hub IoT en cliquant sur **Parcourir**, puis en choisissant le hub à supprimer. Cliquez sur le bouton **Supprimer** situé sous le nom du hub pour supprimer le hub.

## Étapes suivantes

Suivez ces liens pour en savoir plus sur Azure IoT Hub :

- [Prise en main d'IoT Hub (didacticiel)][lnk-get-started]
- [Qu’est-ce qu’Azure IoT Hub ?][]


  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[Qu’est-ce qu’Azure IoT Hub ?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0316_2016-->