---
title: "Schéma de définition d’Azure Cloud Services (fichier .cscfg) | Microsoft Docs"
ms.custom: 
ms.date: 12/07/2016
ms.prod: azure
ms.reviewer: 
ms.service: cloud-services
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: "35"
author: thraka
ms.author: adegeo
manager: timlt
ms.openlocfilehash: bba02688e443d4b5bde89691ca0b74b3597b453a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Schéma de définition d’Azure Cloud Services (fichier .cscfg)
Le fichier de configuration de service spécifie le nombre d’instances de rôle à déployer pour chaque rôle dans le service, les valeurs des paramètres de configuration et les empreintes des certificats associés à un rôle. Si le service fait partie d’un réseau virtuel, les informations de configuration pour le réseau doivent être fournies dans le fichier de configuration de service, ainsi que dans le fichier de configuration de réseau virtuel. L’extension par défaut du fichier de configuration de service est .cscfg.

Le modèle de service est décrit par le [Schéma de définition de services cloud (classique)](schema-csdef-file.md).

Par défaut, le fichier de schéma de configuration Azure Diagnostics est installé dans le répertoire `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Remplacez `<version>` par la version installée du [Kit de développement logiciel (SDK) Azure](https://azure.microsoft.com/downloads/).

Pour plus d’informations sur la configuration des rôles dans un service, consultez [Qu’est-ce que le modèle Cloud Service ?](cloud-services-model-and-package.md)

## <a name="basic-service-configuration-schema"></a>Schéma de configuration de service de base
Le format de base du fichier de configuration de service se présente comme suit.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Définitions du schéma
Les rubriques suivantes décrivent le schéma pour l’élément `ServiceConfiguration` :

- [Schéma Role](schema-cscfg-role.md)
- [Schéma NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Espace de noms de configuration de service
L’espace de noms XML du fichier de configuration de service est : `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> Élément ServiceConfiguration
L’élément `ServiceConfiguration` est l’élément de niveau supérieur du fichier de configuration de service.

Le tableau suivant décrit les attributs de l’élément `ServiceConfiguration`. Toutes les valeurs d’attribut sont des types de chaîne.

| Attribut | Description |
| --------- | ----------- |
|serviceName|Obligatoire. Nom du service cloud. Le nom indiqué ici doit correspondre au nom spécifié dans le fichier de définition de service.|
|osFamily|facultatif. Spécifie le système d’exploitation invité qui s’exécutera sur les instances de rôle dans le service cloud. Pour plus d’informations sur les versions du système d'exploitation invité prises en charge, consultez [Versions du système d'exploitation invité d'Azure et matrice de compatibilité du Kit de développement logiciel (SDK)](cloud-services-guestos-update-matrix.md).<br /><br /> Si vous n’incluez pas de valeur `osFamily` et n’avez pas défini l’attribut `osVersion` sur une version de système d’exploitation invité spécifique, la valeur par défaut utilisée est 1.|
|osVersion|facultatif. Spécifie la version du système d’exploitation invité qui s’exécutera sur les instances de rôle dans le service cloud. Pour plus d’informations sur les versions du système d'exploitation invité, consultez [Versions du système d'exploitation invité d'Azure et matrice de compatibilité du Kit de développement logiciel (SDK)](cloud-services-guestos-update-matrix.md).<br /><br /> Vous pouvez spécifier que le système d’exploitation invité doit être automatiquement mis à niveau vers la dernière version. Pour ce faire, définissez la valeur de l’attribut `osVersion` sur `*`. Quand cette valeur est définie sur `*`, les instances de rôle sont déployées à l’aide de la dernière version du système d’exploitation invité pour la famille de système d’exploitation spécifiée, et elles seront automatiquement mises à niveau dès que de nouvelles versions du système d’exploitation invité seront disponibles.<br /><br /> Pour indiquer une version spécifique manuellement, utilisez la colonne `Configuration String` du tableau de la section relative aux **versions de SE invité futures, actuelles et de transition** de la page [Versions du SE invité et matrice de compatibilité du Kit de développement logiciel (SDK) Azure](cloud-services-guestos-update-matrix.md).<br /><br /> La valeur par défaut de l’attribut `osVersion` est `*`.|
|schemaVersion|facultatif. Spécifie la version du schéma de configuration de service. La version du schéma permet à Visual Studio de sélectionner les outils du Kit de développement logiciel (SDK) appropriés à utiliser pour la validation du schéma si plusieurs versions du Kit SDK sont installées côte à côte. Pour plus d’informations sur la compatibilité des schémas et des versions, consultez [Versions du système d'exploitation invité d'Azure et matrice de compatibilité du Kit de développement logiciel (SDK)](cloud-services-guestos-update-matrix.md).|

Le fichier de configuration de service doit contenir un élément `ServiceConfiguration`. L’élément `ServiceConfiguration` peut inclure un nombre quelconque d’éléments `Role` et zéro ou 1 élément `NetworkConfiguration`.