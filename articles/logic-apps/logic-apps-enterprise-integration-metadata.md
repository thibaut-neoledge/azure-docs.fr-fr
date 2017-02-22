---
title: "Métadonnées de compte d’intégration Azure Logic Apps | Microsoft Docs"
description: "Vue d’ensemble des métadonnées de compte d’intégration"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e5d567800d00b41ec0782216b442f437a2500928
ms.openlocfilehash: 41edd713d85790793341e100f77300f999ac8c73


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Métadonnées de compte d’intégration Azure Logic Apps

## <a name="overview"></a>Vue d'ensemble

Les partenaires, contrats, schémas, mappages ajoutés à un compte d’intégration, stockent les métadonnées avec des combinaisons clé-valeur. Vous pouvez définir des métadonnées personnalisées qui peuvent être récupérées pendant l’exécution.  Pour l’instant, les artefacts n’ont pas la possibilité de créer des métadonnées dans l’interface utilisateur. Vous pouvez utiliser les API REST pour les créer.  Les partenaires, contrats et schémas disposent de **EDIT as JSON** (MODIFIER en tant que JSON) et permet la saisie d’informations sur les métadonnées.  Dans une application logique, **Integration Account Artifact LookUp** (Recherche d’artefact de compte d’intégration) permet de récupérer des informations sur les métadonnées.

## <a name="how-to-store-metadata"></a>Stockage des métadonnées

1. Créez un [compte d’intégration](logic-apps-enterprise-integration-create-integration-account.md).   

2. Ajoutez un [partenaire](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), un [contrat](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements) ou un [schéma](logic-apps-enterprise-integration-schemas.md) au compte d’intégration.

3. Sélectionnez un partenaire, un contrat ou un schéma. Sélectionnez **Edit as JSON** (MODIFIER en tant que JSON) et entrez les informations sur les métadonnées    
![Saisir les métadonnées](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>Appelez **Integration Account Artifact LookUp** (Recherche d’artefact de compte d’intégration) à partir d’une application logique

1. Créez une [application logique](logic-apps-create-a-logic-app.md).

2. [Liez](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app) une application logique à un compte d’intégration.    

3. Créez un déclencheur, par exemple à l’aide de *Request* ou *HTTP* avant de rechercher **Integration Account Artifact LookUp** (Recherche d’artefact de compte d’intégration).  Tapez le mot clé **integration** pour rechercher **Integration Account Artifact LookUp**  
![Recherche](media/logic-apps-enterprise-integration-metadata/image2.png)

3. Sélectionnez **Integration Account Artifact LookUp** (Recherche d’artefact de compte d’intégration)  

4. Sélectionnez le **type d’artefact** et indiquez son **nom**  
![Recherche](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>Exemple de récupération des métadonnées du partenaire

1. Les métadonnées du partenaire disposent de détails relatifs à l’url de routage    
![Recherche](media/logic-apps-enterprise-integration-metadata/image6.png)

2. Dans une application logique, configurez **Integration Account Artifact LookUp** (Recherche d’artefact de compte d’intégration) et **HTTP**   
![Recherche](media/logic-apps-enterprise-integration-metadata/image4.png)

3. Pour récupérer l’URI, la vue du code doit ressembler à ce qui suit :    
![Recherche](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les contrats](logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  



<!--HONumber=Feb17_HO1-->


