<properties
 pageTitle="Présentation de la bibliothèque de gestion des appareils | Microsoft Azure"
 description="Bibliothèque cliente de gestion des appareils (DM) Azure IoT Hub"
 services="iot-hub"
 documentationCenter=""
 authors="CarlosAlayo"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="carlosa"/>

# Présentation de la bibliothèque cliente de gestion des appareils (DM) Azure IoT Hub

## Vue d'ensemble

La bibliothèque cliente de gestion des appareils Azure IoT Hub vous permet de gérer vos appareils IoT avec Azure IoT Hub. Vous pouvez notamment effectuer un redémarrage, rétablir les paramètres d’usine et mettre à jour le microprogramme. Actuellement, nous fournissons une bibliothèque C indépendante de la plateforme, mais nous proposerons bientôt la prise en charge d’autres langages. Comme l’indique la [vue d’ensemble de la gestion des appareils Azure IoT Hub][lnk-dm-overview], la gestion des appareils IoT Hub repose sur trois concepts clés :

- Représentations d’appareil physique
- Travaux d’appareil
- Requêtes d’appareil

Si vous ne connaissez pas ces concepts, consultez la vue d’ensemble avant de continuer. Tous ces concepts sont étroitement liés à la bibliothèque cliente.

La bibliothèque cliente DM a deux grandes responsabilités dans la gestion des appareils :

- Synchronisation des propriétés sur l’appareil physique avec celles de la représentation d’appareil physique correspondante dans IoT Hub
- Orchestration des travaux d’appareil envoyés par IoT Hub à l’appareil

Les propriétés de l’appareil physique (par exemple, niveau de batterie et numéro de série) sont régulièrement synchronisées avec la représentation d’appareil physique cloud de sorte qu’IoT Hub dispose en permanence d’une vue à jour de chacun des appareils physiques (tant que l’appareil est connecté).

Le principal moyen d’interaction du service avec l’appareil physique réside dans les travaux d’appareil et la représentation d’appareil physique. Les types de travaux suivants sont fournis dans IoT Hub. La bibliothèque cliente DM orchestre une grande partie des travaux d’appareil, mais c’est à vous, en tant que développeur, d’implémenter les rappels nécessaires sur votre appareil pour chaque type de travail.

1.	**Mise à jour du microprogramme** : met à jour le microprogramme (ou l’image de système d’exploitation) sur l’appareil physique
2.	**Redémarrage** : redémarre l’appareil physique
3.	**Réinitialisation aux paramètres d’usine** : restaure une image de sauvegarde d’usine, stockée sur l’appareil, correspondant au microprogramme (ou à l’image de système d’exploitation) de l’appareil physique
4.	**Mise à jour de la configuration** : configure l’agent client IoT Hub en cours d’exécution sur l’appareil physique
5.	**Lecture de la propriété de l’appareil** : obtient la dernière valeur d’une propriété de l’appareil sur l’appareil physique
6.	**Écriture de la propriété de l’appareil** : modifie une propriété de l’appareil sur l’appareil physique

Les sections suivantes présentent l’architecture de la bibliothèque cliente et vous expliquent comment implémenter les différents objets d’appareil sur votre appareil.

## Principes de conception et concepts fonctionnels de la bibliothèque cliente de gestion des appareils
La conception de la bibliothèque cliente DM s’articule autour de la portabilité et de l’intégration multiplateforme. Les choix de conception suivants ont donc été faits :

1.	Utilisation du protocole standard LWM2M sur COAP pour permettre l’extensibilité d’une gamme d’appareils variés.
2.	Écriture en langage ANSI C99 pour faciliter la portabilité vers un large éventail de plateformes.
3.	Sécurisation par le biais de l’authentification TCP/TLS et Azure IoT Hub (jetons SAP) pour une utilisation haute sécurité.
4.	Conception basée sur le projet OSS [Eclipse Wakaama][lnk-Wakaama] pour exploiter le code existant et contribuer à la communauté.

### Concepts LWM2M appropriés
Nous avons choisi le standard LWM2M afin de permettre l’extensibilité d’une gamme d’appareils variés. Pour simplifier l’expérience de développement, nous avons fait abstraction de la majeure partie du protocole. Toutefois, il est important de bien comprendre les principes fondamentaux de la bibliothèque, notamment son modèle de données et le mode de transmission des données.

#### Objets et ressources : modèle de données dans LWM2M
Le modèle de données LWM2M présente le concept d’objets et de ressources :

