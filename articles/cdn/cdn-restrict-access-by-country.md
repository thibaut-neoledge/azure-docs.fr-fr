---
title: "Limiter l’accès à votre contenu CDN Azure par pays | Microsoft Docs"
description: "Découvrez comment limiter l’accès à votre contenu Azure CDN à l’aide de la fonctionnalité de filtrage géographique."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.contentlocale: fr-fr
ms.lasthandoff: 01/25/2017

---
# <a name="restrict-azure-cdn-content-by-country"></a>Limiter l’accès à votre contenu CDN Azure par pays

## <a name="overview"></a>Vue d'ensemble
Par défaut, lorsqu'un utilisateur demande du contenu, le contenu est pris en charge, quel que soit l’endroit d’où vient la demande. Dans certains cas, vous souhaiterez limiter l'accès à votre contenu par pays. Cette rubrique explique comment utiliser la fonctionnalité de **Filtrage géographique** afin de configurer le service pour autoriser ou bloquer l'accès par pays.

> [!IMPORTANT]
> Les produits Verizon et Akamai fournissent la même fonctionnalité de filtrage géographique, mais dans une interface utilisateur légèrement différente. Un lien vers les différences est disponible à l’étape 3.


Pour plus d’informations sur les considérations qui s’appliquent à la configuration de ce type de restriction, consultez la section [Considérations](cdn-restrict-access-by-country.md#considerations) à la fin de la rubrique.  

![Filtrage par pays](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Étape 1 : définir le chemin d'accès
Sélectionnez votre point de terminaison dans le portail et trouvez l’onglet Filtrage géographique dans le volet de navigation gauche pour trouver cette fonctionnalité.

Lorsque vous configurez un filtre de pays, vous devez spécifier le chemin d'accès relatif à l'emplacement auquel les accès utilisateurs sont autorisés ou refusés. Vous pouvez appliquer le filtrage géographique pour tous vos fichiers avec « / » ou certains dossiers en spécifiant des chemins d'accès au répertoire "/pictures/". Vous pouvez également appliquer le filtrage géographique à un seul fichier en spécifiant le fichier et en omettant la barre oblique « /pictures/city.png ».

Exemple de filtre de chemin d'accès au répertoire :

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Étape 2 : Définir l'action : autoriser ou bloquer
**Bloquer** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est bloqué aux utilisateurs des pays spécifiés. Si aucune autre option de filtrage par pays n'a été configurée pour cet emplacement, tous les autres utilisateurs sont autorisés à y accéder.

**Autoriser** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est autorisé aux seuls utilisateurs des pays spécifiés.

## <a name="step-3-define-the-countries"></a>Étape 3 : définir les pays
Sélectionnez les pays que vous souhaitez bloquer ou autoriser pour le chemin d'accès. 

Par exemple, la règle de blocage /Photos/Strasbourg/ filtre les fichiers, notamment :

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Codes de pays
La fonctionnalité de **Filtrage géographique** utilise des codes de pays pour définir les pays à partir desquels une demande est autorisée ou bloquée pour un répertoire sécurisé. Vous trouverez les codes de pays sur la page [Azure CDN Country Codes (Code de pays du CDN Azure)](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a id="considerations"></a>Considérations
* L’implémentation des modifications apportées à votre configuration de filtrage par pays peut prendre jusqu’à 90 minutes avec la solution Verizon ou quelques minutes avec la solution Akamai.
* Cette fonctionnalité ne prend pas en charge les caractères génériques (par exemple, « * »).
* La configuration de filtrage géographique associée avec le chemin d'accès relatif de filtrage est appliquée de manière récursive à ce chemin d’accès.
* Une seule règle peut être appliquée au même chemin d'accès relatif (vous ne pouvez pas créer plusieurs filtres de pays qui pointent vers le même chemin d'accès relatif). Toutefois, un dossier peut avoir plusieurs filtres par pays. Cela est dû à la nature récursive des filtres par pays. En d'autres termes, un filtre par pays différent peut être attribué à un sous-dossier d'un dossier déjà configuré.


