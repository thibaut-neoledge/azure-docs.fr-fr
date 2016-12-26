---
title: "Vue d’ensemble de la configuration de FabricTransport d’Azure Service Fabric Reliable Actors | Microsoft Docs"
description: "Découvrez comment configurer les paramètres de communication d’Azure Service Fabric Actor."
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
ms.sourcegitcommit: a395d0653a72883b1901824ba25d40f673276d3f
ms.openlocfilehash: 0b43ba67ea30772054c513b47c31642baaa8b9e7


---
# <a name="configuring-fabrictransport-settings-for-reliable-actors"></a>Configuration des paramètres de FabricTransport pour Reliable Actors

Voici la liste des paramètres que l’utilisateur peut configurer [FabrictTansportSettings](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.fabrictransport.common.fabrictransportsettings)

Vous pouvez modifier la configuration par défaut de FabricTransport des manières suivantes.

1.  À l’aide de l’attribut d’assembly - [FabricTransportActorRemotingProvider](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN#microsoft_servicefabric_actors_remoting_fabrictransport_fabrictransportactorremotingproviderattribute).

  Cet attribut doit être appliqué sur le client Actor et l’assembly du service Actor.
  L’exemple suivant montre comment modifier la valeur par défaut des paramètres de délai d’expiration des opérations de FabricTransport.

  ```csharp
     using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   Le deuxième exemple modifie les valeurs par défaut de FabricTransport MaxMessageSize et OperationTimeoutInSeconds

    ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
    ```

2. À l’aide du [package de configuration](service-fabric-application-model.md), vous pouvez :

  * Configurer les paramètres de FabricTransport pour le service Actor

    Ajoutez une section TransportSettings dans le fichier settings.xml.

    * SectionName : par défaut, le code de l’acteur cherche SectionName en tant que « &lt;ActorName&gt;TransportSettings. » S’il est introuvable, il recherche sectionName en tant que « TransportSettings ».

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

  * Configurer les paramètres de FabricTransport pour l’assembly du client Actor

    Si le client n’est pas exécuté dans le cadre d’un service, vous pouvez créer un fichier xml « &lt;Nom de l’exe du client&gt; ».settings.xml au même endroit que le .exe du client. Ajoutez ensuite une section TransportSettings à ce fichier. SectionName doit être « TransportSettings ».

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



<!--HONumber=Nov16_HO4-->


