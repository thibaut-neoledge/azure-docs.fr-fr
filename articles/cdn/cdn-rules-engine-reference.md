---
title: "Référence du moteur de règles Azure CDN | Microsoft Docs"
description: "Documentation de référence sur les fonctionnalités et conditions de correspondance du moteur de règles Azure CDN."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: c10145661a8c575381493c9aaa901c3ef92c2e81
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cdn-rules-engine"></a>Moteur des règles Azure CDN
Cette rubrique répertorie les descriptions détaillées des fonctionnalités et conditions de correspondance disponibles pour le [moteur de règles](cdn-rules-engine.md)Azure Content Delivery Network (CDN).

Le moteur de règles HTTP est conçu pour être l’autorité finale sur la façon dont certains types de demandes sont traités par le CDN.

**Utilisations courantes** :

- Remplacer ou définir une stratégie de cache personnalisée.
- Sécuriser ou refuser les demandes de contenu sensible.
- Rediriger les demandes.
- Stocker les données de journal personnalisé.

## <a name="terminology"></a>Terminologie
Une règle est définie à l’aide d’[**expressions conditionnelles**](cdn-rules-engine-reference-conditional-expressions.md), de [**correspondances**](cdn-rules-engine-reference-match-conditions.md) et de [**fonctionnalités**](cdn-rules-engine-reference-features.md). Ces éléments sont mis en surbrillance dans l’illustration suivante.

 ![Condition de correspondance CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntaxe

La façon dont les caractères spéciaux sont traités varie en fonction de la façon dont une condition de correspondance ou une fonctionnalité gère les valeurs de texte. Une condition de correspondance ou une fonctionnalité peut interpréter le texte de l’une des manières suivantes :

1. [**Valeurs littérales**](#literal-values) 
2. [**Valeurs de caractère générique**](#wildcard-values)
3. [**Expressions régulières**](#regular-expressions)

### <a name="literal-values"></a>Valeurs littérales
Le texte interprété comme une valeur littérale traite tous les caractères spéciaux, à l’exception du symbole %, comme une partie de la valeur qui doit être mise en correspondance. En d’autres termes, une condition de correspondance littérale définie sur `\'*'\` n’est remplie que si cette valeur exacte (c'est-à-dire `\'*'\`) est trouvée.
 
Un symbole de pourcentage est utilisé pour indiquer l’encodage des URL (par exemple, `%20`).

### <a name="wildcard-values"></a>Valeurs de caractère générique
Le texte interprété comme un caractère générique attribue une signification supplémentaire aux caractères spéciaux. Le tableau ci-dessous décrit comment le jeu de caractères suivant est interprété.

Caractère | Description
----------|------------
\ | Une barre oblique inverse est utilisée pour échapper les caractères spécifiés dans ce tableau. Une barre oblique inverse doit être spécifiée juste avant le caractère spécial à échapper.<br/>Par exemple, la syntaxe suivante échappe un astérisque : `\*`
% | Un symbole de pourcentage est utilisé pour indiquer l’encodage des URL (par exemple, `%20`).
* | Un astérisque est un caractère générique représentant un ou plusieurs caractères.
Espace | Un caractère d’espace indique qu’une condition de correspondance peut être remplie par les valeurs ou les modèles spécifiés.
'valeur' | Un guillemet simple n’a pas de signification particulière. Toutefois, un jeu de guillemets simples est utilisé pour indiquer qu’une valeur doit être traitée comme une valeur littérale. Il peut être utilisé selon les manières suivantes :<br><br/>- Il permet de remplir une condition de correspondance lorsque la valeur spécifiée correspond à une partie de la valeur de comparaison.  Par exemple, `'ma'` peut correspondre à l’une des chaînes suivantes : <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Il permet de spécifier un caractère spécial en tant que caractère littéral. Par exemple, vous pouvez spécifier un caractère d’espace littéral en plaçant un caractère d’espace dans un jeu de guillemets simples (par exemple, `' '` ou `'sample value'`).<br/>- Il permet de spécifier une valeur vide. Spécifiez une valeur vide en entrant un jeu de guillemets simples (par exemple, '').<br /><br/>**Important :**<br/>- Si la valeur spécifiée ne contient pas de caractère générique, elle est automatiquement considérée comme une valeur littérale. Cela signifie qu’il n’est pas nécessaire d’entrer un jeu de guillemets simples.<br/>- Si une barre oblique inverse n’échappe pas à un autre caractère dans ce tableau, elle est ignorée si un jeu de guillemets simples est entré.<br/>- Une autre manière de spécifier un caractère spécial en tant que caractère littéral consiste à l’échapper à l’aide d’une barre oblique inverse (par exemple, `\`).

### <a name="regular-expressions"></a>Expressions régulières

Les expressions régulières définissent un modèle qui sera recherché dans une valeur de texte. La notation d’une expression régulière définit des significations spécifiques pour une variété de symboles. Le tableau ci-dessous indique comment les caractères spéciaux sont traités par les conditions de correspondance et les fonctionnalités prenant en charge les expressions régulières.

Caractère spécial | Description
------------------|------------
\ | Une barre oblique inverse échappe le caractère qui la suit. Ce caractère est alors traité comme une valeur littérale plutôt que d’utiliser sa signification d’expression régulière. Par exemple, la syntaxe suivante échappe un astérisque : `\*`
% | La signification d’un symbole de pourcentage dépend de son utilisation.<br/><br/> `%{HTTPVariable}` : cette syntaxe identifie une variable HTTP.<br/>`%{HTTPVariable%Pattern}` : cette syntaxe utilise un symbole de pourcentage pour identifier une variable HTTP et comme délimiteur.<br />`\%` : l’échappement d’un symbole de pourcentage permet de l’utiliser comme une valeur littérale ou d’indiquer l’encodage des URL (par exemple, `\%20`).
* | Un astérisque permet de mettre en correspondance une ou plusieurs fois le caractère qui le précède. 
Espace | Un caractère d’espace est généralement traité comme un caractère littéral. 
'valeur' | Les guillemets simples sont traités comme des caractères littéraux. Un jeu de guillemets simples n’a pas de signification particulière.


## <a name="next-steps"></a>Étapes suivantes
* [Conditions de correspondance du moteur de règles](cdn-rules-engine-reference-match-conditions.md)
* [Expressions conditionnelles du moteur de règles](cdn-rules-engine-reference-conditional-expressions.md)
* [Fonctionnalités du moteur de règles](cdn-rules-engine-reference-features.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)
* [Vue d'ensemble d'Azure CDN](cdn-overview.md)
