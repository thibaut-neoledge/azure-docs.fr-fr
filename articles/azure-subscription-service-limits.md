<properties linkid="azure-subscription-service-limits" urlDisplayName="Azure Subscription and Service Limits, Quotas, and Constraints" pageTitle="Microsoft Azure Subscription and Service Limits, Quotas, and Constraints" metaKeywords="Cloud Services, Virtual Machines, Web Sites, Virtual Network, SQL Database, Subscription, Storage" description="Provides a list of common Azure subscription and service limits along with maximum values." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title="" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jroth"></tags>

# Abonnement Azure et limites, quotas et contraintes du service

Le document ci-dessous présente quelques-unes des limites Microsoft Azure les plus communes. Notez que ce document ne couvre pas tous les services Azure. Dans le temps, ces limites seront étendues et mises à jour pour couvrir une plus grande partie de la plateforme.

-   [Limites d'abonnement][]
-   [Limites de rôle de travail/web][]
-   [Limites de machine virtuelle][]
-   [Limites de mise en réseau][]
-   [Limites de stockage][]
-   [Limites de base de données SQL][]

> [WACOM.NOTE] Si vous souhaitez élever la limite au-dessus de la **Limite par défaut**, ouvrez un incident auprès du [service client][]. Les limites ne peuvent pas être supérieures à la valeur **Limite maximale** dans les tableaux ci-dessous. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables.

## <a name="subscription"></a>Limites d'abonnement

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
<th align="left">Limite par défaut</th>
<th align="left">Limite maximale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>Cœurs par <a href="http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx">abonnement</a><sup>1</sup></p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>10,000</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx">Coadministrateurs</a> par abonnement</p></td>
<td align="left"><p>200</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/">Comptes de stockage</a> par abonnement</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>50</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Services cloud</a> par abonnement</p></td>
<td align="left"><p>20</p></td>
<td align="left"><p>200</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/library/azure/jj156007.aspx">Réseaux virtuels</a> par abonnement<sup>2</sup></p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/jj157100.aspx">Réseaux locaux</a> par abonnement</p></td>
<td align="left"><p>10</p></td>
<td align="left"><p>100</p></td>
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
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx">Groupes d'affinités</a> par abonnement</p></td>
<td align="left"><p>256</p></td>
<td align="left"><p>256</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Les très petites instances comptent comme un cœur pour la limite de cœurs, malgré l'utilisation d'un cœur partiel.

<sup>2</sup>Chaque réseau virtuel prend en charge une passerelle de réseau virtuel unique.

## <a name="webworkerlimits"></a>Limites de rôle de travail/web

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
<th align="left">Limite par défaut</th>
<th align="left">Limite maximale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/">Rôles de travail/web par déploiement<sup>1</sup></a></p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint">Points de terminaison d'entrée d'instance</a> par déploiement</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint">Points de terminaison d'entrée</a> par déploiement</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint">Points de terminaison internes</a> par déploiement</p></td>
<td align="left"><p>25</p></td>
<td align="left"><p>25</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Chaque service cloud avec des rôles de travail/web peut posséder deux déploiements, un pour la production et un autre pour le développement intermédiaire.

## <a name="vmlimits"></a>Limites de machine virtuelle

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
<th align="left">Limite par défaut</th>
<th align="left">Limite maximale</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="http://azure.microsoft.com/en-us/documentation/services/virtual-machines/">Machines virtuelles</a> par service cloud<sup>1</sup></p></td>
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

## <a name="networkinglimits"></a>Limites de mise en réseau

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
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

<sup>2</sup>Chaque réseau virtuel prend en charge une [passerelle de réseau virtuel unique][].

<sup>3</sup>La liste de contrôle d'accès est prise en charge sur les points de terminaison d'entrée pour les machines virtuelles. Pour les rôles de travail/web, elle est prise en charge sur les points de terminaison d'entrée et d'entrée d'instance.

## <a name="storagelimits"></a>Limites de stockage<sup>1</sup>

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
<th align="left">Limite par défaut</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>To par compte de stockage<sup>2</sup></p></td>
<td align="left"><p>500</p></td>
</tr>
<tr class="even">
<td align="left"><p>IOPS max. pour disque persistant</p></td>
<td align="left"><p>500<sup>3</sup></p></td>
</tr>
<tr class="odd">
<td align="left"><p>IOPS max. par compte de stockage</p></td>
<td align="left"><p>20,000</p></td>
</tr>
<tr class="even">
<td align="left"><p>Entrée max. par compte de stockage (régions des États-Unis)</p></td>
<td align="left"><p>10 Gbits/s si GRS<sup>4</sup> activé, 20 Gbits/s si désactivé</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Entrée max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
<td align="left"><p>5 Gbits/s si GRS<sup>4</sup> activé, 10 Gbits/s si désactivé</p></td>
</tr>
<tr class="even">
<td align="left"><p>Sortie max. par compte de stockage (régions des États-Unis)</p></td>
<td align="left"><p>20 Gbits/s si GRS<sup>4</sup> activé, 30 Gbits/s si désactivé</p></td>
</tr>
<tr class="odd">
<td align="left"><p>Sortie max. par compte de stockage (régions d'Europe et d'Asie)</p></td>
<td align="left"><p>10 Gbits/s si GRS<sup>4</sup> activé, 15 Gbits/s si désactivé</p></td>
</tr>
</tbody>
</table>

<sup>1</sup>Pour plus de détails sur ces limites, consultez la rubrique [Objectifs de performances et d'extensibilité du stockage Azure][].

<sup>2</sup>Pour les objets blob de pages, seules les pages en utilisation augmentent l'utilisation de la capacité. Par exemple, une machine virtuelle avec un disque dur virtuel de 127 Go mais dont seulement 30 Go sont utilisés par le système d'exploitation, n'est facturée que pour la partie utilisée des 30 Go dans le disque dur virtuel, et non pour la totalité des 127 Go.

<sup>3</sup>Ne placez pas plus de 40 disques durs virtuels fortement utilisés dans un compte pour éviter la limite de 20 000 IOPS.

<sup>4</sup>Compte de stockage géo-redondant

## <a name="sqldblimits"></a>Limites de base de données SQL

Pour les limites de base de données SQL, consultez les rubriques suivantes :

-   [Niveaux de service de la Base de données SQL Azure (éditions)][]
-   [Niveaux de service et niveaux de performance de la Base de données SQL Azure][]
-   [Limites de ressources de la Base de données SQL][]

## <a name="seealso"></a>Voir aussi

[Tailles de machines virtuelles et services cloud pour Windows Azure][]

[Objectifs de performance et évolutivité d'Azure Storage][Objectifs de performances et d'extensibilité du stockage Azure]

[Niveaux de service de la Base de données SQL Azure (éditions)][]

[Niveaux de service et niveaux de performance de la Base de données SQL Azure][]

[Limites de ressources de la Base de données SQL][]

  [Limites d'abonnement]: #subscription
  [Limites de rôle de travail/web]: #webworkerlimits
  [Limites de machine virtuelle]: #vmlimits
  [Limites de mise en réseau]: #networkinglimits
  [Limites de stockage]: #storagelimits
  [Limites de base de données SQL]: #sqldblimits
  [service client]: http://azure.microsoft.com/en-us/support/faq/
  [abonnement]: http://msdn.microsoft.com/en-us/library/azure/hh531793.aspx
  [Coadministrateurs]: http://msdn.microsoft.com/en-us/library/azure/gg456328.aspx
  [Comptes de stockage]: http://azure.microsoft.com/en-us/documentation/articles/storage-whatis-account/
  [Services cloud]: http://azure.microsoft.com/en-us/documentation/articles/cloud-services-what-is/
  [Réseaux virtuels]: http://msdn.microsoft.com/library/azure/jj156007.aspx
  [Réseaux locaux]: http://msdn.microsoft.com/en-us/library/jj157100.aspx
  [Groupes d'affinités]: http://msdn.microsoft.com/en-us/library/azure/jj156085.aspx
  [Points de terminaison d'entrée d'instance]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InstanceInputEndpoint
  [Points de terminaison d'entrée]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InputEndpoint
  [Points de terminaison internes]: http://msdn.microsoft.com/en-us/library/gg557552.aspx#InternalEndpoint
  [Machines virtuelles]: http://azure.microsoft.com/en-us/documentation/services/virtual-machines/
  [passerelle de réseau virtuel unique]: http://msdn.microsoft.com/en-us/library/azure/jj156210.aspx
  [Objectifs de performances et d'extensibilité du stockage Azure]: http://msdn.microsoft.com/library/azure/dn249410.aspx
  [Niveaux de service de la Base de données SQL Azure (éditions)]: http://msdn.microsoft.com/en-us/library/azure/dn741340.aspx
  [Niveaux de service et niveaux de performance de la Base de données SQL Azure]: http://msdn.microsoft.com/en-us/library/azure/dn741336.aspx
  [Limites de ressources de la Base de données SQL]: http://msdn.microsoft.com/en-us/library/azure/dn338081.aspx
  [Tailles de machines virtuelles et services cloud pour Windows Azure]: http://msdn.microsoft.com/en-us/library/azure/dn197896.aspx
