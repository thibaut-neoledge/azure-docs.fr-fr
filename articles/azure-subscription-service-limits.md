<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Abonnement Microsoft Azure et limites, quotas et contraintes du service" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Fournit une liste des limites des abonnements et services Azure, ainsi que les valeurs maximales." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth" />

# Abonnement Azure et limites, quotas et contraintes du service

Le document ci-dessous présente quelques-unes des limites Microsoft Azure les plus communes. Notez que ce document ne couvre pas tous les services Azure. Dans le temps, ces limites seront étendues et mises à jour pour couvrir une plus grande partie de la plateforme.

-   [Limites d'abonnement][Limites d'abonnement]
-   [Limites du service cloud][Limites du service cloud]
-   [Limites de machine virtuelle][Limites de machine virtuelle]
-   [Limites des sites web][Limites des sites web]
-   [Limites de mise en réseau][Limites de mise en réseau]
-   [Limites de stockage][Limites de stockage]
-   [Limites de la version préliminaire de DocumentDB][Limites de la version préliminaire de DocumentDB]
-   [Limites de base de données SQL][Limites de base de données SQL]
-   [Limites de Media Services][Limites de Media Services]
-   [Limites de Service Bus][Limites de Service Bus]

> [WACOM.NOTE] Si vous souhaitez élever la limite au-dessus de la **Limite par défaut**, ouvrez gratuitement un incident auprès du [service client][service client]. Les limites ne peuvent pas être supérieures à la valeur **Limite maximale** dans les tableaux ci-dessous. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables.

## <a name="subscription"></a>Limites d'abonnement

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Limite par défaut</th>
<th align="left">Limite maximale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Cœurs par <a href="http://msdn.microsoft.com/fr-fr/library/azure/hh531793.aspx">abonnement</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/fr-fr/library/azure/gg456328.aspx">Coadministrateurs</a> par abonnement</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/storage-whatis-account/">Comptes de stockage</a> par abonnement</p></td>
<td align="left"><p>100</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-what-is/">Services cloud</a> par abonnement</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Réseaux virtuels</a> par abonnement<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/fr-fr/library/jj157100.aspx">Réseaux locaux</a> par abonnement</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Bases de données SQL par abonnement</p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>Serveurs de Base de données SQL logiques par abonnement</p></td>
<td align="left"><p>6</p></td>
<td align="left"><p>150</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Serveurs DNS par abonnement</p></td>
<td align="left"><p>9</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p>Adresses IP réservées par abonnement</p></td>
<td align="left"><p>5</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Certificats de services hébergés par abonnement</p></td>
<td align="left"><p>400</p></td>
<td align="left"><p>400</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/fr-fr/library/azure/jj156085.aspx">Groupes d'affinités</a> par abonnement</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Les très petites instances comptent comme un cœur pour la limite de cœurs, malgré l'utilisation d'un cœur partiel.

<sup>2</sup>Chaque réseau virtuel prend en charge une passerelle de réseau virtuel unique.

## <a name="webworkerlimits"></a>Limites du service cloud

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Limite par défaut</th>
<th align="left">Limite maximale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-what-is/">Rôles de travail/web par déploiement<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/fr-fr/library/gg557552.aspx#InstanceInputEndpoint">Points de terminaison d'entrée d'instance</a> par déploiement</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/fr-fr/library/gg557552.aspx#InputEndpoint">Points de terminaison d'entrée</a> par déploiement</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/fr-fr/library/gg557552.aspx#InternalEndpoint">Points de terminaison internes</a> par déploiement</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Chaque service cloud avec des rôles de travail/web peut posséder deux déploiements, un pour la production et un autre pour le développement intermédiaire. Notez aussi que cette limite s'applique au nombre de rôles (configuration) et non au nombre d'instances par rôle (extensibilité).

## <a name="vmlimits"></a>Limites de machine virtuelle

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Limite par défaut</th>
<th align="left">Limite maximale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/fr-fr/documentation/services/virtual-machines/">Machines virtuelles</a> par service cloud<sup>1</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Points de terminaison internes par service cloud<sup>2</sup></p></td>
<td align="left"><p>150</p></td>
<td align="left"><p>150</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Lors de la création d’une machine virtuelle, un service cloud est automatiquement créé pour la contenir. Vous pouvez ensuite ajouter plusieurs machines virtuelles au même service cloud.

