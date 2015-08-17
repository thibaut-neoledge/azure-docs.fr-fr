<properties
	pageTitle="Azure AD Connect Sync : configurer le filtrage"
	description="Explique comment configurer le filtrage dans Azure AD Sync Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect Sync : configurer le filtrage

Dans Azure AD Connect Sync, vous pouvez activer le filtrage à tout moment. Si vous avez déjà exécuté les configurations par défaut de la synchronisation d’annuaires et configuré le filtrage, les objets éliminés par le filtrage ne sont plus synchronisés avec Azure AD. Par conséquent, tous les objets présents dans Azure AD précédemment synchronisés puis filtrés sont supprimés d’Azure AD. Si les objets ont été supprimés par inadvertance à la suite d’une erreur de filtrage, vous pouvez recréer les objets dans Azure AD en supprimant vos configurations de filtrage, puis synchroniser vos annuaires à nouveau.

> [AZURE.IMPORTANT]Microsoft ne prend pas en charge la modification ou le fonctionnement d’Azure AD Connect Sync en dehors des actions Azure documentées de façon formelle. Une de ces actions peut entraîner un état incohérent ou non pris en charge d’Azure AD Connect Sync et par conséquent, Microsoft ne peut pas fournir de support technique pour ces déploiements.
 
Les configurations sont conservées lorsque vous effectuez une installation ou une mise à niveau vers une version plus récente d’Azure AD Connect, à l’exception du filtrage basé sur l’attribut sortant. Il est toujours recommandé de vérifier que la configuration n’a pas été modifiée par inadvertance après une mise à niveau vers une version plus récente avant d’exécuter le premier cycle de synchronisation.


## Options de filtrage

> [AZURE.NOTE]Dans cette rubrique, Source AD est utilisé comme nom de connecteur de Service de domaine Active Directory. Si vous avez plusieurs forêts, vous aurez un connecteur par forêt et la configuration doit être répétée pour chacune d’elles.
 
Les trois types de configuration de filtrage peuvent être appliqués à l’outil de synchronisation d’annuaire :

- **Basés sur le domaine** : vous pouvez utiliser ce type de filtrage pour gérer les propriétés du connecteur SourceAD dans Azure AD Sync. Ce type vous permet de sélectionner les domaines autorisés à être synchronisé avec Azure AD.

- **Configurer le filtrage basé sur une unité d’organisation** : vous pouvez utiliser ce type de filtrage pour gérer les propriétés du connecteur SourceAD dans Azure AD Connect Sync. Ce type de filtrage vous permet de sélectionner les unités d’organisation autorisées à se synchroniser avec Azure AD.

- **Basée sur un attribut** : vous pouvez utiliser cette méthode de filtrage pour spécifier des filtres basés sur l’attribut. Cela vous permet de contrôler les objets à synchroniser avec le nuage.

## Configurer le filtrage basé sur une domaine

Cette section vous expose les étapes à suivre pour configurer le filtre de votre domaine.


> [AZURE.NOTE]Si vous avez modifié votre filtre de domaine, vous devez également mettre à jour vos profils d’exécution.
 

**Pour définir le filtre de domaine, exécutez les opérations suivantes :**

1. Ouvrez une session sur l’ordinateur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité **ADSyncAdmins**.

2. Dans **Démarrer**, appuyez ou cliquez sur **Service de synchronisation** pour ouvrir **Synchronization Service Manager**. <br>

3. Pour ouvrir la vue de connecteurs, cliquez sur **Connecteurs** dans le menu **Outils**.

4. Dans la liste **Connecteurs**, sélectionnez le connecteur ayant le **Type** **Active Directory Domain Service**.

5. Pour ouvrir la boîte de dialogue **Propriétés**, cliquez sur **Propriétés** dans le menu **Actions**.

6. Cliquez sur **Configurer des Partitions d’annuaire**.

7. Dans la liste **Sélectionner les partitions d’annuaire**, vérifiez que seules les partitions que vous souhaitez synchroniser sont sélectionnées. <br> >[AZURE.Note]Pour supprimer un domaine du processus de synchronisation, désactivez la case correspondant au domaine.

8. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés**.


Si vous avez mis à jour votre filtre de domaine, vous devez également mettre à jour les profils d’exécution suivants :

