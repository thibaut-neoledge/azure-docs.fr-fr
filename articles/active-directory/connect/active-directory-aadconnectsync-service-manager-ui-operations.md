---
title: "Opérations d’Azure AD Connect Synchronization Service Manager | Microsoft Docs"
description: "Comprendre l’onglet des opérations dans Synchronization Service Manager pour Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: fadb15a95edb3ec82b284faa594706963618b9eb
ms.lasthandoff: 02/28/2017

---
# <a name="using-the-sync-service-manager-operations-tab"></a>Utilisation de l’onglet Opérations de Sync Service Manager

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/operations.png)

L’onglet des opérations affiche les résultats des opérations les plus récentes. Cet onglet est essentiel pour comprendre et résoudre les problèmes.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Comprendre les informations visibles dans l’onglet des opérations
La partie supérieure affiche toutes les exécutions dans un ordre chronologique. Par défaut, le journal des opérations conserve les informations des sept derniers jours, mais vous pouvez modifier ce paramètre à l’aide du [planificateur](active-directory-aadconnectsync-feature-scheduler.md). Vous souhaitez rechercher toute exécution qui ne présente pas un état de réussite. Vous pouvez modifier le tri en cliquant sur les en-têtes.

La colonne **État** regroupe les informations les plus importantes et présente le problème le plus sévère pour une exécution. Voici un récapitulatif rapide des états les plus courants par ordre de priorité d’inspection (où * indique plusieurs chaînes d’erreur possibles).

| État | Commentaire |
| --- | --- |
| stopped-* |L’exécution n’a pas pu se terminer. Par exemple, si le système distant est arrêté et ne peut pas être contacté. |
| stopped-error-limit |Il existe plus de 5 000 erreurs. L’exécution a été automatiquement arrêtée en raison du grand nombre d’erreurs. |
| completed-\*-errors |L’exécution s’est terminée, mais il existe des erreurs (moins de 5 000) qui doivent être examinées. |
| completed-\*-warnings |L’exécution s’est terminée, mais des données ne sont pas dans l’état attendu. Si vous avez des erreurs, alors ce message n’est, en général, qu’un symptôme. N’examinez pas les avertissements avant d’avoir résolu les erreurs. |
| réussi |Aucun problème. |

Lorsque vous sélectionnez une ligne, la partie inférieure est mise à jour pour afficher les détails de cette exécution. À l’extrême gauche de la partie inférieure, une liste peut s’afficher indiquant **Step #**. Cette liste ne s’affiche que si votre forêt contient plusieurs domaines et que chaque domaine est représenté par une étape. Le nom de domaine se trouve sous le titre **Partition**. Sous **Statistiques de synchronisation**, vous trouverez plus d’informations sur le nombre de modifications qui ont été traitées. Vous pouvez cliquer sur les liens pour obtenir la liste des objets modifiés. Si vous avez des objets comportant des erreurs, celles-ci s’affichent sous **Erreurs de synchronisation**.

## <a name="troubleshoot-errors-in-operations-tab"></a>Résoudre les erreurs dans l’onglet des opérations
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorsync.png)  
Quand vous avez des erreurs, l’objet comportant l’erreur et l’erreur elle-même s’affichent sous forme de liens qui fournissent davantage d’informations.

Cliquez sur la chaîne d’erreur (**sync-rule-error-function-triggered** dans l’image). Une présentation de l’objet s’affiche d’abord. Pour voir l’erreur, cliquez sur le bouton **Trace de pile**. Cette trace vous fournit des informations de débogage pour l’erreur.

**CONSEIL :** vous pouvez cliquer avec le bouton droit dans la zone des **informations sur la pile d’appels**, choisir **Sélectionner tout** et **Copier**. Vous pouvez ensuite copier la pile et examiner l’erreur dans votre éditeur favori, tel que le Bloc-notes.

* Si l’erreur provient de **SyncRulesEngine**, les informations sur la pile d’appels affichent d’abord une liste de tous les attributs sur l’objet. Faites défiler vers le bas jusqu’à l’en-tête **InnerException =>**.  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/errorinnerexception.png)  
  La ligne qui suit affiche l’erreur. Dans l’image ci-dessus, l’erreur provient d’une règle de synchronisation Fabrikam personnalisée créée.

Si l’erreur elle-même ne fournit pas suffisamment d’informations, il est temps d’examiner les données elles-mêmes. Vous pouvez cliquer sur le lien contenant l’identificateur de l’objet et [Suivre un objet et ses données dans le système](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

