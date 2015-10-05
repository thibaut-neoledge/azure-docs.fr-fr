<properties
	pageTitle="Aide relative à la charge de travail Site Recovery | Microsoft Azure" 
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération des ordinateurs virtuels et services physiques situés en local vers Azure et vers un site local secondaire." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="09/21/2015" 
	ms.author="pratshar"/>

# Aide relative à la charge de travail Site Recovery

Azure Site Recovery permet aux clients de déployer des solutions de disponibilité prenant en charge l'application. Qu'il s'agisse d’applications Windows Server ou Linux, d'applications d'entreprise Microsoft internes ou d'offres d'autres fournisseurs, vous pouvez utiliser Azure Site Recovery pour activer la récupération d'urgence, les environnements de développement/test à la demande ou les migrations cloud.

Microsoft a une grande expertise et expérience dans le développement d'applications d'entreprise de pointe, telles que SharePoint, Exchange, Dynamics, SQL Server et Active Directory. Microsoft travaille également en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat, pour s'assurer que leurs applications et services fonctionnent correctement sur les plateformes Microsoft, y compris Azure et Hyper-V. Nous avons tiré parti de ces avantages uniques pour acquérir une compréhension approfondie des exigences de chaque application en termes de disponibilité pour vous permettre de déployer des solutions de disponibilité et de récupération d'urgence de pointe qui peuvent être personnalisées pour chaque application.


##Fonctionnalités clés
Les fonctionnalités d'Azure Site Recovery ont été conçues avec une protection/récupération de niveau application à l'esprit :

- Réplication presque synchrone avec des RPO de 30 secondes qui répondent aux besoins des applications les plus critiques.
- Captures instantanées cohérentes de l'application pour les applications uniques ou multiniveau.
- Intégration avec réplication de niveau application. Profitez des meilleures offres de niveau application, notamment la réplication Active Directory, SQL Always On, les groupes de disponibilité Exchange Database et Oracle Data Guard
- Les plans de récupération flexibles permettent la récupération de la pile de l'application entière en un seul clic, y compris l'exécution de scripts externes ou même des actions manuelles. 
- La gestion réseau avancée dans ASR et Azure automatise toutes les configurations de mise en réseau spécifiques à votre application : réserver les adresses IP, configurer les équilibreurs de charge ou utiliser Traffic Manager de Microsoft pour les commutations réseau à RTO faible.
- Bibliothèque d'automatisation riche qui fournit des scripts spécifiques à l'application, prêts pour la production. Téléchargez-les et intégrez-les dans vos solutions ASR.


##Résumé de l'aide relative à la charge de travail

Les technologies de réplication ASR sont compatibles avec n’importe quelle application en cours d’exécution sur une machine virtuelle. Nous avons effectué des tests supplémentaires en partenariat avec les équipes chargées des produits d’application, afin d’assurer la prise en charge complète de chaque application.

