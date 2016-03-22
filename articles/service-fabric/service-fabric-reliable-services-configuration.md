<properties
   pageTitle="Vue d'ensemble de la configuration de Service Fabric Reliable Services Azure | Microsoft Azure"
   description="En savoir plus sur la configuration de Reliable Services avec état dans Azure Service Fabric."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/29/2016"
   ms.author="sumukhs"/>

# Configuration des services fiables (Reliable Services) avec état
Vous pouvez modifier la configuration par défaut des Reliable Services avec état par le biais du package de configuration (Config) ou l'implémentation du service (Code).

+ **Config** : la configuration par le biais du package config est obtenue en modifiant le fichier Settings.xml généré dans la racine du package Microsoft Visual Studio, dans le dossier Config de chaque service dans l'application.
+ **Code** : la configuration par l'intermédiaire du code est obtenue en remplaçant StatefulService.CreateReliableStateManager et en créant un ReliableStateManager à l'aide d'un objet ReliableStateManagerConfiguration avec l'ensemble d'options approprié.

Par défaut, le runtime Azure Service Fabric recherche des noms de sections prédéfinis dans le fichier Settings.xml et utilise les valeurs de configuration pendant la création des composants runtime sous-jacents.

>[AZURE.NOTE] Ne supprimez/modifiez **pas** les noms de sections des configurations suivantes dans le fichier Settings.xml généré dans la solution Visual Studio, sauf si vous envisagez de configurer votre service au moyen du code. Renommer les noms de package ou de la section de configuration nécessite une modification du code lors de la configuration de ReliableStateManager.


## Configuration de la sécurité du réplicateur
Les configurations de sécurité du réplicateur sont utilisées pour sécuriser le canal de communication utilisé lors de la réplication. Cela signifie que les services ne sont pas en mesure d'afficher leur trafic de réplication mutuel, ce qui garantit que les données hautement disponibles soient également sécurisées. Par défaut, une section de configuration de sécurité vide empêche de sécuriser la réplication.

### Nom de la section par défaut
ReplicatorSecurityConfig

>[AZURE.NOTE] Pour renommer ce nom de section, substituez le paramètre replicatorSecuritySectionName avec le constructeur ReliableStateManagerConfiguration lors de la création de la classe ReliableStateManager pour ce service.


## Configuration du réplicateur
Les configurations du réplicateur configurent le réplicateur garantissant la haute fiabilité de l'état du service fiable avec état par la réplication et la conservation de l'état localement. La configuration par défaut est générée par le modèle Visual Studio et devrait suffire. Cette section décrit les configurations supplémentaires disponibles pour paramétrer le réplicateur.

### Nom de la section par défaut
ReplicatorConfig

>[AZURE.NOTE] Pour renommer ce nom de section, substituez le paramètre replicatorSettingsSectionName avec le constructeur ReliableStateManagerConfiguration lors de la création de la classe ReliableStateManager pour ce service.


### Noms des configurations
|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondes|0,05|Durée d'attente du réplicateur secondaire après la réception d'une opération et avant de renvoyer un accusé de réception au réplicateur principal. Tous les autres accusés de réception à envoyer pour les opérations traitées durant cet intervalle sont envoyés sous la forme d'une réponse.|
|ReplicatorEndpoint|N/A|Aucune valeur par défaut (paramètre obligatoire)|Adresse IP et port que le réplicateur principal/secondaire utilise pour communiquer avec d'autres réplicateurs dans le jeu de réplicas. Doit faire référence à un point de terminaison de ressource TCP dans le manifeste de service. Reportez-vous aux [ressources du manifeste de service](service-fabric-service-manifest-resources.md) pour en savoir plus sur la définition des ressources de point de terminaison dans le manifeste de service. |
|MaxPrimaryReplicationQueueSize|Nombre d'opérations|8 192|Nombre maximal d'opérations dans la file d'attente principale. Une opération est libérée quand le réplicateur principal reçoit un accusé de réception de tous les réplicateurs secondaires. Cette valeur doit être supérieure à 64 et être une puissance de 2.|
|MaxSecondaryReplicationQueueSize|Nombre d'opérations|16 384|Nombre maximal d'opérations dans la file d'attente secondaire. Une opération est libérée une fois son état devenu hautement disponible grâce à la persistance. Cette valeur doit être supérieure à 64 et être une puissance de 2.|
|CheckpointThresholdInMB|Mo|50|Quantité d'espace du fichier journal après lequel l'état est vérifié.|
|MaxRecordSizeInKB|Ko|1024|Taille maximale de l'enregistrement que le réplicateur peut écrire dans le journal. Cette valeur doit être un multiple de 4 et supérieure à 16.|
|OptimizeLogForLowerDiskUsage|Boolean|true|Si la valeur est true, le journal est configuré de sorte que le fichier journal dédié au réplica est créé à l’aide d’un fichier partiellement alloué NTFS. Cela réduit l'utilisation d'espace disque réel du fichier. Si la valeur est false, le fichier est créé avec des allocations fixes qui offrent les meilleures performances en écriture.|
|MaxRecordSizeInKB|Ko|1024|Taille maximale de l'enregistrement que le réplicateur peut écrire dans le journal. Cette valeur doit être un multiple de 4 et supérieure à 16.|
|SharedLogId|guid|""|Spécifie une valeur guid unique à utiliser pour identifier le fichier journal partagé utilisé avec ce réplica. En règle générale, les services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogId est spécifié, SharedLogPath doit l'être aussi.|
|SharedLogPath|Nom de chemin complet|""|Spécifie le chemin d'accès complet où sera créé le fichier journal partagé pour ce réplica. En règle générale, les services ne doivent pas utiliser ce paramètre. Toutefois, si SharedLogPath est spécifié, SharedLogId doit l'être aussi.|


