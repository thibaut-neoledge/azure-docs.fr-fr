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
	ms.date="01/21/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync : meilleures pratiques pour modifier la configuration par défaut
L’objectif de cette rubrique est de décrire les modifications prises en charge et celles qui ne sont pas prises en charge pour Azure AD Connect Sync.

La configuration créée par Azure AD Connect fonctionne « telle quelle » pour la plupart des environnements qui synchronisent Active Directory local avec Azure AD. Toutefois, dans certains cas, il est nécessaire d’appliquer certaines modifications à une configuration pour répondre à une spécification ou un besoin particulier.

## Modifications apportées au compte de service
Azure AD Connect Sync s’exécute sous un compte de service créé par l’Assistant Installation. Ce compte de service conserve les clés de chiffrement dans la base de données utilisée par la synchronisation. Il est créé avec un mot de passe de 127 caractères de long, défini pour ne pas expirer.

- La modification ou la réinitialisation du mot de passe du compte de service **n’est pas prise en charge**. Sa modification ou sa réinitialisation supprimera les clés de chiffrement et le service ne pourra plus accéder à la base de données et ne pourra pas démarrer.

## Modifications apportées au planificateur
Azure AD Connect Sync est configuré pour synchroniser les données d’identité toutes les 3 heures. Lors de l’installation, une tâche planifiée est créée, qui s’exécute sous un compte de service avec des autorisations pour utiliser le serveur de synchronisation.

