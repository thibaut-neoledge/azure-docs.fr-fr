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
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 3e9b95e6e9e84f8c2b615f43783d9fec5a2c09b6
ms.lasthandoff: 04/14/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>Connexion à des données locales à partir d’applications logiques

Pour accéder à des données locales, vous pouvez configurer une connexion à une passerelle de données locale pour les connecteurs Azure Logic Apps pris en charge. La procédure suivante vous guidera tout au long de l’installation et de la configuration de la passerelle de données locale à utiliser avec vos applications logiques. La passerelle de données locale prend en charge les connexions suivantes :

*   BizTalk Server
*   DB2  
*   Système de fichiers
*   Informix
*   MQ
*   MySQL
*   Oracle Database 
*   Serveur d’applications SAP 
*   Serveur de messagerie SAP
*   SharePoint pour le protocole HTTP uniquement, pas le protocole HTTPS
*   SQL Server
*   Teradata

Pour plus d’informations sur ces connexions, consultez la [liste des connecteurs Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="requirements"></a>Configuration requise

* Vous devez utiliser une adresse de messagerie professionnelle ou scolaire dans Azure pour associer la passerelle de données locale à votre compte (compte Azure Active Directory).

* Si vous utilisez un compte Microsoft (par exemple, @outlook.com), vous pouvez utiliser votre compte Azure pour [créer une adresse de messagerie professionnelle ou scolaire](../virtual-machines/windows/create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

* La passerelle de données locale doit être déjà [installée sur un ordinateur local](logic-apps-gateway-install.md).

* Vous ne pouvez associer votre installation qu’à une seule ressource de passerelle. Votre passerelle ne peut pas être revendiquée par une autre passerelle de données locale Azure. La revendication intervient (au moment de la [création, à l’étape 2 de cette rubrique](#2-create-an-azure-on-premises-data-gateway-resource)).

## <a name="install-and-configure-the-connection"></a>Installer et configurer la connexion

### <a name="1-install-the-on-premises-data-gateway"></a>1. Installation de la passerelle de données locale

Si vous ne l’avez pas encore fait, suivez ces étapes pour [installer la passerelle de données locale](logic-apps-gateway-install.md). Avant de passer aux étapes suivantes, assurez-vous que vous avez installé la passerelle de données sur un ordinateur local.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Création d’une ressource de passerelle de données locale Azure

Une fois la passerelle installée, vous devez associer votre abonnement Azure à la passerelle.

> [!IMPORTANT] 
> Vérifiez que la ressource de passerelle est créée dans la même région Azure que votre application logique. Si vous ne déployez pas la ressource de passerelle dans la même région, votre application logique ne peut pas accéder à la passerelle. 
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

## <a name="edit-your-data-gateway-connection-settings"></a>Modifier vos paramètres de connexion à une passerelle de données

Après avoir ajouté la connexion à la passerelle de données à votre application logique, il vous faudra peut-être la modifier pour en ajuster les paramètres. Cette connexion se trouve dans l’un des deux emplacements suivants :

* Dans le panneau de l’application logique, sous **Outils de développement**, sélectionnez **Connexions d’API**. Cette liste affiche toutes les connexions d’API associées à votre application logique, y compris votre connexion à la passerelle de données. Pour afficher et modifier les paramètres de la connexion, sélectionnez cette dernière.

* Dans le panneau principal Connexions d’API, vous pouvez trouver toutes les connexions d’API associées à votre abonnement Azure, y compris votre connexion à la passerelle de données. Pour afficher et modifier les paramètres de la connexion, sélectionnez cette dernière.

## <a name="next-steps"></a>Étapes suivantes

* [Exemples et scénarios courants pour les applications logiques](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Fonctionnalités d’intégration d'entreprise](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png

