---
title: "Migration de vos données vers SQL Data Warehouse | Microsoft Docs"
description: "Conseils relatifs à la migration de vos données vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: d78f954a-f54c-4aa4-9040-919bc6414887
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 06/29/2017
ms.author: joeyong;barbkess
ms.openlocfilehash: 0d156bc2eecf8220bd5ff4eb811d91482f216837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-data"></a>Migration de vos données
Les données peuvent être déplacées à partir de différentes sources dans SQL Data Warehouse avec divers outils.  Les solutions ADF Copy, SSIS et bcp peuvent toutes être utilisées à cette fin. Toutefois, à mesure de l’augmentation du volume des données, vous avez tout intérêt à réfléchir à un moyen de diviser le processus de migration des données en étapes. Ce faisant, vous vous donnez les moyens d’optimiser chacune des phases en matière de performance et de résilience afin de garantir une migration sans heurts des données.

Cet article s’intéresse tout d’abord aux scénarios simples de migration d’ADF Copy, de SSIS et de bcp. Nous évoquons ensuite de manière plus approfondie les différents moyens d’optimiser la migration.

## <a name="azure-data-factory-adf-copy"></a>Azure Data Factory (ADF) Copy
[ADF Copy][ADF Copy] fait partie intégrante d’[Azure Data Factory][Azure Data Factory]. ADF Copy peut être utilisée pour exporter vos données vers des fichiers plats hébergés sur un espace de stockage local, vers des fichiers plats distants conservés dans un espace de stockage d’objets Blob Microsoft Azure ou directement vers SQL Data Warehouse.

Si vos données sont hébergées initialement dans des fichiers plats, il vous faudra dans un premier temps les transférer vers un espace de stockage d’objets Blob Azure avant de lancer le chargement dans SQL Data Warehouse. Une fois que les données sont transférées dans Stockage Blob Azure, vous pouvez utiliser de nouveau [ADF Copy][ADF Copy] pour les charger dans SQL Data Warehouse.

PolyBase propose également une option hautes performances dédiée au chargement des données. Si vous optez pour cette solution, cela ne signifie pas que vous utilisez deux outils au lieu d’un. Si vous avez besoin de performances optimales, utilisez PolyBase. Si vous souhaitez profiter d’une expérience valorisant un outil unique (et que le volume de données n’est pas considérable), tournez-vous vers ADF.


> 
> 

Consultez l’article suivant afin de découvrir de formidables [exemples ADF][ADF samples].

## <a name="integration-services"></a>Integration Services
Integration Services (SSIS) est un outil puissant et flexible d’extraction, de transformation et de chargement (ETL, Extract Transform and Load) qui prend en charge des workflows complexes, la transformation des données et diverses options de chargement des données. Utilisez SSIS afin de procéder à un transfert simple de données vers Microsoft Azure, ou dans le cadre d’une migration plus importante.

> [!NOTE]
> SSIS peut exporter des données vers le format UTF-8 sans laisser de marque d’ordre d’octet dans le fichier. Pour procéder à la configuration adéquate, vous devez tout d’abord utiliser le composant de colonne dérivée afin de convertir les données caractères dans le flux de données pour utiliser la page de code UTF-8 65001. Une fois que les colonnes ont été converties, procédez à l’écriture des données sur l’adaptateur de destination du fichier plat en vérifiant que la page de code 65001 a été sélectionnée pour le fichier.
> 
> 

La solution SSIS se connecte à SQL Data Warehouse de la manière dont elle se connecte à un déploiement SQL Server. Cependant, vos connexions doivent utiliser un gestionnaire de connexions ADO.NET. Vous devez également vous assurer de configurer le paramètre « Utiliser l’insertion de bloc lorsqu’elle est disponible » afin d’optimiser le débit. Pour en savoir plus sur cette propriété, consultez l’article [Adaptateur de destination ADO.NET][ADO.NET destination adapter].

> [!NOTE]
> La connexion à Microsoft Azure SQL Data Warehouse à l’aide d’OLEDB n’est pas prise en charge.
> 
> 

Par ailleurs, il est toujours possible qu’un package soit mis en échec en raison d’une limitation ou de problèmes réseau. Concevez les packages afin qu’ils puissent être repris à partir du point de défaillance, sans avoir à réitérer les tâches accomplies avant la mise en échec.

Pour en savoir plus, consultez la [documentation relative à SSIS][SSIS documentation].

## <a name="bcp"></a>bcp
bcp est un utilitaire de ligne de commande qui est conçu pour l’importation et l’exportation des données des fichiers plats. Des activités de transformation peuvent avoir lieu durant l’exportation des données. Pour effectuer des transformations simples, utilisez une requête afin de sélectionner et de transformer les données. Une fois qu’ils sont exportés, les fichiers plats peuvent être directement chargés dans la base de données cible SQL Data Warehouse.

> [!NOTE]
> Il est souvent judicieux d’encapsuler les transformations utilisées durant l’exportation des données dans une vue sur le système source. Cela vous garantit que la logique est conservée et que le processus est répétable.
> 
> 