- Les **objets** désignent un ensemble d’entités fonctionnelles cohérentes dans le système, comme les mises à jour du microprogramme et de l’appareil.
- Les **ressources** désignent des attributs ou des actions inclus dans ces objets, comme les informations de niveau de batterie et l’action de redémarrage.

Ce modèle offre deux relations un-à-plusieurs :

- **Appareil et objets** : chaque appareil peut avoir plusieurs objets. Par exemple, un appareil Contoso doit avoir un « objet d’appareil » et un « objet de serveur » (les objets seront décrits en détail dans la section suivante).
- **Objets et ressources** : chaque objet peut avoir plusieurs ressources. Par exemple, un objet peut contenir les ressources de mise à jour du microprogramme d’un appareil Contoso, comme l’URI du package où est stockée la nouvelle image.

#### Modèle observer/informer : mode de transmission des données selon le standard LWM2M
Outre ces concepts, il est important de bien comprendre le flux des données entre l’appareil et le service. Pour ce faire, le standard LWM2M définit le modèle « observer/informer ». Quand l’appareil physique se connecte au service, IoT Hub lance « l’observation » sur les propriétés de l’appareil sélectionné. Ensuite, l’appareil physique informe le service des modifications apportées aux propriétés de l’appareil.

Dans notre bibliothèque cliente, nous avons choisi le modèle observer/informer pour envoyer les données de gestion des appareils de l’appareil vers IoT Hub. Ce modèle est contrôlé par deux paramètres :

- **Période minimale** : période pendant laquelle l’appareil retarde l’envoi d’une mise à jour pour une propriété observée. Ce paramètre est défini sur 5 minutes. Par conséquent, l’appareil envoie une mise à jour pour une propriété observée au maximum toutes les 5 minutes, même si la valeur change plus fréquemment. Autrement dit, si la propriété change toutes les minutes, le service ne voit que la dernière modification effectuée à la 5e minute.

- **Période maximale** : période pendant laquelle l’appareil, que la propriété observée change ou non, envoie une mise à jour pour la valeur de la propriété. Ce paramètre est défini sur 6 heures. Par conséquent, l’appareil envoie une mise à jour pour la valeur d’une propriété observée au minimum toutes les 6 heures, même si la valeur ne change pas.

> [AZURE.NOTE]  Seule exception à ces paramètres : la période minimale destinée aux propriétés utilisées pour le travail de mise à jour du microprogramme est définie sur 30 secondes. En effet, ces propriétés changent très souvent au cours de l’exécution du travail. Nous avons donc réduit la période minimale afin d’accélérer le processus de mise à jour.

## Architecture et fonctions de la bibliothèque cliente
Comme indiqué dans la vue d’ensemble, les bibliothèques clientes prennent en charge deux fonctions principales :

- Synchronisation de l’appareil physique avec la représentation d’appareil physique correspondante dans IoT Hub.
- Orchestration des travaux d’appareil envoyés par IoT Hub à l’appareil.

Cette section nous permet d’explorer chacune de ces fonctions.

### Synchronisation de l’appareil physique avec la représentation d’appareil physique correspondante
La bibliothèque cliente utilise le modèle observer/informer pour maintenir la représentation d’appareil physique correspondante à jour. Rappelez-vous que la représentation d’appareil physique désigne la représentation de l’appareil physique côté service. La synchronisation s’effectue conformément au processus suivant :

1. L’appareil s’inscrit auprès du service, généralement au moment de l’initialisation. Exemple : « Je suis un appareil, mon ID d’appareil est Contoso avec jeton d’accès Y. »
2. Le service observe les ressources sur les objets. Exemple : « Tenez-moi informé du niveau de la batterie de mon appareil Contoso. »
3. L’appareil informe le service de la valeur de la ressource. La fréquence des notifications dépend de la période minimale et de la période maximale. Exemple : « 17:00 niveau de batterie à 99 % ; 17:05 niveau de batterie à 90 % ; etc. »

### Orchestration des travaux d’appareil : fonctionnement conjoint de la représentation d’appareil physique et des travaux d’appareil
Pour agir sur un appareil physique, le service doit trouver la représentation d’appareil physique correspondante. Pour cela, il faut interroger les propriétés ou rechercher un ID spécifique. Si vous connaissez l’ID de la représentation d’appareil physique (et donc la correspondance avec l’appareil physique), le service peut démarrer un travail d’appareil sur l’appareil physique.

Le travail d’appareil désigne un ensemble de commandes représentant le processus spécifique à effectuer (par exemple, étapes de mise à jour du microprogramme). Un travail d’appareil peut se diviser en plusieurs étapes :

