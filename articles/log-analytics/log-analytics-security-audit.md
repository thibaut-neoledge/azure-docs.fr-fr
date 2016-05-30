<properties
	pageTitle="Solution de sécurité et d’audit dans Log Analytics | Microsoft Azure"
	description="La solution de sécurité et d’audit dans Log Analytics offre une vue détaillée de la sécurité informatique de votre organisation. Elle fournit des requêtes de recherche intégrées pour détecter les problèmes notables qui nécessitent votre attention."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="banders"/>

# Solution de sécurité et d’audit dans Log Analytics

La solution de sécurité et d’audit dans Log Analytics offre une vue détaillée de la sécurité informatique de votre organisation. Elle fournit des requêtes de recherche intégrées pour détecter les problèmes notables qui nécessitent votre attention.

## Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Ajoutez la solution de sécurité et d’audit à votre espace de travail OMS en suivant la procédure décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md). Aucune configuration supplémentaire n’est requise.



## Détails de la collecte de données de sécurité et d’audit

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur la façon dont les données sont collectées pour la solution de sécurité et d’audit.

|type de données| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
|---|---|---|---|---|---|---|---|
|Journaux des événements de sécurité Windows|Windows|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)| ![Non](./media/log-analytics-security-audit/oms-bullet-red.png)|![Non](./media/log-analytics-security-audit/oms-bullet-red.png)| pour Azure Storage : 10 minutes ; pour l’agent : à l’arrivée|
|Journaux du pare-feu Windows|Windows|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)|![Non](./media/log-analytics-security-audit/oms-bullet-red.png)| ![Non](./media/log-analytics-security-audit/oms-bullet-red.png)|![Non](./media/log-analytics-security-audit/oms-bullet-red.png)| à l'arrivée|
|Journaux d’événements Windows|Windows|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)| ![Non](./media/log-analytics-security-audit/oms-bullet-red.png)|![Oui](./media/log-analytics-security-audit/oms-bullet-green.png)| pour Azure Storage : 1 minutes ; pour l'agent : à l'arrivée|


## Bonnes pratiques relatives à l'analyse d'investigation

**Rechercher les preuves**

Une analyse d'investigation à l'aide de la solution de sécurité et d'audit vous permet de rechercher des preuves que des utilisateurs potentiellement malveillants auraient pu laisser derrière eux. Peu importe ce que font les utilisateurs dans leur environnement informatique, les activités auxquelles ils participent génèrent des artefacts de sécurité. Notez que, dans la plupart des cas, les personnes malveillantes effacent les journaux. Il s'agit bien souvent de la première étape qu'ils prennent pour cacher leurs méfaits.

Cependant, lorsque des utilisateurs accèdent à leur propre ordinateur sur place ou à distance, la preuve de l'utilisation qu'ils en font est stockée dans les journaux des événements. OMS collecte ces artefacts *dès qu’ils ont lieu*, avant que quiconque ne puisse les falsifier. Vous pouvez ainsi effectuer différents types d’analyses en mettant en corrélation les données de plusieurs ordinateurs.

**Comprendre comment configurer et collecter des événements d'audit**

Pour tirer le meilleur parti de la solution de sécurité et d’audit, vous devez configurer le niveau d’événements d’audit collectés par votre environnement Windows. Le but est qu’il soit adapté au mieux à vos besoins. Pour ce faire, vous pouvez utiliser les ressources web suivantes.

