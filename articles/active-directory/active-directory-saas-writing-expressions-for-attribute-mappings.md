---
title: "Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory | Microsoft Docs"
description: "Découvrez comment utiliser les mappages d’expressions pour transformer des valeurs d’attributs dans un format acceptable lors de l’approvisionnement automatique des objets d’application SaaS dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: markvi
ms.openlocfilehash: c944a355c07b96c27dcdd477f625638284eabdf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Écriture d’expressions pour les mappages d’attributs dans Azure Active Directory
Quand vous configurez l’approvisionnement pour une application SaaS, l’un des types de mappages d’attributs que vous pouvez spécifier est un mappage d’expression. Dans ce cas, vous devez écrire une expression semblable à un script qui vous permet de transformer les données des utilisateurs dans des formats plus acceptables pour l’application SaaS.

## <a name="syntax-overview"></a>Vue d’ensemble de la syntaxe
La syntaxe des expressions pour les mappages d’attributs rappelle celle des fonctions Visual Basic pour Applications (VBA).

* L’expression entière doit être définie en termes de fonctions, qui sont constituées d’un nom suivi d’arguments entre parenthèses : <br>
  *NomDeFonction(&lt;&lt;argument 1&gt;&gt;,&lt;<argument N>&gt;)*
* Vous pouvez imbriquer des fonctions dans d’autres. Par exemple : <br> *FonctionUne(FonctionDeux(&lt;<argument1>&gt;))*
* Vous pouvez passer trois différents types d’arguments dans des fonctions :
  
  1. Des attributs, qui doivent être placés entre crochets. Par exemple : [nom_attribut]
  2. Des constantes de chaîne, qui doivent être placées entre des guillemets doubles. Par exemple : "États-Unis"
  3. D’autres fonctions. Par exemple : fonction_une (<<argument1>>, fonction_deux(<<argument2>>))
