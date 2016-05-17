<properties
 pageTitle="Activer les appareils gérés derrière une passerelle IoT | Microsoft Azure"
 description="Rubrique d’aide sur l’utilisation d’une passerelle IoT créée à l’aide du Kit de développement logiciel (SDK) de la passerelle, ainsi que des appareils gérés par IoT Hub."
 services="iot-hub"
 documentationCenter=""
 authors="chipalost"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="cstreet"/>
 
# Activer les appareils gérés derrière une passerelle IoT

## Isolement de base des appareils

Les organisations utilisent souvent des passerelles IoT pour accroître la sécurité globale de leurs solutions IoT. Certains appareils doivent envoyer des données vers le cloud, mais ils ne sont pas en mesure de se protéger contre les menaces existantes sur Internet. Vous pouvez protéger ces appareils des threads externes en leur permettant de communiquer avec le monde extérieur via une passerelle.

La passerelle se trouve à la limite entre un environnement sécurisé et l’environnement Internet ouvert. Les appareils communiquent avec la passerelle et la passerelle transmet les messages à la destination correcte sur le cloud. La passerelle est renforcée contre les threads externes, bloque les demandes non autorisées, autorise le trafic entrant autorisé et transmet le trafic entrant à l’appareil approprié.

![][1]

Vous pouvez également placer des appareils qui peuvent se protéger eux-mêmes derrière une passerelle pour un niveau de sécurité supplémentaire. Dans ce cas, il vous suffit de faire en sorte que le système d’exploitation de la passerelle soit à jour contre les vulnérabilités les plus récentes, au lieu de mettre à jour le système d’exploitation sur chaque appareil.

## Isolement et intelligence

Un routeur renforcé constitue une passerelle suffisante pour isoler simplement les appareils. Toutefois, les solutions IoT nécessitent souvent qu’une passerelle fournisse davantage d’intelligence que le simple isolement des appareils. Par exemple, vous souhaitez peut-être gérer vos appareils à partir du cloud. Vous êtes en mesure d’utiliser LWM2M, un protocole de gestion des appareils standard, pour le composant de gestion cloud de la solution. Toutefois, les appareils envoient les données de télémétrie à l’aide d’un protocole non TCP/IP. En outre, les appareils produisent un grand nombre de données et vous souhaitez uniquement télécharger un sous-ensemble filtré des données de télémétrie. Vous pouvez créer une solution qui intègre une passerelle IoT capable de gérer deux flux de données distincts. La passerelle doit :

-   Comprendre la **télémétrie**, la filtrer, puis la télécharger vers le cloud via la passerelle. La passerelle n’est plus un simple routeur qui se contente de transférer les données entre l’appareil et le cloud.

-   Échanger simplement les **données de gestion des appareils LWM2M** entre les appareils et le cloud. Il n’est pas nécessaire que la passerelle comprenne les données qu’elle reçoit. Elle doit simplement s’assurer que les données sont transmises dans les deux sens entre les appareils et le cloud.

L’image suivante illustre ce scénario :

![][2]

## La solution : la gestion des appareils IoT Azure et le Kit de développement logiciel (SDK) de la passerelle 

La version publique préliminaire de la [Gestion des appareils Azure IoT][lnk-device-management] et la version bêta du [Kit de développement logiciel (SDK) de la passerelle Azure IoT] donnent vie à ce scénario. La passerelle gère chaque flux de données comme suit :

-   **Télémétrie** : vous pouvez utiliser le Kit de développement logiciel (SDK) de la passerelle pour créer un pipeline qui comprend, filtre et envoie les données de télémétrie vers le cloud. Le Kit de développement logiciel (SDK) de la passerelle fournit le code qui implémente des parties de ce pipeline pour le compte du développeur. Vous trouverez plus d’informations sur l’architecture du Kit de développement logiciel (SDK) dans le didacticiel [Kit de développement logiciel (SDK) de la passerelle IoT - Prise en main][lnk-gateway-get-started].

-   **Gestion des appareils** : la gestion des appareils Azure fournit un client LWM2M qui s’exécute sur l’appareil ainsi qu’une interface cloud pour émettre les commandes de gestion à l’appareil.
    
    Aucune logique spéciale n’est requise sur la passerelle car celle-ci n’a pas besoin de traiter les données LWM2M échangées entre l’appareil et votre IoT Hub. Vous pouvez activer le partage de connexion Internet, une fonctionnalité de nombreux systèmes d’exploitation modernes, sur la passerelle pour activer l’échange de données LWM2M. Vous pouvez choisir un système d’exploitation approprié pour ce scénario, car le Kit de développement logiciel (SDK) de la passerelle prend en charge plusieurs systèmes d’exploitation. Voici des instructions pour activer le partage de connexion Internet sur [Windows 10] et [Ubuntu], deux des systèmes d’exploitation parmi les nombreux pris en charge.

L’illustration suivante montre l’architecture de haut niveau utilisée pour activer ce scénario à l’aide de la [Gestion des appareils Azure IoT][lnk-device-management] et du [Kit de développement logiciel (SDK) de la passerelle Azure IoT].

![][3]

## Étapes suivantes

Pour savoir comment utiliser le Kit de développement logiciel (SDK) de la passerelle, consultez les didacticiels suivants :

- [Kit de développement logiciel (SDK) de la passerelle IoT - Prise en main de Linux][lnk-gateway-get-started]
- [Kit de développement logiciel (SDK) de la passerelle – envoyer des messages appareil-à-cloud avec une simulation d’appareil à l’aide de Linux][lnk-gateway-simulated]

Vous trouverez plus d’informations sur la gestion des appareils avec IoT Hub en lisant la rubrique [Vue d’ensemble de la gestion des appareils Azure IoT Hub][lnk-device-management].

<!-- Images and links -->
[1]: media/iot-hub-gateway-device-management/overview.png
[2]: media/iot-hub-gateway-device-management/manage.png
[Kit de développement logiciel (SDK) de la passerelle Azure IoT]: https://github.com/Azure/azure-iot-gateway-sdk/
[Windows 10]: http://windows.microsoft.com/fr-FR/windows/using-internet-connection-sharing#1TC=windows-7
[Ubuntu]: https://help.ubuntu.com/community/Internet/ConnectionSharing
[3]: media/iot-hub-gateway-device-management/manage_2.png
[lnk-gateway-get-started]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-gateway-simulated]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-device-management]: iot-hub-device-management-overview.md

<!---HONumber=AcomDC_0504_2016-->