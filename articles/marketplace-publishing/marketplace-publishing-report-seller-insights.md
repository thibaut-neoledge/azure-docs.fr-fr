---
title: "Présentation des rapports Azure Marketplace basés sur l’utilisation et du rapport Informations du vendeur| Microsoft Docs"
description: "En tant que vendeur sur Azure Marketplace, vous devez connaître le rapport élaboré à partir de l’utilisation, également appelé rapport Informations du vendeur"
services: Azure Marketplace
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: f1ffde66-98f0-4c3e-ad94-fee1f97cae03
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: v-jeana; hascipio; v-dabosl
ms.openlocfilehash: e098e27e32f7b7ae2009580a430f262aa7225206
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-your-seller-insights-report"></a>Présentation des rapports Informations du vendeur
**En quoi consistent les informations du vendeur ?**

Tous les éditeurs de services de développement facturés à l’utilisation et de machines virtuelles reçoivent des rapports mensuels de la part de Microsoft si leurs offres génèrent de l’utilisation.

**Que vais-je recevoir ?**

* **E-mail de bienvenue :** en tant que nouvel éditeur, vous recevez un e-mail de bienvenue pour vous indiquer que vous allez commencer à recevoir les rapports mensuels contenant les informations du vendeur.
* **Rapports de ventes mensuels :** si vos offres génèrent de l’utilisation, vous recevez un deuxième e-mail contenant vos rapports et des instructions sur la façon d’accéder à votre mot de passe :

  * Si vous proposez une référence de machine virtuelle ou de services de développement facturés à l’utilisation qui génère de l’utilisation, votre rapport mensuel contient les détails relatifs aux commandes, à l’utilisation, aux marchés et des informations sur les clients pour vos offres.
  * Les rapports sont verrouillés par un mot de passe que seuls vous et Microsoft connaissez et ce, afin de protéger les données de vos clients.
  * Microsoft n’envoie pas de rapport si aucune de vos offres ne génère d’utilisation au cours du mois.

## <a name="understand-your-seller-insights-report"></a>Présentation des rapports Informations du vendeur
**Commandes par référence (SKU) et type de licence : onglet Commandes sur la place de marché**

![readingreportbyorders][2]

* Des segments permettent de filtrer votre rapport en fonction de chaque élément.
* Un graphique affiche les commandes mensuelles par type de licence Azure. Chaque barre indique le nombre total de commandes pour ce mois, en fonction du type de licence Azure.
* Un graphique montre les commandes mensuelles par référence (SKU). Chaque barre affiche le total des commandes mensuelles pour toutes les références SKU réparties en fonction de leur référence (SKU).
* Un graphique représente la tendance mensuelle des commandes par type de licence Azure et type de licence Marketplace.
* Des graphiques à secteurs présentent les commandes par type de licence Azure et type de licence Marketplace.
* Un tableau indique le nombre total de commandes mensuelles par type de licence Marketplace, total par mois et total cumulé pour tous les mois.

**Commandes par référence (SKU) et type de licence : onglet Utilisation de la place de marché**

![readingreportbyusage][3]

* Des segments permettent de filtrer votre rapport en fonction de chaque élément.
* Vous devez sélectionner l’utilisation de machines virtuelles normalisée ou l’utilisation brute.
* Un graphique affiche l’utilisation mensuelle par type de licence Azure. Chaque barre indique l’utilisation totale de ce mois, répartie en fonction du type de licence Azure.
* Un graphique représente l’utilisation mensuelle par référence (SKU). Chaque barre affiche l’utilisation totale mensuelle pour toutes les références SKU réparties en fonction de leur référence (SKU).
* Un graphique affiche la tendance mensuelle d’utilisation par type de licence Azure et type de licence Marketplace.
* Des graphiques à secteurs présentent l’utilisation par type de licence Azure et type de licence Marketplace.
* Un tableau indique l’utilisation mensuelle totale par type de licence Marketplace, total par mois et total cumulé pour tous les mois.

**Onglets Données de commandes et Données d’utilisation**

Ces onglets vous fournissent les données détaillées ayant permis de générer le rapport.

![orderdata][4]

![usagedata][5]

**Utilisation par référence (SKU) et type de licence : onglet Clients**

![customerstab][6]

* Notez la clause de confidentialité
* Cet onglet contient la liste de clients par référence, les informations de profil, la date de transaction et si le client a accepté ou non de recevoir des messages promotionnels.
* Le rapport inclut le nombre de commandes par référence SKU et les totaux.

**Clauses d’exclusion de responsabilité**

![legal][1]

Lisez attentivement les clauses d’exclusion de responsabilité. Si vous avez des questions ou des commentaires, cliquez sur le lien en bas des clauses d’exclusion de responsabilité pour être redirigé vers la page de support du Marketplace.

## <a name="request-a-password-reminder"></a>Demander un rappel de mot de passe
Accédez à https://publish.windowsazure.com/ et connectez-vous avec les informations d’identification de votre compte Microsoft.
![passwordreminder][7]

Sélectionnez l’onglet **Éditeurs**. ![selectpublisherstab][8]

Rechercher l’ID d’éditeur dans l’URL :

* Utilisez cet ID comme mot de passe pour ouvrir votre fichier Excel Informations du vendeur.
  Il s’agit de votre mot de passe jusqu’à nouvel ordre.
* Nous vous recommandons d’utiliser Microsoft Office 2013 avec Windows pour lire les classeurs.  Certains utilisateurs ont signalé des problèmes liés à l’utilisation de Microsoft Office pour Mac.

![publisherid][9]

## <a name="next-steps"></a>Étapes suivantes
Si vous avez des questions sur les rapports et les informations, contactez notre équipe de support :

1. Accédez aux pages de support à l’adresse https://publish.windowsazure.com/.
2. Dans la zone **Type de problème**, sélectionnez **Rapports et analyses**.
3. Dans la zone **Catégorie**, sélectionnez **Questions relatives aux rapports**.
4. Cliquez sur **Envoyer une demande**.
   ![sellerinsightsquestions][10]

[1]: ./media/marketplace-publishing-report-seller-insights/legal.png
[2]: ./media/marketplace-publishing-report-seller-insights/readingreportbyorders.png
[3]: ./media/marketplace-publishing-report-seller-insights/readingreportbyusage.png
[4]: ./media/marketplace-publishing-report-seller-insights/orderdata.png
[5]: ./media/marketplace-publishing-report-seller-insights/usagedata.png
[6]: ./media/marketplace-publishing-report-seller-insights/customerstab.png
[7]: ./media/marketplace-publishing-report-seller-insights/passwordreminder.png
[8]: ./media/marketplace-publishing-report-seller-insights/selectpublisherstab.png
[9]: ./media/marketplace-publishing-report-seller-insights/publisherid.png
[10]: ./media/marketplace-publishing-report-seller-insights/sellerinsightsquestions.png
