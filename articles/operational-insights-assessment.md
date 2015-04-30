<properties 
   pageTitle="Optimisez votre environnement à l'aide des packs d'évaluation"
   description="Les packs d'évaluation permettent d'évaluer les risques et l'intégrité de vos environnements de serveurs à intervalles réguliers."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Optimisez votre environnement à l'aide des packs d'évaluation

Les packs d'évaluation permettent d'évaluer les risques et l'intégrité de vos environnements de serveurs à intervalles réguliers. Ils fournissent une liste hiérarchisée de recommandations propres à votre infrastructure de serveurs déployée. Les recommandations sont réparties en six domaines, ce qui vous permet d'évaluer rapidement les risques et la santé de votre infrastructure et de prendre facilement des mesures pour réduire ces risques et améliorer l'état de votre infrastructure.

Actuellement, les packs d'évaluation suivants sont disponibles :

- Active Directory

- SQL Server

Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Chaque recommandation explique pourquoi le problème peut vous concerner et comment implémenter les modifications suggérées.

Vous pouvez choisir les domaines les plus importants pour votre organisation et suivre votre progression vers un environnement sans risque et intègre.

Installez le pack d'analyse décisionnelle pour mettre à jour l'agent Operations Manager et le module de configuration de base pour Operational Insights, et ajoutez le fichier AdvisorAssessment.exe aux serveurs contrôlés. Les données de configuration sont lues, puis envoyées au service Operational Insights dans le cloud pour traitement. Une logique est appliquée aux données reçues et le service cloud enregistre les données. Une fois les évaluations terminées, le résumé des informations de domaines s'affiche sur le tableau de bord **Évaluation** de l'infrastructure de votre environnement. Les informations du tableau de bord **Évaluation** vous donnent accès à des recommandations pour améliorer votre infrastructure de serveur.

![image de la vignette Évaluation de SQL](./media/operational-insights-assessment/overview-sql-assess.png)

![image du tableau de bord Évaluation de SQL](./media/operational-insights-assessment/gallery-ad-01.png)


## Forum aux questions sur les packs d'évaluation

*Quelle est la fréquence d'exécution des évaluations ?*<br>
L'évaluation s'exécute tous les 7 jours.

*Est-il possible de configurer la fréquence d'exécution de l'évaluation ?*<br>
Pas pour l'instant.

*Si un autre serveur est découvert après l'ajout d'un pack d'évaluation, ce serveur sera-t-il évalué ?*<br>
Oui, une fois découverts, les nouveaux serveurs sont évalués tous les 7 jours.

*Si un serveur est désactivé, est-il retiré du processus d'évaluation ?*<br>
Si un serveur n'envoie pas de données pendant 3 semaines, il est supprimé.

*Quel est le nom du processus qui effectue la collecte de données ?*<br>
AdvisorAssessment.exe

*Combien de temps la collecte de données prend-elle ?*<br>
La collecte de données sur le serveur prend environ 1 heure. Cela peut prendre plus de temps sur les serveurs dotés d'un grand nombre de serveurs Active Directory, d'instances SQL ou de bases de données.

*Quels types de données sont collectés ?*<br>
Les types de données suivants sont collectés :

- WMI
- Registre
- Compteurs de performance
- SQL Dynamic Management Views (DMV)

*Est-il possible de configurer les périodes de collecte de données ?*<br>
Pas pour l'instant.

*Pourquoi faut-il configurer un compte d'identification ?*<br>
Pour SQL Server, un petit nombre de requêtes SQL est exécuté. Pour permettre cette exécution, vous devez utiliser un compte d'identification avec les autorisations AFFICHER L'ÉTAT DU SERVEUR.  En outre, pour les requêtes WMI, des informations d'identification d'administrateur local sont requises.

*Pourquoi afficher uniquement les 10 premières recommandations ?*<br>
Au lieu d'exploiter une liste de tâches trop importante, nous vous recommandons de vous concentrer sur les recommandations prioritaires. Une fois que vous les aurez suivies, de nouvelles recommandations apparaîtront. Si vous préférez néanmoins afficher la liste détaillée des recommandations, utilisez la fonction de recherche d'Operational Insights.

*Est-il possible d'ignorer une recommandation ?*<br>
Pas pour l'instant.

## Hiérarchisation des recommandations

Une valeur de pondération déterminant l'importance relative de la recommandation est attribuée à chaque recommandation. Seules les dix recommandations les plus importantes sont affichées.

### Calcul des pondérations

Les pondérations sont des agrégations de valeurs basées sur trois facteurs clés :

-La *probabilité* qu'une anomalie identifiée cause des problèmes. Une plus grande probabilité attribue un score global supérieur à la recommandation.

