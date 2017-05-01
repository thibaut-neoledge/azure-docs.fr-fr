---
title: "Se connecter à un système SAP local dans Azure Logic Apps | Microsoft Docs"
description: "Se connecter à un système SAP local dans le workflow de l’application logique via la passerelle de données locale"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc; LADocs
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: c444f3b33908927c5d6567f87931856b4f721644
ms.lasthandoff: 04/04/2017

---

# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>Se connecter à un système SAP local à partir de Logic Apps avec le connecteur SAP 

La passerelle de données locale vous permet de gérer les données et d’accéder en toute sécurité aux ressources locales. Cette rubrique vous montre comment vous pouvez connecter des applications logiques à un système SAP local. Dans cet exemple, votre application logique demande un IDOC via HTTP et renvoie la réponse.    

> [!NOTE]
> Limitations actuelles : 
> - Votre application logique expire si toutes les étapes nécessaires pour la réponse ne se terminent pas dans la [limite de délai d’attente des demandes](./logic-apps-limits-and-config.md). Dans ce scénario, les demandes peuvent être bloquées. 
> - Le sélecteur de fichiers n’affiche pas tous les champs disponibles. Dans ce scénario, vous pouvez ajouter manuellement des chemins d’accès.

## <a name="prerequisites"></a>Composants requis

- Installez et configurez la dernière [passerelle de données locale](https://www.microsoft.com/download/details.aspx?id=53127), version 1.15.6150.1 ou ultérieure. La rubrique [Se connecter à la passerelle de données locale pour les applications logiques](http://aka.ms/logicapps-gateway) répertorie les étapes nécessaires. La passerelle doit être installée sur un ordinateur local avant de pouvoir poursuivre.

- Téléchargez et installez la dernière bibliothèque cliente SAP sur le même ordinateur que celui où vous avez installé la passerelle de données. Vous pouvez utiliser l’une des versions SAP suivantes : 
    - Serveur SAP
        - SAP ECC 6.0 Unicode
        - SAP ECC 6.0 Unicode avec EHP 4.0
        - SAP ECC 6.0 avec EHP 7.0 et toutes les versions précédentes d’EHP
 
    - Client SAP
        - SAP RFC SDK 7.20 UNICODE
        - Connecteur SAP .NET (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>Ajouter des déclencheurs et des actions pour la connexion à votre système SAP

Le connecteur SAP comporte des actions, mais pas de déclencheurs. Par conséquent, nous devons utiliser un autre déclencheur au début du workflow. 

1. Ajouter le déclencheur de demande/réponse, puis sélectionnez **Nouvelle étape**.

2. Cliquez sur **Ajouter une action**, puis sélectionnez le connecteur SAP en tapant `SAP` dans le champ de recherche :    

     ![Sélectionnez le serveur d’applications SAP ou le serveur de messagerie SAP.](media/logic-apps-using-sap-connector/sap-action.png)

3. Sélectionnez [**Serveur d’applications SAP**](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [**Serveur de messagerie SAP**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), selon votre installation SAP. Si vous n’avez pas de connexion existante, vous êtes invité à en créer une.

   1. Sélectionnez **Connect via on-premises data gateway** (Se connecter via la passerelle de données locale) et entrez les détails de votre système SAP :   

       ![Ajout d’une chaîne de connexion à SAP](media/logic-apps-using-sap-connector/picture2.png)  

   2. Sous **Passerelle**, sélectionnez une passerelle existante, ou pour installer une passerelle, sélectionnez **Installer une passerelle**.

        ![Installer une nouvelle passerelle](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. Après avoir entré toutes les informations, sélectionnez **Créer**. 
   Logic Apps configure et teste la connexion pour vérifier son bon fonctionnement.

4. Entrez un nom pour votre connexion SAP.

5. Les différentes options de SAP sont désormais disponibles. Pour trouver votre catégorie IDOC, effectuez une sélection dans la liste. Ou saisissez manuellement le chemin d’accès et sélectionnez la réponse HTTP dans le champ **Corps** :

     ![Action SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Ajoutez l’action de création d’une **réponse HTTP**. Le message de réponse doit provenir de la sortie SAP.

7. Enregistrez votre application logique. Testez-la en envoyant un IDOC par le biais de l’URL du déclencheur HTTP. Une fois l’IDOC envoyé, attendez la réponse de l’application logique :   

     > [!TIP]
     > Découvrez comment [analyser vos Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Maintenant que le connecteur SAP est ajouté à votre application logique, commencez à explorer les autres fonctionnalités :

- BAPI
- RFC

## <a name="get-help"></a>Obtenir de l’aide

Pour poser des questions ou y répondre et voir ce que font les autres utilisateurs d’Azure Logic Apps, visitez le [Forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Afin d’améliorer Azure Logic Apps ainsi que les connecteurs, votez pour des idées ou soumettez-en sur le [site de commentaires utilisateur Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment valider, transformer et utiliser d’autres fonctions BizTalk dans [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- [Connexion à des données locales](../logic-apps/logic-apps-gateway-connection.md) à partir d’applications logiques

