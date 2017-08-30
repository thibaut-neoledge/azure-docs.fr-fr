---
title: "Sécurité de conteneurs Azure Service Fabric | Microsoft Docs"
description: "En savoir plus sur les services de conteneurs sécurisés."
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
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: a262730aec6ce5a1c6f3b7d2d41056a6e6edfbe0
ms.contentlocale: fr-fr
ms.lasthandoff: 08/16/2017

---

# <a name="container-security"></a>Sécurité du conteneur

Service Fabric fournit un mécanisme pour les services à l’intérieur d’un conteneur pour accéder à un certificat installé sur les nœuds dans un cluster Windows ou Linux (version 5.7 ou version ultérieure). En outre,Service Fabric prend également en charge les gMSA (comptes de service géré de groupe) pour les conteneurs Windows. 

## <a name="certificate-management-for-containers"></a>Gestion des certificats pour les conteneurs

Vous pouvez sécuriser vos services de conteneur en spécifiant un certificat. Le certificat doit être installé sur les nœuds du cluster. Les informations de certificat sont fournies dans le manifeste d’application avec la balise `ContainerHostPolicies` comme le montre l’extrait de code suivant :

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Lors du démarrage de l’application, le runtime lit les certificats et génère un fichier PFX ainsi qu’un mot de passe pour chaque certificat. Ce fichier PFX et le mot de passe sont accessibles à l’intérieur du conteneur à l’aide des variables d’environnement suivantes : 

* **Certificate_[CodePackageName]_[CertName]_PFX**
* **Certificate_[CodePackageName]_[CertName]_Password**

Le service de conteneur ou le processus est responsable de l’importation du fichier PFX vers le conteneur. Pour importer le certificat, vous pouvez utiliser les scripts `setupentrypoint.sh` ou exécuter un code personnalisé dans le processus de conteneur. L’exemple de code en C# qui importe le fichier PFX est le suivant :

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
Ce certificat PFX peut être utilisé pour authentifier l’application ou le service ou la communication sécurisée avec d’autres services.


## <a name="set-up-gmsa-for-windows-containers"></a>Configurer le gMSA pour les conteneurs Windows

Pour configurer un gMSA (compte de service géré de groupe), un fichier de spécification des informations d’identification (`credspec`) est placé sur tous les nœuds du cluster. Le fichier peut être copié sur tous les nœuds à l’aide d’une extension de machine virtuelle.  Le fichier `credspec` doit contenir les informations de compte gMSA. Pour plus d’informations sur le fichier `credspec`, consultez la page [Comptes de service](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts). La spécification d’informations d’identification et la balise `Hostname` sont spécifiées dans le manifeste d’application. La balise `Hostname` doit correspondre au nom de compte gMSA dans lequel le conteneur s’exécute.  La balise `Hostname` permet au conteneur de s’authentifier auprès d’autres services dans le domaine à l’aide de l’authentification Kerberos.  Un exemple de spécification des balises `Hostname` et `credspec` du manifeste d’application est indiqué dans l’extrait de code suivant :

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>Étapes suivantes

* [Déployer un conteneur Windows sur Service Fabric sous Windows Server 2016](service-fabric-get-started-containers.md)
* [Déployer un conteneur Docker sur Service Fabric sous Linux](service-fabric-get-started-containers-linux.md)

