---
title: Solution DNS Analytics dans Azure Log Analytics | Microsoft Docs
description: "Configurez et utilisez la solution DNS Analytics dans Log Analytics pour rassembler des informations liées à la sécurité, aux performances et aux opérations dans une infrastructure DNS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 4473ca68374b96f10c60282135e83f7ec390bb48
ms.lasthandoff: 04/20/2017


---

# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Rassembler des informations sur votre infrastructure DNS avec la solution DNS Analytics (version préliminaire)

Cet article explique comment configurer et utiliser la solution DNS Analytics dans Log Analytics pour rassembler des informations liées à la sécurité, aux performances et aux opérations dans une infrastructure DNS.

DNS Analytics vous aide à effectuer les tâches suivantes :

- Identifier les clients qui tentent de résoudre des noms de domaine malveillant
- Identifier les enregistrements de ressources obsolètes
- Identifier les noms de domaine fréquemment interrogés et les clients DNS communiquant
- Afficher la charge de la demande sur les serveurs DNS
- Afficher les échecs d’inscription DNS dynamique

La solution collecte, analyse et met en corrélation les journaux d’audit et d’analyse DNS Windows et d’autres données connexes de vos serveurs DNS.

## <a name="connected-sources"></a>Sources connectées

Le tableau suivant décrit les sources connectées qui sont prises en charge par cette solution.

| **Source connectée** | **Support** | **Description** |
| --- | --- | --- |
| [Agents Windows](log-analytics-windows-agents.md) | Oui | La solution collecte des informations DNS auprès d’agents Windows. |
| [Agents Linux](log-analytics-linux-agents.md) | Non | La solution ne collecte aucune information DNS auprès d’agents Linux directs. |
| [Groupe d’administration SCOM](log-analytics-om-agents.md) | Oui | La solution collecte des informations DNS auprès d’agents dans un groupe d’administration SCOM connecté. Aucune connexion directe entre l’agent SCOM et OMS n’est obligatoire. Les données sont transférées du groupe d’administration au référentiel OMS. |
| [Compte Stockage Azure](log-analytics-azure-storage.md) | Non | Le stockage Azure n’est pas utilisé par la solution. |

### <a name="data-collection-details"></a>Détails sur la collecte de données

