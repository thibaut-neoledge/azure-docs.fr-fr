---
title: Liste de tous vos travaux Azure Import/Export | Microsoft Docs
description: "Découvrez comment répertorier tous les travaux d’un abonnement qui sont liés au service Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.lasthandoff: 03/30/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Énumération des travaux dans le service Azure Import/Export
Pour énumérer tous les travaux d’un abonnement, appelez l’opération [Répertorier les travaux](/rest/api/storageimportexport/jobs#Jobs_List). `List Jobs` retourne une liste de travaux, ainsi que les attributs suivants :

-   le type de travail (importation ou exportation) ;

-   l’état du travail actuel ;

-   le compte de stockage associé au travail.

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de l’API REST du service Import/Export](storage-import-export-using-the-rest-api.md)

