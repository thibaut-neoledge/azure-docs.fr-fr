<properties 
   pageTitle="Suivre des messages B2B dans vos applications logiques dans Azure App Service | Microsoft Azure" 
   description="Cette rubrique décrit le suivi du traitement B2B" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>


# Suivre des messages B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

## Informations de suivi B2B
La communication B2B implique un traitement des messages entre des partenaires commerciaux. Les relations sont définies comme des accords entre deux partenaires commerciaux. Une fois la communication établie, il doit exister un moyen de contrôler si elle se produit comme prévu.

Nous avons implémenté le suivi des messages pour les scénarios B2B suivants : AS2, EDIFACT et X12.

## AS2
Une fois que vous avez créé une instance d’une application API AS2, accédez à cette instance et sélectionnez **Suivi**. Dans le présent document, vous pouvez afficher et filtrer toutes les informations de suivi AS2 :

![][1]

## EDIFACT
Une fois que vous avez créé une instance d’une application API EDIFACT, accédez à cette instance et sélectionnez **Suivi**. Dans le présent document, vous pouvez afficher et filtrer toutes les informations de suivi EDIFACT. Vous pouvez aussi afficher dans un même affichage les données au niveau de l’échange, au niveau du groupe et au niveau des documents informatisés.

Si des lots sont créés dans le cadre des accords EDIFACT dans l’application API de Gestion des partenaires commerciaux associée, la section Traitement par lots répertorie tous ces lots. Vous pouvez sélectionner un lot pour voir le message actif (le cas échéant) ainsi que les informations pour les lots terminés :

![][2]

## X 12
Une fois que vous avez créé une instance d’une application API X12, accédez à cette instance et sélectionnez **Suivi**. Dans le présent document, vous pouvez afficher et filtrer toutes les informations de suivi X12. Vous pouvez aussi afficher dans un même affichage les données au niveau de l’échange, au niveau du groupe et au niveau des documents informatisés.

Si des lots sont créés dans le cadre des accords X12 dans l’application API de Gestion des partenaires commerciaux associée, la section Traitement par lots répertorie tous ces lots. Vous pouvez sélectionner un lot pour voir le message actif (le cas échéant) ainsi que les informations pour les lots terminés.

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.png
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.png

<!---HONumber=AcomDC_0803_2016-->