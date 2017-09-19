---
title: "Surveillance de l’identité et de l’accès dans Azure Security Center | Microsoft Docs"
description: "Ce document vous aide à utiliser les fonctionnalités d’identité et d’accès dans Azure Security Center pour surveiller les problèmes liés à l’activité d’accès et à l’identité de votre utilisateur."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Surveillance de l’identité et de l’accès dans Azure Security Center
Ce document vous aide à utiliser Azure Security Center pour surveiller l’identité et l’activité d’accès d’un utilisateur.

## <a name="why-monitor-identity-and-access"></a>Pourquoi surveiller l’identité et l’accès ?
L’identité doit être le plan de contrôle de votre entreprise. Protéger votre identité doit être votre priorité absolue. Autrefois, les organisations étaient entourées d’un périmètre de sécurité qui constituait l’une de leurs principales défenses. Aujourd’hui, ces organisations disposent d’un plus gros volume de données et d’applications qui évoluent dans le cloud, et l’identité devient leur nouveau périmètre.

Surveiller vos activités d’identité vous permet de prendre des mesures proactives avant qu’un événement ne survienne, ou des mesures réactives pour contrer une tentative d’attaque. Le tableau de bord Identité et accès vous offre une vue d’ensemble de l’état de votre identité, notamment le nombre de tentatives de connexion ayant échoué, les comptes d’utilisateurs utilisés au cours de ces tentatives, les comptes verrouillés, les comptes pour lesquels le mot de passe a été changé ou réinitialisé et le nombre de comptes actuellement connectés.

## <a name="how-to-monitor-identity-and-access-activities"></a>Comment surveiller les activités d’identité et d’accès ?
Suivez les étapes ci-dessous pour visualiser les activités en cours liées à l’identité et l’accès. Accédez au tableau de bord **Identité et accès** :

1.  Ouvrez le tableau de bord **Security Center**.
2.  Dans le volet gauche, dans **Prévention**, cliquez sur **Identité et accès**. Si vous avez plusieurs espaces de travail, un sélecteur s’affiche.

    ![sélection de l’espace de travail](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Si **METTRE À NIVEAU LE PLAN** apparaît dans la dernière colonne, cet espace de travail utilise un abonnement gratuit. Vous devez le passer au niveau standard pour utiliser cette fonctionnalité. Si NÉCESSITE MISE À JOUR apparaît, vous devez mettre à jour [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour utiliser cette fonctionnalité. Pour en savoir plus sur le plan de tarification, consultez la tarification dans Azure Security Center. 
    > 
3. Si vous disposez de plus d’un espace de travail à analyser, privilégiez l’analyse en fonction de la colonne **ÉCHECS D’OUVERTURE DE SESSION**. Elle indique le nombre actuel d’échecs de tentative d’ouverture de session dans cet espace de travail. Sélectionnez l’espace de travail que vous souhaitez utiliser. Le tableau de bord **Identité et accès** s’affiche.

    ![identité et accès](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Les informations disponibles dans ce tableau de bord peuvent immédiatement vous aider à identifier une activité suspecte potentielle. Ce tableau de bord est divisé en trois zones principales :
    * **Posture d’identité** : résume les activités d’identité qui surviennent dans cet espace de travail.
    * **Échecs d’ouverture de session** : vous aide à identifier rapidement la cause principale de l’échec d’une tentative d’ouverture de session, et affiche une liste des dix comptes rencontrant le plus d’échecs lors de l’ouverture d’une session.
    * **Durée d’ouverture de session supplémentaire** : vous aide à identifier rapidement la durée d’ouverture supplémentaire, et affiche une liste des tentatives d’ouverture de session de compte d’un ordinateur.
    
Quelle que ce soit la vignette choisie, le tableau de bord qui s’affiche se base sur la [requête Log Search](https://docs.microsoft.com/azure/security-center/security-center-search). La seule différence est le type de requête et son résultat. Vous pouvez toujours choisir un élément, un ordinateur par exemple. Cliquez dessus et consultez les données pertinentes. 

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez vu comment surveiller l’identité et l’accès dans Azure Security Center. Pour plus d’informations sur le Centre de sécurité Azure, consultez les rubriques suivantes :

* [Gestion et résolution des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Présentation des alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center. 
* [FAQ du Centre de sécurité Azure](security-center-faq.md). forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.


