<properties
   pageTitle="Sécuriser le trafic de réplication des services avec état dans Azure Service Fabric"
   description="Lorsque la réplication est activée, les services avec état répliquent leur état à partir d'un réplica principal vers des réplicas secondaires et ce trafic doit être protégé contre les écoutes clandestines et les falsifications."
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# Sécuriser le trafic de réplication

Lorsque la réplication est activée, les services avec état répliquent l’état entre les réplicas. Cette page traite de la configuration de la protection de ce type de trafic.

Il existe deux types de paramètres de sécurité pris en charge :

- X509 : utilise le certificat X509 pour sécuriser le canal de communication. Les utilisateurs doivent obtenir un certificat ACL pour les clés privées pour permettre aux processus hôtes Acteur/Service d'être en mesure d'utiliser les informations d'identification du certificat.
- Windows : utilise la sécurité Windows (Active Directory requis) pour sécuriser le canal de communication.

La section suivante montre comment configurer les deux types de paramètres ci-dessus. La configuration « CredentialType » détermine le type utilisé.

## CredentialType=X509

### Noms des configurations

|Nom|Remarques|
|----|-------|
|StoreLocation|Emplacement du magasin pour trouver le certificat : CurrentUser ou LocalMachine|
|StoreName|Nom du magasin pour trouver le certificat|
|FindType|Identifie le type de valeur fournie par le paramètre FindValue : FindBySubjectName ou FindByThumbPrint|
|FindValue|Cible de recherche pour trouver le certificat|
|AllowedCommonNames|Liste séparée par des virgules de noms/noms dns courants de certificats utilisés par les réplicateurs.|
|IssuerThumbprints|Liste séparée par des virgules d'empreintes numériques de certificats de l'émetteur. S'il est spécifié, le certificat entrant est validé s'il est émis par l'une des entrées dans la liste. La validation de la chaîne est toujours effectuée.|
|RemoteCertThumbprints|Liste séparée par des virgules d'empreintes numériques de certificats utilisées par les réplicateurs.|
|ProtectionLevel|Indique comment les données sont protégées : Sign ou EncryptAndSign ou None|

## CredentialType = Windows

### Noms des configurations

|Nom|Remarques|
|----|-------|
|ServicePrincipalName|Nom du service principal enregistré pour le service. Peut être vide si les processus hôtes service/acteur s'exécutent en tant que compte d'ordinateur (par exemple : NetworkService, LocalSystem, etc.)|
|WindowsIdentities|Liste séparée par des virgules d'identités Windows de tous les processus hôtes service/acteur.
|ProtectionLevel|Indique comment les données sont protégées : Sign ou EncryptAndSign ou None|

## Exemples

### Exemple 1 : CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### Exemple 2 : CredentialType=Windows
Cet extrait de code illustre un exemple lorsque tous les processus hôtes service/acteur s'exécutent en tant que NetworkService ou LocalSystem. La valeur ServicePrincipalName est vide.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### Exemple 3 : CredentialType=Windows
Cet extrait de code illustre un exemple lorsque tous les processus hôtes service/acteur s'exécutent en tant que compte de service géré par un groupe avec une valeur ServicePrincipalName valide.

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=July15_HO4-->