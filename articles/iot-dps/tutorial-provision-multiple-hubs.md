---
title: "Utiliser le service IoT Hub Device Provisioning pour approvisionner des appareils sur des hubs IoT à charge équilibrée | Microsoft Docs"
description: "Approvisionnement automatique d’appareils DPS sur des hubs IoT à charge équilibrée dans le portail Azure"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>Approvisionner des appareils sur des hubs IoT à charge équilibrée

Ce didacticiel montre comment approvisionner des appareils sur plusieurs hubs IoT à charge équilibrée en utilisant le service Device Provisioning (DPS). Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Utiliser le portail Azure pour approvisionner un deuxième appareil sur un deuxième hub IoT 
> * Ajouter une entrée de liste d’inscriptions au deuxième appareil
> * Définir la stratégie d’allocation DPS en appliquant une **distribution uniforme**
> * Lier le nouveau hub IoT au service DPS

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

Ce didacticiel s’appuie sur le précédent didacticiel [Approvisionner un appareil sur un hub](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Utiliser le portail Azure pour approvisionner un deuxième appareil sur un deuxième hub IoT

Suivez les étapes du didacticiel [Approvisionner un appareil sur un hub](tutorial-provision-device-to-hub.md) afin d’approvisionner un deuxième appareil sur un autre hub IoT.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Ajouter une entrée de liste d’inscriptions au deuxième appareil

La liste d’inscriptions indique au service DPS la méthode d’attestation (méthode de confirmation d’une identité d’appareil) qu’il utilise avec l’appareil. L’étape suivante consiste à ajouter une entrée de liste d’inscriptions pour le deuxième appareil. 

1. Dans la page de votre service DPS, cliquez sur **Gérer les inscriptions**. La page **Ajouter une entrée de liste d’inscriptions** s’affiche. 
2. En haut de la page, cliquez sur **Ajouter**.
2. Renseignez les champs et cliquez sur **Enregistrer**.

## <a name="set-the-dps-allocation-policy"></a>Définir la stratégie d’allocation du service DPS

La stratégie d’allocation est un paramètre DPS qui détermine la façon dont les appareils sont affectés à un hub IoT. Trois stratégies d’allocation sont prises en charge : 

1. **Latence la plus faible** : les appareils sont configurés pour un hub IoT en fonction du hub ayant la plus faible latence sur l’appareil.
2. **Distribution uniformément pondérée** (par défaut) : les hubs IoT liés sont susceptibles d’avoir des appareils approvisionnés dans des proportions égales. Il s’agit du paramètre par défaut. Si vous approvisionnez des appareils sur un seul hub IoT, vous pouvez conserver ce paramètre. 
3. **Configuration statique par le biais de la liste d'inscriptions** : la spécification du hub IoT souhaité dans la liste d’inscriptions est prioritaire sur la stratégie d’allocation au niveau du service DPS.

Effectuez les étapes suivantes pour définir la stratégie d’allocation :

1. Pour définir la stratégie d’allocation, dans la page du service DPS, cliquez sur **Gérer la stratégie d’allocation**.
2. Définissez la stratégie d’allocation sur **Distribution uniformément pondérée**.
3. Cliquez sur **Enregistrer**.

## <a name="link-the-new-iot-hub-to-dps"></a>Lier le nouveau hub IoT au service DPS

Liez le service DPS et le hub IoT afin que le service puisse enregistrer des appareils sur ce hub.

1. Dans la page **Toutes les ressources**, cliquez sur le service DPS que vous avez créé.
2. Dans la page du service DPS, cliquez sur **Hubs IoT liés**.
3. Cliquez sur **Add**.
4. Dans la page **Ajouter un lien au hub IoT**, utilisez les cases d’option pour spécifier si le hub IoT lié se trouve dans l’abonnement actuel ou dans un autre abonnement. Ensuite, choisissez le nom du hub IoT dans la zone **Hub IoT**.
5. Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Utiliser le portail Azure pour approvisionner un deuxième appareil sur un deuxième hub IoT 
> * Ajouter une entrée de liste d’inscriptions au deuxième appareil
> * Définir la stratégie d’allocation DPS en appliquant une **distribution uniforme**
> * Lier le nouveau hub IoT au service DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
