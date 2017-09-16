---
title: "Configurer les informations d’authentification nommées | Microsoft Docs"
description: "Découvrez comment fournir des informations d’identification que Visual Studio pourra utiliser pour authentifier les demandes effectuées auprès d’Azure, afin que vous puissiez publier une application dans Azure à partir de Visual Studio ou surveiller un service cloud existant."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: b75d190724da284324b0337e95a50a64902e19d8
ms.contentlocale: fr-fr
ms.lasthandoff: 09/06/2017

---
# <a name="set-up-named-authentication-credentials"></a>Configurer les informations d’authentification nommées
Pour publier une application dans Azure à partir de Visual Studio, ou pour analyser un service cloud existant, vous devez fournir des informations d’identification que Visual Studio pourra utiliser pour authentifier les demandes effectuées auprès d’Azure. Il existe plusieurs emplacements dans Visual Studio à partir desquels vous pouvez vous connecter pour fournir ces informations d’identification. Par exemple, à partir de l’Explorateur de serveurs, vous pouvez ouvrir le menu contextuel du nœud **Azure**, puis sélectionner **Se connecter à un abonnement Microsoft Azure**. Quand vous vous connectez, les informations d’abonnement associées à votre compte Azure sont disponibles dans Visual Studio. Vous n’avez donc rien à faire de plus.

Les outils Azure prennent également en charge une ancienne méthode d’authentification : l’utilisation du fichier d’abonnement (fichier .publishsettings). Cet article explique cette méthode, qui est toujours prise en charge dans le SDK Azure 2.2.

Les éléments suivants sont indispensables pour l’authentification dans Azure :

* Votre ID d’abonnement
* Un certificat X.509 v3 valide

> [!NOTE]
> La longueur de la clé du certificat X.509 v3 doit être de 2 048 bits au minimum. Azure rejette les certificats qui ne répondent pas à cette exigence ou qui ne sont pas valides.
>
>

Visual Studio utilise votre ID d’abonnement et les données du certificat comme informations d’identification. Les informations d’identification sont référencées dans le fichier d’abonnement (fichier .publishsettings), qui contient une clé publique pour le certificat. Le fichier d’abonnement peut contenir des informations d’identification pour plusieurs abonnements.

Vous pouvez modifier les informations d’abonnement à partir de la boîte de dialogue **Nouvel abonnement** ou **Modifier l’abonnement**, comme expliqué plus loin dans cet article.

Si vous souhaitez créer vous-même un certificat, vous pouvez consulter les instructions fournies dans [Créer et charger un certificat de gestion pour Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx), puis charger manuellement le certificat sur le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Les informations d’identification dont Visual Studio a besoin pour gérer vos services cloud ne sont pas les mêmes que celles nécessaires pour authentifier une demande effectuée auprès des services de stockage Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importer, configurer ou modifier des informations d’authentification dans Visual Studio

1. Dans l’Explorateur de serveurs, ouvrez le menu contextuel associé au nœud **Azure**, puis sélectionnez **Gérer et filtrer les abonnements**.
2. Sélectionnez l’onglet **Certificats**, puis utilisez une des méthodes suivantes :

    * Sélectionnez **Importer** pour ouvrir la boîte de dialogue **Importer les abonnements Microsoft Azure**. Vous pouvez y télécharger le fichier d’abonnements pour l’abonnement chargé. Accédez à son emplacement de téléchargement, puis importez-le afin de l’utiliser pour l’authentification.
    * Sélectionnez **Nouveau** pour ouvrir la boîte de dialogue **Nouvel abonnement**. Vous pouvez y configurer un nouvel abonnement afin de l’utiliser pour l’authentification.
    * Sélectionnez **Modifier** (après avoir choisi votre abonnement actif) pour ouvrir la boîte de dialogue **Modifier l’abonnement**. Vous pouvez y modifier un abonnement existant afin de l’utiliser pour l’authentification. 

La procédure suivante suppose que la boîte de dialogue **Nouvel abonnement** est ouverte.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Pour modifier ou exporter des informations d’authentification dans Visual Studio
1. Dans la liste **Sélectionnez un certificat existant pour l’authentification**, choisissez un certificat.
2. Sélectionnez le lien **Copier le chemin complet**. Le chemin du certificat (fichier .cer) est copié dans le Presse-papiers.

   > [!IMPORTANT]
   > Pour publier votre application Azure à partir de Visual Studio, vous devrez charger ce certificat sur le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Pour charger le certificat sur le portail Azure :

   a. Ouvrez le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   
   b. Si vous y êtes invité, connectez-vous au portail, puis accédez à **Paramètres** > **Certificats de gestion**.
   
   c. Dans le volet **Certificats de gestion**, sélectionnez **Charger**.
   
   d. Sélectionnez votre abonnement Azure, collez le chemin complet du fichier .cer que vous venez de créer, puis sélectionnez **Charger**.

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble de Web Apps](https://docs.microsoft.com/azure/app-service-web/)
* [Déploiement de votre application dans Azure App Service](https://docs.microsoft.com/en-us/azure/app-service-web/web-sites-deploy?toc=%2fazure%2fapp-service-api%2ftoc.json) 
* [Déployer des tâches web à l’aide de Visual Studio](https://docs.microsoft.com/en-us/azure/app-service-web/websites-dotnet-deploy-webjobs)
* [Création et déploiement d’un service cloud](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)
