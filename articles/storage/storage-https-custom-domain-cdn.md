---
title: "Utilisation du CDN Azure pour accéder aux objets blob avec des domaines personnalisés via HTTPs"
description: "Découvrez comment intégrer le CDN Azure avec le stockage d’objets blob pour accéder aux objets blob avec des domaines personnalisés via HTTPS"
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: mihauss
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 1439198250346ae9484eae448489e8a5de4734b7
ms.contentlocale: fr-fr
ms.lasthandoff: 05/05/2017

---
# <a name="using-the-azure-cdn-to-access-blobs-with-custom-domains-over-https"></a>Utilisation du CDN Azure pour accéder aux objets blob avec des domaines personnalisés via HTTPs

Le réseau de distribution de contenu (CDN) Azure prend désormais en charge le protocole HTTPS pour les noms de domaine personnalisés.
Vous pouvez utiliser cette fonctionnalité pour accéder aux objets blob de stockage à l’aide de votre domaine personnalisé via HTTPS. Pour ce faire, vous devez tout d’abord activer le CDN Azure sur le point de terminaison de votre objet blob et mapper le CDN à un nom de domaine personnalisé. Une fois cette procédure effectuée, l’activation du protocole HTTPS pour votre domaine personnalisé est simplifiée grâce à l’activation en un clic et la gestion complète des certificats, le tout sans coût supplémentaire par rapport au tarif CDN normal.

Ces une option importante, car elle permet de protéger la confidentialité et l’intégrité des données de vos données d’application web sensibles lors de leur transit. L’utilisation du protocole SSL pour assurer le trafic via HTTPS garantit que vos données sont chiffrées lorsqu’elles sont envoyées sur Internet. Le protocole HTTPS assure la confiance et l’authentification, et protège également vos applications web contre les attaques.

> [!NOTE]
> Outre la prise en charge SSL pour les noms de domaine personnalisés, le CDN Azure peut vous aider à faire évoluer votre application pour fournir du contenu de bande passante élevée dans le monde entier.
> Pour plus d’informations, consultez [Vue d’ensemble du CDN Azure](../cdn/cdn-overview.md).
>
>

## <a name="quick-start"></a>Démarrage rapide

Voici les étapes requises pour activer le protocole HTTPS pour le point de terminaison du stockage d’objets blob personnalisé :

1.  [Intégrer un compte de stockage Azure au CDN Azure](../cdn/cdn-create-a-storage-account-with-cdn.md).
    Cet article vous guide dans la création d’un compte de stockage dans le portail Azure si vous ne l’avez pas déjà fait.
2.  [Mapper du contenu CDN Azure avec un domaine personnalisé](../cdn/cdn-map-content-to-custom-domain.md).
3.  [Activer le protocole HTTPS sur un domaine personnalisé CDN Azure](../cdn/cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Les signatures d’accès partagé

Si votre point de terminaison de stockage d’objets blob est configuré pour interdire l’accès anonyme en lecture, vous devrez fournir un jeton de [Signature d’accès partagé (SAP)](storage-dotnet-shared-access-signature-part-1.md) dans chaque demande d’émission effectuée vis-à-vis de votre domaine personnalisé. Par défaut, les points de terminaison de stockage d’objets blob interdisent l’accès en lecture anonyme. Consultez [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](storage-manage-access-to-resources.md) pour en savoir plus sur les signatures d’accès partagé.

CDN Azure ne respecte pas les restrictions ajoutées au jeton SAP. Par exemple, tous les jetons SAP ont un délai d’expiration. Cela signifie que le contenu peut rester accessible même si la SAP est expirée, jusqu’à ce que ce contenu soit éliminé des nœuds de périphérie du CDN. Vous pouvez contrôler la durée pendant laquelle données mises en cache sur le CDN en définissant l’en-tête de réponse du cache. Consultez la rubrique [Gérer l’expiration des objets blob de stockage Azure dans CDN Azure](../cdn/cdn-manage-expiration-of-blob-content.md) pour plus d’informations.

Si vous créez plusieurs URL de SAP pour le même point de terminaison d’objets blob, nous vous recommandons d’activer la chaîne de requête mise en cache pour votre CDN Azure. Cela permet de garantir que chaque URL est traitée comme une entité unique. Pour plus d’informations, consultez [Contrôle du comportement de mise en cache du CDN Azure avec des chaînes de requête](../cdn/cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Redirection HTTP vers HTTPS

Vous pouvez choisir de rediriger le trafic HTTP vers HTTPS. Cela nécessite l’utilisation de l’offre CDN Azure premium de Verizon. Vous devez [remplacer le comportement HTTP à l’aide du moteur de règles CDN Azure](../cdn/cdn-rules-engine.md) par la règle suivante :

![](./media/storage-https-custom-domain-cdn/redirect-to-https.png)

« Cdn-endpoint-name » fait référence au nom que vous avez configuré pour votre point de terminaison CDN. Vous pouvez sélectionner cette valeur dans la liste déroulante. « Origin-path » désigne le chemin d’accès au sein de votre compte de stockage d’origine dans lequel réside votre contenu statique.
Si vous hébergez tout le contenu statique dans un seul conteneur, remplacez « origine-path » par le nom de ce conteneur.

Pour approfondir vos connaissances des règles, consultez les [fonctionnalités du moteur de règles de CDN Azure](../cdn/cdn-rules-engine-reference-features.md).

## <a name="pricing-and-billing"></a>Tarification et facturation

Lorsque vous accédez à des objets blob via un CDN Azure, vous payez [le prix du stockage d’objets Blob](https://azure.microsoft.com/pricing/details/storage/blobs/) pour le trafic entre les nœuds de périphérie et l’origine (stockage d’objets Blob), et [les prix CDN](https://azure.microsoft.com/pricing/details/cdn/) pour l’accès aux données à partir des nœuds de périphérie.

Par exemple, supposons que vous avez un compte de stockage dans l’ouest des États-Unis qui est accessible via un CDN Azure. Si une personne au Royaume-Uni tente d’accéder à un des objets blob de ce compte de stockage via le CDN, Azure vérifie d’abord le nœud de périphérie le plus proche du Royaume-Uni pour cet objet blob. S’il est trouvé, il accède à cette copie de l’objet blob et utilise la tarification CDN, car l’accès se fait via le CDN. Si ce n’est pas le cas, Azure copie l’objet blob vers le nœud de périphérie, ce qui entraîne des frais de sortie et de transaction tels que spécifiés dans la tarification du stockage d’objets Blob, et accède ensuite au fichier sur le nœud de périphérie, ce qui entraîne la facturation du CDN.

Lorsque vous examinez la [page de tarification de CDN](https://azure.microsoft.com/pricing/details/cdn/), notez que la prise en charge HTTPS pour les noms de domaine personnalisés est uniquement disponible pour le CDN Azure des produits Verizon (Standard ou Premium).

## <a name="next-steps"></a>Étapes suivantes

[Configurer un nom de domaine personnalisé pour un point de terminaison de stockage Blob](storage-custom-domain-name.md)

