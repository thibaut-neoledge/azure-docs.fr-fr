---
title: Concepts de service du service Azure IoT Hub Device Provisioning | Microsoft Docs
description: "Décrit les concepts d’approvisionnement de service pour les appareils avec le service Device Provisioning et IoT Hub"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 10/03/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 96c63e5d0379150ea619dbbe912a21e373f808af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Concepts du service IoT Hub Device Provisioning

Le service IoT Hub Device Provisioning est un service d’assistance pour IoT Hub qui vous permet de configurer l’approvisionnement sans contact des appareils dans un hub IoT spécifié. Avec le service Device Provisioning, vous pouvez approvisionner des millions d’appareils de manière sécurisée et scalable.

L’approvisionnement des appareils est un processus en deux parties. La première partie établit la connexion initiale entre l’appareil et la solution IoT en *inscrivant* l’appareil. La deuxième partie applique la *configuration* appropriée à l’appareil selon les exigences spécifiques de la solution. Une fois que ces deux étapes sont terminées, on peut dire que l’appareil est complètement *approvisionné*. Le service Device Provisioning automatise les deux étapes pour fournir une expérience d’approvisionnement fluide pour l’appareil.

Cet article donne une vue d’ensemble des concepts d’approvisionnement applicables en particulier à la gestion du *service*. Cet article concerne particulièrement les personnes impliquées dans l’[étape de configuration du cloud](about-iot-dps.md#cloud-setup-step) qui permet de préparer un appareil pour le déploiement.

## <a name="service-operations-endpoint"></a>Point de terminaison des opérations de service

Le point de terminaison des opérations de service est le point de terminaison de gestion des paramètres de service et de maintenance de la liste d’inscription. Ce point de terminaison est utilisé uniquement par l’administrateur de service. Il n’est pas utilisé par les appareils.

## <a name="device-provisioning-endpoint"></a>Point de terminaison d'approvisionnement des appareils

Le point de terminaison d’approvisionnement des appareils est le point de terminaison central sur lequel communiquent tous les appareils pour l’approvisionnement. L’URL est la même pour tous les services d’approvisionnement de façon à éviter d’avoir à réinitialiser les appareils avec les nouvelles informations de connexion dans les scénarios de chaîne d’approvisionnement. L’[étendue de l’ID](#id-scope) garantit l’isolation des locataires.

## <a name="linked-iot-hubs"></a>Hubs IoT liés

Le service Device Provisioning peut uniquement approvisionner des appareils sur des hubs IoT auxquels il est lié. La liaison d’un hub IoT au service Device Provisioning donne au Registre d’appareils du hub IoT des autorisations en lecture/écriture sur le service. Avec cette liaison, le service Device Provisioning peut inscrire un ID d’appareil et définir la configuration initiale dans l’appareil jumeau. Les hubs IoT liés peuvent se trouver dans n’importe quelle région Azure. Vous pouvez lier des hubs d’autres abonnements à votre service d’approvisionnement.

## <a name="allocation-policy"></a>Stratégie d’allocation

Paramètre au niveau du service qui détermine la façon dont le service Device Provisioning attribue les appareils à un hub IoT. Trois stratégies d’allocation sont prises en charge :
* **Distribution uniformément pondérée** : Les hubs IoT liés sont susceptibles d’avoir des appareils approvisionnés en proportions égales. Paramètre par défaut. Si vous approvisionnez des appareils sur un seul hub IoT, vous pouvez conserver ce paramètre.
* **Latence la plus faible** : Les appareils sont approvisionnés sur le hub IoT ayant la latence la plus faible sur l’appareil. Si plusieurs hubs IoT liés fournissent la même latence la plus faible, le service d’approvisionnement répartit les appareils sur ces hubs
* **Configuration statique par le biais de la liste d’inscriptions** : La spécification du hub IoT souhaité dans la liste d’inscriptions est prioritaire sur la stratégie d’allocation au niveau du service.

## <a name="enrollment"></a>Inscription

L’inscription désigne l’enregistrement d’appareils ou de groupes d’appareils susceptibles de s’inscrire à un moment donné. L’enregistrement d’inscription contient les informations de l’appareil ou du groupe d’appareils, y compris la méthode d’attestation du ou des appareils et, éventuellement, la configuration initiale souhaitée, le hub IoT souhaité et l’ID d’appareil souhaité. Il existe deux types d’inscription pris en charge par le service Device Provisioning.

### <a name="enrollment-group"></a>Groupe d’inscription

Un groupe d’inscription désigne un groupe d’appareils qui partagent un mécanisme d’attestation spécifique. Tous les appareils du groupe d’inscription présentent des certificats X.509 qui ont été signés par la même autorité de certification racine. Les groupes d’inscription peuvent uniquement utiliser le mécanisme d’attestation X.509. Le nom du groupe d’inscription et le nom du certificat sont alphanumériques, en minuscules et peuvent contenir des traits d’union.

> [!TIP]
> Nous vous recommandons d’utiliser un groupe d’inscription pour un grand nombre d’appareils qui partagent une configuration initiale souhaitée ou pour des appareils destinés au même locataire.

### <a name="individual-enrollment"></a>Inscription individuelle

Une inscription individuelle désigne une entrée pour un seul appareil pouvant s’inscrire. Les inscriptions individuelles peuvent utiliser des certificats x509 ou des jetons SAP (dans un module TPM réel ou virtuel) comme mécanismes d’attestation. L’ID d’enregistrement d’une inscription individuelle est alphanumérique, en minuscules et peut contenir des traits d’union. Dans le cas d’inscriptions individuelles, vous pouvez spécifier l’ID de l’appareil IoT Hub souhaité.

> [!TIP]
> Nous vous recommandons d’utiliser des inscriptions individuelles pour les appareils qui nécessitent une configuration initiale unique ou pour les appareils qui peuvent uniquement utiliser des jetons SAP par le biais du module TPM ou du module TPM virtuel comme mécanisme d’attestation.

## <a name="registration"></a>Inscription

L’inscription désigne l’enregistrement d’un appareil inscrit/approvisionné sur un hub IoT via le service Device Provisioning. Les enregistrements d’inscription sont créés automatiquement. Ils peuvent être supprimés, mais pas mis à jour.

## <a name="operations"></a>Opérations

Les opérations désignent l’unité de facturation du service Device Provisioning. Une opération est une instruction envoyée au service qui se déroule correctement. Les opérations incluent les inscriptions et réinscriptions d’appareils, ainsi que les changements côté service, comme l’ajout et la mise à jour des entrées de liste d’inscriptions.
