---
title: "Conditions de correspondance du moteur de règles Azure CDN | Microsoft Docs"
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
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Conditions de correspondance du moteur de règles Azure CDN
Cette rubrique répertorie les descriptions détaillées des conditions de correspondance disponibles pour le [moteur de règles](cdn-rules-engine.md)Azure Content Delivery Network (CDN).

La deuxième partie d’une règle est la condition de correspondance. Une condition de correspondance identifie des types spécifiques de requêtes pour lesquelles un ensemble de fonctionnalités est exécuté.

Par exemple, elle peut être utilisée pour filtrer les requêtes de contenu à un emplacement particulier, les requêtes générées à partir d’une adresse IP ou d’un pays particulier, ou filtrer les requête selon leurs informations d’en-tête.

## <a name="always"></a>Toujours

La condition de correspondance Toujours est conçue pour appliquer un ensemble de fonctionnalités par défaut à toutes les requêtes.

## <a name="device"></a>Appareil

La condition de correspondance Appareil identifie les requêtes effectuées à partir d’un appareil mobile selon ses propriétés.  La détection de périphérique mobile est obtenue par le biais de [WURFL](http://wurfl.sourceforge.net/).  Vous trouverez ci-dessous la liste des fonctionnalités WURFL et leurs variables de moteur de règles CDN.

> [!NOTE] 
> Les variables ci-dessous sont prises en charge dans les fonctionnalités **Modifier l’en-tête de requête client** et **Modifier l’en-tête de réponse client**.

Fonctionnalité | Variable | Description | Exemples de valeurs
-----------|----------|-------------|----------------
Nom de la marque | %{wurfl_cap_brand_name} | Chaîne qui indique le nom de la marque de l’appareil. | Samsung
Système d’exploitation de l’appareil | %{wurfl_cap_device_os} | Chaîne qui indique le système d’exploitation installé sur l’appareil. | IOS
Version du système d’exploitation de l’appareil | %{wurfl_cap_device_os_version} | Chaîne qui indique le numéro de version du système d’exploitation installé sur l’appareil. | 1.0.1
Orientation double | %{wurfl_cap_dual_orientation} | Valeur booléenne qui indique si l’appareil prend en charge l’orientation double. | true
DTD HTML par défaut | %{wurfl_cap_html_preferred_dtd} | Chaîne qui indique la définition de type de document (DTD) par défaut du périphérique mobile pour le contenu HTML. | Aucun<br/>xhtml_basic<br/>html5
Incorporation d’images | %{wurfl_cap_image_inlining} | Valeur booléenne qui indique si l’appareil prend en charge les images codées en Base64. | false
Est Android | %{wurfl_vcap_is_android} | Valeur booléenne qui indique si l’appareil prend en charge le système d’exploitation Android. | true
Est IOS | %{wurfl_vcap_is_ios} | Valeur booléenne qui indique si l’appareil prend en charge le système d’exploitation iOS. | false
Est une télévision intelligente | %{wurfl_cap_is_smarttv} | Valeur booléenne qui indique si l’appareil est une télévision intelligente. | false
Est un smartphone | %{wurfl_vcap_is_smartphone} | Valeur booléenne qui indique si l’appareil est un smartphone. | true
Est une tablette | %{wurfl_cap_is_tablet} | Valeur booléenne qui indique si l’appareil est une tablette. Il s’agit d’une description indépendante du système d’exploitation. | true
Est un appareil sans fil | %{wurfl_cap_is_wireless_device} | Valeur booléenne qui indique si l’appareil est considéré comme un appareil sans fil. | true
Nom marketing | %{wurfl_cap_marketing_name} | Chaîne qui indique le nom marketing de l’appareil. | BlackBerry 8100 Pearl
Navigateur mobile | %{wurfl_cap_mobile_browser} | Chaîne qui indique le navigateur utilisé pour demander du contenu depuis l’appareil. | Chrome
Version du navigateur mobile | %{wurfl_cap_mobile_browser_version} | Chaîne qui indique la version du navigateur utilisé pour demander du contenu depuis l’appareil. | 31
Nom du modèle | %{wurfl_cap_model_name} | Chaîne qui indique le nom du modèle de l’appareil. | s3
Téléchargement progressif | %{wurfl_cap_progressive_download} | Valeur booléenne qui indique si l’appareil prend en charge la lecture d’un fichier audio/vidéo pendant qu’il est toujours en cours de téléchargement. | true
Date de lancement | %{wurfl_cap_release_date} | Chaîne qui indique l’année et le mois de l’ajout de l’appareil à la base de données WURFL.<br/><br/>Format : `yyyy_mm` | 2013_december
Hauteur de résolution | %{wurfl_cap_resolution_height} | Entier qui indique la hauteur de l’appareil en pixels. | 768
Largeur de résolution | %{wurfl_cap_resolution_width} | Entier qui indique la largeur de l’appareil en pixels. | 1 024


## <a name="location"></a>Lieu

Ces conditions de correspondance sont conçues pour identifier les requêtes selon l’emplacement du demandeur.

Nom | Objectif
-----|--------
Numéro AS | Identifie les requêtes issues d’un réseau particulier.
Pays | Identifie les requêtes provenant des pays spécifiés.


## <a name="origin"></a>Origine

Ces conditions de correspondance sont conçues pour identifier les requêtes qui pointent vers le stockage CDN ou le serveur d’origine d’un client.

Nom | Objectif
-----|--------
Origine CDN | Identifie les requêtes de contenu stocké sur le stockage CDN.
Origine client | Identifie les requêtes de contenu stocké sur le serveur d’origine d’un client spécifique.


## <a name="request"></a>Demande

Ces conditions de correspondance sont conçues pour identifier les requêtes selon leurs propriétés.

Nom | Objectif
-----|--------
Adresse IP du client | Identifie les requêtes issues d’une adresse IP particulière.
Paramètre de cookie | Recherche la valeur spécifiée dans les cookies associés à chaque requête.
Expression régulière de paramètre de cookie | Recherche l’expression régulière spécifiée dans les cookies associés à chaque requête.
Cname Edge | Identifie les requêtes qui pointent vers un CNAME de périphérie spécifique.
Domaine de référence | Identifie les requêtes qui ont été référencées à partir des noms d’hôte spécifiés.
Littéral d’en-tête de requête | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur spécifiée.
Expression régulière d’en-tête de requête | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
Caractère générique d’en-tête de requête | Identifie les requêtes qui contiennent l’en-tête spécifié défini sur une valeur qui correspond au modèle spécifié.
Méthode de requête | Identifie les requêtes par leur méthode HTTP.
Modèle de requête | Identifie les requêtes par leur protocole HTTP.

## <a name="url"></a>URL

Ces conditions de correspondance sont conçues pour identifier les requêtes selon leurs URL.

Nom | Objectif
-----|--------
Répertoire du chemin d’URL | Identifie les requêtes par leur chemin relatif.
Extension du chemin d’URL | Identifie les requêtes par leur extension de nom de fichier.
Nom de fichier du chemin d’URL | Identifie les requêtes par leur nom de fichier.
Littéral du chemin d’URL | Compare le chemin relatif d’une requête à la valeur spécifiée.
Expression régulière du chemin d’URL | Compare le chemin relatif d’une requête à l’expression régulière spécifiée.
Caractère générique du chemin d’URL | Compare le chemin relatif d’une requête au modèle spécifié.
Littéral de requête d’URL | Compare la chaîne de requête d’une requête à la valeur spécifiée.
Paramètre de requête d’URL | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond au modèle spécifié.
Expression régulière de requête d’URL | Identifie les requêtes qui contiennent le paramètre de chaîne de requête spécifié défini sur une valeur qui correspond à l’expression régulière spécifiée.
Caractère générique de requête d’URL | Compare les valeurs spécifiées à la chaîne de requête de la requête.


## <a name="next-steps"></a>Étapes suivantes
* [Vue d'ensemble d'Azure CDN](cdn-overview.md)
* [Référence du moteur de règles](cdn-rules-engine-reference.md)
* [Expressions conditionnelles du moteur de règles](cdn-rules-engine-reference-conditional-expressions.md)
* [Fonctionnalités du moteur de règles](cdn-rules-engine-reference-features.md)
* [Remplacement du comportement HTTP par défaut à l’aide du moteur de règles](cdn-rules-engine.md)

