<properties
    pageTitle="Solution de données de communication dans Log Analytics | Microsoft Azure"
    description="Les données de communication sont des données de réseau et de performance centralisées issues d’ordinateurs sur lesquels des agents OMS sont installés, notamment Operations Manager et les agents connectés à Windows. Les données réseau sont associées à vos données de journaux pour vous aider à mettre les données en corrélation."
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
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="wire-data-solution-in-log-analytics"></a>Solution de données de communication dans Log Analytics

Les données de communication sont des données de réseau et de performance centralisées issues d’ordinateurs sur lesquels des agents OMS sont installés, notamment Operations Manager et les agents connectés à Windows. Les données réseau sont associées à vos données de journaux pour vous aider à mettre les données en corrélation. Les agents OMS installés sur les ordinateurs de votre infrastructure informatique surveillent les données réseau envoyées vers et à partir de ces ordinateurs pour les niveaux de réseau 2 et 3 du [modèle OSI](https://en.wikipedia.org/wiki/OSI_model) , notamment les différents protocoles et ports utilisés.

>[AZURE.NOTE] La solution de données de communication ne peut pas être ajoutée aux espaces de travail actuellement. Les clients qui ont déjà activé la solution de communication peuvent continuer à l’utiliser.

Par défaut, OMS recueille les données enregistrées dans les journaux concernant le processeur, la mémoire, les disques et les performances réseau à partir des compteurs intégrés à Windows. La collecte des données réseau et autres est effectuée en temps réel pour chaque agent, notamment les sous-réseaux et les protocoles de niveau application utilisés par l’ordinateur. Vous pouvez ajouter d’autres compteurs de performance dans la page Paramètres de l’onglet Journaux.

Si vous avez utilisé [sFlow](http://www.sflow.org/) ou d’autres logiciels avec le [protocole NetFlow de Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), les statistiques et les données fournies par les données de communication vous seront familières.

Voici quelques types de requêtes de recherche de journal intégrés :

- Agents qui fournissent des données de communication
- Adresse IP des agents fournissant des données de communication
- Communications sortantes par adresse IP
- Nombre d’octets envoyés par les protocoles d’application
- Nombre d’octets envoyés par un service d’application
- Octets reçus par différents protocoles
- Nombre total d’octets envoyés et reçus par adresse IP
- Adresses IP qui ont communiqué avec des agents sur le sous-réseau 10.0.0.0/8
- Latence moyenne pour les connexions qui ont été mesurées de manière fiable
- Processus informatiques ayant initié ou reçu du trafic réseau
- Quantité de trafic réseau pour un processus

Quand vous recherchez à l’aide de données de communication, vous pouvez filtrer et regrouper les données pour afficher des informations sur les principaux agents et protocoles. Vous pouvez aussi savoir quand certains ordinateurs (adresses IP/adresses MAC) ont communiqué, la durée de ces communications et la quantité de données envoyées. En fait, vous affichez des métadonnées sur le trafic réseau qui sont basées sur la recherche.

Toutefois, comme il s’agit de métadonnées, il n’est pas évident qu’elles soient utiles pour résoudre des problèmes complexes. Les données de communication dans OMS ne donnent pas une vue complète de toutes les données réseau. Elles ne sont donc pas censées être utilisées pour résoudre des problèmes complexes au niveau du paquet.
L’avantage de l’utilisation de l’agent (par rapport à d’autres méthodes de collecte) est que vous n’avez pas à installer d’appareils, à reconfigurer vos commutateurs réseau ni à effectuer de configuration compliquée. Les données de communication sont simplement basées sur un agent, qu’il vous suffit d’installer sur un ordinateur pour qu’il surveille son propre trafic réseau. Le recours à un agent est aussi utile quand vous souhaitez surveiller les charges de travail en cours d’exécution dans des fournisseurs de cloud ou des fournisseurs de services d’hébergement ou Microsoft Azure, dans les cas où l’utilisateur n’est pas propriétaire de la couche d’infrastructure.

En revanche, vous n’avez pas une visibilité complète de ce qui se passe sur le réseau si vous n’installez pas d’agents sur tous les ordinateurs de votre infrastructure réseau.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- La solution de données de communication acquiert des données à partir d’ordinateurs exécutant Windows Server 2012 R2, Windows 8.1 et systèmes d’exploitation ultérieurs.
- Vous devez installer Microsoft .NET Framework 4.0 ou ultérieur sur les ordinateurs à partir desquels vous souhaitez acquérir des données de communication.
- Ajoutez la solution de données de communication à votre espace de travail OMS en appliquant la procédure décrite dans la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).  Aucune configuration supplémentaire n’est requise.
- Si vous souhaitez afficher les données de communication pour une solution spécifique, celle-ci doit déjà être ajoutée à votre espace de travail OMS.

## <a name="wire-data-data-collection-details"></a>Détails sur la collecte de données de la solution de données de communication

Les données de communication collectent des métadonnées sur le trafic réseau à l’aide des agents que vous avez activés.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte de la solution de données de communication.


| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
|---|---|---|---|---|---|---|
|Windows (2012 R2 / 8.1 ou version ultérieure)|![Oui](./media/log-analytics-wire-data/oms-bullet-green.png)|![Oui](./media/log-analytics-wire-data/oms-bullet-green.png)|![Non](./media/log-analytics-wire-data/oms-bullet-red.png)|            ![Non](./media/log-analytics-wire-data/oms-bullet-red.png)|![Non](./media/log-analytics-wire-data/oms-bullet-red.png)| chaque minute|


## <a name="combining-wire-data-with-other-solution-data"></a>Combinaison de données de communication avec les données d’autres solutions

Les données retournées par les requêtes intégrées ci-dessus peuvent être intéressantes en elles-mêmes. Toutefois, les données de communication sont le plus utiles quand vous les combinez à des informations obtenues à partir d’autres solutions OMS. Par exemple, vous pouvez utiliser des données d’événements de sécurité recueilles par la solution de sécurité et d’audit, et les associer aux données de communication pour détecter les tentatives de connexion réseau inhabituelles pour des processus nommés.  Dans cet exemple, vous utiliseriez les opérateurs IN et DISTINCT pour joindre les points de données dans votre requête de recherche.

Configuration requise : Pour utiliser l’exemple suivant, vous devez avoir installé la solution de sécurité et d’audit. Toutefois, vous pouvez combiner des données acquises à partir d’autres solutions aux données de communication pour obtenir des résultats similaires.

### <a name="to-combine-wire-data-with-security-events"></a>Pour combiner des données de communication à des événements de sécurité

1. Sur la page Aperçu, cliquez sur la vignette **WireData** .
2. Dans la liste **Requêtes WireData courantes**, cliquez sur **Quantité de trafic réseau (en octets) par processus** pour afficher la liste des processus retournés.
    ![Requêtes WireData](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Si la liste de processus est trop longue pour être entièrement lisible, vous pouvez modifier la requête de recherche comme ceci :

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    L’exemple ci-dessous montre un processus nommé DancingPigs.exe, qui peut sembler suspect.
    ![Résultats de recherche WireData](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. À l’aide des données retournées dans la liste, cliquez sur un processus nommé. Dans cet exemple, nous avons cliqué sur DancingPigs.exe. Les résultats ci-dessous décrivent le type de trafic réseau, par exemple les communications sortantes sur différents protocoles.
    ![Résultats WireData montrant un processus nommé](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. La solution de sécurité et d’audit étant installée, vous pouvez rechercher les événements de sécurité qui ont la même valeur de champ ProcessName en modifiant votre requête de recherche avec les opérateurs de requête de recherche IN et DISTINCT. Vous pouvez procéder ainsi quand vos données de communication et les journaux d’autres solutions ont des valeurs du même format. Modifiez votre requête de recherche comme ceci :

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![résultats de données de communication montrant des données combinées](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. Dans les résultats ci-dessus, vous constatez que les informations de compte sont visibles. Vous pouvez maintenant affiner votre requête de recherche pour déterminer la fréquence à laquelle le compte (qui affiche les données de sécurité et d’audit) a été utilisé par le processus avec une requête semblable à la suivante :        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![résultats de données de communication montrant des données de compte](./media/log-analytics-wire-data/oms-wiredata-05.png)



## <a name="next-steps"></a>Étapes suivantes

- [Lancez une recherche dans les journaux](log-analytics-log-searches.md) pour afficher des enregistrements détaillés sur les recherches de données de communication.
- Consultez le billet de blog de Daniel intitulé [Using Wire Data in Operations Management Suite Log Search](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) (Utilisation des données de communication dans la recherche de journal Operations Management Suite) pour en savoir plus sur la fréquence de collecte des données et sur la façon de modifier les propriétés de collecte pour les agents Operations Manager.



<!--HONumber=Oct16_HO2-->


