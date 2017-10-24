---
title: "Alimenter des groupes dynamiquement en fonction d’attributs d’objet dans Azure Active Directory | Documents Microsoft"
description: "Procédure de création de règles avancées pour une adhésion de groupe incluant des paramètres et des opérateurs de règle d’expression pris en charge."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.openlocfilehash: 168829494de33a21df68d3fc5e2a174f435c5ad5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="populate-groups-dynamically-based-on-object-attributes"></a>Alimenter des groupes dynamiquement en fonction d’attributs utilisateur
Le portail Azure Classic vous permet d’activer des appartenances dynamiques plus complexes basées sur les attributs aux groupes Azure Active Directory (Azure AD).  

Lorsqu’un attribut d’un utilisateur ou d’un appareil change, le système évalue toutes les règles de groupe dynamique d’un annuaire pour voir si la modification déclenche des ajouts ou suppressions de groupe. Si un utilisateur ou un appareil respecte une règle d’un groupe, il est ajouté en tant que membre de ce groupe. S’il ne respecte plus la règle, il est supprimé.

> [!NOTE]
> - Vous pouvez définir une règle d’appartenance dynamique sur les groupes de sécurité ou Office 365.
>
> - Cette fonctionnalité nécessite une licence Azure AD Premium P1 pour chaque utilisateur membre ajouté à au moins un groupe dynamique.
>
> - Vous pouvez créer un groupe dynamique pour les appareils ou utilisateurs, mais vous ne pouvez pas créer une règle qui contient à la fois des objets d’utilisateur et d’appareils.

> - Il est actuellement impossible de créer un groupe d’appareil basé sur les attributs d’un utilisateur propriétaire. Les règles d’appartenance d’un appareil ne peuvent définir que des attributs immédiats d’objets d’appareil dans le répertoire.

## <a name="to-create-an-advanced-rule"></a>Pour créer une règle avancée
1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis ouvrez le répertoire de votre organisation.
2. Sélectionnez l’onglet **Groupes** , puis ouvrez le groupe que vous souhaitez modifier.
3. Sélectionnez l’onglet **Configurer**, sélectionnez l’option **Règle avancée** et entrez la règle avancée dans la zone de texte.

## <a name="constructing-the-body-of-an-advanced-rule"></a>Construction du corps d’une règle avancée
La règle avancée que vous pouvez créer pour l’appartenance dynamique à des groupes est essentiellement une expression binaire qui se compose de trois parties et qui génère un résultat true ou false. Les trois parties sont les suivantes :

* Paramètre de gauche (leftParameter)
* Opérateur binaire (binaryOperator)
* Constante de droite (rightConstant)

Une règle avancée complète ressemble à ceci : (leftParameter binaryOperator «RightConstant»). Elle nécessite des parenthèses ouvrantes et fermantes pour l’ensemble de l’expression binaire, des guillemets doubles pour la constante de droite et l’utilisation de la syntaxe user.property pour le paramètre de gauche. Une règle avancée peut se composer de plusieurs expressions binaires séparées par les opérateurs logiques -and, -or et -not.
Voici des exemples de règles avancées correctement construites :

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Pour obtenir la liste complète des paramètres et des opérateurs de règle d’expression pris en charge, consultez les sections ci-dessous.


Notez que la propriété doit être préfixée par le type d’objet approprié : utilisateur ou appareil.
La règle ci-dessous échoue à la validation : mail –ne null

La règle correcte serait :

user.mail –ne null

La longueur totale du corps de votre règle avancée ne peut pas dépasser 2 048 caractères.

> [!NOTE]
> Les opérations de chaîne et regex (expressions régulières) ne prennent pas en compte la casse.
> Les chaînes contenant des guillemets doubles doivent être placées dans une séquence d’échappement à l’aide du caractère « ' ». Par exemple : `"\`Sales".
> Utilisez uniquement des guillemets pour les valeurs de type chaîne ; utilisez uniquement des guillemets anglais.
>
>

## <a name="supported-expression-rule-operators"></a>Opérateurs de règle d’expression pris en charge
Le tableau suivant répertorie tous les opérateurs de règle d’expression pris en charge et leur syntaxe à utiliser dans le corps de la règle avancée :

