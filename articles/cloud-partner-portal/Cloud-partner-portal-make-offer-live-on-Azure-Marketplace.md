---
title: "Comment faire pour que mon offre soit en direct sur la Place de marché Azure ?  | Microsoft Docs"
description: "Plusieurs étapes sont nécessaires afin de vérifier que le contenu marketing et l’image de machine virtuelle répondent aux exigences en matière de qualité Azure en vue de proposer votre offre en direct sur le site web."
services: marketplace-publishing
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: 4acd94e4b83164d52d2ce8db414bb99de109aca1
ms.lasthandoff: 04/12/2017


---


# <a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Proposer votre offre de machine virtuelle en direct sur la Place de marché Azure

Maintenant que vous avez rempli tous les détails de l’offre, il est temps de la publier et de la proposer en direct sur la Place de marché Azure.  Plusieurs étapes sont nécessaires afin de vérifier que le contenu marketing et l’image de machine virtuelle répondent aux exigences en matière de qualité Azure en vue de proposer votre offre en direct sur le site web.


![Offre en direct - Séquence 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)


Examinons ce processus plus en détail pour mieux comprendre ce qui se passe lors de chaque étape, et à quels moments dans ce processus vous devez intervenir pour garantir la progression de votre offre. 


## <a name="publishing-process"></a>Processus de publication

Pour démarrer le processus de publication, cliquez sur « Publier » dans l’onglet Éditeur. 

![Offre en direct - Séquence 1](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)


Sous l’onglet État, vous verrez les étapes de publication et la position de votre offre dans le processus. 


![Offre en direct - Séquence 2](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

À tout moment dans le processus de publication, vous pouvez également vous connecter et cliquer sur l’onglet Toutes les offres pour afficher l’état le plus récent de vos offres. Vous pouvez cliquer directement sur l’état de votre offre et afficher les détails concernant sa position dans le processus de publication.

![Offre en direct - Séquence 3](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

Nous allons étudier chacune des étapes de publication pour comprendre en quoi elles consistent et estimer leur durée. 

**Valider les conditions préalables (<1 jour)**

Lorsque vous cliquez sur « Publier », une vérification automatique s’effectue afin de confirmer que vous avez rempli tous les champs obligatoires pour votre offre. Si tous les champs ne sont pas renseignés, un avertissement s’affiche en regard du champ vide. Remplissez-le correctement, puis cliquez sur « Publier » à nouveau. 


Une fois cette étape terminée, une fenêtre contextuelle apparaît pour vous demander votre adresse de messagerie. Il s’agit de la messagerie sur laquelle vous recevrez les notifications d’état de publication pour le reste du processus de publication. Une fois l’adresse de messagerie communiquée, cette étape est terminée.

![Offre en direct - Séquence 4](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**Certification (<5 jours)**

Lors de cette étape, nous exécutons plusieurs tests afin de vérifier que votre image de machine virtuelle répond aux conditions de certification Azure. Vous trouverez [ici](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites) tous les conseils nécessaires pour remplir les conditions de certification.

Cette étape pouvant prendre plusieurs jours, vous pouvez vous déconnecter du portail Microsoft Cloud Partner. Nous vous enverrons un e-mail de notification si des erreurs sont à résoudre. Si tout se passe correctement, le processus passe automatiquement à l’étape d’approvisionnement. 

**Approvisionnement (<1 jour)**

Pendant cette phase, nous répliquons vos images vers tous les centres de données Azure dans le monde. Cette opération peut prendre jusqu’à 24 heures.

**Empaquetage et référencement de génération de prospects (<1 heure)**

Pendant cette phase, nous combinons le contenu technique et marketing sur la page produit du site web. 

En outre, si vous avez configuré la fonctionnalité de génération de prospect, nous vérifions que votre intégration CRM fonctionne en lui envoyant un message test. Vous verrez un enregistrement contenant des données fictives dans votre CRM ou dans votre table Azure une fois cette étape terminée. Toute la documentation concernant la génération de prospects se trouve ici.

**Offre disponible en version préliminaire**

Vous recevrez un e-mail de notification indiquant que votre offre a rempli toutes les étapes requises et qu’elle est désormais disponible en version préliminaire. Au cours de cette étape, il est recommandé d’afficher un aperçu de votre offre afin de vous assurer que tout se présente comme il convient et que votre machine virtuelle se déploie correctement dans l’environnement intermédiaire. 

**Seuls les abonnements figurant dans la liste approuvée peuvent procéder à cette vérification.***

**Validation de l’éditeur**

Lorsque vous aurez vérifié que tous les éléments fonctionnent correctement en version préliminaire, vous êtes prêt à proposer votre offre en direct. Pour ce faire, cliquez sur Démarrer sous l’onglet État afin de lancer le processus permettant à votre offre de s’exécuter dans un environnement de production et sur le site web. En règle générale,plusieurs heures peuvent s’écouler entre le moment où vous cliquez sur le bouton Démarrer et le moment où votre offre est disponible en direct sur le site web. Nous vous enverrons un e-mail de notification lorsque votre offre sera officiellement disponible en direct sur le site web.

![Offre en direct - Séquence 5](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**En direct**    

Votre offre est désormais disponible en direct sur la Place de marché et sur le portail Azure, et les clients sont en mesure d’afficher et de déployer votre machine virtuelle dans leur abonnement Azure.
À tout moment, vous pouvez cliquer sur l’onglet Toutes les offres et voir l’état de toutes vos offres répertorié dans la colonne de droite. Vous pouvez cliquer sur l’état pour afficher en détail l’état de flux de publication de votre offre.

## <a name="error-handling"></a>Gestion des erreurs

Une erreur peut se produire lors du processus de publication. Le cas échéant, vous recevrez un e-mail de notification vous informant de l’erreur et contenant des instructions pour les étapes à suivre. Vous pouvez également voir les erreurs à tout moment au cours de ce processus en cliquant sur l’onglet État. Vous verrez à quel moment du processus l’erreur s’est produite, ainsi qu’un message indiquant les éléments nécessitant votre attention. 

![Offre en direct - Séquence 6](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)


Si vous rencontrez des erreurs pendant le processus de publication, vous devez les corriger, puis cliquer sur « Publier » pour redémarrer le processus. Vous devez rependre au début des étapes de publication de la séquence Valider les conditions préalables lorsque vous republiez après avoir corrigé une erreur.

Si vous rencontrez des problèmes lors de la résolution d’une erreur, vous pouvez ouvrir une demande de support pour obtenir l’aide de nos ingénieurs du support technique.


![Offre en direct - Séquence 7](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)



## <a name="canceling-the-publishing-request"></a>Annulation de la demande de publication

Vous pouvez démarrer le processus de publication, puis vous rendre compte que vous devez annuler votre demande. Vous pouvez annuler une demande de publication uniquement si l’étape de validation de l’éditeur a été atteinte. Pour annuler, cliquez sur « Annuler la publication ». L’état de publication est réinitialisé à l’étape 1, et pour publier de nouveau, vous devez cliquer sur Publier et suivre les étapes de l’état.

![Offre en direct - Séquence 8](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)














