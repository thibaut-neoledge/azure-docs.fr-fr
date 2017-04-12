---
title: "Déploiement de SAP S/4HANA ou BW/4HANA sur une machine virtuelle Azure | Microsoft Docs"
description: "Déploiement de SAP S/4HANA ou BW/4HANA sur une machine virtuelle Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2a645e6ab50b7b764a56bb7a79ad5cf295881214
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-sap-s4hana-or-bw4hana-on-microsoft-azure"></a>Déploiement de SAP S/4HANA ou BW/4HANA sur Microsoft Azure
Cet article explique comment déployer S/4HANA sur Microsoft Azure en utilisant SAP Cloud Appliance Library (SAP CAL) 3.0. Le déploiement d’autres solutions basées sur SAP HANA comme BW/4 HANA fonctionne de la même façon en termes de processus. Il vous suffit de sélectionner une autre solution.

> [!NOTE]
Pour plus d’informations sur SAP Cloud Appliance Library, consultez la [Page d’accueil de leur site](https://cal.sap.com/). Il existe également un blog SAP sur [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

## <a name="step-by-step-process-to-deploy-the-solution"></a>Procédure pas à pas de déploiement de la solution

Les captures d’écran suivantes montrent comment déployer S/4HANA sur Azure. Le processus fonctionne de la même façon pour les autres solutions telles que BW/4HANA.

La première capture d’écran montre toutes les solutions basées sur SAP CAL HANA disponibles sur Azure. Notez que **SAP S/4HANA édition locale** se trouve en bas.

![Capture d’écran de la fenêtre Solutions SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic-1b.jpg)

Créez d’abord un nouveau compte SAP CAL. Dans **Comptes**, vous voyez deux options pour Azure : Microsoft Azure, ainsi qu’une option Azure gérée par 21Vianet. Pour cet exemple, sélectionnez **Microsoft Azure**.

![Capture d’écran de la fenêtre Comptes SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic-2.jpg)

Ensuite, entrez l’ID d’abonnement Azure qui se trouve sur le portail Azure. Ensuite, téléchargez un certificat de gestion Azure.

![Capture d’écran de la fenêtre Comptes SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic3b.jpg)

> [!NOTE]
Pour trouver votre ID d’abonnement Azure, vous devez utiliser le portail Azure Classic, et non le nouveau portail. En effet, SAP CAL n’est pas encore adapté pour ce nouveau modèle et il vous faut toujours le portail « classique » pour travailler avec les certificats de gestion.

La capture d’écran qui suit présente le portail classique. Dans **PARAMÈTRES**, sélectionnez l’onglet **ABONNEMENTS** pour trouver l’ID d’abonnement qui doit être entré dans la fenêtre Comptes SAP CAL.

![Capture d’écran du portail Azure Classic](./media/cal-s4h/s4h-pic4b.jpg)

À partir de **PARAMÈTRES**, basculez vers l’onglet **CERTIFICATS DE GESTION**. Le téléchargement d’un certificat de gestion donne à SAP CAL les autorisations nécessaires à la création de machines virtuelles au sein d’un abonnement client. (vous chargez le certificat de gestion précédemment téléchargé depuis SAP CAL.)

![Capture du portail Azure Classic, onglet Certificats de gestion](./media/cal-s4h/s4h-pic5.jpg)

Une boîte de dialogue s’affiche pour sélectionner le fichier de certificat téléchargé.

![Capture d’écran de la boîte de dialogue Télécharger un certificat de gestion](./media/cal-s4h/s4h-pic8.jpg)

Une fois que le certificat a été chargé, la connexion entre SAP CAL et l’abonnement Azure peut être testée au sein de SAP CAL. Un message indiquant que la connexion est valide devrait s’afficher.

![Capture d’écran de la fenêtre Comptes SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic9.jpg)

Ensuite, sélectionnez une solution devant être déployée et créer une instance.
Entrez un nom d’instance, choisissez une région Azure et définissez un mot de passe principal pour la solution.

![Capture d’écran de la fenêtre Solutions SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic10.jpg)

Après un délai variable en fonction de la taille et de la complexité de la solution (SAP CAL en donne une estimation), la solution apparaît comme « active » et prête à être utilisée.

![Capture d’écran de la fenêtre Instances SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic11.jpg)

Vous pouvez consulter certains détails de la solution, comme le genre des machines virtuelles déployées. Dans ce cas, trois machines virtuelles Azure de tailles et objectifs variables ont été créées.

![Capture d’écran de la fenêtre Instances SAP Cloud Appliance Library](./media/cal-s4h/s4h-pic12.jpg)

Sur le portail Azure classique, les machines virtuelles sont repérables car elles commencent par le nom d’instance qui a été spécifié dans SAP CAL.

![Capture d’écran présentant trois machines virtuelles dans le portail Azure Classic](./media/cal-s4h/s4h-pic13.jpg)

Il est à présent possible de se connecter à la solution via le bouton de connexion dans le portail SAP CAL. La boîte de dialogue contient un lien vers un guide d’utilisation donnant toutes les informations d’identification par défaut permettant de travailler avec la solution.

![Capture d’écran la boîte de dialogue Connexion à l’instance](./media/cal-s4h/s4h-pic14b.jpg)

Une autre option consiste à se connecter à la machine virtuelle Windows cliente et de commencer la SAP GUI préconfigurée.

![Capture d’écran de l’interface graphique SAP préconfigurée](./media/cal-s4h/s4h-pic15.jpg)

