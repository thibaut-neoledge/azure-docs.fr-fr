<properties
   pageTitle="Configuration des services fiables avec état"
   description="En savoir plus sur la configuration des services fiables avec état dans Service Fabric."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/24/2015"
   ms.author="sumukhs"/>

# Configuration des services fiables avec état
La configuration par défaut du service fiable avec état peut être changée en modifiant le fichier « settings.xml » généré dans la racine du package Visual Studio, dans le dossier « Config » de chaque service dans l'application.

Le runtime Service Fabric recherche des noms de sections prédéfinis dans le fichier « settings.xml » et utilise les valeurs de configuration lors de la création des composants runtime sous-jacents.

> [AZURE.NOTE]Ne supprimez/modifiez **PAS** les noms de sections des configurations suivantes dans le fichier « settings.xml » généré dans la solution Visual Studio.

## Configuration de la sécurité du réplicateur
Les configurations de sécurité du réplicateur sont utilisées pour sécuriser le canal de communication utilisé lors de la réplication. Cela signifie que les services ne sont pas en mesure d'afficher leur trafic de réplication mutuel, ce qui garantit que les données hautement disponibles soient également sécurisées. Par défaut, une section de configuration de sécurité vide ne permet pas de sécuriser la réplication.

### Nom de la section
ReplicatorSecurityConfig

### Noms des configurations
Reportez-vous à la section [Sécurité de la réplication](../service-fabric/service-fabric-replication-security.md)

## Configuration du réplicateur
Les configurations de Replicator servent à configurer le réplicateur garantissant la haute fiabilité de l'état du service fiable avec état par la réplication et la conservation de l'état localement. La configuration par défaut est générée par le modèle Visual Studio et devrait suffire. Cette section décrit les configurations supplémentaires disponibles pour paramétrer le réplicateur.

### Nom de la section
ReplicatorConfig

### Noms des configurations

|Nom|Unité|Valeur par défaut|Remarques|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondes|0,05|Durée d'attente du réplicateur secondaire après la réception d'une opération et avant de renvoyer un accusé de réception au réplicateur principal. Tous les autres accusés de réception à envoyer pour les opérations traitées durant cet intervalle sont envoyés sous la forme d'une réponse.|
|ReplicatorEndpoint|N/A|N/A - RequiredParameter|Adresse IP et port que le réplicateur principal/secondaire utilise pour communiquer avec d'autres réplicateurs dans le jeu de réplicas. Doit faire référence à une ressource de système d'extrémité TCP dans le manifeste de service. Reportez-vous aux [ressources du manifeste de service](service-fabric-service-manifest-resources.md) pour en savoir plus sur la définition des ressources du système d'extrémité dans le manifeste de service. |
|RetryInterval|Secondes|5|Période après laquelle le réplicateur retransmet un message s'il ne reçoit aucun accusé de réception pour une opération.|
|MaxReplicationMessageSize|Octets|50 Mo|Taille maximale des données de réplication pouvant être transmises dans un même message.|
|MaxPrimaryReplicationQueueSize|Nombre d'opérations|1 024|Nombre maximal d'opérations dans la file d'attente principale. Une opération est libérée après que le réplicateur principal reçoit un accusé de réception de tous les réplicateurs secondaires. Cette valeur doit être une puissance de 2 supérieure à 64.|
|MaxSecondaryReplicationQueueSize|Nombre d'opérations|2 048|Nombre maximal d'opérations dans la file d'attente secondaire. Une opération est libérée une fois son état devenu hautement disponible via la persistance. Cette valeur doit être supérieure à 64 et être une puissance de 2.|
|MaxStreamSizeInMB|Mo|1024|Quantité d'espace réservée pour le fichier journal. La taille en octets doit être supérieure à 16 fois la valeur MaxRecordSize en octets.|
|MaxRecordSizeInKB|Ko|1024|Taille d'enregistrement maximale des données que le réplicateur peut écrire dans le journal. Cette valeur doit être un multiple de 4 et supérieure à 16.|
|OptimizeForLocalSSD|booléenne|false|Si la valeur est true, le journal est configuré de sorte que les informations d'état sont écrites directement dans le fichier journal dédié du réplica. Cela garantit les meilleures performances lorsque les fichiers journaux sont situés sur des supports SSD ou lorsque le taux d'E/S du disque de la machine virtuelle est très limité. Si la valeur est false, les informations d'état sont d'abord placées dans le fichier journal partagé puis transférées vers le fichier journal dédié.|
|OptimizeLogForLowerDiskUsage|booléenne|false|Si la valeur est true, le journal est configuré de sorte que le fichier journal dédié au réplica est créé à l'aide d'un fichier fragmenté NTFS. Cela réduit l'utilisation d'espace disque réel du fichier. Si la valeur est false, le fichier est créé avec des allocations fixes qui offrent les meilleures performances en écriture.|
|SharedLogId|guid|""|Spécifie une valeur guid unique à utiliser pour identifier le fichier journal partagé utilisé avec ce réplica. En général, les services ne doivent pas utiliser ce paramètre, mais si une valeur SharedLogId est spécifiée, alors la valeur SharedLogPath doit également être définie.|
|SharedLogPath|Chemin d'accès complet|""|Spécifie le chemin d'accès complet où sera créé le fichier journal partagé pour ce réplica. En général, les services ne doivent pas utiliser ce paramètre, mais si une valeur SharedLogPath est spécifiée, alors la valeur SharedLogId doit également être définie.|

