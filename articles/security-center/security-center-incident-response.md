<properties
   pageTitle="Utilisation d’Azure Security Center pour la réponse aux incidents | Microsoft Azure"
   description="Ce document explique comment tirer parti d’Azure Security Center pour un scénario de réponse aux incidents."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>

# Utilisation d’Azure Security Center pour la réponse aux incidents
De nombreuses organisations apprennent à répondre aux incidents uniquement après avoir subi une attaque. Pour réduire les coûts et les dommages, il est important de disposer d’un plan de réponse aux incidents avant qu’une attaque ne survienne. Azure Security Center peut être utilisé à différentes étapes de la réponse à un incident.

## Réponse aux incidents

L’efficacité d’un plan dépend de trois caractéristiques principales : la capacité à protéger contre les menaces, à détecter ces dernières et à y répondre. La protection vise à empêcher les incidents, la détection à identifier les menaces au plus tôt, et la réponse à expulser le cybercriminel et à restaurer les systèmes pour atténuer l’impact d’une intrusion.

Cet article utilise les étapes de réponse aux incidents de sécurité de l’article [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) (Réponse aux incidents de sécurité mise en œuvre par Microsoft Azure dans le cloud), illustrées dans le schéma suivant :

![Cycle de vie de la réponse aux incidents](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Security Center peut être utilisé pendant les étapes de détection, d’évaluation et de diagnostic. Pour en savoir plus sur chacune de ces étapes, voici un exemple de l’utilité de Security Center au cours des trois étapes initiales de réponse aux incidents :

- **Détecter** : premier signe de l’examen d’un événement.
	- Exemple : vérification initiale du déclenchement d’une alerte de sécurité à priorité élevée dans le tableau de bord de Security Center.
- **Évaluer** : procéder à l’évaluation initiale pour obtenir plus d’informations sur l’activité suspecte.
	- Exemple : obtention d’informations supplémentaires sur l’alerte de sécurité.
- **Diagnostiquer** : mener une investigation technique, identifier les stratégies de confinement, d’atténuation des risques et de contournement.
	- Exemple : suivre les étapes techniques de résolution décrites par Security Center dans cette alerte de sécurité spécifique.

Le scénario qui suit vous montre comment tirer parti de Security Center pendant les étapes de détection, d’évaluation et de diagnostic/réponse d’un incident de sécurité. Dans Security Center, un [incident de sécurité](security-center-incident.md) est un regroupement de toutes les alertes d’une ressource correspondant à des modèles de [chaîne de destruction](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Les incidents apparaissent dans la vignette et le panneau [Alertes de sécurité](security-center-managing-and-responding-alerts.md). Un incident affiche la liste des alertes associées, qui vous permet d’en savoir plus sur chaque occurrence. Security Center offre également des alertes de sécurité autonomes pouvant être utilisées pour détecter une activité suspecte.

## Scénario

Contoso a récemment migré une partie de ses ressources locales dans Azure, notamment certaines bases de données SQL et charges de travail métier reposant sur des machines virtuelles. L’équipe principale de réponse aux incidents de sécurité informatique (CSIRT) de Contoso rencontre aujourd’hui des difficultés pour étudier les problèmes de sécurité en raison de l’absence d’intégration des renseignements sur la sécurité fournis par leurs outils actuels de réponse aux incidents. Cette absence d’intégration pose un problème durant la détection (trop de faux positifs) et durant les étapes d’évaluation et de diagnostic. Dans le cadre de la migration, l’équipe a décidé d’opter pour Security Center pour l’aider à résoudre ce problème.

La première phase de cette migration s’est terminée après l’intégration de toutes les ressources et l’application de toutes les recommandations de sécurité de Security Center. L’équipe CSIRT de Contoso est le point central pour la gestion des incidents de sécurité informatique. Elle se compose d’un groupe de personnes chargées de traiter les incidents de sécurité. Les membres de l’équipe ont des responsabilités clairement définies pour s’assurer que tous les domaines d’action sont couverts.

Pour les besoins de ce scénario, nous allons nous concentrer sur les rôles des personnes suivantes, qui font partie de l’équipe CSIRT de Contoso :

![Cycle de vie de la réponse aux incidents](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Judy est spécialisée dans les opérations de sécurité et ses responsabilités incluent :
- La surveillance et l’élimination en continu des menaces de sécurité
- La remontée des problèmes au propriétaire des charges de travail de cloud ou à l’analyste de la sécurité en fonction des besoins

Sam est analyste de la sécurité et ses responsabilités incluent :
- L’examen des attaques
- La correction des alertes
- La collaboration avec les propriétaires des charges de travail pour déterminer et appliquer des mesures de correction

Comme vous pouvez le voir, Judy et Sam ont des responsabilités différentes, et ils doivent travailler ensemble en se partageant les informations obtenues grâce à Security Center.

## Solution recommandée

Comme Judy et Sam ont des rôles différents, ils utiliseront différentes sections de Security Center afin d’obtenir des informations pertinentes pour leurs activités quotidiennes. Judy utilisera les alertes de sécurité dans le cadre de sa surveillance quotidienne.

![Alertes de sécurité](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Judy utilisera les alertes de sécurité pendant les étapes de détection et d’évaluation. Une fois l’évaluation initiale terminée, Judy peut faire remonter le problème à Sam si un examen complémentaire est requis. À ce stade, Sam devra utiliser les informations fournies par Security Center, parfois conjointement avec d’autres sources de données, pour passer à l’étape de diagnostic.


## Mise en œuvre de cette solution 

Pour découvrir comment utiliser Azure Security Center dans un scénario de réponse aux incidents, nous allons suivre Judy pendant les étapes de détection et d’évaluation, puis voir ce que Sam fait pour diagnostiquer le problème.

### Étapes de détection et d’évaluation de la réponse aux incidents 

Judy s’est connectée au portail Azure et se trouve dans la console Security Center. Dans le cadre de ses activités de surveillance quotidiennes, elle a commencé à examiner les alertes de sécurité à priorité élevée en suivant cette procédure :

1. Cliquer sur la vignette **Alertes de sécurité** et accéder au panneau **Alertes de sécurité**. ![Panneau Alertes de sécurité](./media/security-center-incident-response/security-center-incident-response-fig4.png)

	> [AZURE.NOTE] Pour les besoins de ce scénario, Judy va procéder à une évaluation de l’alerte d’activité SQL malveillante signalée dans la capture d’écran ci-dessus.
2. Cliquer sur l’alerte **Malicious SQL activity** (Activité SQL malveillante) et examiner les ressources attaquées dans le panneau **Malicious SQL Activity** (Activité SQL malveillante) : ![Détails de l’incident](./media/security-center-incident-response/security-center-incident-response-fig5.png)
	
	Dans ce panneau, Judy peut prendre connaissance des ressources attaquées, du nombre de fois que cette attaque s’est produite se produire et de l’heure à laquelle elle a été détectée.
3. Cliquer sur la **ressource attaquée** pour obtenir des informations supplémentaires sur cette attaque.

Après avoir lu la description, Judy est convaincue qu’il ne s’agit pas d’un faux positif et qu’elle doit faire remonter ce dossier à Sam.

### Étape de diagnostic de la réponse aux incidents 

Sam reçoit le dossier de Laura et commence à examiner les étapes de correction suggérées par Security Center.

![Cycle de vie de la réponse aux incidents](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### Ressources supplémentaires

L’équipe de réponse aux incidents peut également tirer parti de la fonctionnalité [Power BI de Security Center](security-center-powerbi.md) pour consulter différents types de rapports et visualiser, analyser et filtrer les alertes de sécurité et les recommandations dans le cadre d’un examen approfondi. Les entreprises qui utilisent leur solution SIEM (Security Information and Event Management) pendant le processus d’examen peuvent également [intégrer Security Center à celle-ci](security-center-integrating-alerts-with-log-integration.md). Les journaux d’audit et les événements de sécurité des machines virtuelles Azure peuvent également être intégrés à l’aide de [l’outil d’intégration des journaux Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Ces données peuvent être utilisées conjointement avec les informations fournies par Security Center pour examiner une attaque.


## Conclusion

La mise en place d’une équipe avant la survenue d’un incident est très importante pour votre organisation et aura un impact positif sur la gestion des incidents. Si elle dispose des bons outils pour surveiller les ressources, cette équipe pourra plus facilement prendre des mesures précises pour résoudre un incident de sécurité. Les [fonctionnalités de détection](security-center-detection-capabilities.md) de Security Center aideront le département informatique à répondre aux incidents de sécurité et à corriger les problèmes de sécurité rapidement.

<!---HONumber=AcomDC_0921_2016-->