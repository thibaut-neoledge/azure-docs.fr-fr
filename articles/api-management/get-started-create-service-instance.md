---
title: "Créer une instance de la Gestion des API Azure | Microsoft Docs"
description: "Suivez les étapes de ce didacticiel pour créer une instance de la Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/17/2017
ms.author: apimpm
ms.openlocfilehash: 6433ea1f0eb6ad375402b998b4dfa80bded35c4b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Créer une instance du service Gestion des API Azure

Ce didacticiel décrit les étapes permettant de créer une instance de la Gestion des API à l’aide du [portail Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Composants requis

+ Un abonnement Azure actif.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-new-service"></a>Créer un service

1. Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Nouveau** > **Intégration Entreprise** > **Gestion des API**.

    Vous pouvez également choisir **Nouveau**, entrer `API management` dans la zone de recherche, puis appuyer sur Entrée. Cliquez sur **Créer**.

2. Dans la fenêtre **Service Gestion des API**, entrez un **nom** unique pour votre service Gestion des API. Vous ne pourrez plus changer ce nom.

    > [!TIP]
    > Le nom du service sert à générer un nom de domaine par défaut sous la forme *{nom}.azure-api.net.* Si vous souhaitez utiliser un nom de domaine personnalisé, consultez [Configurer un domaine personnalisé](configure-custom-domain.md). <br/>
    > Le nom du service est utilisé pour faire référence au service et la ressource Azure correspondante.

5. Sélectionnez un **abonnement** parmi les différents abonnements Azure auxquels vous avez accès.
6. Dans **Groupe de ressources**, sélectionnez la ressource (nouvelle ou existante).  Un groupe de ressources désigne une collection de ressources qui partagent un cycle de vie, des autorisations et des stratégies. En savoir plus [ici](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Dans **Emplacement**, sélectionnez la zone géographique où le service Gestion des API est créé. Seules les régions du service Gestion des API disponibles s’affichent dans la liste déroulante. 
9. Entrez un **nom d’organisation**. Ce nom est utilisé à plusieurs endroits. Par exemple, pour le titre du portail des développeurs et pour l’expéditeur des e-mails de notification.
10. Dans **E-mail de l'administrateur**, indiquez l’adresse e-mail à laquelle toutes les notifications de la **Gestion des API** seront envoyées.
11. Dans **Niveau de tarification**, définissez le niveau **Développeur** pour évaluer le service. Ce niveau n’est pas destiné à la production. Pour en savoir plus sur la mise à l’échelle des niveaux du service Gestion des API, consultez [Mettre à niveau et mettre à l’échelle](upgrade-and-scale.md).
12. Cliquez sur **Créer**.

    > [!TIP]
    > La création d’un service Gestion des API prend 20 à 30 minutes. Sélectionnez **Épingler au tableau de bord** pour rechercher plus rapidement un service qui vient d’être créé.

## <a name="next-steps"></a>Étapes suivantes

[Publier une API avec Gestion des API Azure](#api-management-getstarted-publish-api.md)