<properties 
	pageTitle="Suivre des messages B2B" 
	description="Cette rubrique décrit le suivi du traitement B2B" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java" 
	authors="harishkragarwal" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration" 
	ms.date="02/27/2015"
	ms.author="hariag"/>


# Suivre des messages B2B

## Informations de suivi B2B
La communication B2B implique un traitement des messages entre des partenaires commerciaux. Les relations sont définies comme des accords entre deux partenaires commerciaux. Une fois la communication établie, il doit exister un moyen de contrôler si elle se produit comme prévu. 
Dans le cadre de l'intégration des applications API B2B à Azure App Services, nous avons activé les données de suivi, qui sont également exposées via le portail Azure. 

## AS2
Une fois que vous avez créé une instance d'une application API AS2, accédez à cette instance et à la partie Suivi. Vous pourrez alors visualiser toutes les informations de suivi AS2 et les filtrer via les panneaux de filtres exposés.

![][1]  

## EDIFACT
Une fois que vous avez créé une instance d'une application API EDIFACT, accédez à cette instance et à la partie Suivi. Vous pourrez alors visualiser toutes les informations de suivi EDIFACT et les filtrer via les filtres exposés.
Vous pourrez aussi voir les données au niveau de l'échange, au niveau du groupe et au niveau des documents informatisés. 

Si des lots ont été créés dans le cadre des accords EDIFACT dans l'application API de Gestion des partenaires commerciaux associée, la partie Traitement par lots répertorie tous ces lots. Vous pourriez alors parcourir un lot pour voir le message qui constitue le message actif (le cas échéant) ainsi que les informations des lots qui se sont terminés auparavant.

![][2]      

## X 12
Une fois que vous avez créé une instance d'une application API X12, accédez à cette instance et à la partie Suivi. Vous pourrez alors visualiser toutes les informations de suivi X12 et les filtrer via les filtres exposés.
Vous pourrez aussi voir les données au niveau de l'échange, au niveau du groupe et au niveau des documents informatisés. 

Si des lots ont été créés dans le cadre des accords X12 dans l'application API de Gestion des partenaires commerciaux associée, la partie Traitement par lots répertorie tous ces lots. Vous pourriez alors parcourir un lot pour voir le message qui constitue le message actif (le cas échéant) ainsi que les informations des lots qui se sont terminés auparavant. 

X12 et EDIFACT ont des vues de suivi similaires. 

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!--HONumber=49--> 