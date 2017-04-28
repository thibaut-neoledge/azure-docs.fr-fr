---
title: Obtenir des prospects pour Azure | Microsoft Docs
description: "Cet article explique tout ce qu’il faut savoir sur la génération de prospects Microsoft et la manière de connecter votre système CRM pour l’intégrer dans votre pipeline de vente avec le portail Microsoft Cloud Partner."
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
ms.openlocfilehash: 6207695ede6c01f9423ddde7467d2a9b1ed35bd3
ms.lasthandoff: 04/18/2017


---

# <a name="get-customer-leads"></a>Obtenir des prospects 
Les clients sont au centre de toute entreprise prospère. Dans la transformation des modes d’acquisition de produits d’aujourd’hui, les responsables marketing doivent se concentrer sur la connexion directe avec les clients et la construction d’une relation. C’est pourquoi la génération de prospects de haute qualité est un outil essentiel pour votre cycle de vente.
Le portail Microsoft Cloud Partner vous permet de recevoir automatiquement des informations de contact client **immédiatement** après qu’un client manifeste de l’intérêt pour votre produit ou déploie celui-ci. Ce document aborde tout ce que vous devez savoir sur ces prospects générés, sur la manière de les connecter à votre système CRM, et sur la façon de les intégrer dans le pipeline des ventes.

