<properties urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Limites, quotas et contraintes des abonnements et services Microsoft Azure" metaKeywords="Cloud Services, machines virtuelles, Sites web, Virtual Network, SQL Database, abonnement, Storage" description="Fournit une liste des limites des abonnements et services Azure, ainsi que les valeurs maximales." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="jeffreyg" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="jroth" />

# Abonnement Azure et limites, quotas et contraintes du service

Le document ci-dessous présente quelques-unes des limites Microsoft Azure les plus communes. Notez que ce document ne couvre pas tous les services Azure. Dans le temps, ces limites seront étendues et mises à jour pour couvrir une plus grande partie de la plateforme.

- [Limites d'abonnement](#subscription)
- [Limites de service cloud](#webworkerlimits)
- [Limites de machine virtuelle](#vmlimits)
- [Limites de sites web](#websiteslimits)
- [Limites de mise en réseau](#networkinglimits)
- [Limites de stockage](#storagelimits)
- [Limites de Batch en version préliminaire](#batchlimits)
- [Limites de DocumentDB en version préliminaire](#documentdblimits)
- [Limites de base de données SQL](#sqldblimits)
- [Limites de Media Services](#mediaserviceslimits)
- [Limites de Service Bus](#servicebuslimits)
- [Limites d'Active Directory](#adlimits)

> [WACOM.NOTE] Si vous souhaitez élever la limite au-dessus de la **Limite par défaut**, [ouvrez gratuitement un incident auprès du service client][azurelimitsblogpost]. Les limites ne peuvent pas être supérieures à la valeur **Limite maximale** dans les tableaux ci-dessous. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables.

##<a name="subscription"></a>Limites d'abonnement

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
   <th align="left" valign="middle">Limite maximale</th>
</tr>
<tr>
   <td valign="middle"><p>Cœurs par <a href="http://msdn.microsoft.com/fr-fr/library/azure/hh531793.aspx">abonnement</a><sup>1</sup></p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>10 000</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/fr-fr/library/azure/gg456328.aspx">Coadministrateurs</a> par abonnement</p></td>
   <td valign="middle"><p>200</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/storage-whatis-account/">Comptes de stockage</a> par abonnement</p></td>
   <td valign="middle"><p>100</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-what-is/">Services cloud</a> par abonnement</p></td>
   <td valign="middle"><p>20</p></td>
   <td valign="middle"><p>200</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Réseaux virtuels</a> par abonnement<sup>2</sup></p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/fr-fr/library/jj157100.aspx">Réseaux locaux</a> par abonnement</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Serveurs de bases de données SQL par abonnement</p></td>
   <td valign="middle"><p>6</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
<tr>
   <td valign="middle"><p>Bases de données SQL par serveur</p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>500</p></td>
</tr>
<tr>
   <td valign="middle"><p>Serveurs DNS par abonnement</p></td>
   <td valign="middle"><p>9</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Adresses IP réservées par abonnement</p></td>
   <td valign="middle"><p>5</p></td>
   <td valign="middle"><p>100</p></td>
</tr>
<tr>
   <td valign="middle"><p>Certificats de services hébergés par abonnement</p></td>
   <td valign="middle"><p>400</p></td>
   <td valign="middle"><p>400</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/fr-fr/library/azure/jj156085.aspx">Groupes d'affinités</a> par abonnement</p></td>
   <td valign="middle"><p>256</p></td>
   <td valign="middle"><p>256</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/azure-preview-portal-using-resource-groups/">Groupes de ressources</a> par abonnement</p></td>
   <td valign="middle"><p>300</p></td>
   <td valign="middle"><p>300</p></td>
</tr>

<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/fr-fr/services/batch/">Comptes du service Batch en version préliminaire</a> par région et par abonnement</p></td>
   <td valign="middle"><p>1</p></td>
   <td valign="middle"><p>50</p></td>   
</tr>
</table>

<sup>1</sup>Les très petites instances comptent comme un cœur pour la limite de cœurs, malgré l'utilisation d'un cœur partiel.

<sup>2</sup>Chaque réseau virtuel prend en charge une passerelle de réseau virtuel unique.

##<a name="webworkerlimits"></a>Limites de service cloud

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
   <th align="left" valign="middle">Limite maximale</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/fr-fr/documentation/articles/cloud-services-what-is/">Rôles de travail/web par déploiement<sup>1</sup></a></p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/fr-fr/library/gg557552.aspx#InstanceInputEndpoint">Points de terminaison d'entrée d'instance</a> par déploiement</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/fr-fr/library/gg557552.aspx#InputEndpoint">Points de terminaison d'entrée</a> par déploiement</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/fr-fr/library/gg557552.aspx#InternalEndpoint">Points de terminaison internes</a> par déploiement</p></td>
   <td valign="middle"><p>25</p></td>
   <td valign="middle"><p>25</p></td>
</tr>
</table>

<sup>1</sup>Chaque service cloud avec des rôles de travail/web peut posséder deux déploiements, un pour la production et un autre pour le développement intermédiaire. Notez aussi que cette limite s'applique au nombre de rôles (configuration) et non au nombre d'instances par rôle (extensibilité). 

##<a name="vmlimits"></a>Limites de machine virtuelle

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
   <th align="left" valign="middle">Limite maximale</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://azure.microsoft.com/fr-fr/documentation/services/virtual-machines/">Machines virtuelles</a> par service cloud<sup>1</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Points de terminaison d'entrée par service cloud<sup>2</sup></p></td>
   <td valign="middle"><p>150</p></td>
   <td valign="middle"><p>150</p></td>
</tr>
</table>

<sup>1</sup>Lors de la création d'une machine virtuelle, un service cloud est automatiquement créé pour la contenir. Vous pouvez ensuite ajouter plusieurs machines virtuelles au même service cloud.

<sup>2</sup>Les points de terminaison d'entrée permettent d'autoriser la communication vers des machines virtuelles externes au service cloud qui les contient. Les machines virtuelles dans le même service cloud autorisent automatiquement la communication entre tous les ports UDP et TCP pour la communication interne.

##<a name="websiteslimits"></a>Limites de sites web

[WACOM.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

##<a name="networkinglimits"></a>Limites de mise en réseau

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Limite par défaut</th>
   <th align="left" valign="middle">Limite maximale</th>
</tr>
<tr>
   <td valign="middle"><p>Nombre total de machines<sup>1</sup> par <a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Réseau virtuel</a><sup>2</sup></p></td>
   <td valign="middle"><p>2048</p></td>
   <td valign="middle"><p>2048</p></td>
</tr>
<tr>
   <td valign="middle"><p>Connexions TCP simultanées pour une machine virtuelle ou une instance de rôle</p></td>
   <td valign="middle"><p>500 Ko</p></td>
   <td valign="middle"><p>500 Ko</p></td>
</tr>
<tr>
   <td valign="middle"><p>Listes de contrôle d'accès (ACL) par point de terminaison<sup>3</sup></p></td>
   <td valign="middle"><p>50</p></td>
   <td valign="middle"><p>50</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sites de réseau local par réseau virtuel</p></td>
   <td valign="middle"><p>10</p></td>
   <td valign="middle"><p>10</p></td>
</tr>
</table>

<sup>1</sup>Le nombre total de machines comprend les machines virtuelles et les instances de rôle de travail/web.

<sup>2</sup>Chaque réseau virtuel prend en charge une [passerelle de réseau virtuel unique][gateway].

<sup>3</sup>La liste de contrôle d'accès est prise en charge sur les points de terminaison d'entrée pour les machines virtuelles. Pour les rôles de travail/web, elle est prise en charge sur les points de terminaison d'entrée et d'entrée d'instance.

##<a name="storagelimits"></a>Limites de stockage

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource<sup>1</sup></th>
   <th align="left" valign="middle">Limite par défaut</th>
</tr>
<tr>
   <td valign="middle"><p>To par compte de stockage</p></td>
   <td valign="middle"><p>500 To</p></td>
</tr>
<tr>
   <td valign="middle"><p>Taille maximale d'un conteneur d'objets blob, d'une table ou d'une file d'attente</p></td>
   <td valign="middle"><p>500 To</p></td>
</tr>
<tr>
   <td valign="middle"><p>Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage</p></td>
   <td valign="middle"><p>La seule limite est celle de 500 To de la capacité du compte de stockage</p></td>
</tr>
<tr>
   <td valign="middle"><p>Taille maximale d'un partage de fichiers</p></td>
   <td valign="middle"><p>5 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>Nombre maximal de fichiers dans un partage de fichiers</p></td>
   <td valign="middle"><p>La seule limite est celle de 5 To de la capacité totale du partage de fichiers</p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximum 8 Ko d'opérations E/S par disque persistant (niveau De base)</p></td>
   <td valign="middle"><p>300<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Maximum 8 Ko d'opérations E/S par disque persistant (niveau Standard)</p></td>
   <td valign="middle"><p>500<sup>2</sup></p></td>
</tr>
<tr>
   <td valign="middle"><p>Taux de demandes total (objets de 1 Ko) par compte de stockage</p></td>
   <td valign="middle"><p>Jusqu'à 20 000 entités ou messages par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Débit cible pour un objet blob unique</p></td>
   <td valign="middle"><p>Jusqu'à 60 Mo par seconde ou jusqu'à 500 demandes par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Débit cible pour une file d'attente unique (messages de 1 Ko)</p></td>
   <td valign="middle"><p>Jusqu'à 2 000 messages par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Débit cible pour une partition de table unique (entités de 1 Ko)</p></td>
   <td valign="middle"><p>Jusqu'à 2 000 entités par seconde</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrée max. par compte de stockage (régions des États-Unis)</p></td>
   <td valign="middle"><p>10 Gbits/s si compatible GRS<sup>3</sup> et 20 Gbits/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sortie max. par compte de stockage (régions des États-Unis)</p></td>
   <td valign="middle"><p>20 Gbits/s si compatible GRS<sup>3</sup> et 30 Gbits/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Entrée max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
   <td valign="middle"><p>5 Gbits/s si compatible GRS<sup>3</sup> et 10 Gbits/s pour LRS</p></td>
</tr>
<tr>
   <td valign="middle"><p>Sortie max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
   <td valign="middle"><p>10 Gbits/s si compatible GRS<sup>3</sup> et 15 Gbits/s pour LRS</p></td>
</tr>
</table>

<sup>1</sup>Pour plus d'informations sur ces limites, consultez les [objectifs de performances et d'extensibilité d'Azure Storage][storagelimits]. 

<sup>2</sup>Pour les machines virtuelles utilisées au niveau De base, ne placez pas plus de 66 disques durs virtuels à haute utilisation dans un compte de stockage, afin d'éviter la limite de débit d'appels fixée à 20 000 au total (20 000/300). Pour les machines virtuelles utilisées dans le niveau Standard, ne placez pas plus de 40 disques durs virtuels à haute utilisation dans un compte de stockage (20 000/500). Pour plus d'informations, consultez [Tailles de machine virtuelle et de service cloud pour Azure][vmsizes]. 

<sup>3</sup>GRS signifie [Geo Redundant Storage][georedundantstorage], soit stockage géo-redondant. LRS signifie [Locally Redundant Storage][locallyredundantstorage], soit stockage localement redondant. Notez que GRS est également redondant en local.

##<a name="batchlimits"></a>Limites de Batch en version préliminaire

[WACOM.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

##<a name="documentdblimits"></a>Limites de DocumentDB en version préliminaire

[WACOM.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

##<a name="sqldblimits"></a>Limites de base de données SQL

Pour les limites de base de données SQL, consultez les rubriques suivantes :

 - [Niveaux du service Azure SQL Database (éditions)][sqltiers]
 - [Niveaux du service Azure SQL Database et niveaux de performances][sqltiersperflevels]
 - [Quotas d'unités de débit de base de données(UDBD)][sqlDTU]
 - [Limites de ressources du service SQL Database][sqldblimits]

##<a name="mediaserviceslimits"></a>Limites de Media Services

[WACOM.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

##<a name="servicebuslimits"></a>Limites de Service Bus

[WACOM.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]

##<a name="adlimits"></a>Limites d'Active Directory

Pour Azure Active Directory (AD), consultez la rubrique suivante :

 - [Restrictions et limites du service Azure Active Directory][adlimitsandrestrictions]

##<a name="seealso"></a>Voir aussi

[Présentation des limites et des augmentations Azure][azurelimitsblogpost]

[Tailles de machine virtuelle et de service cloud pour Azure][vmsizes]

  [customersupportfaq]: http://azure.microsoft.com/fr-fr/support/faq/
  [azurelimitsblogpost]: http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
  [gateway]: http://msdn.microsoft.com/fr-fr/library/azure/jj156210.aspx 
  [storagelimits]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [sqldblimits]: http://msdn.microsoft.com/fr-fr/library/azure/dn338081.aspx
  [sqltiers]: http://msdn.microsoft.com/fr-fr/library/azure/dn741340.aspx
  [sqltiersperflevels]: http://msdn.microsoft.com/fr-fr/library/azure/dn741336.aspx
  [sqlDTU]: http://msdn.microsoft.com/fr-fr/library/azure/ee336245.aspx#DTUs
  [vmsizes]: http://msdn.microsoft.com/fr-fr/library/azure/dn197896.aspx
  [georedundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
  [locallyredundantstorage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/08/introducing-locally-redundant-storage-for-windows-azure-storage.aspx
  [adlimitsandrestrictions]: http://msdn.microsoft.com/fr-fr/library/azure/dn764971.aspx

<!--HONumber=35.2-->
