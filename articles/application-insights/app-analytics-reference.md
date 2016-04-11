<properties 
	pageTitle="Documents de référence pour Analytics dans Application Insights" 
	description="Expressions régulières dans Analytics, le puissant outil de recherche d’Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Application Insights : documents de référence Analytics

[Analytics](app-analytics.md) vous permet d’exécuter de puissantes requêtes sur les données de télémétrie collectées par [Application Insights](app-insights-overview.md) à partir de votre application. Ces pages décrivent son langage de requête.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Expressions régulières


[Description générale des expressions régulières](https://github.com/google/re2/wiki/Syntax).

Cette page répertorie la syntaxe des expressions régulières acceptée par RE2. Elle répertorie également la syntaxe acceptée par PCRE, PERL et VIM.

|
|---|---
|Caractères uniques : | 
|. |n’importe quel caractère, y compris éventuellement un caractère de nouvelle ligne (s=vrai) 
|[xyz] |classe de caractères 
|[^xyz] |classe de caractères exclue 
|\\d |classe de caractères Perl 
|\\D |classe de caractères Perl exclue 
|[[:alpha:]] |classe de caractères ASCII 
|[[:^alpha:]] |classe de caractères ASCII exclue 
|\\pN |classe de caractères Unicode (nom d’une lettre) 
|\\p{Greek} |classe de caractères Unicode 
|\\PN |classe de caractères Unicode exclue (nom d’une lettre) 
|\\P{Greek} |classe de caractères Unicode exclue 
|Composites: | 
|xy |x suivi de y 
|x&#124;y |x ou y (préférer x) 
| 
|Répétitions: | 
| |zéro x ou plus, préférer plus 
|x+ |un x ou plus, préférer plus 
|x? |zéro ou un x, préférer un 
|x{n,m} |n ou n+1 ou ... ou m x, préférer plus 
|x{n,} |n x ou plus, préférer plus 
|x{n} |exactement n x 
|x*? |zéro x ou plus, préférer moins 
|x+? |un x ou plus, préférer moins 
|x?? |zéro ou un x, préférer zéro 
|x{n,m}? |n ou n+1 ou ... ou m x, préférer moins 
|x{n,}? |n x ou plus, préférer moins 
|x{n}? |exactement n x 
|x{} |(== x*) (non pris en charge) VIM 
|x{-} |(== x*?) (non pris en charge) VIM
|x{-n} |(== x{n}?) (non pris en charge) VIM 
|x= |(== x?) (non pris en charge) VIM 
|Restriction d’implémentation : les formes de comptage x{n,m}, x{n,} et x{n} | 
|rejettent les formes qui créent un nombre de répétitions minimal ou maximal supérieur à 1 000. | 
|Les répétitions illimitées échappent à cette restriction. | 
|Répétitions possessives : | 
|x*+ |zéro x ou plus, possessif (non pris en charge) 
|x++ |un x ou plus, possessif (non pris en charge) 
|x?+ |zéro ou un x, possessif (non pris en charge) 
|x{n,m}+ |n ou ... ou m x, possessif (non pris en charge) 
|x{n,}+ |n x ou plus, possessif (non pris en charge) 
|x{n}+ |exactement n x, possessif (non pris en charge) 
|Groupement : |
|(re) |groupe de capture numéroté (sous-correspondance) 
|(?P<name>re) |groupe de capture nommé & numéroté (sous-correspondance) 
|(?<name>re) |groupe de capture nommé & numéroté (sous-correspondance) (non pris en charge) 
|(?'nom're) |groupe de capture nommé & numéroté (sous-correspondance) (non pris en charge) 
|(?:re) |groupe de non-capture 
|(?indicateurs) |définir des indicateurs dans le groupe actuel ; non-capture 
|(?indicateurs:re) |définir des indicateurs pendant re ; non-capture 
|(?#texte) |commentaire (non pris en charge) 
|(?&#124;x&#124;y&#124;z)  |numérotation de branche redéfinie (non pris en charge) 
|(?>re) |correspondance possessive de re (non pris en charge) 
|re@> |correspondance possessive de re (non pris en charge) VIM 
|%(re) |groupe de non-capture (non pris en charge) VIM
|Indicateurs : | 
|i |non-respect de la casse (faux par défaut) 
|m |mode multi-ligne : ^ et $ correspondent au début et à la fin de la ligne et au début et à la fin du texte (faux par défaut) 
|s |laisser . correspondre à \\n (faux par défaut) 
|U |non vorace : signification équivalente de x* et x*?, x+ et x+?, etc. (faux par défaut) 
|La syntaxe de l’indicateur est xyz (définir) ou -xyz (effacer) ou xy-z (définir xy, effacer z). | 
|Chaînes vides : | 
|^ |au début du texte ou de la ligne (m=vrai) 
|$ |à la fin du texte (comme \\z, pas \\Z) ou de la ligne (m=vrai) 
|\\A |au début du texte 
|\\b |à la limite d’un mot ASCII (\\w d’un côté et \\W, \\A ou \\z de l’autre) 
|\\B |pas à la limite d’un mot ASCII 
|\\G |au début du sous-texte recherché (non pris en charge) PCRE 
|\\G |à la fin de la dernière correspondance (non pris en charge) PERL 
|\\Z |à la fin du texte, ou avant le caractère de nouvelle ligne à la fin du texte (non pris en charge) 
|\\z |à la fin du texte 
|(?=re) |avant le texte correspondant à re (non pris en charge) 
|(?!re) |avant le texte non correspondant à re (non pris en charge) 
|(?<=re) |après le texte correspondant à re (non pris en charge) 
|(?<!re) |après le texte non correspondant à re (non pris en charge) 
|re& |avant le texte correspondant à re (non pris en charge) VIM 
|re@= |avant le texte correspondant à re (non pris en charge) VIM 
|re@! |avant le texte non correspondant à re (non pris en charge) VIM 
|re@<= |après le texte correspondant à re (non pris en charge) VIM 
|re@<! |après le texte non correspondant à re (non pris en charge) VIM
|\\zs |définit le début de la correspondance (= \\K) (non pris en charge) VIM 
|\\ze |définit la fin de la correspondance (non pris en charge) VIM 
|\\%^ |début du fichier (non pris en charge) VIM 
|\\%$ |fin du fichier (non pris en charge) VIM 
|\\%V |à l’écran (non pris en charge) VIM 
|\\%# |position du curseur (non pris en charge) VIM 
|\\%'m |position de la marque m (non pris en charge) VIM 
|\\%23l |dans ligne 23 (non pris en charge) VIM 
|\\%23c |dans colonne 23 (non pris en charge) VIM 
|\\%23v |dans colonne virtuelle 23 (non pris en charge) VIM 
|Séquences d’échappement : | 
|\\a |sonnerie (== \\007) 
|\\f |saut de page (== \\014) 
|\\t |caractère Tab horizontal (== \\011) 
|\\n |caractère de nouvelle ligne (== \\012) 
|\\r |retour chariot (== \\015) 
|\\v |caractère Tab vertical (== \\013) 
|* |littéral *, pour n’importe quel signe de ponctuation * 
|\\123 |code de caractère octal (jusqu’à trois chiffres) 
|\\x7F |code de caractère hexadécimal (exactement deux chiffres) 
|\\x{10FFFF} |code de caractère hexadécimal 
|\\C |un seul octet même en mode UTF-8|
|\\Q...\\E |texte littéral ... même si ... contient une ponctuation 
|\\1 |référence arrière (non pris en charge) 
|\\b |retour arrière (non pris en charge) (utiliser \\010) 
|\\cK |caractère de contrôle ^K (non pris en charge) (utiliser \\001 etc.) 
|\\e |échappement (non pris en charge) (utiliser \\033) 
|\\g1 |référence arrière (non pris en charge) 
|\\g{1} |référence arrière (non pris en charge) 
|\\g{+1} |référence arrière (non pris en charge) 
|\\g{-1} |référence arrière (non pris en charge) 
|\\g{nom} |référence arrière nommée (non pris en charge) 
|\\g<name> |appel de sous-routine (non pris en charge) 
|\\g'nom' |appel de sous-routine (non pris en charge) 
|\\k<name> |référence arrière nommée (non pris en charge)
|\\k'nom' |référence arrière nommée (non pris en charge) 
|\\lX |X minuscule (non pris en charge) 
|\\ux |x majuscule (non pris en charge) 
|\\L...\\E |texte en minuscules ... (non pris en charge) 
|\\K |redéfinir le début de $0 (non pris en charge)
|\\N{nom} |caractère Unicode nommé (non pris en charge) 
|\\R |saut de ligne (non pris en charge) 
|\\U...\\E |texte en majuscules ... (non pris en charge) 
|\\X |séquence Unicode étendue (non pris en charge) 
|\\%d123 |caractère décimal 123 (non pris en charge) VIM 
|\\%xFF |caractère hexadécimal FF (non pris en charge) VIM 
|\\%o123 |caractère octal 123 (non pris en charge) VIM 
|\\%u1234 |caractère Unicode 0x1234 (non pris en charge) VIM
|\\%U12345678 |caractère Unicode 0x12345678 (non pris en charge) VIM 
|Éléments de classe de caractères : | 
|x |caractère unique
|A-Z |plage de caractères (inclusive) 
|\\d |classe de caractères Perl 
|[:foo:] |classe de caractères ASCII foo 
|\\p{Foo} |classe de caractères Unicode Foo 
|\\pF |classe de caractères Unicode F (nom d’une lettre) 
|Classes de caractères nommées en tant qu’éléments de classe de caractères : | 
|[\\d] |chiffres (== \\d) 
|[^\\d] |pas de chiffres (== \\D) 
|[\\D] |pas de chiffres (== \\D) 
|[^\\D] |pas pas de chiffres (== \\d) 
|[[:nom:]] |classe ASCII nommée dans classe de caractères (== [:nom:])  
|[^[:nom:]]|classe ASCII nommée dans classe de caractères exclue (== [:^nom:]) 
|[\\p{Nom}] |propriété Unicode nommée dans classe de caractères (== \\p{Nom}) 
|[^\\p{Nom}] |propriété Unicode nommée dans classe de caractères exclue (== \\P{Nom}) 
|Classes de caractères Perl (toutes ASCII uniquement) : | 
|\\d |chiffres (== [0-9]) 
|\\D |pas de chiffres (== [^0-9]) 
|\\s |espace (== [\\t\\n\\f\\r ]) 
|\\S |pas d’espace (== [^\\t\\n\\f\\r ]) 
|\\w |caractères de mot (== [0-9A-Za-z\_]) 
|\\W |pas de caractères de mot (== [^0-9A-Za-z\_]) 
|\\h |espace horizontal (non pris en charge) 
|\\H |pas d’espace horizontal (non pris en charge) 
|\\v |espace vertical (non pris en charge)
|\\V |pas d’espace vertical (non pris en charge) 
|Classes de caractères ASCII : | 
|[[:alnum:]] |alphanumérique (== [0-9A-Za-z]) 
|[[:alpha:]] |alphabétique (== [A-Za-z]) 
|[[:ascii:]] |ASCII (== [\\x00-\\x7F]) 
|[[:blank:]] |espace (== [\\t ]) 
|[[:cntrl:]] |contrôle (== [\\x00-\\x1F\\x7F]) 
|[[:digit:]] |chiffres (== [0-9]) 
|[[:graph:]] |graphique (== [!-~] == [A-Za-z0-9!"#$%&’()*+,-./:;<=>?@[\\]^\_`{&#124;}~]) 
|[[:lower:]] |lower case (== [a-z]) 
|[[:print:]] |printable (== [ -~] == [ [:graph:]]) 
|[[:punct:]] |punctuation (== [!-/:-@[-`{-~]) 
|[[:space:]] |espace blanc (== [\\t\\n\\v\\f\\r ]) 
|[[:upper:]] |majuscule (== [A-Z]) 
|[[:word:]] |caractères de mot (== [0-9A-Za-z\_]) 
|[[:xdigit:]] |chiffre hexadécimal (== [0-9A-Fa-f]) 
|Noms de classes de caractères Unicode - catégorie générale : | 
|C |autre 
|Cc |contrôle 
|Cf |format 
|Cn |points de code non affecté (non pris en charge) 
|Co |usage privé 
|Cs |substitution 
|L |lettre
|LC |lettre à casse gérée (non pris en charge) 
|L& |lettre à casse gérée (non pris en charge)
|Ll |lettre minuscule 
|Lm |lettre modificatrice 
|Lo |autre lettre 
|Lt |lettre avec casse de titre 
|Lu |lettre majuscule 
|M |marque 
|Mc |marque d’espacement
|Me |marque d’encadrement 
|Mn |marque de non-espacement 
|N |nombre 
|Nd |nombre décimal 
|Nl |nombre de lettre
|No |autre nombre 
|P |ponctuation 
|Pc |ponctuation de connexion 
|Pd |tiret de ponctuation 
|Pe |ponctuation de fermeture
|Pf |ponctuation finale 
|Pi |ponctuation initiale 
|Po |autre ponctuation 
|Ps |ponctuation d’ouverture
|S |symbole 
|Sc |symbole monétaire 
|Sk |symbole modificateur 
|Sm |symbole mathématique 
|So |autre symbole 
|Z |séparateur 
|Zl |séparateur de ligne 
|Zp |séparateur de paragraphe 
|Zs |espace de séparation 
|Noms de classes de caractères Unicode - scripts : |
|Arabic |arabe 
|Armenian |arménien 
|Balinese |balinais 
|Bamum |bamoum 
|Batak |batik 
|Bengali |bengali 
|Bopomofo |bopomofo 
|Brahmi |brahmi 
|Braille |braille 
|Buginese |bugi 
|Buhid |bouhid 
|Canadian\_Aboriginal |autochtones canadiens 
|Carian |carien 
|Chakma |chakma 
|Cham |cham 
|Cherokee |cherokee 
|Common |caractères non spécifiques d’un script 
|Coptic |copte 
|Cuneiform |cunéiforme 
|Cypriot |chypriote 
|Cyrillic |cyrillique 
|Deseret |déseret 
|Devanagari |devanagari 
|Egyptian\_Hieroglyphs|hiéroglyphes égyptiens 
|Ethiopic |éthiopien 
|Georgian |géorgien 
|Glagolitic |glagolitique 
|Gothic |gothique 
|Greek |grec 
|Gujarati |goudjrati
|Gurmukhi |gurmukhi 
|Han |han 
|Hangul |hangul 
|Hanunoo |hanounóo 
|Hebrew |hébreu
|Hiragana |hiragana 
|Imperial\_Aramaic |araméen impérial 
|Inherited |hériter le script du caractère précédent 
|Inscriptional\_Pahlavi |pehlevi des inscriptions 
|Inscriptional\_Parthian |parthe des inscriptions
|Javanese |javanais 
|Kaithi |kaithî 
|Kannada |kannada 
|Katakana |katakana 
|Kayah\_Li |kayah li 
|Kharoshthi |kharochthî 
|Khmer |khmer 
|Lao |lao 
|Latin |latin 
|Lepcha |lepcha 
|Limbu |limbou
|Linear\_B |linéaire B 
|Lycian |lycien 
|Lydian |lydien 
|Malayalam |malayalam 
|Mandaic |mandéen 
|Meetei\_Mayek |meitei mayek
|Meroitic\_Cursive |méroïtique cursif 
|Meroitic\_Hieroglyphs |méroïtique hiéroglyphique 
|Miao |miáo 
|Mongolian |mongol 
|Myanmar |myanmar 
|New\_Tai\_Lue |nouveau taï-lue (également appelé taï-lue simplifié) 
|Nko |N'ko 
|Ogham |ogham 
|Ol\_Chiki |ol tchiki 
|Old\_Italic |ancien italique 
|Old\_Persian |persépolitain 
|Old\_South\_Arabian |sud-arabique 
|Old\_Turkic |orkhon 
|Oriya |oriya  
|Osmanya|osmanais 
|Phags\_Pa |phags pa 
|Phoenician |phénicien 
|Rejang |redjang 
|Runic |runique 
|Saurashtra |saurachtra 
|Sharada |sharada 
|Shavian| shavien 
|Sinhala |cinghalais 
|Sora\_Sompeng |sora sompeng 
|Sundanese |sundanais 
|Syloti\_Nagri |syloti nagri 
|Syriac|syriaque 
|Tagalog |tagalog 
|Tagbanwa |tagbanoua 
|Tai\_Le |taï-le 
|Tai\_Tham |taï tham 
|Tai\_Viet |taï viêt 
|Takri |takri
|Tamil |tamoul 
|Telugu |télougou 
|Thaana |tana 
|Thai |thaï 
|Tibetan |tibétain 
|Tifinagh |tifinagh 
|Ugaritic |ougaritique
|Vai |vaï 
|Yi |yi 
|Classes de caractères Vim : | 
|\\i |caractère identificateur (non pris en charge) VIM 
|\\I |\\i à l’exception des chiffres (non pris en charge) VIM 
|\\k |caractère de mot clé (non pris en charge) VIM 
|\\K |\\k à l’exception des chiffres (non pris en charge) VIM 
|\\f |caractère de nom de fichier (non pris en charge) VIM 
|\\F |\\f à l’exception des chiffres (non pris en charge) VIM 
|\\p |caractère imprimable (non pris en charge) VIM 
|\\P |\\p à l’exception des chiffres (non pris en charge) VIM 
|\\s |espace blanc(== [ \\t]) (non pris en charge) VIM 
|\\S |caractère autre que l’espace blanc(== [^ \\t]) (non pris en charge) VIM 
|\\d |chiffres (== [0-9]) VIM 
|\\D |pas \\d VIM 
|\\x |chiffres hexadécimaux (== [0-9A-Fa-f]) (non pris en charge) VIM 
|\\X |pas \\x (non pris en charge) VIM 
|\\o |chiffres octaux (== [0-7]) (non pris en charge) VIM 
|\\O |pas \\o (non pris en charge) VIM 
|\\w |caractère de mot VIM 
|\\W |pas \\w VIM 
|\\h |caractère tête de mot (non pris en charge) VIM
|\\H |pas \\h (non pris en charge) VIM 
|\\a |alphabétique (non pris en charge) VIM 
|\\A |pas \\a (non pris en charge) VIM 
|\\l |minuscule (non pris en charge) VIM 
|\\L |pas minuscule (non pris en charge) VIM 
|\\u |majuscule (non pris en charge) VIM 
|\\U |pas majuscule (non pris en charge) VIM 
|\_x |\\x plus caractère de nouvelle ligne, pour tout x (non pris en charge) VIM 
|Indicateurs Vim : | 
|\\c |ignorer la casse (non pris en charge) VIM 
|\\C |respecter la casse (non pris en charge) VIM 
|\\m |magique (non pris en charge) VIM 
|\\M |non magique (non pris en charge) VIM 
|\\v |très magique (non pris en charge) VIM 
|\\V |très non magique (non pris en charge) VIM 
|\\Z |ignorer les différences dans les caractères de combinaison Unicode (non pris en charge) VIM 
|Magique : | 
|(?{code}) |code Perl arbitraire (non pris en charge) PERL 
|(??{code}) |code Perl arbitraire différé (non pris en charge) PERL 
|(?n) |appel récursif au groupe de capture de l’expression régulière n (non pris en charge) 
|(?+n) |appel récursif au groupe relatif +n (non pris en charge) 
|(?-n) |appel récursif au groupe relatif -n (non pris en charge) 
|(?C) |appel PCRE (non pris en charge) PCRE 
|(?R) |appel récursif à l’ensemble de l’expression régulière (== (?0)) (non pris en charge) 
|(?&nom) |appel récursif à un groupe nommé (non pris en charge) 
|(?P=nom) |référence arrière nommée (non pris en charge) 
|(?P>nom) |appel récursif à un groupe nommé (non pris en charge) 
|(?(cond)vrai&#124;faux) |branche conditionnelle (non pris en charge) 
|(?(cond)vrai) |branche conditionnelle (non pris en charge) 
|(*ACCEPT) |rapprocher les expressions régulières de Prolog (non pris en charge)
|(*COMMIT) |(non pris en charge) 
|(*F) |(non pris en charge) 
|(*FAIL) |(non pris en charge) 
|(*MARK) |(non pris en charge) 
|(*PRUNE) |(non pris en charge) 
|(*SKIP) |(non pris en charge) 
|(*THEN) |(non pris en charge) 
|(*ANY) |définir la convention pour le caractère nouvelle ligne (non pris en charge) 
|(*ANYCRLF) |(non pris en charge) 
|(*CR) |(non pris en charge) 
|(*CRLF) |(non pris en charge) 
|(*LF) |(non pris en charge) 
|(*BSR_ANYCRLF) |définir la convention pour \R (non pris en charge) PCRE
|(*BSR_UNICODE) |(non pris en charge) PCRE




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->