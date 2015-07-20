<properties
   pageTitle="Ressources du manifeste de service Service Fabric"
   description="Comment décrire les ressources dans un manifeste de service"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="sumukhs"/>

# Ressources du manifeste de service

## Vue d'ensemble

Le manifeste de service met les ressources à la disposition du service à déclarer/modifier sans changer le code compilé. Service Fabric prend en charge la configuration des ressources des points de terminaison du service. L’accès aux ressources spécifiées dans le manifeste de service peut être contrôlé à l’aide de la valeur « SecurityGroup » du manifeste d’application. La déclaration des ressources permet de les modifier lors du déploiement. Ainsi, vous ne sollicitez pas le service pour l’introduction d’un nouveau mécanisme de configuration.

## Points de terminaison

Lorsqu’une ressource de point de terminaison est définie dans le manifeste de service, Service Fabric alloue les ports de la plage de ports d’application réservés. En outre, les services peuvent également solliciter un port spécifique d’une ressource. Les réplicas de service exécutés sur des nœuds de cluster différents peuvent être alloués à des numéros de ports différents, tandis que les réplicas d’un même service exécutés sur un même nœud partagent le même port. Ces ports peuvent être utilisés par les réplicas de service à différentes fins (réplication, écoute des requêtes des clients, etc.).

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Consultez la page [Configuration des services fiables avec état](../Service-Fabric/service-fabric-reliable-services-configuration.md) pour en savoir plus sur le référencement des points de terminaison du fichier de configuration des paramètres de package (settings.xml).

## Exemple
Le manifeste de service suivant définit une ressource de point de terminaison TCP et deux ressources de point de terminaison HTTP dans l’élément &lt;Ressources&gt;.

Les points de terminaison HTTP sont automatiquement répertoriés sur la liste de contrôle d’accès par Service Fabric.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=July15_HO2-->