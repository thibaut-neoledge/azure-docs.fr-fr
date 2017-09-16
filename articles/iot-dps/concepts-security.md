---
title: "Concepts de sécurité du service Azure IoT Hub Device Provisioning | Microsoft Docs"
description: "Décrit les concepts d’approvisionnement de sécurité pour les appareils avec le service Device Provisioning et IoT Hub"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.contentlocale: fr-fr
ms.lasthandoff: 09/06/2017

---

# <a name="iot-hub-device-provisioning-service-security-concepts"></a>Concepts de sécurité du service IoT Hub Device Provisioning 

Le service IoT Hub Device Provisioning est un service d’assistance pour IoT Hub qui vous permet de configurer l’approvisionnement sans contact des appareils dans un hub IoT spécifié. Avec le service Device Provisioning, vous pouvez approvisionner des millions d’appareils de manière sécurisée et scalable. Cet article donne une vue d’ensemble des concepts de *sécurité* impliqués dans l’approvisionnement d’appareils. Cet article concerne toutes les personnes impliquées dans la préparation d’un appareil pour le déploiement.

## <a name="attestation-mechanism"></a>Mécanisme d’attestation

Le mécanisme d’attestation est la méthode utilisée pour confirmer l’identité d’un appareil. Le mécanisme d’attestation concerne également la liste d’inscriptions, qui indique au service d’approvisionnement la méthode d’attestation à utiliser avec un appareil donné.

> [!NOTE]
> IoT Hub utilise un « schéma d’authentification » pour un concept semblable dans ce service.

Le service Device Provisioning prend en charge deux formes d’attestation :
* **Certificats X.509** basés sur le flux d’authentification de certificat X.509 standard.
* **Jetons SAP** basés sur une stimulation de nonces utilisant le standard TPM pour les clés. Il n’est pas nécessaire d’avoir un TPM physique sur l’appareil, mais le service utilise pour l’attestation la paire de clés de type EK conformément à la [spécification TPM](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/).

## <a name="hardware-security-module"></a>Module de sécurité matériel

Le module de sécurité matériel, ou module HSM, est utilisé pour le stockage matériel sécurisé des secrets de l’appareil. C’est la forme de stockage de secrets la plus sécurisée. Les certificats X.509 et les jetons SAP peuvent être stockés dans le module HSM. Les modules HSM peuvent être utilisés avec les deux mécanismes d’attestation que l’approvisionnement prend en charge.

> [!TIP]
> Nous vous recommandons vivement d’utiliser un module HSM avec les appareils pour stocker en toute sécurité des secrets sur vos appareils.

Les secrets de l’appareil peuvent également être stockés sous forme logicielle (mémoire), mais cette forme de stockage est moins sécurisée que le module HSM.

## <a name="trusted-platform-module-tpm"></a>Module de plateforme sécurisée (TPM)

Un module TPM peut faire référence à un standard de stockage sécurisé pour les clés utilisées dans l’authentification de la plateforme ou à l’interface d’E/S utilisée pour interagir avec les modules qui implémentent le standard. Les modules TPM peuvent exister sous forme de matériel distinct, de matériel intégré, basés sur un microprogramme ou basés sur un logiciel. Découvrez plus d’informations sur [Modules TPM et attestation TPM](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation). Le service Device Provisioning prend en charge uniquement TPM 2.0.

## <a name="endorsement-key"></a>Paire de clés de type EK (Endorsement Key)

La paire de clés de type EK est une clé asymétrique contenue dans un module TPM qui a été générée au moment de la fabrication et est unique pour chaque module TPM. La paire de clés de type EK ne peut pas être changée ou supprimée. La partie privée de la paire de clés de type EK n’est jamais publiée en dehors du module TPM, tandis que sa partie publique est utilisée pour reconnaître un module TPM authentique. Découvrez plus d’informations sur la [paire de clés de type EK (Endorsement Key)](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx).

## <a name="storage-root-key"></a>Clé racine de stockage

La clé racine de stockage est stockée dans le module TPM et est utilisée pour protéger les clés TPM créées par des applications, afin que ces clés ne soient pas utilisables sans le module TPM. La clé racine de stockage est générée quand vous prenez possession du module TPM. Quand vous effacez le module TPM pour transférer la propriété a un autre utilisateur, une nouvelle clé racine de stockage est générée. Découvrez plus d’informations sur la [clé racine de stockage](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx).

## <a name="root-certificate"></a>Certificat racine

Un certificat racine est un type de certificat X.509 représentant une autorité de certification et auto-signé. Il constitue la dernière étape de la chaîne d’approbation.

## <a name="intermediate-certificate"></a>Certificat intermédiaire

Un certificat intermédiaire est un certificat X.509 qui a été signé par le certificat racine (ou par un autre certificat contenant le certificat racine dans sa chaîne) qui est utilisé pour signer le certificat feuille.

## <a name="leaf-certificate"></a>Certificat feuille

Un certificat feuille, ou certificat d’entité finale, est utilisé pour identifier le détenteur du certificat et sa chaîne d’approbation contient le certificat racine. Le certificat feuille n’est pas utilisé pour signer d’autres certificats.

