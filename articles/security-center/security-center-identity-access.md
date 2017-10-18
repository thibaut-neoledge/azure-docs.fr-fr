---
title: "Surveiller l’identité et l’accès dans Azure Security Center | Microsoft Docs"
description: "Découvrez comment utiliser les fonctionnalités d’identité et d’accès dans Azure Security Center pour surveiller les problèmes liés à l’activité d’accès et à l’identité de vos utilisateurs."
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: 842045fbcb5b4f661cc006a207f4087370a7b4ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-identity-and-access-in-azure-security-center"></a>Surveiller l’identité et l’accès dans Azure Security Center
Cet article vous aide à utiliser Azure Security Center pour surveiller l’identité et l’activité d’accès d’un utilisateur.

## <a name="why-monitor-identity-and-access"></a>Pourquoi surveiller l’identité et l’accès ?
L’identité doit être le plan de contrôle de votre entreprise. La protection de votre identité doit être votre priorité absolue. Auparavant, les organisations étaient entourées de périmètres définis. Il s’agissait de l’une des principales lignes de défense. Désormais, comme de plus en plus d’applications et de données sont déplacées vers le cloud, l’identité devient le nouveau périmètre.

Le fait de surveiller vos activités d’identité vous permet de prendre des mesures avant qu’un événement ne survienne, ou des mesures réactives, pour contrer une tentative d’attaque. Le tableau de bord Identité et accès vous offre une vue d’ensemble de l’état de votre identité. Cela inclut les éléments suivants :

* Nombre d’échecs de connexion. 
* Comptes d’utilisateur utilisés lors de ces tentatives.
* Comptes verrouillés.
* Comptes dont le mot de passe a été modifié ou réinitialisé. 
* Nombre de comptes actuellement connectés.

## <a name="monitor-identity-and-access-activities"></a>Surveiller les activités d’identité et d’accès
Pour visualiser les activités en cours liées à l’identité et l’accès, vous devez accéder au tableau de bord **Identité et accès**.

1. Ouvrez le tableau de bord **Security Center**.

2. Dans le volet gauche, dans **Prévention**, cliquez sur **Identité et accès**. Si vous avez plusieurs espaces de travail, un sélecteur s’affiche.

    ![Sélection de l’espace de travail](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > Si la colonne située à l’extrême droite affiche le message **PLAN DE MISE À NIVEAU**, cela signifie que cet espace de travail utilise l’abonnement gratuit. Effectuez la mise à niveau vers l’abonnement Standard pour pouvoir utiliser cette fonctionnalité. Si la colonne située à l’extrême droite affiche le message **MISE À JOUR REQUISE**, mettez à jour [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) pour pouvoir utiliser cette fonctionnalité. Pour en savoir plus sur le plan de tarification, consultez la tarification dans Azure Security Center. 
    > 
3. Si vous avez plusieurs espaces de travail à examiner, hiérarchisez l’examen en fonction de la colonne **ÉCHECS D’OUVERTURES DE SESSIONS**. Cette colonne indique le nombre actuel de tentatives de connexion ayant échoué dans cet espace de travail. Sélectionnez l’espace de travail que vous souhaitez utiliser. Le tableau de bord **Identité et accès** s’affiche.

    ![Identité et accès](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. Les informations disponibles dans ce tableau de bord peuvent vous aider à identifier immédiatement une activité suspecte potentielle. Ce tableau de bord est divisé en trois zones principales :

    a. **Posture d’identité**. Résume les activités d’identité qui surviennent dans cet espace de travail.

    b. **Échecs d’ouvertures de sessions**. Vous aide à identifier rapidement la cause principale de l’échec d’une tentative d’ouverture de session. Affiche la liste des 10 comptes présentant le plus grand nombre de tentatives ayant échoué.

    c. **Nombre de connexions dans le temps**. Vous permet d’identifier rapidement le nombre d’ouvertures de session au fil du temps. Ce champ affiche une liste des principales tentatives de connexion de compte.
    
Quelle que soit la mosaïque sélectionnée, le tableau de bord qui s’affiche se base sur la requête de recherche dans les journaux. Les seules différences sont le type de requête et le résultat. Vous pouvez toujours choisir un élément, un ordinateur par exemple, pour consulter les données pertinentes. 

## <a name="see-also"></a>Voir aussi
Dans cet article, vous avez vu comment surveiller l’identité et l’accès dans Azure Security Center. Pour plus d’informations sur Security Center, consultez les articles suivants :

* [Gérer et répondre aux alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Apprenez à gérer les alertes et à répondre aux incidents de sécurité dans Security Center.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md). découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Comprendre les alertes de sécurité dans Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). En savoir plus sur les différents types d’alertes de sécurité.
* [Guide de résolution des problèmes d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Apprenez à résoudre les problèmes fréquents dans Azure Security Center. 
* [FAQ du Centre de sécurité Azure](security-center-faq.md). Cherchez des réponses aux questions fréquentes concernant l’utilisation de Security Center.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/). accédez à des billets de blog sur la sécurité et la conformité Azure.

