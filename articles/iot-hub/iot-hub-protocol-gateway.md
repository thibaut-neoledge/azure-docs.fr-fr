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

Azure IoT Hub en mode natif prend en charge la communication via les protocoles HTTPS et AMQP. Dans certains cas, il est possible que des appareils ou des passerelles ne soient pas en mesure d’utiliser l’un des protocoles standard et nécessitent une adaptation de protocole. Dans ce cas, vous pouvez utiliser une passerelle personnalisée. Une passerelle personnalisée peut activer l’adaptation de protocole pour les points de terminaison IoT Hub en effectuant le pontage du trafic vers et depuis IoT Hub. Une passerelle de protocole Internet des objets (IoT) Azure active l’adaptation du protocole pour IoT Hub. Elle implémente également un adaptateur de protocole MQTT pour autoriser la communication entre un appareil IoT et IoT Hub via le protocole MQTT.

## Passerelle de protocole Azure IoT

La passerelle de protocole Azure IoT est une infrastructure pour l’adaptation du protocole, conçue pour la communication bidirectionnelle à grande échelle entre les appareils, avec IoT Hub. La passerelle de protocole est un composant direct qui accepte les connexions des appareils via un protocole spécifique. Elle réduit le trafic vers IoT Hub via AMQP 1.0. La passerelle de protocole IoT est disponible en tant que projet de logiciel open source pour apporter de la flexibilité et une aide supplémentaire pour un grand nombre de protocoles et de versions de protocoles.

Vous pouvez déployer la passerelle de protocole dans Azure de manière très évolutive grâce aux rôles de travail d’Azure Cloud Services. En outre, la passerelle de protocole peut être déployée dans les environnements locaux comme des passerelles de champ.

La passerelle de protocole Azure IoT inclut un adaptateur MQTT pour faciliter la communication avec des appareils via le protocole MQTT v3.1.1. La passerelle de protocole et l’implémentation de MQTT sont fournies en tant que projet logiciel open source pour plus de flexibilité. Cela vous permet de personnaliser l’implémentation en fonction des besoins.

L’adaptateur MQTT illustre également le modèle de programmation pour la création d’adaptateurs de protocole pour d’autres protocoles. En outre, le modèle de programmation de passerelle de protocole IoT vous permet de connecter des composants personnalisés pour les traitements spécialisés, comme l’authentification personnalisée, les transformations de messages, la compression / décompression ou le chiffrement / déchiffrement du trafic entre les appareils et IoT Hub.

## Étapes suivantes

Pour en savoir plus sur la passerelle de protocole Azure IoT, son utilisation et son déploiement dans le cadre de votre solution IoT, consultez :

* [Référentiel sur la passerelle de protocole Azure IoT sur GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guide du développeur de passerelle de protocole Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=AcomDC_1223_2015-->