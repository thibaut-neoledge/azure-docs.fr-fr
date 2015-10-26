<properties
 pageTitle="Utiliser le portail Azure pour gérer les IoT Hubs | Microsoft Azure"
 description="Une vue d’ensemble de la façon de créer et gérer les concentrateurs IoT Azure via le portail Azure"
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
 ms.date="09/29/2015"
 ms.author="nasing"/>

# Configuration de concentrateurs IoT par le biais du portail Azure

## Introduction :

Cette rubrique décrit comment démarrer avec Azure IoT Hub via le portail Azure, comment trouver les concentrateurs IoT et comment créer et gérer des concentrateurs IoT.

## Où trouver les concentrateurs IoT :

Vous pouvez trouver les concentrateurs IoT à certains endroits.

1. **+ New** : IoT Hub d’Azure est un service IoT qui peut se trouver sous la catégorie « Internet des objets » sous **+ New**, équivalents à d’autres services.

2. Les concentrateurs IoT sont également accessibles via Marketplace en tant que héros de service sous **Internet des objets**.

## Création d’un concentrateur IoT

Vous pouvez créer un concentrateur Azure IoT en utilisant les méthodes répertoriées dans la section précédente.

1. La création d’un concentrateur IoT via l’option **+ New** vous amène au panneau final situé ci-dessous. Les étapes de création du concentrateur IoT via cette méthode et via le marketplace sont identiques.

2. Création d’un concentrateur IoT via le Marketplace : un clic sur **Créer** ouvre un panneau identique au panneau précédent correspondant à l’expérience **+ New**. Plusieurs opérations intervenant dans la création d’un concentrateur IoT figurent dans les sections qui suivent :

### Choisir le nom du concentrateur IoT

Pour créer un concentrateur IoT, l’utilisateur doit nommer le concentrateur. Veuillez noter que le nom doit être unique sur les concentrateurs. Aucune duplication de concentrateurs n’est autorisée sur le serveur principal, et il est donc recommandé de nommer ce concentrateur de la façon la plus unique possible.

### Choisir le niveau de tarification.

Le client peut choisir entre 3 niveaux: **Gratuit**, **Standard 1** et **Standard 2**. Le niveau gratuit permet la connexion de seulement 10 périphériques au concentrateur IoT.

**S1 (basse fréquence)** : IoT Hubs S1 (faible fréquence) est conçu pour les solutions IoT qui ont un grand nombre de périphériques générant de petites quantités de données par périphérique. Chaque unité de l’édition S1 (basse fréquence) permet la connectivité de jusqu’à 500 périphériques ou 50 000 messages par jour sur tous les périphériques connectés.

**S2 (haute fréquence)** : IoT Hub édition S2 (haute fréquence) est conçu pour des solutions IoT dans lequel les périphériques génèrent de grandes quantités de données. Chaque unité de l’édition S2 (haute fréquence) permet la connectivité de jusqu’à 500 périphériques ou jusqu’à 1,5 million de messages par jour sur tous les périphériques connectés.

![][4]

> [AZURE.NOTE]IoT Hub ne permet qu’un concentrateur gratuit par abonnement.

### Unités de concentrateur IoT

Une unité IoT contient 500 périphériques. Le choix du nombre d’unités IoT signifie donc que le nombre total d’appareils pris en charge dans ce concentrateur est le nombre d’unités multiplié par 500. Par exemple, si vous souhaitez que le concentrateur IoT prenne en charge 1 000 périphériques, vous devez choisir 2 unités.

### Les partitions appareil vers cloud et groupe de ressources

Vous pouvez modifier le nombre de partitions pour un concentrateur IoT. Les partitions par défaut sont définies sur 4 ; toutefois, vous pouvez choisir un nombre de partitions différent dans la liste déroulante.

Pour les groupes de ressources, vous n’avez pas besoin de créer explicitement un groupe de ressources vide. Lorsque vous créez une ressource, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe de ressources existant.

![][5]

### Choisir des abonnements

IoT Hub Azure montre automatiquement la liste des abonnements auquel le compte utilisateur est lié. Vous pouvez choisir l’une des options présentes ici pour associer le concentrateur IoT à cet abonnement.

### Choisir l’emplacement

L’option d’emplacement fournit une liste des régions dans lesquelles IoT Hub est proposé. Pour la version préliminaire publique, le concentrateur est proposé sur seulement 3 sites : Est des États-Unis, Europe du Nord et Asie.

### Créer le concentrateur IoT

Lorsque toutes les étapes répertoriées ci-dessus sont terminées, le concentrateur IoT est maintenant prêt à être créé. Un clic sur **Créer** démarre le processus principal de création du concentrateur IoT avec des options spécifiques et le déploie à l’emplacement spécifié.

Veuillez noter que la création du concentrateur IoT peut prendre quelques minutes, car le déploiement principal peut se produire dans les serveurs d’emplacement appropriés.

## Modifier les paramètres du concentrateur IoT

Vous pouvez modifier les paramètres d’un concentrateur IoT existant après sa création. En cliquant et en choisissant le concentrateur IoT, vous ouvrirez la page de paramètres.

![][8]