- Importation complète
- Synchronisation complète
- Importation d’écart
- Synchronisation d’écart
- Exportation


Si vous avez supprimé une partition de la liste de partitions d’annuaire, vous devez vous assurer que toutes les étapes de profil d’exécution faisant référence à cette partition sont également supprimées.

**Pour supprimer une étape d’un profil d’exécution, exécutez les étapes suivantes :**

1. Dans la liste **Connecteurs**, sélectionnez le connecteur ayant le **Type** **Active Directory Domain Service**.

2. Pour ouvrir la boîte de dialogue **Configurer des profils d’exécution**, cliquez sur **Configurer des profils** dans le menu **Actions**.

3. Dans la liste **Profils d’exécution de connecteur**, sélectionnez le profil d’exécution à configurer

4. Pour chaque étape dans la liste étape, procédez comme suit :

     4\.1. Si nécessaire, cliquez sur les étapes pour développer ses détails.

     4\.2. Si la **valeur** de l’attribut **Partition** est un GUID, cliquez sur Supprimer l’étape.

5. Pour fermer la boîte de dialogue **Configurer des profils d’exécution**, cliquez sur **OK**.

Si vous avez ajouté une partition à la liste de partitions de l’annuaire, vous devez vous assurer qu’un profil d’exécution est disponible sur chacun des profils d’exécution figurant dans la liste ci-dessus.

**Pour ajouter une étape à l’exécution d’un profil d’exécution, exécutez les étapes suivantes :**

1. Dans la liste **Connecteurs**, sélectionnez le connecteur ayant le **Type** **Active Directory Domain Service**.

2. Pour ouvrir la boîte de dialogue **Configurer des profils d’exécution**, cliquez sur **Configurer des profils** dans le menu **Actions**.

3. Dans la liste **Profils d’exécution de connecteur**, sélectionnez le profil d’exécution à configurer

4. Pour ouvrir la boîte de dialogue **Configurer le profil d’exécution**, cliquez sur **Nouvelle étape**.

5. Sur la page **Configurer une étape**, dans la liste de type d’étape, sélectionnez le type d’étape, puis cliquez sur **Suivant**.

6. Sur la page **Configuration du connecteur**, dans la liste **Partition**, sélectionnez le nom de la partition que vous avez ajoutée à votre filtre de domaine.

7. Pour fermer la boîte de dialogue **Configurer un profil d’exécution**, cliquez sur **Terminer**.

8. Pour fermer la boîte de dialogue **Configurer des profils d’exécution**, cliquez sur **OK**.

 

## Configurer le filtrage basé sur une unité d’organisation

**Pour configurer le filtrage basé sur l’unité d’organisation, procédez comme suit :**

1. Ouvrez une session sur l’ordinateur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité **ADSyncAdmins**.

2. Dans **Démarrer**, appuyez ou cliquez sur **Service de synchronisation** pour ouvrir **Synchronization Service Manager**.<br>

3. Dans **Synchronization Service Manager**, cliquez sur **Connecteurs**, puis double-cliquez sur **SourceAD**.

4. Cliquez sur **Configurer des Partitions d’annuaire**, sélectionnez le domaine que vous souhaitez configurer, puis cliquez sur **Conteneurs**.

5. Lorsque vous y êtes invité, saisissez vos informations d’identification sur le domaine de la forêt Active Directory locale.<br> >[AZURE.NOTE]À la présentation de la boîte de dialogue des informations d’identification, le compte utilisé pour importer et exporter vers le DS AD s’affichera. Si vous ne connaissez pas le mot de passe correspondant au compte, vous pouvez saisir un autre compte à utiliser. Le compte que vous utilisez doit avoir les autorisations en lecture sur le domaine en cours de configuration.

6. Dans la boîte de dialogue **Sélectionner des conteneurs**, désactivez les unités d’organisation que vous ne souhaitez pas synchroniser avec l’annuaire Cloud, puis cliquez sur la zone **OK**.

7. Cliquez sur **OK** sur la page **Propriétés SourceAD**.