- La modification de la tâche planifiée **n’est pas prise en charge**. Le mot de passe du compte de service n’est pas connu. Consultez [Modifications apportées au compte de service](#changes-to-the-service-account)
- La synchronisation à une fréquence supérieure à la fréquence par défaut de 3 heures **n’est pas prise en charge**.
	- Il est possible d’effectuer une synchronisation hors ligne lorsque vous testez une nouvelle configuration. Cependant, il est déconseillé d’exécuter des exportations vers Azure AD à un rythme plus fréquent.

## Modifications apportées aux règles de synchronisation
L'Assistant d’installation fournit une configuration qui est censée fonctionner pour les scénarios les plus courants. Au cas où vous devez apporter des modifications à la configuration, vous devez suivre ces règles pour toujours avoir une configuration prise en charge.

- Vous pouvez [Modifier le flux d’attributs](#change-attribute-flows) si le flux des attributs directs par défaut ne convient pas à votre organisation.
- Si vous ne souhaitez [pas transférer un attribut](#do-not-flow-an-attribute) et voulez supprimer des valeurs d’attribut d’Azure AD existantes, vous devez créer une règle.
- [Désactiver une règle de synchronisation indésirable](#disable-an-unwanted-sync-rule) plutôt que la supprimer. Une règle supprimée sera recréée pendant la mise à niveau.
- Pour [effectuer des modifications sur une règle out-of-box](#change-an-out-of-box-rule), vous devez faire une copie de la règle d’origine, puis la désactiver. L'éditeur de règles de synchronisation vous y invitera et vous aidera à le faire.
- Exportez vos règles de synchronisation personnalisées à l’aide de l’éditeur de règles de synchronisation. Cela vous donne un script PowerShell que vous pouvez utiliser pour facilement les recréer dans le cadre d’une récupération d’urgence.

>[AZURE.WARNING] Les règles de synchronisation out-of-box ont une empreinte numérique. Si vous apportez une modification à ces règles, l'empreinte numérique ne correspondra plus et vous pourrez rencontrer des problèmes dans le futur lorsque vous essaierez d'appliquer une nouvelle version d'Azure AD Connect. Procédez à des modifications uniquement de la façon décrite dans cet article.

### Modifier les flux d’attributs
Dans certains cas, les flux d’attributs par défaut ne fonctionnent pas pour une organisation.

Vous devez suivre ces règles :

- Créez une nouvelle règle de synchronisation avec vos flux d’attributs. Si vous leur attribuez une priorité supérieure (valeur numérique inférieure), vos règles vont remplacer les flux d’attributs out-of-box.
- N’ajoutez pas de flux supplémentaire à une règle out-of-box. Ces modifications seront perdues à la mise à niveau.

Chez Fabrikam, il existe une forêt où l’alphabet local est utilisé pour les prénoms, noms et noms complets. La représentation sous forme de caractères latins de ces attributs est stockée dans les attributs d’extension. Au moment de la création de la liste d’adresses globale dans Azure AD et Office 365, l’organisation souhaite qu’elles soient utilisées.

Avec une configuration par défaut, un objet de la forêt locale ressemble à ceci :

![Flux d’attributs 1](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp1.png)

Pour créer une règle avec d’autres flux d’attributs, procédez comme suit :

- Ouvrez l’**Éditeur de règles de synchronisation** à partir du menu de démarrage.
- L’option **Entrant** étant toujours sélectionnée sur la gauche, cliquez sur le bouton **Ajouter une nouvelle règle**.
- Attribuez à la règle un nom et une description. Sélectionnez Active Directory local et les types d’objets appropriés. Dans **Type de lien**, sélectionnez **Jointure**. Pour le choix de la priorité, sélectionnez un nombre qui n’est pas utilisé par une autre règle. Les règles out-of-box commencent à 100, donc, il est possible d’utiliser la valeur 50 dans cet exemple. ![Flux d’attributs 2](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp2.png)
- Laissez l’option d’étendue vide (elle doit s’appliquer à tous les objets utilisateur dans la forêt).
- Laissez les règles de jointure vides (c’est la règle out-of-box qui doit gérer toutes les jointures).
- Dans Transformations, créez les flux suivants.![Flux d’attributs 3](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp3.png)
- Cliquez sur **Ajouter** pour enregistrer la règle.
- Accédez à **Synchronization Service Manager**. Sur **Connecteurs**, sélectionnez le connecteur auquel nous avons ajouté la règle. Sélectionnez **Exécuter** et **Synchronisation complète**. Une synchronisation complète va recalculer tous les objets en utilisant les règles en cours.

Il s’agit du résultat final pour le même objet avec cette règle personnalisée :

![Flux d’attributs 4](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp4.png)

### Ne transmettez pas d'attribut
Il existe deux manières de ne pas transmettre un attribut. La première est disponible dans l'Assistant d'installation et vous permet de [Supprimer les attributs sélectionnés](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Cette option fonctionne si vous n'avez jamais synchronisé l'attribut auparavant. Toutefois, si vous avez commencé à synchroniser cet attribut et que vous le supprimez ultérieurement avec cette fonctionnalité, le moteur de synchronisation cessera de gérer l'attribut et les valeurs existantes resteront dans Azure AD.

Si vous souhaitez supprimer la valeur d'un attribut et vous assurer qu'il ne sera pas transmis à l'avenir, vous devrez créer une règle personnalisée à la place.

Chez Fabrikam, nous avons réalisé que certains des attributs que nous synchronisons vers le cloud ne devraient pas s'y trouver. Nous souhaitons garantir que ces attributs sont supprimés d'Azure AD.

![Attributs d'extension](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Créer une nouvelle règle de synchronisation entrante et remplir la description ![Descriptions](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Créer des flux d'attributs de type **Expression** et avec la source **AuthoritativeNull**. Le littéral **AuthoritativeNull** indique que la valeur devrait être vide dans l'ordinateur virtuel même si une règle de synchronisation de précédence inférieure essaie de remplir la valeur. ![Attributs d'extension](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Enregistrer la règle de synchronisation. Démarrez **Service de synchronisation**, recherchez le connecteur, sélectionnez **Exécuter** et **synchronisation complète**. Tous les flux d'attributs seront alors recalculés.
- Vérifiez que les modifications prévues vont être exportées en recherchant l'espace de connecteur. ![Suppression progressive](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

### Désactiver une règle de synchronisation indésirable
Ne supprimez pas une règle de synchronisation out-of-box ; elle sera recréée lors de la prochaine mise à niveau.

Dans certains cas, l'Assistant d'installation a produit une configuration qui ne fonctionnera pas pour votre topologie. Par exemple, si vous disposez d'une topologie de forêt compte-ressource, mais que vous avez étendu le schéma dans la forêt de comptes avec le schéma Exchange, alors les règles pour Exchange seront créées pour la forêt de comptes, ainsi que pour la forêt de ressources. Dans ce cas, nous devons désactiver la règle de synchronisation pour Exchange.

![Règle de synchronisation désactivée](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Dans l'image ci-dessus l'Assistant d'installation a trouvé un ancien schéma Exchange 2003 dans la forêt de comptes. Ce schéma a été ajouté avant l'introduction de la forêt de ressources dans l'environnement de Fabrikam. Pour garantir qu'aucun attribut de l'ancienne implémentation Exchange n'est synchronisé, la règle de synchronisation devrait être désactivée comme indiqué.

### Modifier une règle out-of-box
Si vous devez effectuer des modifications d’une règle out-of-box, vous devriez en faire une copie, puis désactiver la règle d’origine. Modifiez ensuite la règle clonée. L’éditeur de règles de synchronisation vous aidera à le faire. Lorsque vous ouvrez une règle out-of-box, cette boîte de dialogue s'affiche :

![Règle d'avertissement out-of-the box](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Sélectionnez **Oui** pour créer une copie de la règle. La règle clonée est ensuite ouverte.

![Règle clonée](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Sur cette règle clonée, effectuez les modifications nécessaires de la portée, de la jointure et des transformations.

## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->