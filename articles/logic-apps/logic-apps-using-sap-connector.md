---
title: "Se connecter à un système SAP local dans Azure Logic Apps | Microsoft Docs"
description: "Utiliser la passerelle de données locale pour se connecter à un système SAP local dans le workflow de l’application logique"
services: logic-apps
documentationcenter: 
author: padmavc
manager: anneta
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 72ac4936c656847fa07f1c1a37d6ddbf4ec1acf4
ms.openlocfilehash: 62b30acf324a5ed6a1b817157c86575d83b4104e


---
# <a name="use-the-sap-connector-in-your-logic-app"></a>Utiliser le connecteur SAP dans votre application logique 

La passerelle de données locale vous permet de gérer les données et d’accéder en toute sécurité aux ressources locales. Utilisez cette rubrique pour vous connecter à un système SAP local pour demander un IDOC sur HTTP et renvoyer la réponse.    

 > [!NOTE]
> Limitations actuelles :
 > - Logic Apps expire si une demande dépasse 90 secondes. Dans ce scénario, les demandes peuvent être bloquées. 
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

## <a name="add-the-sap-connector"></a>Ajouter le connecteur SAP

Le connecteur SAP comporte des actions. Il ne possède aucun déclencheur. Par conséquent, utilisez un autre déclencheur au début de votre workflow. 

1. Ajouter le déclencheur de demande/réponse, puis sélectionnez **Nouvelle étape**.
2. Cliquez sur **Ajouter une action**, puis sélectionnez le connecteur SAP en tapant `SAP` dans le champ de recherche :    
 ![Sélectionnez le serveur d’applications SAP ou le serveur de messagerie SAP](media/logic-apps-using-sap-connector/picture1.png).

3. Sélectionnez un système **SAP** : [serveur d’applications](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [serveur de messagerie](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), selon votre installation SAP. Si vous n’avez pas de connexion existante, vous êtes invité à en créer une : 
    1. Sélectionnez **Connect via on-premises data gateway** (Se connecter via la passerelle de données locale) et entrez les détails de votre système SAP :   
 ![Ajout d’une chaîne de connexion à SAP](media/logic-apps-using-sap-connector/picture2.png)  
    2. Sélectionnez une **passerelle** existante. Sinon, sélectionnez **Installer une passerelle** pour installer une nouvelle passerelle :    
 ![Installation d’une nouvelle passerelle](media/logic-apps-using-sap-connector/install-gateway.png)
  
    3. Après avoir entré toutes les informations, sélectionnez **Créer**. Logic Apps configure et teste la connexion pour vérifier son bon fonctionnement.

4. Entrez un nom pour votre connexion SAP.

5. Les différentes options de SAP sont désormais disponibles. Utilisez le sélecteur de fichiers pour trouver votre catégorie IDOC. Ou saisissez manuellement le chemin d’accès et sélectionnez la réponse HTTP dans le champ **Corps** :     
 ![ACTION SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Créez une réponse HTTP en ajoutant une nouvelle action. Le message de réponse doit provenir de la sortie SAP.

7. Enregistrez votre application logique. Testez-la en envoyant un IDOC par le biais de l’URL du déclencheur HTTP. Une fois l’IDOC envoyé, attendez la réponse de l’application logique :   

  > [!TIP]
  > Découvrez comment [analyser vos Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Maintenant que le connecteur SAP est ajouté à votre application logique, commencez à explorer les autres fonctionnalités :

  - BAPI
  - RFC

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment valider, transformer et utiliser d’autres fonctions BizTalk dans [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- Créez une [connexion locale](../logic-apps/logic-apps-gateway-connection.md) vers Logic Apps.



<!--HONumber=Feb17_HO1-->