La solution collecte des données liées aux inventaires DNS et aux événements DNS sur les serveurs DNS sur lesquels un agent Log Analytics est installé. Ces données sont ensuite chargées vers Log Analytics, puis affichées dans le tableau de bord de la solution. Les données liées aux inventaires, comme le nombre de serveurs, de zones et d’enregistrements de ressources DNS, sont collectées en exécutant les applets de commande PowerShell de DNS. Les données sont mises à jour tous les deux jours. Les données liées aux événements sont collectées quasiment en temps réel à partir des [journaux d’analyse et d’audit](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fournis par le biais de la fonctionnalité améliorée des diagnostics et journalisation DNS de Windows Server 2012 R2.

## <a name="configuration"></a>Configuration

Utilisez les informations suivantes pour configurer la solution.

- Vous devez disposer d’un agent [Windows](log-analytics-windows-agents.md) ou [Operations Manager](log-analytics-om-agents.md) sur chaque serveur DNS que vous souhaitez surveiller.
- Ajoutez la solution DNS Analytics à votre espace de travail OMS depuis la [Place de marché Azure](https://aka.ms/dnsanalyticsazuremarketplace) ou en procédant de la manière décrite dans [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

La solution commence la collecte des données sans que vous ne deviez procéder à une configuration supplémentaire. Toutefois, vous pouvez utiliser la configuration suivante pour personnaliser la collecte de données.

###  <a name="configure-the-solution"></a>Configuration de la solution

Dans le tableau de bord de la solution, cliquez sur **Configuration** pour ouvrir la page Configuration de DNS Analytics. Voici les deux types de modification de configuration que vous pouvez effectuer :

- **Noms de domaine dans la liste verte** La solution ne traite pas toutes les requêtes de recherche. Elle gère une liste verte des suffixes de nom de domaine. Les requêtes de recherche, qui résolvent les noms de domaine qui correspondent à des suffixes de noms de domaine de cette liste verte, ne sont pas traitées par la solution. Ne pas traiter les noms de domaine figurant dans la liste verte permet d’optimiser les données envoyées à Log Analytics. La liste verte par défaut inclut des noms de domaine public populaires, tels que www.google.com et www.facebook.com. Vous pouvez afficher la liste par défaut complète à l’aide de la barre de défilement.

Vous pouvez modifier la liste pour ajouter (ou supprimer) un suffixe de nom de domaine dont vous ne souhaitez pas afficher les informations de recherche.

- **Seuil de clients communiquant** Les clients DNS, qui dépassent le seuil correspondant au nombre de demandes de recherche, sont mis en surbrillance dans le panneau **Clients DNS**. Par défaut, le seuil est défini sur 1 000 et vous pouvez le modifier.

![noms de domaine de la liste verte](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Packs d’administration

Si vous utilisez Microsoft Monitoring Agent (MMA) pour vous connecter à votre espace de travail OMS, le pack d’administration suivant est installé.

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Si votre groupe d’administration SCOM est connecté à votre espace de travail OMS, les packs d’administration suivants sont installés dans SCOM quand vous ajoutez cette solution. Ces packs d’administration ne nécessitent aucune opération de configuration ou de maintenance.

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

Pour plus d’informations sur la façon dont ces packs d’administration de solution sont mis à jour, consultez [Connecter Operations Manager à Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-solution"></a>Utiliser la solution

Cette section explique toutes les fonctions du tableau de bord et comment les utiliser.

Une fois que vous avez ajouté la solution DNS Analytics à votre espace de travail, la vignette de la solution affichée sur la page de présentation d’OMS indique un résumé rapide de votre infrastructure DNS. Il répertorie le nombre de serveurs DNS sur lesquels les données sont collectées et le nombre de demandes effectuées par les clients pour résoudre les domaines malveillants au cours des dernières 24 heures. Lorsque vous cliquez sur la vignette, le tableau de bord de la solution s’ouvre.

![Vignette DNS Analytics](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Tableau de bord de solution

Le tableau de bord de la solution contient une synthèse des informations portant sur les différentes fonctionnalités de la solution. Il inclut également des liens vers la vue détaillée de diagnostic et d’analyse d’investigation. Par défaut, les données sont affichées pour les sept derniers jours. Vous pouvez modifier la plage de dates et d’heures avec le contrôle de sélection de date/heure, semblable à l’illustration de l’image suivante.

![contrôle de sélection de temps](./media/log-analytics-dns/dns-time.png)

Le tableau de bord de la solution affiche les panneaux suivants :

**Sécurité DNS** : signale les clients DNS qui tentent de communiquer avec des domaines malveillants. À l’aide des flux d’évaluation des menaces de Microsoft, DNS Analytics peut détecter les adresses IP des clients qui essaient d’accéder aux domaines malveillants. Dans de nombreux cas, les appareils infectés par des programmes malveillants &quot;composent un numéro&quot; qui renvoie au centre de &quot;commande et de contrôle&quot; du domaine malveillant en résolvant le nom de domaine des programmes malveillants.

![Panneau Sécurité DNS](./media/log-analytics-dns/dns-security-blade.png)

Lorsque vous cliquez sur une adresse IP de client dans la liste, la fonctionnalité Recherche dans les journaux s’ouvre et affiche les détails de la recherche de la requête respective. Dans l’exemple suivant, DNS Analytics a détecté que la communication a été effectuée avec un [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot).

![résultats de recherche dans les journaux affichant un ircboot](./media/log-analytics-dns/ircbot.png)

Ces informations vous aident à identifier les éléments suivants :

- IP du client qui a initié la communication
- Nom de domaine résolvant l’adresse IP malveillante
- Adresses IP de résolution du nom de domaine
- Adresse IP malveillante
- Gravité du problème
- Motif du blocage de l’adresse IP malveillante
- Heure de détection

**Domaines interrogés** : indique les noms de domaine les plus fréquemment interrogés par les clients DNS dans votre environnement. Vous pouvez afficher la liste de tous les noms de domaine interrogés et explorer les détails de demande de recherche d’un nom de domaine spécifique dans Recherche dans les journaux.

![Panneau Domaines interrogés](./media/log-analytics-dns/domains-queried-blade.png)

**Clients DNS** : signale les clients qui **excèdent le seuil** correspondant au nombre de requêtes dans la période choisie. Vous pouvez afficher dans Recherche dans les journaux la liste de tous les clients DNS et les détails des requêtes qu’ils ont effectuées.

![Panneau Clients DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Inscriptions DNS dynamiques** : signale les échecs d’inscription de nom. Tous les échecs d’inscription pour les [enregistrements de ressources](https://en.wikipedia.org/wiki/List_of_DNS_record_types) d’adresse (type A et AAAA) sont mis en surbrillance avec les adresses IP des clients qui ont effectué les demandes d’inscription. Vous pouvez ensuite utiliser ces informations pour rechercher la cause principale de l’échec de l’inscription :

1. Recherchez dans la zone qui fait autorité le nom que le client essaie de mettre à jour.
2. Utilisez la solution pour vérifier les informations d’inventaire de cette zone.
3. Vérifiez que la mise à jour dynamique de la zone est activée.
4. Vérifiez si la zone est configurée pour la mise à jour dynamique sécurisée ou non.

![Panneau Inscriptions DNS dynamiques](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Demandes d’inscriptions de noms** : la vignette supérieure indique une tendance du nombre de demandes de mise à jour dynamique DNS réussies et en échec. La vignette inférieure répertorie les 10 principaux clients ayant envoyé des demandes de mise à jour DNS en échec aux serveurs DNS, triés en fonction du nombre d’échecs.

![Panneau Demandes d’inscriptions de noms ](./media/log-analytics-dns/name-reg-req-blade.png)

**Exemples de requêtes DNS Analytics** : contient la liste des requêtes de recherche les plus courantes qui extraient directement les données d’analyse brutes.

![exemples de requêtes](./media/log-analytics-dns/queries.png)

Vous pouvez utiliser ces requêtes comme point de départ pour créer vos propres requêtes de génération de rapports personnalisés.

- **Liste des serveurs** : ce lien ouvre la page Recherche dans les journaux DNS qui affiche la liste de tous les serveurs DNS avec leur nom de domaine complet associé, le nom de domaine, le nom de la forêt et les adresses IP de serveur.
- **Liste des zones DNS** : ce lien ouvre la page Recherche dans les journaux DNS qui affiche la liste de toutes les zones DNS avec le nom de la zone associée, l’état de mise à jour dynamique, les serveurs de noms et l’état de signature DNSSEC.
- **Enregistrements de ressources non utilisés** : ce lien ouvre la page Recherche dans les journaux DNS et affiche la liste de tous les enregistrements de ressources inutilisés/obsolètes. Cette liste contient le nom et le type d’enregistrement de ressource, le serveur DNS associé, l’heure de génération d’enregistrement et le nom de la zone. Cette liste permet d’identifier les enregistrements de ressources DNS qui ne sont plus utilisés. En fonction de ces informations, vous pouvez ensuite supprimer ces entrées des serveurs DNS.
- **Charge de requête des serveurs DNS** : ce lien ouvre la page Recherche dans les journaux DNS où vous pouvez obtenir la perspective de la charge DNS sur vos serveurs DNS pour vous aider à planifier la capacité des serveurs. Vous pouvez passer à l’onglet **Mesures** pour modifier l’affichage et le remplacer par une visualisation graphique. Cet affichage vous permet de comprendre de quelle façon la charge DNS est répartie entre vos serveurs DNS. Il indique les tendances des taux de requête DNS pour chaque serveur.
    ![Résultats de Recherche dans les journaux des requêtes des serveurs DNS](./media/log-analytics-dns/dns-servers-query-load.png)  
- **Charge de requête des zones DNS** : ce lien ouvre la page Recherche dans les journaux DNS dans laquelle vous pouvez afficher les statistiques de requête de zone DNS par seconde de toutes les zones sur les serveurs DNS gérés par la solution. Cliquez sur l’onglet **Mesures** pour modifier l’affichage et passer des enregistrements détaillés à une visualisation graphique des résultats.
- **Événements de configuration** : ce lien ouvre la page Recherche dans les journaux DNS où vous pouvez afficher tous les événements de modification de la configuration de DNS ainsi que les messages associés. Vous pouvez ensuite filtrer ces événements en fonction de l’heure de l’événement, de l’ID d’événement, du serveur DNS ou de la catégorie de la tâche. Vous pouvez ainsi auditer les modifications apportées à des serveurs DNS spécifiques à des moments donnés.
- **Journal analytique DNS** : ce lien ouvre la page Recherche dans les journaux DNS où vous pouvez afficher tous les événements analytiques sur l’ensemble des serveurs DNS gérés par la solution. Vous pouvez alors filtrer ces événements en fonction de l’heure de l’événement, de l’ID d’événement, du serveur DNS, de l’adresse IP du client qui a effectué la requête de recherche et de la catégorie du type de requête. Les événements d’analyse de serveur DNS permettent le suivi des activités sur le serveur DNS. Un événement d’analyse est enregistré chaque fois que le serveur envoie ou reçoit des informations DNS.

### <a name="dns-log-search"></a>Recherche dans les journaux DNS

Sur la page Recherche, vous pouvez créer une requête, puis lorsque vous effectuez une recherche, vous pouvez filtrer les résultats en utilisant des contrôles de facette. Vous pouvez également créer des requêtes avancées pour la transformation, le filtrage et les rapports relatifs aux résultats. Vous pouvez commencer à l’aide des requêtes suivantes.

Dans le champ de requête de recherche, tapez `Type=DnsEvents` pour afficher tous les événements DNS générés par les serveurs DNS gérés par la solution. Les résultats indiquent les données de journal pour tous les événements liés aux requêtes de recherche, aux inscriptions dynamiques et aux modifications de la configuration.

![recherche dans les journaux des événements dns](./media/log-analytics-dns/log-search-dnsevents.png)  

- Pour afficher les données de journal correspondant à la requête de recherche, filtrez avec le sous-type **LookUpQuery** à partir du contrôle de facette LHS. Une liste/un tableau contenant les événements de requête de recherche pour la période sélectionnée s’affiche.
- Pour afficher les données de journal correspondant aux inscriptions dynamiques, filtrez avec le sous-type **DynamicRegistration** à partir du contrôle de facette LHS. Une liste/un tableau contenant tous les événements d’inscription dynamique pour la période sélectionnée s’affiche.
- Pour afficher les données de journal correspondant aux modifications de configuration, filtrez avec le sous-type **ConfigurationChange** à partir du contrôle de facette LHS. Une liste/un tableau contenant tous les événements de modification de configuration pour la période sélectionnée s’affiche.

Dans le champ de requête de recherche, tapez `Type=DnsInventory` pour afficher toutes les données associées aux inventaires DNS pour les serveurs DNS gérés par la solution. Les résultats indiquent les données de journal pour les serveurs DNS, les zones DNS et les enregistrements de ressources.
![recherche dans les journaux d’inventaire dns](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Commentaires

Vous pouvez envoyer vos commentaires de deux façons :

- **UserVoice** Publiez vos idées portant sur des fonctionnalités de DNS Analytics à travailler. Visitez la [page OMS UserVoice](https://aka.ms/dnsanalyticsuservoice).
- **Rejoignez notre cohorte** Nous sommes toujours enthousiastes quand de nouveaux clients rejoignent notre cohorte pour obtenir un accès en avant-première aux nouvelles fonctionnalités et nous aider à améliorer DNS Analytics. Si vous souhaitez rejoindre notre cohorte, répondez à cette [enquête rapide](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Étapes suivantes

- [Effectuez des recherches dans les journaux](log-analytics-log-searches.md) pour afficher des enregistrements de journal DNS détaillés.

