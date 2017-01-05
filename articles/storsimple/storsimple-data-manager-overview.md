---
title: "Vue d’ensemble de Microsoft Azure StorSimple Data Manager | Microsoft Docs"
description: "Offre une vue d’ensemble du service StorSimple Data Manager (version préliminaire privée)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 32fcca540c1db317c5f11e94b2ee3359cadcc806
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73

---

# <a name="storsimple-data-manager-overview-private-preview"></a>Vue d’ensemble de StorSimple Data Manager (version préliminaire privée)

## <a name="overview"></a>Vue d'ensemble

Microsoft Azure StorSimple est une solution de stockage de cloud hybride qui gère les complexités des données non structurées couramment associées aux partages de fichiers. StorSimple utilise le stockage cloud pour étendre la solution sur site et hiérarchise automatiquement les données sur le stockage local et le stockage cloud. La protection des données intégrée, qui comprend à la fois des instantanés en local et des instantanés sur le cloud, évite d’avoir à s’appuyer sur une immense infrastructure de stockage. L’archivage et la récupération d’urgence se font également en toute transparence, le cloud faisant office d’emplacement hors site.

Le service de transformation de données présenté dans ce document permet d’accéder facilement aux données StorSimple dans le cloud. Ce service fournit des API conçues pour extraire des données de StorSimple et les présenter aux autres services Azure dans des formats qui peuvent être immédiatement utilisés. Les formats pris en charge dans cette version préliminaire sont les objets blob Azure et les ressources Azure Media Services. Cette transformation vous permet de lier facilement des services tels que Azure Media Services, Azure HDInsight, Azure Machine Learning et Azure Search afin d’exploiter les données sur un appareil local de la gamme StorSimple 8000.

Le diagramme de blocs général ci-dessous illustre cela.

![Diagramme général](./media//storsimple-data-manager-overview/high-level-diagram.png)

Ce document explique comment s’inscrire à la version préliminaire privée de ce service. Il explique également comment utiliser ce service pour écrire des applications qui utilisent des données de StorSimple et les autres services Azure dans le cloud.

## <a name="sign-up-for-data-manager-preview"></a>Inscription à la version préliminaire de Data Manager
Avant de vous inscrire au service Data Manager, passez en revue les conditions préalables suivantes.

### <a name="prerequisites"></a>Composants requis

Cet exercice suppose que vous disposiez :
* d’un abonnement Azure actif ;
* d’un accès à un appareil de la gamme StorSimple 8000 inscrit ;
* de toutes les clés associées à l’appareil de la gamme StorSimple 8000.

### <a name="sign-up"></a>Inscription

StorSimple Data Manager est disponible en version préliminaire privée. Pour vous inscrire à la version préliminaire privée de ce service, procédez comme suit :

1.  Connectez-vous au portail Azure avec l’extension StorSimple Data Manager à l’adresse : [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager). Utilisez vos informations d’identification Azure pour vous connecter.

2.  Cliquez sur l’icône **+** pour créer un service. Cliquez sur **Stockage**, puis sur **Afficher tout** dans le panneau qui s’ouvre.

    ![Rechercher l’icône de StorSimple Data Manager](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. L’icône de StorSimple Data Manager est affichée.

    ![Sélectionner l’icône de StorSimple Data Manager](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. Cliquez sur l’icône de StorSimple Data Manager, puis sur **Créer**. Sélectionnez l’abonnement que vous souhaitez activer pour la version préliminaire privée, puis cliquez sur **Sign me up!** (Inscription).

    ![Inscription](./media/storsimple-data-manager-overview/sign-me-up.png)

5. Une demande d’intégration est envoyée. Nous vous intégrerons dès que possible. Une fois votre abonnement activé, vous pouvez créer un service StorSimple Data Manager.

6. Pour accéder facilement au service StorSimple Data Manager, cliquez sur l’étoile pour l’épingler à vos favoris.

    ![Accéder à StorSimple Data Manager](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>Étapes suivantes

[Utilisez l’interface utilisateur de StorSimple Data Manager pour transformer vos données](storsimple-data-manager-ui.md).


<!--HONumber=Nov16_HO4-->


