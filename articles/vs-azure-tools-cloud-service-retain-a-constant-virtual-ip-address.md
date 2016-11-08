---
title: Conserver une adresse IP virtuelle constante pour un service cloud | Microsoft Docs
description: Découvrez comment vous assurer que l’adresse IP virtuelle de votre service cloud Azure ne change pas.
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: ''

ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 08/15/2016
ms.author: tarcher

---
# Conserver une adresse IP virtuelle constante pour un service cloud
Lors de la mise à jour d’un service cloud hébergé dans Azure, vérifiez que l’adresse IP virtuelle du service n’est pas modifiée. De nombreux services de gestion de domaine utilisent la méthode DNS (Domain Name System), une méthode d’enregistrement pour stocker des noms de domaine et qui fonctionne uniquement si l’adresse IP virtuelle est inchangée. Utilisez l’**Assistant Publication** dans Azure Tools pour garantir que l’adresse IP virtuelle de votre service cloud ne change pas lors de sa mise à jour. Pour plus d’informations sur l’utilisation de la gestion de domaine DNS pour les services cloud, consultez [Configuration d’un nom de domaine personnalisé pour un service cloud Azure](cloud-services/cloud-services-custom-domain-name.md).

## Publication d’un service cloud sans modifier son adresse IP virtuelle
L’adresse IP virtuelle d’un service cloud est allouée la première fois que vous la déployez dans Azure dans un environnement spécifique, tel que l’environnement Production. L’adresse IP virtuelle n’est pas modifiée à moins que vous ne supprimiez le déploiement explicitement ou qu’il soit supprimé implicitement par le processus de mise à jour de déploiement. Pour conserver l’adresse IP virtuelle, vous ne devez pas supprimer votre déploiement, et vous devez également vous assurer qu’il n’est pas supprimé automatiquement par Visual Studio. Pour en contrôler le comportement, spécifiez les paramètres de déploiement dans l’**Assistant Publication**, qui prend en charge plusieurs options de déploiement. Spécifiez un nouveau déploiement ou une mise à jour de déploiement, qui peut être incrémentielle ou simultanée. Dans ces deux cas de mise à jour, l’adresse IP virtuelle est conservée. Pour les définitions de ces différents types de déploiement, consultez [Assistant Publication d’application Azure](vs-azure-tools-publish-azure-application-wizard.md). De plus, vous pouvez contrôler si une erreur se produit, dans le cas de la suppression du déploiement précédent d’un service cloud. L’adresse IP virtuelle peut être modifiée de façon inattendue si cette option n’est pas définie correctement.

### Pour mettre à jour un service cloud sans modifier son adresse IP virtuelle
1. Après avoir déployé votre service cloud au moins une fois, ouvrez le menu contextuel du nœud de votre projet Azure, puis choisissez **Publier**. L’Assistant **Publication d’application Azure** s’affiche.
2. Dans la liste des abonnements, sélectionnez celui vers lequel vous souhaitez effectuer le déploiement, puis choisissez le bouton **Suivant**. La page **Paramètres** de l’assistant s’affiche.
3. Dans l’onglet **Paramètres communs**, vérifiez que le nom du service cloud vers lequel vous souhaitez effectuer le déploiement, l’**Environnement**, la **Configuration de build** et la **Configuration du service** sont tous corrects.
4. Dans l’onglet **Paramètres avancés**, vérifiez que le compte de stockage et l’étiquette de déploiement sont corrects, que la case à cocher **Supprimer le déploiement en cas d’échec** est désactivée et que la case de mise à jour **Déploiement** est cochée. Cocher la case de mise à jour **Déploiement** vous garantit que votre déploiement ne sera pas supprimé et que votre adresse IP virtuelle ne sera pas perdue lorsque vous republierez votre application. Désactiver la case à cocher **Supprimer le déploiement en cas d’échec** vous garantit que votre adresse IP virtuelle ne sera pas perdue si une erreur se produit pendant le déploiement.
5. Pour spécifier avec plus de détails la façon dont vous souhaitez que les rôles soient mis à jour, cliquez sur le lien **Paramètres** à côté de la zone **Mise à jour du déploiement**, puis sélectionnez l’option de déploiement incrémentiel ou de déploiement simultané dans la boîte de dialogue des paramètres **Mise à jour du déploiement**. Si vous spécifiez un déploiement incrémentiel, les instances sont mises à jour l’une après l’autre, de sorte que l’application soit toujours disponible. Si vous spécifiez un déploiement simultané, toutes les instances sont mises à jour en même temps. La mise à jour simultanée est plus rapide, mais votre service risque de ne pas être disponible pendant la durée du processus de mise à jour.
6. Si vos paramètres sont satisfaisants, choisissez le bouton **Suivant**.
7. Dans la page **Résumé** de l’Assistant, vérifiez les paramètres, puis cliquez sur le bouton **Publier**.
   
   > [!WARNING]
   > En cas d’échec du déploiement, vous devez trouver la raison de cet échec et effectuer à nouveau le déploiement rapidement, pour éviter de laisser votre service cloud dans un état endommagé.
   > 
   > 

## Étapes suivantes
Pour en savoir plus sur la publication sur Azure depuis Visual Studio, consultez [Assistant Publication d’application Azure](vs-azure-tools-publish-azure-application-wizard.md).

<!---HONumber=AcomDC_0817_2016-->