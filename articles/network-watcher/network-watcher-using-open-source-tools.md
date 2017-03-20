---
title: "Visualiser les modèles de trafic réseau à l’aide d’Azure Network Watcher et d’outils open source | Microsoft Docs"
description: "Cette page explique comment utiliser la capture de paquets Network Watcher avec Capanalysis pour visualiser les modèles de trafic depuis et vers les machines virtuelles."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 05fb0b7e80684737277626c12468ca9c754a0ac9
ms.openlocfilehash: e27bb694d0cbcf1ff7c9d8ca4682a79c8b5c5cb1
ms.lasthandoff: 02/22/2017

---

# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Visualiser les modèles de trafic réseau depuis et vers les machines virtuelles à l’aide d’outils open source

Les captures de paquets contiennent des données réseau qui vous permettent d’effectuer des analyses réseau et une inspection approfondie des paquets. Il existe de nombreux outils open source que vous pouvez utiliser pour analyser des captures de paquets et obtenir des informations sur le réseau. L’un de ces outils est CapAnalysis, un outil de visualisation de capture de paquets open source. La visualisation des données de capture de paquets est un moyen efficace de trouver des informations sur les modèles et anomalies au sein de votre réseau. Les visualisations fournissent également un moyen de partager ces informations d’une manière facilement consommable.

Network Watcher d’Azure vous donne les moyens de capturer ces données précieuses en vous permettant d’effectuer des captures de paquets sur le réseau. Dans cet article, nous vous indiquons la procédure pour visualiser et exploiter les informations issues des captures de paquets en utilisant CapAnalysis avec Network Watcher.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Scénario

Vous disposez d’une application web simple déployée sur une machine virtuelle dans Azure et vous voulez utiliser les outils open source pour visualiser son trafic réseau et identifier rapidement les modèles de flux et les anomalies potentielles. Avec Network Watcher, vous pouvez obtenir une capture de paquets de votre environnement réseau et la stocker directement dans votre compte de stockage. CapAnalysis peut ensuite ingérer la capture de paquets directement à partir de l’objet blob de stockage, puis visualiser son contenu.

![scénario][1]

## <a name="steps"></a>Étapes

### <a name="install-capanalysis"></a>Installation de CapAnalysis

Pour installer CapAnalysis sur une machine virtuelle, vous pouvez consulter les instructions officielles ici https://www.capanalysis.net/ca/how-to-install-capanalysis.
Pour accéder à CapAnalysis à distance, nous devons ouvrir le port 9877 sur votre machine virtuelle en ajoutant une nouvelle règle de sécurité entrante. Pour plus d’informations sur la création de règles dans les groupes de sécurité réseau, reportez-vous à [Création de règles dans un NSG existant](../virtual-network/virtual-networks-create-nsg-arm-pportal.md#create-rules-in-an-existing-nsg). Une fois la règle correctement ajoutée, vous devez pouvoir accéder à CapAnalysis à partir de `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Utilisation d’Azure Network Watcher pour démarrer une session de capture de paquets

Network Watcher vous permet de capturer les paquets pour suivre le trafic entrant et sortant d’une machine virtuelle. Vous pouvez vous reporter aux instructions de l’article [Manage packet captures with Network Watcher](network-watcher-packet-capture-manage-portal.md) (Gérer les captures de paquets avec Network Watcher) pour démarrer une session de capture de paquets. Cette capture de paquets peut être stockée dans un objet blob de stockage accessible par CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Chargement d’une capture de paquets vers CapAnalysis
Vous pouvez directement charger une capture de paquets effectuée par Network Watcher à l’aide de l’onglet « Importer à partir d’une URL » en fournissant un lien vers l’objet blob de stockage contenant la capture de paquets.

Lorsque vous fournissez un lien vers CapAnalysis, veillez à ajouter un jeton SAP à l’URL de l’objet blob de stockage.  Pour ce faire, accédez à la signature d’accès partagé à partir du compte de stockage, désignez les autorisations accordées et appuyez sur le bouton Générer une signature d’accès partagé pour créer un jeton. Vous pouvez ensuite ajouter ce jeton SAP à l’URL de l’objet blob de stockage de la capture de paquets.

L’URL doit ressembler à ceci : http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analyse des captures de paquets

CapAnalysis propose différentes options pour visualiser la capture de paquets. Chacune fournit une analyse sous un angle différent. Ces synthèses visuelles vous permettent de comprendre les tendances du trafic réseau et d’identifier rapidement les activités inhabituelles. La liste suivante montre quelques-unes de ces fonctionnalités :

1. Tables des flux

    Cette table fournit la liste des flux dans les données du paquet, l’horodatage associé aux flux et les différents protocoles associés à chaque flux, ainsi que les IP source et de destination.

    ![page de flux capanalysis][5]

1. Vue d’ensemble du protocole

    Ce volet vous permet de visualiser rapidement la répartition du trafic réseau sur les différents protocoles et zones géographiques.

    ![vue d’ensemble du protocole capanalysis][6]

1. Statistiques

    Ce volet vous permet de consulter les statistiques du trafic réseau : octets envoyés et reçus à partir des IP source et de destination, flux de chaque IP source et de destination, protocole utilisé pour les différents flux et durée des flux.

    ![statistiques de capanalysis][7]

1. Carte des zones géographiques

    Ce volet vous fournit une vue cartographique de votre trafic réseau, avec des couleurs correspondant au volume de trafic de chaque pays. Vous pouvez sélectionner les pays en surbrillance pour afficher des statistiques de flux supplémentaires, telles que la proportion de données envoyées et reçues à partir des IP dans ce pays.

    ![carte des zones géographiques][8]

1. Filtres

    CapAnalysis fournit un ensemble de filtres pour l’analyse rapide de paquets spécifiques. Vous pouvez par exemple choisir de filtrer les données par protocole pour obtenir des informations spécifiques sur ce sous-ensemble de trafic.

    ![filtres][11]

    Visitez [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) pour en savoir plus sur l’ensemble des fonctionnalités de CapAnalysis.

## <a name="conclusion"></a>Conclusion

La fonctionnalité de capture de paquets de Network Watcher vous permet de capturer les données nécessaires pour effectuer des analyses réseau et mieux comprendre le trafic réseau. Dans ce scénario, nous vous avons montré comment intégrer facilement les captures de paquets de Network Watcher avec des outils de visualisation open source. À l’aide d’outils open source permettant de visualiser les captures de paquets comme CapAnalysis, vous pouvez effectuer une analyse approfondie des paquets et identifier rapidement les tendances de votre trafic réseau.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les journaux de flux NSG, consultez l’article sur les [journaux de flux NSG](network-watcher-nsg-flow-logging-overview.md).

Découvrez comment visualiser vos journaux de flux de groupe de sécurité réseau avec Power BI en consultant la page [Visualizing Network Security Group flow logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualisation des journaux de flux de groupe de sécurité réseau avec Power BI).
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png

