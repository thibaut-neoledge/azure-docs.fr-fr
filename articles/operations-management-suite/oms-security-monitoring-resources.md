---
title: Surveillance des ressources dans la solution de sécurité et d’audit d’Operations Management Suite | Microsoft Docs
description: Ce document vous aide à utiliser les fonctionnalités de sécurité et d’audit d’OMS pour surveiller vos ressources et identifier des problèmes de sécurité.
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: ''

ms.service: operations-management-suite
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2016
ms.author: yurid

---
# <a name="monitoring-resources-in-operations-management-suite-security-and-audit-solution"></a>Surveillance des ressources dans la solution de sécurité et d’audit d’Operations Management Suite
Ce document vous aide à utiliser les fonctionnalités de sécurité et d’audit d’OMS pour surveiller vos ressources et identifier des problèmes de sécurité.

## <a name="what-is-oms?"></a>Qu’est-ce qu’OMS ?
Microsoft Operations Management Suite (OMS) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et de cloud. Pour plus d’informations sur OMS, lisez l’article [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="monitoring-resources"></a>Surveillance des ressources
Chaque fois que cela est possible, vous souhaitez d’abord empêcher la survenue d’incidents de sécurité. Toutefois, il est impossible de prévenir tous les incidents de sécurité. Lorsqu’un incident de sécurité se produit, vous devez minimiser son impact.  Il existe trois recommandations critiques qui peuvent être utilisées pour réduire le nombre et l’impact des incidents de sécurité :

* Évaluer régulièrement les vulnérabilités de votre environnement.
* Vérifiez régulièrement tous les systèmes d’ordinateurs et périphériques réseau pour vous assurer qu’ils disposent tous des derniers correctifs.
* Vérifiez régulièrement tous les journaux et mécanismes de journalisation, y compris les journaux d’événements de système d’exploitation, les journaux spécifiques des applications et les journaux système de détection d’intrusion.

La solution de sécurité et d’audit d’OMS permet au service informatique de surveiller activement toutes les ressources, ce qui peut aider à minimiser l’impact des incidents de sécurité. Cette solution possède des domaines de sécurité qui peuvent être utilisés pour la surveillance des ressources. Les domaines de sécurité fournissent un accès rapide à une option pour la surveillance de la sécurité. Les domaines suivants seront abordés plus en détail :

* Évaluation des logiciels malveillants
* Évaluation des mises à jour
* Identité et accès

> [!NOTE]
> pour avoir une vue d’ensemble de toutes ces options, consultez [Getting started with Operations Management Suite Security and Audit Solution](oms-security-getting-started.md)(Prise en main de la solution de sécurité et d’audit d’Operations Management Suite).
> 
> 

### <a name="monitoring-system-protection"></a>Surveillance de la protection du système
Dans une approche de défense approfondie, chaque couche de protection est importante pour l’état de sécurité global de votre ressource. Les ordinateurs sur lesquels des menaces ont été détectées et ceux présentant une protection insuffisante figurent dans la vignette Évaluation des logiciels malveillants sous Domaines de sécurité. Les informations de la vignette Évaluation des logiciels malveillants vous permettent d’identifier un plan de protection pour les serveurs qui en ont besoin. Pour accéder à cette option, procédez comme suit :

1. Dans le tableau de bord principal de **Microsoft Operations Management Suite**, cliquez sur la vignette **Sécurité et audit**.
   
    ![Sécurité et audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. Dans le tableau de bord **Sécurité et audit**, cliquez sur **Évaluation des logiciels malveillants** sous **Domaines de sécurité**. Le tableau de bord **Évaluation des logiciels malveillants** s’affiche comme indiqué ci-dessous :

![Évaluation des logiciels malveillants](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig2-ga.png)

Vous pouvez utiliser le tableau de bord **Évaluation des logiciels malveillants** pour identifier les problèmes de sécurité suivants :

* **Menaces actives**: ordinateurs compromis présentant des menaces actives dans le système.
* **Menaces corrigées**: ordinateurs compromis, mais dont les menaces ont été corrigées.
* **Signature obsolète**: ordinateurs disposant d’une protection contre les logiciels malveillants, dont la signature est obsolète.
* **Aucune protection en temps réel**: ordinateurs sur lesquels aucun logiciel anti-programme malveillant n’est installé.

### <a name="monitoring-updates"></a>Surveillances des mises à jour
L’application des dernières mises à jour de sécurité fait partie des meilleures pratiques en termes de sécurité, et doit être intégrée à votre stratégie de gestion des mises à jour. Le service Microsoft Monitoring Agent (HealthService.exe) lit les informations de mise à jour des ordinateurs faisant l’objet d’une surveillance, puis envoie ces informations actualisées au service OMS du cloud pour traitement. Le service Microsoft Monitoring Agent est configuré comme service automatique et doit être toujours en cours d’exécution sur l’ordinateur cible.

![Surveillances des mises à jour](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig3.png)

La logique est appliquée aux données de mise à jour et le service cloud enregistre les données. Si des mises à jour manquantes sont trouvées, elles sont affichées sur le tableau de bord des **Mises à jour** . Vous pouvez utiliser le tableau de bord des **Mises à jour** pour trouver les mises à jour manquantes et définir un programme pour les appliquer aux serveurs qui en ont besoin. Suivez les étapes ci-dessous pour accéder au tableau de bord **Mises à jour** :

1. Dans le tableau de bord principal de **Microsoft Operations Management Suite**, cliquez sur la vignette **Sécurité et audit**.
2. Dans le tableau de bord **Sécurité et audit**, cliquez sur **Évaluation des mises à jour** sous **Domaines de sécurité**. Le tableau de bord de mise à jour s’affiche comme indiqué ci-dessous :

![Évaluation des mises à jour](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig4.png)

Dans ce tableau de bord, vous pouvez effectuer une évaluation des mises à jour pour comprendre l’état actuel de vos ordinateurs et de traiter les menaces les plus critiques. À l’aide de la vignette **Mises à jour critiques relatives à la sécurité** , les administrateurs informatiques pourront accéder aux informations détaillées sur les mises à jour manquantes, comme indiqué ci-dessous :

![résultat de la recherche](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig5.png)

Ce rapport inclut des informations critiques qui peuvent être utilisées pour identifier le type de menace auquel ce système est vulnérable, ainsi que des articles de la base de connaissances Microsoft associés à la mise à jour de sécurité et le bulletin MS contenant des informations détaillées sur la vulnérabilité.

### <a name="monitoring-identity-and-access"></a>Surveillance des identités et des accès
Les utilisateurs travaillent en déplacement, utilisent différents appareils et accèdent à un grand nombre d’applications locales, c’est pourquoi il est impératif que leurs informations d’identification soient protégées. Le vol d’informations d’identification est une attaque au cours de laquelle une personne malveillante accède aux informations d’identification d’un utilisateur régulier pour accéder à un système au sein du réseau. Souvent, cette attaque initiale est simplement un moyen d’accéder au réseau, dans l’objectif de découvrir des comptes détenant des privilèges. 

Les personnes malveillantes restent dans le réseau et utilisent librement les outils disponibles pour extraire les informations d’identification à partir des sessions d’autres comptes connectés. Selon la configuration du système, ces informations d’identification peuvent être extraites sous la forme de hachages, de tickets ou même de mots de passe en clair.  

> [!NOTE]
> les machines directement exposées à Internet seront sujettes à de nombreux échecs de tentatives de connexion utilisant des noms d’utilisateurs connus (comme celui d’administrateur). Dans la plupart des cas, cela ne pose aucun problème, si les noms d’utilisateurs connus ne sont pas utilisés et si le mot de passe est suffisamment fort.
> 
> 

Il est possible d’identifier ces intrusions avant qu’elles ne compromettent un compte à privilèges. Vous pouvez tirer parti de la **solution de sécurité et d’audit d’OMS** pour surveiller les identités et les accès. Suivez les étapes ci-dessous pour accéder au tableau de bord **Identité et accès** :

1. Dans le tableau de bord principal de **Microsoft Operations Management Suite** , cliquez sur la vignette Sécurité et audit.
2. Dans le tableau de bord **Sécurité et audit**, cliquez sur **Identité et accès** sous **Domaines de sécurité**. Le tableau de bord **Identité et accès** s’affiche comme indiqué ci-dessous :

![Identité et accès](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig6-ga.png)

Dans le cadre de votre stratégie de surveillance régulière, vous devez inclure la surveillance des identités. L’administrateur informatique doit vérifier si un grand nombre de tentatives est associé à un nom d’utilisateur valide spécifique. Cela peut révéler l’acquisition d’un nom d’utilisateur réel et la tentative d’attaque par force brute par une personne malveillante ou l’utilisation d’un mot de passe codé en dur et expiré par un outil automatique.

Ce tableau de bord permet au département informatique d’identifier des menaces potentielles relatives aux identités et aux accès pesant sur les ressources d’une société. Il est particulièrement important d’identifier également les tendances potentielles, par exemple dans la vignette relative aux connexions dans le temps, en termes de durée et de nombre de tentatives de connexions. Dans ce cas, l’ordinateur **FileServer** a reçu 35 tentatives de connexion. Vous pouvez obtenir de plus amples informations sur cet ordinateur en cliquant dessus. 

![détails supplémentaires](./media/oms-security-monitoring-resources/oms-security-monitoring-resources-fig7-new.png)

Le rapport généré pour cet ordinateur comporte des informations utiles sur ce modèle. Notez que la colonne **ACCOUNT** indique le compte d’utilisateur utilisé pour tenter d’accéder au système, que la colonne **TIMEGENERATED** indique l’intervalle de temps de la tentative de connexion et que la colonne **LOGONTYPENAME** donne l’emplacement de cette tentative. Si ces tentatives ont été effectuées localement dans le système par un programme, la colonne **PROCESS** indique le nom du processus. Dans les scénarios où la tentative de connexion provient d’un programme, vous disposez déjà du nom de processus disponible et vous pouvez maintenant effectuer d’autres recherches dans le système cible.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris à utiliser la solution de sécurité et d’audit d’OMS pour surveiller vos ressources. Pour plus d’informations sur la sécurité OMS, consultez les articles suivants :

* [Présentation - Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Getting started with Operations Management Suite Security and Audit Solution (Prise en main de la solution de sécurité et d’audit d’Operations Management Suite)](oms-security-getting-started.md)
* [Monitoring and Responding to Security Alerts in Operations Management Suite Security and Audit Solution (Surveillance et réponse aux alertes de sécurité dans la solution de sécurité et d’audit d’Operations Management Suite)](oms-security-responding-alerts.md)

<!--HONumber=Oct16_HO2-->


