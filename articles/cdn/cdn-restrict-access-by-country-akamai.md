---
title: "Restriction de l’accès de votre contenu CDN Azure par pays | Microsoft Docs"
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
ms.date: 10/14/2016
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 2ee72c31072fb5201e057f4c3567d33ce53e4fbc
ms.openlocfilehash: 1dfd5833d8637232b7998b7fdc7f39679645e1f7


---
# <a name="restrict-access-to-your-content-by-country---akamai"></a>Restriction de l'accès à votre contenu par pays - Akamai
> [!div class="op_single_selector"]
> * [Verizon](cdn-restrict-access-by-country.md)
> * [Akamai Standard](cdn-restrict-access-by-country-akamai.md)
> 
> 

## <a name="overview"></a>Vue d’ensemble
Par défaut, lorsqu'un utilisateur demande du contenu, le contenu est pris en charge, quel que soit l’endroit d’où vient la demande. Dans certains cas, vous souhaiterez limiter l'accès à votre contenu par pays. Cette rubrique explique comment utiliser la fonctionnalité de **Filtrage géographique** afin de configurer le service pour autoriser ou bloquer l'accès par pays.

> [!IMPORTANT]
> Les produits Verizon et Akamai fournissent les mêmes fonctionnalités de filtrage géographique, mais leurs interfaces utilisateur diffèrent. Ce document décrit l’interface du **Azure CDN Standard fourni par Akamai**. Pour le filtrage géographique avec **Azure CDN Standard/Premium de Verizon**, consultez [Restriction de l'accès à votre contenu par pays - Verizon](cdn-restrict-access-by-country.md).
> 
> 

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
Sélectionnez les pays que vous souhaitez bloquer ou autoriser pour le chemin d'accès. Pour plus d’informations, consultez l’article [Azure CDN from Akamai Country Codes](https://msdn.microsoft.com/library/mt761717.aspx) (Codes de pays Azure CDN par Akamai).

Par exemple, la règle de blocage /Photos/Strasbourg/ filtre les fichiers, notamment :

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


## <a name="country-codes"></a>Codes de pays
La fonctionnalité de **Filtrage géographique** utilise des codes de pays pour définir les pays à partir desquels une demande est autorisée ou bloquée pour un répertoire sécurisé. Vous trouverez les codes de pays sur la page [Azure CDN from Akamai Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)(Code de pays Azure CDN par Akamai). 

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Considérations
* L’entrée en vigueur des modifications apportées à votre configuration de filtrage par pays peut prendre jusqu’à plusieurs minutes.
* Cette fonctionnalité ne prend pas en charge les caractères génériques (par exemple, « * »).
* La configuration de filtrage géographique associée avec le chemin d'accès relatif de filtrage est appliquée de manière récursive à ce chemin d’accès.
* Une seule règle peut être appliquée au même chemin d'accès relatif (vous ne pouvez pas créer plusieurs filtres de pays qui pointent vers le même chemin d'accès relatif). Toutefois, un dossier peut avoir plusieurs filtres par pays. Cela est dû à la nature récursive des filtres par pays. En d'autres termes, un filtre par pays différent peut être attribué à un sous-dossier d'un dossier déjà configuré.




<!--HONumber=Nov16_HO3-->


