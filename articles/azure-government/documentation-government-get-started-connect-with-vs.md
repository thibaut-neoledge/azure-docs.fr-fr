---
title: "Connexion à Azure Government avec Visual Studio | Microsoft Docs"
description: "Informations sur la gestion de votre abonnement dans Azure Government en vous connectant à Visual Studio"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: faf269aa-e879-4b0e-a5ba-d4110684616a
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: d49b2c6afc48cda01045cddb3844c5234e72de37
ms.lasthandoff: 02/21/2017


---


# <a name="connecting-via-visual-studio"></a>Connexion via Visual Studio
Visual Studio est utilisé par les développeurs pour gérer facilement leurs abonnements Azure lors de la création de solutions.  Visual Studio ne vous permet actuellement pas de configurer une connexion à Azure Government dans l’interface utilisateur.  

### <a name="updating-visual-studio-for-azure-government"></a>Mise à jour de Visual Studio pour Azure Government
Pour permettre à Visual Studio de se connecter à Azure Government, vous devez mettre à jour le registre.

1. Fermez Visual Studio
2. Créez un fichier texte nommé **VisualStudioForAzureGov.reg**
3. Copiez et collez le texte suivant dans **VisualStudioForAzureGov.reg** :
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Enregistrez le fichier, puis exécutez-le en double-cliquant dessus.  Vous êtes invité à fusionner le fichier dans votre registre.
5. Démarrez Visual Studio et commencez à utiliser [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md)

> [!NOTE]
> Une fois cette clé de registre définie, seuls les abonnements Azure Government sont accessibles.  Les abonnements que vous avez configurés précédemment sont toujours visibles mais ils ne fonctionnent pas, car Visual Studio est maintenant connecté à Azure Government au lieu d’Azure Public.  Consultez la section suivante pour connaître les étapes permettant d’annuler les modifications.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Suppression de la connexion de Visual Studio à Azure Government
Pour permettre à Visual Studio de se connecter à Azure Public, vous devez supprimer les paramètres du registre qui permettent la connexion à Azure Government.

1. Fermez Visual Studio
2. Créez un fichier texte nommé **VisualStudioForAzureGov_Remove.reg**
3. Copiez et collez le texte suivant dans **VisualStudioForAzureGov_Remove.reg** :
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Enregistrez le fichier, puis exécutez-le en double-cliquant dessus.  Vous êtes invité à fusionner le fichier dans votre registre.
5. Démarrez Visual Studio

> [!NOTE]
> Une fois cette clé de registre modifiée, vos abonnements Azure Government s’affichent mais ne sont pas accessibles.  Ils peuvent être supprimés en toute sécurité.
> 
> 

