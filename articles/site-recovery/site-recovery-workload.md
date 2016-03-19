<properties
	pageTitle="Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?" 
	description="Azure Site Recovery protège vos charges de travail et applications en coordonnant la réplication, le basculement et la récupération des machines virtuelles et serveurs physiques locaux sur Azure, ou sur un site local secondaire." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/01/2015" 
	ms.author="raynew"/>

# Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?

Azure Site Recovery permet aux clients de déployer des solutions de disponibilité tenant compte des applications qui contribuent à la stratégie de continuité d’activité et de récupération d’urgence (BCDR) de votre entreprise. Qu’il s’agisse d’applications Windows Server ou Linux, d’applications d’entreprise Microsoft ou d’offres d’autres fournisseurs, vous pouvez utiliser Azure Site Recovery pour activer la récupération d’urgence, les environnements de développement et de test à la demande ou la migration cloud.

Site Recovery s’intègre avec les applications Microsoft, y compris SharePoint, Exchange, Dynamics, SQL Server et Active Directory. Microsoft travaille également en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat, pour s’assurer que leurs applications et services fonctionnent correctement sur les plateformes Microsoft telles qu’Azure et Hyper-V. Vous pouvez personnaliser votre solution de récupération d’urgence pour chaque application spécifique.


##Fonctionnalités clés
Les fonctionnalités d’Azure Site Recovery qui contribuent à votre stratégie de protection et de récupération au niveau des applications incluent notamment :

- Réplication presque synchrone avec des RPO de 30 secondes pour répondre aux besoins des applications les plus critiques.
- Captures instantanées cohérentes de l’application pour les applications uniques ou multiniveau.
- Intégration avec SQL Server AlwaysOn et partenariat avec d’autres technologies de réplication au niveau des applications, y compris la réplication AD, SQL AlwaysOn, les groupes de disponibilité de base de données (DAG) Exchange et Oracle Data Guard.
- Les plans de récupération flexibles qui vous permettent de récupérer une pile de l’application entière en un seul clic, et incluent les scripts externes ou des actions manuelles. 
- La gestion réseau avancée dans Site Recovery et Azure simplifie la configuration réseau requise pour une application, notamment la réservation d’adresses IP, la configuration d’équilibreurs de charge ou l’intégration d’Azure Traffic Manager pour les commutations réseau à RTO faible.
-  Une bibliothèque d’automatisation avancée qui fournit des scripts spécifiques d’application prêts pour la production, qui peuvent être téléchargés et intégrés avec Site Recovery.



##Synthèse relative aux charges de travail

Les technologies de réplication Site Recovery sont compatibles avec n’importe quelle application en cours d’exécution sur une machine virtuelle. En outre, nous avons effectué des tests supplémentaires en partenariat avec les équipes chargées des produits d’application, afin d’assurer la prise en charge complète de chaque application.

**Charge de travail** | <p>**Répliquer des machines virtuelles Hyper-V**</p> <p>**(sur un site secondaire)**</p> | <p>**Répliquer des machines virtuelles Hyper-V**</p> <p>**(sur Azure)**</p> | <p>**Répliquer des machines virtuelles VMware**</p> <p>**(sur un site secondaire)**</p> | <p>**Répliquer des machines virtuelles VMware**</p><p>**(sur Azure)****</p>
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y
Web apps (IIS, SQL) | Y | Y | Y | Y
SCOM | Y | Y | Y | Y
Sharepoint | Y | Y | Y | Y
<p>SAP</p><p>Répliquer site SAP sur Azure pour non cluster</p> | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Exchange (non DAG) | Y | Prochainement | Y | Y
Bureau à distance/VDI | Y | Y | Y | N/A
<p>Linux</p> <p>(système d'exploitation et applications)</p> | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Prochainement | Y | Prochainement
Oracle | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft) | Y (testé par Microsoft)
Windows File Server | Y | Y | Y | Y

##Protéger Active Directory et DNS

Toutes les applications d’entreprise telles que SharePoint, Dynamics AX et SAP dépendent d’Active Directory et de l’infrastructure DNS. Dans le cadre de votre solution de continuité d’activité et de récupération d’urgence, vous devez protéger et récupérer ces composants d’infrastructure avant de récupérer vos charges de travail et applications.

À l’aide de Site Recovery, vous pouvez créer un plan de récupération d’urgence automatisé complet pour Active Directory et DNS. Par exemple, si vous utilisez Active Directory pour plusieurs applications, telles que SharePoint et SAP dans votre site principal et que vous souhaitez basculer le site intégral, vous pouvez basculer Active Directory en premier lieu à l’aide d’un plan de récupération, puis basculer les applications reposant sur Active Directory en utilisant des plans de récupération spécifiques aux applications.

