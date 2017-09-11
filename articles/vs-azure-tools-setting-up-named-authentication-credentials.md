---
title: "Configuration des informations d’authentification nommées | Microsoft Docs"
description: "Découvrez comment fournir des informations d’identification que Visual Studio pourra utiliser pour authentifier les demandes effectuées auprès d’Azure dans le cadre de la publication d’une application dans Azure à partir de Visual Studio, ou de l’analyse d’un service cloud existant. "
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
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c486676a70e195ec85ad40540ea4b7caaa86bc48
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---
# <a name="setting-up-named-authentication-credentials"></a>Configuration des informations d’authentification nommées
Pour publier une application dans Azure à partir de Visual Studio, ou pour analyser un service cloud existant, vous devez fournir des informations d’identification que Visual Studio pourra utiliser pour authentifier les demandes effectuées auprès d’Azure. Il existe plusieurs emplacements dans Visual Studio à partir desquels vous pouvez vous connecter pour fournir ces informations d’identification. Par exemple, à partir de l’Explorateur de serveurs, vous pouvez ouvrir le menu contextuel du nœud **Azure**, puis sélectionner **Se connecter à Abonnement Microsoft Azure...**. Quand vous vous connectez, les informations d’abonnement associées à votre compte Azure sont disponibles dans Visual Studio. Vous n’avez donc rien à faire de plus.

Les outils Azure prennent également en charge une ancienne méthode d’authentification, qui est l’utilisation du fichier d’abonnement (fichier .publishsettings). Cette rubrique explique cette méthode, qui est toujours prise en charge dans le kit de développement logiciel (SDK) Azure 2.2.

Les éléments suivants sont indispensables pour l’authentification dans Azure :

* Votre ID d’abonnement
* Un certificat X.509 v3 valide

> [!NOTE]
> La longueur de la clé du certificat X.509 v3 doit être de 2 048 bits au minimum. Azure rejette les certificats qui ne répondent pas à cette exigence ou qui ne sont pas valides.
>
>

Visual Studio utilise votre ID d’abonnement et les données du certificat comme informations d’identification. Les informations d’identification sont référencées dans le fichier d’abonnement (fichier .publishsettings), qui contient une clé publique pour le certificat. Le fichier d’abonnement peut contenir des informations d’identification pour plusieurs abonnements.

Vous pouvez modifier les informations d’abonnement à partir de la boîte de dialogue **Modifier l’abonnement/Nouvel abonnement** , comme expliqué plus loin dans cette rubrique.

Si vous souhaitez créer vous-même un certificat, vous pouvez consulter les instructions fournies dans [Créer et charger un certificat de gestion pour Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx), puis charger manuellement le certificat vers le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885).

> [!NOTE]
> Les informations d’identification dont Visual Studio a besoin pour gérer vos services cloud ne sont pas les mêmes que celles nécessaires pour authentifier une demande effectuée auprès des services de stockage Azure.
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Importer, configurer ou modifier des informations d’authentification dans Visual Studio
Vous pouvez également importer, configurer ou modifier vos informations d’authentification à partir de la boîte de dialogue **Nouvel abonnement** qui s’affiche quand vous effectuez l’action suivante :

* Dans l’**Explorateur de serveurs**, ouvrez le menu contextuel du nœud **Azure**, choisissez **Gérer et filtrer les abonnements...**, sélectionnez l’onglet **Certificats**, puis effectuez une des actions suivantes :

    * Choisissez **Importer** pour ouvrir la boîte de dialogue Importer les abonnements Microsoft Azure, où vous pouvez télécharger le fichier d’abonnements pour l’abonnement actuellement chargé. Accédez à son emplacement de téléchargement, puis importez-le pour l’utiliser pour l’authentification.
    * Choisissez **Nouveau** pour ouvrir la boîte de dialogue Nouvel abonnement, dans laquelle vous pouvez configurer un nouvel abonnement à utiliser pour l’authentification.
    * Choisissez **Modifier** (après le choix de votre abonnement) pour ouvrir la boîte de dialogue Modifier l’abonnement, où vous pouvez modifier un abonnement existant pour une utilisation pour l’authentification. 

La procédure suivante suppose que la boîte de dialogue **Nouvel abonnement** est ouverte.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Pour modifier ou exporter des informations d’authentification dans Visual Studio
1. Dans la liste **Sélectionnez un certificat existant pour l’authentification** , choisissez un certificat.
2. Cliquez sur le lien **Copier le chemin d’accès complet**. Le chemin d’accès du certificat (fichier .cer) est copié dans le Presse-papiers.

   > [!IMPORTANT]
   > Pour publier votre application Azure à partir de Visual Studio, vous devrez charger ce certificat vers le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   >
   >
3. Pour télécharger le certificat sur le portail Azure :

   1. Ouvrez le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
   2. Si vous y êtes invité, connectez-vous au portail, puis accédez à **Paramètres**, **Certificats de gestion**.
   3. Dans le volet Certificats de gestion, choisissez **Télécharger**.
   4. Sélectionnez votre abonnement Azure, collez le chemin d’accès complet du fichier .cer que vous venez de créer, puis choisissez **Télécharger**.