Les avantages de bcp sont les suivants :

* Simplicité. Les commandes bcp sont simples à concevoir et à exécuter.
* Processus de chargement redémarrable. Une fois que les données ont été exportées, le chargement peut être exécuté un nombre illimité de fois.

Les limites de l’utilitaire bcp sont les suivantes :

* bcp fonctionne avec des fichiers plats en format tableau uniquement. Cette solution ne prend pas en charge les fichiers aux formats xml ou JSON.
* Les fonctionnalités de transformation de données sont utilisables durant la phase d’exportation uniquement, et sont simples par nature.
* L’utilitaire bcp n’a pas été modifié afin d’offrir une fiabilité acceptable durant le chargement des données sur Internet. Toute instabilité du réseau peut provoquer une erreur de chargement.
* bcp s’appuie sur le schéma initialement présent dans la base de données cible avant le chargement.

Pour en savoir plus, consultez la rubrique [Utilisation de bcp pour charger des données dans SQL Data Warehouse][Use bcp to load data into SQL Data Warehouse].

## <a name="optimizing-data-migration"></a>Optimisation de la migration des données
Un processus de migration des données SQLDW peut être efficacement divisé en trois étapes distinctes :

1. Exportation des données sources
2. Transfert des données vers Microsoft Azure
3. Chargement dans la base de données SQLDW

Chaque étape peut être optimisée de manière isolée afin de concevoir un processus fiable, redémarrable et résistant de migration qui génère de hautes performances à chaque phase.

## <a name="optimizing-data-load"></a>Optimisation du chargement des données
Si nous prenons le processus dans l’ordre inverse, nous constatons que PolyBase procure le moyen le plus rapide de charger des données. L’optimisation nécessaire à un processus de chargement PolyBase ajoutant des phases préalables à l’exécution des étapes précédentes, vous avez tout intérêt à comprendre ces phases supplémentaires en amont. Les voici :

1. Encodage des fichiers de données
2. Formatage des fichiers de données
3. Définition de l’emplacement des fichiers de données

### <a name="encoding"></a>Encodage
PolyBase nécessite d’utiliser des fichiers de données au format UTF-8 ou UTF-16FE. 



### <a name="format-of-data-files"></a>Formatage des fichiers de données
PolyBase requiert un terminateur de ligne fixe \n ou un renvoi à la ligne. Vos fichiers de données doivent être conformes à cette directive. Il n’existe aucune restriction relative aux terminateurs de chaînes ou de colonnes.

Vous devrez définir chacune des colonnes du fichier en tant que composante de table externe dans PolyBase. Vérifiez que l’ensemble des colonnes exportées sont requises et que les types définis sont conformes aux normes requises.

Veuillez vous référer à l’article [Migration de votre schéma] pour en savoir plus sur les types de données pris en charge.

### <a name="location-of-data-files"></a>Définition de l’emplacement des fichiers de données
SQL Data Warehouse utilise PolyBase pour charger des données exclusivement à partir d’objets Blob Microsoft Azure Storage. De fait, les données doivent avoir été préalablement transférées dans des objets Blob.

## <a name="optimizing-data-transfer"></a>Optimisation du transfert des données
Le transfert des données vers Microsoft Azure est l’une des phases les plus lentes de la migration des données. Cette étape peut être associée à une problématique de bande passante et entraver la progression, en réduisant la fiabilité du réseau. Par défaut, la migration des données vers Microsoft Azure s’effectue via Internet. Ainsi, la probabilité d’erreurs de transfert est raisonnablement élevée. Toutefois, ces erreurs peuvent nécessiter le renvoi complet ou partiel des données.

Fort heureusement, vous disposez de plusieurs options permettant d’améliorer la rapidité et la résilience de ce processus :

### <a name="expressrouteexpressroute"></a>[ExpressRoute][ExpressRoute]
Vous pouvez éventuellement vous tourner vers [ExpressRoute][ExpressRoute] afin d’accélérer la vitesse de transfert. [ExpressRoute][ExpressRoute] vous procure une connexion exclusivement privée avec Azure, pour que la connexion ne passe pas par l’Internet public. Vous n’êtes aucunement obligé de recourir à cette option. Toutefois, sachez qu’elle améliore le débit de transfert des données à partir d’une installation sur site ou d’un emplacement de colocalisation vers Microsoft Azure.

Les avantages procurés par [ExpressRoute][ExpressRoute] sont les suivants :

1. Accroissement de la fiabilité
2. Augmentation de la vitesse du réseau
3. Réduction de la latence du réseau
4. Amélioration de la sécurité du réseau

[ExpressRoute][ExpressRoute] profite à de nombreux scénarios, pas uniquement à la migration.

Vous êtes intéressé ? Pour plus d’informations et pour consulter la tarification, accédez à la [documentation ExpressRoute][ExpressRoute documentation].

