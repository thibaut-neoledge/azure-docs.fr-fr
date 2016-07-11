<properties
	pageTitle="Azure AD Connect Sync : exécuter l’Assistant Installation une deuxième fois | Microsoft Azure"
	description="Explique le fonctionnement de l’Assistant Installation la deuxième fois que vous l’exécutez."
	keywords="L’Assistant Installation d’Azure AD Connect vous permet de configurer les paramètres de maintenance la deuxième fois que vous l’exécutez"
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
	ms.date="06/27/2016"
	ms.author="andkjell"/>


# Azure AD Connect Sync : exécuter l’Assistant Installation une deuxième fois
La première fois que vous exécutez l’Assistant Installation d’Azure AD Connect, il vous guide dans la procédure de configuration de votre installation. Si vous réexécutez l’Assistant Installation, il vous propose des options de maintenance.

Vous trouverez l’Assistant Installation dans le menu Démarrer sous le nom **Azure Connect AD**.

![Menu Démarrer](./media/active-directory-aadconnectsync-installation-wizard/startmenu.png)

Lorsque vous démarrez l’Assistant Installation, une page s’affiche avec ces options :

![Page avec une liste de tâches supplémentaires](./media/active-directory-aadconnectsync-installation-wizard/additionaltasks.png)

Si vous avez installé AD FS avec Azure AD Connect, vous aurez davantage d’options. Les options supplémentaires que vous avez pour AD FS sont documentées dans [Gestion AD FS](active-directory-aadconnect-federation-management.md#ad-fs-management).

Sélectionnez l’une des tâches et cliquez sur **Suivant** pour continuer.

> [AZURE.IMPORTANT] Tant que l’Assistant Installation est ouvert, toutes les opérations du moteur de synchronisation sont suspendues. Prenez soin de fermer l’Assistant Installation dès que vous avez terminé vos modifications de configuration.

## Afficher la configuration actuelle
Cette option vous donne un aperçu rapide de vos options actuellement configurées.

![Page avec la liste de toutes les options et leur état](./media/active-directory-aadconnectsync-installation-wizard/viewconfig.png)

Cliquez sur **Précédent** pour revenir en arrière. Si vous sélectionnez **Quitter**, vous fermerez l’Assistant Installation.

## Personnaliser les options de synchronisation
Cette option est utilisée pour apporter des modifications à la configuration de la synchronisation. Vous verrez un sous-ensemble d’options provenant du chemin d’installation de la configuration personnalisée. Et ce même si vous avez initialement utilisé l’installation rapide.

- [Ajouter d’autres annuaires](active-directory-aadconnect-get-started-custom.md#connect-your-directories). Pour supprimer un annuaire, consultez [Supprimer un connecteur](active-directory-aadconnectsync-service-manager-ui-connectors.md#delete).
- [Modifier le filtrage par domaine ou unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering).
- Supprimer le filtrage de groupe.
- [Modifier les fonctionnalités facultatives](active-directory-aadconnect-get-started-custom.md#optional-features).

Les autres options provenant de l’installation initiale ne peuvent pas être modifiées et ne sont pas disponibles. Ce sont notamment :

- Modifier l’attribut à utiliser pour userPrincipalName et sourceAnchor.
- Modifier la méthode de jointure d’objets provenant de différentes forêts.
- Activer le filtrage de groupe.

## Actualiser le schéma d’annuaire
Cette option est utilisée si vous avez modifié le schéma dans l’une de vos forêts AD DS locales. Par exemple, vous pouvez avoir installé Exchange ou mis à niveau vers un schéma Windows Server 2012 avec des objets de périphérique. Dans ce cas, vous devez demander à Azure AD Connect de relire le schéma AD DS et de mettre à jour son cache. Cette opération regénère également les règles de synchronisation. Si vous ajoutez le schéma Exchange, par exemple, les règles de synchronisation d’Exchange sont ajoutées à la configuration.

Lorsque vous sélectionnez cette option, tous les annuaires de votre configuration sont répertoriés. Vous pouvez conserver le paramètre par défaut et actualiser toutes les forêts ou désélectionner certaines d’entre elles.

![Page avec la liste de tous les annuaires de l’environnement](./media/active-directory-aadconnectsync-installation-wizard/refreshschema.png)

## Configurer le mode de préproduction
Cette option permet d’activer et de désactiver le mode de préproduction sur le serveur. Vous trouverez plus d’informations sur le mode de préproduction et son utilisation dans [Opérations](active-directory-aadconnectsync-operations.md#staging-mode).

L’option s’affiche si la préproduction est actuellement activée ou désactivée : ![Option qui affiche également l’état actuel du mode de préproduction](./media/active-directory-aadconnectsync-installation-wizard/stagingmodecurrentstate.png)

Pour modifier l’état, sélectionnez cette option et cochez ou décochez la case. ![Option qui affiche également l’état actuel du mode de préproduction](./media/active-directory-aadconnectsync-installation-wizard/stagingmodeenable.png)

## Modifier la connexion de l’utilisateur
Cette option vous permet de passer de la synchronisation de mot de passe à la fédération ou l’inverse. Vous ne pouvez pas passer à **ne pas configurer**.

Pour plus d’informations sur cette option, consultez [connexion de l’utilisateur](active-directory-aadconnect-user-signin.md#changing-user-sign-in-method).


## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0629_2016-->