| Opérateur | Syntaxe |
| --- | --- |
| Non égal à |-ne |
| Égal à |-eq |
| Ne commence pas par |-notStartsWith |
| Commence par |-startsWith |
| Ne contient pas |-notContains |
| Contient |-contains |
| Ne correspond pas |-notMatch |
| Correspond |-match |
| Dans | -in |
| Pas dans | -notIn |

## <a name="operator-precedence"></a>Précédence des opérateurs

Tous les opérateurs sont répertoriés ci-dessous par précédence, de la plus basse à la plus élevée ; les opérateurs dans la même ligne sont de même précédence : -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn

Tous les opérateurs peuvent être utilisés avec ou sans le préfixe de trait d’union.

Notez que les parenthèses ne sont pas toujours nécessaires, vous devez uniquement ajouter des parenthèses lorsque la précédence ne satisfait pas vos exigences Par exemple :

   user.department –eq "Marketing" –and user.country –eq "US"

équivaut à :

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>Utilisation des opérateurs -in et -notIn

Si vous souhaitez comparer la valeur d’un attribut utilisateur par rapport à un nombre de valeurs différentes, vous pouvez utiliser les opérateurs -in ou -notin. Voici un exemple d’utilisation d’un opérateur -in :

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

Notez l’utilisation de « [ » et «] » au début et à la fin de la liste de valeurs. Cette condition évalue sur True la valeur de user.department égale à l’une des valeurs dans la liste.

## <a name="query-error-remediation"></a>Correction d’erreur de requête
Le tableau suivant répertorie les erreurs potentielles et la méthode pour les corriger si elles se produisent

