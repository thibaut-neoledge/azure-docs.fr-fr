<properties
   pageTitle="Passerelle de protocole IoT Azure | Microsoft Azure"
   description="Explique comment utiliser la passerelle de protocole Azure IoT pour étendre les capacités et la prise en charge du protocole Azure IoT Hub."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="kdotchko"/>

# Prise en charge de protocoles supplémentaires pour IoT Hub

IoT Hub en mode natif prend en charge la communication via les protocoles HTTPS et AMQP. Dans certains cas, il est possible que des appareils ou des passerelles ne soient pas en mesure d’utiliser l’un des protocoles standard et nécessitent une adaptation de protocole. Dans ce cas, une passerelle personnalisée peut activer l’adaptation de protocole pour les points de terminaison IoT Hub en effectuant le pontage du trafic vers et depuis IoT Hub. La passerelle de protocole Azure IoT permet une adaptation de protocole pour IoT Hub et implémente un adaptateur de protocole MQTT pour activer la communication entre les appareils IoT et IoT Hub via le protocole MQTT.

## Passerelle de protocole Azure IoT

La passerelle de protocole Azure IoT est une infrastructure pour l’adaptation du protocole conçue pour la communication d’appareils bidirectionnelle à grande échelle avec IoT Hub. La passerelle de protocole est un composant direct acceptant les connexions d’appareils via un protocole spécifique et effectuant un pontage du trafic vers IoT Hub via AMQP 1.0. La passerelle de protocole IoT est disponible en tant que projet OSS (Open Source Software) pour offrir une grande flexibilité lors de l’ajout de la prise en charge des différents protocoles et de leurs versions.

La passerelle de protocole peut être déployée dans Azure de manière hautement évolutive à l’aide des rôles de travail de Cloud Services. En outre, la passerelle de protocole peut être déployée dans les environnements locaux comme des passerelles de champ.

La passerelle de protocole Azure IoT inclut un adaptateur MQTT pour faciliter la communication avec des appareils via le protocole MQTT v3.1.1. La passerelle de protocole et l’implémentation MQTT sont fournies en tant que projet OSS pour la flexibilité permettant de personnaliser l’implémentation en fonction des besoins.

L’adaptateur MQTT illustre également le modèle de programmation pour la création d’adaptateurs de protocole pour d’autres protocoles. En outre, le modèle de programmation de passerelle de protocole IoT permet de connecter des composants personnalisés pour traiter l’authentification personnalisée, les transformations de messages, la compression/décompression ou le chiffrement/déchiffrement du trafic entre les appareils et IoT Hub.

## Étapes suivantes

Pour en savoir plus sur la passerelle de protocole Azure IoT, son utilisation et son déploiement dans le cadre de votre solution IoT, consultez le site :

* [Référentiel sur la passerelle de protocole Azure IoT sur GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guide du développeur de passerelle de protocole Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=AcomDC_1210_2015-->