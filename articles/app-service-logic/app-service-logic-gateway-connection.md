---
title: Connexion à la passerelle de données locale à partir d’applications logiques | Microsoft Docs
description: Informations sur la création d’une connexion à la passerelle de données locale à partir d’une application logique.
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan

---
# Se connecter à la passerelle de données locale pour les applications logiques
Les connecteurs d’applications logiques pris en charge vous permettent de configurer votre connexion pour accéder aux données locales via la passerelle de données locale. La procédure suivante vous guidera tout au long de l’installation et de la configuration de la passerelle de données locale à utiliser avec une application logique.

## Composants requis
* Vous devez utiliser une adresse de messagerie professionnelle ou scolaire dans Azure pour associer la passerelle de données locale à votre compte (compte Azure Active Directory)
  * Si vous utilisez un compte Microsoft (par exemple, @outlook.com, @live.com), vous pouvez utiliser votre compte Azure pour créer adresse de messagerie professionnelle ou scolaire en [suivant les étapes décrites ici](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [!WARNING]
> Pour le moment, l’installation de la passerelle locale ne peut être effectuée qu’à l’aide d’un compte inscrit auprès de Power BI. Pendant cette période, inscrivez un compte auprès de Power BI (gratuit) pour procéder à l’installation.
> 
> 

* La passerelle de données locale doit être [installée sur un ordinateur local](app-service-logic-gateway-install.md).
* La passerelle ne doit pas avoir été revendiquée par une autre passerelle de données locale Azure ([cette revendication se produit lors de la création à l’étape 2 ci-dessous](#2-create-an-azure-on-premises-data-gateway-resource)), car une installation ne peut être associée qu’à une seule ressource de passerelle.

## Installation et configuration de la connexion
### 1\. Installation de la passerelle de données locale
Vous trouverez des informations sur l’installation de la passerelle de données locale [dans cet article](app-service-logic-gateway-install.md). La passerelle doit être installée sur un ordinateur local avant de pouvoir effectuer le reste des étapes.

### 2\. Création d’une ressource de passerelle de données locale Azure
Une fois la passerelle installée, vous devez associer votre abonnement Azure à la passerelle de données locale.

1. Connectez-vous à Azure en utilisant la même adresse de messagerie professionnelle ou scolaire utilisée lors de l’installation de la passerelle
2. Cliquez sur le bouton de ressource **Nouveau**.
3. Recherchez et sélectionnez la **passerelle de données locale**.
4. Complétez les informations pour associer la passerelle à votre compte, notamment en sélectionnant un **nom d’installation** approprié.
   
    ![Connexion à la passerelle de données locale][1]
5. Cliquez sur le bouton **Créer** pour créer la ressource

### 3\. Création d’une connexion à l’application logique dans le concepteur
Maintenant que votre abonnement Azure est associé à une instance de la passerelle de données locale, vous pouvez créer une connexion à cette passerelle à partir d’une application logique.

1. Ouvrez une application logique et choisissez un connecteur prenant en charge la connectivité locale (à ce jour, SQL Server)
2. Cochez la case **Se connecter via la passerelle de données locale**
   
    ![Création de la passerelle à l’aide du concepteur d’application logique][2]
3. Sélectionnez la **passerelle** à laquelle vous souhaitez vous connecter et fournissez toutes les autres informations de connexion requises
4. Cliquez sur **Créer** pour créer la connexion

La connexion devrait maintenant être correctement configurée pour une utilisation dans votre application logique.

## Étapes suivantes
* [Exemples et scénarios courants pour les applications logiques](app-service-logic-examples-and-scenarios.md)
* [Fonctionnalités d’intégration d'entreprise](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG

<!---HONumber=AcomDC_0803_2016-->