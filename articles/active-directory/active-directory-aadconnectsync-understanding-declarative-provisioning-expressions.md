<properties
	pageTitle="Azure AD Connect Sync : présentation des expressions d’approvisionnement déclaratif"
	description="Explique les expressions d’approvisionnement déclaratif."
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


# Azure AD Connect Sync : présentation des expressions d’approvisionnement déclaratif

Le service Azure Active Directory Connect Synchronization (Azure AD Connect Sync) s’appuie sur l’approvisionnement déclaratif introduit dans Forefront Identity Manager 2010 pour vous permettre d’implémenter votre logique d’entreprise d’intégration des identités complète sans avoir à écrire de code.

Le langage d’expression utilisé dans les flux d’attributs constitue une partie essentielle de l’approvisionnement déclaratif. Le langage utilisé est un sous-ensemble de Microsoft® Visual Basic® pour Applications (VBA). Ce langage est utilisé dans Microsoft Office et les utilisateurs qui ont une certaine expérience de VBScript le reconnaîtront également. Le langage d’expression de l’approvisionnement déclaratif utilise uniquement des fonctions. Il ne s’agit pas d’un langage structuré et il ne contient ni méthodes ni instructions. Les fonctions sont imbriquées pour exprimer le déroulement du programme.

Pour plus d’informations, consultez [Bienvenue dans la référence linguistique Visual Basic pour Applications pour Office 2013] (https://msdn.microsoft.com/library/gg264383(v=office.15).aspx).

Les attributs sont fortement typés. Une fonction qui attend un attribut de chaîne à valeur unique n’accepte pas les attributs à valeurs multiples ou ceux d’un type différent. Elle respecte également la casse. Les noms des fonctions et les noms des attributs doivent avoir une casse appropriée, sinon une erreur est levée.





## Définitions linguistiques et identificateurs

- Les fonctions ont un nom suivi d’arguments entre parenthèses : NomFonction(<<argument 1>>,<<argument N>>).
- Les attributs sont identifiés par des crochets : [NomAttribut]
- Les paramètres sont identifiés par des signes de pourcentage : %NomParamètre%
- Les constantes de chaîne sont placées entre des guillemets doubles, par exemple : “Contoso”
- Les valeurs numériques sont exprimées sans guillemets et les valeurs attendues sont décimales. Les valeurs hexadécimales sont précédées de &H. Par exemple, 98052, &HFF
- Les valeurs booléennes sont exprimées avec des constantes : True, False.
- Les constantes intégrées sont exprimées uniquement par leur nom : NULL, CRLF, IgnoreThisFlow


## Opérateurs

Vous pouvez utiliser les opérateurs suivants :

- **Comparaison** : <, < =, <>, =, >, > =
- **Mathématiques** : +, -, *, - - **Chaîne** : & (concaténation)
- **Logiques** : && (et), || (ou)
- **Ordre d’évaluation** : ( )



Les opérateurs sont évalués de gauche à droite. 2\*(5+3) n’est pas la même chose que 2\*5+3.<br> Les parenthèses () servent à modifier l’ordre d’évaluation.





## Paramètres

Un paramètre est défini par un connecteur ou par un administrateur à l’aide de PowerShell. Les paramètres contiennent généralement des valeurs qui diffèrent d’un système à un autre, par exemple le nom du domaine où se trouve l’utilisateur. Vous pouvez les utiliser dans des flux d’attributs.

Le connecteur Active Directory fournissait les paramètres suivants pour les règles de synchronisation entrantes :

 
| Domain.Netbios | Domain.FQDN | Domain.LDAP | | Forest.Netbios | Forest.FQDN | Forest.LDAP |
 

Le système fournit le paramètre suivant :

Connector.ID

Voici un exemple qui remplit le domaine d’attribut du métaverse avec le nom netbios du domaine où se trouve l’utilisateur.

domain <- %Domain.Netbios%

## Scénarios courants

### Longueur des attributs

Les attributs de chaîne sont par défaut définis pour être indexables et la longueur maximale est de 448 caractères. Si vous travaillez avec des attributs de chaîne qui peuvent contenir davantage de caractères, assurez-vous d’inclure ce qui suit dans le flux d’attributs :

`attributeName <- Left([attributeName],448)`

### Modification de userPrincipalSuffix

L’attribut userPrincipalName dans Active Directory n’est pas toujours connu des utilisateurs et peut ne pas convenir comme ID de connexion. Le guide d’installation d’AAD Sync permet de choisir un autre attribut, par exemple mail, mais dans certains cas l’attribut doit être calculé. Par exemple, la société Contoso possède deux annuaires AAD, un pour la production et un pour les tests. Elle souhaite que les utilisateurs de son client test modifient simplement le suffixe dans l’ID de connexion userPrincipalName <- Word([userPrincipalName],1,"@") & "@contosotest.com"

Dans cette expression, nous prenons tout ce qui figure à gauche du premier signe @ (Word) et concaténons avec une chaîne fixe.





### Convertir un attribut à valeurs multiples en attribut à valeur unique

Certains attributs dans Active Directory sont à valeurs multiples dans le schéma, même s’ils semblent être à valeur unique dans Utilisateurs et ordinateurs Active Directory. L’attribut description constitue un exemple.

Dans cette expression, au cas où l’attribut a une valeur, nous prenons le premier élément (Item) dans l’attribut, nous supprimons les espaces à gauche et à droite (Trim), puis nous conservons les 448 premiers caractères (Left) dans la chaîne.



## Concept avancé

### NULL vs IgnoreThisFlow

Pour les règles de synchronisation entrantes, vous devez toujours utiliser la constante **NULL**. Cela indique que le flux n’a aucune valeur à contribuer et qu’une autre règle peut contribuer une valeur. Si aucune règle n’a contribué de valeur, l’attribut du métaverse est supprimé.

Pour les règles de synchronisation sortantes, il existe deux constantes différentes à utiliser : NULL et IgnoreThisFlow. Toutes deux indiquent que le flux d’attributs n’a rien à contribuer, mais la différence est ce qui se produit quand aucune autre règle n’a rien à contribuer non plus. Si l’annuaire connecté contient une valeur, une valeur NULL provoque une suppression de l’attribut, tandis qu’IgnoreThisFlow conserve la valeur existante.



#### ImportedValue

La fonction ImportedValue est différente de toutes les autres fonctions, car le nom d’attribut doit être placé entre guillemets doubles plutôt qu’entre crochets : ImportedValue(“proxyAddresses”).

Généralement lors de la synchronisation, un attribut utilise la valeur attendue, même s’il n’a pas encore été exporté ou si une erreur a été reçue pendant l’exportation (“top of the tower”). Une synchronisation entrante part du principe qu’un attribut qui n’a pas encore atteint un annuaire connecté finira par l’atteindre. Dans certains cas, il est important de ne synchroniser qu’une valeur qui a été confirmée par l’annuaire connecté, et dans ce cas la fonction ImportedValue est utilisée (“hologram and delta import tower”).

Un exemple se trouve dans la règle de synchronisation par défaut In from AD – User Common d’Exchange où, dans la version hybride d’Exchange, la valeur ajoutée par Exchange Online doit être synchronisée uniquement s’il a été confirmé que la valeur avait été exportée avec succès :


`proxyAddresses <- RemoveDuplicates(Trim(ImportedValues(“proxyAddresses”)))`

Pour obtenir une liste complète de fonctions, consultez [Azure AD Connect Sync : référence des fonctions](active-directory-aadconnectsync-functions-reference.md)


## Ressources supplémentaires

* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=July15_HO5-->