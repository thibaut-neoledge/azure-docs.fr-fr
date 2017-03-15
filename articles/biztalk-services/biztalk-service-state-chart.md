---
title: "Tâches autorisées dans les différents états ou statuts de BizTalk Services | Microsoft Docs"
description: "Actions/opérations autorisées dans différents états MABS : arrêter, démarrer, redémarrer, suspendre, reprendre, supprimer, mettre à l échelle, mettre à jour la configuration et sauvegarder"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 331c03cd0819aa4935f9b486ff38f54d23d6a7fd
ms.openlocfilehash: e3d5f89b1c8525f791e73667d6f7cd6a999ab971
ms.lasthandoff: 11/17/2016


---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Ce que vous pouvez et ne pouvez pas faire avec l’état du service BizTalk
Selon l'état actuel du service BizTalk, vous pouvez effectuer ou non certaines opérations sur celui-ci.

Par exemple, vous approvisionnez un nouveau service BizTalk dans le portail Azure Classic. Quand il se termine correctement, le service BizTalk est à l’état `active`. À l’état actif, vous pouvez arrêter, interrompre et supprimer le service BizTalk. Si vous arrêtez le service BizTalk et que l’arrêt échoue, le service BizTalk passe à l’état `StopFailed`. À l’état `StopFailed`, vous pouvez redémarrer le service BizTalk. Si vous tentez une opération non autorisée, par exemple une reprise, l’erreur suivante se produit :

`Operation not allowed`

## <a name="view-the-possible-states"></a>Afficher les états possibles

Les tableaux ci-dessous répertorient les opérations ou les actions qui peuvent être effectuées selon l’état du service BizTalk. Un ✔ signifie que l'opération peut être effectuée dans cet état. Une case vide signifie que l'opération ne peut pas être effectuée dans cet état.

| État du service | Démarrer | Arrêter | Redémarrer | Interrompre | Reprendre | Supprimer | Mettre à l'échelle | Mettre à jour <br/> Configuration | Backup  |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Actif |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Désactivé |  |  |  |  |  | ✔ | |  |  | 
| Interrompu |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Arrêté | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Échec de mise à jour du service |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Voir aussi
* [Créer un service BizTalk à l’aide du portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [Ce que vous pouvez faire sous les onglets Tableau de bord, Surveiller et Mettre à l’échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Ce que vous obtenez avec les éditions Développeur, De base, Standard et Premium dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Guide pratique pour sauvegarder et restaurer un service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Qu’est-ce que la limitation dans BizTalk Services ?](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Récupérer le nom et les valeurs de clés de l’émetteur du contrôle d’accès et Service Bus pour votre service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)