[En savoir plus](http://aka.ms/asr-ad)

##Protéger SQL Server

SQL Server fournit une base pour les services de données pour de nombreuses applications de gestion dans un centre de données local. Les technologies Site Recovery et SQL Server HA/DR sont complémentaires et peuvent être combinées pour fournir une protection de bout en bout pour les applications d’entreprise multiniveau. Site Recovery offre les avantages suivants pour les environnements SQL Server :

- Protection et réplication faciles des serveurs SQL autonomes ou en cluster sur Azure ou sur un site secondaire. Fournit une solution de récupération d’urgence simple et économique pour de nombreuses éditions et versions de SQL Server.
- Intégration avec les groupes de disponibilité SQL AlwaysOn, gestion des processus de basculement et de restauration automatique avec les plans de récupération dans Azure Site Recovery.
- Plans de récupération de bout en bout pour la pile de l’application entière, notamment les bases de données SQL Server.
- Mise à l’échelle de SQL Server pour les charges maximales à l’aide d’Azure Site Recovery par « éclatement » en tailles de machines virtuelles IaaS plus volumineuses dans Azure.
- Test de SQL Server dans Azure ou sur un site secondaire à l’aide d’Azure Site Recovery. Les basculements de test peuvent être utilisés pour l’analyse et les vérifications de conformité des données sans impact sur votre environnement de production.

[En savoir plus](http://aka.ms/asr-sql)

##Protéger SharePoint

Azure Site Recovery vous aide à protéger votre déploiement SharePoint. Avec Site Recovery, vous pouvez :

- Éliminer le besoin et le coût de l'infrastructure associée à une batterie de secours pour la récupération d'urgence. Avec Site Recovery, vous pouvez activer la protection de la batterie entière (niveaux web, application et base de données) sur Azure ou sur un site secondaire.
- Simplifier la gestion et le déploiement de l'application. Les mises à jour déployées sur le site principal sont automatiquement répliquées, et seront disponibles lorsque la batterie de serveurs sera récupérée sur le site secondaire. Cela élimine la complexité de gestion due au maintien d’une batterie de secours à jour et réduit ainsi les coûts.
- Simplifiez le développement et le test d’applications SharePoint en créant un environnement de réplica de copie à la demande de type production pour le test et le débogage.
- Simplifiez la transition vers le cloud en utilisant Site Recovery pour migrer les déploiements SharePoint vers Azure.

[En savoir plus](http://aka.ms/asr-sharepoint)


## Protéger Dynamics AX

Azure Site Recovery vous permet de protéger votre solution Dynamics AX ERP :

- Activer la protection de l’ensemble de votre environnement Dynamics AX (niveaux web et AOS, niveaux bases de données, SharePoint) sur Azure ou sur un site secondaire à l’aide de Site Recovery.
- Simplifiez la migration vers le cloud en utilisant Site Recovery pour migrer les déploiements Dynamics AX vers Azure.
- Simplifiez le développement et le test d’applications Dynamics AX en créant une copie de type production à la demande pour le test et le débogage.

[En savoir plus](http://aka.ms/asr-dynamics)

## Protéger les services Bureau à distance 
RDS active l'infrastructure de bureau virtuel (VDI), les bureaux basés sur session et les applications, permettant aux utilisateurs de travailler n'importe où. Avec Site Recovery, vous pouvez activer la protection de bureaux virtuels en pool gérés ou non gérés sur un site secondaire, ainsi que des applications et des sessions à distance sur un site secondaire ou sur Azure.

[En savoir plus](http://aka.ms/asr-rds)


## Protéger Exchange

Microsoft Exchange inclut la prise en charge intégrée pour la haute disponibilité et la récupération d’urgence. Les groupes de disponibilité de base de données (DAG) Exchange et Azure Site Recovery peuvent travailler ensemble.

- Les groupes de disponibilité de base de données (DAG) Exchange sont la solution recommandée pour la récupération d’urgence Exchange dans une entreprise. Les plans de récupération dans Site Recovery peuvent inclure des groupes de disponibilité de base de données pour orchestrer le basculement de ceux-ci entre les sites.
- Pour les déploiements de petite taille, comme des serveurs uniques ou non en cluster, Site Recovery peut protéger et basculer sur Azure ou sur un site secondaire.

[En savoir plus](http://aka.ms/asr-exchange)

## Protéger SAP

Utilisez Site Recovery pour protéger votre déploiement SAP :

- Activez la protection du déploiement SAP entier avec plusieurs couches sur Azure ou sur un site secondaire.
- Simplifier la migration vers le cloud en utilisant Site Recovery pour migrer votre déploiement SAP vers Azure.
- Simplifiez le développement et le test SAP en créant une copie de type production à la demande pour le test et le débogage des applications.

[En savoir plus](http://aka.ms/asr-sap)

<!---HONumber=AcomDC_1203_2015-->