* Pour les constantes de chaîne, si vous avez besoin d’une barre oblique inverse (\) ou d’un guillemet (") dans la chaîne, vous devez le faire précéder du symbole de barre oblique inverse (\). Par exemple : « Nom de la société : \"Contoso\" »

## <a name="list-of-functions"></a>Liste des fonctions
[Append](#append)&nbsp;&nbsp;&nbsp;&nbsp;[FormatDateTime](#formatdatetime)&nbsp;&nbsp;&nbsp;&nbsp;[Join](#join)&nbsp;&nbsp;&nbsp;&nbsp;[Mid](#mid)&nbsp;&nbsp;&nbsp;&nbsp;[Not](#not)&nbsp;&nbsp;&nbsp;&nbsp;[Replace](#replace)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces)&nbsp;&nbsp;&nbsp;&nbsp;[Switch](#switch)

- - -
### <a name="append"></a>Append
**Fonction :**<br> Append(source, suffixe)

**Description :**<br> prend une valeur de chaîne source et ajoute le suffixe à la fin de celle-ci.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **source** |Requis |String |Généralement le nom de l’attribut de l’objet source |
| **suffix** |Requis |String |Chaîne que vous souhaitez ajouter à la fin de la valeur source. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**Fonction :**<br> FormatDateTime(source, inputFormat, outputFormat)

**Description :**<br> prend une chaîne de date dans un format et la convertit dans un autre format.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **source** |Requis |String |Généralement le nom de l’attribut de l’objet source. |
| **inputFormat** |Requis |String |Format attendu de la valeur source. Pour plus d’informations sur les formats pris en charge, consultez [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx). |
| **outputFormat** |Requis |String |Format de la date de sortie. |

- - -
### <a name="join"></a>Join
**Fonction :**<br> Join(séparateur, source1, source2, …)

**Description :**<br> Join() est similaire à Append(), mais elle peut combiner plusieurs valeurs de chaîne **sources** dans une même chaîne et chaque valeur sera séparée par une chaîne de **séparation**.

Si l’une des valeurs sources est un attribut à valeurs multiples, toutes les valeurs de cet attribut seront jointes, séparées par la valeur de séparation.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **separator** |Requis |String |Chaîne utilisée pour séparer les valeurs sources quand elles sont concaténées en une seule chaîne. Peut être "" si aucun séparateur n’est requis. |
| **source1  … sourceN ** |Requis, nombre de fois variable |String |Valeurs de chaîne à joindre ensemble. |

- - -
### <a name="mid"></a>Mid
**Fonction :**<br> Mid(source, début, longueur)

**Description :**<br> retourne une sous-chaîne de la valeur source. Une sous-chaîne est une chaîne qui ne contient que certains des caractères de la chaîne source.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **source** |Requis |String |Généralement le nom de l’attribut. |
| **start** |Requis |integer |Index dans la chaîne **source** où la sous-chaîne doit commencer. Le premier caractère dans la chaîne aura l’index 1, le deuxième caractère aura l’index 2, et ainsi de suite. |
| **length** |Requis |integer |Longueur de la sous-chaîne. Si la longueur se termine à l’extérieur de la chaîne **source**, la fonction retourne la sous-chaîne de l’index **start** jusqu’à la fin de l’index **source**. |

- - -
### <a name="not"></a>not
**Fonction :**<br> Not(source)

**Description :**<br> inverse la valeur booléenne de la **source**. Si la valeur **source** est « *True* », cette fonction retourne «*False* ». Sinon, elle retourne «*True*».

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **source** |Requis |Chaîne de type Boolean |Les valeurs **sources** attendues sont « True » ou « False ». |

- - -
### <a name="replace"></a>Replace
**Fonction :**<br> ObsoleteReplace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**Description :**<br>
Remplace les valeurs dans une chaîne. Elle fonctionne différemment selon les paramètres fournis :

* Quand **oldValue** et **replacementValue** sont fournis :
  
  * Remplace toutes les occurrences d’oldValue dans la source par  replacementValue.
* Quand **oldValue** et **template** sont fournis :
  
  * Remplace toutes les occurrences d’**oldValue** dans le **template** par la valeur **source**.
* Quand **oldValueRegexPattern**, **oldValueRegexGroupName** et **replacementValue** sont fournis :
  
  * Remplace toutes les valeurs correspondant à oldValueRegexPattern dans la chaîne source par replacementValue.
* Quand **oldValueRegexPattern**, **oldValueRegexGroupName** et **replacementPropertyName** sont fournis :
  
  * Si **source** a une valeur, **source** est retourné.
  * Si **source** n’a aucune valeur, la fonction utilise **oldValueRegexPattern** et **oldValueRegexGroupName** pour extraire la valeur de remplacement de la propriété avec **replacementPropertyName**. La valeur de remplacement est retournée comme résultat.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **source** |Requis |String |Généralement le nom de l’attribut de l’objet source. |
| **oldValue** |Facultatif |String |Valeur à remplacer dans **source** ou **template**. |
| **regexPattern** |Facultatif |String |Modèle d’expression régulière pour la valeur à remplacer dans **source**. Ou, quand replacementPropertyName est utilisé, modèle pour extraire la valeur de la propriété de remplacement. |
| **regexGroupName** |Facultatif |String |Nom du groupe à l’intérieur de **regexPattern**. Nous extrayons la valeur de ce groupe comme replacementValue à partir de la propriété de remplacement uniquement quand replacementPropertyName est utilisé. |
| **replacementValue** |Facultatif |String |Nouvelle valeur par laquelle remplacer l’ancienne. |
| **replacementAttributeName** |Facultatif |String |Nom de l’attribut à utiliser pour la valeur de remplacement, quand la source n’a aucune valeur. |
| **template** |Facultatif |String |Lorsque la valeur **template** est fournie, nous recherchons **oldValue** dans le modèle et la remplaçons par la valeur source. |

- - -
### <a name="stripspaces"></a>StripSpaces
**Fonction :**<br> StripSpaces(source)

**Description :**<br> supprime tous les caractères d’espacement (" ") de la chaîne source.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **source** |Requis |String |**source** à mettre à jour. |

- - -
### <a name="switch"></a>Switch
**Fonction :**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**Description :**<br> quand la valeur **source** correspond à une **clé**, retourne la **valeur** de cette **clé**. Si la valeur **source** ne correspond à aucune clé, retourne **defaultValue**.  Les paramètres **key** et **value** doivent toujours être fournis par paires. La fonction attend toujours un nombre pair de paramètres.

**Paramètres :**<br> 

| Nom | Requis / Répétition | Type | Remarques |
| --- | --- | --- | --- |
| **source** |Requis |String |**Source** à mettre à jour. |
| **defaultValue** |Facultatif |String |Valeur par défaut à utiliser quand la source ne correspond à aucune clé. Peut être une chaîne vide (""). |
| **key** |Requis |String |**Key** avec laquelle comparer la valeur **source**. |
| **value** |Requis |String |Valeur de remplacement pour la **source** correspondant à la clé. |

## <a name="examples"></a>Exemples
### <a name="strip-known-domain-name"></a>Supprimer un nom de domaine connu
Vous devez supprimer un nom de domaine connu de l’adresse de messagerie d’un utilisateur pour obtenir un nom d’utilisateur. <br>
Par exemple, si le domaine est « contoso.com », vous pouvez utiliser l’expression suivante :

**Expression :** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** (e-mail) : "john.doe@contoso.com"
* **SORTIE** : « john.doe »

### <a name="append-constant-suffix-to-user-name"></a>Ajouter un suffixe de constante à un nom d’utilisateur
Si vous utilisez un Sandbox Salesforce, vous devrez peut-être ajouter un suffixe supplémentaire à tous les noms d’utilisateurs avant de les synchroniser.

**Expression :** <br>
`Append([userPrincipalName], ".test"))`

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** : (userPrincipalName) : "John.Doe@contoso.com"
* **SORTIE**:  "John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>Générer des alias d’utilisateurs en concaténant des parties du prénom et du nom
Vous devez générer un alias d’utilisateur en prenant les trois premières lettres du prénom de l’utilisateur et les cinq premières lettres de son nom de famille.

**Expression :** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**Exemple d’entrée/sortie :** <br>

* **ENTRÉE** (givenName): « John »
* **ENTRÉE** (surname) : « Doe »
* **SORTIE** : « JohDoe »

### <a name="output-date-as-a-string-in-a-certain-format"></a>Sortir une date sous la forme d’une chaîne dans un certain format
Vous souhaitez envoyer des dates à une application SaaS dans un format donné. <br>
Par exemple, vous souhaitez mettre en forme des dates pour ServiceNow.

**Expression :** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (extensionAttribute1) : « 20150123105347.1Z »
* **SORTIE** : « 2015-01-23 »

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>Remplacer une valeur en fonction d’un ensemble d’options prédéfini
Vous devez définir le fuseau horaire de l’utilisateur en fonction du code d’état stocké dans Azure AD. <br>
Si le code d’état ne correspond à aucune des options prédéfinies, utilisez la valeur par défaut « Australia/Sydney ».

**Expression :** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**Exemple d’entrée/sortie :**

* **ENTRÉE** (state) : « QLD »
* **SORTIE**: « Australia/Brisbane »

## <a name="related-articles"></a>Articles connexes
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Automatiser l’approvisionnement/le déprovisionnement des utilisateurs pour les applications SaaS](active-directory-saas-app-provisioning.md)
* [Personnalisation des mappages d’attributs pour l’approvisionnement des utilisateurs](active-directory-saas-customizing-attribute-mappings.md)
* [Filtres d’étendue pour l’approvisionnement des utilisateurs](active-directory-saas-scoping-filters.md)
* [Utilisation de SCIM pour activer la configuration automatique des utilisateurs et des groupes d’Azure Active Directory sur des applications](active-directory-scim-provisioning.md)
* [Notifications d’approvisionnement de comptes](active-directory-saas-account-provisioning-notifications.md)
* [Liste des didacticiels sur l’intégration des applications SaaS](active-directory-saas-tutorial-list.md)

