<properties 
	pageTitle="Azure Cloud Services - Définition de service et configuration de service - Certificat XML" 
	description="Découvrez comment configurer un certificat dans les fichiers de définition et de configuration de service." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015"
	ms.author="adegeo"/>



# Paramétrage de la définition et de la configuration de service d'un certificat

Les ordinateurs virtuels qui exécutent des rôles Web ou des rôles de travail peuvent avoir des certificats qui leur sont associés. Une fois que vous avez chargé votre certificat dans le portail, vous devez configurer les fichiers de définition de service (.csdef) et de configuration de service (.cscfg) du certificat.

L’ordinateur virtuel peut accéder à la clé privée du certificat après l’installation de ce dernier. Pour cette raison, vous pouvez tout à fait limiter l'accès aux processus par le biais d’autorisations élevées.

## Exemple de fichier de définition

Voici un exemple de certificat défini dans la définition de service.

```xml
<ServiceDefinition name="WindowsAzureProject4" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="MyWokerRole"> <!-- or <WebRole name="MyWebRole" vmsize="Small"> -->
    <ConfigurationSettings>
      ...
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="MySSLCert" storeLocation="LocalMachine" storeName="My" permissionLevel="elevated" />
    </Certificates>
  </WorkerRole>
</ServiceDefinition>
```

### Autorisations
Les autorisations (attribut `permisionLevel`) peuvent être définies sur les valeurs suivantes :

| Valeur de l’autorisation | Description |
| ----------------  | ----------- |
| limitedOrElevated | **(Par défaut)** Tous les processus de rôle peuvent accéder à la clé privée. |
| elevated | Seuls les processus élevés peuvent accéder à la clé privée.|

## Exemple de configuration de service

Voici un exemple de certificat défini dans la configuration de service.

```xml
<Role name="MyWokerRole">
...
    <Certificates>
        <Certificate name="MySSLCert" 
            thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
            thumbprintAlgorithm="sha1" />
    </Certificates>
...
</Role>
```

**Observez** les attributs `name` correspondants.

## Étapes suivantes
Examinez le schéma [Définition de service XML](https://msdn.microsoft.com/library/azure/ee758711.aspx) et le schéma [Configuration de service XML](https://msdn.microsoft.com/library/azure/ee758710.aspx).

<!---HONumber=August15_HO6-->