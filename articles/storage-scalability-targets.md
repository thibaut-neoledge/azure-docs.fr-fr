<properties 
   pageTitle="Objectifs de performance et d’extensibilité Azure Storage | Azure"
   description="Obtenez plus d’informations sur les objectifs d’extensibilité et de performances des comptes Azure Storage, y compris la capacité, le taux de demande et la bande passante entrante et sortante. Découvrez les objectifs de performances des partitions dans chacun des services Azure Storage."
   services="storage"
   documentationCenter="na"
   authors="tamram"
   manager="na"
   editor="na" /> <tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage"
   ms.date="04/24/2015"
   ms.author="tamram" />

# Objectifs de performance et évolutivité d'Azure Storage

Cet article décrit l’extensibilité et les performances de Microsoft Azure Storage. Pour prendre connaissance des autres informations relatives aux limites Azure, consultez [Limites, quotas et contraintes applicables aux services et abonnements Azure](azure-subscription-service-limits.md).

>[AZURE.NOTE]Tous les comptes de stockage s’exécutent sur la nouvelle topologie de réseau plat et prennent en charge les objectifs d’extensibilité et de performances décrits ci-après, quel que soit le moment où ils ont été créés. Pour plus d’informations sur l’architecture de réseau plat Azure Storage et sur son extensibilité, consultez le blog de l’équipe Azure Storage.

<!-- -->

>[AZURE.IMPORTANT]Les objectifs d’extensibilité et de performances répertoriés ici sont des objectifs haut de gamme mais réalisables. Dans tous les cas, le taux de demande et la bande passante atteints par votre compte de stockage dépendent de la taille des objets stockés, des modèles d’accès utilisés et du type de charge de travail de votre application. Veillez à tester votre service afin de déterminer si ses performances répondent à vos besoins. Dans la mesure du possible, évitez les pics soudains de trafic et assurez-vous que le trafic est bien réparti sur toutes les partitions.

>Lorsque votre application atteint la limite de gestion d’une partition concernant la charge de travail, Azure Storage commence à renvoyer des codes d’erreur 503 \(Serveur occupé\) ou 500 \(Délai d’expiration de l’opération\). Quand cela se produit, l’application doit utiliser une stratégie d’interruption exponentielle pour les nouvelles tentatives. L’interruption exponentielle diminue la charge sur la partition et atténue les pics de trafic pour cette partition.

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, concevez votre application de sorte qu’elle utilise plusieurs comptes de stockage et partitionnez vos objets de données sur ces comptes. Un seul abonnement Azure permet l’utilisation de 100 comptes de stockage. Pour plus d'informations sur la tarification des licences en volume, consultez la page [Tarification Azure Storage](http://azure.microsoft.com/pricing/details/storage/).

## Objectifs d’extensibilité des comptes de stockage

Les objectifs d’extensibilité d’un compte de stockage dépendent de la région où il a été créé. Les sections suivantes décrivent les objectifs d’extensibilité de plusieurs régions : États-Unis, Europe et Asie.

Pour plus d’informations sur les objectifs d’architecture et d’extensibilité des comptes de stockage, consultez le [blog de l’équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/04/windows-azure-s-flat-network-storage-and-2012-scalability-targets.aspx).

### Objectifs d’extensibilité des comptes de stockage standard aux États-Unis

Le tableau suivant décrit les objectifs d’extensibilité des comptes de stockage standard aux États-Unis en fonction du niveau de redondance choisi :