<sup>2</sup>Les points de terminaison d'entrée permettent d'autoriser la communication vers des machines virtuelles externes au service cloud qui les contient. Les machines virtuelles dans le même service cloud autorisent automatiquement la communication entre tous les ports UDP et TCP pour la communication interne.

## <a name="websiteslimits"></a>Limites des sites web

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## <a name="networkinglimits"></a>Limites de mise en réseau

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource</th>
<th align="left">Limite par défaut</th>
<th align="left">Limite maximale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Nombre total de machines<sup>1</sup> par <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">réseau virtuel</a><sup>2</sup></p></td>
<td align="left"><p>2048</p></td>
<td align="left"><p>2048</p></td>
</tr>
<tr class="even">
<td align="left"><p>Connexions TCP simultanées pour une machine virtuelle ou une instance de rôle</p></td>
<td align="left"><p>500K</p></td>
<td align="left"><p>500K</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Listes de contrôle d'accès par point de terminaison<sup>3</sup></p></td>
<td align="left"><p>50</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p>Sites de réseau local par réseau virtuel</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>10</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Le nombre total de machines comprend les machines virtuelles et les instances de rôle de travail/web.

<sup>2</sup>Chaque réseau virtuel prend en charge une [passerelle de réseau virtuel unique][passerelle de réseau virtuel unique].

<sup>3</sup>La liste de contrôle d'accès est prise en charge sur les points de terminaison d'entrée pour les machines virtuelles. Pour les rôles de travail/web, elle est prise en charge sur les points de terminaison d'entrée et d'entrée d'instance.

## <a name="storagelimits"></a>Limites de stockage

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Ressource<sup>1</sup></th>
<th align="left">Limite par défaut</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>To par compte de stockage</p></td>
<td align="left"><p>500 To</p></td>
</tr>
<tr class="even">
<td align="left"><p>Taille maximale d'un conteneur d'objets blob, d'une table ou d'une file d'attente</p></td>
<td align="left"><p>500 To</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage</p></td>
<td align="left"><p>La seule limite est celle de 500 To de la capacité du compte de stockage</p></td>
</tr>
<tr class="even">
<td align="left"><p>Taille maximale d'un partage de fichiers</p></td>
<td align="left"><p>5 To</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Nombre maximal de fichiers dans un partage de fichiers</p></td>
<td align="left"><p>La seule limite est celle de 5 To de la capacité totale du partage de fichiers</p></td>
</tr>
<tr class="even">
<td align="left"><p>Maximum 8 Ko d'opérations E/S par disque persistant (niveau De base)</p></td>
<td align="left"><p>300<sup>2</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>Maximum 8 Ko d'opérations E/S par disque persistant (niveau Standard)</p></td>
<td align="left"><p>500<sup>2</sup></p></td>
</tr>
<tr class="even">
<td align="left"><p>Taux de demandes total (objets de 1 Ko) par compte de stockage</p></td>
<td align="left"><p>Jusqu'à 20 000 entités ou messages par seconde</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Débit cible pour un objet blob unique</p></td>
<td align="left"><p>Jusqu'à 60 Mo par seconde ou jusqu'à 500 demandes par seconde</p></td>
</tr>
<tr class="even">
<td align="left"><p>Débit cible pour une file d'attente unique (messages de 1 Ko)</p></td>
<td align="left"><p>Jusqu'à 2 000 messages par seconde</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Débit cible pour une partition de table unique (entités de 1 Ko)</p></td>
<td align="left"><p>Jusqu'à 2 000 entités par seconde</p></td>
</tr>
<tr class="even">
<td align="left"><p>Entrée max. par compte de stockage (régions des États-Unis)</p></td>
<td align="left"><p>10 Gbps if GRS<sup>3</sup> enabled, 20 Gbps for LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Sortie max. par compte de stockage (régions des États-Unis)</p></td>
<td align="left"><p>20 Gbit/s si GRS<sup>3</sup> activé, 30 Gbit/s pour LRS</p></td>
</tr>
<tr class="even">
<td align="left"><p>Entrée max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
<td align="left"><p>5 Gbit/s si GRS<sup>3</sup> activé, 10 Gbit/s pour LRS</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Sortie max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
<td align="left"><p>10 Gbps if GRS<sup>3</sup> enabled, 15 Gbps for LRS</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Pour plus de détails sur ces limites, consultez la rubrique [Objectifs de performances et d'extensibilité du stockage Azure][Objectifs de performances et d'extensibilité du stockage Azure].

