---
title: "Gérer les problèmes de protection du point de terminaison avec Azure Security Center | Microsoft Docs"
description: "Découvrez comment gérer les problèmes de protection du point de terminaison dans Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2017
ms.author: terrylan
ms.openlocfilehash: abbcb0a8e0206d78ca94520dfa81ab92506c47af
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2017
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Gérer les problèmes de protection du point de terminaison avec Azure Security Center
Azure Security Center surveille l’état de protection des logiciels anti-programme malveillant et l’indique dans le panneau Endpoint protection issues (Problèmes de protection du point de terminaison). Security Center met en évidence les problèmes, tels que les menaces détectées et une protection insuffisante, qui peuvent rendre vos machines virtuelles et ordinateurs vulnérables aux menaces des logiciels anti-programme malveillant. En utilisant les informations dans **Endpoint protection issues** (Problèmes de protection du point de terminaison), vous pouvez déterminer un plan pour résoudre les problèmes identifiés.

Security Center signale les problèmes de protection du point de terminaison suivants :

- Endpoint protection not installed on Azure VMs (Protection du point de terminaison non installée sur des machines virtuelles Azure) : une solution anti-programme malveillant prise en charge n’est pas installée sur ces machines virtuelles Azure.
- Endpoint protection not installed on non-Azure computers (Protection du point de terminaison non installée sur des ordinateurs autres qu’Azure) : un logiciel anti-programme malveillant pris en charge n’est pas installé sur ces ordinateurs autres qu’Azure.
- Intégrité de la protection du point de terminaison :

   - Signature obsolète : une solution anti-programme malveillant est installée sur ces machines virtuelles et ordinateurs, mais elle ne dispose pas des dernières signatures de logiciels anti-programme malveillant.
   - Pas de protection en temps réel : une solution anti-programme malveillant est installée sur ces machines virtuelles et ordinateurs, mais elle n’est pas configurée pour la protection en temps réel.   Le service peut être désactivé ou Security Center peut ne pas être en mesure d’obtenir l’état, car la solution n’est pas prise en charge. Consultez [Intégration des partenaires dans Azure Security Center](security-center-partner-integration.md) pour obtenir la liste de solutions prises en charge.
   - Pas de rapport : une solution anti-programme malveillant est installée, mais ne crée pas de rapport de données.
   - Inconnu : une solution anti-programme malveillant est installée, mais son état est inconnu ou signale une erreur inconnue.

   > [!NOTE]
   > Consultez [Intégrer des solutions de sécurité](security-center-partner-integration.md#integrated-azure-security-solutions) pour obtenir la liste des solutions de sécurité de protection de point de terminaison intégrées à Security Center.
   >
   >

## <a name="implement-the-recommendation"></a>Implémenter la recommandation
Les problèmes de protection du point de terminaison sont présentés sous forme de recommandations dans Security Center.  Si votre environnement est vulnérable aux menaces de logiciels anti-programme malveillant, cette recommandation s’affichera dans **Recommandations** et dans **Calcul**. Pour consulter le **tableau de bord Endpoint protection issues** (Problèmes de protection du point de terminaison), vous devez suivre le flux de travail Calcul.

Dans cet exemple, nous utilisons **Calcul**.  Nous allons examiner comment installer des logiciels anti-programme malveillant sur des machines virtuelles Azure et sur des ordinateurs autres qu’Azure.

## <a name="install-antimalware-on-azure-vms"></a>Installer des logiciels anti-programme malveillant sur des machines virtuelles Azure

1. Dans le menu principal de Security Center, sélectionnez **Calcul** ou **Vue d’ensemble**.

   ![Sélectionner Calcul][1]

2. Dans **Calcul**, sélectionnez **Endpoint protection issues** (Problèmes de protection du point de terminaison). Le tableau de bord **Endpoint protection issues** (Problèmes de protection du point de terminaison) s’ouvre.

   ![Sélectionner Endpoint Protection issues (Problèmes de protection du point de terminaison)][2]

   La partie supérieure du tableau de bord indique :

   - Installed endpoint protection providers (Fournisseurs de protection du point de terminaison installé) : répertorie les différents fournisseurs identifiés par Security Center.
   - Installed endpoint protection health state (État d’intégrité de protection du point de terminaison installé) : affiche l’état d’intégrité des machines virtuelles et des ordinateurs ayant une solution de protection du point de terminaison installée. Le graphique montre le nombre de machines virtuelles et d’ordinateurs sains et le nombre de machines virtuelles et d’ordinateurs ayant une protection insuffisante.
   - Programmes malveillants détectés : affiche le nombre de machines virtuelles et d’ordinateurs pour lesquels Security Center a détecté un programme malveillant.
   - Attacked computers (Ordinateurs attaqués) : affiche le nombre de machines virtuelles et d’ordinateurs pour lesquels Security Center signale des attaques par des programmes malveillants.

   Une liste des problèmes de protection du point de terminaison se trouve dans la partie inférieure du tableau de bord. Elle comprend les informations suivantes :  

   - **TOTAL** : le nombre de machines virtuelles et d’ordinateurs concernés par le problème.
   - Une barre regroupant le nombre de machines virtuelles et d’ordinateurs concernés par le problème. Les couleurs dans la barre déterminent la priorité :

      - Rouge : priorité élevée ; le problème doit être traité immédiatement.
      - Orange : priorité moyenne ; le problème doit être traité dès que possible.

3. Sélectionnez **Endpoint protection not installed on Azure VMs** (Protection du point de terminaison non installée sur des machines virtuelles Azure).

   ![Sélectionner Endpoint protection not installed on Azure VMs (Protection du point de terminaison non installée sur des machines virtuelles Azure)][3]

4. Dans **Endpoint protection not installed on Azure VMs** (Protection du point de terminaison non installée sur des machines virtuelles Azure) se trouve une liste des machines virtuelles Azure n’ayant pas de logiciel anti-programme malveillant installé.  Vous pouvez choisir d’installer des logiciels anti-programme malveillant sur toutes les machines virtuelles de la liste ou vous pouvez sélectionner chaque machine virtuelle pour y installer des logiciels anti-programme malveillant en cliquant sur la machine virtuelle spécifique.
5. Dans **Sélectionner Endpoint Protection**, sélectionnez la solution de protection du point de terminaison de votre choix. Dans cet exemple, sélectionnez **Microsoft Antimalware**.
6. Des informations supplémentaires sur la solution Endpoint Protection s’affichent. Sélectionnez **Créer**.

## <a name="install-antimalware-on-non-azure-computers"></a>Installer des logiciels anti-programme malveillant sur des ordinateurs autres qu’Azure

1. Revenez à **Endpoint protection issues** (Problèmes de protection du point de terminaison) et sélectionnez **Endpoint protection not installed on non-Azure computers** (Protection du point de terminaison non installée sur des ordinateurs autres qu’Azure).

   ![Sélectionner Endpoint protection not installed on non-Azure computers (Protection du point de terminaison non installée sur des ordinateurs autres qu’Azure)][4]

2. Dans **Endpoint protection not installed on non-Azure computers** (Protection du point de terminaison non installée sur des ordinateurs autres qu’Azure), sélectionnez un espace de travail. Une requête de recherche Log Analytics filtrée sur l’espace de travail s’ouvre et répertorie les ordinateurs n’ayant pas de logiciel anti-programme malveillant installé. Sélectionnez un ordinateur dans la liste pour plus d’informations.

   ![Recherche Log Analytics][5]

Un autre résultat de recherche s’ouvre avec les informations filtrées uniquement pour cet ordinateur.

  ![Recherche Log Analytics][6]

> [!NOTE]
> Nous recommandons l’approvisionnement de la protection du point de terminaison pour toutes les machines virtuelles Windows, afin d’identifier et de supprimer les virus, les logiciels espions et autres logiciels malveillants.
>
>

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a montré comment implémenter la recommandation de Security Center « Installer Endpoint Protection ». Pour en savoir plus sur l’activation de Microsoft Antimalware dans Azure, consultez le document suivant :

* [Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](../security/azure-security-antimalware.md) : découvrez comment déployer Microsoft Antimalware.

Pour plus d’informations sur Security Center, consultez les documents suivants :

* [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) : découvrez comment configurer des stratégies de sécurité.
* [Gestion des recommandations de sécurité dans Azure Security Center](security-center-recommendations.md) : découvrez comment les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) : découvrez comment surveiller l’intégrité de vos ressources Azure.
* [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) : découvrez comment gérer et résoudre les alertes de sécurité.
* [Surveillance des solutions partenaires avec Azure Security Center](security-center-partner-solutions.md) : découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
* [FAQ Azure Security Center](security-center-faq.md) : forum aux questions concernant l’utilisation de ce service.
* [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : recherchez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