- [Comment configurer les paramètres de stratégie de sécurité](https://technet.microsoft.com/library/dn135243&#40;v=ws.10&#41;.aspx)
- [Configuration de la stratégie d’audit avancée](https://technet.microsoft.com/library/jj852202&#40;v=ws.10&#41;.aspx)
- [Recommandations pour la stratégie d'audit](https://technet.microsoft.com/library/dn487457.aspx)

**Activer AppLocker**

Vous devez également activer les événements AppLocker pour obtenir des informations détaillées sur les exécutions de processus qui se produisent dans votre environnement informatique. Pour plus d’informations, consultez [Configurer une stratégie AppLocker pour les audits uniquement](https://technet.microsoft.com/library/hh994622.aspx).

**Configurer le niveau d'audit pour les événements Windows**

L'environnement informatique de Windows vous permet de configurer le niveau de capture des enregistrements relatifs à la sécurité. Par exemple, vous pouvez configurer votre environnement pour qu'un événement soit généré chaque fois qu'un utilisateur accède à un fichier, lit ou ouvre un fichier. Le niveau de détail que vous souhaitez collecter varie en fonction de vos besoins. Cependant, chaque option que vous activez présente une forme de coût, car vous devez stocker toutes les informations que vous collectez. C'est pourquoi, dans de nombreux environnements informatiques d'organisations, la collecte de données relatives à la lecture ou à l'écriture d'objet n'est pas activée. Chaque fois qu'une personne accède à un fichier, des milliers d'événements peuvent être générés, dont certains sont inutiles. Pour définir le niveau de la collecte que souhaitez mettre en œuvre, fiez-vous à votre propre jugement.

>[AZURE.NOTE] Si vous utilisez l’agent direct et que vous disposez d’un serveur proxy au sein de votre organisation, vous devez le configurer pour permettre à l’agent d’accéder à OMS. Pour plus d’informations, consultez [Configurer les paramètres de pare-feu et de proxy dans Log Analytics](log-analytics-proxy-firewall.md).

## Meilleures pratiques relatives aux enquêtes de modèle de faille de sécurité

**Enquêter sur les modèles d'activités anormales**

Généralement les failles de sécurité proviennent d'informations d'identification légitimes et nécessitent qu'un utilisateur malveillant essaie d'obtenir des privilèges élevés grâce à des attaques. La solution de sécurité et d’audit n’aborde pas la question de la détection d’intrusion. Elle vous aide plutôt à enquêter sur les modèles d’activités anormales, répertoriés dans Problèmes notables, et à les découvrir. Par exemple, vous devez enquêter sur les activités anormales suivantes et sur celles qui apparaissent sous **Problèmes notables**.

- connexions inhabituelles sur l'ordinateur d'un utilisateur qui ne l'utilise normalement pas
- énumération de réseau inhabituelle à partir d'ordinateurs ou d'utilisateurs atypiques
- nouveaux comptes d'utilisateur créés avec des droits d'administration
- modifications apportées aux stratégies de sécurité ou de journalisation
- exécutables suspects

## Meilleures pratiques relatives aux scénarios d'audit

Votre organisation a certainement mis en place des réglementations et des stratégies de conformité relatives au réseau et aux ordinateurs que vous devez respecter. Cela nécessite d'importantes quantités d'enregistrements d'audit. Par exemple, si votre organisation est une société de financement, vous devez conserver des enregistrements qui vous permettent de retrouver l'utilisateur qui a effectué une opération spécifique sur le réseau, à un moment précis, et en obtenir la preuve. Vous pouvez également être amené à générer des rapports détaillés concernant l'activité d'un utilisateur en particulier ou d'un serveur choisi sur demande, et revenir plusieurs mois (parfois même plusieurs années) en arrière.

Vous pouvez utiliser la solution de sécurité et d'audit pour collecter des données d'audit à travers l'ensemble de votre environnement informatique, que vos ordinateurs soient sur site ou dans le cloud. Toutes les données d'audit sont stockées, indexées et conservées par votre abonnement. Avec un abonnement Premium à OMS, un nombre illimité de données est stocké pendant un an. Vous pouvez ensuite afficher les données d'audit, effectuer des recherches et mettre en corrélation différents types de données et ordinateurs pour obtenir des résultats complets, peu importe l'intervalle de temps dans la mesure où les données ont été collectées.

**Utiliser une stratégie de groupe pour collecter des données d'audit**

Toutes les données d’audit que vous souhaitez collecter et envoyer à OMS sont entièrement gérées par une stratégie de groupe. Vous pouvez ainsi définir des configurations de sécurité via des objets de stratégie de groupe (GPO), liés à des conteneurs Active Directory comme des sites, des domaines et des unités d'organisation. Ces GPO vous aident à gérer les paramètres de sécurité. Les données de la stratégie sont enregistrées et envoyées ultérieurement au service OMS.

**Utiliser AppLocker pour collecter des données d'audit**

En plus des paramètres de stratégie locale, si vous utilisez AppLocker pour collecter des données d’audit, OMS les rassemble pour que vous puissiez ensuite les afficher.

## Mener une enquête simple concernant un exécutable suspect

1. Connectez-vous à OMS.
2. Dans la page **Vue d'ensemble**, lisez les informations affichées dans la vignette **Sécurité et audit** puis cliquez dessus. ![Image de la page Vue d'ensemble](./media/log-analytics-security-audit/oms-security-audit-dash02.png)
3. Dans la page **Sécurité et audit**, lisez les informations contenues dans le panneau **PROBLÈMES NOTABLES**. L'image d'exemple présente aujourd'hui six problèmes importants, dont deux apparus hier. Dans cet exemple, un exécutable suspect a été détecté. Cliquez sur **Exécutables suspects** dans le panneau **PROBLÈMES NOTABLES**. ![Image de la page Sécurité et audit](./media/log-analytics-security-audit/oms-security-audit-dash03.png)
4. La recherche affiche la requête et les résultats concernant l'exécutable suspect sur lequel vous avez cliqué. Dans cet exemple, un résultat est obtenu. Son hachage de fichier s'affiche. Cliquez sur l'ID **FILEHASH**. ![Image du hachage de fichier des résultats de la recherche](./media/log-analytics-security-audit/oms-security-audit-search01.png)
5. La recherche affiche des informations supplémentaires concernant le fichier exécutable, dont son chemin d'accès et le nom de son processus. Cliquez sur le **&lt;nom de fichier&gt; du processus**. Dans cet exemple, il s'agit de HEXEDIT. EXE. ![Image du processus des résultats de la recherche](./media/log-analytics-security-audit/oms-security-audit-search02.png)
6. La recherche ajoute le nom du processus entre guillemets à la requête. « **HEXEDIT.EXE** » dans cet exemple. ![Image de la requête de la recherche](./media/log-analytics-security-audit/oms-security-audit-search03.png)
7. Dans la zone de requête de la recherche, supprimez tous les éléments sauf le nom du processus et les guillemets. Cliquez ensuite sur l'icône de recherche. ![Image des informations détaillées de la recherche](./media/log-analytics-security-audit/oms-security-audit-search04.png)
8. La recherche affiche des informations détaillées concernant le processus, dont les ordinateurs sur lesquels il s'exécute, le compte d'utilisateur qu'il utilise, ainsi que la date et l'heure auxquelles un événement a été créé pour le processus.
9. À l'aide des informations que vous trouvez, vous pouvez prendre des mesures correctives, si nécessaire. Par exemple, si vous déterminez que l'exécutable est un programme malveillant, alors vous souhaitez sans doute prendre des mesures pour le supprimer de tous les ordinateurs qu'il affecte. Une fois que le fichier exécutable est supprimé et qu’OMS reçoit les événements d’audit et de journal mis à jour de vos ordinateurs, les valeurs du panneau PROBLÈMES NOTABLES sont modifiées le lendemain.

## Enquêter sur des adresses IP malveillantes

Pour protéger vos serveurs, une méthode consiste à vérifier qu’ils ne communiquent pas avec des adresses IP malveillantes ou suspectes connues. Dans OMS, vous disposez d’une vue centralisée de toutes les adresses IP malveillantes avec lesquelles vos clients et vos serveurs gérés sont susceptibles de communiquer. En collaboration avec Microsoft Threat Intelligence Center (MSTIC), OMS obtient toutes les heures des mises à jour relatives aux dernières adresses IP malveillantes connues et vous informe si l’un de vos serveurs risque d’être compromis. L’équipe MSTIC collabore avec différents partenaires sur la recherche d’informations relatives aux menaces pour collecter et fournir cette liste centralisée au service OMS.

Les adresses IP malveillantes sont visibles dans la solution de sécurité et d’audit du portail OMS, dans **Distinct Suspicious IP Addresses Accessed** (Accès à différentes adresses IP suspectes).

Vous pouvez ouvrir la vignette et afficher la liste complète des différentes adresses IP suspectes avec lesquelles vos appareils sont peut-être en train de communiquer. OMS analyse toutes les sources de données que vous envoyez au service, notamment :

- Journaux du pare-feu Windows
- Journaux IIS
- WireData


OMS affiche de nombreux champs liés aux adresses IP suspectes dans les résultats de la recherche de journal. Voici quelques-uns des plus importants :

- **IndicatorThreatType** : citons en exemple botnet, proxy, darknet, commande de programme malveillant et nœud de contrôle.
- **Description** : type de menace et niveau de confiance sur le caractère malveillant de l’adresse IP. Ces données, les mêmes que celles utilisées par Microsoft pour se protéger, sont mises à la disposition des utilisateurs d’OMS.

Pour obtenir la liste complète des champs relatifs aux adresses IP suspectes, consultez [Champs de recherche de journal d’adresses IP malveillantes](#Malicious-IP-log-search-fields) ci-dessous.

### Pour enquêter sur une adresse IP malveillante

Dans cet exemple, nous allons examiner une éventuelle adresse IP malveillante à l’aide des données du journal du Pare-feu Windows.

1. Connectez-vous à OMS.
2. Dans la page **Vue d'ensemble**, lisez les informations affichées dans la vignette **Sécurité et audit** puis cliquez dessus. ![Image de la page Vue d'ensemble](./media/log-analytics-security-audit/oms-security-audit-dash02.png)  
3. Dans la page **Sécurité et audit**, passez en revue les informations de synthèse dans la vignette **Distinct Suspicious IP Addresses Accessed** (Accès à différentes adresses IP suspectes). ![page Sécurité et audit indiquant une adresse IP suspecte](./media/log-analytics-security-audit/oms-maliciousip-01.png)  
4. Cet exemple recense 6 adresses IP suspectes. Cliquez sur la vignette **Distinct Suspicious IP Addresses Accessed** (Accès à différentes adresses IP suspectes).
5. La recherche affiche la requête et les résultats correspondants pour les adresses IP malveillantes détectées. Cet exemple compte 6 résultats. Les adresses IP sont affichées. ![résultats de la recherche indiquant des adresses IP suspectes](./media/log-analytics-security-audit/oms-maliciousip-02-revised.png)  
6. Dans l’image ci-dessus, notez la dernière adresse IP. Dans cet exemple, il s’agit de **94.102.56.130**. Cliquez sur cette adresse.
7. La recherche présente des résultats détaillés et montre diverses données sur l’indicateur de menace. Cliquez sur **Afficher plus** pour afficher l’ensemble complet des résultats. ![résultats de la recherche indiquant des données détaillées sur l’indicateur de menace](./media/log-analytics-security-audit/oms-maliciousip-03-revised.png)  
8. Si vous souhaitez afficher une liste de tous les serveurs de votre environnement susceptibles de communiquer avec une adresse IP malveillante, vous pouvez utiliser la requête de recherche de journal suivante.

    ```
    IsActive=True | measure count() by Computer
    ```

### Exécution d’actions correctives pour les communications avec des adresses IP malveillantes

Si vous êtes sûr qu’un composant ou processus de votre réseau envoie des données à des adresses IP malveillantes, la procédure à suivre est claire :

- Vérifiez que votre ressource envoie des données à l’adresse IP malveillante.
- Éventuellement, supprimez ou bloquez le logiciel sur l’ordinateur qui envoie des données. De plus, empêchez toute communication du logiciel concerné avec les adresses IP spécifiées.
- Déterminez si les informations d’identification de l’utilisateur exécutant un exécutable ou un processus risquent d’être compromises. Pour cela, examinez tous les comptes sous lesquels le processus suspect s’exécute. Identifiez les autres ressources ayant pu faire l’objet d’un accès.
- Déterminez comment le logiciel a été installé sur l’ordinateur.
- Laissez temporairement le logiciel douteux installé pour pouvoir analyser étroitement son activité. Après une évaluation approfondie, effectuez une action corrective, si nécessaire.


Toutefois, si *vous n’êtes pas sûr* que des données sont envoyées à des adresses IP malveillantes, ou si *le doute est difficile à lever*, vos actions correctives sont moins évidentes. En général, vous pouvez mener une enquête en suivant certaines des étapes ci-dessus. Dans d’autres cas, il n’est peut-être pas utile d’entreprendre quelque action que ce soit. En fin de compte, vous êtes la personne la mieux informée sur l’infrastructure informatique de votre organisation. Vous êtes donc la mieux placée pour déterminer la façon dont les menaces potentielles sont traitées.


Les journaux de pare-feu indiquent le sens de circulation des données. Quand votre pare-feu est configuré pour bloquer les communications sortantes (indiquées dans les journaux d’envoi) vers des adresses IP spécifiques, toute tentative de communication d’un logiciel potentiellement compromis vers ces adresses IP malveillantes comporte l’état *bloqué* dans les résultats de la recherche de journal. Dans pareil cas, vous devez prendre des mesures supplémentaires. Même si le logiciel concerné ne peut plus communiquer avec une adresse IP spécifique, il peut tenter de communiquer avec d’autres adresses IP inconnues et potentiellement malveillantes par la suite.

Comme les journaux de pare-feu indiquent également les blocages de données entrantes, vous risquez d’être confronté à ce genre de situation. Faut-il vous en soucier ? Normalement non. Quand vos journaux de pare-feu indiquent que des données entrantes sont bloquées, ils confirment que votre pare-feu fait le nécessaire : protéger votre infrastructure.


### Informations sur les menaces liées aux adresses IP malveillantes

Microsoft partage des informations avec un grand nombre d’organisations pour créer une synthèse de tous les indicateurs connus susceptibles de compromettre des données. Cette synthèse vise à identifier les correspondances dans les jeux de données. Elle permet aussi d’afficher les détails complémentaires des menaces sous forme de liens facilement visibles. Vous pouvez afficher ces informations relatives aux menaces dans les résultats de la recherche de journal pour adresses IP malveillantes.

Microsoft reçoit les données sur les menaces selon trois méthodes. Ces données sont ensuite regroupées pour former un jeu de données d’informations sur les menaces. Les fournisseurs de données se répartissent essentiellement en fonction des catégories suivantes :

- Abonnements payants : entreprises avec lesquelles Microsoft a conclu un contrat pour acheter leurs données.
- Données de partenaires : sociétés spécialisées dans la sécurité. Vous pouvez considérer ces données comme des « signaux d’information », car elles ne contiennent pas de rapports détaillés. Toutefois, il s’agit de données jugées pertinentes.
- Processus internes à Microsoft : Microsoft Threat Intelligence Center (MSTIC), principalement.

#### Champs de recherche de journal d’adresses IP malveillantes

Les champs d’adresses IP malveillantes suivants s’affichent dans les résultats de la recherche de journal.

|champ de recherche| description |
|---|---|
| **IsActive**| Signale que l’indicateur est actif et qu’il doit être considéré comme valide s’il correspond à une adresse IP détectée. MSTIC gère cet indicateur.|
|**Confidence**| Affiche une valeur comprise entre 1 et 100. MSTIC gère cette valeur et les algorithmes qui la définissent. Il s’agit d’une valeur intéressante à partir de laquelle filtrer les résultats de la recherche.|
|**TLPLevel**| Niveau du protocole TLP (Traffic Light Protocol). Les valeurs sont : Green, Amber et Red (vert, orange et rouge). Ces valeurs correspondent respectivement à un impact faible, moyen et élevé sur l’entreprise.|
| **IndicatorThreatType** | Brève description du type de menace représenté par l’indicateur. Exemples : botnet, DDoS, programme malveillant, URL malveillante, adresse IP malveillante, hameçonnage et courrier indésirable.|
| **Niveau de gravité** | Affiche une valeur comprise entre 0 et 5. Indique le degré de gravité de la menace.|
| **MaliciousIP** | Adresse IP de l’hôte malveillant.|
| **CommunicationDirection** | Indique le sens de communication entre le trafic IP et l’adresse IP malveillante.|

## Étapes suivantes

- [Lancez une recherche dans les journaux](log-analytics-log-searches.md) pour afficher des données détaillées de sécurité et d’audit.

<!---HONumber=AcomDC_0518_2016-->