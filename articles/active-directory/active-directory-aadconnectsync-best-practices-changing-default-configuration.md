<properties
	pageTitle="Meilleures pratiques pour la modification de la configuration par défaut | Microsoft Azure"
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
	ms.date="11/11/2015"
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

## Modifications apportées aux règles de synchronisation

L'Assistant d’installation fournit une configuration qui est censée fonctionner pour les scénarios les plus courants. Au cas où vous devez apporter des modifications à la configuration, vous devez suivre ces règles pour toujours avoir une configuration prise en charge.

- La seule modification prise en charge d'une règle de synchronisation out-of-box consiste est sa désactivation. Toute autre modification pourra se perdre lors d'une mise à niveau.
- Si vous avez besoin effectuer toute autre modification d'une règle out-of-box, faites-en une copie, puis désactivez la règle d'origine. L'éditeur de règles de synchronisation vous y invitera et vous aidera à le faire.
- Exportez vos règles de synchronisation personnalisées à l’aide de l’éditeur de règles de synchronisation. Cela vous donne un script PowerShell que vous pouvez utiliser pour facilement les recréer dans le cadre d’une récupération d’urgence.

>[AZURE.WARNING]Les règles de synchronisation out-of-box ont une empreinte numérique. Si vous apportez une modification à ces règles, l'empreinte numérique ne correspondra plus et vous pourrez rencontrer des problèmes dans le futur lorsque vous essaierez d'appliquer une nouvelle version d'Azure AD Connect. Procédez à des modifications uniquement de la façon décrite dans cet article.

### Supprimer une règle de synchronisation indésirable
Ne supprimez pas une règle de synchronisation out-of-box ; elle sera recréée lors de la prochaine mise à niveau.

Dans certains cas, l'Assistant d'installation a produit une configuration qui ne fonctionnera pas pour votre topologie. Par exemple, si vous disposez d'une topologie de forêt compte-ressource, mais que vous avez étendu le schéma dans la forêt de comptes avec le schéma Exchange, alors les règles pour Exchange seront créées pour la forêt de comptes, ainsi que pour la forêt de ressources. Dans ce cas, nous devons désactiver la règle de synchronisation pour Exchange.

![Règle de synchronisation désactivée](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Dans l'image ci-dessus l'Assistant d'installation a trouvé un ancien schéma Exchange 2003 dans la forêt de comptes. Ce schéma a été ajouté avant l'introduction de la forêt de ressources dans l'environnement de Fabrikam. Pour garantir qu'aucun attribut de l'ancienne implémentation Exchange n'est synchronisé, la règle de synchronisation devrait être désactivée comme indiqué.

### Modifier les règles out-of-box
Si vous devez effectuer des modifications d’une règle out-of-box, vous devriez en faire une copie, puis désactiver la règle d’origine. Modifiez ensuite la règle clonée. L’éditeur de règles de synchronisation vous aidera à le faire. Lorsque vous ouvrez une règle out-of-box, cette boîte de dialogue s'affiche :

![Règle d'avertissement out-of-the box](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Sélectionnez **Oui** pour créer une copie de la règle. La règle clonée est ensuite ouverte.

![Règle clonée](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Sur cette règle clonée, effectuez les modifications nécessaires de la portée, de la jointure et des transformations.

### Ne transmettez pas d'attribut
Il existe deux manières de ne pas transmettre un attribut. La première est disponible dans l'Assistant d'installation et vous permet de [Supprimer les attributs sélectionnés](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Cette option fonctionne si vous n'avez jamais synchronisé l'attribut auparavant. Toutefois, si vous avez commencé à synchroniser cet attribut et que vous le supprimez ultérieurement avec cette fonctionnalité, le moteur de synchronisation cessera de gérer l'attribut et les valeurs existantes resteront dans Azure AD.

Si vous souhaitez supprimer la valeur d'un attribut et vous assurer qu'il ne sera pas transmis à l'avenir, vous devrez créer une règle personnalisée à la place.

Chez Fabrikam, nous avons réalisé que certains des attributs que nous synchronisons vers le cloud ne devraient pas s'y trouver. Nous souhaitons garantir que ces attributs sont supprimés d'Azure AD.

![Attributs d'extension](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Créer une nouvelle règle de synchronisation entrante et remplir la description ![Descriptions](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Créer des flux d'attributs de type **Expression** et avec la source **AuthoritativeNull**. Le littéral **AuthoritativeNull** indique que la valeur devrait être vide dans l'ordinateur virtuel même si une règle de synchronisation de précédence inférieure essaie de remplir la valeur. ![Attributs d'extension](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Enregistrer la règle de synchronisation. Démarrez **Service de synchronisation**, recherchez le connecteur, sélectionnez **Exécuter** et **synchronisation complète**. Tous les flux d'attributs seront alors recalculés.
- Vérifiez que les modifications prévues vont être exportées en recherchant l'espace de connecteur. ![Suppression progressive](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## Étapes suivantes
En savoir plus sur la configuration d’[Azure AD Connect sync](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Nov15_HO4-->