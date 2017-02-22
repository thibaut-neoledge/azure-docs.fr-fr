---
title: "Filtres de connexion IP d’Azure Hub IoT | Microsoft Docs"
description: "Utilisation de filtres IP pour bloquer les connexions à partir d’adresses IP spécifiques pour votre Azure IoT Hub. Vous pouvez bloquer les connexions à partir d’adresses IP individuelles ou de plages d’adresses IP."
services: iot-hub
documentationcenter: 
author: BeatriceOltean
manager: timlt
editor: 
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: boltean
translationtype: Human Translation
ms.sourcegitcommit: ddb729d29072724f691c178967b6181f6ce06df4
ms.openlocfilehash: a9207d116e9b7360865c950ba00210ed67c3e028


---

# <a name="use-ip-filters"></a>Utiliser des filtres IP

La sécurité est un aspect important de toute solution IoT basée sur Azure IoT Hub. Vous devez parfois placer certaines adresses IP sur liste noire ou sur liste blanche dans le cadre de votre configuration de sécurité. La fonctionnalité _Filtre IP_ vous permet de configurer des règles de rejet ou d’acceptation du trafic provenant de certaines adresses IPv4.

## <a name="when-to-use"></a>Quand utiliser

Il existe deux cas d’utilisation spécifiques illustrant lorsqu’il est utile de bloquer les points de terminaison IoT Hub pour certaines adresses IP :

- Lorsque votre IoT Hub ne doit recevoir du trafic qu’à partir d’une plage spécifiée d’adresses IP et rejeter tout le reste. Par exemple, lorsque vous utilisez votre IoT Hub avec [Azure Express Route] pour créer des connexions privées entre un IoT Hub et votre infrastructure sur site.
- Lorsque vous devez refuser le trafic provenant d’adresses IP qui ont été identifiées comme suspectes par l’administrateur de IoT Hub.

## <a name="how-filter-rules-are-applied"></a>Application des règles de filtre

Les règles de filtre IP sont appliquées au niveau du service IoT Hub. Par conséquent, les règles de filtre IP s’appliquent à toutes les connexions issues des appareils et des applications principales utilisant n’importe quel protocole pris en charge.

Toute tentative de connexion à partir d’une adresse IP qui correspond à une règle IP de rejet dans votre IoT Hub reçoit un code d’état 401 non autorisé et une description. Le message de réponse ne mentionne pas la règle IP.

## <a name="default-setting"></a>Paramètre par défaut
Par défaut, la grille **Filtre IP** dans le portail pour un IoT Hub est vide. Ce paramètre par défaut signifie que votre concentrateur accepte les connexions de n’importe quelle adresse IP. Ce paramètre par défaut est équivalent à une règle qui accepte la plage d’adresses IP 0.0.0.0/0.

![][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Ajout ou modification d’une règle de filtre IP

Lorsque vous ajoutez une règle de filtre IP, vous êtes invité à renseigner les valeurs suivantes :

- Un **nom de règle de filtre IP** qui doit être une chaîne unique, ne respectant pas la casse, alphanumérique, comptant jusqu'à 128 caractères. Seuls les caractères alphanumériques ASCII 7 bits et `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` sont acceptés.
- Sélectionnez **rejeter** ou **accepter** comme **action** pour la règle de filtre IP.
- Fournissez une adresse IPv4 unique ou un bloc d’adresses IP en notation CIDR. Par exemple, dans la notation CIDR, 192.168.100.0/22 représente les 1024 adresses IPv4 allant de 192.168.100.0 à 192.168.103.255.

![][img-ip-filter-add-rule]

Une fois la règle enregistrée, une alerte s’affiche vous informant que la mise à jour est en cours.

![][img-ip-filter-save-new-rule]

L’option **Ajouter** est désactivée lorsque vous atteignez le nombre maximal de dix règles de filtre IP.

Vous pouvez modifier une règle existante en double-cliquant sur la ligne qui contient la règle.

> [!NOTE]
> Le rejet d’adresses IP est de nature à empêcher d’autres services Azure (comme Azure Stream Analytics, Azure Virtual Machines ou l’Explorateur d’appareils dans le portail) d’interagir avec IoT Hub.

## <a name="delete-an-ip-filter-rule"></a>Suppression d’une règle de filtre IP

Pour supprimer une règle de filtre IP, sélectionnez une ou plusieurs règles dans la grille et cliquez sur **Supprimer**.

![][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Évaluation de règle de filtre IP

Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

Par exemple, si vous souhaitez accepter les adresses dans la plage 192.168.100.0/22 et rejeter tout le reste, la première règle de la grille doit accepter la plage d’adresses 192.168.100.0/22. La règle suivante doit rejeter toutes les adresses à l’aide de la plage 0.0.0.0/0.

Vous pouvez modifier l’ordre de vos règles de filtre IP dans la grille en cliquant sur les trois points verticaux au début d’une ligne et en effectuant un glisser-déplacer.

Pour enregistrer le nouvel ordre de vos règles de filtre IP, cliquez sur **Enregistrer**.

![][img-ip-filter-rule-order]

## <a name="next-steps"></a>Étapes suivantes

Pour explorer davantage les capacités de IoT Hub, consultez :

* [Surveillance des opérations][lnk-monitor]
* [Métriques d’IoT Hub][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/en-us/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md


<!--HONumber=Jan17_HO4-->