**Stratégies d’accès partagé** : ce sont les stratégies qui définissent les autorisations pour que les périphériques et services se connectent à IoT Hub. Vous pouvez accéder à ces stratégies en cliquant sur **Stratégies d’accès partagé** sous **Paramètres**. Dans ce panneau, vous pouvez soit modifier les stratégies existantes, soit ajouter une nouvelle stratégie.

### Créer une nouvelle stratégie

- Cliquez sur le bouton **Ajouter** pour ouvrir un panneau dans lequel vous pouvez saisir le nouveau nom de stratégie, et les autorisations que vous voulez associer à la stratégie, comme illustré dans la figure suivante.

	Il existe un certain nombre d’autorisations qui peuvent être associées à ces stratégies partagées. Les deux premières stratégies, **Lecture de registre** et **Écriture de registre**, servent à accorder les autorisations en lecture et en écriture au stockage d’identité de périphérique ou au registre d’identité. Veuillez noter que le choix de l’option en écriture choisit automatiquement l’option en lecture.

 	La stratégie de connexion octroie au groupe de consommateurs des services de connexion au concentrateur IoT, tandis que la connexion de périphérique accorde des autorisations pour le côté périphérique du concentrateur IoT.

- Cliquez sur la stratégie de création pour ajouter la stratégie créée à la liste existante.

![][10]

## Messagerie

Cliquez sur les stratégies **Messagerie** pour affiche une liste des messages de propriétés pour le concentrateur IoT Hub en cours de modification. Il existe deux types principaux de propriétés pouvant être modifiés ou copiés : **Cloud vers appareil** et **Appareil vers cloud**.

- **Cloud pour les paramètres d’appareil** : il contient 2 configurations secondaires : **Cloud vers appareil TTL** (durée de vie) et **Durée de rétention** pour les messages. Lorsque le concentrateur IoT est créé, ces deux paramètres sont créés avec une valeur par défaut d’1 heure. Toutefois, vous pouvez les personnaliser en utilisant les curseurs ou en saisissant les valeurs.

- **Paramètres Appareil vers cloud** : contient plusieurs paramètres secondaires, dont certains sont nommés/affectés lorsque le concentrateur IoT est créé et ne peut être copié vers d’autres paramètres secondaires sont personnalisables. Tous ces éléments sont répertoriés dans la section suivante.

**Partitions** : cette valeur est définie lorsque le concentrateur IoT est créé et peut être modifié via ce paramètre.

**Nom compatible et point de terminaison Event Hub** : lorsque le concentrateur IoT, un Event Hub est créé en interne, et l’utilisateur peut nécessiter l’accès dans certaines circonstances. Ce nom Event Hub et le point de terminaison ne peuvent pas être personnalisés, mais sont disponibles à l’utilisation via le bouton **Copier**.

**Durée de rétention** : la valeur par défaut est 1, mais elle peut être personnalisée sur d’autres valeurs à l’aide de la liste déroulante. Veuillez noter que pour Appareil vers cloud, la valeur est exprimée en jours et non en heures, de même que le paramètre similaire pour Cloud vers appareil.

**Groupes de consommateurs** : les groupes de consommateurs sont similaires aux systèmes de messagerie qui peuvent être utilisés pour extraire des données de façon spécifique afin de connecter des applications ou des services IoT Hub. Chaque concentrateur IoT est créé avec un groupe de consommateurs par défaut. Toutefois, vous pouvez ajouter ou supprimer des groupes de consommateurs de vos concentrateurs IoT.

> [AZURE.NOTE]Le groupe de consommateurs par défaut ne peut être ni modifié ni supprimé.

![][11]

## Tarification et mise à l’échelle

La tarification d’un concentrateur IoT existant peut être modifiée via les paramètres **tarification** avec les exceptions suivantes :

- Dans l’implémentation actuelle, un concentrateur IoT avec une référence gratuite ne peut pas changer de niveau pour une référence payante, ou vice-versa.
- Il ne peut y avoir qu’un niveau IoT Hub gratuit par abonnement.

![][12]

Le déplacement d’un niveau élevé (S2) vers un faible niveau (S1) est autorisé uniquement lorsque le nombre de messages envoyés pour cette journée n’est pas en conflit. Par exemple, si le nombre de messages par jour est supérieur à 50 000, la couche correspondant au niveau IoT Hub ne peut être modifiée de S2 à S1.

## Supprimez IoT Hub

Vous pouvez accéder à IoT Hub en cliquant sur **Parcourir**, puis en choisissant le concentrateur à supprimer. En cliquant sur le bouton **Supprimer** situé sous le nom du concentrateur, vous supprimez le concentrateur.


  [4]: ./media/iot-hub-manage-through-portal/create-iothub.png
  [5]: ./media/iot-hub-manage-through-portal/location1.png
  [8]: ./media/iot-hub-manage-through-portal/portal-settings.png
  [10]: ./media/iot-hub-manage-through-portal/shared-access-policies.png
  [11]: ./media/iot-hub-manage-through-portal/messaging-settings.png
  [12]: ./media/iot-hub-manage-through-portal/pricing-error.png

<!---HONumber=Oct15_HO3-->