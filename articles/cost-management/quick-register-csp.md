---
title: "S’inscrire à l’aide des informations de partenaire CSP à la Gestion des coûts Azure | Microsoft Docs"
description: "Utilisez vos informations de partenaire CSP pour vous inscrire à la Gestion des coûts Azure par Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/25/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: a7bd4aed7dbcde803c2106b8c053e8a2412eea99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>S’inscrire au programme pour les partenaires CSP et afficher les données de coût

En tant que partenaire CSP, vous pouvez vous inscrire à la Gestion des coûts Azure par Cloudyn. Votre inscription vous donne accès au portail Cloudyn. Ce guide de démarrage rapide décrit le processus d’inscription nécessaire pour créer un abonnement d’évaluation Cloudyn et vous connecter au portail Cloudyn. Il explique également comment afficher immédiatement des données de coût.

Pour vous inscrire, vous devez être un administrateur de programme partenaires ayant accès à l’API Espace partenaires. La configuration de l’API Espace partenaires est requise pour l’authentification et l’accès aux données. Pour plus d’informations, consultez Se connecter à l’API Espace partenaires.

## <a name="log-in-to-azure"></a>Connexion à Azure

- Connectez-vous au Portail Azure à l’adresse http://portal.azure.com.

## <a name="create-a-trial-registration"></a>Créer une inscription d’évaluation

1. Dans le portail Azure, cliquez sur **Gestion des coûts + Facturation** dans la liste des services.
2. Sous **Vue d’ensemble**, cliquez sur **Gestion des coûts**.  
    ![Page Gestion des coûts](./media/quick-register-csp/cost-mgt-billing-service.png)
3. Sur la page **Gestion des coûts**, cliquez sur le bouton d’**accès à la Gestion des coûts** pour ouvrir la page d’inscription de Cloudyn dans une nouvelle fenêtre.
4. Sur la page d’inscription à une évaluation du portail Cloudyn, tapez le nom de votre société, sélectionnez l’option d’**administrateur de programme partenaire CSP Microsoft** et cliquez sur **Suivant**.  
5. Entrez un **ID d’application**, un **ID de commerce** et une **clé secrète d’application**, puis sélectionnez le **plan tarifaire par défaut**. Si vous n’avez pas les informations nécessaires à portée de main, connectez-vous au portail Espace partenaires à l’adresse [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) avec votre compte d’administrateur principal et procédez comme suit :
  1. Accédez à **Tableau de bord** > **Paramètres du compte** > **Gestion des applications**.
  2. Si vous avez déjà créé une application web, ignorez cette étape. Dans le cas contraire, cliquez sur **Ajouter une application Web** dans la section **Application web**.
  3. Copiez l’identificateur unique **ID d’application** à partir de votre application web.
  4. Copiez le GUID de **l’ID de commerce** à partir de votre application web.
  5. Sélectionnez la durée de validité de la clé, à savoir un ou deux ans, selon les besoins. Sélectionnez **Ajouter une clé**, puis copiez et enregistrez la valeur de la clé secrète.  
    ![Espace partenaires CSP](./media/quick-register-csp/csp-partner-center.png)
  6. Revenez à la page d’inscription et collez les informations.  
      ![Informations d’identification du compte CSP](./media/quick-register-csp/csp-reg.png)
6. Acceptez les conditions d’utilisation, puis validez les informations. Cliquez sur **Suivant** pour autoriser Cloudyn à collecter des données de ressource Azure. Les données collectées incluent les données d’utilisation, de performances, de facturation et de balise de vos abonnements.  
7. L’option pour **inviter d’autres parties prenantes** permet d’ajouter des utilisateurs en tapant leurs adresses e-mail. Lorsque vous avez terminé, cliquez sur **Suivant**. L’ajout de toutes vos données de facturation à Cloudyn prend environ deux heures.
8. Cliquez sur le bouton d’**accès à Cloudyn** pour ouvrir le portail Cloudyn. La page de **gestion des comptes cloud** devrait alors afficher les informations de votre compte CSP inscrit.

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez utilisé vos informations CSP pour vous inscrire à la Gestion des coûts. Vous vous êtes également connecté au portail Cloudyn et avez affiché des données de coût. Pour en savoir plus sur la Gestion des coûts Azure par Cloudyn, poursuivez avec le didacticiel sur la Gestion des coûts.

> [!div class="nextstepaction"]
> [Afficher les données de coût](./tutorial-review-usage.md)