-L'*impact* de l'anomalie sur votre organisation si elle devait causer des problèmes. Un plus grand impact attribue un score global supérieur à la recommandation.

-L'*effort*  requis pour implémenter la recommandation. Un plus grand effort attribue un score global inférieur à la recommandation.

La pondération de chaque recommandation est exprimée en pourcentage du score total disponible pour chaque domaine. Par exemple, si une recommandation dans le domaine de la sécurité et de la conformité a un score de 5 %, l'implémentation de cette recommandation augmentera votre score global de sécurité et conformité de 5 %.

### Domaines

**Sécurité et conformité** - Protégez la réputation de votre organisation en vous préservant des failles et des menaces de sécurité, en appliquant des stratégies d'entreprise et en répondant aux exigences techniques, juridiques et réglementaires.

**Disponibilité et continuité opérationnelle** - Assurez la disponibilité de vos services et la continuité de vos opérations en garantissant la résilience de votre infrastructure et en bénéficiant d'un niveau de protection adéquat en cas de sinistre.

**Performances et évolutivité** - Optimisez la croissance et le pouvoir d'innovation de votre organisation en vous assurant que votre environnement informatique peut répondre aux besoins de performances actuels et s'adapter rapidement à l'évolution des besoins.

**Mise à niveau, migration et déploiement** - Placez votre service informatique au cœur du changement et de l'innovation en tirant pleinement parti des nouvelles technologies à valeur ajoutée pour les clients, les employés et les unités organisationnelles.

**Opérations et surveillance** - Réduisez votre budget de maintenance informatique et optimisez les performances de votre entreprise en rationalisant vos opérations informatiques et en implémentant un programme de maintenance préventive complet.

**Gestion des changements et de la configuration** - Protégez les opérations quotidiennes de votre organisation et assurez-vous que les modifications n'auront pas d'impact négatif sur vos activités en mettant en place des procédures de contrôle des modifications et en effectuant le suivi et l'audit des configurations système.

### Faut-il viser un score de 100 % dans chaque domaine ?

Pas nécessairement. Les recommandations sont basées sur les connaissances et l'expérience que les ingénieurs de Microsoft ont acquises au contact de milliers de visiteurs. Toutefois, chaque infrastructure de serveur étant différente, certaines recommandations peuvent être plus ou moins adaptées à votre système. Par exemple, il se peut que certaines recommandations de sécurité soient moins appropriées si vos ordinateurs virtuels ne sont pas connectés à Internet. Certaines recommandations de disponibilité peuvent être moins pertinentes pour les services qui fournissent des rapports et des données ad hoc de faible priorité. Les problèmes importants pour une entreprise bien établie peuvent l'être moins pour une start-up. Nous vous conseillons donc d'identifier tout d'abord vos domaines prioritaires, puis d'observer l'évolution de vos résultats au fil du temps.

Chaque recommandation est accompagnée d'une explication sur son importance. Servez-vous de cette explication pour évaluer si la mise en œuvre de la recommandation est importante pour vous, en fonction de la nature de vos services informatiques et des besoins de votre organisation.

## Utilisation des recommandations des domaines d'évaluation

Avant de pouvoir utiliser un pack d'évaluation dans Microsoft Azure Operational Insights, vous devez installer un pack d'analyse décisionnelle. Pour plus d'informations sur l'installation de packs d'analyse décisionnelle, consultez la rubrique [Utilisation de la galerie pour ajouter ou supprimer des packs d'analyse décisionnelle](operational-insights-add-intelligence-pack.md). Une fois le pack installé, vous pouvez afficher un résumé des recommandations à l'aide de la vignette Évaluation de la page Vue d'ensemble d'Operational Insights.

Consultez le résumé des évaluations de conformité pour votre infrastructure, puis explorez les recommandations.

![image des recommandations d'évaluation de SQL](./media/operational-insights-assessment/gallery-ad-03.png)



### Pour afficher les recommandations relatives à un domaine et prendre des mesures correctives

1. Sur la page **Vue d'ensemble**, cliquez sur la vignette **Évaluation** de votre infrastructure de serveur.

2. Sur la page **Évaluation**, passez en revue les informations de résumé relatives à un domaine, puis cliquez sur le domaine requis pour afficher les recommandations y afférentes.

3. Les pages relatives au domaine répertorient les recommandations prioritaires pour votre environnement. Cliquez sur une recommandation pour en afficher les détails et comprendre pourquoi elle apparaît sous **Objets affectés**.

4. Effectuez les actions correctives suggérées dans **Mesures conseillées**. Une fois l'élément traité, les évaluations ultérieures indiqueront que des mesures ont été prises et votre score de conformité augmentera. Les éléments corrigés apparaissent comme **objets passés**.

<!--HONumber=52-->