### <a name="azure-import-and-export-service"></a>Azure Import Export Service
Azure Import and Export Service est un processus de transfert de données conçu pour les transferts importants (plusieurs Go) à massifs (plusieurs To) de données dans Microsoft Azure. Il implique l’écriture de vos données sur des disques et leur transfert vers un centre de données Microsoft Azure. Ensuite, le contenu des disques est chargé dans des objets Blob Microsoft Storage en votre nom.

Voici une vue de niveau supérieur du processus d’importation-exportation :

1. Configuration d’un conteneur d’objets Blob Microsoft Storage dédié à la réception des données
2. Exportation de vos données vers le stockage local
3. Copie de vos données sur des disques durs 3,5 pouces SATA II/III à l’aide de [l’outil Azure Import/Export]
4. Création d’une tâche d’importation à l’aide du service Azure Import Export, avec les fichiers journaux produits par [l’outil Azure Import/Export]
5. Livraison des disques à votre centre de données Microsoft Azure désigné
6. Transfert de vos données vers votre conteneur d’objets Blob Microsoft Azure Storage
7. Chargement de vos données dans SQLDW à l’aide de PolyBase

### <a name="azcopyazcopy-utility"></a>Utilitaire [AZCopy][AZCopy]
L’utilitaire [AZCopy][AZCopy] est l’outil idéal pour transférer vos données dans des objets blob de stockage Azure. Il est conçu pour des transferts de données modestes (plusieurs Mo) à très importants (plusieurs Go). [AZCopy] a également été conçu pour fournir un débit efficace et robuste lors du transfert de données vers Azure. Par conséquent, cette solution convient parfaitement au transfert de données. Une fois que le transfert a été effectué, vous pouvez charger les données dans SQL Data Warehouse à l’aide de PolyBase. Vous pouvez également intégrer AZCopy dans vos packages SSIS, en appliquant une tâche d’exécution du processus.

Il vous faudra dans un premier temps télécharger et installer AZCopy. Une [version de production][production version] et une [préversion][preview version] sont disponibles.

Pour charger un fichier à partir de votre système de fichier, vous devrez recourir à une commande de ce type :

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Voici les étapes possibles d’un processus de niveau supérieur :

1. Configuration d’un conteneur de stockage d’objets Blob Microsoft Azure dédié à la réception des données
2. Exportation de vos données vers le stockage local
3. Traitement de vos données à l’aide d’AZCopy dans le contenu de stockage d’objets Blob Microsoft Azure
4. Charger les données dans SQL Data Warehouse à l’aide de PolyBase

Documentation complète disponible : [AZCopy][AZCopy].

## <a name="optimizing-data-export"></a>Optimisation de l’exportation des données
En plus d’assurer la conformité de l’exportation avec les exigences associées à PolyBase, vous pouvez également chercher à optimiser l’exportation des données afin d’améliorer davantage le processus.



### <a name="data-compression"></a>Compression des données
PolyBase peut lire les données compressées au format gzip. Si vous n’êtes pas en mesure de compresser vos données au format gzip, il vous faudra réduire le volume de données transmis sur le réseau.

### <a name="multiple-files"></a>Fichiers multiples
La fragmentation de gros tableaux en plusieurs fichiers vous permet d’accélérer la vitesse d’exportation, mais facilite également le redémarrage des transferts et améliore la gestion globale des données transférées vers le stockage d’objets Blob Microsoft Azure. PolyBase permet notamment de lire l’ensemble des fichiers stockés dans un dossier et de les traiter en tant que tableau unique. Par conséquent, il est avisé de conserver les fichiers associés à un tableau dans un dossier séparé.

PolyBase prend également en charge une fonction appelée « balayage de dossier récursif ». Vous pouvez la mettre à profit pour optimiser l’organisation de vos données exportées et ainsi améliorer la gestion de vos données.

Pour en savoir plus sur le chargement des données à l’aide de PolyBase, consultez la section [Utilisation de PolyBase pour charger des données dans SQL Data Warehouse][Use PolyBase to load data into SQL Data Warehouse].

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la migration, consultez la section [Migration de votre solution vers SQL Data Warehouse][Migrate your solution to SQL Data Warehouse].
Pour obtenir des conseils supplémentaires en matière de développement, consultez la [vue d’ensemble du développement][development overview].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/common/storage-use-azcopy.md
[ADF Copy]: ../data-factory/v1/data-factory-data-movement-activities.md 
[ADF samples]: ../data-factory/v1/data-factory-samples.md
[ADF Copy examples]: ../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md
[development overview]: sql-data-warehouse-overview-develop.md
[Migrate your solution to SQL Data Warehouse]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Use bcp to load data into SQL Data Warehouse]: sql-data-warehouse-load-with-bcp.md
[Use PolyBase to load data into SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[ExpressRoute documentation]: http://azure.microsoft.com/documentation/services/expressroute/

[production version]: http://aka.ms/downloadazcopy/
[preview version]: http://aka.ms/downloadazcopypr/
[ADO.NET destination adapter]: https://msdn.microsoft.com/library/bb934041.aspx
[SSIS documentation]: https://msdn.microsoft.com/library/ms141026.aspx