| Erreur d’analyse de requête | Utilisation incorrecte | Utilisation corrigée |
| --- | --- | --- |
| Erreur : attribut non pris en charge. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>La propriété doit correspondre à l’une de celles figurant dans la [liste des propriétés prises en charge](#supported-properties). |
| Erreur : l’opérateur n’est pas pris en charge sur l’attribut. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/>La propriété est de type booléen. Utilisez les opérateurs pris en charge (-eq ou -ne) sur un type booléen dans la liste ci-dessus. |
| Erreur : erreur de compilation de la requête. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>L’opérateur logique doit correspondre à l’une des valeurs de la liste des propriétés prises en charge ci-dessus. (user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Error dans l’expression régulière. |
| Erreur : l’expression binaire n’est pas au format correct. |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La requête comporte plusieurs erreurs. Une parenthèse n’est pas au bon endroit. |
| Erreur : une erreur inconnue s’est produite lors de la configuration des appartenances dynamiques. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La requête comporte plusieurs erreurs. Une parenthèse n’est pas au bon endroit. |

## <a name="supported-properties"></a>Propriétés prises en charge
Voici toutes les propriétés d’utilisateur que vous pouvez utiliser dans vos règles avancées :

### <a name="properties-of-type-boolean"></a>Propriétés de type booléen
Opérateurs autorisés

* -eq
* -ne

| Propriétés | Valeurs autorisées | Usage |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Propriétés de type chaîne
Opérateurs autorisés

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| Propriétés | Valeurs autorisées | Usage |
| --- | --- | --- |
| city |Toute valeur de chaîne ou $null |(user.city -eq "value") |
| country |Toute valeur de chaîne ou $null |(user.country -eq "value") |
| companyName | Toute valeur de chaîne ou $null | (user.companyName -eq "value") |
| department |Toute valeur de chaîne ou $null |(user.department -eq "value") |
| displayName |Toute valeur de chaîne. |(user.displayName -eq "value") |
| facsimileTelephoneNumber |Toute valeur de chaîne ou $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Toute valeur de chaîne ou $null |(user.givenName -eq "value") |
| jobTitle |Toute valeur de chaîne ou $null |(user.jobTitle -eq "value") |
| mail |Toute valeur de chaîne ou $null (adresse SMTP de l’utilisateur) |(user.mail -eq "value") |
| mailNickName |Toute valeur de chaîne (alias de messagerie de l’utilisateur) |(user.mailNickName -eq "value") |
| mobile |Toute valeur de chaîne ou $null |(user.mobile -eq "value") |
| objectId |GUID de l’objet utilisateur |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificateur de sécurité (SID) local pour les utilisateurs synchronisés localement vers le cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Aucune DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Toute valeur de chaîne ou $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Toute valeur de chaîne ou $null |(user.postalCode -eq "value") |
| preferredLanguage |Code ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Toute valeur de chaîne ou $null |(user.sipProxyAddress -eq "value") |
| state |Toute valeur de chaîne ou $null |(user.state -eq "value") |
| streetAddress |Toute valeur de chaîne ou $null |(user.streetAddress -eq "value") |
| surname |Toute valeur de chaîne ou $null |(user.surname -eq "value") |
| telephoneNumber |Toute valeur de chaîne ou $null |(user.telephoneNumber -eq "value") |
| usageLocation |Paramètre régional à deux lettres |(user.usageLocation -eq "US") |
| userPrincipalName |Toute valeur de chaîne. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Propriétés de type collection de chaînes
Opérateurs autorisés

* -contains
* -notContains

| Propriétés | Valeurs autorisées | Usage |
| --- | --- | --- |
| otherMails |Toute valeur de chaîne. |(user.otherMails -contains "alias@domain") |

| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>Propriétés à valeurs multiples
Opérateurs autorisés

* -any (respectée lorsqu’au moins un élément de la collection correspond à la condition)
* -all (respectée lorsque tous les éléments de la collection correspondent à la condition)

| Propriétés | Valeurs | Usage |
| --- | --- | --- |
| assignedPlans |Chaque objet de la collection affiche les propriétés de chaînes suivantes : capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -et assignedPlan.capabilityStatus -eq "Enabled") |

Les propriétés à valeurs multiples sont des collections d’objets du même type. Vous pouvez utiliser les opérateurs -any et -all pour appliquer respectivement une condition à un ou tous les objets de la collection. Par exemple :

assignedPlans est une propriété à valeurs multiples qui répertorie tous les plans de service assignés à l’utilisateur. L’expression ci-dessous sélectionne les utilisateurs dont le plan de service Exchange Online (Plan 2) est également dans l’état activé :

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(L’identificateur Guid détermine le plan de service Exchange Online (Plan 2).)

> [!NOTE]
> Ceci est utile si vous souhaitez identifier tous les utilisateurs pour lesquels une fonctionnalité Office 365 (ou tout autre service en ligne Microsoft) a été activée, pour cibler un ensemble de stratégies défini, par exemple.

L’expression suivante sélectionne tous les utilisateurs qui disposent d’un plan de service associé au service Intune (identifié par le nom de service « SCO ») :
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Utiliser des valeurs Null

Pour spécifier une valeur null dans une règle, vous pouvez utiliser « null » ou $null. Exemple :

   user.mail –ne null équivaut à user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>Attributs d’extension et attributs personnalisés
Les attributs d’extension et les attributs personnalisés sont pris en charge dans les règles d’appartenance dynamique.

Les attributs d’extension sont synchronisés à partir de Windows Server AD local et prennent le format « ExtensionAttributeX », lorsque X est égal à 1-15.
Voici en exemple de règle utilisant un attribut d’extension :

(user.extensionAttribute15 -eq "Marketing")

Les attributs personnalisés sont synchronisés à partir de Windows Server AD local ou d’une application SaaS connectée et le format de « user.extension[GUID]\__[Attribut] », lorsque [GUID] est l’identificateur unique dans AAD pour l’application qui a créé l’attribut dans AAD et [Attribut] est le nom de l’attribut tel qu’il a été créé.
Voici un exemple de règle utilisant un attribut personnalisé :

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

Vous pouvez accéder au nom de l’attribut personnalisé dans le répertoire en lançant une requête sur un attribut d’utilisateur, à l’aide de l’explorateur graphique, et en recherchant le nom d’attribut.
Actuellement, nous ne prenons pas en charge les attributs à valeurs multiples synchronisés à partir de l’annuaire Active Directory local.

## <a name="direct-reports-rule"></a>Règle de « collaborateurs directs »
Vous pouvez créer un groupe contenant tous les collaborateurs directs d’un responsable. À l’avenir, lorsque les collaborateurs directs d’un responsable changeront, l’appartenance du groupe sera ajustée automatiquement.

> [!NOTE]
> 1. Pour que la règle fonctionne, assurez-vous que la propriété **ID Responsable** est correctement définie sur les utilisateurs de votre client. Vous pouvez vérifier la valeur actuelle d’un utilisateur sur son **onglet Profil**.
> 2. Cette règle prend uniquement en charge les collaborateurs **directs**. Il est actuellement impossible de créer un groupe pour une hiérarchie imbriquée, par exemple un groupe qui inclut des collaborateurs directs et leurs rapports.

**Pour configurer le groupe**

1. Suivez les étapes 1 à 5 de la section [Pour créer la règle avancée](#to-create-the-advanced-rule), puis sélectionnez le **type d’appartenance** de l’**utilisateur dynamique**.
2. Dans le panneau **Règles d’appartenance dynamique** , saisissez la règle avec la syntaxe suivante :

    *Collaborateurs directs pour {ID objet_du_responsable}*

    Exemple de règle valide :
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. Une fois la règle enregistrée, tous les utilisateurs avec la valeur ID Responsable spécifiée seront ajoutés au groupe.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>Utilisation d’attributs pour créer des règles pour les objets d’appareil
Vous pouvez également créer une règle qui sélectionne des objets d’appareil pour l’appartenance à un groupe. Les attributs d’appareil suivants peuvent être utilisés :

| Propriétés              | Valeurs autorisées                     | Usage                                                       |
|-------------------------|------------------------------------|-------------------------------------------------------------|
| accountEnabled          | true false                         | (device.accountEnabled -eq true)                            |
| displayName             | Toute valeur de chaîne.                   | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | Toute valeur de chaîne.                   | (device.deviceOSType -eq "Android")                         |
| deviceOSVersion         | Toute valeur de chaîne.                   | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | Un nom de catégorie d’appareil valide.       | (device.deviceCategory -eq "BYOD")                          |
| deviceManufacturer      | Toute valeur de chaîne.                   | (device.deviceManufacturer -eq "Samsung")                   |
| deviceModel             | Toute valeur de chaîne.                   | (device.deviceModel -eq "iPad Air”)                         |
| deviceOwnership         | Personnel, Entreprise                  | (device.deviceOwnership -eq "Company")                      |
| domainName              | Toute valeur de chaîne.                   | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | Nom de profil de l’inscription d’appareil Apple.    | (device.enrollmentProfileName -eq "DEP iPhones")       |
| isRooted                | true false                         | (device.isRooted -eq true)                              |
| managementType          | « MDM » pour les appareils mobiles, « PC » pour les ordinateurs gérés via l’agent Intune PC    | (device.managementType -eq "MDM")                  |
| organizationalUnit      | Toute valeur de chaîne correspondant au nom de l’unité d’organisation définie par le Active Directory local | (device.organizationalUnit -eq "US PCs")      |
| deviceId                | un ID d’appareil Intune valide                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | Un ID d’objet Azure AD valide            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> Les règles d’appareil ne peuvent pas être créées à l’aide de la liste déroulante « règle simple » dans le portail Azure Classic.
>
>

## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>Changement de l’appartenance dynamique en appartenance statique et vice versa
Il est possible de modifier la façon dont l’appartenance est gérée dans un groupe. Cela est utile lorsque vous souhaitez conserver le même nom et le même ID de groupe dans le système, afin que toutes les références au groupe existantes soient toujours valides ; la création d’un groupe nécessiterait la mise à jour de ces références.

> [!WARNING]
> Lorsque vous changez un groupe statique existant en groupe dynamique, tous les membres existants sont supprimés du groupe, puis la règle d’appartenance est exécutée pour ajouter de nouveaux membres. Si le groupe est utilisé pour contrôler l’accès aux applications ou aux ressources, les membres d’origine peuvent perdre leur accès tant que la règle d’appartenance n’a pas été totalement exécutée.
>
> Il est recommandé de tester la nouvelle règle d’appartenance au préalable pour vous assurer que la nouvelle appartenance du groupe est conforme à votre attente.

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), ouvrez le groupe.
2. Sélectionnez l’onglet **Configurer** pour afficher l’état actuel de l’appartenance dynamique.
3. Pour rendre un groupe statique, il vous suffit de définir le paramètre **Activer les appartenances dynamiques** sur **NON**. Cliquez sur le bouton **Enregistrer** dans la barre d’outils ci-dessous pour confirmer. Les membres existants sont conservés dans le groupe, et dorénavant la règle d’appartenance n’est pas exécutée.
4. Pour rendre un groupe dynamique, définissez le paramètre sur **OUI**, spécifiez la règle d’appartenance souhaitée, puis cliquez sur **Enregistrer**. Les membres existants sont supprimés et la nouvelle règle commence à ajouter de nouveaux membres.

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Résolution des problèmes liés à l’appartenance dynamique à des groupes](active-directory-accessmanagement-troubleshooting.md)
* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
