<properties
	pageTitle="Plateformes d’analyse : comparaison d’Apache Storm et de Stream Analytics | Microsoft Azure"
	description="Comparez Apache Storm et Stream Analytics pour vous aider à choisir une plateforme d’analyse cloud. Découvrez les fonctionnalités et les différences."
	keywords="plateforme d’analyse, plateformes d’analyse, plateforme d’analyse cloud, comparaison avec storm"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>

# Conseils pour choisir une plateforme d’analyse de diffusion en continu : comparaison d’Apache Storm et d’Azure Stream Analytics

Comparez Apache Storm et Azure Stream Analytics pour vous aider à choisir une plateforme d’analyse cloud. Identifiez les propositions de valeur de Stream Analytics et d’Apache Storm en tant que service géré sur Azure HDInsight, et choisissez la solution qui convient aux cas d’utilisation de votre entreprise.

Si ces deux plateformes d’analyse fournissent les avantages d’une solution PaaS, il existe quelques fonctionnalités principales qui les différencient. Ces fonctionnalités et les limitations des services sont répertoriées ci-dessous pour vous aider à identifier la solution qui vous permettra d’atteindre vos objectifs.

## Comparaison de Storm et de Stream Analytics : fonctionnalités générales ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache&#160;Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Open Source</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Non. Azure Stream Analytics est une offre propriétaire de Microsoft.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Oui. Apache Storm est une technologie sous licence Apache.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Pris en charge par Microsoft</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Oui
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Oui
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Configuration matérielle requise</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Aucune configuration matérielle requise. Azure Stream Analytics est un service Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Aucune configuration matérielle requise. Apache&#160;Storm est un service Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unité de niveau supérieur</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Azure Stream Analytics permet aux clients de déployer et de surveiller les travaux de diffusion en continu.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Apache Storm sur HDInsight permet aux clients de déployer et de surveiller un cluster entier, qui peut héberger plusieurs travaux Storm ainsi que d’autres charges de travail (notamment le traitement).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prix</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Stream Analytics est facturé par volume de données traitées et par nombre d’unités de diffusion en continu (par heure d’exécution du travail) requis.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/stream-analytics/">Des informations de tarification supplémentaires sont disponibles ici.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Pour Apache Storm sur HDInsight, l’unité d’achat est basée sur le cluster et facturée en fonction de la durée d’exécution du cluster, indépendamment des travaux déployés.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/pricing/details/hdinsight/">Des informations de tarification supplémentaires sont disponibles ici.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Création sur chaque plateforme d’analyse ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache&#160;Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fonctionnalités&#160;: DSL&#160;SQL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Oui. Une prise en charge du langage&#160;SQL facile à utiliser est disponible.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Non. Les utilisateurs doivent écrire du code en Java&#160;C# ou utiliser les&#160;API Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fonctionnalités&#160;: Opérateurs temporels</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Des agrégats fenêtrés et des jointures temporelles sont pris en charge sans configuration supplémentaire.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Les opérateurs temporels doivent être implémentés par l’utilisateur.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Expérience de développement</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Création et débogage interactifs via le portail Azure sur des exemples de données.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    L’expérience de développement, de débogage et de surveillance est fournie via Visual Studio aux utilisateurs de&#160;.NET, tandis que pour Java et les autres langages, les développeurs peuvent utiliser l’IDE de leur choix.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge du débogage</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Stream Analytics propose un état de travail de base et des journaux d’opération comme moyen de déboguer, mais pour le moment, ce service n’offre pas la flexibilité de savoir ce qui est inclus dans les journaux et en quelle quantité, à savoir le mode détaillé.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Des journaux détaillés sont disponibles pour le débogage. Il existe deux&#160;moyens de présenter les journaux aux utilisateurs&#160;: via Visual Studio, ou les utilisateurs peuvent utiliser une connexion&#160;RDP au cluster pour accéder aux journaux.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge des fonctions définies par l’utilisateur (UDF)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Il n’existe actuellement aucune prise en charge des fonctions&#160;UDF.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Les fonctions&#160;UDF peuvent être écrites en C#, Java ou dans le langage de votre choix.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Code extensible&#160;-&#160;Code personnalisé </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Aucune prise en charge de code extensible n’existe dans Stream Analytics.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Oui, il est possible d’écrire du code personnalisé en&#160;C#, Java ou dans d’autres langages pris en charge sur Storm.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Sources de données et sorties ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache&#160;Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
		<tr>
            <td width="174" valign="top">
				<p>
				 <strong>Sources de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Les sources d’entrée prises en charge sont Azure Event Hubs et Azure Blob.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Des connecteurs sont disponibles pour Event Hubs, Service Bus, Kafka, etc. Des connecteurs non pris en charge peuvent être implémentés via du code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Les formats d’entrée pris en charge sont Avro, JSON et&#160;CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Tous les formats peuvent être implémentés via du code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Sorties</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Un travail de diffusion en continu peut avoir plusieurs sorties. Sorties prises en charge&#160;: Azure Event Hubs, Azure Blob Storage, Azure Tables, Azure SQL DB et PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Prise en charge de nombreuses sorties dans une topologie, chacune pouvant disposer d’une logique personnalisée pour le traitement en aval. Storm prêt à l’emploi inclut des connecteurs pour PowerBI, Azure Event Hubs, Azure Blob Store, Azure DocumentDB, SQL et&#160;HBase. Des connecteurs non pris en charge peuvent être implémentés via du code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats de codage de données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Stream Analytics requiert l’utilisation du format de données&#160;UTF-8.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Tous les formats de codage de données peuvent être implémentés via du code personnalisé.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Gestion et opérations ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache&#160;Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modèle de déploiement de travail</strong>
                </p>
                <p>
                    - <strong>Portail Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Le déploiement est implémenté via le portail Azure, PowerShell et les&#160;API&#160;REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Le déploiement est implémenté via le portail Azure, PowerShell, Visual Studio et les&#160;API&#160;REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Analyse (statistiques, compteurs, etc.)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    L’analyse est implémentée via le portail Azure et les&#160;API&#160;REST.
                </p>
                <p>
                    L’utilisateur peut également configurer des alertes Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    L’analyse est implémentée via l’interface utilisateur de Storm et les&#160;API&#160;REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensibilité</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Nombre d’unités de diffusion en continu pour chaque travail. Chaque unité de diffusion en continu traite jusqu’à 1&#160;Mo/s. Maximum de 50&#160;unités par défaut. Appel à l’augmentation de la limite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Nombre de nœuds dans le cluster Storm&#160;HDI. Aucune limite quant au nombre de nœuds (limite supérieure définie par votre quota Azure). Appel à l’augmentation de la limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de traitement des données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Les utilisateurs peuvent augmenter ou réduire le nombre d’unités de diffusion en continu pour améliorer le traitement des données ou optimiser les coûts.
                </p>
                <p>
                    Montée en puissance jusqu’à 1&#160;Go/s
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    L’utilisateur peut augmenter ou réduire la taille de cluster en fonction de ses besoins.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrêt/Reprise</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Arrêt et reprise à partir du dernier arrêt.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Arrêt et reprise à partir du dernier arrêt basé sur le filigrane.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Mise à jour de service et d’infrastructure</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Mise à jour corrective automatique sans temps d’arrêt.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Mise à jour corrective automatique sans temps d’arrêt.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuité d’activité via un service hautement disponible avec les contrats&#160;SLA garantis</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Contrat&#160;SLA de temps d’activité de 99,9&#160;%
                </p>
                <p>
                    Récupération automatique des défaillances
                </p>
                <p>
                    La récupération des opérateurs temporels avec état est intégrée.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Contrat&#160;SLA de temps d’activité de 99,9&#160;% du cluster Storm. Apache Storm est une plateforme de diffusion en continu à tolérance de panne. Cependant, il incombe aux clients de s’assurer que leurs travaux de diffusion en continu s’exécutent sans interruption.
                </p>
            </td>
        </tr>
    </tbody>
</table>
## Fonctionnalités avancées ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure Stream Analytics</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache&#160;Storm sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrivée tardive et gestion des événements de manière désordonnée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Stratégies configurables intégrées pour réorganiser, supprimer des événements ou régler l’heure de l’événement.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    L’utilisateur doit implémenter une logique pour gérer ce scénario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Données de référence</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    Données de référence disponibles à partir des objets blob Azure avec une taille maximale de 100&#160;Mo de cache de recherche en mémoire. L’actualisation des données de référence est gérée par le service.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Aucune limitation de la taille des données. Connecteurs disponibles pour HBase, DocumentDB, SQL Server et Azure. Des connecteurs non pris en charge peuvent être implémentés via du code personnalisé.
                </p>
                <p>
                    L’actualisation des données de référence doit être gérée par du code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Intégration à Machine Learning</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    En configurant des modèles Azure Machine Learning publiés en tant que fonctions lors de la création de travail&#160;ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(aperçu privé)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    Disponible via Storm Bolts.
                </p>
            </td>
        </tr>
    </tbody>
</table>

<!---HONumber=AcomDC_0727_2016-->