## <a name="what-are-these-leads"></a>Quelles sont ces prospects ?
Ces prospects sont des clients qui déploient vos produits à partir de la Place de marché. Si votre produit est répertorié sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/) ou sur [AppSource](https://appsource.com/), vous pouvez recevoir des prospects de clients intéressés par le produit.
### <a name="azure-marketplace"></a>Azure Marketplace
1.    Un client a opté pour une « Version d’évaluation » de votre offre. Les versions d’évaluation constituent pour vous une opportunité accélérée de partager votre activité instantanément avec des clients potentiels sans barrières à l’entrée. Toutes les versions d’évaluation génèrent un prospect d’un client qui s’est intéressé à l’essai de votre produit pour en savoir plus.  Pour en savoir plus sur les versions d’évaluation, voir [Version d’évaluation sur la Place de marché Microsoft Azure](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Offre de version d’évaluation](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

2.    Le client consent à partager ses informations après avoir cliqué sur le bouton « Télécharger maintenant ». Ce prospect est un prospect d’intérêt initial ; nous partageons un client qui a manifesté de l’intérêt pour votre produit. Il constitue la partie supérieure de l’entonnoir d’acquisition.
 
    ![Bouton Télécharger maintenant](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

3.    Un client clique sur « Acheter » dans le [portail Azure](https://portal.azure.com/) quand il acquiert officiellement votre produit. Ce prospect est un prospect actif ; nous partageons un client qui dispose d’un abonnement Azure et qui a commencé à déployer votre produit réel.

    ![Bouton Acheter](./media/cloud-partner-portal-get-customer-leads/purchase-button.png) 

### <a name="appsource"></a>AppSource
1.    Un client a opté pour une « Version d’évaluation » de votre offre. Les versions d’évaluation constituent pour vous une opportunité accélérée de partager votre activité instantanément avec des clients potentiels sans barrières à l’entrée. Toutes les versions d’évaluation génèrent un prospect d’un client qui s’est intéressé à l’essai de votre produit pour en savoir plus.  Pour en savoir plus sur les versions d’évaluation, voir l’article concernant les [versions d’évaluation sur AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Offre de version d’évaluation](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.    Le client consent à partager ses informations après avoir cliqué sur le bouton « Télécharger maintenant ». Ce prospect est un prospect d’intérêt initial ; nous partageons un client qui a manifesté de l’intérêt pour votre produit. Il constitue la partie supérieure de l’entonnoir d’acquisition.

    ![Bouton Télécharger maintenant](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)

3.    Un client clique sur le bouton « Me contacter » de votre offre. Ce prospect est un prospect actif ; nous partageons un client qui a demandé activement à être recontacté au sujet de votre produit.

    ![Me contacter](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

## <a name="lead-data"></a>Données de prospect
Pour chaque prospect que vous recevez, les données correspondantes sont insérées dans les champs spécifiques. Puisque vous recevez des prospects à chaque étape du parcours d’acquisition de chaque client, la meilleure façon de les gérer consiste à dédupliquer et à personnaliser les suivis. De cette manière, chaque client reçoit un message approprié et vous créez à chaque fois une relation unique.

|Nom du champ|Exemples|
|----------|--------|
|LeadSource| Le format est **Source**-**Action**&#124;**Offre** <br/><br/>**Sources**<br/>« AzureMarketplace »,<br/>« AzurePortal »,<br/>« TestDrive », <br/>« SPZA » (acronyme de AppSource)<br/><br/>**Actions** :<br/>« INS » : signifie « installation ». Apparaît sur la Place de marché Microsoft Azure ou sur AppSource chaque fois qu’un client appuie sur le bouton pour acquérir votre produit.<br/><br/>« PLT » : désigne une évaluation conduite par un partenaire. Apparaît sur AppSource chaque fois qu’un client clique sur le bouton Me contacter.<br/><br/>«DNC » : signifie « Ne pas contacter ». Apparaît sur AppSource chaque fois qu’un partenaire qui a été répertorié sur la page de votre application reçoit une demandé de contact. Nous vous informons que ce client a été répertorié sur votre application, mais qu’il n’est pas nécessaire de le contacter.<br/><br/>« Créer » : apparaît uniquement à l’intérieur du portail Azure à chaque fois qu’un client achète votre offre sur son compte.<br/><br/>« StartTestDrive » : uniquement pour les versions d’évaluation ; apparaît chaque fois qu’un client démarre sa version d’évaluation.<br/><br/>**Offres** :<br/>    « checkpoint.check-point-r77-10sg-byol »,<br/>« bitnami.openedxcypress »,<br/>« docusign.3701c77e-1cfa - 4c 56-91e6-3ed0b622145a »<br/> <br/> Il s’agit de l’identificateur unique qui vous est attribué, ainsi qu’à votre offre spécifique. |
|CustomerInfo|    {<br/>"FirstName":"John",<br/>"LastName":"Smith",<br/>"Email":"jsmith@microsoft.com",<br/>"Phone":"1234567890",<br/>"Country":"US",<br/>"Company":"Microsoft",<br/>"Title":"CTO"<br/>}<br/><br/>Remarque : certaines données ne sont pas disponibles pour tous les prospects|


Nous travaillons activement à l’amélioration des prospects. S’il y a un champ de données que vous ne voyez pas ici mais que vous aimeriez avoir, veuillez [nous envoyer vos commentaires](mailto:AzureMarketOnboard@microsoft.com).

## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Comment connecter votre système CRM au portail Microsoft Cloud Partner 
Pour commencer à obtenir des prospects, nous avons basé notre connecteur de gestion des prospects sur le portail Microsoft Cloud Partner afin que vous puissiez facilement intégrer vos informations CRM. De plus, nous établissons cette connexion à votre place. Vous pouvez désormais exploiter facilement les prospects générés par la Place de marché, sans avoir à consentir de lourds efforts d’ingénierie pour opérer l’intégration avec un système externe.
 
![Connecteur de gestion des prospects](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png) Nous pouvons écrire des prospects dans un vaste éventail de systèmes CRM ou directement dans une table de stockage Azure où vous pouvez gérer les prospects comme vous le souhaitez. Voici des instructions étape par étape sur la façon de se connecter à chacune des destinations de prospects possibles : <todo use updated markdown files as links from appsource/testdrive! Rewalk through the lead connections Non json just fields required now in separate pages>

*    Dynamics CRM Online : [cliquez ici](./cloud-partner-portal-lead-management-instructions-dynamics.md) pour obtenir des instructions sur la manière de configurer Dynamics CRM Online pour obtenir des prospects. 
*    Marketo : [cliquez ici](./cloud-partner-portal-lead-management-instructions-marketo.md) pour obtenir des instructions sur la manière de configurer des prospects Marketo pour obtenir des prospects.
*    Salesforce : [cliquez ici](./cloud-partner-portal-lead-management-instructions-salesforce.md) pour obtenir des instructions sur la manière de configurer votre instance Salesforce pour obtenir des prospects.
*    Table Azure : [cliquez ici](./cloud-partner-portal-lead-management-instructions-azure-table.md) pour obtenir des instructions sur la manière de configurer votre compte de stockage Azure pour obtenir des prospects dans une table Azure. 

Une fois que vous avez correctement configuré votre destination de prospect et que vous avez publié votre offre, nous validons la connexion et vous envoyons un prospect de test. Lorsque vous visualisez l’offre avant son lancement, vous pouvez également tester votre connexion de prospect en essayant d’acquérir vous-même l’offre dans l’environnement en version préliminaire.  Il est important de s’assurer que vos paramètres de prospects restent à jour afin d’éviter de perdre des prospects. Veillez donc à mettre à jour ces connexions chaque fois que quelque chose change de votre côté.

## <a name="what-next"></a>Étapes suivantes 
Une fois les paramètres techniques configurés, vous devez incorporer ces prospects dans votre stratégie commerciale et marketing, ainsi que dans vos processus opérationnels actuels. Nous cherchons à mieux comprendre votre processus de vente global. C’est pourquoi nous tenons à travailler en étroite collaboration avec vous pour vous offrir des prospects de haute qualité et des données en quantité suffisante pour garantir votre réussite. Nous apprécions vos commentaires sur la façon dont nous pouvons optimiser et améliorer les prospects que nous vous envoyons, accompagnés de toutes les données nécessaires pour assurer la réussite de ces clients.
Faites-nous savoir si vous souhaitez formuler des [commentaires](mailto:AzureMarketOnboard@microsoft.com) et des suggestions pour permettre à votre équipe de vente de mieux exploiter les prospects de la Place de marché.