**Charge de travail** | <p>**Répliquer des machines virtuelles Hyper-V**</p> <p>**(sur un site secondaire)**</p> | <p>**Répliquer des machines virtuelles Hyper-V**</p> <p>**(sur Azure)**</p> | <p>**Répliquer des machines virtuelles VMware**</p> <p>**(sur un site secondaire)**</p> | <p>**Répliquer des machines virtuelles VMware**</p><p>**(sur Azure)****</p> ---|---|---|---|--- Active Directory, DNS | Y | Y | Y | Y Web apps (IIS, SQL) | Y | Y | Y | Y SCOM | Y | Y | Y | Y Sharepoint | Y | Y | Y | Y <p>SAP</p><p>Répliquer site SAP sur Azure pour non cluster</p> | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) Exchange (non DAG) | Y | Prochainement | Y | Y Bureau à distance/VDI | Y | Y | Y | N/A <p>Linux</p> <p>(système d'exploitation et applications)</p> | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) Dynamics AX | Y | Y | Y | Y Dynamics CRM | Y | Prochainement | Y | Prochainement Oracle | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) Windows File Server | Y | Y | Y | Y

##Active Directory et DNS

Toutes les applications d'entreprise telles que SharePoint, Dynamics AX et SAP dépendent du bon fonctionnement d'Active Directory et de l'infrastructure DNS. Lors de la création d'une solution de récupération d'urgence pour une application de ce type, il est important de protéger et de restaurer Active Directory avant que les autres composants de l'application ne soient à nouveau disponibles en cas d'interruption.

À l'aide d'Azure Site Recovery, vous pouvez créer un plan de récupération d'urgence automatisé complet pour votre AD. Si vous avez un AD pour plusieurs applications, comme SharePoint et SAP dans votre site principal et que vous décidez de basculer le site complet, vous pouvez commencer par basculer Active Directory à l'aide du plan de récupération d'urgence d'Active Directory, puis basculer les autres applications à l'aide de plans de récupération d'urgence spécifiques à chaque application.

Consultez le document lié pour des instructions détaillées sur le [déploiement d'Azure Site Recovery pour AD](http://aka.ms/asr-ad)

##SQL Server
Microsoft SQL Server est la base de nombreuses applications d'entreprise internes, tierces et professionnelles personnalisées qui s'exécutent dans le centre de données local d'un client. Les applications comme SharePoint, Dynamics et SAP utilisent SQL Server pour fournir des services de données. Les technologies Azure Site Recovery et SQL Server HA/DR sont complémentaires et peuvent être utilisées pour fournir une protection de bout en bout pour les applications d'entreprise multiniveau. Azure Site Recovery offre les avantages suivants pour les environnements SQL Server :

- Protéger facilement les serveurs SQL autonomes ou en cluster sur Azure ou un site secondaire. Étendre une solution de récupération d'urgence simple et économique à toute version et édition de SQL Server.
- Intégrer avec des groupes de disponibilité SQL Always On, une solution de récupération d'urgence de pointe et gérer l'opération de basculement/restauration avec des plans de récupération ASR.
- Créer des plans de récupération de bout en bout pour la pile de l'application entière, notamment les bases de données SQL.
- Utiliser Azure Site Recovery pour mettre à l'échelle SQL Server lors des charges maximales en les décomposant en tailles de VM IaaS plus volumineuses dans Azure.
- Utiliser Azure Site Recovery pour créer des copies d'essai de SQL Server dans Azure ou sur un site secondaire. Utiliser cette copie pour l'analyse et les vérifications de conformité des données sans impact sur l'environnement de production.

Consultez le document lié pour des instructions détaillées sur le [déploiement d'Azure Site Recovery pour SQL](http://aka.ms/asr-sql)

##SharePoint
SharePoint est une application populaire qui permet aux organisations de collaborer en fournissant des portails intranet, la gestion de documents et de fichiers, des réseaux sociaux, des fonctionnalités de recherche de sites web et d'entreprise. Il s'agit également d'une plateforme d'application permettant de déployer facilement des flux de travail et des applications personnalisés. À l'aide d'Azure Site Recovery pour SharePoint, vous pouvez :

- Éliminer le besoin et le coût de l'infrastructure associée à une batterie de secours pour la récupération d'urgence. Avec ASR, vous pouvez activer la protection de la batterie entière (niveaux web, application et base de données) sur Azure ou un site secondaire.
- Simplifier la gestion et le déploiement de l'application. Les mises à jour déployées sur le site principal sont automatiquement répliquées et disponibles lorsque la batterie de serveurs est récupérée sur le site secondaire. Cela élimine la complexité de gestion consistant à conserver une batterie de secours à jour et réduit le coût total de possession.
- Facilitez le développement d'applications SharePoint et leur test en créant une copie de type production à la demande pour le test et le débogage.
- Simplifiez votre chemin d'accès au cloud en utilisant ASR pour migrer les déploiements SharePoint vers Azure.

Consultez le document lié pour des instructions détaillées sur le [déploiement d'Azure Site Recovery pour Sharepoint](http://aka.ms/asr-sharepoint)


##Dynamics AX
Microsoft Dynamics AX est une solution ERP de pointe facile à apprendre et à utiliser, de sorte que vous pouvez offrir de la valeur plus rapidement, tirer parti des opportunités commerciales et dynamiser l'intervention et l'innovation de la part des utilisateurs dans toute l'organisation.

- Avec ASR, vous pouvez activer la protection de l'ensemble de Dynamics Ax (niveau web et AOS, niveaux bases de données, SharePoint) sur Azure ou un site secondaire.
- Simplifiez votre chemin d'accès au cloud en utilisant ASR pour migrer les déploiements Dynamics Ax vers Azure.
- Facilitez le développement d'applications Dynamics et leur test en créant une copie de type production à la demande pour le test et le débogage.

Consultez le document lié pour des instructions détaillées sur le [déploiement d'Azure Site Recovery pour Dynamics AX](http://aka.ms/asr-dynamics)

## RDS 
Windows Server Remote Desktop Services (RDS) accélère et étend les déploiements de bureau et d'applications sur n'importe quel appareil, ce qui améliore l'efficacité des travailleurs distants tout en sécurisant la propriété intellectuelle et en simplifiant la conformité avec les réglementations. RDS active l'infrastructure de bureau virtuel (VDI), les bureaux basés sur session et les applications, permettant aux utilisateurs de travailler n'importe où.

Avec ASR, vous pouvez activer la protection de bureaux virtuels en pool gérés ou non gérés sur un site secondaire, ainsi que des applications et des sessions à distance sur un site secondaire ou sur Azure.

Consultez le document lié pour des instructions détaillées sur le [déploiement d'Azure Site Recovery pour RDS/VDI](http://aka.ms/asr-rds)


## Microsoft Exchange
Microsoft Exchange est le logiciel par défaut utilisé par les entreprises pour héberger leurs services de messagerie et de courrier électronique. Exchange garantit l'accès à la communication entre l'ordinateur, le téléphone ou le navigateur, tout en offrant une fiabilité, une facilité de gestion et une protection des données inégalées.

Microsoft Exchange en mode natif prend en charge une haute disponibilité de niveau entreprise et des solutions de récupération d'urgence. Les technologie Groupes de disponibilité Exchange Database et Azure Site Recovery sont complémentaires.

- Les Groupes de disponibilité Exchange Database sont l'option de déploiement recommandée pour obtenir la meilleure récupération d'urgence pour les déploiements Exchange. Les plans de récupération ASR peuvent inclure des Groupes de disponibilité Exchange Database pour orchestrer les basculements de ceux-ci entre les sites.
- Pour les déploiements de petite taille, comme un serveur unique ou des serveurs non clusterisés, Azure Site Recovery peut protéger et basculer des serveurs individuels sur Azure ou un site secondaire.

Consultez le document lié pour des instructions détaillées sur le [déploiement d'Azure Site Recovery pour Exchange](http://aka.ms/asr-exchange)

## SAP

SAP est un logiciel ERP de pointe utilisé par la plupart des organisations dans le monde entier. SAP est souvent considéré comme une des applications les plus critiques au sein des entreprises. L'architecture et les opérations de ces applications sont généralement très complexes et il est très important de vous assurer que vous remplissez les conditions sur BCDR.

- Avec ASR, vous pouvez activer la protection du déploiement SAP entier avec plusieurs couches sur Azure ou un site secondaire.
- Simplifiez votre chemin d'accès au cloud en utilisant ASR pour migrer les déploiements SAP vers Azure.
- Facilitez le développement d'applications SAP et leur test en créant une copie de type production à la demande pour le test et le débogage.

Consultez le document lié pour des instructions détaillées sur le [déploiement d'Azure Site Recovery pour SAP NetWeaver](http://aka.ms/asr-sap)

<!---HONumber=Sept15_HO4-->