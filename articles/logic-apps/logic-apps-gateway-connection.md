---
title: "Accès aux données locales - Azure Logic Apps | Microsoft Docs"
description: "Comment se connecter à une passerelle de données locale pour permettre à vos applications logiques d’accéder aux données locales."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: ef7df25d8080cae41235dffb287906508d4a652d
ms.lasthandoff: 03/30/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>Connexion à des données locales à partir d’applications logiques

Pour accéder à des données locales, vous pouvez configurer une connexion à une passerelle de données locale pour les connecteurs Azure Logic Apps pris en charge. La procédure suivante vous guidera tout au long de l’installation et de la configuration de la passerelle de données locale à utiliser avec vos applications logiques.
La passerelle de données locale prend en charge les connexions de source de données suivantes :

*   BizTalk Server
*    DB2  
*   Système de fichiers
*   Informix
*   MQ
*    Oracle Database 
*   Serveur d’applications SAP 
*   Serveur de messagerie SAP
*    SQL Server

Pour plus d’informations sur ces connexions, consultez la [liste des connecteurs Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="requirements"></a>Configuration requise

* Vous devez utiliser une adresse de messagerie professionnelle ou scolaire dans Azure pour associer la passerelle de données locale à votre compte (compte Azure Active Directory).

* Si vous utilisez un compte Microsoft (par exemple, @outlook.com), vous pouvez utiliser votre compte Azure pour [créer une adresse de messagerie professionnelle ou scolaire](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

* La passerelle de données locale doit être déjà [installée sur un ordinateur local](logic-apps-gateway-install.md).

* Vous ne pouvez associer votre installation qu’à une seule ressource de passerelle. Votre passerelle ne peut pas être revendiquée par une autre passerelle de données locale Azure. La revendication intervient (au moment de la [création, à l’étape 2 de cette rubrique](#2-create-an-azure-on-premises-data-gateway-resource)).

## <a name="install-and-configure-the-connection"></a>Installer et configurer la connexion

### <a name="1-install-the-on-premises-data-gateway"></a>1. Installation de la passerelle de données locale

Si vous ne l’avez pas encore fait, suivez ces étapes pour [installer la passerelle de données locale](logic-apps-gateway-install.md). Avant de passer aux étapes suivantes, assurez-vous que vous avez installé la passerelle de données sur un ordinateur local.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Création d’une ressource de passerelle de données locale Azure

Une fois la passerelle installée, vous devez associer votre abonnement Azure à la passerelle.

> [!IMPORTANT] 
> Vérifiez que la ressource de passerelle est créée dans la même région Azure que votre application logique. Si vous ne la déployez pas dans la même région, elle ne sera pas accessible dans votre application logique. 
> 

1. Connectez-vous à Azure en utilisant la même adresse de messagerie professionnelle ou scolaire que celle utilisée lors de l’installation de la passerelle.
2. Sélectionnez **Nouveau**.
3. Recherchez et sélectionnez la **passerelle de données locale**.
4. Complétez les informations pour associer la passerelle à votre compte, notamment en sélectionnant un **nom d’installation**.
   
    ![Connexion à la passerelle de données locale][1]

5. Cliquez sur **Créer** pour créer la ressource.

### <a name="3-create-a-logic-app-connection-in-logic-app-designer"></a>3. Création d’une connexion à l’application logique dans le concepteur d’applications logiques

Maintenant que votre abonnement Azure est associé à une instance de la passerelle de données locale, vous pouvez créer une connexion à cette passerelle à partir de votre application logique.

1. Ouvrez une application logique et choisissez un connecteur prenant en charge la connectivité locale (par exemple, SQL Server).
2. Sélectionnez l’option **Se connecter via la passerelle de données locale**.
   
    ![Création de la passerelle à l’aide du concepteur d’application logique][2]

3. Sélectionnez la **passerelle** à laquelle vous souhaitez vous connecter et fournissez toutes les autres informations de connexion requises.
4. Cliquez sur **Créer** pour créer la connexion.

Votre connexion est maintenant configurée pour être utilisée par votre application logique.

## <a name="data-gateway-connection-modifications"></a>Modifications de la connexion à la passerelle de données
Une fois que vous avez ajouté à votre application logique la connexion à la passerelle de données, il vous faudra peut-être la modifier pour en ajuster les paramètres. Cette connexion se trouve dans l’un des deux emplacements suivants :
* Dans le panneau principal de l’application logique se trouve normalement un panneau de configuration pour les connexions d’API, dans la section Outils de développement. Choisissez d’afficher toutes les connexions API associées à l’application logique, dont l’une sera votre connexion à la passerelle de données. Sélectionnez-la, puis visualisez et modifiez les paramètres associés à la connexion.
* En sélectionnant le panneau principal des connexions API, vous accédez à toutes les connexions API de l’abonnement. Votre connexion à la passerelle de données figure dans cette liste. Sélectionnez-la, puis visualisez et modifiez les paramètres qui lui sont associés.

## <a name="next-steps"></a>Étapes suivantes

* [Exemples et scénarios courants pour les applications logiques](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Fonctionnalités d’intégration d'entreprise](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png

