---
title: "Utilisation du connecteur SAP avec la passerelle de données locale dans Azure Logic Apps | Microsoft Docs"
description: "Logic Apps vous permet de vous connecter facilement au système SAP local dans le cadre de votre workflow."
services: logic-apps
documentationcenter: dev-center-name
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 12584e9aa9c1400aba040320bd3a5f3ba3017470
ms.openlocfilehash: a483f527d15372c94fe7fe813dc49c3d6423e1e3


---
# <a name="get-started-with-the-sap-connector"></a>Prise en main du connecteur SAP 

La connectivité de cloud hybride est au cœur de Logic Apps. La passerelle de données locale vous permet de gérer les données et d’accéder en toute sécurité aux ressources locales à partir de Logic Apps. Cet article montre comment se connecter à un système SAP local selon un scénario simple : demander un IDOC sur HTTP et renvoyer la réponse.    

 > [!NOTE]
 > Ce connecteur SAP prend en charge les systèmes SAP suivants. Il existe actuellement une limitation du délai d’expiration dans Logic Apps, qui bloque les demandes sur 90 secondes. Il existe actuellement une limitation au nombre de champs à afficher dans le sélecteur de fichiers (des chemins d’accès peuvent être ajoutés manuellement).
 >
 >

## <a name="prerequisites"></a>Composants requis
- Installez et configurez la toute dernière [passerelle de données locale](https://www.microsoft.com/en-us/download/details.aspx?id=53127).  

    Installez la dernière passerelle de données locale, version 1.15.6150.1 ou version ultérieure, si vous ne l’avez pas encore. Vous trouverez de plus amples instructions dans [cet article](http://aka.ms/logicapps-gateway). La passerelle doit être installée sur un ordinateur local avant de pouvoir effectuer le reste des étapes.

- Téléchargez et installez la dernière bibliothèque cliente SAP sur le même ordinateur que celui où vous avez installé la passerelle universelle.

## <a name="use-sap-connector"></a>Utilisation du connecteur SAP

1. Nous allons créer un déclencheur de requête HTTP, puis sélectionner l’action du connecteur SAP en tapant « SAP » dans le champ de recherche.    
 ![Recherche de SAP](media/logic-apps-using-sap-connector/picture1.png)

2. Choisissez « SAP » (ApplicationHost ou MessagingHost selon votre installation SAP) et créez une connexion correspondante à l’aide de la passerelle universelle.
 - Si vous n’avez pas de connexion existante, vous êtes invité à en créer une.
 - Cochez l’option Connect via on-premises data gateway (Se connecter via la passerelle de données locale) pour afficher les champs supplémentaires.
 - Spécifiez la chaîne de nom de connexion.
 - Sélectionnez la passerelle que vous avez installée lors de l’étape précédente ou sélectionnez « Installer une passerelle » pour installer une nouvelle passerelle.   
 ![Ajout d’une chaîne de connexion à SAP](media/logic-apps-using-sap-connector/picture2.png)   
  
  > [!NOTE]
  > Il existe deux connexions SAP différentes : l’une pour [l’hôte d’application](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) et l’autre pour [l’hôte de messagerie](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)
  >
  >

3. Une fois tous les détails saisis, cliquez sur Créer. Logic Apps configure et teste la connexion pour vous assurer de son bon fonctionnement. Si tout est correct, les options de la carte que vous avez sélectionnée précédemment s’affichent. Utilisez le sélecteur de fichiers pour trouver la bonne catégorie d’IDOC ou tapez manuellement le chemin d’accès et sélectionnez la réponse HTTP dans le champ Corps.    
 ![ACTION SAP](media/logic-apps-using-sap-connector/picture3.png)

4. Créez une réponse HTTP en ajoutant une nouvelle action. Le message de réponse doit provenir de la sortie SAP.

5. Enregistrez votre application logique et teste-la en envoyant un IDOC par le biais de l’URL du déclencheur HTTP.

6. Une fois l’IDOC envoyé, attendez la réponse de l’application logique.   

  > [!TIP]
  > Découvrez comment [analyser vos Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).
  >
  >

7. Maintenant, vous disposez d’une application logique utilisant le connecteur SAP. Vous pouvez commencer à explorer les autres fonctionnalités proposées :
  - BAPI
  - RFC

## <a name="next-steps"></a>Étapes suivantes
- Découvrez [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Créez une [connexion locale](../logic-apps/logic-apps-gateway-connection.md) vers Logic Apps.


<!--HONumber=Jan17_HO3-->


