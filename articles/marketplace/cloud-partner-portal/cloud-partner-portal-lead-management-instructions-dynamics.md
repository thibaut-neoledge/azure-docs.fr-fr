---
title: "Instructions de gestion des prospects pour Dynamics CRM Online sur la Place de marché Microsoft Azure | Microsoft Docs"
description: "Cet article guide les éditeurs, étape par étape, dans la configuration de leur gestion des prospects avec Dynamics CRM Online."
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 05b076bf173d0fbff4baaf44c57ce215dbf23979
ms.lasthandoff: 04/18/2017


---

# <a name="lead-management-instructions-for-dynamics-crm-online"></a>Instructions de gestion des prospects pour Dynamics CRM Online

Ce document contient des instructions pour la configuration de votre système Dynamics CRM Online afin que Microsoft puisse vous fournir des prospects.

> [!NOTE]
> **Autorisations utilisateur nécessaires pour effectuer les étapes suivantes :** vous devez être un administrateur sur votre instance Dynamics CRM Online pour installer une solution, et vous devez être un administrateur des clients pour créer un compte de service pour le service des prospects.

**Installation de la solution :** <br/>
1. Téléchargez les [solutions Microsoft Marketplace Lead Writer](https://testdriveaccount.blob.core.windows.net/testdrivecon/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) et enregistrez-les localement.

2. Accédez aux paramètres de Dynamics CRM Online.  <br/>
(Si vous ne voyez pas les options telles que l’image ci-dessous, cela signifie que vous ne disposez pas des autorisations requises) <br/>
![Image de configuration de Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.    Cliquez sur Importer, puis sélectionnez la solution que vous avez téléchargée à l’étape 1.  <br/>
![Image d’importation de Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.    Achevez l’installation de la solution.

**Autorisations de l’utilisateur :** <br/>
Nous avons besoin de partager des informations de compte de service en notre sein afin que nous puissions autoriser l’écriture de prospects dans votre instance Dynamics CRM. Les étapes ci-dessous vous guident dans la création du compte de service et dans l’attribution des autorisations. <br/>
1.    Accédez au [Portail d’administration Office 365](https://go.microsoft.com/fwlink/?LinkId=225975).

2.    Cliquez sur le titre « Admin » : <br/>
![Image de configuration d’administrateur Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3.    Cliquez sur « Ajouter un utilisateur ».<br/>
![Image d’ajout d’utilisateur Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4.    Créez un utilisateur pour le service d’écriture de prospect. Veillez aux points suivants 
*  Fournissez un mot de passe et désactivez la case à cocher « Amener cet utilisateur à modifier son mot de passe lors de sa première connexion »
*     Sélectionnez le rôle « Utilisateur (pas d’accès administrateur) »
*     Sélectionnez une licence de produit, comme illustré ci-dessous (Vous serez facturé pour la licence sélectionnée. La solution fonctionnera également avec licence Dynamics CRM Online De base) <br/>
![Image d’ajout d’utilisateur Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

5.  Accédez à Dynamics CRM Online en sélectionnant Paramètres -> Sécurité.<br/>
![Image des paramètres de sécurité Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

6.  Sélectionnez l’utilisateur que vous avez créé à l’étape 4, cliquez sur Gérer les rôles dans le ruban du haut, puis attribuez le rôle en tant que Microsoft Marketplace Lead Writer, comme indiqué ci-dessous : <br/>
![Image de gestion des rôles Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)<br/>

Ce rôle est créé par la solution que vous avez importée, et dispose uniquement des autorisations d’écrire les prospects et de connaître la version de la solution pour garantir la compatibilité.

7.  Pour Dynamics 365 8.2, une autorisation supplémentaire est requise pour le rôle Microsoft Marketplace Lead Writer créé par la solution que vous avez importée. Dans Sécurité, sélectionnez Rôles de sécurité, trouvez le rôle Microsoft Marketplace Lead Writer, choisissez-le, puis modifiez le paramètre de création/lecture/écriture pour les Paramètres d’interface utilisateur de l’entité utilisateur sous l’onglet Enregistrements principaux illustré ci-dessous : <br/>
![Image de rôles de sécurité Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)<br/>
![Image de gestion des rôles de sécurité Dynamics](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)<br/>


Enfin, entrez vos informations d’accès dans les champs **URL**, **Nom d’utilisateur** et **Mot de passe** sur le portail Microsoft Cloud Partner.
