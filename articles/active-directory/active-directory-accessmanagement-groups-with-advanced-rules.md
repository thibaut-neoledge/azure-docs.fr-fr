
<properties
	pageTitle="Utilisation d’attributs pour créer des règles avancées | Microsoft Azure"
	description="Procédure de création de règles avancées pour un groupe incluant des paramètres et des opérateurs de règle d’expression pris en charge."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/09/2015" 
	ms.author="femila"/>


# Utilisation d’attributs pour créer des règles avancées
Le portail de gestion Azure vous permet de définir une règle plus avancée pour activer l’appartenance dynamique à des groupes.

**Pour créer la règle avancée** Dans le portail de gestion Azure, sous l’onglet **Configurer** du groupe, activez la case d’option **Règle avancée**, puis tapez votre règle avancée dans la zone de texte fournie. Vous pouvez créer votre règle avancée à l’aide des informations suivantes.

## Construction du corps d’une règle avancée
La règle avancée que vous pouvez créer pour l’appartenance dynamique à des groupes est essentiellement une expression binaire qui se compose de trois parties et qui génère un résultat true ou false. Les trois parties sont les suivantes :

- Paramètre de gauche (leftParameter)
- Opérateur binaire (binaryOperator)
- Constante de droite (rightConstant) 

Une règle avancée complète ressemble à ceci : (leftParameter binaryOperator "rightConstant"). Elle nécessite des parenthèses ouvrantes et fermantes pour l’ensemble de l’expression binaire, des guillemets doubles pour la constante de droite et l’utilisation de la syntaxe user.property pour le paramètre de gauche. Une règle avancée peut se composer de plusieurs expressions binaires séparées par les opérateurs logiques -and, -or et -not. Voici des exemples de règles avancées correctement construites :

