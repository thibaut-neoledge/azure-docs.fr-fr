---
title: "Erreurs de nom de ressource réservé Azure | Microsoft Docs"
description: "Explique comment résoudre les erreurs quand vous fournissez un nom de ressource qui inclut un mot réservé."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 2f06b7bc65ea309bc5374b41f402f8e7bc4d2a38
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="resolve-reserved-resource-name-errors"></a>Résoudre les erreurs de nom de ressource réservé

Cet article décrit l’erreur que vous rencontrez quand vous déployez une ressource dont le nom inclut un mot réservé.

## <a name="symptom"></a>Symptôme

Au cours du déploiement d’une ressource qui est disponible par le biais d’un point de terminaison public, vous pouvez rencontrer l’erreur suivante :

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Cause :

Le nom d’une ressource qui a un point de terminaison public ne peut pas contenir de mots réservés ou de marques.

Les mots suivants sont réservés :

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

Les mots suivants ne peuvent pas être utilisés en tant que mot entier ou sous-chaîne dans le nom :

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Solution

Fournissez un nom qui n’utilise aucun des mots réservés.