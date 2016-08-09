<properties 
	pageTitle="Apprendre à encoder ou à décoder des fichiers plats à l’aide d’Enterprise Integration Pack et des applications logiques | Microsoft Azure App Service | Microsoft Azure" 
	description="Utiliser les fonctionnalités d’Enterprise Integration Pack et des applications logiques pour encoder ou décoder des fichiers plats" 
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

# Intégration d'entreprise avec des fichiers plats

## Vue d'ensemble

Vous allez utiliser le connecteur d'encodage de fichier plat à partir d'une application logique qui encode le contenu XML. Par exemple, vous pouvez encoder le contenu XML avant de l’envoyer à un partenaire commercial dans un scénario B2B. L’application logique que vous créez peut obtenir son contenu XML de diverses sources y compris à partir d’un déclencheur de demande HTTP ou d’une autre application, voire d’un des nombreux [connecteurs](../connectors/apis-list.md). Consultez la [documentation sur les applications logiques](./app-service-logic-what-are-logic-apps.md "En savoir plus sur les applications logiques") pour en savoir plus sur le potentiel des applications logiques.

## Comment créer le connecteur d’encodage de fichier plat ?

Suivez ces étapes pour créer une application logique et y ajouter un connecteur d’encodage de fichier plat

1. Créez une application logique et [liez-la à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique") qui contient le schéma que vous utiliserez pour encoder les données XML.
2. Ajoutez un déclencheur **Demande - Quand une demande HTTP est reçue** à votre application logique ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
3. Ajoutez l'action d'encodage du fichier plat en procédant comme suit :
-  Sélectionnez le signe **plus**
-  Sélectionnez le lien **Ajouter une action** qui s’affiche après avoir sélectionné le signe plus (+)
-  Entrez *Plat* dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser
-  Sélectionnez l'action **Flat File Encoding** dans la liste ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
6. Sélectionnez la zone de texte **CONTENT** sur le contrôle Flat File Encoding qui s’affiche ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-3.png)
7. Sélectionnez la balise body comme contenu à encoder. La balise body permet de renseigner le champ de contenu. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-4.png)
8. Sélectionnez la zone de liste **NOM DU SCHÉMA** et choisissez le schéma que vous souhaitez utiliser pour encoder le *contenu* entré ci-dessus ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-5.png)
9. Enregistrez votre travail ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

À ce stade, vous avez terminé de configurer votre connecteur d’encodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données encodées dans une application métier, comme SalesForce, ou envoyer les données encodées à un partenaire commercial. Vous pouvez facilement ajouter une action pour envoyer la sortie de l’action d’encodage à SalesForce ou à votre partenaire commercial en utilisant l’un des autres connecteurs fournis.

Vous pouvez maintenant tester votre connecteur en envoyant une demande au point de terminaison HTTP, en incluant le contenu XML dans le corps de la demande.

## Comment créer le connecteur de décodage de fichier plat ?

### Configuration requise
Vous devez disposer d’un fichier de schéma déjà téléchargé sur votre compte d’intégration pour effectuer ces étapes.

1. Ajoutez un déclencheur **Demande - Quand une demande HTTP est reçue** à votre application logique ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-1.png)
2. Ajoutez l'action d'encodage du fichier plat en procédant comme suit :
-  Sélectionnez le signe **plus**
-  Sélectionnez le lien **Ajouter une action** qui s’affiche après avoir sélectionné le signe plus (+)
-  Entrez *Plat* dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser
-  Sélectionnez l'action **Flat File Decoding** dans la liste ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-2.png)
- Sélectionnez le contrôle **CONTENT**. Vous obtiendrez une liste du contenu des étapes précédentes, que vous pouvez utiliser comme contenu à décoder. Vous remarquerez que la balise *Body* de la demande HTTP entrante peut être utilisée comme contenu à décoder. Notez que vous pouvez également entrer le contenu à décoder directement dans le contrôle **CONTENT**. Dans cet exemple, j’ai sélectionné *Body* afin d'utiliser le corps de la demande HTTP entrante provenant de l’étape 1 de la procédure de décodage.
- Sélectionnez la balise *Body*. Notez que la balise body apparaît maintenant dans le contrôle CONTENT.
- Sélectionnez le nom du schéma que vous souhaitez utiliser pour décoder le contenu. Dans cet exemple, je sélectionne *OrderFile*. Notez que OrderFile est le nom du schéma que j’ai auparavant téléchargé sur mon compte d’intégration. ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-decode-1.png)
- Sélectionnez le lien **Enregistrer** dans le menu pour enregistrer votre travail ![](./media/app-service-logic-enterprise-integration-flatfile/flatfile-6.png)

À ce stade, vous avez terminé de configurer votre connecteur de décodage de fichier plat. Dans une application réelle, vous souhaiterez peut-être stocker les données encodées dans une application métier, comme SalesForce. Vous pouvez facilement ajouter une action pour envoyer la sortie de l'action d'encodage à SalesForce.

Vous pouvez maintenant tester votre connecteur en envoyant une demande au point de terminaison HTTP, en incluant le contenu XML à décoder dans le corps de la demande.

## En savoir plus
- [En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")

<!---HONumber=AcomDC_0727_2016-->