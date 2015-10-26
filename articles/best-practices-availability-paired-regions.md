<properties
	pageTitle="Amélioration de la continuité des activités avec les paires régionales Azure"
	description="Utilisez les paires régionales pour maintenir la résilience des applications dans le cas d’échecs des centres de données."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2015"
    ms.author="robb"/>

# Amélioration de la disponibilité à l’aide des paires régionales Azure

## Explication sur les régions jumelées Azure

Azure fonctionne dans plusieurs zones géographiques à travers le monde. Une zone géographique Azure est une zone définie du monde contenant au moins une région Azure. Une région Azure est une zone géographique qui contient un ou plusieurs centres de données.

Chaque région Azure est jumelée avec une autre région au sein de la même zone géographique (à l’exception du Sud du Brésil qui est jumelé avec une région située en dehors de sa zone géographique). Ces deux régions forment ainsi une paire régionale.


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figure 1 – Diagramme de paire régionale Azure



| Geography | Régions jumelées | |
| :-------------| :-------------   | :-------------   |
| Amérique du Nord | États-Unis - partie centrale septentrionale | États-Unis - partie centrale méridionale |
| Amérique du Nord | Est des États-Unis | Ouest des États-Unis |
| Amérique du Nord | Est des États-Unis 2 | Centre des États-Unis |
| Europe | Europe du Nord | Europe de l'Ouest |
| Asie | Asie du Sud-Est | Est de l'Asie |
| Chine | Chine orientale | Chine du Nord |
| Japon | Est du Japon | Ouest du Japon |
| Brésil | Sud du Brésil (1) | États-Unis - partie centrale méridionale |
| Australie | Est de l’Australie | Sud-est de l’Australie|
| Gouvernement américain | Gouvernement américain - Iowa | Gouvernement américain - Virginie |

Tableau 1 - Mise en correspondance des paires régionales Azure

> (1) La région Sud du Brésil est unique, car elle est jumelée avec une région située en dehors de sa propre zone géographique. Notez que la région secondaire de la région Sud du Brésil est Sud-Centre des États-Unis mais que la région secondaire de la région Sud-Centre des États-Unis n’est pas Sud du Brésil.

Nous vous recommandons de répliquer les charges de travail sur les paires régionales pour tirer parti des stratégies d’isolation et de disponibilité d’Azure. Par exemple, les mises à jour planifiées du système Azure sont déployées séquentiellement (pas en même temps) entre les régions jumelées. Cela signifie que même dans les rares cas de mise à jour défectueuse, les deux régions ne sont pas affectées simultanément. En outre, dans l’éventualité d’une défaillance générale, la récupération d’au moins une région de chaque paire est prioritaire.

## Exemple de paire régionale
La Figure 2 ci-dessous montre une application hypothétique qui utilise les régions jumelées pour la récupération d’urgence. Les numéros verts mettent en surbrillance les activités entre les régions de trois services Azure (de stockage, de calcul et de base de données) et comment ils sont configurés pour répliquer les régions. Les avantages uniques du déploiement entre les régions jumelées sont mis en surbrillance par les nombres en orange.


![Vue d’ensemble des avantages des région jumelées](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figure 2 – Paire régionale Azure hypothétique

## Activités entre régions
Conformément à la figure 2.

![1Vert](./media/best-practices-availability-paired-regions/1Green.png) **Calcul Windows Azure (PaaS)** – Vous devez approvisionner des ressources de calcul supplémentaires à l’avance pour garantir la disponibilité des ressources dans d’autres régions au cours d’un incident. Pour plus d’informations, consultez [Guide technique Azure Business Continuity](https://msdn.microsoft.com/library/azure/hh873027.aspx)

![2Vert](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** - Le stockage géo-redondant (GRS, Geo-Redundant Storage) est configuré par défaut quand vous créez un compte de stockage Azure. Avec GRS, vos données sont répliquées trois fois dans la région principale et trois fois dans la région jumelée. Pour plus d'informations, consultez [Options de redondance du stockage Azure](../storage/storage-redundancy.md).


![3Vert](./media/best-practices-availability-paired-regions/3Green.png)**Bases de données SQL ** – avec Azure SQL géo-réplication Standard, vous pouvez configurer la réplication asynchrone des transactions vers une région jumelée. Avec la géo-réplication Premium, vous pouvez configurer la réplication pour n’importe quelle région du monde ; toutefois, nous vous recommandons de déployer ces ressources dans une région jumelée pour la récupération d’urgence. Pour plus d’informations, consultez la rubrique [Géo-réplication dans la base de données SQL Azure](https://msdn.microsoft.com/library/azure/dn783447.aspx).

![4Vert](./media/best-practices-availability-paired-regions/4Green.png)**Azure Resource Manager (ARM)** - ARM offre par nature une isolation logique des composants de gestion de service entre les régions. Cela signifie que des échecs logiques dans une région sont moins susceptibles d’avoir un impact sur une autre.

## Avantages d’une région jumelée
Conformément à la figure 2.

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png) **Isolation physique** – Quand cela est possible, Azure préfère une séparation de 483 kilomètres (300 miles) au moins entre les centres de données d’une paire régionale, même si ce n’est pas pratique, voire impossible dans toutes les régions géographiques. La séparation physique du centre de données réduit la probabilité de catastrophes naturelles, de troubles civils, de coupures de courant ou de pannes de réseau physique affectant les deux régions en même temps. L’isolation est soumise aux contraintes géographiques (étendue, disponibilité de l’infrastructure réseau et de l’alimentation, réglementations, etc.).

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)**Réplication fournie par plate-forme** - Certains services de stockage géo-redondant fournissent la réplication automatique à la région jumelée.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png) **Ordre de récupération de la région** – En cas de panne étendue, la récupération d’une région est hiérarchisée pour chaque paire. Les applications déployées dans des régions jumelées ont la garantie que l’une des régions est récupérée en priorité. Si une application est déployée dans des régions qui ne sont pas jumelées, la récupération peut être retardée. Dans le pire des cas, les régions choisies peuvent être les deux dernières à être récupérées.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png) **Mises à jour séquentielles** – Les mises à jour planifiées du système Azure sont déployées vers les régions jumelées séquentiellement (pas en même temps) pour limiter les interruptions de service, l’effet des bogues et les échecs logiques dans les rares cas d’échec de mise à jour.


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png) **Résidence de données** – Une région se trouve dans la même zone géographique que la région avec laquelle elle est jumelée (à l’exception du Sud du Brésil) pour répondre aux exigences de la résidence de données en termes d’impôts et d’application de la loi.

<!---HONumber=Oct15_HO3-->