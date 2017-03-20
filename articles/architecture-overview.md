---
title: "Architecture d’application sur Microsoft Azure | Microsoft Docs"
description: Architecture overview that covers common design patterns
services: 
documentationcenter: 
author: rboucher
manager: carmonm
editor: 
ms.assetid: 3a37bbc0-f624-46f3-bc4e-5a10560f9fbf
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: b463c363f131c3d75bbab229fe9f14495b5f3b95
ms.openlocfilehash: d2a13d5c80513a6fe80979bb97368b269a24a883
ms.lasthandoff: 03/01/2017


---
# <a name="application-architecture-on-microsoft-azure"></a>Architecture d’application sur Microsoft Azure
Cet article répertorie les ressources pour la création d’applications basées sur Microsoft Azure. Il inclut des outils pour dessiner et décrire visuellement les systèmes logiciels.

## <a name="design-patterns-poster"></a>Poster Modèles de conception
Microsoft Patterns and Practices a publié le livre [Modèles de conception de cloud](http://msdn.microsoft.com/library/dn568099.aspx), disponible à la fois sur MSDN et en téléchargement au format PDF. Il existe également une affiche grand format qui énumère visuellement tous les modèles.

![Poster des modèles cloud de Patterns and Practices](./media/architecture-overview/PnPPatternPosterThumb.jpg)


## <a name="drawing-symbol-and-icon-sets"></a>Jeux de symboles et d'icônes graphiques
[Visionnez la vidéo de formation sur Visio et les symboles](http://aka.ms/CnESymbolsVideo), puis [téléchargez le jeu de symboles Cloud et Enterprise](http://aka.ms/CnESymbols) pour créer des documents techniques qui décrivent Azure, Windows Server, SQL Server, etc. Vous pouvez utiliser les symboles dans les éléments suivants gratuitement ou de manière lucrative.  
- diagrammes architecturaux 
- supports de formation 
- présentations 
- feuilles de données 
- infographie 
- livres blancs 
- livres tiers, s’ils forment à l’utilisation des produits Microsoft.

Les symboles ne sont pas destinés à être utilisés dans les interfaces utilisateur, mais vous pouvez en demander l’autorisation.  Les symboles Azures peuvent être utilisés pour représenter les objets de la même façon que dans le portail Azure.  Par exemple, le symbole d’Azure Service Bus doit être utilisé pour représenter uniquement les objets Azure Service Bus et y accéder. Les symboles tiers ne sont pas possédés par Microsoft. Regardez sur les sites web de ces sociétés pour connaître les règles d’utilisation de leurs icônes.

Les symboles CnE sont aux formats Visio, SVG et PNG. Vous trouverez des instructions supplémentaires d’utilisation des symboles dans PowerPoint. Le jeu de symboles est fourni tous les trimestres et est mis à jour à chaque publication de nouveaux services.

D’autres symboles utilisables avec Microsoft Office et des technologies associées sont disponibles dans le [gabarit Microsoft Office Visio](http://www.microsoft.com/en-us/download/details.aspx?id=35772), même s’ils ne sont pas optimisés pour des plans d’architecture comme le jeu CnE.   

Envoyez-nous un e-mail à [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) pour poser des questions spécifiques et nous adresser vos commentaires. Nous aimerions connaître votre avis, y compris les commentaires positifs, afin de décider de continuer dans cette voie ou non.

![Jeu de symboles/icônes Cloud et Enterprise](./media/architecture-overview/CnESymbols.png)

## <a name="microsoft-architecture-certification-course"></a>Cours de certification d'architecture Microsoft
Microsoft a créé un cours d’architecture en août 2015 prenant en charge l’examen de certification Microsoft 70-534. Il est [disponible gratuitement sur EDX.ORG](https://www.edx.org/course/architecting-microsoft-azure-solutions-microsoft-dev205x).  Il utilise le [modèle&3;D Blueprint Visio](#3d-blueprint-visio-template).

![Cours de certification d'architecture Microsoft](./media/architecture-overview/EDXCourse.png)

## <a name="microsoft-solutions"></a>Solutions Microsoft
Microsoft publie un ensemble [d’architectures de solution](http://aka.ms/azblueprints) de haut niveau qui montre comment créer des types spécifiques de système à l’aide des produits Microsoft.

Microsoft a publié précédemment un ensemble de plans 3D isométriques présentant des exemples d’architecture. Ces plans ont été remplacés par les architectures de solution. Le lien du plan a été redirigé pour pointer vers les solutions. Si vous devez accéder aux plans précédents pour quelque raison que ce soit, envoyez votre demande par e-mail à l’adresse [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com).   

Les plans et schémas d’architectures de solution utilisent des parties du [Jeu de symboles Cloud et Enterprise](http://aka.ms/CnESymbols).   

![Diagrammes 3D des plans d’architecture Microsoft.](./media/architecture-overview/BluePrintThumb.jpg)

## <a name="3d-blueprint-visio-template"></a>modèle&3;D Blueprint Visio
Les versions 3D des [plans d’architecture Microsoft](http://aka.ms/azblueprints) (qui n’existent plus) ont initialement été créées avec un outil autre que Microsoft. Un modèle Visio 2013 (et versions ultérieures) a été publié le 5 août 2015 dans le cadre d’un [cours de certification d’architecture Microsoft distribué sur EDX.ORG](#microsoft-architecture-certification-course).

Le modèle est également disponible en dehors du cours.

* [visionner la vidéo de formation](http://aka.ms/3dBlueprintTemplateVideo) afin de savoir ce qu'il peut faire.   
* Téléchargez le [modèle Microsoft 3D Blueprint Visio](http://aka.ms/3DBlueprintTemplate)
* Téléchargez le [jeu de symboles Cloud et Enterprise](https://www.microsoft.com/en-us/download/details.aspx?id=41937) à utiliser avec le modèle 3D. 

Envoyez-nous un message à l'adresse [CnESymbols@microsoft.com](mailto:CnESymbols@microsoft.com) si vous avez des questions spécifiques auxquelles les supports de formation n'apportent pas de réponse, ou pour transmettre vos commentaires. Le modèle n’est plus en cours de développement actif, mais il reste utile et pertinent, car il peut utiliser n’importe quel format PNG ou [jeu de symboles Cloud et Enterprise](http://aka.ms/CnESymbols) mis à jour.  

![modèle Microsoft 3D Blueprint Visio](./media/architecture-overview/3DBlueprintVisioTemplate.jpg)

## <a name="architecture-infographics"></a>Éléments d'infographie relatifs à l'architecture
Microsoft publie plusieurs affiches/éléments d’infographie relatifs à l’architecture. Cela inclut la [création d’applications cloud réalistes](https://azure.microsoft.com/documentation/infographics/building-real-world-cloud-apps/) et leur [mise à l’échelle à l’aide de services Cloud](https://azure.microsoft.com/documentation/infographics/cloud-services/).

![Éléments d’infographie relatifs à l’architecture Azure](./media/architecture-overview/AzureArchInfographicThumb.jpg)

