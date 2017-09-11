---
title: "Installer une passerelle de données locale | Microsoft Docs"
description: "Découvrez comment installer et configurer une passerelle de données locale."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/22/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 6ef296fb98478be9240f0231c8ad39cd2a0af995
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installer et configurer une passerelle de données locale
Une passerelle de données locale est requise lorsqu’un ou plusieurs serveurs Azure Analysis Services de la même région se connectent aux sources de données locales. Pour en savoir plus sur la passerelle, consultez la page [Passerelle de données locale](analysis-services-gateway.md).

## <a name="prerequisites"></a>Composants requis
**Configuration minimale requise :**

* .NET Framework 4.5
* Version 64 bits de Windows 7 / Windows Server 2008 R2 (ou version ultérieure)

**Recommandé :**

* Processeur 8 cœurs
* 8 Go de mémoire
* Version 64 bits de Windows 2012 R2 (ou version ultérieure)

**Points importants à prendre en compte :**

* Pendant la configuration, lors de l’inscription de votre passerelle auprès d’Azure, la région par défaut de votre abonnement est sélectionnée. Vous pouvez choisir une autre région. Si vous avez des serveurs dans plusieurs régions, vous devez installer une passerelle pour chaque région. 
* La passerelle ne peut pas être installée sur un contrôleur de domaine.
* Une seule passerelle peut être installée sur un ordinateur.
* Installez la passerelle sur un ordinateur qui reste activé et qui ne se met pas en veille.
* N’installez pas la passerelle sur un ordinateur sans fil connecté à votre réseau. Les performances peuvent être réduites.


## <a name="download"></a>Télécharger
 [Télécharger la passerelle](https://aka.ms/azureasgateway)

## <a name="install"></a>Installer

1. Exécutez le programme d’installation.

2. Sélectionnez un emplacement, acceptez les termes du contrat, puis cliquez sur **Installer**.

   ![Emplacement d’installation et termes du contrat de licence](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Sélectionnez **Passerelle de données locale (recommandé)**. Azure Analysis Services ne prend pas en charge le mode personnel.

   ![Choisir le type de passerelle](media/analysis-services-gateway-install/aas-gateway-installer-shared.png)

4. Entrez un compte pour vous connecter à Azure. Le compte doit se trouver dans l’Azure Active Directory de votre locataire. Ce compte est utilisé pour l’administrateur de passerelle. 

   ![Entrer un compte pour vous connecter à Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Si vous vous connectez avec un compte de domaine, il sera mappé à votre compte professionnel dans Azure AD. Votre compte professionnel sert de compte d’administrateur de passerelle.

## <a name="register"></a>S’inscrire
Pour créer une ressource de passerelle dans Azure, vous devez inscrire l’instance locale que vous avez installée auprès du service cloud de passerelle. 

1.  Sélectionnez **Inscrivez une nouvelle passerelle sur cet ordinateur**.

    ![S’inscrire](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Saisissez un nom et une clé de récupération pour votre passerelle. Par défaut, la passerelle utilise la région par défaut de votre abonnement. Si vous souhaitez choisir une autre région, sélectionnez **Changer la région**.

   ![S’inscrire](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-resource"></a>Créer une ressource de passerelle Azure
Une fois que vous avez installé et inscrit votre passerelle, vous devez créer une ressource de passerelle dans votre abonnement Azure. Connectez-vous à Azure avec le même compte que celui utilisé lors de l’inscription de la passerelle.

1. Dans le portail Azure, cliquez sur **Création d’un nouveau service** > **Enterprise Integration** > **Passerelle de données locale** > **Créer**.

   ![Créer une ressource de passerelle](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Dans **Créer une passerelle connexion**, entrez les paramètres suivants:

    * **Nom** : entrez un nom pour votre ressource de passerelle. 

    * **Abonnement** : sélectionnez l’abonnement Azure à associer à votre ressource de passerelle. 
    Cet abonnement doit être le même que celui de vos serveurs.
   
      L’abonnement par défaut est basé sur le compte Azure que vous avez utilisé pour vous connecter.

    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez-en un.

    * **Emplacement** : sélectionnez la région dans laquelle vous avez inscrit votre passerelle.

    * **Nom de l’installation** : si votre installation de passerelle n’est pas encore sélectionnée, sélectionnez la passerelle que vous avez inscrite. 

    Une fois ces opérations effectuées, cliquez sur **Créer**.

## <a name="connect-servers"></a>Connecter les serveurs à la ressource de passerelle

1. Dans la vue d’ensemble du serveur Azure Analysis Services, cliquez sur **Passerelle de données locale**.

   ![Connecter le serveur à la passerelle](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. Dans la zone **Pick an On-Premises Data Gateway to connect** (Choisir une passerelle de données locale à connecter), sélectionnez votre ressource de passerelle, puis cliquez sur **Connect selected gateway** (Connecter la passerelle sélectionnée).

   ![Connecter le serveur à la ressource de passerelle](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Si votre passerelle n’apparaît pas dans la liste, votre serveur se trouve probablement dans une autre région que celle spécifiée lors de l’inscription de la passerelle. 

Vous avez terminé. Si vous devez ouvrir des ports ou effectuer des opérations de dépannage, veillez à consulter la page [Passerelle de données locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Étapes suivantes
* [Gérer Analysis Services](analysis-services-manage.md)   
* [Obtenir les données d’Azure Analysis Services](analysis-services-connect.md)

