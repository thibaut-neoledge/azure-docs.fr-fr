<properties
	pageTitle="Synchronisation des identités et résilience d’attribut en double| Microsoft Azure"
	description="Nouveau comportement de gestion des objets présentant des conflits UPN ou ProxyAddress pendant la synchronisation d’annuaires à l’aide d’Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="markusvi"/>



# Synchronisation des identités et résilience d’attribut en double
La résilience d’attribut en double est une fonctionnalité d’Azure Active Directory afin d’éliminer les problèmes liés aux conflits entre **UserPrincipalName** et **ProxyAddress** lors de l’exécution de l’un des outils de synchronisation de Microsoft.

Ces deux attributs doivent généralement être uniques pour tous les objets **Utilisateur**, **Groupe** ou **Contact** dans un répertoire Azure Active Directory donné.

> [AZURE.NOTE] Seuls les Utilisateurs peuvent avoir des noms UPN.

Cette fonctionnalité active un nouveau comportement qui se trouve dans la partie cloud du pipeline de synchronisation. Par conséquent, cette fonctionnalité convient à tout type de client et pour tout produit de synchronisation Microsoft, y compris Azure AD Connect, DirSync et MIM + Connector. Le terme générique « client de synchronisation » désigne ces produits dans le présent document.

## Comportement actuel
En cas de tentative d’approvisionnement d’un nouvel objet avec une valeur UPN ou ProxyAddress qui enfreint cette contrainte d’unicité, Azure Active Directory bloque la création de l’objet. De même, si un objet est mis à jour avec une valeur UPN ou ProxyAddress qui n’est pas unique, la mise à jour échoue. Le client de synchronisation refait la tentative d’approvisionnement ou la mise à jour à chaque cycle d’exportation ; il échoue à chaque fois jusqu’à la résolution du conflit. Chaque tentative infructueuse génère un e-mail contenant un rapport d’erreur, et une erreur est consignée par le client de synchronisation.

## Comportement avec une résilience d’attribut en double
Au lieu de rejeter l’approvisionnement ou la mise à jour d’un objet comportant un attribut en double, Azure Active Directory met en « quarantaine » l’attribut en double qui enfreint la contrainte d’unicité. Si cet attribut est requis pour l’approvisionnement, comme pour UserPrincipalName, le service affecte une valeur d’espace réservé. Le format de ces valeurs temporaires est « ***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***». Si l’attribut n’est pas obligatoire, comme **ProxyAddress**, Azure Active Directory met simplement en quarantaine l’attribut à l’origine du conflit et poursuit la création ou la mise à jour de l’objet.

Lorsque l’attribut est mis en quarantaine, des informations sur le conflit sont envoyées dans le même e-mail de rapport d’erreur utilisé avec l’ancien comportement. Toutefois, ces informations n’apparaissent qu’une fois dans le rapport d’erreurs (lors de la mise en quarantaine) ; elles ne sont pas consignées dans les e-mails suivants. En outre, étant donné que l’exportation de cet objet a réussi, le client de synchronisation ne consigne pas d’erreur et ne retente pas la création/la mise à jour lors des cycles de synchronisation suivants.

Pour prendre en charge ce comportement, un nouvel attribut a été ajouté aux classes d’objets Utilisateur, Groupe et Contact : **DirSyncProvisioningErrors**

Il s’agit d’un attribut à valeurs multiples utilisé pour stocker les attributs en conflit qui enfreindraient la contrainte d’unicité s’ils étaient ajoutés normalement. Une tâche du minuteur d’arrière-plan a été activée dans Azure Active Directory. Celle-ci s’exécute toutes les heures pour rechercher des conflits d’attributs en double ayant été résolus, puis supprime automatiquement les attributs en question de la quarantaine.

### Activer la résilience d’attribut en double
La fonctionnalité peut être activée en téléchargeant la dernière version du module Azure Active Directory PowerShell, puis en exécutant ce qui suit :

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

La résilience d’UPN et de ProxyAddress peut désormais être activée et désactivée individuellement. Ce comportement changera dans le futur et les deux seront activées pour tous les répertoires Azure AD et ne seront pas en mesure de les désactiver.

## Identification des objets avec DirSyncProvisioningErrors
Il existe actuellement deux méthodes pour identifier les objets qui comportent ces erreurs en raison de conflits de propriété dupliquée : Azure Active Directory PowerShell et le portail d’administration Office 365. Il est prévu d’augmenter la capacité de génération de rapports dans le portail.

