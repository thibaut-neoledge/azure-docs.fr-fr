---
title: "Connecteurs dans l’interface utilisateur d’Azure AD Synchronization Service Manager | Microsoft Docs"
description: "Comprendre l’onglet Connecteurs dans Synchronization Service Manager pour Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: 55adbf800c6312371d937ed2da98a4a5ce704e51
ms.lasthandoff: 02/28/2017

---
# <a name="using-connectors-with-the-auzre-ad-connect-sync-service-manager"></a>Utilisation de connecteurs avec Azure AD Connect Sync Service Manager

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

L’onglet Connecteurs permet de gérer tous les systèmes auquel le moteur de synchronisation est connecté.

## <a name="connector-actions"></a>Actions du connecteur
| Action | Commentaire |
| --- | --- |
| Créer |Ne pas utiliser. Pour la connexion à des forêts Active Directory supplémentaires, utilisez l’Assistant Installation. |
| Propriétés |Permet le filtrage de domaine et d’unité organisationnelle. |
| [Supprimer](#delete) |Permet de supprimer les données dans l’espace connecteur ou de supprimer la connexion à une forêt. |
| [Configurer les profils d’exécution](#configure-run-profiles) |À l’exception du filtrage de domaine, il n’y a rien à configurer ici. Vous pouvez vous servir de cette action pour voir les profils d’exécution déjà configurés. |
| Exécuter |Permet de lancer l’exécution unique d’un profil. |
| Arrêter |Arrête un connecteur qui exécute un profil. |
| Exporter le connecteur |Ne pas utiliser. |
| Importer le connecteur |Ne pas utiliser. |
| Mettre à jour le connecteur |Ne pas utiliser. |
| Actualiser le schéma |Actualise le schéma mis en cache. Il est préférable d’utiliser l’option dans l’Assistant Installation, car les règles de synchronisation sont également mises à jour. |
| [Espace de connecteur de recherche](#search-connector-space) |Permet de rechercher des objets et de [Suivre un objet et ses données dans le système](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Supprimer
L’action de suppression est utilisée dans deux cas.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

L’option **Supprimer l’espace connecteur uniquement** supprime toutes les données en conservant la configuration.

L’option **Supprimer le connecteur et l’espace connecteur** supprime les données et la configuration. Utilisez cette option quand vous ne souhaitez plus vous connecter à une forêt.

Les deux options synchronisent tous les objets et mettent à jour les objets du métaverse. Cette action est une opération de longue durée.

### <a name="configure-run-profiles"></a>Configurer les profils d’exécution
Cette option vous permet de voir les profils d'exécution configurées pour un connecteur.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Espace de connecteur de recherche
L’action de recherche dans l’espace connecteur permet de rechercher des objets et de résoudre les problèmes relatifs aux données.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Commencez par sélectionner une **portée**. Vous pouvez rechercher des données (nom unique relatif, nom de domaine, ancre, sous-arborescence) ou l’état de l’objet (toutes les autres options).  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Par exemple, si vous effectuez une recherche dans la sous-arborescence, vous obtenez tous les objets d’une unité d’organisation.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
À partir de cette grille, vous pouvez sélectionner un objet, sélectionner des **propriétés** et [les suivre](#follow-an-object-and-its-data-through-the-system) par le biais du métaverse, de l’espace connecteur source vers l’espace connecteur cible.

## <a name="follow-an-object-and-its-data-through-the-system"></a>Suivre un objet et ses données dans le système
Lorsque vous résolvez un problème avec les données, suivre un objet à partir de l’espace connecteur source, vers le métaverse et vers l’espace connecteur cible est une procédure essentielle pour comprendre pourquoi les données n’ont pas les valeurs attendues.

### <a name="connector-space-object-properties"></a>Propriétés de l’objet espace connecteur
**Importerer**  
Quand vous ouvrez un objet cs, plusieurs onglets sont affichés en haut. L’onglet **Importer** affiche les données mises en lot après une importation.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png)  
L’**ancienne valeur** montre ce qui est actuellement stocké dans le système, et la **nouvelle valeur** ce qui a été reçu à partir du système source et n’a pas encore été appliqué. Dans ce cas, comme il existe une erreur de synchronisation, la modification ne peut pas être appliquée.

**Erreur**  
: la page d’erreur est visible uniquement s’il existe un problème au niveau de l’objet. Pour plus d’informations sur la [résolution des erreurs de synchronisation](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab), consultez les détails dans la page sur les opérations.

**Lignage**  
: l’onglet Lignage montre de quelle manière l’objet espace connecteur est lié à l’objet métaverse. Vous pouvez voir à quel moment le connecteur a importé pour la dernière fois une modification dans le système connecté et connaître les règles appliquées pour remplir les données dans le métaverse.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png)  
Dans la colonne **Action**, vous pouvez voir une règle de synchronisation **Entrante** avec l’action **Approvisionner**. Cela signifie que tant que cet objet espace connecteur est présent, l’objet métaverse est conservé. Si la liste des règles de synchronisation affiche à la place une règle de synchronisation avec la direction **Sortante** et **Approvisionner**, cela indique que cet objet est supprimé en même temps que l’objet métaverse.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png)  
Vous pouvez également voir dans la colonne **PasswordSync** que l’espace connecteur entrant peut modifier le mot de passe, car une règle de synchronisation a la valeur **True**. Ce mot de passe est ensuite envoyé à Azure AD au moyen de la règle sortante.

Sous l’onglet Lignage, vous pouvez accéder au métaverse en cliquant sur [Propriétés de l’objet métaverse](#metaverse-object-properties).

Deux boutons apparaissent en bas de tous les onglets : **Aperçu** et **Journal**.

**Aperçu**  
: la page d’aperçu sert à synchroniser un seul objet. Elle est utile si vous résolvez des problèmes liés aux règles de synchronisation du client et que vous souhaitez voir l’effet d’une modification sur un seul objet. Vous pouvez choisir entre **Synchronisation complète** et **Synchronisation delta**. Vous pouvez également choisir entre **Générer l’aperçu**, qui permet de conserver uniquement la modification en mémoire, et **Valider l’aperçu**, qui implémente toutes les modifications dans les espaces connecteur cibles.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png)  
Vous pouvez inspecter l’objet et la règle appliquée pour un flux d’attribut particulier.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Journal**  
La page du journal est utilisée pour afficher l’état de synchronisation et l’historique du mot de passe.

### <a name="metaverse-object-properties"></a>Propriétés de l’objet métaverse
**Attributs**  
: sous l’onglet Attributs, vous pouvez voir les valeurs et le connecteur qui y a contribué.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)  
**Connecteurs**  
: l’onglet Connecteurs affiche tous les espaces connecteur qui ont une représentation de l’objet.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png)  
Cet onglet permet également d’accéder à l’[objet CS (Connector Space)](#connector-space-object-properties).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

