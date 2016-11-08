
---
title: Utilisation d’attributs pour créer des règles avancées pour l’appartenance au groupe dans la version préliminaire d’Azure Active Directory | Microsoft Docs
description: Procédure de création de règles avancées pour une adhésion de groupe dynamique incluant des paramètres et des opérateurs de règle d’expression pris en charge.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: curtand

---
# Utilisation d’attributs pour créer des règles avancées pour l’appartenance au groupe dans la version préliminaire d’Azure Active Directory
Le portail Azure vous permet de créer des règles avancées pour activer des appartenances dynamiques plus complexes basées sur les attributs aux groupes de la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md) Cet article détaille les attributs de règle et la syntaxe pour créer ces règles avancées.

## Pour créer une règle avancée
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Utilisateurs et groupes** dans la zone de texte, puis sélectionnez **Entrée**.
   
   ![Ouvrir la gestion des utilisateurs](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)
3. Dans le panneau **Utilisateurs et groupes**, sélectionnez **Tous les groupes**.
   
   ![Ouvrir le panneau de groupes](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)
4. Dans le panneau **Utilisateurs et groupes - Tous les groupes**, sélectionnez la commande **Ajouter**.
   
   ![Add new group](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)
5. Dans le panneau **Groupe**, saisissez un nom et une description pour le nouveau groupe. Sélectionnez un **Type d’appartenance** entre **Utilisateur dynamique** et **Appareil dynamique**, selon que vous souhaitiez créer une règle pour des utilisateurs ou des périphériques, puis sélectionnez **Ajouter une requête dynamique**. Pour les attributs utilisés pour les règles d’appareil, consultez la page [Utilisation d’attributs pour créer des règles pour les objets d’appareil](#using-attributes-to-create-rules-for-device-objects).
   
   ![Ajouter une règle d’appartenance dynamique](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)
6. Dans le panneau **Règles d’appartenance dynamique**, saisissez votre règle dans la zone **Ajouter une règle d’appartenance dynamique avancée**, appuyez sur Entrée, puis sélectionnez **Créer** en bas du panneau.
7. Sélectionnez **Créer** dans le panneau **Groupe** panneau pour créer le groupe.

## Construction du corps d’une règle avancée
La règle avancée que vous pouvez créer pour l’appartenance dynamique à des groupes est essentiellement une expression binaire qui se compose de trois parties et qui génère un résultat true ou false. Les trois parties sont les suivantes :

* Paramètre de gauche (leftParameter)
* Opérateur binaire (binaryOperator)
* Constante de droite (rightConstant)

Une règle avancée complète ressemble à ceci : (leftParameter binaryOperator «RightConstant»). Elle nécessite des parenthèses ouvrantes et fermantes pour l’ensemble de l’expression binaire, des guillemets doubles pour la constante de droite et l’utilisation de la syntaxe user.property pour le paramètre de gauche. Une règle avancée peut se composer de plusieurs expressions binaires séparées par les opérateurs logiques -and, -or et -not.

Voici des exemples de règles avancées correctement construites :

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

Pour obtenir la liste complète des paramètres et des opérateurs de règle d’expression pris en charge, consultez les sections ci-dessous. Pour les attributs utilisés pour les règles d’appareil, consultez la page [Utilisation d’attributs pour créer des règles pour les objets d’appareil](#using-attributes-to-create-rules-for-device-objects).

La longueur totale du corps de votre règle avancée ne peut pas dépasser 2 048 caractères.

> [!NOTE]
> Les opérations de chaîne et regex (expressions régulières) ne prennent pas en compte la casse. Vous pouvez également effectuer des vérifications de la valeur Null, en utilisant $null en tant que constante. Par exemple : user.department -eq $null. Les chaînes contenant des guillemets doubles doivent être placées dans une séquence d’échappement à l’aide du caractère « ' ». Par exemple : `"Sales".
> 
> 

## Opérateurs de règle d’expression pris en charge
Le tableau suivant répertorie tous les opérateurs de règle d’expression pris en charge et leur syntaxe à utiliser dans le corps de la règle avancée :

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

## Correction d’erreur de requête
Le tableau suivant répertorie les erreurs potentielles et la méthode pour les corriger si elles se produisent

| Erreur d’analyse de requête | Utilisation incorrecte | Utilisation corrigée |
| --- | --- | --- |
| Erreur : attribut non pris en charge. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>La propriété doit correspondre à l’une de celles figurant dans la [liste des propriétés prises en charge](#supported-properties). |
| Erreur : l’opérateur n’est pas pris en charge sur l’attribut. |(user.accountEnabled -contains true) |(user.accountEnabled - eq true)<br/>La propriété est de type booléen. Utilisez les opérateurs pris en charge (-eq ou -ne) sur un type booléen dans la liste ci-dessus. |
| Erreur : erreur de compilation de la requête. |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>L’opérateur logique doit correspondre à l’un de ceux figurant dans la liste des propriétés prises en charge ci-dessus.(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Erreur dans l’expression régulière. |
| Erreur : l’expression binaire n’est pas au format correct. |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La requête comporte plusieurs erreurs. Une parenthèse n’est pas au bon endroit. |
| Erreur : une erreur inconnue s’est produite lors de la configuration des appartenances dynamiques. |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>La requête comporte plusieurs erreurs. Une parenthèse n’est pas au bon endroit. |

## Propriétés prises en charge
Voici toutes les propriétés d’utilisateur que vous pouvez utiliser dans vos règles avancées :

### Propriétés de type booléen
Opérateurs autorisés

* -eq
* -ne

| Propriétés | Valeurs autorisées | Usage |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true) |
| dirSyncEnabled |true false null |(user.dirSyncEnabled -eq true) |

### Propriétés de type chaîne
Opérateurs autorisés

* -eq
* -ne
* -notStartsWith
* -StartsWith
* -contains
* -notContains
* -match
* -notMatch

| Propriétés | Valeurs autorisées | Utilisation |
| --- | --- | --- |
| city |Toute valeur de chaîne ou $null |(user.city -eq "value") |
| country |Toute valeur de chaîne ou $null |(user.country -eq "value") |
| department |Toute valeur de chaîne ou $null |(user.department -eq "value") |
| displayName |Toute valeur de chaîne. |(user.displayName -eq "value") |
| facsimileTelephoneNumber |Toute valeur de chaîne ou $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Toute valeur de chaîne ou $null |(user.givenName -eq "value") |
| jobTitle |Toute valeur de chaîne ou $null |(user.jobTitle -eq "value") |
| mail |Toute valeur de chaîne ou $null (adresse SMTP de l’utilisateur) |(user.mail -eq "value") |
| mailNickName |Toute valeur de chaîne (alias de messagerie de l’utilisateur) |(user.mailNickName -eq "value") |
| mobile |Toute valeur de chaîne ou $null |(user.mobile -eq "value") |
| objectId |GUID de l’objet utilisateur |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies |Aucune DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Toute valeur de chaîne ou $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Toute valeur de chaîne ou $null |(user.postalCode -eq "value") |
| preferredLanguage |Code ISO 639-1 |(user.preferredLanguage -eq "fr-FR") |
| sipProxyAddress |Toute valeur de chaîne ou $null |(user.sipProxyAddress -eq "value") |
| state |Toute valeur de chaîne ou $null |(user.state -eq "value") |
| streetAddress |Toute valeur de chaîne ou $null |(user.streetAddress -eq "value") |
| surname |Toute valeur de chaîne ou $null |(user.surname -eq "value") |
| telephoneNumber |Toute valeur de chaîne ou $null |(user.telephoneNumber -eq "value") |
| usageLocation |Paramètre régional à deux lettres |(user.usageLocation -eq "US") |
| userPrincipalName |Toute valeur de chaîne. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(user.userType -eq "Member") |

### Propriétés de type collection de chaînes
Opérateurs autorisés

* -contains
* -notContains

| Propriétés | Valeurs autorisées | Utilisation |
| --- | --- | --- |
| otherMails |Toute valeur de chaîne. |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## Attributs d’extension et attributs personnalisés
Les attributs d’extension et les attributs personnalisés sont pris en charge dans les règles d’appartenance dynamique.

Les attributs d’extension sont synchronisés à partir d’un Windows Server AD et prennent le format « ExtensionAttributeX », où X est égal à 1-15. Voici en exemple de règle utilisant un attribut d’extension :

(user.extensionAttribute15 -eq "Marketing")

Les attributs personnalisés sont synchronisés à partir d’un système AD Windows Server local ou d’une application SaaS connectée et du format de « user.extension_[GUID]\__[Attribut] », où [GUID] est l’identificateur unique, dans AAD, de l’application qui a créé l’attribut dans AAD, et [Attribut] est le nom de l’attribut tel qu’il a été créé. Voici un exemple de règle utilisant un attribut personnalisé :

user.extension\_c272a57b722d4eb29bfe327874ae79cb\_\_OfficeNumber

Vous pouvez accéder au nom de l’attribut personnalisé dans le répertoire en lançant une requête sur un attribut d’utilisateur, à l’aide de l’explorateur graphique, et en recherchant le nom d’attribut.

## Règle de collaborateurs
Vous pouvez maintenant remplir les membres d’un groupe en fonction de l’attribut de responsable hiérarchique d’un utilisateur.

**Pour configurer un groupe en tant que groupe « Responsable »**

1. Suivez les étapes 1 à 5 [pour créer la règle avancée](#to-create-the-advanced-rule), puis sélectionnez le **type d’appartenance** **utilisateur dynamique**.
2. Dans le panneau **Règles d’appartenance dynamique**, saisissez la règle avec la syntaxe suivante :
   
    Rapports directs pour *Rapports directs pour {ID objet\_du\_responsable}*. Voici un exemple e règle valable pour Collaborateurs :
   
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”
   
    où « 62e19b97-8b3d-4d4a-a106-4ce66896a863 » est l’ID objet du responsable. L’ID d’objet se trouve dans Azure AD, dans l’**onglet Profil** de la page Utilisateur de l’utilisateur qui est responsable.
3. Une fois cette règle enregistrée, tous les utilisateurs qui satisfont à la règle seront joints en tant que membres du groupe. Le remplissage initial du groupe peut prendre quelques minutes.

## Utilisation d’attributs pour créer des règles pour les objets d’appareil
Vous pouvez également créer une règle qui sélectionne des objets d’appareil pour l’appartenance à un groupe. Les attributs d’appareil suivants peuvent être utilisés :

| Propriétés | Valeurs autorisées | Utilisation |
| --- | --- | --- |
| displayName |Toute valeur de chaîne |(device.displayName -eq "Rob Iphone”) |
| deviceOSType |Toute valeur de chaîne |(device.deviceOSType -eq "IOS") |
| deviceOSVersion |Toute valeur de chaîne |(device.OSVersion -eq "9.1") |
| isDirSynced |true false null |(device.isDirSynced -eq "true") |
| isManaged |true false null |(device.isManaged -eq "false") |
| isCompliant |true false null |(device.isCompliant -eq "true") |

## Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur les groupes dans Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)

<!---HONumber=AcomDC_0914_2016-->