8. Exécutez une importation complète et une synchronisation d’écart en exécutant les opérations suivantes :

     8\.1. Dans la liste des connecteurs, sélectionnez **SourceAD**

     8\.2. Pour ouvrir la boîte de dialogue **Exécuter le connecteur**, sélectionnez **Exécuter** dans le menu **Actions**.

     8\.3. Dans la liste **Profils d’exécution**, sélectionnez **Importation complète**, puis attendez que le profil d’exécution ait terminé.

     8\.4. Pour ouvrir la boîte de dialogue **Exécuter le connecteur**, sélectionnez **Exécuter** dans le menu **Actions**.

     8\.5. Dans la liste **Profils d’exécution**, sélectionnez **Synchronisation d’écart**, puis attendez que le profil d’exécution ait terminé.




## Configurer le filtrage de l’attribut

Il existe plusieurs façons de configurer le filtrage en fonction d’attributs. La configuration de trafic entrant à partir d’Active Directory est recommandée dans la mesure où ces paramètres de configuration seront conservés même après une mise à niveau vers une version plus récente. La configuration sortante à AAD est pris en charge, mais ces paramètres ne seront pas conservés après une mise à niveau vers une version plus récente et ne doivent être utilisés que lorsqu’il est nécessaire d’examiner l’objet combiné dans les données de métaverse pour déterminer le filtrage.

### Filtrage entrant

Le filtrage entrant exploite la configuration par défaut dans laquelle l’attribut métaverse cloudFiltered des objets transmis à AAD ne doit pas être défini sur une valeur et l’attribut métaverse ObjectType doit être défini sur « Utilisateur » ou « Contact ».

L’attribut cloudFiltered doit être défini sur True lorsque l’objet ne doit pas être synchronisé sur Azure AD et conservé vide dans les autres cas. Cette méthode est utilisée dans les cas où on peut choisir de ne pas synchroniser un objet (filtrage négatif) après l’avoir examiné.

Dans l’exemple qui suit, nous excluons avec un filtre tous les utilisateurs dont l’extensionAttribute15 a pour valeur NoSync :

1. Ouvrez une session sur l’ordinateur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité ADSyncAdmins.
2. Ouvrez l’**Éditeur de règles de synchronisation** dans le **Menu Démarrer**.
3. Assurez-vous que l’option **Entrants** est sélectionnée, cliquez sur **Ajouter une nouvelle règle**.
4. Donnez à la règle un nom descriptif, par exemple « *Entrée d’Active Directory – Utilisateur DoNotSyncFilter* », sélectionnez la forêt appropriée, **Utilisateur** en tant que **Type d’objet CS** et **Personne** comme **Type d’objet MV**. Dans **Type de lien**, sélectionnez **Jointure** et pour la priorité, tapez une valeur non utilisée actuellement par une autre règle de synchronisation (par exemple, 50), puis cliquez sur **Suivant**.
5. Dans **Filtre d’étendue**, cliquez sur **Ajouter un groupe**, cliquez sur **Ajouter une clause** et dans attribut, sélectionnez **ExtensionAttribute15**. Assurez-vous que l’opérateur est défini sur **Égal à** et **entrez** la valeur NoSync dans la zone Valeur. Cliquez sur **Suivant**.
6. Laissez les règles de **Jointure** vides, puis cliquez sur **Suivant**.
7. Cliquez sur **Ajouter une transformation**, définissez le **Type de flux** sur **Constante**, sélectionnez l’attribut cible cloudFiltered et dans la zone de texte Source, saisissez la valeur True. Cliquez sur Ajouter pour enregistrer la règle.
8. Effectuez une synchronisation complète : dans l’onglet **Connecteurs**, cliquez avec le bouton droit sur **SourceAD**, cliquez sur **Exécuter**, sur **Synchronisation complète**, puis sur **OK**. 


L’attribut **sourceObjectType** fournira un **Utilisateur** ou un **Contact** à Azure AD si cet attribut a la valeur **Utilisateur** ou **Contact**, respectivement. En créant une règle de synchronisation avec une priorité supérieure à ceux de la zone, vous pouvez remplacer le comportement par défaut. Cette méthode vous donne également la possibilité d’exprimer des règles à la fois positives et négatives.

Dans l’exemple suivant, vous allez synchroniser uniquement les utilisateurs avec l’attribut service « * Ventes* » ou est vide :

