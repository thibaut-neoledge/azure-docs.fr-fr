---
title: " Création d’un compte Azure Media Services avec le portail Azure | Microsoft Docs"
description: "Ce didacticiel vous guide à travers les étapes de création d’un compte Azure Media Services à l’aide du portail Azure."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8e03f7b3d4b9c17b08aca033f44ebfc5f3332eec


---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Création d’un compte Azure Media Services à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Portail](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)
> 
> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Le portail Azure permet de créer rapidement un compte Azure Media Services (AMS). Vous pouvez utiliser ce dernier pour accéder à Media Services afin de stocker, de chiffrer, d'encoder, de gérer et de diffuser du contenu multimédia dans Azure. Lorsque vous créez un compte Media Services, vous créez également un compte de stockage associé ou en utilisez un existant, situé dans la même zone géographique que le compte Media Services.

Cet article explique certains concepts courants et indique comment créer un compte Media Services avec le portail Azure.

## <a name="concepts"></a>Concepts
L'accès à Media Services requiert deux comptes associés :

* Un compte Media Services. Votre compte vous donne accès à un ensemble de services Media Services sur le cloud, disponibles dans Azure. Un compte Media Services ne stocke pas de contenu multimédia à proprement parler. Il stocke des métadonnées relatives au contenu multimédia et aux travaux de traitement multimédia dans votre compte. Lorsque vous créez le compte, vous sélectionnez une région Media Services disponible. Cette dernière est un centre de données qui stocke les enregistrements de métadonnées pour votre compte.
  
    Les régions de disponibilité des Media Services (AMS) sont les suivantes : Europe du Nord, Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis, Sud-Est de l’Asie, Est de l’Asie, Ouest du Japon, Est du Japon. Media Services n'utilise pas de groupes d'affinités.
  
    AMS est désormais également disponible dans les centres de données suivants : Sud du Brésil, Inde-Ouest, Inde-Sud et Inde-Centre. Vous pouvez maintenant utiliser le portail Azure pour créer des comptes Media Service et effectuer les différentes tâches décrites ici. La fonctionnalité Live Encoding n'est cependant pas activée dans ces centres de données. En outre, tous les types d'unités réservées d'encodage ne sont pas disponibles dans ces centres de données.
  
  * Sud du Brésil : seules les unités réservées d’encodage standard et de base sont disponibles.
  * Inde-Ouest, Inde-Sud : 
* Un compte de stockage Azure. Les comptes de stockage doivent se trouver dans la même zone géographique que le compte Media Services. Lorsque vous créez un compte Media Services, vous pouvez choisir un compte de stockage existant dans la même région ou en créer un. Si vous supprimez un compte Media Services, les objets blob de votre compte de stockage associé ne seront pas supprimés.

## <a name="create-an-ams-account"></a>Création d’un compte AMS
Cette section montre comment créer un compte AMS.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+Nouveau** > **Web + Mobile** > **Media Services**.
   
    ![Media Services Créer](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Dans **CREATE MEDIA SERVICES ACCOUNT** (CRÉER UN COMPTE MEDIA SERVICES), entrez les valeurs requises.
   
    ![Media Services Créer](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Dans **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte Media Services se compose de chiffres ou de lettres en minuscules, sans espaces. Sa longueur est comprise entre 3 et 24 caractères.
   2. Dans Abonnement, sélectionnez l’un des abonnements Azure auxquels vous avez accès.
   3. Dans **Groupe de ressources**, sélectionnez la ressource (nouvelle ou existante).  Un groupe de ressources désigne une collection de ressources qui partagent un cycle de vie, des autorisations et des stratégies. En savoir plus [ici](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Dans **Emplacement**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements multimédias et les métadonnées de votre compte Media Services. Cette région servira à traiter et diffuser vos médias. Seules les régions Media Services disponibles s’affichent dans la liste déroulante. 
   5. Dans **Compte de stockage**, sélectionnez le compte de stockage qui fournira le stockage d’objets blob du contenu multimédia provenant de votre compte Media Services. Vous pouvez sélectionner un compte de stockage dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.
      
       Pour en savoir plus sur le stockage, cliquez [ici](../storage/storage-introduction.md).
   6. Sélectionnez **Épingler au tableau de bord** pour voir la progression du déploiement du compte.
4. Cliquez sur **Créer** en bas du formulaire.
   
    Une fois créé, le compte prend l’état **En cours d’exécution**. 
   
    ![Media Services Paramètres](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Pour gérer votre compte AMS (par exemple, charger des vidéos, encoder des éléments multimédias ou surveiller la progression de tâches), utilisez la fenêtre **Paramètres** .

## <a name="manage-keys"></a>Gérer les clés
Vous avez besoin du nom de compte et des informations de clé primaire pour accéder par programme au compte Media Services.

1. Dans le portail Azure, sélectionnez votre compte. 
   
    La fenêtre **Paramètres** s’affiche sur la droite. 
2. Dans la fenêtre **Paramètres**, sélectionnez **Clés**. 
   
    La fenêtre **Gérer les clés** affiche le nom du compte ainsi que les clés primaires et secondaires. 
3. Cliquez sur le bouton de copie pour copier les valeurs.
   
    ![Media Services Clés](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez maintenant télécharger des fichiers dans votre compte AMS. Pour plus d’informations, consultez la section [Téléchargement de fichiers dans un compte Media Services à l’aide du portail Azure](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO2-->


