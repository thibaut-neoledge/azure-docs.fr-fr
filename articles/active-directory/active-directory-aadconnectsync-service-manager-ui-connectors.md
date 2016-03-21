<properties
	pageTitle="Synchronisation d’Azure AD Connect : Interface utilisateur de Synchronization Service Manager | Microsoft Azure"
	description="Comprendre l’onglet Connecteurs dans Synchronization Service Manager pour Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/03/2016"
	ms.author="andkjell"/>


# Synchronisation Azure AD Connect : Synchronization Service Manager

| [Opérations](active-directory-aadconnectsync-service-manager-ui-operations.md) | [Connecteurs](active-directory-aadconnectsync-service-manager-ui-connectors.md) | [Concepteur de métaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md) | [Recherche de métaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) |
| --- | --- | --- | --- |

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

L’onglet Connecteurs permet de gérer tous les systèmes auquel le moteur de synchronisation est connecté.

## Actions du connecteur

| Action | Commentaire |
| --- | --- |
| Créer | Ne pas utiliser. Pour la connexion à des forêts Active Directory supplémentaires, utilisez l’Assistant Installation. |
| Propriétés | Permet le filtrage de domaine et d’unité organisationnelle. |
| Supprimer | Permet de supprimer les données dans l’espace connecteur ou de supprimer la connexion à une forêt. |
| Configurer les profils d’exécution | À l’exception du filtrage de domaine, il n’y a rien à configurer ici. |
| Exécuter | Permet de lancer l’exécution unique d’un profil. |
| Arrêter | Arrête un connecteur qui exécute un profil. |
| Exporter le connecteur | Ne pas utiliser. |
| Importer le connecteur | Ne pas utiliser. |
| Mettre à jour le connecteur | Ne pas utiliser. |
| Actualiser le schéma | Actualise le schéma mis en cache. Il est préférable d’utiliser l’option dans l'Assistant Installation, car les règles de synchronisation sont également mises à jour.
| Espace de connecteur de recherche | Permet de rechercher des objets et de [suivre un objet et ses données dans le système](#follow-an-object-and-its-data-through-the-system). |

### Supprimer
L’action de suppression est utilisée dans deux cas. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

L’option **Supprimer l’espace connecteur uniquement** supprime toutes les données en conservant toutes les configurations.

L’option **Supprimer le connecteur et l’espace connecteur** supprime les données ainsi que la configuration. Utilisez cette option quand vous ne souhaitez plus vous connecter à une forêt.

Les deux options synchronisent tous les objets et mettent à jour les objets du métaverse. Il s’agit d’une opération de longue durée.

### Espace de connecteur de recherche
L’action de recherche dans l’espace connecteur permet de rechercher des objets et de résoudre les problèmes relatifs aux données.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Commencez par sélectionner une **étendue**. Vous pouvez rechercher des données (nom unique relatif, nom de domaine, ancre, sous-arborescence) ou l’état de l’objet (toutes les autres options). ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png) Par exemple, si vous effectuez une recherche dans la sous-arborescence, vous obtenez tous les objets d’une unité d’organisation. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png) À partir de cela, vous pouvez sélectionner un objet, sélectionner les **propriétés** et [les suivre](#follow-an-object-and-its-data-through-the-system), par le biais du métaverse, de l’espace connecteur source vers l’espace connecteur cible.

## Suivre un objet et ses données dans le système
Lorsque vous résolvez un problème avec les données, suivre un objet à partir de l’espace connecteur source, vers le métaverse et vers l’espace connecteur cible est une procédure essentielle pour comprendre pourquoi les données n’ont pas les valeurs attendues.

### Propriétés de l’objet espace connecteur
**Importer** : lorsque vous ouvrez un objet cs, celui-ci inclut plusieurs onglets situés en haut. L’onglet **Importer** affiche les données mises en lot après une importation. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png) L’**ancienne valeur** montre ce qui est stocké actuellement dans le système, et la **nouvelle valeur** ce qui a été reçu à partir du système source et n’a pas encore été appliqué. Dans ce cas, comme il existe une erreur de synchronisation, la modification ne peut pas être appliquée.

**Erreur** : la page d’erreur est visible uniquement si un problème existe avec l’objet. Consultez les détails sur la page d’opérations pour plus d'informations sur la [résolution des erreurs de synchronisation](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab).

**Lignage** : l’onglet Lignage montre de quelle manière l’objet espace connecteur est lié à l’objet métaverse. Nous pouvons voir à quel moment nous avons importé pour la dernière fois une modification dans le système connecté, ainsi que les règles appliquées pour remplir les données dans le métaverse. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png) Dans la colonne **Action**, nous pouvons voir une règle de synchronisation **entrante** avec l’action **Approvisionner**. Cela signifie que tant que cet objet espace connecteur est présent, l’objet métaverse est conservé. Si la liste des règles de synchronisation affiche à la place une règle de synchronisation avec la direction **Sortante** et **Approvisionner**, cela indique que cet objet est supprimé en même temps que l’objet métaverse. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png) Nous pouvons également voir dans la colonne **PasswordSync** que l’espace connecteur entrant peut modifier le mot de passe, car une règle de synchronisation a la valeur **True**. Ce mot de passe est ensuite envoyé à Azure AD au moyen de la règle sortante.

Sous l’onglet Lignage, vous pouvez accéder au métaverse en cliquant sur [Propriétés de l’objet métaverse](#metaverse-object-properties).

Deux boutons apparaissent en bas de tous les onglets : **Aperçu** et **Journal**.

**Aperçu** : la page d’aperçu est utilisée pour synchroniser un seul objet. Elle est utile si vous résolvez des problèmes liés aux règles de synchronisation du client et que vous souhaitez voir l’effet d’une modification sur un seul objet. Vous pouvez choisir entre **Synchronisation complète** et **Synchronisation delta**. Vous pouvez également choisir entre **Générer l’aperçu**, qui permet uniquement de conserver la modification en mémoire et **Valider l’aperçu** qui implémente toutes les modifications dans les espaces connecteur cibles. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png) Vous pouvez inspecter l’objet et la règle qui est appliquée pour un flux d’attribut particulier. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**Journal** : la page Journal permet d’afficher le statut et l’historique de synchronisation du mot de passe. Pour plus d’informations, consultez [Résoudre les problèmes de synchronisation de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).

### Propriétés de l’objet métaverse
**Attributs** : sous l’onglet Attributs, vous pouvez voir les valeurs et le connecteur qui y a contribué. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png) **Connecteurs** : l’onglet Connecteurs affiche tous les espaces connecteur qui ont une représentation de l’objet. ![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png) Cet onglet permet également d’accéder à l’[objet espace connecteur](#connector-space-object-properties).

## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!------HONumber=AcomDC_0309_2016-->