<properties 
	pageTitle="Vue d'ensemble des partenaires et Enterprise Integration Pack | Microsoft Azure App Service" 
	description="Découvrez comment utiliser les partenaires avec Enterprise Integration Pack et vos applications logiques" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>


# En savoir plus sur les contrats et Enterprise Integration Pack

## Vue d'ensemble
Les contrats constituent la pierre angulaire des communications Business-to-Business (B2B), permettant à des entités métier de communiquer en toute transparence à l’aide de protocoles standard.

## Qu’est-ce qu'un contrat ?

En ce qui concerne Enterprise Integration Pack, un contrat est une organisation de communications conclue entre des partenaires commerciaux B2B. Un contrat est basé sur le type de communication dont les partenaires souhaitent bénéficier. Il est propre au protocole ou au transport.

L'intégration d’entreprise prend en charge trois normes de protocole/transport :

- [AS2](./app-service-logic-enterprise-integration-as2.md)
- [X 12](./app-service-logic-enterprise-integration-x12.md)
- [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) (bientôt disponible)

## Pourquoi utiliser des contrats ?
Voici quelques-uns des avantages d'utiliser des contrats :
- Permet à différentes organisations et entreprises d'échanger des informations dans un format reconnu.
- Améliore l’efficacité des transactions B2B
- Les accords sont faciles à créer, gérer et utiliser lors de la création d'applications d’intégration d'entreprise

## Comment créer des contrats ?
- [Créer un contrat AS2](./app-service-logic-enterprise-integration-as2.md)
- [Créer un contrat X12](./app-service-logic-enterprise-integration-x12.md)

## Comment utiliser un contrat ?
Après avoir créé un contrat, vous pouvez l’utiliser via le portail Azure pour créer des [applications logiques](./app-service-logic-what-are-logic-apps.md "En savoir plus sur les applications logiques") avec fonctionnalités B2B.

## Comment modifier un contrat ?
Vous pouvez modifier n’importe quel contrat en procédant comme suit :
1. Sélectionnez le compte d’intégration contenant le contrat que vous souhaitez modifier.
2. Sélectionnez la mosaïque **Contrats**
3. Sélectionnez le contrat à modifier dans le panneau **Contrats**
4. Sélectionnez **Modifier** dans le menu ci-dessus
5. Dans le menu Modifier qui apparaît, effectuez vos modifications puis sélectionnez le bouton **OK** pour enregistrer les modifications

## Suppression d'un contrat
Vous pouvez supprimer un contrat en suivant ces étapes depuis le compte d’intégration contenant le contrat que vous souhaitez supprimer :
1. Sélectionnez la mosaïque **Contrats**
2. Sélectionnez le contrat à supprimer dans le panneau **Contrats**
3. Sélectionnez **Supprimer** dans le menu ci-dessus
4. Confirmez que vous souhaitez réellement supprimer le contrat
5. Notez que le contrat n’est plus répertorié dans le panneau Contrats
 

## Étapes suivantes
- [Créer un contrat AS2](./app-service-logic-enterprise-integration-as2.md)

<!---HONumber=AcomDC_0803_2016-->