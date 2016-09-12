<properties
	pageTitle="Azure AD Connect Sync : comment modifier la configuration par défaut | Microsoft Azure"
	description="Cet article vous guide dans les changements de configuration d’Azure AD Connect Sync."
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
	ms.date="08/26/2016"
	ms.author="andkjell"/>


# Azure AD Connect Sync : comment modifier la configuration par défaut
L’objectif de cette rubrique est de vous expliquer comment apporter des modifications à la configuration par défaut dans Azure AD Connect Sync. Elle explique pas à pas la procédure pour les scénarios courants. Après avoir lu cette page, vous devriez être en mesure d’apporter des modifications simples à votre configuration en fonction de vos propres règles d’entreprise.

## Éditeur de règles de synchronisation
L’éditeur de règles de synchronisation sert à afficher et modifier la configuration par défaut. Il est disponible dans le menu Démarrer, sous le groupe **Azure Connect AD**. ![Menu Démarrer avec l’Éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

Lorsque vous l’ouvrez, vous accédez directement aux règles par défaut.

![Éditeur de règles de synchronisation](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### Navigation dans l’Éditeur
Les listes déroulantes situées en haut de l’Éditeur vous permettent d’accéder rapidement à une règle particulière. Par exemple, si vous souhaitez afficher les règles comprenant l’attribut proxyAddresses, vous pouvez modifier les listes déroulantes comme suit : ![Filtrage SRE](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png) pour redéfinir le filtrage et charger une nouvelle configuration, appuyez sur la **F5** du clavier.

En haut à droite de l’écran se trouve le bouton **Ajouter une nouvelle règle**. Ce bouton vous permet de créer vos propres règles personnalisées.

En bas, vous disposez de boutons permettant d’agir sur une règle de synchronisation sélectionnée. Les boutons **Modifier** et **Supprimer** permettent de modifier et supprimer des règles. Le bouton **Exporter** génère un script PowerShell pour recréer la règle de synchronisation. Cette procédure vous permet de déplacer une règle de synchronisation d’un serveur vers un autre.

## Création de votre première règle personnalisée
Le changement le plus courant consiste à modifier les flux d’attributs. Les données contenues dans votre répertoire source ne sont peut-être pas telles que vous souhaiteriez les restituer dans Azure AD. Dans l’exemple de cette section, vous voulez faire en sorte que le nom d’un utilisateur donné apparaisse toujours au format **Nom propre**.

### Désactivation du planificateur
Par défaut, le [planificateur](active-directory-aadconnectsync-feature-scheduler.md) s’exécute toutes les 30 minutes. Vous souhaitez vous assurer qu’il ne démarre pas pendant que vous apportez des modifications et corrigez les problèmes de vos nouvelles règles. Pour désactiver temporairement le planificateur, démarrez PowerShell et exécutez `Set-ADSyncScheduler -SyncCycleEnabled $false`

![Désactivation du planificateur](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)

### Création de la règle

1. Cliquez sur **Ajouter une nouvelle règle**.
2. Sur la page **Description**, entrez les informations suivantes : ![Filtrage des règles entrantes](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)
	- Nom : donnez à la règle un nom descriptif.
	- Description : texte descriptif permettant aux autres utilisateurs de comprendre l’objet de la règle.
	- Connected system (système connecté) : système dans lequel se trouve l’objet. Dans ce cas, sélectionnez le connecteur Active Directory.
	- Connected System/Metaverse Object Type (Type de système connecté/d’objet métaverse) : sélectionnez **Utilisateur** et **Personne**, respectivement.
	- Type de lien : remplacez cette valeur par **Jointure**.
	- Priorité : indiquez une valeur unique dans le système. Une valeur numérique inférieure indique une priorité plus élevée.
	- Balise : laissez ce champ vide. Seules les règles par défaut de Microsoft doivent contenir une valeur dans cette zone.
3. Sur la page **Scoping Filter** (Filtre d’étendue), entrez **givenName ISNOTNULL**. ![Filtre d’étendue des règles entrantes](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png) Cette section permet de définir les objets auxquels la règle s’applique. Si vous la laissez vide, la règle s’appliquera à tous les objets utilisateur, y compris les salles de conférence, les comptes de service et les autres objets d’utilisateurs non humains.
4. Ne renseignez pas le champ **Join rule** (Règles de jointure).
5. Sur la page **Transformations**, définissez le type de flux sur **Expression**. Sélectionnez l’attribut cible **giveName** et, dans Source, entrez `PCase([givenName])`. ![Transformations des règles entrantes](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png) Le moteur de synchronisation respecte la casse aussi bien pour le nom de la fonction que pour le nom de l’attribut. Si vous faites une erreur de saisie, un message d’avertissement s’affiche lorsque vous ajoutez la règle. L’éditeur vous permet d’enregistrer et de continuer, mais vous devrez donc rouvrir la règle pour la corriger.
6. Cliquez sur **Ajouter** pour enregistrer la règle.

Votre nouvelle règle personnalisée doit être visible pour les autres règles de synchronisation du système.

### Vérification des modifications
Après avoir apporté une nouvelle modification, il est préférable de s’assurer que tout fonctionne comme prévu et qu’aucune erreur n’est générée. Selon le nombre d’objets dont vous disposez, il existe deux façons de procéder.

1. Exécuter une synchronisation complète de tous les objets
2. Exécuter un aperçu et une synchronisation complète sur un seul objet

Démarrez le **Service de synchronisation** depuis le menu Démarrer. Les étapes décrites dans cette section se trouvent toutes dans cet outil.

1. **Synchronisation complète sur tous les objets** Sélectionnez **Connecteurs** en haut de la page. Identifiez le connecteur que vous avez modifié dans la section précédente (dans ce cas, les services de domaine Active Directory), puis sélectionnez-le. Sous Actions, sélectionnez **Exécuter**, puis **Synchronisation complète** et **OK**. ![Synchronisation complète](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)Les objets ne sont pas mis à jour dans le métaverse. Vous voulez à présent examiner l’objet dans le métaverse.

2. **Aperçu et synchronisation complète sur un seul objet** Sélectionnez **Connecteurs** en haut de la page. Identifiez le connecteur que vous avez modifié dans la section précédente (dans ce cas, les services de domaine Active Directory), puis sélectionnez-le. Sélectionnez **Search Connector Space** (Rechercher l’espace de connecteur). Utilisez l’étendue pour rechercher un objet que vous souhaitez utiliser pour tester les modifications. Sélectionnez l’objet et cliquez sur **Aperçu**. Dans le nouvel écran, sélectionnez **Commit Preview** (Valider l’aperçu). ![Validation de l’aperçu](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png) La modification est maintenant validée dans le métaverse.

**Consulter l’objet dans le métaverse** Vous souhaitez maintenant choisir quelques exemples d’objets pour vous assurer que la valeur correspond à celle attendue et que la règle est bien appliquée. Sélectionnez **Metaverse Search** (Recherche dans le métaverse) en haut de l’écran. Ajoutez les filtres dont vous avez besoin pour rechercher les objets appropriés. Dans les résultats de la recherche, ouvrez un objet. Examinez les valeurs d’attribut et vérifiez dans la colonne **Règles de synchronisation** que la règle a bien été appliquée comme prévu. ![Recherche de métaverse](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)
### Activation du planificateur
Si tout fonctionne comme prévu, vous pouvez réactiver le planificateur. À partir de PowerShell, exécutez `Set-ADSyncScheduler -SyncCycleEnabled $true`.

## Autres modifications courantes du flux d’attributs
Dans la section précédente, nous avons vu comment apporter des modifications à un flux d’attributs. Dans cette section, vous trouverez d’autres exemples. Les étapes de création de la règle de synchronisation ont été condensées, mais vous trouverez la procédure complète dans la section précédente.

### Utiliser un autre attribut que l’attribut par défaut
Chez Fabrikam, il existe une forêt où l’alphabet local est utilisé pour les prénoms, noms et noms complets. La représentation sous forme de caractères latins de ces attributs est stockée dans les attributs d’extension. Au moment de la création de la liste d’adresses globale dans Azure AD et Office 365, l’organisation souhaite que ces attributs soient utilisés.

Avec une configuration par défaut, un objet de la forêt locale ressemble à ceci : ![Flux d’attributs 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

Pour créer une règle avec d’autres flux d’attributs, procédez comme suit :

- Ouvrez l’**Éditeur de règles de synchronisation** depuis le menu Démarrer.
- En maintenant l’option **Entrant** sélectionnée sur la gauche, cliquez sur le bouton **Ajouter une nouvelle règle**.
- Attribuez à la règle un nom et une description. Sélectionnez Active Directory local et les types d’objets appropriés. Dans **Type de lien**, sélectionnez **Jointure**. Pour le choix de la priorité, sélectionnez un nombre qui n’est pas utilisé par une autre règle. Les règles par défaut commencent à 100, donc, il est possible d’utiliser la valeur 50 dans cet exemple. ![Flux d’attributs 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
- Laissez l’option d’étendue vide (elle doit s’appliquer à tous les objets utilisateur de la forêt).
- Laissez les règles de jointure vides (c’est la règle par défaut qui doit gérer toutes les jointures).
- Dans Transformations, créez les flux suivants : ![Flux d’attributs 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
- Cliquez sur **Ajouter** pour enregistrer la règle.
- Accédez à **Synchronization Service Manager**. Sous **Connecteurs**, sélectionnez le connecteur auquel nous avons ajouté la règle. Sélectionnez **Exécuter** et **Synchronisation complète**. Une synchronisation complète recalcule tous les objets en utilisant les règles en cours.

Il s’agit du résultat obtenu pour le même objet avec cette règle personnalisée : ![Flux d’attributs 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### Longueur des attributs
Les attributs de chaîne sont par défaut définis pour être indexables et la longueur maximale est de 448 caractères. Si vous travaillez avec des attributs de chaîne qui peuvent contenir davantage de caractères, assurez-vous d’inclure ce qui suit dans le flux d’attributs : `attributeName` <- `Left([attributeName],448)`

### Modification de userPrincipalSuffix
L’attribut userPrincipalName dans Active Directory n’est pas toujours connu des utilisateurs et peut ne pas convenir comme ID de connexion. L’Assistant d’installation d’Azure AD Connect Sync permet de choisir un autre attribut, par exemple un e-mail, mais dans certains cas l’attribut doit être calculé. Par exemple, la société Contoso possède deux annuaires AD, un pour la production et un pour les tests. Elle veut que les utilisateurs de son client test utilisent un autre suffixe dans l’ID de connexion. `userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

Dans cette expression, prenons tout ce qui figure à gauche du premier signe @ (Word) et concaténons avec une chaîne fixe.

### Convertir un attribut à valeurs multiples en attribut à valeur unique
Certains attributs dans Active Directory sont à valeurs multiples dans le schéma, même s’ils semblent être à valeur unique dans Utilisateurs et ordinateurs Active Directory. L’attribut description constitue un exemple. `description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

Dans cette expression, au cas où l’attribut a une valeur, nous prenons le premier élément (Item) dans l’attribut, nous supprimons les espaces à gauche et à droite (Trim), puis nous conservons les 448 premiers caractères (Left) dans la chaîne.

### Ne transmettez pas d'attribut
Pour plus d’informations sur le scénario de cette section, consultez la section [Contrôler le processus de flux d’attributs](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process).

Il existe deux manières de ne pas transmettre un attribut. La première est disponible dans l'Assistant d'installation et vous permet de [Supprimer les attributs sélectionnés](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Cette option fonctionne si vous n'avez jamais synchronisé l'attribut auparavant. Toutefois, si vous avez commencé à synchroniser cet attribut et que vous le supprimez ultérieurement avec cette fonctionnalité, le moteur de synchronisation cesse de gérer l’attribut et les valeurs existantes sont maintenues dans Azure AD.

Si vous souhaitez supprimer la valeur d’un attribut et vous assurer qu’il ne sera pas transmis à l’avenir, vous devez créer une règle personnalisée à la place.

Chez Fabrikam, nous nous sommes rendus compte que certains des attributs que nous synchronisons vers le cloud ne devraient pas s’y trouver. Nous souhaitons garantir que ces attributs sont supprimés d’Azure AD. ![Attributs d’extension erronés](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

- Créer une nouvelle règle de synchronisation entrante et remplir la description ![Descriptions](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
- Créer des flux d'attributs de type **Expression** et avec la source **AuthoritativeNull**. Le littéral **AuthoritativeNull** indique que la valeur devrait être vide dans l'ordinateur virtuel même si une règle de synchronisation de précédence inférieure essaie de remplir la valeur. ![Transformation des attributs d’extension](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
- Enregistrer la règle de synchronisation. Démarrez **Service de synchronisation**, recherchez le connecteur, sélectionnez **Exécuter** et **synchronisation complète**. Cette étape permet de recalculer tous les flux d’attributs.
- Vérifiez que les modifications prévues vont être exportées en recherchant l'espace de connecteur. ![Suppression progressive](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## Étapes suivantes

En savoir plus sur l’[approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md) et les options disponibles dans les règles de synchronisation.

En savoir plus sur [l’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) utilisé pour les flux d’attributs.

En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l'[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0831_2016-->