<properties 
   pageTitle="Explorer les données de sécurité et d'audit d'Operational Insights"
   description="Découvrez le pack d'analyse décisionnelle de sécurité et d'audit d'Operational Insights qui vous permet d'obtenir une vue détaillée de la sécurité informatique de votre organisation. Il fournit des requêtes de recherche intégrées pour détecter les problèmes importants qui requièrent votre attention."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" /> <tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Explorer les données de sécurité et d'audit d'Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../includes/operational-insights-note-moms.md)]

Le pack d'analyse décisionnelle de sécurité et d'audit d'Operational Insights offre une vue détaillée de la sécurité informatique de votre organisation. Il fournit des requêtes de recherche intégrées pour détecter les problèmes importants qui requièrent votre attention.

Cet article vous apprendra les éléments suivants :

- Mener une enquête simple concernant un exécutable suspect
- Comprendre les meilleures pratiques relatives à l'analyse d'investigation
- Comprendre les meilleures pratiques relatives aux enquêtes de modèle de faille de sécurité
- Comprendre les meilleures pratiques relatives aux scénarios d'audit

## Mener une enquête simple concernant un exécutable suspect

1. Connectez-vous à Operational Insights.
2. Dans la page **Vue d'ensemble**, lisez les informations affichées dans la vignette **Sécurité et audit** puis cliquez dessus. ![Image de la page Vue d'ensemble](./media/operational-insights-security-audit/sec-audit-dash02.png)
3. Dans la page **Sécurité et audit**, lisez les informations contenues dans le panneau **PROBLÈMES IMPORTANTS**. L'image d'exemple présente aujourd'hui six problèmes importants, dont deux apparus hier. Dans cet exemple, un exécutable suspect a été détecté. Cliquez sur **Exécutables suspects** dans le panneau **PROBLÈMES IMPORTANTS**. ![Image de la page Sécurité et audit](./media/operational-insights-security-audit/sec-audit-dash03.png)
4. La recherche affiche la requête et les résultats concernant l'exécutable suspect sur lequel vous avez cliqué. Dans cet exemple, un résultat est obtenu. Son hachage de fichier s'affiche. Cliquez sur l'ID **FILEHASH**. ![Image du hachage de fichier des résultats de la recherche](./media/operational-insights-security-audit/sec-audit-search01.png) 
5. La recherche affiche des informations supplémentaires concernant le fichier exécutable, dont son chemin d'accès et le nom de son processus. Cliquez sur le **&lt;nom de fichier&gt; du processus**. Dans cet exemple, il s'agit de HEXEDIT. EXE. ![Image du processus des résultats de la recherche](./media/operational-insights-security-audit/sec-audit-search02.png) 
6. La recherche ajoute le nom du processus entre guillemets à la requête \("\*\*HEXEDIT.EXE"\*\*, dans cet exemple\). ![Image de la requête de la recherche](./media/operational-insights-security-audit/sec-audit-search03.png)
7. Dans la zone de requête de la recherche, supprimez tous les éléments sauf le nom du processus et les guillemets. Cliquez ensuite sur l'icône de recherche. ![Image des informations détaillées de la recherche](./media/operational-insights-security-audit/sec-audit-search04.png)
8. La recherche affiche des informations détaillées concernant le processus, dont les ordinateurs sur lesquels il s'exécute, le compte d'utilisateur qu'il utilise, ainsi que la date et l'heure auxquelles un événement a été créé pour le processus.
9. À l'aide des informations que vous trouvez, vous pouvez prendre des mesures correctives, si nécessaire. Par exemple, si vous déterminez que l'exécutable est un programme malveillant, alors vous souhaitez sans doute prendre des mesures pour le supprimer de tous les ordinateurs qu'il affecte. Lorsque le fichier exécutable est supprimé et qu'Operational Insights reçoit le journal des mises à jour et les événements d'audit de vos ordinateurs, les valeurs du panneau PROBLÈMES IMPORTANTS seront modifiées le lendemain.

## Meilleures pratiques relatives à l'analyse d'investigation

**Rechercher les preuves**

Une analyse d'investigation à l'aide du pack d'analyse décisionnelle de sécurité et d'audit vous permet de rechercher des preuves que des utilisateurs malveillants auraient pu laisser derrière eux. Peu importe ce que font les utilisateurs dans leur environnement informatique, les activités auxquelles ils participent génèrent des artefacts de sécurité. Notez que, dans la plupart des cas, les personnes malveillantes effacent les journaux. Il s'agit bien souvent de la première étape qu'ils prennent pour cacher leurs méfaits.

Cependant, lorsque des utilisateurs accèdent à leur propre ordinateur sur place ou à distance, la preuve de l'utilisation qu'ils en font est stockée dans les journaux des événements. Operational Insights collecte ces artefacts *dès qu'ils ont lieu*, avant que quiconque ne puisse les falsifier. Vous pouvez ainsi effectuer différents types d'analyses en mettant en corrélation les données de plusieurs ordinateurs.

**Comprendre comment configurer et collecter des événements d'audit**

Pour tirer le meilleur parti du pack d'analyse décisionnelle de sécurité et d'audit, vous devez configurer le niveau d'événements d'audit collectés par votre environnement Windows. Le but est qu'il soit adapté au mieux à vos besoins. Pour ce faire, vous pouvez utiliser les ressources suivantes.

