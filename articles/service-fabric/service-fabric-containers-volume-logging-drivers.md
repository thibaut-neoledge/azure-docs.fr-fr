---
title: "Version préliminaire de Docker Compose dans Azure Service Fabric | Microsoft Docs"
description: "Azure Service Fabric accepte le format Docker Compose pour vous permettre d’orchestrer facilement des conteneurs existants à l’aide de Service Fabric. Cette prise en charge est actuellement en mode préliminaire."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 868c3051f60c27f15bfd99f66e50b65595951a00
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017

---

# <a name="specifying-volume-plugins-and-logging-drivers-for-your-container"></a>Spécification de plug-ins de volume et de pilotes de journalisation pour votre conteneur

Service Fabric prend en charge la spécification de [plug-ins de volume Docker](https://docs.docker.com/engine/extend/plugins_volume/) et de [pilotes de journalisation de Docker](https://docs.docker.com/engine/admin/logging/overview/) pour votre service de conteneur. Les plug-ins sont spécifiés dans le manifeste de l’application, comme indiqué dans le manifeste suivant :


```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myexternalvolume" Destination="c:\testmountlocation3" Driver="sf" IsReadOnly="true"></Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

Dans l’exemple précédent, l’étiquette `Source` pour le `Volume` fait référence au dossier source. Le dossier source peut être un dossier de la machine virtuelle qui héberge les conteneurs ou un magasin distant persistant. L’étiquette `Destination` est l’emplacement de mappage de la `Source` dans le conteneur en cours d’exécution. Quand vous utilisez un plug-in de volume, le nom du plug-in (étiquette `Driver`) est spécifié comme indiqué dans l’exemple précédent.  Si un pilote de journalisation Docker est spécifié, il est nécessaire de déployer des agents (ou conteneurs) pour gérer les journaux dans le cluster. 

Consultez les articles suivants pour déployer des conteneurs dans un cluster Service Fabric :

[Déployer un conteneur Windows sur Service Fabric sous Windows Server 2016](service-fabric-deploy-container.md)

[Déployer un conteneur Docker sur Service Fabric sous Linux](service-fabric-deploy-container-linux.md)


