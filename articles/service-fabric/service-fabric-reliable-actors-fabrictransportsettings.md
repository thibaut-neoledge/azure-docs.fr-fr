---
title: "Modifier les paramètres de Fabric Transport dans les microservices Azure | Microsoft Docs"
description: "Découvrez comment configurer les paramètres de communication d’un intervenant Azure Service Fabric."
services: Service-Fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: 
ms.assetid: dbed72f4-dda5-4287-bd56-da492710cd96
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: suchia
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 4cbca6e496135a312bf4704dd0989f45dcccfc00
ms.lasthandoff: 04/14/2017


---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>Configuration des paramètres de FabricTransport pour Reliable Actors

Voici les paramètres que vous pouvez configurer :

- C# : [FabricTansportSettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)
- Java : [FabricTransportRemotingSettings](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport._fabric_transport_remoting_settings)

Vous pouvez modifier la configuration par défaut de FabricTransport des manières suivantes.

## <a name="assembly-attribute"></a>Attribut d’assembly

L’attribut [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute) doit être appliqué au niveau des assemblys du client et du service de l’intervenant.

L’exemple suivant montre comment modifier la valeur par défaut des paramètres de FabricTransport OperationTimeout :

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

L’exemple suivant montre comment modifier les valeurs par défaut de FabricTransport MaxMessageSize et OperationTimeoutInSeconds :

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Package de configuration

Vous pouvez utiliser un [package de configuration](service-fabric-application-model.md) pour modifier la configuration par défaut.

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>Configurer les paramètres de FabricTransport pour le service de l’intervenant

Ajoutez une section TransportSettings dans le fichier settings.xml.

Par défaut, le code de l’intervenant cherche SectionName en tant que « &lt;ActorName&gt;TransportSettings ». S’il est introuvable, il recherche SectionName en tant que « TransportSettings ».

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>Configurer les paramètres de FabricTransport pour l’assembly du client de l’intervenant

Si le client n’est pas exécuté dans le cadre d’un service, vous pouvez créer un fichier « &lt;Nom de l’exe du client&gt;.settings.xml » au même endroit que le fichier .exe du client. Ajoutez ensuite une section TransportSettings à ce fichier. SectionName doit être « TransportSettings ».

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