<sup>2</sup>Pour les machines virtuelles utilisées dans le niveau De base, ne placez pas plus de 66 disques durs virtuels à haute utilisation dans un compte de stockage, afin d'éviter la limite de débit d'appels fixée à 20 000 au total (20 000/300). Pour les machines virtuelles utilisées dans le niveau Standard, ne placez pas plus de 40 disques durs virtuels à haute utilisation dans un compte de stockage (20 000/500). Pour plus d'informations, consultez la page [Tailles de machine virtuelle et de service cloud pour Azure][Tailles de machine virtuelle et de service cloud pour Azure].

<sup>3</sup>GRS signifie [Geo Redundant Storage, soit stockage géo-redondant][Geo Redundant Storage, soit stockage géo-redondant]. LRS signifie [Locally Redundant Storage, soit stockage localement redondant][Locally Redundant Storage, soit stockage localement redondant]. Notez que GRS est également redondant en local.

## <a name="documentdblimits"></a>Limites de la version préliminaire de DocumentDB

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

## <a name="sqldblimits"></a>Limites de base de données SQL

Pour les limites de base de données SQL, consultez les rubriques suivantes :

-   [Niveaux de service de la Base de données SQL Azure (éditions)][Niveaux de service de la Base de données SQL Azure (éditions)]
-   [Niveaux de service et niveaux de performance de la Base de données SQL Azure][Niveaux de service et niveaux de performance de la Base de données SQL Azure]
-   [Quotas d'unités de débit de base de données (UDBD)][Quotas d'unités de débit de base de données (UDBD)]
-   [Limites de ressources de la Base de données SQL][Limites de ressources de la Base de données SQL]

## <a name="mediaserviceslimits"></a>Limites de Media Services

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## <a name="servicebuslimits"></a>Limites de Service Bus

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

## <a name="seealso"></a>Voir aussi

[Présentation des limites et des augmentations Azure][service client]

[Tailles de machines virtuelles et services cloud pour Windows Azure][Tailles de machine virtuelle et de service cloud pour Azure]

  [Limites d'abonnement]: #subscription
  [Limites du service cloud]: #webworkerlimits
  [Limites de machine virtuelle]: #vmlimits
  [Limites des sites web]: #websiteslimits
  [Limites de mise en réseau]: #networkinglimits
  [Limites de stockage]: #storagelimits
  [Limites de la version préliminaire de DocumentDB]: #documentdblimits
  [Limites de base de données SQL]: #sqldblimits
  [Limites de Media Services]: #mediaserviceslimits
  [Limites de Service Bus]: #servicebuslimits
  [service client]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [passerelle de réseau virtuel unique]: http://msdn.microsoft.com/fr-fr/library/azure/jj156210.aspx
  [Objectifs de performances et d'extensibilité du stockage Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Tailles de machine virtuelle et de service cloud pour Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn197896.aspx
  [Geo Redundant Storage, soit stockage géo-redondant]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [Locally Redundant Storage, soit stockage localement redondant]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [Niveaux de service de la Base de données SQL Azure (éditions)]: http://msdn.microsoft.com/fr-fr/library/azure/dn741340.aspx
  [Niveaux de service et niveaux de performance de la Base de données SQL Azure]: http://msdn.microsoft.com/fr-fr/library/azure/dn741336.aspx
  [Quotas d'unités de débit de base de données (UDBD)]: http://msdn.microsoft.com/fr-fr/library/azure/ee336245.aspx#DTUs
  [Limites de ressources de la Base de données SQL]: http://msdn.microsoft.com/fr-fr/library/azure/dn338081.aspx
