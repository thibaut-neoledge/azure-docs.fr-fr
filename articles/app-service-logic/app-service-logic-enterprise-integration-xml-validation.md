<properties 
	pageTitle="Vue d’ensemble de la validation XML et d’Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
	description="Découvrez le fonctionnement de la validation dans Enterprise Integration Pack et vos applications logiques" 
	services="app-service\logic" 
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
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Intégration d’entreprise avec la validation XML

## Vue d'ensemble
Souvent, dans les scénarios d’entreprise à entreprise (B2B), les partenaires au sein d’un contrat doivent confirmer que les messages qu’ils échangent sont valides avant de commencer le traitement des données. Dans Enterprise Integration Pack, vous pouvez utiliser le connecteur XML Validation pour valider les documents par rapport à un schéma prédéfini.

## Comment valider un document avec le connecteur XML Validation ?
1. Créez une application logique et [liez-la à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique") qui contient le schéma que vous utiliserez pour valider les données XML.
2. Ajoutez un déclencheur **Demande - Quand une demande HTTP est reçue** à votre application logique ![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)
3. Ajouter l’action **Validation XML** en sélectionnant d’abord **Ajouter une action**
4. Entrez *xml* dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser
5. Sélectionnez **Validation XML** ![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)
6. Sélectionnez la zone de texte **CONTENU** ![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Sélectionnez la balise body en tant que contenu qui sera validé. ![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)
8. Sélectionnez la zone de liste **NOM DU SCHÉMA** et choisissez le schéma que vous souhaitez utiliser pour valider le *contenu* entré ci-dessus ![](./media/app-service-logic-enterprise-integration-xml/xml-4.png)
9. Enregistrez votre travail ![](./media/app-service-logic-enterprise-integration-xml/xml-5.png)

À ce stade, vous avez terminé de configurer votre connecteur de validation. Dans une application réelle, vous souhaiterez peut-être stocker les données validées dans une application métier, comme SalesForce. Vous pouvez facilement ajouter une action pour envoyer la sortie de la validation à SalesForce.

Vous pouvez maintenant tester votre action de validation en effectuant une demande au point de terminaison HTTP.

## Étapes suivantes

[En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

<!---HONumber=AcomDC_0727_2016-->