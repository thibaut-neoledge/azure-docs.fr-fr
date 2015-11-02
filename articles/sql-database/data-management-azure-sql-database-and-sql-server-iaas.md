<properties 
	pageTitle="Présentation d’Azure SQL Database et de SQL Server dans les machines virtuelles Azure | Microsoft Azure" 
	description="Présentation d'Azure SQL Database et de SQL Server dans les machines virtuelles Azure. Passez en revue les motivations métier courantes permettant de déterminer la technologie SQL qui convient le mieux à votre application." 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="selcint"/>

# Présentation d’Azure SQL Database et de SQL Server dans les machines virtuelles Azure

Microsoft Azure vous offre deux options pour l’hébergement de SQL Server : [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) et [SQL Server dans les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Dans cet article, nous allons commencer par examiner comment chaque option s'adapte globalement dans la plateforme de données de Microsoft avant de passer à des discussions détaillées en fonction des besoins qui motivent votre choix. Que vous privilégiiez les économies ou une administration minimale, cet article peut vous aider à déterminer l'approche adaptée, en présentant les avantages de chaque option par rapport aux besoins essentiels de l'entreprise.

- [Plateforme de données de Microsoft](#platform)
- [Étude détaillée d'Azure SQL Database et de SQL Server dans les machines virtuelles Azure](#close)	
- [Motivations commerciales pour choisir entre Azure SQL Database et SQL Server dans les machines virtuelles Azure](#business)	
	- [Coût](#cost)
		- [Facturation et licences de base](#billing)	
		- [Calcul du coût total de l’application](#appcost)	
	- [Administration](#admin)	
	- [Contrat de niveau de service (SLA)](#sla)	
	- [Délai de commercialisation](#market)	
- [Résumé](#summary)	
- [Remerciements](#ack)	
- [Ressources supplémentaires](#resources)	


##<a name="platform"></a>Plateforme de données de Microsoft

L'une des premières choses à comprendre dans toute discussion comparant les bases de données Azure et SQL Server locales est que vous pouvez tout utiliser. La plateforme de données de Microsoft utilise la technologie SQL Server et la rend disponible sur les machines physiques locales, les environnements de cloud privé, les environnements de cloud privé hébergé par un tiers et le cloud public. Cela vous permet de répondre à des besoins spécifiques et différents via une combinaison de déploiements locaux et hébergés dans le cloud, tout en utilisant le même ensemble de produits serveur, d'outils de développement et de savoir-faire dans ces environnements.

   ![][1]

Comme indiqué sur le diagramme, chaque offre peut être caractérisée par le niveau d'administration dont vous disposez sur l'infrastructure (sur l'axe des X) et par le degré d'efficacité opérationnelle obtenue par consolidation au niveau de la base de données et par automatisation (sur l'axe des Y).

Lorsque vous concevez une application, quatre options de base sont disponibles pour l'hébergement de sa partie SQL Server :

- SQL Server sur des machines physiques non virtualisées 
- SQL Server sur des machines virtualisées locales (cloud privé)
- SQL Server dans les machines virtuelles Azure (cloud public)
- Azure SQL Database (cloud public)

Les sections suivantes offrent de plus amples informations sur ces deux derniers éléments : Azure SQL Database et SQL Server dans les machines virtuelles Azure. Nous allons également explorer les motivations commerciales courantes permettant de déterminer l'option qui convient le mieux à votre application.

##<a name="close"></a>Étude détaillée d'Azure SQL Database et de SQL Server dans les machines virtuelles Azure

La **base de données SQL Microsoft Azure** est une base de données relationnelle en tant que service, qui entre dans la catégorie de la *plateforme en tant que service (PaaS)*. Azure SQL Database repose sur du matériel et des logiciels standardisés, qui sont détenus, hébergés et gérés par Microsoft. Avec SQL Database, vous pouvez développer directement sur le service à l'aide de fonctionnalités intégrées. Lorsque vous utilisez SQL Database, vous payez à l'utilisation avec les options de mise à l'échelle pour plus de puissance.

**SQL Server dans les machines virtuelles Azure** entre dans la catégorie de l’*infrastructure en tant que service (IaaS)* et vous permet d’exécuter SQL Server sur une machine virtuelle dans le cloud. Comme pour Azure SQL Database, elle repose sur du matériel standardisé détenu, hébergé et géré par Microsoft. Lorsque vous utilisez SQL Server sur une machine virtuelle, vous pouvez appliquer votre propre licence SQL Server pour Azure ou utiliser l'une des images préconfigurées de SQL Server dans le portail Azure.

En général, ces deux options SQL sont optimisées à différentes fins :

- **Azure SQL Database** est optimisé pour l’approvisionnement et la gestion de nombreuses bases de données à moindre coût. Il réduit les coûts d'administration en cours, car vous n'avez pas à gérer les machines virtuelles, le système d'exploitation ni le logiciel de base de données, notamment les mises à niveau, la haute disponibilité et les sauvegardes. En règle générale, SQL Database peut augmenter considérablement le nombre de bases de données gérées par une seule personne du service informatique ou du développement.
- **SQL Server dans les machines virtuelles Azure** est optimisé pour étendre les applications SQL Server locales sur Azure dans un scénario hybride ou déployer une application existante sur Azure dans un scénario de migration ou de développement/test. Un scénario hybride, par exemple, consiste à conserver les réplicas de bases de données secondaires dans Azure via[ Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Avec SQL Server dans les machines virtuelles Azure, vous disposez des droits d'administration complets sur une instance dédiée de SQL Server et sur une machine virtuelle dans le cloud. C'est le choix idéal lorsqu'une organisation possède déjà les ressources informatiques disponibles pour maintenir les machines virtuelles. Avec SQL Server sur une machine virtuelle, vous pouvez créer un système hautement personnalisé pour répondre aux demandes de performances spécifiques et aux exigences de disponibilité de votre application.

Le tableau suivant résume les principales caractéristiques d’Azure SQL Database et de SQL Server dans les machines virtuelles Azure :

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Azure SQL Database</th>
   <th align="left" valign="middle">SQL Server dans les machines virtuelles Azure</th>
   
</tr>
<tr>
   <td valign="middle"><p><b>Idéal pour</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Nouvelles applications conçues pour le cloud qui ont des contraintes de temps de développement et de marketing. 
          <li type=round>Applications qui nécessitent une haute disponibilité automatique, une solution de récupération d'urgence et des mécanismes de mise à niveau intégrés.
          <li type=round>Si vous avez des centaines ou des milliers de bases de données et que vous ne souhaitez pas gérer les paramètres sous-jacents du système d'exploitation, du matériel et de la configuration. 
         <li type=round>Applications utilisant des modèles de montée en charge.
         <li type=round>Bases de données jusqu'à 500&#160;Go.
         <li type=round>Création d'applications logicielles en tant que service.
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Applications existantes qui requièrent une migration rapide vers le cloud avec un minimum de modifications.
      <li type=round>Applications SQL Server qui nécessitent l'accès aux ressources locales (par exemple, Active Directory) à partir d'Azure via un tunnel sécurisé. 
      <li type=round>Si vous avez besoin d'un environnement informatique personnalisé avec des droits d'administration complets.
      <li type=round>Scénarios de développement et de test rapides lorsque vous ne souhaitez pas acheter du matériel SQL Server local hors production.
      <li type=round>Récupération d’urgence pour les applications SQL Server locales utilisant la <a href="http://msdn.microsoft.com/library/jj919148.aspx">sauvegarde sur Azure Storage</a> ou les <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">réplicas AlwaysOn dans les machines virtuelles Azure</a>.
      <li type=round>Bases de données volumineuses de plus de 1&#160;To.
      </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Ressources</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>Vous ne souhaitez pas utiliser les ressources informatiques pour le support et la maintenance de l'infrastructure sous-jacente.
       <li type=round>Vous souhaitez vous concentrer sur la couche Application.
       </ul></td>
   <td valign="middle"><ul><li type=round>Vous disposez des ressources informatiques pour le support et la maintenance.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Coût total de possession</b></p></td>
   <td valign="middle"><ul><li type=round>Élimine les coûts matériels. Réduit les coûts d'administration.</ul></td>
   <td valign="middle"><ul><li type=round>Élimine les coûts matériels. </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Continuité des activités</b></p></td>
   <td valign="middle"><ul><li type=round>Outre les fonctionnalités intégrées d'infrastructure de tolérance de panne, Azure SQL Database fournit des fonctionnalités telles que la limite de restauration dans le temps, la géorestauration et la géoréplication afin d'améliorer la continuité de l'activité. Pour plus d’informations, consultez la rubrique <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Continuité des activités de l'entreprise dans Azure SQL Database</a>.</ul></td>
   <td valign="middle"><ul><li type=round>SQL&#160;Server dans les machines virtuelles Azure vous permet de configurer une solution haute disponibilité et de récupération d'urgence pour les besoins spécifiques de votre base de données. Vous pouvez donc disposer d'un système hautement optimisé pour votre application. Vous pouvez tester et exécuter des basculements par vous-même si nécessaire. Pour plus d’informations, consultez <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure</a>.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Cloud hybride</b></p></td>
   <td valign="middle"><ul><li type=round>Votre application locale peut accéder aux données d’Azure SQL Database.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Avec SQL&#160;Server dans les machines virtuelles Azure, vous pouvez disposer d'applications qui s'exécutent en partie dans le cloud et en partie localement. Par exemple, vous pouvez étendre le réseau local et le domaine Active Directory sur le cloud via <a href="https://azure.microsoft.com/documentation/articles/virtual-networks-overview/">Azure Network Services</a>. En outre, vous pouvez stocker des fichiers de données locaux dans Azure Storage en utilisant les <a href="http://msdn.microsoft.com/library/dn385720.aspx">fichiers de données SQL Server dans la fonctionnalité Azure</a>. Pour plus d’informations, consultez la rubrique <a href="http://msdn.microsoft.com/library/dn606154.aspx">Introduction au cloud hybride SQL Server&#160;2014</a>.
      <li type=round>Prise en charge de la récupération d’urgence pour les applications SQL Server locales utilisant la <a href="http://msdn.microsoft.com/library/jj919148.aspx">sauvegarde sur Azure Storage</a> ou les <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">réplicas AlwaysOn dans les machines virtuelles Azure</a>.
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>Motivations commerciales pour choisir entre Azure SQL Database et SQL Server dans les machines virtuelles Azure

###<a name="cost"></a>Coût

Que vous soyez une start-up à court de liquidités ou une équipe dans une société établie qui subit de fortes contraintes budgétaires, le manque de capitaux est souvent le principal moteur pour décider du mode d'hébergement des bases de données. Dans cette section, nous aborderons d’abord les notions de base sur la facturation et les licences dans Azure en lien avec ces deux options de base de données relationnelles : Azure SQL Database et SQL Server dans les machines virtuelles Azure. Nous verrons ensuite comment calculer le coût total de l'application.

####<a name="billing"></a>Facturation et licences de base

**Azure SQL Database** est vendu aux clients sous la forme d’un service sans licence, alors que SQL Server dans les machines virtuelles Azure nécessite une licence SQL Server classique.

Actuellement, **Azure SQL Database** est disponible selon plusieurs niveaux de service. Pour les niveaux de service De base, Standard et Premium, vous êtes facturé à l'heure à un taux fixe en fonction du niveau de service et du niveau de performances choisis. Les niveaux de service De base, Standard et Premium sont conçus pour fournir des performances prévisibles avec plusieurs niveaux de performances pour répondre aux exigences de pic de votre application. Vous pouvez changer de niveau de service et de niveau de performances pour répondre aux besoins de débit variés de votre application. Pour obtenir des informations à jour sur les niveaux de service pris en charge, consultez la rubrique [Niveaux de service d'Azure SQL Database (éditions)](sql-database-service-tiers.md).

Avec **Azure SQL Database**, le logiciel de base de données est automatiquement configuré, corrigé et mis à niveau par Microsoft Azure dans les centres de données du monde entier. Vous bénéficiez ainsi de coûts d'administration réduits. En outre, ses fonctionnalités de [sauvegarde intégrée](http://msdn.microsoft.com/library/azure/jj650016.aspx) vous permettent de réaliser des économies importantes, notamment lorsque vous avez un grand nombre de bases de données. Lorsque vous utilisez Azure SQL Database, vous n’êtes pas facturé pour des requêtes individuelles qui s’exécutent dans Azure SQL Database ni pour le trafic Internet entrant. Vous êtes facturé pour [le trafic internet sortant](http://azure.microsoft.com/pricing/details/data-transfers/). Si votre base de données présente un volume élevé de transactions et doit prendre en charge simultanément de nombreux utilisateurs, nous vous recommandons d'utiliser les niveaux de service Premium plutôt que De base ou Standard.

Avec **SQL Server dans les machines virtuelles Azure**, vous utilisez la gestion de licences SQL Server traditionnelle. Vous pouvez utiliser l'image SQL Server fournie par la plateforme ou utiliser votre licence SQL Server dans Azure. Lorsque vous utilisez les images SQL Server fournies par la plateforme, les coûts dépendent de la taille de la machine virtuelle et de la version choisie de SQL Server. En fait, vous payez le coût de gestion de licence SQL Server par minute, la gestion de licence Windows Server par minute et le coût du stockage Azure. L'option de facturation par minute vous permet d'utiliser SQL Server aussi longtemps que vous en avez besoin sans acheter de licence SQL Server complète. Si vous utilisez votre propre licence SQL Server sur Azure, seuls les coûts de calcul et de stockage Azure vous sont facturés. Pour plus d’informations, consultez [License Mobility via Software Assurance sur Azure](http://azure.microsoft.com/pricing/license-mobility/).

####<a name="appcost"></a>Calcul du coût total de l’application

Lorsque vous utilisez une plateforme cloud, le coût d'exécution de votre application inclut principalement les coûts de développement et d'administration, ainsi que les coûts de service nécessaires pour la plateforme de cloud public.

Voici le calcul de coûts détaillé pour votre application qui s'exécute dans Azure SQL Database et SQL Server dans les machines virtuelles Azure :

**Avec Azure SQL Database :**

*Coût total de l’application = coûts d’administration très réduits + coûts de développement logiciel + coûts du service Azure SQL Database*

**Avec SQL Server dans les machines virtuelles Azure :**

*Coût total de l’application = coûts réduits de modification/développement logiciel + coûts d’administration + coûts de licence SQL Server et Windows Server + coûts Azure Storage*

> [AZURE.IMPORTANT]actuellement, Base de données SQL Azure ne prend pas en charge toutes les fonctionnalités de SQL Server. Pour obtenir des informations de comparaison détaillées, voir [Instructions et limitations d’Azure SQL Database](http://msdn.microsoft.com/library/azure/ff394102.aspx). Tenez-en compte si vous souhaitez déplacer une base de données existante vers Azure SQL Database, car il faut parfois compter un budget supplémentaire pour la nouvelle conception de la base de données. Azure SQL Database est l'offre Microsoft de plateforme en tant que service. Lorsque vous migrez une application SQL Server locale vers Azure SQL Database, nous vous recommandons de mettre à jour l'application pour bénéficier de tous les avantages de l'offre de plateforme en tant que service. Par exemple, démarrez à l’aide des [sites web Azure](http://azure.microsoft.com/documentation/services/websites/) ou des [services cloud Azure](http://azure.microsoft.com/services/cloud-services/) sur la couche Application pour une meilleure rentabilité. En outre, validez votre application dans différents niveaux de service d’Azure SQL Database et cherchez celui qui répond le mieux aux besoins de votre application. Ce processus vous permet d'obtenir de meilleurs résultats de performances et réduit les coûts. Pour plus d'informations, consultez [Niveaux de performances et de service d'Azure SQL Database](sql-database-service-tiers.md).

Pour une estimation détaillée des coûts, utilisez la [calculatrice de tarification Azure](http://azure.microsoft.com/pricing/calculator/).

Pour plus d'informations sur la tarification, consultez les ressources suivantes :

- [Détails de tarification - Azure SQL Database](http://azure.microsoft.com/pricing/details/sql-database/) 
- [Détails de tarification - Virtual Machines](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [Détails de tarification - SQL Server dans les machines virtuelles Azure](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Détails de tarification - Windows Server dans les machines virtuelles Azure](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>Administration

Si vous êtes déjà débordé, vous ne souhaitez sans doute pas vous charger en plus de l'administration du serveur et de la base de données. Pour de nombreuses entreprises, la décision d'adopter un service cloud repose sur la possibilité de se décharger des tâches d'administration complexes. Avec **Azure SQL Database**, Microsoft administre le matériel physique, comme les disques durs, les serveurs et le stockage, réplique automatiquement toutes les données pour assurer la haute disponibilité, configure et met à niveau le logiciel de base de données, gère l’équilibrage de charge et procède au basculement transparent en cas de défaillance du serveur. Vous pouvez continuer à administrer vos instances Azure SQL Database, mais sans contrôler les ressources physiques de l'instance SQL Server sous-jacente et de la plateforme Azure. Par exemple, vous pouvez administrer les bases de données et les connexions, paramétrer l'index et optimiser les requêtes, mais vous ne pouvez pas administrer les tables système et la gestion de groupe de fichiers. Pour plus d’informations, consultez [Instructions et limitations de la Base de données SQL Azure](http://msdn.microsoft.com/library/ff394102.aspx).

En revanche, vous pouvez avoir du savoir-faire en interne et le souhait de conserver le contrôle de l'emplacement de la base de données jusqu'à la machine. Avec **SQL Server s’exécutant dans des machines virtuelles Azure**, vous avez le contrôle intégral du système d’exploitation et de la configuration de l’instance SQL Server. Avec une machine virtuelle, c'est à vous de décider quand mettre à jour/à niveau le système d'exploitation et les logiciels de base de données et installer d'autres logiciels tels que les outils de sauvegarde et de protection antivirus. En outre, vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Par exemple, Azure vous permet de modifier la taille d'une machine virtuelle en cours d'exécution en fonction des besoins. Pour plus d'informations, consultez [Tailles de machines virtuelles et de services cloud pour Azure](../virtual-machines/virtual-machines-size-specs.md).

###<a name="sla"></a>Contrat de Niveau de Service (SLA)

Pour certains d'entre nous, satisfaire aux obligations de temps d'exécution d'un contrat de niveau de service (SLA) est la priorité absolue. Dans cette section, nous allons examiner les implications du contrat SLA pour chaque option d'hébergement de base de données.

Pour **Azure SQL Database**, avec les niveaux de service De base, Standard et Premium, Microsoft fournit un contrat SLA dont la disponibilité est de 99,99 %. Notez que le contrat SLA de disponibilité concerne la capacité à se connecter à la base de données. Autrement dit, il s'agit d'un contrat SLA de niveau base de données. Pour obtenir les dernières informations sur les contrats SLA, voir [Contrats de niveau de service](http://azure.microsoft.com/support/legal/sla/). Pour obtenir les dernières informations sur les niveaux de service d'Azure SQL Database (éditions) et les plans de continuité des activités d'entreprise pris en charge, voir [Niveaux de service d'Azure SQL Database](sql-database-service-tiers.md).

Pour les **machines virtuelles hébergées dans Azure**, Microsoft fournit un contrat SLA dont la disponibilité est de 99,95 %. Cette disponibilité concerne la machine virtuelle et non le processus qui s’exécute dans la machine virtuelle (comme SQL Server). Le [contrat SLA de machine virtuelle](http://www.microsoft.com/download/details.aspx?id=38427) nécessite l’hébergement d’au moins deux machines virtuelles dans un groupe à haute disponibilité. Avec cette configuration, Azure garantit qu'au moins une des machines virtuelles sera disponible 99,95 % du temps. Pour la base de données haute disponibilité (HA) dans des machines virtuelles, vous devez configurer une des options de haute disponibilité prises en charge dans SQL Server, comme les groupes de disponibilité AlwaysOn. Notez que la configuration AlwaysOn dans Azure nécessite une configuration manuelle et une gestion, et que vous payez pour chaque groupe supplémentaire exploité.


###<a name="market"></a>Délai de commercialisation

**Azure SQL Database** est la solution idéale pour les applications conçues pour le cloud lorsque la productivité des développeurs et la rapidité de mise sur marché sont essentielles. Avec les fonctionnalités de programmation comme le DBA, il est parfait pour les architectes et les développeurs du cloud, car il tempère la nécessité de gérer le système d'exploitation et la base de données sous-jacents. Il permet aux développeurs de comprendre et de configurer les tâches liées à la base de données. Par exemple, vous pouvez utiliser l’[API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) et des [applets de commande PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) pour automatiser et gérer des opérations d’administration pour des milliers de bases de données. Avec la [mise à l’échelle élastique](sql-database-elastic-pool.md) dans le cloud, vous pouvez facilement vous concentrer sur la couche Application et mettre votre application sur le marché plus rapidement.

**SQL Server s’exécutant sur les machines virtuelles Azure** est idéal si vos applications, existantes et nouvelles, accèdent à toutes les fonctionnalités d’une instance SQL Server et les contrôlent, et lorsque vous souhaitez migrer telles quelles des applications et des bases de données locales sur le cloud. Étant donné que vous n'avez pas besoin de modifier la présentation, l'application et les couches de données, vous économisez en temps et en budget sur le remaniement de votre solution existante. Vous pouvez ainsi vous concentrer sur la migration de tous les packages de votre solution vers les machines virtuelles et effectuer certaines optimisations de performances requises par la plateforme Azure. Pour plus d'informations, consultez [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md).

##<a name="summary"></a>Résumé

Dans cet article, nous avons exploré Azure SQL Database et SQL Server dans les machines virtuelles Azure. Nous avons également abordé les principales motivations commerciales susceptibles d'affecter le choix entre les deux.

Voici un résumé des suggestions à prendre en compte pour utiliser l'un ou l'autre :

Choisissez **Azure SQL Database** dans les cas suivants :

- Vous développez des applications entièrement nouvelles dans le cloud ou vous souhaitez migrer votre base de données SQL Server existante vers Azure, mais votre base de données n'utilise pas l'une des fonctionnalités non prises en charge dans Azure SQL Database. Pour plus d’informations, voir [Guide de référence Transact-SQL d’Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336281.aspx). Cette approche offre les avantages d'un service cloud entièrement géré et assure une mise sur le marché rapide.

- Vous souhaitez que Microsoft effectue des opérations de gestion courantes sur vos bases de données et vous avez besoin d'une plus grande disponibilité offerte par le biais des contrats SLA pour les bases de données. Cette approche permet de réduire les coûts d'administration tout en garantissant une disponibilité de la base de données.

    [Créer votre première base de données SQL Azure](sql-database-get-started.md)


Choisissez **SQL Server s’exécutant sur les machines virtuelles Azure** dans les cas suivants :

- Vous disposez d'applications locales et vous ne souhaitez plus assurer la gestion de votre propre matériel ou vous envisagez des solutions hybrides. Cette approche vous permet d'accéder plus rapidement à une capacité élevée de la base de données et connecte également vos applications locales au cloud via un tunnel sécurisé.

- Vous disposez de ressources informatiques, vous avez besoin de droits d'administration complets sur SQL Server et de la compatibilité complète avec SQL Server en local (par exemple, certaines fonctionnalités n'existent pas dans Azure SQL Database). Cette approche vous permet de réduire les coûts de développement ou de modifications des applications existantes avec la possibilité d'exécuter la plupart des applications. En outre, elle fournit le contrôle intégral de la machine virtuelle, du système d'exploitation et de la configuration de base de données.

    [Approvisionnement d’une machine virtuelle SQL Server dans Azure](virtual-machines-provision-sql-server.md)

> [AZURE.NOTE]Voulez-vous essayer SQL Server 2016 CTP2 ? Inscrivez-vous à Microsoft Azure, puis allez [ici](http://aka.ms/sql2016vm "ici") pour créer une machine virtuelle avec SQL Server 2016 CTP2 déjà installé.


##<a name="ack"></a>Remerciements

Cet article du groupe Microsoft Cloud and Enterprise Content Services a été conçu avec l'aide de nombreuses personnes de la communauté Microsoft.

**Auteur :** Selcin Turkarslan

**Contribution technique :** Conor Cunningham

**Réviseurs techniques :** Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**Réviseurs rédactionnels :** Heidi Steen, Maggie Sparkman.

Merci à tous pour votre participation !

##<a name="resources"></a>Ressources supplémentaires 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Description</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN&#160;: Azure SQL Database</a></p>
<p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/">Présentation de SQL Server dans des machines virtuelles Azure</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com&#160;: Azure SQL Database</a></p></td>
   <td valign="middle">Liens vers la documentation de la bibliothèque.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-application-patterns-and-development-strategies/">Modèles d’application et stratégies de développement pour SQL Server dans les machines virtuelles Azure</p></td>
   <td valign="middle">Cet article décrit les modèles d'application courants qui s'appliquent à SQL Server dans les machines virtuelles Azure et dans les scénarios hybrides incluant Azure SQL Database. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Bloc applicatif de gestion des erreurs temporaires de Microsoft Enterprise Library</p></td>
   <td valign="middle">Cette bibliothèque permet aux développeurs de renforcer leurs applications s'exécutant sur Azure SQL Database en ajoutant une logique de gestion robuste des erreurs temporaires. Les erreurs temporaires sont des erreurs qui se produisent en raison d'une condition temporaire, par exemple des problèmes de connectivité réseau ou d'indisponibilité de service. Azure SQL Database étant un service mutualisé, il est important de gérer ces erreurs afin de réduire les interruptions de service. </td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png

<!---HONumber=Oct15_HO4-->