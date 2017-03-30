---
title: "Conservation d’une adresse IP virtuelle constante pour un service cloud Azure | Microsoft Docs"
description: "Découvrez comment vous assurer que l’adresse IP virtuelle de votre service cloud Azure ne change pas."
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 4a58e2c6-7a79-4051-8a2c-99182ff8b881
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 12d73e2f490acc92bb4a2e1014bb24f7f672f713
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-retain-a-constant-virtual-ip-address-for-an-azure-cloud-service"></a>Conservation d’une adresse IP virtuelle constante pour un service cloud Azure
Lors de la mise à jour d’un service cloud hébergé dans Azure, vérifiez que l’adresse IP virtuelle du service n’est pas modifiée. De nombreux services de gestion de domaine utilisent la méthode DNS (Domain Name System), une méthode d’enregistrement pour stocker des noms de domaine et qui fonctionne uniquement si l’adresse IP virtuelle est inchangée. Utilisez l’**Assistant Publication** dans Azure Tools pour garantir que l’adresse IP virtuelle de votre service cloud ne change pas lors de sa mise à jour. Pour plus d’informations sur l’utilisation de la gestion de domaine DNS pour les services cloud, consultez [Configuration d’un nom de domaine personnalisé pour un service cloud Azure](cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Publication d’un service cloud sans modifier son adresse IP virtuelle
L’adresse IP virtuelle d’un service cloud est allouée la première fois que vous la déployez dans Azure dans un environnement spécifique, tel que l’environnement Production. L’adresse IP virtuelle est modifiée uniquement si vous supprimez le déploiement explicitement ou s’il est supprimé implicitement par le processus de mise à jour de déploiement. Pour conserver l’adresse IP virtuelle, vous ne devez pas supprimer votre déploiement, et vous devez également vous assurer qu’il n’est pas supprimé automatiquement par Visual Studio. Pour en contrôler le comportement, spécifiez les paramètres de déploiement dans l’**Assistant Publication**, qui prend en charge plusieurs options de déploiement. Spécifiez un nouveau déploiement ou une mise à jour de déploiement, qui peut être incrémentielle ou simultanée. Dans ces deux cas de mise à jour, l’adresse IP virtuelle est conservée. Pour les définitions de ces différents types de déploiement, consultez [Assistant Publication d’application Azure](vs-azure-tools-publish-azure-application-wizard.md).  De plus, vous pouvez contrôler si une erreur se produit, dans le cas de la suppression du déploiement précédent d’un service cloud. L’adresse IP virtuelle peut être modifiée de façon inattendue si cette option n’est pas définie correctement.

## <a name="updating-a-cloud-service-without-changing-its-vip"></a>Mise à jour d’un service cloud sans modification de son adresse IP virtuelle
1. Créez ou ouvrez un projet de service cloud Azure dans Visual Studio. 

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis, dans le menu contextuel, sélectionnez **Publier**.

    ![Publish menu](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/solution-explorer-publish-menu.png)

1. Dans la boîte de dialogue **Publication d’application Azure**, sélectionnez l’abonnement Azure vers lequel vous souhaitez effectuer le déploiement, connectez-vous si nécessaire, puis sélectionnez **suivant**.

    ![Publication d’application Azure : connexion](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-signin.png)

1. Dans l’onglet **Paramètres communs**, vérifiez que le nom du service cloud vers lequel vous souhaitez effectuer le déploiement, **l’environnement**, la **configuration de build** et la **configuration du service** sont corrects.

    ![Publication d’application Azure : paramètres communs](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-common-settings.png)

1. Dans l’onglet **Paramètres avancés**, vérifiez que le compte de stockage et l’étiquette de déploiement sont corrects, que la case **Delete deployment on failure (Supprimer le déploiement en cas d’échec)** est décochée et que la case **Mise à jour du déploiement** est cochée. Cocher la case **Mise à jour du déploiement** vous garantit que votre déploiement ne sera pas supprimé et que votre adresse IP virtuelle ne sera pas perdue lorsque vous republierez votre application. Décocher la case **Delete deployment on failure (Supprimer le déploiement en cas d’échec)** vous garantit que votre adresse IP virtuelle ne sera pas perdue si une erreur se produit pendant le déploiement.

    ![Publication d’application Azure : paramètres avancés](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-advanced-settings.png)

1. (Facultatif) Pour préciser davantage comment vous souhaitez mettre à jour les rôles, sélectionnez **Paramètres** en regard de **Mise à jour du déploiement**. Sélectionnez **Mise à jour incrémentielle** ou **Mise à jour simultanée**. Si vous choisissez **Mise à jour incrémentielle**, les instances de votre application sont mises à jour l’une après l’autre afin que l’application soit toujours disponible. Si vous choisissez **Mise à jour simultanée**, toutes les instances de votre application sont mises à jour en même temps. La mise à jour simultanée est plus rapide, mais votre service risque de ne pas être disponible pendant la durée du processus de mise à jour. Lorsque vous avez terminé, sélectionnez **Suivant**.

    ![Publication d’application Azure : paramètres de mise à jour de déploiement](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-deployment-update-settings.png)

1. Lorsque vous retournez dans la boîte de dialogue **Publication d’application Azure**, sélectionnez **Suivant** jusqu’à l’affichage de la page **Résumé**. Vérifiez vos paramètres, puis sélectionnez **Publier**.
   
    ![Publication d’application Azure : résumé](./media/vs-azure-tools-cloud-service-retain-a-constant-virtual-ip-address/azure-publish-summary.png)

## <a name="next-steps"></a>Étapes suivantes
- [Utilisation de l’Assistant Publication d’application Azure dans Visual Studio](vs-azure-tools-publish-azure-application-wizard.md)


