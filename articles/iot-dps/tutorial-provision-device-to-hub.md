---
title: Approvisionner un appareil avec le service IoT Hub Device Provisioning | Microsoft Docs
description: Approvisionner votre appareil sur un seul hub IoT avec le service IoT Hub Device Provisioning
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: bf50699d2dc67294d554ba15713254a8b88d8ade
ms.contentlocale: fr-fr
ms.lasthandoff: 09/06/2017

---

# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Approvisionner l’appareil sur un hub IoT avec le service IoT Hub Device Provisioning

Dans le didacticiel précédent, vous avez appris à configurer un appareil pour vous connecter à votre service Device Provisioning. Dans ce didacticiel, vous allez apprendre à utiliser ce service pour approvisionner votre appareil sur un seul hub IoT à l’aide de **_listes d’inscriptions_**. Ce didacticiel vous explique comment :

> [!div class="checklist"]
> * Inscrire l’appareil
> * Démarrer l’appareil
> * Vérifier que l’appareil est enregistré

## <a name="prerequisites"></a>Prérequis

Avant de continuer, assurez-vous de configurer votre appareil et son *module de sécurité matériel* comme indiqué dans le didacticiel [Configurer un appareil à approvisionner à l’aide du service IoT Hub Device Provisioning](./tutorial-set-up-device.md).


<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Inscrire l’appareil

Cette étape implique l’ajout des artefacts de sécurité uniques de l’appareil au service Device Provisioning. Ces artefacts de sécurité sont les suivants :

- Pour les appareils TPM :
    - La *paire de clés de type EK* qui est unique pour chaque simulation ou processeur TPM. Pour plus d’informations, consultez [Comprendre la paire de clés de type EK (Endorsement Key) du module de plateforme sécurisée](https://technet.microsoft.com/library/cc770443.aspx).
    - *L’ID d’enregistrement* qui est utilisé pour identifier un appareil dans l’espace de noms ou l’étendue. Cet ID peut être ou non le même que l’ID de l’appareil. L’ID est obligatoire pour chaque appareil. Pour les appareils basés sur TPM, l’ID d’enregistrement peut être dérivé du module TPM lui-même, par exemple un hachage SHA-256 de la paire de clés de type EK TPM.

    ![Informations d’inscription pour le module TPM dans le portail](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)

- Pour les appareils basés sur X.509 :
    - Le [certificat délivré à X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) (processeur ou simulation), sous la forme d’un fichier *.pem* ou *.cer*. Pour une inscription individuelle, vous devez utiliser le *certificat du signataire* de votre système X.509, tandis que pour des groupes d’inscriptions, vous devez utiliser le *certificat racine*.

    ![Informations d’inscription pour X.509 dans le portail](./media/tutorial-provision-device-to-hub/x509-device-enrollment.png)


Il existe deux façons d’inscrire l’appareil auprès du service Device Provisioning :

- **Groupe d’inscriptions** : représente un groupe d’appareils qui partagent un mécanisme d’attestation spécifique. Nous recommandons d’utiliser un groupe d’inscriptions pour un grand nombre d’appareils qui partagent une configuration initiale souhaitée ou pour des appareils destinés au même locataire.

    ![Groupes d’inscriptions pour X.509 dans le portail](./media/tutorial-provision-device-to-hub/x509-enrollment-groups.png)

- **Inscription individuelle** : représente une entrée d’un seul appareil pouvant être enregistré auprès du service Device Provisioning. Les inscriptions individuelles peuvent utiliser des certificats x509 ou des jetons SAP (dans un module TPM réel ou virtuel) comme mécanismes d’attestation. Nous recommandons d’utiliser des inscriptions individuelles pour les appareils qui nécessitent une configuration initiale unique ou pour les appareils qui peuvent seulement utiliser des jetons SAP par le biais du module TPM ou du module TPM virtuel comme mécanisme d’attestation. Dans le cas des inscriptions individuelles, vous pouvez spécifier l’ID d’appareil de hub IoT de votre choix.

Voici les étapes à suivre pour inscrire l’appareil dans le portail :

1. Notez les artefacts de sécurité pour le module HSM sur votre appareil. Dans un environnement de développement, vous devrez peut-être utiliser les API mentionnées dans la section [Extraire les artefacts de sécurité](./tutorial-set-up-device.md#extractsecurity) du didacticiel précédent.

1. Connectez-vous au portail Azure, cliquez sur le bouton **Toutes les ressources** dans le menu de gauche et ouvrez votre service Device Provisioning.

1. Dans le panneau de résumé du service Device Provisioning, sélectionnez **Gérer les inscriptions**. Suivant la configuration de votre appareil, sélectionnez l’onglet **Inscriptions individuelles** ou **Groupes d’inscriptions**. Cliquez sur le bouton **Ajouter** dans la partie supérieure. Sélectionnez **TPM** ou **X.509** comme *mécanisme* d’attestation de l’identité, puis entrez les artefacts de sécurité appropriés, comme indiqué précédemment. Vous pouvez entrer un nouvel **ID d’appareil IoT Hub**. Quand vous avez terminé, cliquez sur le bouton **Enregistrer**. 

1. Une fois l’appareil correctement inscrit, il doit apparaître comme suit dans le portail :

    ![Inscription TPM réussie dans le portail](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)


## <a name="start-the-device"></a>Démarrer l’appareil

À ce stade, la configuration suivante est prête pour l’enregistrement de l’appareil :

1. Votre appareil ou groupe d’appareils est inscrit auprès de votre service Device Provisioning. 
2. Votre appareil est prêt avec le processeur HSM configuré et accessible via l’application à l’aide du kit SDK client du service Device Provisioning.

Démarrez l’appareil pour autoriser votre application cliente à démarrer l’enregistrement auprès de votre service Device Provisioning.  


## <a name="verify-the-device-is-registered"></a>Vérifier que l’appareil est enregistré

Une fois que votre appareil démarre, les actions suivantes doivent se produire. Pour plus d’informations, consultez l’exemple d’application de simulateur TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c). 

1. L’appareil envoie une demande d’enregistrement à votre service Device Provisioning.
2. Pour les appareils TPM, le service Device Provisioning envoie une demande d’enregistrement à laquelle répond votre appareil. 
3. Une fois l’enregistrement réussi, le service Device Provisioning envoie l’URI du hub IoT, l’ID de l’appareil et la clé chiffrée à l’appareil. 
4. L’application cliente IoT Hub sur l’appareil peut alors se connecter à votre hub. 
5. Une fois la connexion au hub établie, l’appareil doit apparaître dans **Device Explorer** dans le hub IoT. 

    ![Connexion réussie au hub dans le portail](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Inscrire l’appareil
> * Démarrer l’appareil
> * Vérifier que l’appareil est enregistré

Passez au didacticiel suivant pour apprendre à approvisionner plusieurs appareils sur des hubs à charge équilibrée. 

> [!div class="nextstepaction"]
> [Approvisionner des appareils sur des hubs IoT à charge équilibrée](./tutorial-provision-multiple-hubs.md)