## Exemple de configuration au moyen du code
```csharp
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            new ReliableStateManagerReplicatorSettings
            {
                RetryInterval = TimeSpan.FromSeconds(3)
            }));
}
```


## Exemple de fichier de configuration
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


## Remarques
BatchAcknowledgementInterval contrôle la latence de réplication. La valeur « 0 » entraîne la latence la plus faible possible, au détriment du débit (car davantage de messages d'accusé de réception doivent être envoyés et traités, chacun contenant moins d'accusés de réception). Plus la valeur de BatchAcknowledgementInterval est élevée, plus le débit de réplication général est élevé, au détriment d'une plus grande latence de l'opération. Cela se traduit directement par une latence dans la validation des transactions.

La valeur CheckpointThresholdInMB contrôle la quantité d'espace disque que le réplicateur peut utiliser pour stocker des informations d'état dans le fichier journal dédié au réplica. Son augmentation pour une valeur supérieure à celle par défaut peut entraîner des temps de reconfiguration plus rapides quand un nouveau réplica est ajouté à l'ensemble. Cela est dû au transfert d'état partiel qui intervient suite à la disponibilité d'un historique des opérations plus important dans le journal. Cela peut potentiellement accroître le temps de récupération d’un réplica après un blocage.

Si vous affectez à OptimizeForLowerDiskUsage la valeur true, l’espace du fichier journal est surapprovisionné. Les réplicas actifs peuvent ainsi stocker davantage d’informations d’état dans leurs fichiers journaux, tandis que les réplicas inactifs utilisent moins d’espace disque. Il est donc possible d’héberger davantage de réplicas sur un nœud. Si vous affectez à OptimizeForLowerDiskUsage la valeur false, les informations d’état sont écrites dans les fichiers journaux plus rapidement.

Le paramètre MaxRecordSizeInKB définit la taille maximale d’un enregistrement que le réplicateur peut écrire dans le fichier journal. Dans la plupart des cas, la taille d’enregistrement par défaut de 1 024 Ko est optimale. Toutefois, si le service ajoute des données plus volumineuses aux informations d’état, cette valeur devra éventuellement être augmentée. Il n'est pas recommandé de choisir une valeur MaxRecordSizeInKB inférieure à 1 024 Ko, car des enregistrements plus petits utilisent uniquement l'espace nécessaire à l'enregistrement le plus petit. Cette valeur ne doit être modifiée qu'en de rares occasions.

Les paramètres SharedLogId et SharedLogPath sont toujours utilisés ensemble pour permettre à un service d'utiliser un journal partagé distinct du journal partagé par défaut pour le nœud. Pour plus d'efficacité, vous devriez spécifier autant de services que possible dans le même journal partagé. Les fichiers journaux partagés doivent être placés sur des disques uniquement utilisés pour le fichier journal partagé afin de réduire la contention des mouvements de la tête. Cette valeur ne doit être modifiée qu'en de rares occasions.

<!---------HONumber=AcomDC_0309_2016-->