<table>
<tr>
<th>Capacité totale des comptes</th>
<th>Taux de demandes total (objets de 1&#160;Ko)</th>
<th>Bande passante totale pour un compte de stockage géo-redondant</th>
<th>Bande passante totale pour un compte de stockage localement redondant</th>
<tr>
<td>500&#160;To</td>
<td>Jusqu'à 20&#160;000&#160;entités ou messages par seconde</td>
<td><ul>
<li>*Entrante&#160;: jusqu’à 10&#160;Gbps</li>
<li>*Sortante&#160;: jusqu’à 20&#160;Gbps</li>
</ol>
</td>
<td><ul>
<li>*Entrante&#160;: jusqu’à 20&#160;Gbps</li>
<li>*Sortante&#160;: jusqu’à 30&#160;Gbps</li>
</tr>
</table>

*Les données entrantes représentent toutes les données \(demandes\) envoyées à un compte de stockage. 

*Les données sortantes représentent toutes les données \(réponses\) reçues d’un compte de stockage. 

### Objectifs d’extensibilité des comptes de stockage standard en Europe et en Asie

Le tableau suivant décrit les objectifs d’extensibilité des comptes de stockage standard en Europe et en Asie, en fonction du niveau de redondance choisi :

<table>
<tr>
<th>Capacité totale des comptes</th>
<th>Taux de demandes total (objets de 1&#160;Ko)</th>
<th>Bande passante totale pour un compte de stockage géo-redondant</th>
<th>Bande passante totale pour un compte de stockage localement redondant</th>
<tr>
<td>500&#160;To</td>
<td>Jusqu'à 20&#160;000&#160;entités ou messages par seconde</td>
<td><ul>
<li>*Entrante&#160;: jusqu’à 5&#160;Gbps</li>
<li>*Sortante&#160;: jusqu’à 10&#160;Gbps</li>
</ol>
</td>
<td><ul>
<li>*Entrante&#160;: jusqu’à 10&#160;Gbps</li>
<li>*Sortante&#160;: jusqu’à 15&#160;Gbps</li>
</tr>
</table>

*Les données entrantes représentent toutes les données \(demandes\) envoyées à un compte de stockage. 

*Les données sortantes représentent toutes les données \(réponses\) reçues d’un compte de stockage. 

### Objectifs d’extensibilité pour les comptes de stockage Premium

Le tableau suivant décrit les objectifs d’extensibilité des comptes de stockage Premium disponibles dans les régions de l’ouest des États-Unis, de l’est des États-Unis 2 et de l’Europe occidentale :

<table>
<tr>
<th>Capacité totale des comptes</th>
<th>Bande passante totale pour un compte de stockage Premium localement redondant</th>
<tr>
<td><ul>
<li>Capacité du disque&#160;: 35&#160;To</li>
<li>Capacité d’instantanés&#160;: 10&#160;To</li>
</td>
<td>Jusqu'à 50&#160;Go par seconde pour les données entrantes/sortantes</td>
</table>

*Les données entrantes représentent toutes les données \(demandes\) envoyées à un compte de stockage. 

*Les données sortantes représentent toutes les données \(réponses\) reçues d’un compte de stockage.

Pour plus d’informations sur les disques de stockage Premium, consultez [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](storage-premium-storage-preview-portal.md).

## Partitions dans Azure Storage

Le tableau suivant décrit les objectifs de performances pour une seule partition dans chaque service :

<table>
<tr>
<th>Débit cible pour un objet blob unique</th>
<th>Débit cible pour une file d'attente unique (messages de 1&#160;Ko)</th>
<th>Débit cible pour une partition de table unique (entités de 1&#160;Ko)</th>
<th>Débit cible pour un fichier unique (version préliminaire)</th><tr>
<td>Jusqu'à 60&#160;Mo par seconde ou jusqu'à 500&#160;demandes par seconde</td>
<td>Jusqu'à 2&#160;000&#160;messages par seconde</td>
<td>Jusqu'à 2&#160;000&#160;entités par seconde</td>
<td>Jusqu’à 60&#160;Mo par seconde</td>
</table>

Chaque objet contenant des données stockées dans Azure Storage \(blobs, messages, entités et fichiers\) appartient à une partition et est identifié par une clé de partition. La partition détermine la manière dont Azure Storage équilibre la charge des blobs, des messages, des entités et des fichiers sur les serveurs pour répondre aux besoins de trafic de ces objets. La clé de partition est unique dans le compte de stockage. Elle permet de localiser un blob, un message ou une entité.

Pour les tables, toutes les entités affichant la même valeur de clé de partition sont regroupées dans la même partition et sont stockées sur le même serveur de partition. Il s’agit d’un point important de la conception de votre application. Votre application doit offrir un parfait équilibre entre les avantages de l’extensibilité de la répartition des entités sur plusieurs partitions et les avantages d’accès aux données du regroupement des entités en une seule partition. Grâce au regroupement des entités en partitions, il est possible de mener des opérations atomiques sur les entités d’une même partition, car une partition existe sur un serveur unique.

Les partitions affectent l’extensibilité et l’équilibrage de charge de chacun des services de stockage comme suit :

- **Blobs** : la clé de partition d’un blob est le nom du conteneur +  le nom du blob. Autrement dit, chaque blob a sa propre partition. Les blobs peuvent donc être répartis sur plusieurs serveurs afin de d’offrir un accès horizontal. Si les blobs peuvent être regroupés de manière logique dans des conteneurs, ce regroupement n’a aucune incidence sur le partitionnement.

- **Messages** : la clé de partition d’un message est le nom de la file d’attente : tous les messages d’une file d’attente sont regroupés en une seule partition et servis par un seul serveur. Plusieurs files d’attente peuvent être traitées par différents serveurs pour équilibrer la charge, quel que soit le nombre de files d’attente du compte de stockage.

- **Entités** : la clé de partition d’une entité est le nom de la table + la clé de partition, cette dernière correspondant à la valeur de la propriété **PartitionKey** requise et définie par l’utilisateur pour l’entité.

	Les entités d’une même table peuvent avoir les mêmes valeurs de clé de partition, auquel cas elles sont regroupées dans la même partition. Une application peut effectuer des transactions atomiques par lots sur les entités d’une même partition, car elles sont issues du même serveur.

	Les entités qui se trouvent dans la même table mais qui appartiennent à des partitions différentes peuvent être réparties sur plusieurs serveurs, ce qui permet de disposer d’une table plus extensible.

## Voir aussi

- [Tarification Azure Storage](http://azure.microsoft.com/pricing/details/storage/)
- [Azure Storage : service de stockage sur le cloud à haute disponibilité et à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Abonnement Azure et limites, quotas et contraintes du service](azure-subscription-service-limits.md)


<!--HONumber=54-->