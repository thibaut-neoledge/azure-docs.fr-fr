<properties
	pageTitle="Azure AD Connect Sync : meilleures pratiques pour modifier la configuration par défaut | Microsoft Azure"
	description="Décrit les meilleures pratiques pour la modification de la configuration par défaut d’Azure AD Connect Sync."
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
	ms.date="08/22/2016"
	ms.author="markvi;andkjell"/>


# Azure AD Connect Sync : meilleures pratiques pour modifier la configuration par défaut
L’objectif de cette rubrique est de décrire les modifications prises en charge et celles qui ne sont pas prises en charge pour Azure AD Connect Sync.

La configuration créée par Azure AD Connect fonctionne « telle quelle » pour la plupart des environnements qui synchronisent Active Directory local avec Azure AD. Toutefois, dans certains cas, il est nécessaire d’appliquer certaines modifications à une configuration pour répondre à une spécification ou un besoin particulier.

## Modifications apportées au compte de service
Azure AD Connect Sync s’exécute sous un compte de service créé par l’Assistant Installation. Ce compte de service conserve les clés de chiffrement dans la base de données utilisée par la synchronisation. Il est créé avec un mot de passe de 127 caractères de long, défini pour ne pas expirer.

- La modification ou la réinitialisation du mot de passe du compte de service **n’est pas prise en charge**. Sa modification ou sa réinitialisation supprime les clés de chiffrement et le service ne peut pas accéder à la base de données ni démarrer.

## Modifications apportées au planificateur
À partir de la version 1.1 (février 2016), vous pouvez configurer le [planificateur](active-directory-aadconnectsync-feature-scheduler.md) sur un cycle de synchronisation autre que le cycle par défaut de 30 minutes.

## Modifications apportées aux règles de synchronisation
L'Assistant d’installation fournit une configuration qui est censée fonctionner pour les scénarios les plus courants. Au cas où vous devez apporter des modifications à la configuration, vous devez suivre ces règles pour toujours avoir une configuration prise en charge.

- Vous pouvez [modifier le flux d’attributs](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) si le flux des attributs directs par défaut ne convient pas à votre organisation.
- Si vous ne souhaitez [pas transférer un attribut](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) et voulez supprimer des valeurs d’attribut d’Azure AD existantes, vous devez créer une règle pour ce scénario.
- [Désactiver une règle de synchronisation indésirable](#disable-an-unwanted-sync-rule) plutôt que la supprimer. Une règle supprimée est recréée pendant la mise à niveau.
- Pour [effectuer des modifications sur une règle out-of-box](#change-an-out-of-box-rule), vous devez faire une copie de la règle d’origine, puis la désactiver. L’éditeur de règles de synchronisation affiche une invite et vous propose de l’aide.
- Exportez vos règles de synchronisation personnalisées à l’aide de l’éditeur de règles de synchronisation. L’éditeur vous donne un script PowerShell que vous pouvez utiliser pour facilement les recréer dans le cadre d’une récupération d’urgence.

>[AZURE.WARNING] Les règles de synchronisation out-of-box ont une empreinte numérique. Si vous apportez une modification à ces règles, l’empreinte numérique ne correspondra plus. Vous risquez de rencontrer des problèmes lorsque vous tenterez d’appliquer une nouvelle version d’Azure AD Connect. Procédez à des modifications uniquement de la façon décrite dans cet article.

### Désactiver une règle de synchronisation indésirable
Ne supprimez pas une règle de synchronisation out-of-box. Elle est recréée au cours de la mise à niveau suivante.

Dans certains cas, l'Assistant d'installation a produit une configuration qui ne fonctionne pas pour votre topologie. Par exemple, si vous disposez d'une topologie de forêt compte-ressource, mais que vous avez étendu le schéma dans la forêt de comptes avec le schéma Exchange, alors les règles pour Exchange sont créées pour la forêt de comptes, ainsi que pour la forêt de ressources. Dans ce cas, vous devez désactiver la règle de synchronisation pour Exchange.

![Règle de synchronisation désactivée](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Dans l'image ci-dessus, l'Assistant d'installation a trouvé un ancien schéma Exchange 2003 dans la forêt de comptes. Cette extension de schéma a été ajoutée avant l’introduction de la forêt de ressources dans l’environnement de Fabrikam. Pour garantir qu’aucun attribut de l’ancienne implémentation Exchange n’est synchronisé, la règle de synchronisation devrait être désactivée comme indiqué.

### Modifier une règle out-of-box
Si vous devez effectuer des modifications d’une règle out-of-box, vous devriez en faire une copie, puis désactiver la règle d’origine. Modifiez ensuite la règle clonée. L’éditeur de règles de synchronisation vous accompagne dans ces étapes. Lorsque vous ouvrez une règle out-of-box, cette boîte de dialogue s'affiche : ![Règle d'avertissement out-of-the box](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Sélectionnez **Oui** pour créer une copie de la règle. La règle clonée est ensuite ouverte. ![Règle clonée](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Sur cette règle clonée, effectuez les modifications nécessaires de la portée, de la jointure et des transformations.

## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l'[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0824_2016-->