- [Comment configurer les paramètres de stratégie de sécurité](https://technet.microsoft.com/library/dn135243(v=ws.10).aspx\)

- [Configuration de la stratégie d'audit avancée](https://technet.microsoft.com/library/jj852202(v=ws.10).aspx\)

- [Recommandations pour la stratégie d'audit](https://technet.microsoft.com/library/dn487457.aspx)

**Activer AppLocker**

Vous devez également activer les événements AppLocker pour obtenir des informations détaillées sur les exécutions de processus qui se produisent dans votre environnement informatique. Pour plus d'informations, consultez l'article [Configurer une stratégie AppLocker pour les audits uniquement](https://technet.microsoft.com/library/hh994622.aspx)

**Configurer le niveau d'audit pour les événements Windows**

L'environnement informatique de Windows vous permet de configurer le niveau de capture des enregistrements relatifs à la sécurité. Par exemple, vous pouvez configurer votre environnement pour qu'un événement soit généré chaque fois qu'un utilisateur accède à un fichier, lit ou ouvre un fichier. Le niveau de détail que vous souhaitez collecter varie en fonction de vos besoins. Cependant, chaque option que vous activez présente une forme de coût, car vous devez stocker toutes les informations que vous collectez. C'est pourquoi, dans de nombreux environnements informatiques d'organisations, la collecte de données relatives à la lecture ou à l'écriture d'objet n'est pas activée. Chaque fois qu'une personne accède à un fichier, des milliers d'événements peuvent être générés, dont certains sont inutiles. Pour définir le niveau de la collecte que souhaitez mettre en œuvre, fiez-vous à votre propre jugement.

>[AZURE.NOTE]Si vous utilisez l'agent direct et que vous disposez d'un serveur proxy au sein de votre organisation, vous devez le configurer pour permettre à l'agent d'accéder à Operational Insights. Pour plus d'informations, consultez la page [Configuration des paramètres de pare-feu et de proxy](operational-insights-proxy-firewall.md).

## Meilleures pratiques relatives aux enquêtes de modèle de faille de sécurité

**Enquêter sur les modèles d'activités anormales**

Généralement les failles de sécurité proviennent d'informations d'identification légitimes et nécessitent qu'un utilisateur malveillant essaie d'obtenir des privilèges élevés grâce à des attaques. Le pack d'analyse décisionnelle de sécurité et d'audit n'aborde pas la question de la détection d'intrusion. Il vous aide à enquêter sur les modèles d'activités anormales et à les découvrir, avec des problèmes importants. Par exemple, vous devez enquêter sur les activités anormales suivantes et sur celles qui apparaissent sous **Problèmes importants**.

- connexions inhabituelles sur l'ordinateur d'un utilisateur qui ne l'utilise normalement pas

- énumération de réseau inhabituelle à partir d'ordinateurs ou d'utilisateurs atypiques

- nouveaux comptes d'utilisateur créés avec des droits d'administration

- modifications apportées aux stratégies de sécurité ou de journalisation

- exécutables suspects

## Meilleures pratiques relatives aux scénarios d'audit

Votre organisation a certainement mis en place des réglementations et des stratégies de conformité relatives au réseau et aux ordinateurs que vous devez respecter. Cela nécessite d'importantes quantités d'enregistrements d'audit. Par exemple, si votre organisation est une société de financement, vous devez conserver des enregistrements qui vous permettent de retrouver l'utilisateur qui a effectué une opération spécifique sur le réseau, à un moment précis, et en obtenir la preuve. Vous pouvez également être amené à générer des rapports détaillés concernant l'activité d'un utilisateur en particulier ou d'un serveur choisi sur demande, et revenir plusieurs mois \(parfois même plusieurs années\) en arrière.

Vous pouvez utiliser le pack d'analyse décisionnelle de sécurité et d'audit pour collecter des données d'audit à travers l'ensemble de votre environnement informatique, que vos ordinateurs soient sur site ou dans le cloud. Toutes les données d'audit sont stockées et indexées. De plus, avec un abonnement de niveau Premium à Operational Insights, toutes les données sont stockées indéfiniment. Vous pouvez ensuite afficher les données d'audit, effectuer des recherches et mettre en corrélation différents types de données et ordinateurs pour obtenir des résultats complets, peu importe l'intervalle de temps dans la mesure où les données ont été collectées.

**Utiliser une stratégie de groupe pour collecter des données d'audit**

Toutes les données d'audit que vous pourriez souhaiter collecter et envoyer vers Operational Insights sont entièrement gérées par une stratégie de groupe. Vous pouvez ainsi définir des configurations de sécurité via des objets de stratégie de groupe \(GPO\), liés à des conteneurs Active Directory comme des sites, des domaines et des unités d'organisation. Ces GPO vous aident à gérer les paramètres de sécurité. Les données de la stratégie sont enregistrées et envoyées ultérieurement au service d'Operational Insights.

**Utiliser AppLocker pour collecter des données d'audit**

En plus des paramètres de stratégie locale, si vous utilisez AppLocker pour collecter des données d'audit, Operational Insights les rassemble pour que vous puissiez ensuite les afficher.

<!--HONumber=54-->