1.	La représentation d’appareil physique a des propriétés qui définissent l’état d’un travail d’appareil.
2.	Pour passer d’un processus à l’autre dans le travail d’appareil, les propriétés de la représentation d’appareil physique doivent afficher une certaine valeur. Par conséquent, l’appareil physique doit définir la propriété adéquate pour être synchronisé avec la représentation d’appareil physique et permettre une progression.

Cette séquence se répète si le processus se divise en plusieurs étapes (par exemple, pendant une mise à jour du microprogramme, la représentation d’appareil physique modifie plusieurs fois ses propriétés lors des différentes étapes avant de considérer que le travail est terminé).

## Directives d’implémentation de la gestion des appareils dans votre client
Dans les sections précédentes, nous avons découvert les fonctions des bibliothèques clientes de gestion des appareils, leurs principes de conception et leurs liens avec le standard LWM2M. Nous allons à présent expliquer comment tout s’intègre dans l’exécution.

De manière générale, la bibliothèque cliente gère la communication entre l’appareil et le service, si bien qu’il ne reste plus qu’à implémenter la logique spécifique de l’appareil. Cette étape se fait en deux phases :

1.	**Implémentation des détails spécifiques de l’appareil** : cela implique d’écrire la logique spécifique de l’appareil pour effectuer les actions requises par le service (par exemple, téléchargement du package du microprogramme ) dans les rappels appropriés. Vous trouverez ci-dessous un exemple de rappel pour le package de mise à jour du microprogramme. Les rappels sont présents dans les fichiers .c du dossier [ici][lnk-github1].

            object_firmwareupdate *obj = get_firmwareupdate_object(0);
            obj->firmwareupdate_packageuri_write_callback =     start_firmware_download;
            // platform specific code
            obj->firmwareupdate_update_execute_callback = start_firmware_update;
            //platform specific code


2.	**Notification à la bibliothèque cliente lors des modifications des propriétés** : pour cela, vous devez appeler les fonctions set correspondantes dans le fichier .h du dossier [ici][lnk-github2].

        IOTHUB_CLIENT_RESULT set_firmwareupdate_state(uint16_t instanceId, int value);

### Objets à implémenter dans la bibliothèque cliente DM

Nous venons d’expliquer comment implémenter la logique spécifique de l’appareil pour effectuer les travaux d’appareil. À présent, nous allons évoquer les objets que vous pouvez utiliser.

Certains de ces objets sont nécessaires, ce qui signifie que vous devez implémenter la logique spécifique à l’appareil pour que ce dernier fasse partie de la gestion des appareils IoT Hub. D’autres sont facultatifs : vous pouvez donc faire votre choix en fonction des besoins de votre service (par exemple, vous pouvez choisir de ne pas effectuer les mises à jour du microprogramme avec IoT Hub). Voici une description de chacun de ces objets :

- **Objet d’appareil (obligatoire)** : fournit des informations spécifiques de l’appareil, comme les informations relatives au fabricant, le numéro de modèle, le numéro de série et l’heure de l’appareil. Le service peut lire ces informations et parfois les mettre à jour. Il définit également deux actions que le service peut effectuer sur un appareil : le redémarrage et la réinitialisation aux paramètres d’usine.
- **Objet de serveur (obligatoire)** : contient les paramètres de connexion à IoT Hub, comme la durée de vie de l’inscription et la liaison de transport. Le service peut uniquement lire ces informations.
- **Objet de configuration (facultatif)** : contient des informations de configuration définies par l’utilisateur et qui peuvent être interrogées sur l’appareil ou transférées vers un appareil à partir du service dans l’optique de faciliter la configuration de l’appareil. Le service peut lire et mettre à jour ces informations.
- **Objet de mise à jour du microprogramme (facultatif)** : fournit une action de mise à jour du microprogramme que le service peut appeler. Il fournit également des informations, comme l’emplacement du package du microprogramme et l’état d’une opération de mise à jour du microprogramme en cours.

Chacun de ces objets est associé à un ensemble de ressources (n’oubliez pas la relation un-à-plusieurs). La liste des objets LWM2M et de toutes les ressources associées qui sont pris en charge dans la gestion des appareils Azure IoT Hub se trouve à la fin de cet article.

> [AZURE.NOTE] Les propriétés d’appareil personnalisées, les instances de ressource multiples et les instances d’objet multiples ne sont pas prises en charge dans la version actuelle du système.

### Exemple complet

Vous trouverez ci-dessous un diagramme récapitulatif. Sur la droite, en bleu, vous pouvez voir les différents composants de la bibliothèque cliente de gestion des appareils : objets, gestionnaires d’événements et méthodes de notification. Le côté gauche, en vert, représente la logique dont vous avez besoin pour écrire au niveau de l’application de l’appareil. La bibliothèque cliente se connecte à la logique d’application avec IoT Hub, ce qui garantit une gestion adaptée de la communication et de l’orchestration.