- (user.department -eq "Sales") -or (user.department -eq "Marketing") 
- (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE") 

Pour obtenir la liste complète des paramètres et des opérateurs de règle d’expression pris en charge, consultez les sections ci-dessous.

La longueur totale du corps de votre règle avancée ne peut pas dépasser 255 caractères.
> [AZURE.NOTE]Les opérations de chaîne et regex (expressions régulières) ne prennent pas en compte la casse. Vous pouvez également effectuer des vérifications de la valeur Null, en utilisant $null en tant que constante. Par exemple : user.department -eq $null. Les chaînes contenant des guillemets doubles doivent être placées dans une séquence d’échappement à l’aide du caractère « ' ». Par exemple : "Sa`"les".

##Opérateurs de règle d’expression pris en charge
Le tableau suivant répertorie tous les opérateurs de règle d’expression pris en charge et leur syntaxe à utiliser dans le corps de la règle avancée :

| Opérateur | Syntaxe |
|-----------------|----------------|
| Non égal à | -ne |
| Égal à | -eq |
| Ne commence pas par | -notStartsWith |
| Commence par | -startsWith |
| Ne contient pas | -notContains |
| Contient | -contains |
| Ne correspond pas | -notMatch |
| Correspond | -match |


| Erreur d’analyse de requête | Utilisation incorrecte | Utilisation corrigée |
|----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Erreur : attribut non pris en charge. | (user.invalidProperty -eq "Value") | (user.department -eq "value")La propriété doit correspondre à l’une de celles figurant dans la liste des propriétés prises en charge ci-dessus. |
| Erreur : l’opérateur n’est pas pris en charge sur l’attribut. | (user.accountEnabled -contains true) | (user.accountEnabled - eq true)La propriété est de type booléen. Utilisez les opérateurs pris en charge (-eq ou -ne) sur un type booléen dans la liste ci-dessus. |
| Erreur : erreur de compilation de la requête. | (user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") | (user.department -eq "Sales") -and (user.department -eq "Marketing")L’opérateur logique doit correspondre à l’un de ceux figurant dans la liste des propriétés prises en charge ci-dessus.(user.userPrincipalName -match ".*@domain.ext")or(user.userPrincipalName -match "@domain.ext$")Erreur dans l’expression régulière. |
| Erreur : l’expression binaire n’est pas au format correct. | (user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")La requête comporte plusieurs erreurs. Une parenthèse n’est pas au bon endroit. |
| Erreur : une erreur inconnue s’est produite lors de la configuration des appartenances dynamiques. | (user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") | (user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")La requête comporte plusieurs erreurs. Une parenthèse n’est pas au bon endroit. |

##Paramètres pris en charge
Voici toutes les propriétés d’utilisateur que vous pouvez utiliser dans vos règles avancées :

**Propriétés de type booléen**

Opérateurs autorisés

* -eq


* -ne


| Propriétés | Valeurs autorisées | Usage |
|----------------|-----------------|--------------------------------|
| accountEnabled | true false | user.accountEnabled -eq true) |
| dirSyncEnabled | true false null | (user.dirSyncEnabled -eq true) |

**Propriétés de type chaîne**

Opérateurs autorisés

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -contains


* -notContains


* -match


* -notMatch

| Propriétés | Valeurs autorisées | Usage |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| city | Toute valeur de chaîne ou $null. | (user.city -eq "value") |
| country | Toute valeur de chaîne ou $null. | (user.country -eq "value") |
| department | Toute valeur de chaîne ou $null. | (user.department -eq "value") |
| displayName | Toute valeur de chaîne. | (user.displayName -eq "value") |
| facsimileTelephoneNumber | Toute valeur de chaîne ou $null. | (user.facsimileTelephoneNumber -eq "value") |
| givenName | Toute valeur de chaîne ou $null. | (user.givenName -eq "value") |
| jobTitle | Toute valeur de chaîne ou $null. | (user.jobTitle -eq "value") |
| mail | Toute valeur de chaîne ou $null. Adresse SMTP de l’utilisateur. | (user.mail -eq "value") |
| mailNickName | Toute valeur de chaîne. Alias de messagerie de l’utilisateur. | (user.mailNickName -eq "value") |
| mobile | Toute valeur de chaîne ou $null. | (user.mobile -eq "value") |
| objectId | GUID de l’objet utilisateur | (user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| passwordPolicies | Aucune DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword | (user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName | Toute valeur de chaîne ou $null. | (user.physicalDeliveryOfficeName -eq "value") |
| postalCode | Toute valeur de chaîne ou $null. | (user.postalCode -eq "value") |
| preferredLanguage | Code ISO 639-1 | (user.preferredLanguage -eq "fr-FR") |
| sipProxyAddress | Toute valeur de chaîne ou $null. | (user.sipProxyAddress -eq "value") |
| state | Toute valeur de chaîne ou $null. | (user.state -eq "value") |
| streetAddress | Toute valeur de chaîne ou $null. | (user.streetAddress -eq "value") |
| surname | Toute valeur de chaîne ou $null. | (user.surname -eq "value") |
| telephoneNumber | Toute valeur de chaîne ou $null. | (user.telephoneNumber -eq "value") |
| usageLocation | Paramètre régional à deux lettres | (user.usageLocation -eq "US") |
| userPrincipalName | Toute valeur de chaîne. | (user.userPrincipalName -eq "alias@domain") |
| userType | member guest $null | (user.userType -eq "Member") |

**Propriétés de type collection de chaînes**

Opérateurs autorisés

* -contains


* -notContains

| Propriétés | Valeurs autorisées | Usage |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails | Toute valeur de chaîne. | (user.otherMails -contains "alias@domain") |
| proxyAddresses | SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -contains "SMTP: alias@domain") |

## Règle de collaborateurs
Vous pouvez maintenant remplir les membres d’un groupe en fonction de l’attribut de responsable hiérarchique d’un utilisateur.
Pour configurer un groupe en tant que groupe « Responsable »
--------------------------------------------------------------------------------
1. Dans le portail d’administration, cliquez sur l’onglet **Configurer** et sélectionnez **Règle avancée**. 
2. Tapez la règle avec la syntaxe suivante : Collaborateurs de *Collaborateurs de {ID\_utilisateur\_du\_responsable}*
3. Une fois cette règle enregistrée, tous les utilisateurs qui satisfont à la règle seront joints en tant que membres du groupe. Notez que le remplissage initial du groupe peut prendre quelques minutes. 


## Informations supplémentaires
Voici des rubriques qui fournissent des informations supplémentaires sur Azure Active Directory

* [Résolution des problèmes liés à l’appartenance dynamique à des groupes](active-directory-accessmanagement-troubleshooting.md)

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)

* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Oct15_HO3-->