<properties
	pageTitle="Azure AD Connect sync : Référence aux fonctions | Microsoft Azure"
	description="Référence d’expressions d’approvisionnement déclaratif dans Azure AD Connect Sync."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="markusvi"/>


# Azure AD Connect Sync : Référence aux fonctions


Dans Azure Active Directory Sync, les fonctions sont utilisées pour manipuler une valeur d’attribut lors de la synchronisation. <br>La syntaxe des fonctions s’exprime selon le format suivant :<br>`<output type> FunctionName(<input type> <position name>, ..)`

Si une fonction est surchargée et accepte plusieurs syntaxes, toutes les syntaxes valides sont répertoriées.<br> Les fonctions sont fortement typées et ils vérifient que le type passé correspond au type documenté.<br> Une erreur est levée si le type ne correspond pas.

Les types s’expriment avec la syntaxe suivante :

- **bin** : binaire
- **bool** : booléen
- **dt** : date/heure UTC
- **enum** : énumération des constantes connues
- **exp** : expression, qui est censée correspondre à une valeur booléenne
- **mvbin** : binaire à valeurs multiples
- **mvstr** : référence à valeurs multiples
- **num** : numérique
-  **ref** : référence à valeur unique
- **str** : chaîne à valeur unique
- **var** : variante de (quasiment) tout autre type
- **void** : ne retourne aucune valeur



## Référence des fonctions

----------
**Conversion :**

[CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [CDate](#cdate) &nbsp;&nbsp;&nbsp;&nbsp; [CGuid](#cguid) &nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromBase64](#convertfrombase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertFromUTF8Hex](#convertfromutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [CNum](#cnum) &nbsp;&nbsp;&nbsp;&nbsp; [CRef](#cref) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromGuid](#StringFromGuid) &nbsp;&nbsp;&nbsp;&nbsp; [StringFromSid](#stringfromsid)

**Date/Heure :**

[DateAdd](#dateadd) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Now](#now) &nbsp;&nbsp;&nbsp;&nbsp; [NumFromDate](#numfromdate)

**Répertoire**

[DNComponent](#dncomponent) &nbsp;&nbsp;&nbsp;&nbsp; [DNComponentRev](#dncomponentrev) &nbsp;&nbsp;&nbsp;&nbsp; [EscapeDNComponent](#escapedncomponent)

**Évaluation :**

[IsBitSet](#isbitset) &nbsp;&nbsp;&nbsp;&nbsp; [IsDate](#isdate) &nbsp;&nbsp;&nbsp;&nbsp; [IsEmpty](#isempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsGuid](#isguid) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsNumeric](#isnumeric) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring)

**Mathématiques :**

[BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [BitOr](#bitor) &nbsp;&nbsp;&nbsp;&nbsp; [RandomNum](#randomnum)

**Valeurs multiples**

[Contient](#contains) &nbsp;&nbsp;&nbsp;&nbsp; [Nombre](#count) &nbsp;&nbsp;&nbsp;&nbsp; [Élément](#item) &nbsp;&nbsp;&nbsp;&nbsp; [ItemOrNull](#itemornull) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)

**Flux de programme :**

[Erreur](#error) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp; [Basculer](#switch)


**Texte**

[GUID](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [InStrRev](#instrrev) &nbsp;&nbsp;&nbsp;&nbsp; [LCase](#lcase) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Len](#len) &nbsp;&nbsp;&nbsp;&nbsp; [LTrim](#ltrim) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [PadLeft](#padleft) &nbsp;&nbsp;&nbsp;&nbsp; [PadRight](#padright) &nbsp;&nbsp;&nbsp;&nbsp; [PCase](#pcase) &nbsp;&nbsp;&nbsp;&nbsp; [Remplacer](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [ReplaceChars](#replacechars) &nbsp;&nbsp;&nbsp;&nbsp; [Droite](#right) &nbsp;&nbsp;&nbsp;&nbsp; [RTrim](rtrim) &nbsp;&nbsp;&nbsp;&nbsp; [Découper](#trim) &nbsp;&nbsp;&nbsp;&nbsp; [UCase](#ucase) &nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

----------
### BitAnd

**Description :**<br> la fonction BitAnd définit des bits spécifiés sur une valeur.

**Syntaxe :**<br> `num BitAnd(num value1, num value2)`

- value1, value2 : valeurs numériques qui doivent être liées par AND

**Remarques :**<br> cette fonction convertit les deux paramètres de la représentation binaire et définit un bit sur :

- 0 - si un des bits, ou les deux bits correspondants dans *masque* et *indicateur* ont pour valeur 0
- 1 - si les deux bits correspondants sont définis sur 1.

En d’autres termes, elle renvoie 0 dans tous les cas, sauf si les bits correspondants de ces deux paramètres sont définis sur 1.

**Exemple :**<br>`BitAnd(&HF, &HF7)`<br> renvoie 7 car les valeurs « F » et « F7 » hexadécimal donne cette valeur.

----------
### BitOr

**Description :**<br> la fonction BitOr définit des bits spécifiés sur une valeur.

**Syntaxe :** <br> `num BitOr(num value1, num value2)`

- value1, value2 : valeurs numériques qui doivent être liées par OR

**Remarques :** <br> cette fonction convertit les deux paramètres de la représentation binaire et définit un bit sur 1 si l’un des bits ou les deux correspondants dans masque et indicateur ont pour valeur 1 et sur 0 si les deux bits correspondants ont la valeur 0. <br> En d’autres termes, elle renvoie 1 dans tous les cas, sauf si les bits correspondants de ces deux paramètres ont pour valeur 0.

----------
### CBool

**Description :**<br> la fonction CBool renvoie une valeur booléenne basée sur l’expression évaluée

**Syntaxe :** <br> `bool CBool(exp Expression)`

**Remarques :**<br> si l’expression retourne une valeur autre que zéro, CBool renvoie la valeur True, sinon il renvoie False.


**Exemple :**<br> `CBool([attrib1] = [attrib2])` <br>

Retourne True si les attributs ont la même valeur.




----------
### CDate

**Description :**<br> la fonction CDate renvoie une valeur DateTime UTC à partir d’une chaîne. DateTime n’est pas un type d’attribut natif dans Sync, mais il est utilisé par certaines fonctions.

**Syntaxe :**<br> `dt CDate(str value)`

- Valeur : chaîne comportant une date, une heure, et éventuellement, un fuseau horaire

**Remarques :**<br> la chaîne retournée est toujours au format UTC.

**Exemple :**<br>`CDate([employeeStartTime])`<br> renvoie une valeur DateTime à partir de l’heure de départ de l’employé

`CDate("2013-01-10 4:00 PM -8")` <br>Renvoie une valeur DateTime représentant « 2013-01-11 12:00 AM »




----------
### CGuid

**Description :**<br> la fonction CGuid convertit la représentation de chaîne de GUID à sa représentation binaire.

**Syntaxe :**<br> `bin CGuid(str GUID)GUID`

- Une chaîne rédigée au format suivant : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}




----------
### Contient

**Description :**<br> la fonction Contains détecte une chaîne à l’intérieur d’un attribut à valeurs multiples

**Syntaxe :**<br> `num Contains (mvstring attribute, str search)`- Respecter la casse<br> `num Contains (mvstring attribute, str search, enum Casetype)`<br> `num Contains (mvref attribute, str search)`- Respecter la casse

- attribut : attribut à valeurs multiples à rechercher.<br>
- recherche : chaîne à trouver dans l’attribut.<br>
- Casetype : CaseInsensitive ou CaseSensitive.<br>

Renvoie l’indice dans l’attribut à plusieurs valeurs où la chaîne a été trouvée. Si la chaîne est introuvable, la valeur renvoyée est 0.


**Remarques :**<br> pour les attributs de chaîne à plusieurs valeurs la recherche trouvera des sous-chaînes dans les valeurs.<br> Pour les attributs de référence, la chaîne recherchée doit correspondre exactement à la valeur pour être considérée comme une correspondance.

**Exemple :**<br> `IIF(Contains([proxyAddresses],”SMTP:”)>0,[proxyAddresses],Error(“No primary SMTP address found.”))`<br> si l’attribut proxyAddresses dispose d’une adresse de messagerie principale (indiquée par « SMTP : ») renvoie ensuite l’attribut proxyAddress, dans le cas contraire, renvoie une erreur.




----------
### ConvertFromBase64

**Description :**<br> la fonction ConvertFromBase64 convertit la valeur codée base64 Hex en chaîne régulière.

**Syntaxe :**<br> `str ConvertFromBase64(str source)`- suppose que le codage utilisé est Unicode<br> `str ConvertFromBase64(str source, enum Encoding)`

- source : chaîne encodée Base64<br>
- Codage : Unicode, ASCII, UTF8

**Exemple**<br> `ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`<br> `ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Les deux exemples renvoient « *Hello world!* »




----------
### ConvertFromUTF8Hex

**Description :**<br> La fonction ConvertFromUTF8Hex convertit la valeur codée hexadécimale UTF8 spécifiée en chaîne.

**Syntaxe :**<br> `str ConvertFromUTF8Hex(str source)`

- source : chaîne encodée sur 2 octets UTF8

**Remarques :**<br> La différence entre cette fonction et ConvertFromBase64(,UTF8) est que le résultat est convivial pour l’attribut DN.<br> Ce format est utilisé par Azure Active Directory en tant que nom de domaine.

**Exemple :**<br>`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`<br> Renvoie « *Hello world!* »




----------
### ConvertToBase64

**Description :** <br> La fonction ConvertToBase64 convertit une chaîne en chaîne Unicode base64.<br> Convertit la valeur d’un tableau d’entiers en sa représentation sous forme de chaîne équivalente encodée avec des chiffres en base 64.

**Syntaxe :** <br> `str ConvertToBase64(str source)`

**Exemple :**<br>`ConvertToBase64("Hello world!")`<br> Renvoie « SABlAGwAbABvACAAdwBvAHIAbABkACEA »




----------
### ConvertToUTF8Hex

**Description :**<br> La fonction ConvertToUTF8Hex convertit une chaîne en valeur hexadécimale encodée UTF8.

**Syntaxe :**<br> `str ConvertToUTF8Hex(str source)`

**Remarques :**<br> Le format de sortie de cette fonction est utilisé par Azure Active Directory en tant que format d’attribut de nom de domaine.

**Exemple :** <br> `ConvertToUTF8Hex("Hello world!")` <br> Renvoie 48656C6C6F20776F726C6421




----------
### Nombre

**Description :**<br> La fonction Nombre renvoie le nombre d’éléments dans un attribut à valeurs multiples

**Syntaxe :** <br> `num Count(mvstr attribute)`




----------
### CNum

**Description :**<br> La fonction CNum prend une chaîne et renvoie un type de données numérique.

**Syntaxe :** <br> `num CNum(str value)`




----------
### CRef

**Description :** <br> Convertit une chaîne en attribut de référence

**Syntaxe :** <br> `ref CRef(str value)`

**Exemple :** <br> `CRef(“CN=LC Services,CN=Microsoft,CN=lcspool01, CN=Pools,CN=RTC Service,” & %Forest.LDAP%)`




----------
### CStr

**Description :**<br> la fonction CStr est convertie en un type de données de chaîne.

**Syntaxe :** <br> `str CStr(num value)` <br> `str CStr(ref value)` <br> `str CStr(bool value)` <br>

- valeur : peut être une valeur numérique, un attribut de référence ou une valeur booléenne.

**Exemple :**<br>`CStr([dn]) <br>` Peut renvoyer « cn=Joe,dc=contoso,dc=com »




----------
### DateAdd

**Description :** <br> Renvoie une date contenant une date à laquelle un intervalle de temps spécifié a été ajouté.

**Syntaxe :** <br> `dt DateAdd(str interval, num value, dt date)`

- intervalle : expression de chaîne correspondant l’intervalle de temps que vous souhaitez ajouter. La chaîne doit avoir une des valeurs suivantes :
 - yyyy Année
 - q Trimestre
 - m Mois
 - y Jour de l’année
 - s Jour
 - w Jour de la semaine
 - ww Semaine
 - h Heure
 - n Minute
 - s Seconde
- valeur : nombre d’unités que vous souhaitez ajouter. Elle peut être positive (pour obtenir des dates dans le futur) ou négative (pour obtenir des dates dans le passé).
- date : DateTime représentant la date à laquelle l’intervalle est ajouté.

**Exemple :** <br> `DateAdd("m", 3, CDate("2001-01-01"))` <br> ajoute 3 mois et retourne une valeur DateTime représentant « 2001-04-01 »




----------
### DateFromNum

**Description :** <br> La fonction DateFromNum convertit une valeur au format de date AD en un type DateTime.

**Syntaxe :** <br> `dt DateFromNum(num value)`

**Exemple :** <br> `DateFromNum([lastLogonTimestamp])` <br> `DateFromNum(129699324000000000)` <br> Renvoie une valeur DateTime représentant 2012-01-01 23:00:00




----------
### DNComponent

**Description :**<br> La fonction DNComponent renvoie la valeur d’un composant de nom de domaine spécifié en partant de la gauche.

**Syntaxe :** <br> `str DNComponent(ref dn, num ComponentNumber)`

- dn : attribut de référence à interpréter
- ComponentNumber : composant du nom de domaine à renvoyer

**Exemple :** <br> `DNComponent([dn],1)` <br> Si DN est « cn=Joe,ou=… », il renvoie Joe




----------
### DNComponentRev

**Description :**<br> La fonction DNComponentRev renvoie la valeur d’un composant de nom de domaine spécifié en partant de la droite (fin).

**Syntaxe :** <br> `str DNComponentRev(ref dn, num ComponentNumber)` <br> `str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

- dn : attribut de référence à interpréter
- ComponentNumber - composant du nom de domaine à retourner
- Options : Le contrôleur de domaine – ignorer tous les composants avec « dc = »

**Exemple :** <br> `If dn is “cn=Joe,ou=Atlanta,ou=GA,ou=US, dc=contoso,dc=com” then DNComponentRev([dn],3)` <br> `DNComponentRev([dn],1,”DC”)` <br> Les deux renvoient US.




----------
### Erreur

**Description :** <br> La fonction Erreur est utilisée pour renvoyer une erreur personnalisée.

**Syntaxe :** <br> `void Error(str ErrorMessage)`

**Exemple :** <br> `IIF(IsPresent([accountName]),[accountName],Error(“AccountName is required”))` <br> Si l’attribut accountName n’est pas présent, lever une erreur sur l’objet.




----------
### EscapeDNComponent

**Description :**<br> La fonction EscapeDNComponent prend un seul composant de nom de domaine et le fait échapper de façon à le représenter dans l’annuaire LDAP.

**Syntaxe :** <br> `str EscapeDNComponent(str value)`

**Exemple :** <br> `EscapeDNComponent(“cn=” & [displayName]) & “,” & %ForestLDAP%` <br> Permet de s’assurer que l’objet peut être créé dans un annuaire LDAP, même si l’attribut displayName comporte des caractères d’échappement dans LDAP.




----------
### FormatDateTime

**Description :** <br> La fonction FormatDateTime est utilisée pour mettre en forme une valeur DateTime en chaîne dans le format spécifié

**Syntaxe :** <br> `str FormatDateTime(dt value, str format)`

- valeur : valeur au format DateTime<br>
- format : chaîne représentant le format à convertir.

**Remarques :** <br> Vous trouverez les valeurs de format possibles ici : [Formats de date/heure définis par l’utilisateur (fonction Format)](http://msdn2.microsoft.com/library/73ctwf33(VS.90).aspx)

**Exemple :** <br>

`FormatDateTime(CDate(“12/25/2007”),”yyyy-mm-dd”)`<br> Donne comme résultat « 2007-12-25 ».

`FormatDateTime(DateFromNum([pwdLastSet]),”yyyyMMddHHmmss.0Z”)` <br> Peut entraîner une « 20140905081453.0Z »




----------
### GUID

**Description :**<br> La fonction GUID génère un nouveau GUID aléatoire

**Syntaxe :** <br> `str GUID()`




----------
### IIF

**Description :** <br> La fonction IIF renvoie une valeur parmi un ensemble de valeurs possibles en fonction d’une condition spécifiée.

**Syntaxe :** <br> `var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

- condition : toute valeur ou expression qui peut être évaluée à true ou false.
- valueIfTrue : valeur renvoyée si la condition prend la valeur True.
- valueIfFalse : une valeur qui est renvoyée si la condition prend la valeur False.

**Exemple :** <br> `IIF([employeeType]=“Intern”,”t-“&[alias],[alias])` <br> Renvoie l’alias d’un utilisateur avec le suffixe « t- » ajouté au début de celui-ci si l’utilisateur est un stagiaire. Sinon, l’alias reste inchangé.




----------
### InStr

**Description :**<br> La fonction InStr recherche la première occurrence d’une sous-chaîne dans une chaîne

**Syntaxe :** <br>

`num InStr(str stringcheck, str stringmatch)` <br> `num InStr(str stringcheck, str stringmatch, num start)` <br> `num InStr(str stringcheck, str stringmatch, num start , enum compare)`

- stringcheck : chaîne à rechercher <br>
- stringmatch : chaîne à trouver <br>
- start : position de départ pour trouver la sous-chaîne <br>
- compare : vbTextCompare ou vbBinaryCompare

**Remarques :** <br> Renvoie la position à laquelle la sous-chaîne a été trouvée ou 0 si elle est introuvable.

**Exemple :** <br> `InStr("The quick brown fox","quick")` <br> Prend la valeur 5

`InStr("repEated","e",3,vbBinaryCompare)` <br> Prend la valeur 7




----------
### InStrRev

**Description :**<br> La fonction InStrRev recherche la dernière occurrence d’une sous-chaîne dans une chaîne

**Syntax :** <br> `num InstrRev(str stringcheck, str stringmatch)` <br> `num InstrRev(str stringcheck, str stringmatch, num start)` <br> `num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

- stringcheck : chaîne à rechercher <br>
- stringmatch : chaîne à trouver <br>
- start : position de départ pour trouver la sous-chaîne <br>
- compare : vbTextCompare ou vbBinaryCompare

**Remarques :** <br> Renvoie la position à laquelle la sous-chaîne a été trouvée ou 0 si elle est introuvable.

**Exemple :** <br> `InStrRev("abbcdbbbef","bb")` <br> Renvoie 7.




----------
### IsBitSet

**Description :** <br> La fonction IsBitSet vérifie si un bit est défini ou non

**Syntaxe :** <br> `bool IsBitSet(num value, num flag)`

- value : valeur numérique évaluée. flag : valeur numérique contenant le bit à évaluer

**Exemple :** <br> `IsBitSet(&HF,4)`<br> Renvoie True, car le bit « 4 » est défini dans la valeur hexadécimale « F »




----------
### IsDate

**Description :** <br> La fonction IsDate prend la valeur True si l’expression peut être évaluée à un type DateTime.

**Syntaxe :** <br> `bool IsDate(var Expression)`

**Remarques :** <br> Permet de déterminer si CDate() a réussi.




----------
###IsEmpty

**Description : ** <br> La fonction IsEmpty prend la valeur True si l’attribut est présent dans CS ou MV, mais est évaluée à une chaîne vide.

**Syntaxe :** <br> `bool IsEmpty(var Expression)`




----------
###IsGuid

**Description :** <br> la fonction IsGuid renvoie true si la chaîne peut être convertie en GUID.

**Syntaxe :** <br> `bool IsGuid(str GUID)`

**Remarques :**<br> Un GUID est défini en tant que chaîne selon un de ces modèles : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ou {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}

Utilisé pour déterminer si CGuid() sera réussi.

**Exemple :** <br> `IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)` <br> Si StrAttribute est au format GUID, renvoie une représentation binaire. Sinon, renvoie la valeur Null.




----------
###IsNull

**Description :** <br> La fonction IsNull renvoie true si l’expression correspond à la valeur Null.

**Syntaxe :** <br> `bool IsNull(var Expression)`

**Remarques :**<br> Dans le cas d’un attribut, une valeur Null est exprimée par l’absence de ce dernier.

**Exemple :** <br> `IsNull([displayName])` <br> Renvoie True si l’attribut n’est pas présent dans SC ou MV.




----------
###IsNullOrEmpty

**Description :** <br> La fonction IsNullOrEmpty renvoie la valeur true si l’expression est null ou s’il s’agit d’une chaîne vide.

**Syntaxe :** <br> `bool IsNullOrEmpty(var Expression)`

**Remarques :**<br> Dans le cas d’un attribut, cela donne la valeur True si l’attribut est absent ou est présent mais qu’il s’agit d’une chaîne vide.<br> L’inverse de cette fonction est nommé IsPresent.

**Exemple :** <br> `IsNull([displayName])` <br> Renvoie True si l’attribut n’est pas présent dans SC ou MV ou s’il s’agit d’une chaîne vide.




----------
### IsNumeric

**Description :** <br> La fonction IsNumeric renvoie une valeur booléenne indiquant si une expression peut être évalue en tant que type de nombre.

**Syntaxe :** <br> `bool IsNumeric(var Expression)`

**Remarques :**<br> Permet de déterminer si CNum() réussira à analyser l’expression.




----------
### IsString

**Description :**<br> La fonction IsString prend la valeur True si l’expression peut être évaluée en tant que type de chaîne.

**Syntaxe :** <br> `bool IsString(var expression)`

**Remarques :** <br> Permet de déterminer si CStr() réussira à analyser l’expression.




----------
### IsPresent

**Description :**<br> La fonction IsPresent renvoie true si l’expression correspond à une chaîne qui n’a pas la valeur Null et n’est pas vide.

**Syntaxe :** <br> `bool IsPresent(var expression)`

**Remarques :** <br> L’inverse de cette fonction est nommé IsNullOrEmpty.

**Exemple :** <br>

`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`




----------
### Item

**Description :**<br> La fonction Élément renvoie un élément à partir d’une chaîne/d’un attribut à valeurs multiples.

**Syntaxe :** <br> `var Item(mvstr attribute, num index)`

- attribute : attribut à valeurs multiples <br>
- index : index vers un élément dans la chaîne à valeurs multiples.

**Remarques :** <br> La fonction Item est utile avec la fonction Contains, car cette dernière renvoie l’index à un élément de l’attribut à valeurs multiples.

Génère une erreur si l’index est hors limites.

**Exemple :** <br> `Mid(Item([proxyAddress],Contains([proxyAddress], ”SMTP:”)),6)` <br> Renvoie l’adresse de messagerie principale.




----------
### ItemOrNull

**Description :**<br> La fonction ItemOrNull renvoie un élément à partir d’une chaîne/d’un attribut à valeurs multiples.

**Syntaxe :** <br> `var ItemOrNull(mvstr attribute, num index)`

- attribute : attribut à valeurs multiples <br>
- index : index vers un élément dans la chaîne à valeurs multiples.

**Remarques :** <br> La fonction ItemOrNull est utile avec la fonction Contains, car cette dernière renvoie l’index à un élément de l’attribut à valeurs multiples.

Retourne une valeur Null si l’index est hors limites.




----------
### Join

**Description :** <br> La fonction Join prend une chaîne à valeurs multiples et retourne une chaîne à valeur unique avec le séparateur spécifié inséré entre chaque élément.

**Syntaxe :** <br> `str Join(mvstr attribute)` <br> `str Join(mvstr attribute, str Delimiter)`

- attribute : attribut à valeurs multiples contenant des chaînes à joindre. <br>
- délimiter : toute chaîne utilisée pour séparer les sous-chaînes dans la chaîne renvoyée. En cas d’omission, le caractère espace (" ") est utilisé. Si le délimiteur est une chaîne de longueur nulle ("") ou Nothing, tous les éléments de la liste sont concaténés sans délimiteurs.

**Remarques**<br> Il existe une parité entre les fonctions Join et Split. La fonction Join prend un tableau de chaînes et les joint à l’aide d’une chaîne de délimiteur, pour renvoyer une chaîne unique. La fonction Split accepte une chaîne et la sépare au niveau du délimiteur, pour renvoyer un tableau de chaînes. Toutefois, la principale différence est que Join peut concaténer des chaînes avec n’importe quelle chaîne de délimiteur, Split peut uniquement séparer des chaînes à l’aide d’un délimiteur de caractère unique.

**Exemple :** <br> `Join([proxyAddresses],”,”)` <br> Peut renvoyer “SMTP :john.doe@contoso.com,smtp:jd@contoso.com”




----------
### LCase

**Description :** <br> La fonction LCase convertit tous les caractères d’une chaîne en minuscules.

**Syntaxe :** <br> `str LCase(str value)`

**Exemple :** <br> `LCase(“TeSt”)` <br> Renvoie “test”.




----------
### Left

**Description :**<br> La fonction Left renvoie un nombre spécifié de caractères en partant de la gauche d’une chaîne.

**Syntaxe :** <br> `str Left(str string, num NumChars)`

- string : chaîne à partir de laquelle les caractères sont renvoyés <br>
- NumChars : nombre identifiant le nombre de caractères à retourner du début (à gauche) de la chaîne

**Remarques :** <br> Chaîne contenant les numChars premiers caractères de la chaîne :

- Si numChars = 0, retourne une chaîne vide.
- Si numChars < 0, retourne une chaîne d’entrée.
- Si string a la valeur null, retourne une chaîne vide.

Si la chaîne contient moins de caractères que le nombre spécifié dans numChars, une chaîne identique à la chaîne (c’est-à-dire, contenant tous les caractères du paramètre 1) est renvoyée.

**Exemple :** <br> `Left(“John Doe”, 3)` <br> renvoie « Joh ».




----------
### Len

**Description :**<br> La fonction Len renvoie le nombre de caractères contenus dans une chaîne.

**Syntaxe :** <br> `num Len(str value)`

**Exemple :** <br> `Len(“John Doe”)` <br> Renvoie 8.




----------
### LTrim

**Description :** <br> La fonction LTrim supprime les espaces blancs situés au début d’une chaîne.

**Syntaxe :** <br> `str LTrim(str value)`

**Exemple :** <br> `LTrim(“ Test ”)` <br> renvoie « Test ».




----------
### Mid

**Description :** <br> La fonction Mid renvoie un nombre spécifié de caractères à partir d’une position spécifiée dans une chaîne.

**Syntaxe :** <br> `str Mid(str string, num start, num NumChars)`

- string : chaîne à partir de laquelle renvoyer des caractères <br>
- start : nombre identifiant la position de départ dans la chaîne à partir de laquelle les caractères sont renvoyés
- NumChars : nombre identifiant le nombre de caractères à retourner à partir de la position dans la chaîne


**Remarques :** <br> Renvoie numChars caractères à partir de la position de départ dans la chaîne.<br> Chaîne contenant numChars caractères à partir de la position de départ dans la chaîne :

- Si numChars = 0, retourne une chaîne vide.
- Si numChars < 0, retourne une chaîne d’entrée.
- Si start > la longueur de la chaîne, retourne une chaîne d’entrée.
- Si start < = 0, retourne une chaîne d’entrée.
- Si la chaîne est null, retourne une chaîne vide.

S’il ne reste pas numChars caractères dans la chaîne à partir de la position de départ, autant de caractères que possible sont renvoyés.

**Exemple :** <br>

`Mid(“John Doe”, 3, 5)` <br> Renvoie « hn Do ».

`Mid(“John Doe”, 6, 999)` <br> Renvoie « Doe ».




----------
### Now

**Description :**<br> La fonction Now renvoie une valeur DateTime indiquant la date et l’heure actuelles, qui correspondent à la date et à l’heure système de votre ordinateur.

**Syntaxe :** <br> `dt Now()`




----------
### NumFromDate

**Description :**<br> la fonction NumFromDate renvoie une date au format de date AD.

**Syntaxe :** <br> `num NumFromDate(dt value)`


**Exemple :** <br> `NumFromDate(CDate("2012-01-01 23:00:00"))` <br> Renvoie 129699324000000000.




----------
### PadLeft

**Description :** <br> La fonction PadLeft remplit par la gauche une chaîne sur une longueur spécifiée à l’aide d’un caractère de remplissage fourni.

**Syntaxe :** <br> `str PadLeft(str string, num length, str padCharacter)`

- string : chaîne à remplir. <br>
- length : entier représentant la longueur de chaîne souhaitée. <br>
- padCharacter : chaîne constituée d’un seul caractère à utiliser comme caractère de remplissage



----------
### Remarques

- Si la longueur de chaîne est inférieure à la longueur length, padCharacter est ajouté à plusieurs reprises au début (à gauche) de la chaîne jusqu’à ce qu’à atteindre la longueur length.
- PadCharacter peut être un caractère d’espacement, mais il ne peut pas s’agir de la valeur null.
- Si la longueur de chaîne est égale ou supérieure à la longueur length, la chaîne est renvoyée inchangée.
- Si la longueur de la chaîne est supérieure ou égale à la longueur length, une chaîne identique est renvoyée.
- Si la longueur de chaîne est inférieure à la longueur length, une nouvelle chaîne de longueur souhaitée est retournée, et contient une chaîne remplie avec un padCharacter.
- Si la chaîne est null, la fonction retourne une chaîne vide.

**Exemple :** <br> `PadLeft(“User”, 10, “0”)` <br> Renvoie « 000000User ».




----------
### PadRight

**Description :** <br> La fonction PadRight remplit par la droite une chaîne sur une longueur spécifiée à l’aide d’un caractère de remplissage fourni.

**Syntaxe :** <br> `str PadRight(str string, num length, str padCharacter)`

- string : chaîne à remplir.
- length : entier représentant la longueur de chaîne souhaitée.
- padCharacter : chaîne constituée d’un seul caractère à utiliser comme caractère de remplissage

**Remarques :**

- Si la longueur de chaîne est inférieure à la longueur length, padCharacter est ajouté à plusieurs reprises à la fin (à droite) de la chaîne jusqu’à ce qu’à atteindre la longueur length.
- PadCharacter peut être un caractère d’espacement, mais il ne peut pas s’agir d’une valeur null.
- Si la longueur de chaîne est égale ou supérieure à la longueur length, la chaîne est renvoyée inchangée.
- Si la longueur de la chaîne est supérieure ou égale à la longueur length, une chaîne identique est renvoyée.
- Si la longueur de chaîne est inférieure à la longueur length, une nouvelle chaîne de longueur souhaitée est retournée, et contient une chaîne remplie avec un padCharacter.
- Si la chaîne est null, la fonction retourne une chaîne vide.


**Exemple :**<br>`PadRight(“User”, 10, “0”)`<br> Renvoie « User000000 ».




----------
### PCase

**Description :** <br> La fonction PCase met en majuscule le premier caractère de chaque mot délimité par un espace dans une chaîne et tous les autres caractères sont convertis en minuscules.

**Syntaxe :** <br> `String PCase(string)`

**Exemple :** <br> `PCase(“TEsT”)` <br> renvoie « Test ».




----------
### RandomNum

**Description :**<br> La fonction RandomNum renvoie un nombre aléatoire dans un intervalle spécifié.

**Syntaxe :** <br> `num RandomNum(num start, num end)`

- start : nombre identifiant la limite inférieure de la valeur aléatoire à générer <br>
- end : nombre identifiant la limite supérieure de la valeur aléatoire à générer

**Exemple :** <br> `Random(100,999)` <br> Renvoie 734.




----------
### RemoveDuplicates

**Description :** <br> La fonction RemoveDuplicates prend une chaîne à valeurs multiples et vérifie que chaque valeur est unique.

**Syntaxe :** <br> `mvstr RemoveDuplicates(mvstr attribute)`

**Exemple :** <br> `RemoveDuplicates([proxyAddresses])` <br> Renvoie un attribut proxyAddress expurgé où toutes les valeurs en double ont été supprimées.




----------
### Replace

**Description :** <br> La fonction Replace remplace toutes les occurrences d’une chaîne par une autre chaîne.

**Syntaxe :** <br> `str Replace(str string, str OldValue, str NewValue)`

- string : chaîne dans laquelle les caractères sont remplacés. <br>
- OldValue : chaîne à rechercher et remplacer. <br>
- NewValue : chaîne de remplacement.


**Remarques :** <br> La fonction reconnaît les monikers spéciaux suivants :

- \\n – Nouvelle ligne
- \\r – Retour chariot
- \\t – Tabulation


**Exemple :** <br>

`Replace([address],”\r\n”,”, “)` <br> Remplace CRLF par une virgule et un espace et peut générer « One Microsoft Way, Redmond, WA, USA »




----------
### ReplaceChars

**Description :** <br> La fonction ReplaceChars remplace toutes les occurrences des caractères trouvés dans la chaîne ReplacePattern.

**Syntaxe :** <br> `str ReplaceChars(str string, str ReplacePattern)`

- string : chaîne dans laquelle les caractères sont remplacés.
- ReplacePattern : chaîne contenant un dictionnaire avec des caractères à remplacer.

Le format est {source1}: {target1}, {source2}: {target2}, {sourceN}, {targetN}, source étant le caractère à rechercher et la cible, la chaîne à remplacer.


**Remarques :**

- La fonction prend chaque occurrence de sources définies et la remplace par les cibles.
- La source doit être exactement un caractère (unicode).
- La source ne peut pas être vide ou dépasser un caractère (erreur d’analyse).
- La cible peut comporter plusieurs caractères, par exemple, ö:oe, β:ss.
- La cible peut être vide, indiquant que le caractère doit être supprimé.
- La source respecte la casse et il doit s’agir d’une correspondance exacte.
- La , (Virgule) et : (deux-points) sont des caractères réservés et ne peuvent pas être remplacés avec cette fonction.
- Les espaces et autres caractères blancs dans la chaîne ReplacePattern sont ignorés.


**Exemple :** <br> '%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o'

`ReplaceChars(”Räksmörgås”,%ReplaceString%)` <br> Renvoie Raksmorgas

`ReplaceChars(“O’Neil”,%ReplaceString%)` <br> Renvoie « ONeil », la coche simple est définie comme étant à supprimer.




----------
### Right

**Description :**<br> La fonction Right renvoie un nombre spécifié de caractères en partant de la droite (fin) d’une chaîne.

**Syntaxe :** <br> `str Right(str string, num NumChars)`

- string : chaîne à partir de laquelle les caractères sont renvoyés
- numChars : nombre identifiant le nombre de caractères à retourner à partir de la fin (à droite) de la chaîne

**Remarques :** <br> numChars caractères sont renvoyés à partir de la dernière position de la chaîne.

Chaîne contenant les numChars derniers caractères de la chaîne :

- Si numChars = 0, retourne une chaîne vide.
- Si numChars < 0, retourne une chaîne d’entrée.
- Si la chaîne est null, retourne une chaîne vide.

Si la chaîne contient un nombre de caractères inférieur au nombre spécifié dans numChars, une chaîne identique est renvoyée.

**Exemple :** <br> `Right(“John Doe”, 3)` <br> Renvoie « Doe ».




----------
### RTrim

**Description :** <br> La fonction RTrim supprime les espaces blancs de fin d’une chaîne.

**Syntaxe :** <br> `str RTrim(str value)`

**Exemple :** <br> `RTrim(“ Test ”)` <br> Renvoie “ Test”.




----------
### Split

**Description :** <br> La fonction Split prend une chaîne séparée par un délimiteur et en fait une chaîne à valeurs multiples.


**Syntaxe :** <br> `mvstr Split(str value, str delimiter)` <br? `mvstr Split(str value, str delimiter, num limit)`

- value : chaîne contenant un caractère délimiteur pour assurer la séparation.
- delimiter : caractère unique à utiliser comme délimiteur.
- limit : nombre maximal de valeurs qui seront renvoyées.

**Exemple :** <br> `Split(“SMTP:john.doe@contoso.com,smtp:jd@contoso.com”,”,”)` <br> renvoie une chaîne à plusieurs valeurs avec 2 éléments utiles pour l’attribut proxyAddress




----------
### StringFromGuid

**Description :**<br> La fonction StringFromGuid prend un GUID binaire et le convertit en chaîne

**Syntaxe :** <br> `str StringFromGuid(bin GUID)`




----------
### StringFromSid

**Description :**<br> La fonction StringFromSid convertit un tableau d’octets ou un tableau d’octets à valeurs multiples contenant un identificateur de sécurité en chaîne ou chaîne à valeurs multiples.

**Syntaxe :** <br> `str StringFromSid(bin ObjectSID)` <br> `mvstr StringFromSid(mvbin ObjectSID)`




----------
### Switch

**Description :**<br> la fonction Switch est utilisée pour renvoyer une valeur unique en fonction des conditions évaluées.

**Syntaxe :** <br> `var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

- expr : expression de variante à évaluer.
- value : valeur à retourner si l’expression correspondante a la valeur True.

**Remarques :** <br> La liste d’arguments de la fonction Switch se compose de paires d’expressions et de valeurs. Les expressions sont évaluées de gauche à droite et la valeur associée à la première expression à évoluer à True est renvoyée. Si les parties ne sont pas correctement couplées, une erreur d’exécution se produit.

Par exemple, si expr1 est True, Switch renvoie la valeur1. Si expr-1 est False, mais expr-2 est True, Switch renvoie la valeur 2, et ainsi de suite.

Switch renvoie une expression Nothing if : -aucune des expressions n’est défini sur True. -La première expression True possède une valeur correspondante est Null.

Switch évalue toutes les expressions, même si elle n’en renvoie qu’une. Pour cette raison, il est conseillé de surveiller les éventuels effets secondaires. Par exemple, si l’évaluation d’une expression entraîne une division par zéro, une erreur se produit.

La valeur peut être également la fonction Error, qui renvoie une chaîne personnalisée.

**Exemple :** <br> `Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error(“Unknown city”))` <br> Renvoie la langue parlée dans certaines grandes villes ; sinon, renvoie une erreur.




----------
### Trim

**Description :** <br>la fonction Trim supprime les espaces à gauche et à droite d’une chaîne.

**Syntaxe :** <br> `str Trim(str value)` <br> `mvstr Trim(mvstr value)`

**Exemple :** <br> `Trim(“ Test ”)` <br> Renvoie “Test”.

`Trim([proxyAddresses])` <br> Supprime les espaces blancs de début et de fin pour chaque valeur contenue dans l’attribut proxyAddress.




----------
### UCase

**Description :** <br> La fonction UCase convertit tous les caractères d’une chaîne en majuscules.

**Syntaxe :** <br> `str UCase(str string)`

**Exemple :** <br> `UCase(“TeSt”)` <br> Renvoie “TEST”.




----------
### Word

**Description :**<br> La fonction Word renvoie un mot contenu dans une chaîne, en fonction des paramètres qui décrivent les délimiteurs à utiliser et le nombre de mots à renvoyer.

**Syntaxe :** <br> `str Word(str string, num WordNumber, str delimiters)`

- string : chaîne à partir de laquelle le mot est renvoyé.
- WordNumber : nombre identifiant le nombre de mots à renvoyer.
- delimiters : chaîne représentant le ou les délimiteur(s) à utiliser pour identifier les mots

**Remarques :** <br> Chaque chaîne de caractères contenue dans la chaîne séparée par l’un des caractères figurant dans delimiters est identifiée en tant que mot :

- Si number < 1, retourne une chaîne vide.
- Si string a la valeur null, renvoie une chaîne vide.

Si la chaîne contient moins de mots ou ne contient pas les mots identifiés par les délimiteurs, une chaîne vide est retournée.


**Exemple :** <br> `Word(“The quick brown fox”,3,” “)` <br> Renvoie « brown ».

`Word(“This,string!has&many seperators”,3,”,!&#”)` <br> Renvoie « has »


## Ressources supplémentaires

* [Comprendre les expressions d’approvisionnement déclaratif](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync : personnalisation des options de synchronisation](active-directory-aadconnectsync-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)


<!--Image references-->

<!---HONumber=AcomDC_0128_2016-->