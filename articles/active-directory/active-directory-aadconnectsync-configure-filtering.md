<properties
	pageTitle="Azure AD Connect Sync : configurer le filtrage | Microsoft Azure"
	description="Explique comment configurer le filtrage dans Azure AD Connect Sync."
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
	ms.date="02/16/2016"
	ms.author="andkjell;markusvi"/>


# Azure AD Connect Sync : configurer le filtrage
Le filtrage vous permet de contrôler les objets de votre annuaire local qui doivent apparaître dans Azure AD. La configuration par défaut concerne l’ensemble des objets présents dans tous les domaines des forêts configurées. En général, il s’agit de la configuration recommandée. Par exemple, les utilisateurs finaux qui utilisent les charges de travail Office 365 telles qu’Exchange Online et Skype Entreprise pourront utiliser une liste d’adresses globale complète pour envoyer des courriers électroniques et appeler tout le monde. La configuration par défaut leur offre la même expérience qu’une implémentation locale d’Exchange ou de Lync.

Dans certains cas, il est nécessaire d’apporter certaines modifications à la configuration par défaut. Voici quelques exemples :

- Vous prévoyez d’utiliser la [topologie multi-Azure AD-directory](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Vous devez appliquer un filtre pour déterminer les objets à synchroniser avec un annuaire Azure AD particulier.
- Vous exécutez un pilote pour Azure ou Office 365 et souhaitez uniquement disposer d’un sous-ensemble d’utilisateurs dans Azure AD. Dans le petit pilote, il n’est pas impératif de disposer d’une liste d’adresses globale complète pour illustrer la fonctionnalité.
- Vous avez très grand nombre de comptes de service et autres comptes non personnels dont vous ne voulez pas dans Azure AD.
- Pour des raisons de conformité, vous ne supprimez aucun compte utilisateur local. Vous vous contentez de les désactiver. Cependant, vous souhaitez n’avoir que des comptes actifs dans Azure AD.

Cet article explique comment configurer les différents modes de filtrage.

> [AZURE.IMPORTANT]Microsoft ne prend pas en charge la modification ou le fonctionnement d’Azure AD Connect Sync en dehors des actions Azure documentées de façon formelle. Ces actions peuvent entraîner un état incohérent ou non pris en charge d’Azure AD Connect Sync et par conséquent, Microsoft ne peut pas fournir de support technique pour ces déploiements.

## Principes de base et remarques importantes
Dans Azure AD Connect Sync, vous pouvez activer le filtrage à tout moment. Si vous avez commencé par une configuration de synchronisation d’annuaires par défaut et configuré le filtrage, les objets éliminés par le filtrage ne sont plus synchronisés avec Azure AD. Par conséquent, tous les objets présents dans Azure AD précédemment synchronisés puis filtrés sont supprimés d’Azure AD.

Avant d’apporter des modifications au filtrage, assurez-vous de [désactiver la tâche planifiée](#disable-scheduled-task) afin de ne pas exporter des modifications dont vous n’avez pas encore vérifié l’exactitude.

Dans la mesure où le filtrage peut supprimer de très nombreux objets simultanément, vous souhaitez vous assurer que vos nouveaux filtres sont corrects avant de lancer l’exportation de toutes les modifications vers Azure AD. Après avoir terminé les étapes de configuration, il vous est fortement recommandé d’exécuter les [étapes de vérification](#apply-and-verify-changes) avant d’exporter et d’apporter des modifications à Azure AD.

Pour vous protéger contre la suppression de nombreux objets par inadvertance, la fonctionnalité [éviter des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) est activée par défaut. Si vous supprimez plusieurs objets suite à un filtrage (500 par défaut), vous devez exécuter les opérations décrites dans cet article pour permettre que les suppressions concernent Azure AD.

Si vous utilisez une version antérieure à novembre 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)), modifiez la configuration du filtre et utilisez la synchronisation de mot de passe. Vous devez déclencher une synchronisation complète de tous les mots de passe une fois que vous avez terminé la configuration. Pour savoir comment déclencher une synchronisation complète des mots de passe, consultez [Déclencher une synchronisation complète de tous les mots de passe](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Si vous utilisez la version 1.0.9125 ou ultérieure, l’opération régulière de **synchronisation complète** déterminera également si les mots de passe doivent être synchronisés. Cette opération n’est plus nécessaire actuellement.

Si des objets **utilisateur** ont été supprimés par inadvertance à la suite d’une erreur de filtrage Azure AD, vous pouvez recréer ces objets utilisateur dans Azure AD en supprimant les configurations de filtrage, puis synchroniser à nouveau vos annuaires. Cette opération restaure les utilisateurs présents dans la corbeille dans Azure AD. Toutefois, vous ne pouvez pas annuler la suppression d’autres types d’objets. Par exemple, si vous supprimez accidentellement un groupe de sécurité et que ce dernier a été utilisé comme liste de contrôle l’accès (ACL) d’une ressource, il est impossible de récupérer le groupe et les ACL.

Azure AD Connect supprime uniquement les objets qu’il considère comme étant compris dans la portée. S’il existe dans Azure AD des objets qui ont été créés par un autre moteur de synchronisation et qui ne sont pas dans la portée, l’ajout d’un filtrage ne les supprimera pas. Par exemple, si vous démarrez avec un serveur de synchronisation d’annuaires DirSync qui a créé une copie complète de la totalité de votre annuaire dans Azure AD, et installez un nouveau serveur de synchronisation Azure AD Connect en parallèle avec le filtrage activé dès le départ, les objets supplémentaires créés par DirSync ne seront pas supprimés.

Les configurations de filtrage sont conservées lorsque vous effectuez une installation ou une mise à niveau vers une version plus récente d’Azure AD Connect. Il est toujours recommandé de vérifier que la configuration n’a pas été modifiée par inadvertance après une mise à niveau vers une version plus récente avant d’exécuter le premier cycle de synchronisation.

Si vous disposez de plusieurs forêts, les configurations de filtrage décrites dans cette rubrique doivent être appliquées à chaque forêt (en supposant que vous souhaitez la même configuration pour toutes).

### Désactivation d’une tâche planifiée
Pour désactiver la tâche planifiée qui déclenchera un cycle de synchronisation toutes les 3 heures, procédez comme suit :

1. Lancez **Planificateur de tâche** depuis le menu de démarrage.
2. Sous **Bibliothèque du Planificateur de tâches** directement, trouvez la tâche **Azure AD Sync Scheduler**, cliquez dessus avec le bouton droit de la souris et sélectionnez **Désactiver**. ![Planificateur de tâches](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Vous pouvez apporter des modifications à la configuration et exécuter le moteur de synchronisation manuellement depuis la console **gestionnaire de service de synchronisation**.

Une fois vos modifications de filtrage terminées, n’oubliez pas de revenir et de **réactiver** la tâche.

## Options de filtrage
Les types de configuration de filtrage suivants peuvent être appliqués à l’outil de synchronisation d’annuaire :

- [**Basé sur un groupe**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups) : un filtrage basé sur un seul groupe ne peut être configuré que lors d’une installation initiale à l’aide de l’Assistant installation. Cette option n’est pas abordée dans cette rubrique.

- [**Basé sur un domaine**](#domain-based-filtering) : cette option vous permet de sélectionner les domaines qui seront synchronisés avec Azure AD. Elle permet également d’ajouter et de supprimer des domaines de la configuration du moteur de synchronisation si vous apportez des modifications à votre infrastructure locale une fois Azure AD Connect sync installé.

- [**Basé sur une unité d’organisation**](#organizational-unitbased-filtering) : cette option de filtrage vous permet de sélectionner les unités d’organisation qui seront synchronisées avec Azure AD. Cette option s’applique à tous les types d’objets présents dans les unités d’organisation sélectionnées.

- [**Basé sur l’attribut**](#attribute-based-filtering) : cette option permet de filtrer les objets en fonction des valeurs d’attribut sur les objets. Vous pouvez également avoir des filtres différents pour différents types d’objets.

Vous pouvez utiliser plusieurs options de filtrage en même temps. Vous pouvez par exemple utiliser le filtrage basé sur une unité d’organisation pour inclure des objets dans une unité d’organisation uniquement et, dans le même temps, utiliser un filtrage basé sur un attribut pour filtrer davantage les objets. Lorsque vous utilisez plusieurs méthodes de filtrage, les filtres utilisent l’élément logique AND entre les filtres.

## Filtrage basé sur un domaine
Cette section vous expose les étapes à suivre pour configurer le filtre de votre domaine. Si vous avez ajouté ou supprimé des domaines dans votre forêt après avoir installé Azure AD Connect, vous devez également mettre à jour la configuration du filtrage.

La meilleure façon de modifier le filtrage basé sur le domaine consiste à exécuter l’Assistant d’installation et de modifier le [filtrage domaine et unité organisationnelle](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L’Assistant d’installation automatise toutes les tâches décrites dans cette rubrique.

Vous devez uniquement suivre ces étapes si, pour une raison quelconque, vous ne pouvez pas exécuter l’Assistant d’installation.

Configuration de filtrage basé sur un domaine se compose des étapes suivantes :

- [Sélectionnez les domaines](#select-domains-to-be-synchronized) à inclure dans la synchronisation.
- Pour chaque domaine ajouté et supprimé, réglez les [profils d’exécution](#update-run-profiles).
- [Appliquez et vérifiez les modifications](#apply-and-verify-changes).

### Sélectionnez les domaines à synchroniser
**Pour définir le filtre de domaine, exécutez les opérations suivantes :**

1. Connectez-vous au serveur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité **ADSyncAdmins**.
2. Démarrez le **Service de synchronisation** depuis le menu Démarrer.
3. Sélectionnez **Connecteurs**, puis, dans la liste **Connecteurs**, sélectionnez le premier connecteur de type **Services de domaine Active Directory**. Dans **Actions**, sélectionnez**Propriétés**. ![Propriétés du connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Cliquez sur **Configurer des partitions d’annuaire**.
5. Dans la liste **Sélectionner des partitions d’annuaire**, sélectionnez et désélectionnez les domaines en fonction de vos besoins. Vérifiez que seules les partitions que vous voulez synchroniser sont sélectionnées. ![Partitions](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png) Si vous avez modifié votre infrastructure AD locale et ajouté ou supprimé des domaines dans la forêt, cliquez sur le bouton **Actualiser** pour obtenir une liste mise à jour. Lorsque vous procédez à l’actualisation, vous êtes invité à saisir des informations d’identification. Fournissez des informations d’identification avec accès en lecture à Active Directory en local. Il ne doit pas forcément s’agir de l’utilisateur prédéfini dans la boîte de dialogue. ![Actualisation requise](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Quand vous avez terminé, fermez la boîte de dialogue **Propriétés** en cliquant sur **OK**. Si vous avez supprimé des domaines de la forêt, un message apparaît indiquant qu’un domaine a été supprimé et que la configuration va être nettoyée.
7. Continuez à ajuster les [profils d’exécution](#update-run-profiles).

### Mettre à jour les profils d’exécution
Si vous avez mis à jour votre filtre de domaine, vous devez également mettre à jour les profils d’exécution.

1. Dans la liste **Connecteurs**, assurez-vous que le connecteur que vous avez modifié à l’étape précédente est sélectionné. Dans **Actions**, sélectionnez **Configurer des profils d’exécution**. ![Profils d’exécution du connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Vous devez ajuster les profils suivants :

- Importation complète
- Synchronisation complète
- Importation d’écart
- Synchronisation d’écart
- Exportation

Pour chacun des cinq profils, procédez comme suit pour chaque domaine **ajouté** :

1. Sélectionnez le profil d’exécution, puis cliquez sur **Nouvelle étape**.
2. Sur la page **Configurer une étape**, dans la liste déroulante **Type**, sélectionnez le type d’étape portant le même nom que le profil que vous configurez. Cliquez ensuite sur **Suivant**. ![Profils d’exécution du connecteur](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. Dans la page **Configuration du connecteur**, dans la liste déroulante **Partition**, sélectionnez le nom du domaine que vous avez ajouté à votre filtre de domaine. ![Profils d’exécution du connecteur](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Pour fermer la boîte de dialogue **Configurer un profil d’exécution**, cliquez sur **Terminer**.

Pour chacun des cinq profils, procédez comme suit pour chaque domaine **supprimé** :

1. Sélectionnez le profil d’exécution.
2. Si la **valeur** de l’attribut **Partition** est un identificateur global unique (GUID). Sélectionnez l’étape d’exécution, puis cliquez sur **Supprimer l’étape**. ![Profils d’exécution du connecteur](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

Le résultat final doit être que chaque domaine que vous souhaitez synchroniser doit être répertorié comme une étape dans chaque profil d’exécution.

Pour fermer la boîte de dialogue **Configurer des profils d’exécution**, cliquez sur **OK**.

- Pour achever la configuration, [appliquez et vérifiez les modifications](#apply-and-verify-changes).

## Filtrage basé sur une unité d’organisation
La meilleure façon de modifier le filtrage basé sur une unité d’organisation consiste à exécuter l’Assistant d’installation et de modifier le [filtrage domaine et unité organisationnelle](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). L’Assistant d’installation automatise toutes les tâches décrites dans cette rubrique.

Vous devez uniquement suivre ces étapes si, pour une raison quelconque, vous ne pouvez pas exécuter l’Assistant d’installation.

**Pour configurer le filtrage basé sur l’unité d’organisation, procédez comme suit :**

1. Connectez-vous au serveur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité **ADSyncAdmins**.
2. Démarrez le **Service de synchronisation** depuis le menu Démarrer.
3. Sélectionnez **Connecteurs**, puis, dans la liste **Connecteurs**, sélectionnez le premier connecteur de type **Services de domaine Active Directory**. Dans **Actions**, sélectionnez**Propriétés**. ![Propriétés du connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Cliquez sur **Configurer des partitions d’annuaire**, sélectionnez le domaine que vous voulez configurer, puis cliquez sur **Conteneurs**.
5. Lorsque vous y êtes invité, fournissez des informations d’identification avec accès en lecture à Active Directory en local. Il ne doit pas forcément s’agir de l’utilisateur prédéfini dans la boîte de dialogue.
6. Dans la boîte de dialogue **Sélectionner des conteneurs**, désactivez les unités d’organisation que vous ne voulez pas synchroniser avec l’annuaire cloud, puis cliquez sur **OK**. ![Unité d’organisation](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - Le conteneur **Ordinateurs** doit être sélectionné pour vos ordinateurs Windows 10 afin de réussir la synchronisation avec Azure AD. Si les ordinateurs rattachés à un domaine sont situés dans d’autres unités d’organisation, assurez-vous qu’elles sont sélectionnées.
  - Le conteneur **ForeignSecurityPrincipals** doit être sélectionné si vous disposez de plusieurs forêts avec approbations. Ainsi, l’appartenance au groupe de sécurité inter forêts sera résolue.
  - L’unité d’organisation **RegisteredDevices** doit être sélectionnée si vous avez activé la fonctionnalité d’écriture différée des appareils. Si vous utilisez une autre fonction d’écriture différée telle que l’écriture différée de groupe, assurez-vous que ces emplacements sont sélectionnés.
  - Sélectionnez une autre unité d’organisation dans laquelle les utilisateurs, iNetOrgPersons, groupes, contacts et ordinateurs sont localisés. Dans l’illustration ci-dessus, tous ces éléments se trouvent dans l’unité d’organisation ManagedObjects.
7. Quand vous avez terminé, fermez la boîte de dialogue **Propriétés** en cliquant sur **OK**.
8. Pour achever la configuration, [appliquez et vérifiez les modifications](#apply-and-verify-changes).

## Filtrage par attribut
Vérifiez que vous disposez bien de la version de novembre 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) ou d’une version ultérieure avant d’effectuer ces étapes.

Le filtrage par attribut est le moyen le plus simple de filtrer des objets. Vous pouvez exploiter les possibilités de l’[approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) pour contrôler presque tous les aspects qui déterminent à quel moment un objet doit être synchronisé à Azure AD.

Le filtrage peut être appliqué en [entrée](#inbound-filtering) d’Active Directory vers le métaverse et en [sortie](#outbound-filtering) du métaverse vers Azure AD. Il est recommandé d’appliquer le filtrage entrant, car c’est le moyen le plus facile à garder. Le filtrage sortant doit être utilisé uniquement s’il est nécessaire d’associer des objets issus de forêts différentes avant que l’évaluation puisse avoir lieu.

### Filtrage entrant
Le filtrage entrant exploite la configuration par défaut dans laquelle l’attribut métaverse cloudFiltered des objets transmis à AAD ne doit pas être défini sur une valeur à synchroniser. Si cet attribut a la valeur **True**, l’objet n’est pas synchronisé. De par sa conception, il ne doit pas avoir la valeur **False**. Pour vous assurer que les autres règles peuvent apporter une valeur, cet attribut est supposé n’accepter que les valeurs **True** ou **NULL** (absent).

Dans la zone de filtrage entrant, nous utiliserons les possibilités de l’**étendue** pour déterminer les objets qui doivent ou non être synchronisés. C’est à ce stade que vous allez réaliser des ajustements en fonction des besoins particuliers de votre organisation. Le module d’étendue se sert du **groupe** et de la **clause** pour déterminer si une règle de synchronisation doit se trouver dans l’étendue. Un **groupe** contient une ou plusieurs **clauses**. Il existe un opérateur logique AND entre plusieurs clauses et un opérateur logique OR entre plusieurs groupes.

Intéressons-nous à un exemple : ![Scope](./media/active-directory-aadconnectsync-configure-filtering/scope.png) Voici la lecture qu’il faut en faire **(service = Informatique) OU (service = Ventes et c = US)**.

Dans les exemples et les étapes ci-dessous, nous nous servirons de l’objet utilisateur comme exemple, mais vous pouvez l’utiliser pour tous les types d’objets.

Dans les exemples ci-dessous, les valeurs de priorité utilisées commencent à 500. Cela garantit qu’elles seront évaluées après les règles out-of-box (priorité plus faible, valeur numérique plus élevée).

#### Filtrage négatif, « ne pas synchroniser »
Dans l’exemple qui suit, nous allons exclure (ne pas synchroniser) tous les utilisateurs pour lesquels **extensionAttribute15** a pour valeur **NoSync**.

1. Connectez-vous au serveur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité **ADSyncAdmins**.
2. Ouvrez l’**Éditeur de règles de synchronisation** depuis le menu Démarrer.
3. Assurez-vous que l’option **Entrante** est sélectionnée et cliquez sur **Ajouter une nouvelle règle**.
4. Attribuez à la règle un nom descriptif, par exemple « *Entrée depuis AD – Utilisateur DoNotSyncFilter* ». Sélectionnez la forêt appropriée, **Utilisateur** en tant que **type d’objet CS**, et **Personne** en tant que **type d’objet MV**. Comme **Type de lien**, sélectionnez **Jointure** et pour le type de priorité, tapez une valeur actuellement non utilisée par une autre règle de synchronisation (par exemple 500), puis cliquez sur **Suivant**. ![1 description entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. Dans **Filtre d’étendue**, cliquez sur **Ajouter un groupe**, cliquez sur **Ajouter une clause** et dans attribut, sélectionnez **ExtensionAttribute15**. Vérifiez que l’opérateur est défini sur **EQUAL** et tapez la valeur **NoSync** dans la zone Valeur. Cliquez sur **Suivant**. ![2 étendue entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Laissez les règles de **Jointure**, puis cliquez sur**Suivant**.
7. Cliquez sur **Ajouter une transformation**, définissez le **Type de flux** sur **Constante**, sélectionnez l’attribut cible **cloudFiltered** et dans la zone de texte Source, saisissez la valeur **True**. Cliquez sur **Ajouter** pour enregistrer la règle. ![3 transformation entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Pour achever la configuration, [appliquez et vérifiez les modifications](#apply-and-verify-changes).

#### Filtrage positif, « synchroniser uniquement ces éléments »
L’expression d’un filtrage positif peut être plus difficile, car vous devez également prendre en compte les objets qui ne sont pas évidents à synchroniser, notamment les salles de conférence.

L’option de filtrage positive nécessite deux règles de synchronisation. Une (ou plusieurs) définit la portée correcte des objets à synchroniser et une deuxième règle de synchronisation fourre-tout élimine tous les objets qui n’ont pas encore été identifiés en tant qu’objet à synchroniser.

Dans l’exemple suivant, nous allons synchroniser uniquement les objets utilisateur dont l’attribut « service » a pour valeur **Ventes** :

1. Connectez-vous au serveur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité **ADSyncAdmins**.
2. Ouvrez l’**Éditeur de règles de synchronisation** depuis le menu Démarrer.
3. Assurez-vous que l’option **Entrante** est sélectionnée et cliquez sur **Ajouter une nouvelle règle**.
4. Attribuez à la règle un nom descriptif, par exemple « *Entrée depuis AD - Sync Ventes utilisateur* ». Sélectionnez la forêt appropriée, **Utilisateur** en tant que **type d’objet CS**, et **Personne** en tant que **type d’objet MV**. Comme **Type de lien**, sélectionnez **Jointure** et pour le type de priorité, tapez une valeur actuellement non utilisée par une autre règle de synchronisation (par exemple 501), puis cliquez sur **Suivant**. ![4 description entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. Dans **Filtre d’étendue**, cliquez sur **Ajouter un groupe**, sur **Ajouter une clause** et dans Attribut, sélectionnez **Service**. Vérifiez que l’opérateur est défini sur **EQUAL** et tapez la valeur **Ventes** dans la zone Valeur. Cliquez sur **Suivant**. ![5 étendue entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Laissez les règles de **Jointure**, puis cliquez sur**Suivant**.
7. Cliquez sur **Ajouter une transformation**, définissez le **Type de flux** sur **Constante**, sélectionnez l’attribut cible **cloudFiltered** et dans la zone de texte Source, tapez la valeur **False**. Cliquez sur **Ajouter** pour enregistrer la règle. ![6 transformation entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png) Il s’agit d’un cas particulier où nous allons définir explicitement cloudFiltered sur False.

	Nous devons maintenant créer la règle de synchronisation fourre-tout.

8. Donnez à la règle un nom descriptif, par exemple « *Entrée depuis Active Directory - Filtre fourre-tout utilisateur* ». Sélectionnez la forêt appropriée, **Utilisateur** en tant que **type d’objet CS**, et **Personne** en tant que **type d’objet MV**. Dans **Type de lien**, sélectionnez **Jointure** et pour le type de priorité, entrez une valeur actuellement non utilisée par une autre règle de synchronisation (par exemple, 600). Nous avons sélectionné une valeur de priorité supérieure (priorité moindre) à la règle de synchronisation précédente, mais nous avons également laissé de la place de façon à pouvoir ajouter des règles de synchronisation de filtrage ultérieures lorsque vous souhaitez démarrer la synchronisation de services supplémentaires. Cliquez sur **Suivant**. ![7 description entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)
9. Laissez l’option **Filtre d’étendue** vide, puis cliquez sur **Suivant**. Un filtre vide indique que la règle doit être appliquée à tous les objets.
10. Laissez les règles de **Jointure**, puis cliquez sur**Suivant**.
11. Cliquez sur **Ajouter une transformation**, définissez le **Type de flux** sur **Constante**, sélectionnez l’attribut cible **cloudFiltered** et dans la zone de texte Source, saisissez la valeur **True**. Cliquez sur **Ajouter** pour enregistrer la règle. ![3 transformation entrante](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Pour achever la configuration, [appliquez et vérifiez les modifications](#apply-and-verify-changes).

En cas de besoin, il est possible de créer d’autres règles du premier type avec lesquelles inclure davantage d’objets dans notre synchronisation.

### Filtrage sortant
Dans certains cas, il est nécessaire d’effectuer le filtrage uniquement lorsque les objets se sont engagés dans le métaverse. Il peut par exemple être nécessaire d’examiner l’attribut de messagerie à partir de la forêt de ressources et l’attribut userPrincipalName à partir de la forêt de comptes pour déterminer si un objet doit être synchronisé. Dans ce cas, nous allons créer le filtrage sur la règle de trafic sortant.

Dans cet exemple, nous modifierons le filtrage de sorte que seuls les utilisateurs dont le message et userPrincipalName se termine par @contoso.com seront synchronisés :

1. Connectez-vous au serveur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité **ADSyncAdmins**.
2. Ouvrez l’**Éditeur de règles de synchronisation** depuis le menu Démarrer.
3. Sous Type de règles, cliquez sur **Sortant**.
4. Recherchez la règle appelée **Out vers AAD - Jointure Utilisateur**. Cliquez sur **Modifier**.
5. Dans la fenêtre contextuelle, sélectionnez **Oui** pour créer une copie de la règle.
6. Sur la page **Description**, changez la priorité à une valeur inutilisée, par exemple, 50.
7. Cliquez sur **Filtre d’étendue** dans la barre de navigation gauche. Cliquez sur **Ajouter une clause** puis, dans Attribut, sélectionnez **courrier**, dans Opérateur, sélectionnez **ENDSWITH** et dans valeur, tapez **@contoso.com**. Cliquez sur **Ajouter une clause**, puis, dans Attribut, sélectionnez **userPrincipalName**, dans Opérateur, sélectionnez **ENDSWITH**, et dans Valeur, saisissez **@contoso.com**.
8. Cliquez sur **Enregistrer**.
9. Pour achever la configuration, [appliquez et vérifiez les modifications](#apply-and-verify-changes).

## Appliquer et vérifier les modifications
Une fois que vous avez modifié votre configuration, elle doit être appliquée aux objets déjà présents dans le système. Il se peut également que des objets qui ne se trouvent pas dans le moteur de synchronisation actuellement doivent être traités et nous devions consulter le système source pour vérifier son contenu.

Si vous avez modifié la configuration à l’aide d’un filtrage par **domaine** ou **unité d’organisation**, vous devez procéder à une **importation complète** suivie d’une **synchronisation delta**.

Si vous avez modifié la configuration en utilisant un filtrage d’**attribut**, vous devez exécuter une **synchronisation complète**.

Procédez comme suit :

1. Démarrez le **Service de synchronisation** depuis le menu Démarrer.
2. Sélectionnez **Connecteurs**, et dans la liste **Connecteurs**, sélectionnez le connecteur dont vous avez modifié la configuration précédemment. Dans **Actions**, sélectionnez **Exécuter**. ![Exécution de connecteur](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. Dans **Profils d’exécution**, sélectionnez l’opération mentionnée à la section précédente. Si vous devez exécuter deux actions, exécutez la seconde une fois que la première est terminée, (la colonne **État** correspondant au connecteur sélectionné est à l’état **Inactif**).

Après la synchronisation, toutes les modifications sont indexées pour l’exportation. Avant de réellement effectuer les modifications dans Azure AD, nous voulons vérifier que toutes ces modifications sont correctes.

1. Démarrez une invite de commande et accédez à `%Program Files%\Microsoft Azure AD Sync\bin`
2. Exécution : `csexport "Name of Connector" %temp%\export.xml /f:x` le nom du connecteur se trouve dans le service de synchronisation. Il possède un nom semblable à « contoso.com – AAD » pour Azure AD.
3. Exécuter : `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Vous disposez maintenant d’un fichier dans %temp% nommé export.csv qui peuvent être examiné dans Microsoft Excel. Ce fichier contient toutes les modifications sur le point d’être exportées.
5. Apportez les modifications nécessaires aux données ou à la configuration et réexécutez ces opérations (Importer, synchroniser et vérifier) jusqu’à ce que les modifications sur le point d’être exportées soient attendues.

Lorsque vous êtes satisfait, exportez les modifications apportées à Azure AD.

1. Sélectionnez **Connecteurs** puis, dans la liste **Connecteurs**, sélectionnez le connecteur Azure AD. Dans **Actions**, sélectionnez **Exécuter**.
2. Dans **Profils d’exécution**, sélectionnez **Exporter**.
3. Si les modifications de configuration suppriment de nombreux objets et si leur nombre est supérieur au seuil configuré (par défaut, 500), une erreur d’exportation est affichée. Dans ce cas, vous devez désactiver provisoirement la fonctionnalité [Éviter les suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Il est maintenant temps de réactiver le planificateur.

1. Lancez **Planificateur de tâche** depuis le menu de démarrage.
2. Sous **Bibliothèque du Planificateur de tâches** directement, trouvez la tâche **Azure AD Sync Scheduler**, cliquez dessus avec le bouton droit de la souris et sélectionnez **Activer**.

## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->