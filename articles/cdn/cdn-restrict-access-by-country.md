---
title: "Restriction de l’accès à votre contenu CDN Azure par pays | Microsoft Docs"
description: "Découvrez comment limiter l’accès à votre contenu Azure CDN à l’aide de la fonctionnalité de filtrage géographique."
services: cdn
documentationcenter: 
author: lichard
manager: akucer
editor: 
ms.assetid: 20943ac8-9dce-4cf2-85ed-e8362750aa63
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: 2ee72c31072fb5201e057f4c3567d33ce53e4fbc
ms.openlocfilehash: 1a1cf8cea1b321bb4e758e9ad06e42da59d3bcb4


---
# <a name="restrict-access-to-your-content-by-country---verizon"></a>Restriction de l’accès à votre contenu par pays - Verizon
> [!div class="op_single_selector"]
> * [Verizon](cdn-restrict-access-by-country.md)
> * [Akamai Standard](cdn-restrict-access-by-country-akamai.md)
> 
> 

## <a name="overview"></a>Vue d’ensemble
Par défaut, lorsqu'un utilisateur demande du contenu, le contenu est pris en charge, quel que soit l’endroit d’où vient la demande. Dans certains cas, vous souhaiterez limiter l'accès à votre contenu par pays. Cette rubrique explique comment utiliser la fonctionnalité de **Filtrage géographique** afin de configurer le service pour autoriser ou bloquer l'accès par pays.

> [!IMPORTANT]
> Les produits Verizon et Akamai fournissent les mêmes fonctionnalités de filtrage géographique, mais leurs interfaces utilisateur diffèrent. Ce document décrit l’interface du **CDN Azure Standard fourni par Verizon** et du **CDN Azure Premium fourni par Verizon**. Pour le filtrage géographique avec **Azure CDN Standard fourni par Akamai**, voir [Restriction de l’accès à votre contenu par pays - Akamai](cdn-restrict-access-by-country-akamai.md).
> 
> 

Pour plus d’informations sur les considérations qui s’appliquent à la configuration de ce type de restriction, consultez la section [Considérations](cdn-restrict-access-by-country.md#considerations) à la fin de la rubrique.  

> [!NOTE]
> Une fois la configuration définie, elle s’applique à tous les points de terminaison du **CDN Azure fourni par Verizon** dans ce profil CDN Azure.
> 
> 

![Filtrage par pays](./media/cdn-filtering/cdn-country-filtering.png)

## <a name="step-1-define-the-directory-path"></a>Étape 1 : définir le chemin d'accès
Lorsque vous configurez un filtre de pays, vous devez spécifier le chemin d'accès relatif à l'emplacement auquel les accès utilisateurs sont autorisés ou refusés. Vous pouvez appliquer le filtrage de pays pour tous vos fichiers avec « / » ou certains dossiers en spécifiant des chemins d'accès au répertoire.

Exemple de filtre de chemin d'accès au répertoire :

    /                                 
    /Photos/
    /Photos/Strasbourg

## <a name="step-2-define-the-action-block-or-allow"></a>Étape 2 : Définir l'action : autoriser ou bloquer
**Bloquer** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est bloqué aux utilisateurs des pays spécifiés. Si aucune autre option de filtrage par pays n'a été configurée pour cet emplacement, tous les autres utilisateurs sont autorisés à y accéder.

**Autoriser** : l’accès aux ressources demandées à partir de ce chemin d'accès récursif est autorisé aux seuls utilisateurs des pays spécifiés.

## <a name="step-3-define-the-countries"></a>Étape 3 : définir les pays
Sélectionnez les pays que vous souhaitez bloquer ou autoriser pour le chemin d'accès. Pour plus d’informations, consultez l’article [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)(Codes de pays Azure CDN par Verizon).

Par exemple, la règle de blocage /Photos/Strasbourg/ filtre les fichiers, notamment :

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


## <a name="country-codes"></a>Codes de pays
La fonctionnalité de **Filtrage géographique** utilise des codes de pays pour définir les pays à partir desquels une demande est autorisée ou bloquée pour un répertoire sécurisé. Vous trouverez les codes de pays sur la page [Azure CDN from Verizon Country Codes](https://msdn.microsoft.com/library/mt761717.aspx)(Code de pays Azure CDN par Verizon). Si vous spécifiez « EU » (Europe) ou « AP » (Asie/Pacifique), un sous-ensemble des adresses IP issues de n'importe quel pays de ces régions est bloqué ou autorisé.

## <a name="a-idconsiderationsaconsiderations"></a><a id="considerations"></a>Considérations
* L’entrée en vigueur des modifications apportées à votre configuration de filtrage par pays peut prendre jusqu’à 90 minutes.
* Cette fonctionnalité ne prend pas en charge les caractères génériques (par exemple, « * »).
* La configuration de filtrage par pays associée avec le chemin d'accès relatif de filtrage est appliquée de manière récursive à ce chemin d’accès.
* Une seule règle peut être appliquée au même chemin d'accès relatif (vous ne pouvez pas créer plusieurs filtres de pays qui pointent vers le même chemin d'accès relatif). Toutefois, un dossier peut avoir plusieurs filtres par pays. Cela est dû à la nature récursive des filtres par pays. En d'autres termes, un filtre par pays différent peut être attribué à un sous-dossier d'un dossier déjà configuré.




<!--HONumber=Nov16_HO3-->