1. Ouvrez une session sur l’ordinateur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité ADSyncAdmins.
2. Ouvrez l’**Éditeur de règles de synchronisation** dans le **menu Démarrer**.
3. Assurez-vous que l’option **Entrants** est sélectionnée, puis cliquez sur **Ajouter une nouvelle règle**.
4. Donnez à la règle un nom descriptif, par exemple « *Entrée d’Active Directory – Utilisateur DoNotSyncFilter* », sélectionnez la forêt appropriée, **Utilisateur** en tant que **Type d’objet CS** et **Personne** comme **Type d’objet MV**. Dans **Type de lien**, sélectionnez **Jointure** et pour le **type de priorité**, tapez une valeur non utilisée actuellement par une autre règle de synchronisation (par exemple, 60). Cliquez sur **Suivant**.
5. Laissez les champs **Filtre d’étendue** et **Règles de jointure** vides et cliquez deux fois sur **Suivant**.
6. Cliquez sur **Ajouter une transformation**, définissez **FlowType** sur **Expression** et l’**attribut cible** sur **sourceObjectType**. Dans **Source**, saisissez l’expression suivante :<br>`IIF(IsNullOrEmpty([department]),NULL,IIF([department]<>”Sales”,”DoNotSync”,NULL))`
7. Cliquez sur Ajouter pour enregistrer la règle.
8. Effectuez une synchronisation complète : dans l’onglet **Connecteurs**, cliquez avec le bouton droit sur **SourceAD**, cliquez sur **Exécuter**, sur **Synchronisation complète**, puis sur **OK**. Voici comment se présenterait le résultat :<br>

> [AZURE.NOTE]Notez que nous utilisons un mélange de cloudFiltered et sourceObjectType pour déterminer quels objets nous souhaitons synchroniser avec AAD.
 
Grâce aux expressions, vous disposez d’options de filtrage très puissantes. Dans l’expression ci-dessus, notez que nous avons fourni le littéral NULL lorsque le service n’est pas présent et le service Ventes. Cela indique que cet attribut n’a pas renvoyé de valeur et les règles par défaut seront évaluées. Nous voulons cela afin de déterminer si c’est un **Utilisateur** ou un **Contact** que nous devons créer dans Azure AD.


## Filtrage basé sur la sortie

Dans certains cas, il est nécessaire d’effectuer le filtrage uniquement lorsque les objets se sont engagés dans le métaverse. Il peut par exemple être nécessaire d’examiner l’attribut de messagerie à partir de la forêt de ressources et l’attribut userPrincipalName à partir de la forêt de comptes pour déterminer si un objet doit être synchronisé. Dans ce cas, nous allons créer le filtrage sur la règle de trafic sortant.

> [AZURE.NOTE]Cette méthode exige la modification des règles de synchronisation. La modification de l’étendue d’une règle de synchronisation est prise en charge, mais les modifications ne sont pas conservées après la mise à niveau vers une version plus récente de Connect de Windows Azure AD. Si vous utilisez le filtrage sortant, prenez note des modifications à apporter et après une mise à niveau, assurez-vous que le filtre est toujours là et réappliquez si nécessaire.
 

Dans cet exemple, nous modifierons le filtrage de sorte que seuls les utilisateurs dont le message et userPrincipalName se termine par @contoso.com seront synchronisés :

1. Ouvrez une session sur l’ordinateur qui exécute Azure AD Connect Sync en utilisant un compte membre du groupe de sécurité ADSyncAdmins.
2. Ouvrez l’Éditeur de règles de synchronisation que vous trouverez dans le Menu Démarrer.
3. Sous Types de règles, cliquez sur Sortie.
4. Recherchez la règle appelée Out vers AAD – Jointure Utilisateur. Cliquez sur Modifier.
5. Cliquez sur le filtre d’étendue dans la barre de navigation gauche. Cliquez sur Ajouter une clause puis, dans Attribute, sélectionnez courrier, dans Opérateur, sélectionnez ENDSWITH et dans valeur, tapez @contoso.com. Cliquez sur Ajouter une clause, puis, dans Attribute, sélectionnez userPrincipalName, dans Opérateur, sélectionnez ENDSWITH, et dans Valeur, saisissez @contoso.com.
6. Cliquez sur Enregistrer.
7. Effectuez une synchronisation complète : sur l’onglet Connecteurs, cliquez avec le bouton droit sur SourceAD, cliquez sur Exécuter, cliquez sur Synchronisation complète, puis cliquez sur OK.



## Ressources supplémentaires

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

 

<!---HONumber=August15_HO6-->