### Azure Active Directory PowerShell
Pour les applets de commande PowerShell dans cette rubrique, les conditions suivantes sont vérifiées :

- Toutes les applets de commande suivantes sont sensibles à la casse.
- L’indicateur **–ErrorCategory PropertyConflict** doit toujours être inclus. Il n’existe actuellement pas d’autres types de **ErrorCategory**, mais cela pourrait changer.

Commencez par exécuter **Connect-MsolService** et entrer les informations d’identification d’un administrateur client.

Utilisez ensuite les applets de commande et les opérateurs suivants pour afficher les erreurs de différentes manières :

1. [Afficher tout](#see-all)

2. [Par type de propriété](#by-property-type)

3. [Par valeur en conflit](#by-conflicting-value)

4. [À l’aide d’une recherche de chaîne](#using-a-string-search)

5. [Triées](#sorted)

6. [Par quantité limitée ou l’ensemble des erreurs](#in-a-limited-quantity-or-all)


#### Afficher tout
Une fois connecté, exécutez ce qui suit pour voir une liste générale d’erreurs d’approvisionnement d’attribut du client :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Le résultat ressemble à ce qui suit : ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")


#### Par type de propriété
Pour voir les erreurs par type de propriété, ajoutez l’indicateur **-PropertyName** à l’argument **UserPrincipalName** ou **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### Par valeur en conflit
Pour afficher les erreurs concernant une propriété spécifique, ajoutez l’indicateur **-PropertyValue** (**-PropertyName** doit également être utilisé avec cet indicateur) :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### À l’aide d’une recherche de chaîne
Pour effectuer une recherche de chaîne élargie, utilisez l’indicateur **-SearchString**. Il peut s’utiliser indépendamment des autres indicateurs ci-dessus, à l’exception de **-ErrorCategory PropertyConflict**, qui est toujours requis :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### Triées
Deux indicateurs peuvent être utilisés pour trier les résultats d’une requête donnée :

1.	**SortField** : les arguments valides sont DisplayName et UserPrincipalName.

2.	**SortDirection** : les arguments valides sont Ascending et Descending.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SortField UserPrincipalName -SortDirection Ascending`

#### Par quantité limitée ou l’ensemble des erreurs
1. **MaxResults <Int>** permet de limiter la requête à un nombre spécifique de valeurs.

2. **All** permet de vérifier que tous les résultats sont récupérés, notamment si le nombre d’erreurs est élevé.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## Portail d’administration Office 365

Vous pouvez afficher les erreurs de synchronisation d’annuaires dans le Centre d’administration Office 365. Le rapport sur le portail Office 365 n’affiche que les objets **Utilisateur** qui présentent ces erreurs. Il n’indique pas d’informations sur les conflits entre **Groupes**, **Contacts** ou **PublicFolders**.


![Utilisateurs actifs](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Utilisateurs actifs")

Pour obtenir des instructions sur l’affichage des erreurs de synchronisation d’annuaires dans le Centre d’administration Office 365, consultez [Identifier les erreurs de synchronisation d’annuaires dans Office 365](https://support.office.com/fr-FR/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### Rapport d’erreur de synchronisation d’identité
Lorsqu’un objet présentant un conflit d’attribut en double est traité avec ce nouveau comportement, une notification afférente est incluse dans l’e-mail standard contenant le rapport d’erreur de synchronisation d’identité. Ce dernier est envoyé au contact du client en charge des notifications techniques. Toutefois, ce comportement présente un changement majeur. Auparavant, les informations de conflit d’attribut en double apparaissaient dans chaque rapport d’erreurs généré jusqu’à la résolution du conflit. Avec ce nouveau comportement, la notification d’erreur pour un conflit donné n’apparaît qu’une fois : au moment où l’attribut en conflit est mis en quarantaine.

Voici un exemple de notification par e-mail d’un conflit ProxyAddress : ![Utilisateurs actifs](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Utilisateurs actifs")

## Résolution des conflits
Les stratégies et tactiques de résolution des problèmes pour ces erreurs ne doivent pas différer de la façon dont les erreurs d’attribut en double ont été traitées par le passé. La seule différence est que la tâche du minuteur effectue un balayage du client côté serveur afin d’ajouter automatiquement l’attribut en question à l’objet concerné lorsque le conflit est résolu.

L’article suivant présente différentes stratégies de dépannage et de résolution : [Les attributs en double ou non valides empêchent la synchronisation d’annuaires dans Office 365](https://support.microsoft.com/kb/2647098).

## Problèmes connus
Aucun de ces problèmes connus n’entraîne une dégradation du service ou une perte des données. Plusieurs d’entre eux relèvent de l’esthétisme ; d’autres génèrent des erreurs d’attribut en double (« *pré-résilience* »), au lieu de mettre en quarantaine l’attribut à l’origine du conflit ; un dernier requiert un travail de correction manuelle supplémentaire pour certaines erreurs.

**Comportement de base :**

1. Un utilisateur ayant une configuration d’attribut spécifique continue à recevoir des erreurs d’exportation ; les attributs ne sont pas mis en quarantaine. Par exemple :

    a. Le nouvel utilisateur est créé dans AD avec les attributs UPN **Joe@contoso.com** et ProxyAddress **smtp:Joe@contoso.com**

    b. Les propriétés de cet objet sont en conflit avec un Groupe existant, où ProxyAddress est **SMTP:Joe@contoso.com**.

    c. Lors de l’exportation, une erreur de **conflit ProxyAddress** est générée au lieu de la mise en quarantaine des attributs à l’origine du conflit. L’opération est retentée à chaque cycle de synchronisation, comme cela était le cas avant l’activation de la fonction de résilience.

2. La tâche du minuteur qui recherche les conflits d’attributs en double résolus compare uniquement les conflits UPN avec d’autres conflits UPN. Ceci provoque le problème indiqué à l’étape 4 du scénario suivant :

    a. **UserA@contoso.com** a un UPN qui n’est pas unique, car l’attribut ProxyAddress d’un autre objet a également cette valeur.

    b. UserA reçoit un **UPN MOERA** temporaire, **UserA1234@contoso.onmicrosoft.com**, et la valeur réelle de l’UPN est mise en quarantaine (comme prévu).

    c. L’autre objet en conflit voit son paramètre ProxyAddress supprimé ultérieurement.

    d. L’UPN de UserA n’est jamais résolu automatiquement ; il doit être mis à jour manuellement.

3. Si deux Groupes sont créés en local avec la même adresse SMTP, l’approvisionnement de l’un d’entre eux échoue à la première tentative, ce qui génère une erreur standard d’attribut **ProxyAddress** en double. Toutefois, la valeur en double est bien mise en quarantaine lors du prochain cycle de synchronisation.

**Applets de commande PowerShell** :

1. **ImmutableId** / **LastDirSyncTime** ne sont pas affichés pour la classe d’objets Utilisateur.

2. Les indicateurs **SortField** et **SortDirection** n’ont aucun effet sur les résultats.

3. L’utilisation de l’indicateur **PropertyValue** sans l’indicateur **PropertyName** génère une erreur d’ambiguïté.

4. L’indicateur **SearchString** renvoie des résultats supplémentaires s’il est exécuté sans les indicateurs **PropertyValue** et **PropertyName**.

**Rapport du portail Office** :

1. Le message d’erreur détaillé pour deux objets dans un ensemble de conflit UPN est le même. Cela indique que l’UPN des deux objets a changé/été mis en quarantaine, alors que seules les données de l’un d’entre eux ont changé.

2. Le message d’erreur détaillé d’un conflit UPN affiche une propriété displayName incorrecte pour un utilisateur dont l’UPN a changé/été mis en quarantaine. Par exemple :

    a. L’**utilisateur A** est synchronisé en premier avec **UPN = User@contoso.com**.

    b. L’**utilisateur B** fait ensuite l’objet d’une tentative de synchronisation avec **UPN = User@contoso.com**.

    c. L’UPN de l’**utilisateur B** est remplacé par **User1234@contoso.onmicrosoft.com** et **User@contoso.com** est ajouté dans **DirSyncProvisioningErrors**.

    d. Le message d’erreur de l’**utilisateur B** doit indiquer que l’**utilisateur A** a déjà **User@contoso.com** comme UPN, mais il affiche le paramètre displayName de l’**utilisateur B**.

3. Il est possible que le rapport n’affiche que des informations détaillées sur les erreurs concernant les utilisateurs qui rencontrent des conflits d’**UPN**, et non les utilisateurs qui rencontrent des erreurs **ProxyAddress** (nous essayons de déterminer si ce problème est permanent ou lié à l’environnement utilisé).

## Voir aussi

- [Synchronisation d’Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

- [Identifier les erreurs de synchronisation d’annuaires dans Office 365](https://support.office.com/fr-FR/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

<!---HONumber=AcomDC_0817_2016-->