La figure ci-dessous présente les composants de la bibliothèque cliente DM.

![][img-library-overview]

## Étapes suivantes : Expérience pratique
Cet article a abordé les principes de base de l’utilisation de la bibliothèque cliente de gestion des appareils IoT Hub pour C.

Pour mettre ces connaissances en pratique, vous pouvez accéder aux ressources suivantes :

- Exemple de mise à jour du microprogramme Intel Edison : exemple comportant les fonctionnalités de gestion des appareils compatibles avec un appareil Intel Edison. Consultez la page [iotdm\_edison\_sample][lnk-edison-sample].
- Exemple de simulations d’appareil : exemple d’appareil indépendant de la plateforme, qui s’exécute sur des appareils Linux et Windows. Consultez la page [iotdm\_simple\_sample][lnk-simple-sample].
- Pour en savoir plus sur les objets LWM2M, consultez le [registre de ressources et d’objets OMA LWM2M][lnk-oma]

## Annexe : Objets et ressources LWM2M actuellement pris en charge

### Objet d’appareil

| Nom de la ressource | Opération à distance autorisée sur la ressource | Type | Plage et unités | Description |
|-----------------|--------------------------------------|---------|-----------------|-------------|
| Fabricant | Lire | String | | Nom du fabricant. |
| ModelNumber | Lire | String | | Identificateur de modèle (chaîne spécifique du fabricant). |
| DeviceType | Lire | String | | Type d’appareil (chaîne spécifique du fabricant)<br/>Remarque : cela correspond à l’API côté serveur **SystemPropertyNames.DeviceDescription**. |
| SerialNumber | Lire | String | | Numéro de série de l’appareil. |
| FirmwareVersion | Lire | String | | Version actuelle du microprogramme installé sur l’appareil. |
| HardwareVersion | Lire | String | | Version actuelle du matériel de l’appareil. |
| Reboot | Exécuter | | | Permet de redémarrer l’appareil. |
| FactoryReset | Exécuter | | | Permet de rétablir les paramètres d’usine de l’appareil afin que l’appareil ait la même configuration qu’au moment de son déploiement initial. |
| CurrentTime | Lecture<br/>Écriture | Time | | Horodatage UNIX actuel de l’appareil. Le client est chargé d’augmenter cette valeur de temps à chaque seconde qui s’écoule.<br/>Le serveur DM peut écrire dans cette ressource pour synchroniser le client avec l’heure du serveur. |
| UTCOffset | Lecture<br/>Écriture | String | | Décalage UTC en vigueur. |
| Fuseau horaire | Lecture<br/>Écriture | String | | Indique dans quel fuseau horaire se trouve l’appareil. |
| MemoryFree | Lire | Integer | Ko | Estimation de la mémoire actuellement disponible pour l’espace de stockage qui permet de stocker des données et des logiciels dans l’appareil. |
| MemoryTotal | Lire | Integer | Ko | Quantité totale d’espace de stockage permettant de stocker des données et des logiciels dans l’appareil. |
| BatteryLevel | Lire | Integer | 0-100 % | Niveau actuel de la batterie sous forme de pourcentage (de 0 à 100). |
| BatteryStatus | Lire | Integer | 0-6 | **0** : la batterie fonctionne correctement et elle n’est pas branchée.<br/>**1** : la batterie est en cours de chargement.<br/>**2** : la batterie est entièrement chargée et elle est branchée.<br/>**3** : la batterie est endommagée.<br/>**4** : la batterie affiche un niveau de charge faible.<br/>**5** : la batterie n’est pas présente.<br/> **6** : les informations relatives à la batterie ne sont pas disponibles. |

### Objet de mise à jour du microprogramme

