---
title: "Accès à des clouds Azure privés avec Visual Studio | Microsoft Docs"
description: "Découvrez comment accéder aux ressources de cloud privé à l&quot;aide de Visual Studio."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b29a3299433e57709ea31de3c7849230ea09c09a
ms.lasthandoff: 03/21/2017


---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Accès à des clouds privés Azure avec Visual Studio
Par défaut, Visual Studio prend en charge les points de terminaison REST du cloud public Azure. Cette rubrique explique comment utiliser votre certificat de cloud privé pour accéder au cloud privé et y interagir à partir de Visual Studio.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Pour accéder à un cloud privé Azure dans Visual Studio
1. Dans le [portail Azure Classic](http://go.microsoft.com/fwlink/?LinkID=213885) du cloud privé, téléchargez votre fichier de paramètres de publication ou contactez votre administrateur pour obtenir un fichier de paramètres de publication. Sur la version publique d'Azure, le lien de téléchargement est [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Le fichier téléchargé doit porter l’extension `.publishsettings`)

1. Ouvrez Visual Studio.

1. Dans l’**Explorateur de recherche**, cliquez avec le bouton droit sur le nœud **Azure**, puis dans le menu contextuel, cliquez sur **Gérer et filtrer les abonnements**.
   
    ![Commande Gérer les abonnements](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Dans la boîte de dialogue **Gérer les abonnements Microsoft Azure**, sélectionnez l’onglet **Certificats**, puis le bouton **Importer**.
   
    ![Importation de certificats Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Dans la boîte de dialogue **Importer les abonnements Microsoft Azure**, sélectionnez **Parcourir**.

    ![Bouton Parcourir dans la boîte de dialogue Importer les abonnements Microsoft Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. Dans la boîte de dialogue **Ouvrir**, parcourez le répertoire dans lequel vous avez enregistré le fichier de paramètres de publication, sélectionnez ce dernier, puis cliquez sur **Ouvrir**.

    ![Sélection du fichier de paramètres de publication](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. Lorsque vous revenez à la boîte de dialogue **Importer les abonnements Microsoft Azure**, sélectionnez **Importer**.

    ![Importation du fichier de paramètres de publication](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    Les certificats sont importés du fichier de paramètres de publication vers Visual Studio. Vous pouvez maintenant interagir avec vos ressources de cloud privé.
   
## <a name="next-steps"></a>Étapes suivantes
- [Publication d’un service cloud Azure depuis Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [Téléchargement et importation des paramètres de publication et des informations d’abonnement](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)