## Exemple de fichier de configuration

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="MaxStreamSizeInMB" Value="512" />
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
BatchAcknowledgementInterval contrôle la latence de réplication. La valeur « 0 » entraîne la latence la plus faible possible, au détriment du débit (car davantage de messages d'accusé de réception doivent être envoyés et traités, chacun contenant moins d'accusés de réception). Plus la valeur de BatchAcknowledgementInterval est élevée, plus le débit de réplication général est élevé, au détriment d'une plus grande latence de l'opération. Cela se traduit directement par une latence dans la validation des transactions.

La valeur MaxStreamSizeInMB détermine la quantité d'espace disque que le réplicateur peut utiliser pour stocker des informations d'état dans le fichier journal dédié au réplica. Comme l'état des informations stockées est utilisé pour permettre à un autre réplica de correspondre à l'état d'un réplica principal, il est généralement préférable d'avoir un fichier journal plus volumineux car cela réduit le délai nécessaire pour que l'autre réplica corresponde à l'état du réplica principal. Cependant, les fichiers volumineux peuvent utiliser davantage d'espace disque et réduire ainsi le nombre de réplicas pouvant être hébergés sur un nœud particulier.

Le paramètre OptimizeForLowerDiskUsage permet de « surapprovisionner » l'espace du fichier journal afin que les réplicas actifs puissent stocker davantage d'informations d'état dans leurs fichiers journaux tandis que des réplicas inactifs utiliseraient moins d'espace disque. Bien que cela permette d'héberger plus de réplicas sur un nœud qu'en cas d'un manque d'espace disque, avec une valeur OptimizeForLowerDiskUsage définie sur false, les informations d'état sont écrites dans les fichiers journaux plus rapidement.

Le paramètre OptimizeForLocalSSD sert à désactiver l'écriture des informations d'état dans le fichier journal partagé avant de les transférer vers le fichier journal dédié. Il doit généralement être défini lorsque le stockage sur disque local du fichier journal dédié est un support SSD puisque la réduction des mouvements de la tête de lecture du disque ne pose pas de problème. Il peut également être utilisé lorsque la machine virtuelle effectue beaucoup d'opération d'E/S sur le disque local et que les taux d'E/S du disque local sont considérablement limités par la machine virtuelle hôte.

La valeur MaxRecordSizeInKB définit la taille maximale d'un enregistrement que le réplicateur peut écrire dans le fichier journal. Dans la plupart des cas, la taille d'enregistrement de 1 024 Ko par défaut est optimale ; mais si le service ajoute des données plus volumineuses aux informations d'état, cette valeur devra éventuellement être augmentée. Il n'est pas recommandé de choisir une valeur MaxRecordSizeInKB inférieure à 1024 car des enregistrements plus petits utilisent uniquement l'espace nécessaire à l'enregistrement le plus petit. Cette valeur ne devrait être modifiée qu'en de rares occasions.

Les paramètres SharedLogId et SharedLogPath sont toujours utilisés ensemble et permettent à un service d'utiliser un journal partagé distinct du journal partagé par défaut pour le nœud. Pour plus d'efficacité, vous devriez spécifier autant de services que possible dans le même journal partagé. Les fichiers journaux partagés doivent être placés sur des disques uniquement utilisés pour le fichier journal partagé afin de réduire la contention des mouvements de la tête de lecture. Cette valeur ne devrait être modifiée qu'en de rares occasions.
 

<!---HONumber=July15_HO4-->