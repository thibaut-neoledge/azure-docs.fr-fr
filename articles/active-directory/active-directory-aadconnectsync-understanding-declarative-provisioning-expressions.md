<properties
	pageTitle="Azure AD Connect Sync : présentation des expressions d’approvisionnement déclaratif | Microsoft Azure"
	description="Explique les expressions d’approvisionnement déclaratif."
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
	ms.date="08/29/2016"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync : présentation des expressions d’approvisionnement déclaratif
Azure AD Connect sync s’appuie sur l’approvisionnement déclaratif introduit pour la première fois dans Forefront Identity Manager 2010. Il vous permet d’implémenter toute votre logique métier d’intégration des identités sans avoir à écrire de code compilé.

Le langage d’expression utilisé dans les flux d’attributs constitue une partie essentielle de l’approvisionnement déclaratif. Le langage utilisé est un sous-ensemble de Microsoft® Visual Basic® pour Applications (VBA). Ce langage est utilisé dans Microsoft Office et les utilisateurs qui ont une certaine expérience de VBScript le reconnaîtront également. Le langage d’expression de l’approvisionnement déclaratif utilise uniquement des fonctions. Il ne s’agit pas d’un langage structuré. Il ne contient ni méthodes ni instructions. Les fonctions sont imbriquées pour exprimer le déroulement du programme.

Pour plus d’informations, consultez [Bienvenue dans la référence linguistique Visual Basic pour Applications pour Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Les attributs sont fortement typés. Une fonction accepte uniquement les attributs de type correct. Elle respecte également la casse. Les noms des fonctions et les noms des attributs doivent avoir une casse appropriée, sinon une erreur est levée.

## Définitions linguistiques et identificateurs

- Les fonctions ont un nom suivi d’arguments entre parenthèses : NomFonction (argument 1, argument N).
- Les attributs sont identifiés par des crochets : [NomAttribut]
- Les paramètres sont identifiés par des signes de pourcentage : %NomParamètre%
- Les constantes de chaîne sont placées entre guillemets : par exemple, "Contoso" (Remarque : vous devez utiliser des guillemets droits "" et non pas des guillemets courbes “”)
- Les valeurs numériques sont exprimées sans guillemets et les valeurs attendues sont décimales. Les valeurs hexadécimales sont précédées de &H. Par exemple, 98052, &HFF
- Les valeurs booléennes sont exprimées avec des constantes : True, False.
- Les constantes intégrées et les littéraux sont exprimés uniquement par leur nom : NULL, CRLF, IgnoreThisFlow

### Fonctions
L’approvisionnement déclaratif utilise de nombreuses fonctions pour permettre de transformer les valeurs d’attribut. Elles peuvent être imbriquées. Le résultat d’une fonction est alors transmis à une autre fonction.

`Function1(Function2(Function3()))`

Vous trouverez la liste complète des fonctions dans la [référence de fonction](active-directory-aadconnectsync-functions-reference.md).

### Paramètres
Un paramètre est défini par un connecteur ou par un administrateur à l’aide de PowerShell. Les paramètres contiennent généralement des valeurs qui diffèrent d’un système à un autre, par exemple le nom du domaine où se trouve l’utilisateur. Vous pouvez les utiliser dans des flux d’attributs.

Le connecteur Active Directory fournissait les paramètres suivants pour les règles de synchronisation entrantes :

| Nom du paramètre | Commentaire |
| --- | --- |
| Domain.Netbios | Format NetBIOS du domaine en cours d'importation, par exemple, FABRIKAMSALES |
| Domain.FQDN | Format FQDN du domaine en cours d’importation, par exemple, sales.fabrikam.com |
| Domain.LDAP | Format LDAP du domaine en cours d’importation, par exemple, DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios | Format NetBIOS du nom de forêt en cours d’importation, par exemple, FABRIKAMCORP |
| Forest.FQDN | Format FQDN du nom de forêt en cours d’importation, par exemple, fabrikam.com |
| Forest.LDAP | Format LDAP du nom de forêt en cours d’importation, par exemple, DC=fabrikam,DC=com |

Le système fournit le paramètre suivant, qui est utilisé pour obtenir l'identificateur du connecteur en cours d'exécution : `Connector.ID`

Voici un exemple qui remplira le domaine d’attribut du métaverse avec le nom netbios du domaine où se trouve l’utilisateur : `domain` <- `%Domain.Netbios%`

### Opérateurs
Vous pouvez utiliser les opérateurs suivants :

- **Comparaison** : <, < =, <>, =, >, > =
- **Mathématiques** : +, -, *, -
- **Chaîne** : & (concaténation)
- **Logiques** : && (et), || (ou)
- **Ordre d’évaluation** : ( )

Les opérateurs sont évalués de la gauche vers la droite et ont la même priorité d'évaluation. Par exemple, le * (signe multiplicateur) n’est pas évalué avant - (soustraction). 2*(5+3) n’est pas la même chose que 2*5+3. Les parenthèses ( ) sont utilisées pour modifier l’ordre d’évaluation lorsqu’un ordre d’évaluation de la gauche vers la droite n’est pas approprié.

## Attributs à valeurs multiples
Les fonctions peuvent fonctionner sur des attributs à valeur unique et à valeurs multiples. Pour les attributs à valeurs multiples, la fonction fonctionne sur toutes les valeurs et applique la même fonction à chaque valeur.

Par exemple, `Trim([proxyAddresses])` supprime les espaces de gauche à droite dans chaque valeur de l’attribut proxyAddress. `Word([proxyAddresses],1,"@") & "@contoso.com"` Pour chaque valeur comportant un signe @, remplacez le domaine par @contoso.com. `IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` Recherchez l’adresse SIP et supprimez-la des valeurs.

## Ressources supplémentaires

- [Azure AD Connect Sync : présentation de l’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
- [Azure AD Connect Sync : Référence aux fonctions](active-directory-aadconnectsync-functions-reference.md)
- [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0831_2016-->