| Nom de la ressource | Opération | Type | Plage et unités | Description |
|----------------|-----------|---------|-----------------|-------------|
| Package | Écrire | Opaque | | Package du microprogramme au format binaire.<br/>Correspond à l’API de service :<br/>**SystemPropertyNames.FirmwarePackage** |
| PackageURI | Écrire | String | 0-255 octets | URI à partir duquel l’appareil peut télécharger le package du microprogramme.<br/>Correspond à l’API de service : **SystemPropertyNames.FirmwarePackageUri** |
| Mettre à jour | Exécuter | | | Met à jour le microprogramme à l’aide du package du microprogramme stocké dans Package, ou par le biais du microprogramme téléchargé à partir de l’URI du package.<br/>Correspond à l’API de service :<br/>**ScheduleFirmwareUpdateAsync** |
| State | Lire | Integer | 1-3 | État du processus de mise à jour du microprogramme :<br/>**1** : inactif. Cela peut survenir avant le téléchargement du package du microprogramme ou après l’application du package du microprogramme.<br/>**2** : téléchargement du package du microprogramme.<br/>**3** : package du microprogramme téléchargé.<br/> Correspond à l’API de service : **SystemPropertyNames.FirmwareUpdateState** |
| UpdateResult | Lire | Integer | 0-6 | Résultat du téléchargement ou de la mise à jour du microprogramme<br/>**0** : valeur par défaut.<br/>**1** : mise à jour du microprogramme réussie.<br/>**2** : mémoire insuffisante pour le nouveau package du microprogramme.<br/>**3** : mémoire insuffisante pendant le téléchargement du package du microprogramme.<br/>**4** : déconnexion intempestive lors du téléchargement du microprogramme.<br/>**5** : échec du contrôle CRC pour le nouveau package téléchargé.<br/>**6** : type de package du microprogramme non pris en charge.<br/>**7** : URI non valide. Correspond à l’API de service : **SystemPropertyNames.FirmwareUpdateResult** |
| PkgName | Lire | String | 0-255 octets | Nom descriptif du package du microprogramme référencé par la ressource **Package**<br/>Correspond à l’API de service : <br/>**SystemPropertyNames.FirmwarePackageName** |
| PackageVersion | Lire | String | 0-255 octets | Version du package du microprogramme référencé par la ressource **Package**<br/>Correspond à l’API de service : <br/>**SystemPropertyNames.FirmwarePackageVersion** |

### Objet de serveur LWM2M

| Nom de la ressource | Opération | Type | Plage et unités | Description |
|------------------------|------------|---------|-----------------|---------------|
| Période minimale par défaut. | Lecture Écriture | Integer | Secondes | Période pendant laquelle l’appareil retarde l’envoi d’une mise à jour pour une propriété observée. Par exemple, pour une valeur **DefaultMinPeriod** de 5 minutes, l’appareil envoie une mise à jour relative à une propriété observée au maximum toutes les 5 minutes, même si la valeur change plus fréquemment. Correspond à l’API de service : **SystemPropertyNames.DefaultMinPeriod** |
| Période maximale par défaut. | Lecture Écriture | Integer | Secondes | Période (en secondes) pendant laquelle l’appareil, que la propriété observée change ou non, envoie une mise à jour pour la valeur de la propriété. Par exemple, pour une valeur **DefaultMaxPeriod** de 6 heures, une propriété observée envoie une mise à jour relative à la valeur de cette propriété au moins toutes les 6 heures, que les ressources changent ou non.<br/>Correspond à l’API de service : <br/>**SystemPropertyNames.DefaultMaxPeriod** |
| Durée de vie | Lecture Écriture | Integer | Secondes | Durée de vie de l’inscription de l’appareil. L’appareil doit envoyer une nouvelle demande d’inscription ou de mise à jour dans cette durée de vie. Dans le cas contraire, l’appareil est désinscrit du service.<br/>Correspond à l’API de service : <br/>**SystemPropertyNames.RegistrationLifetime** |

### Objet de configuration

| Nom de la ressource | Opération | Type | Plage et unités | Description |
|---------------|------------|--------|-----------------|-------------|
| Nom | Lecture Écriture | String | | Identifie de façon unique le nom de la configuration d’appareil à lire ou à mettre à jour. |
| Valeur | Lecture Écriture | String | | Identifie de façon unique la valeur de la configuration à lire ou à mettre à jour. |
| Appliquer | Exécuter | | | Applique la modification de configuration sur l’appareil. |

## Étapes suivantes

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Conception de votre solution][lnk-design]
- [Guide du développeur][lnk-devguide]
- [Simulation d’un appareil avec le Kit de développement logiciel (SDK) Gateway][lnk-gateway]
- [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]

[img-library-overview]: media/iot-hub-device-management-library/library.png
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-simple-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_simple_sample
[lnk-edison-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[Azure IoT Hub device SDK]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c
[Azure IoT Hub]: Link%20to%20DM%20Overview
[Lightweight M2M]: http://openmobilealliance.org/about-oma/work-program/m2m-enablers/
[CoAP]: https://tools.ietf.org/html/rfc7252
[Wakaama]: https://github.com/eclipse/wakaama
[OMA LWM2M Object and resource registry]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-Wakaama]: https://github.com/eclipse/wakaama
[lnk-github1]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-github2]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-oma]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->