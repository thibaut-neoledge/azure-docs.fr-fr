<properties
	pageTitle="Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure | Microsoft Azure"
	description="Considérations importantes avant la sauvegarde de machines virtuelles dans Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="sauvegarde de machines virtuelles"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="trinadhk; jimpark; markgal;"/>

# Planification de votre infrastructure de sauvegarde de machines virtuelles dans Azure
Cet article aborde les éléments clés à garder à l’esprit lorsque vous prévoyez de sauvegarder des machines virtuelles dans Azure. Si vous avez [préparé votre environnement](backup-azure-vms-prepare.md), il s’agit de l’étape à suivre avant de passer à la [sauvegarde des machines virtuelles](backup-azure-vms.md). Si vous avez besoin d’informations sur les machines virtuelles Azure, consultez la [Documentation sur les machines virtuelles](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Comment Azure sauvegarde-t-il des machines virtuelles Azure ?
Lorsque le service Azure Backup lance le travail de sauvegarde à l’heure planifiée, il déclenche l’extension de sauvegarde pour prendre un instantané à un moment donné. Cet instantané est pris de façon coordonnée avec le service VSS (Service de copie shadow de volume) pour obtenir un instantané cohérent des disques sur la machine virtuelle sans avoir à arrêter cette dernière.

Une fois l’instantané réalisé, les données sont transférées par le service Azure Backup dans un coffre de sauvegarde. Pour rendre le processus de sauvegarde plus efficace, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente.

![Architecture de la sauvegarde des machines virtuelles Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Une fois le transfert de données terminé, l’instantané est supprimé et un point de récupération est créé.

### Cohérence des données
La sauvegarde et la restauration des données critiques d’entreprise sont compliquées par le fait qu’elles doivent être sauvegardées alors que les applications qui génèrent les données sont en cours d’exécution. Pour résoudre ce problème, Azure Backup fournit des sauvegardes cohérentes avec les charges de travail de Microsoft en utilisant le service VSS pour vous assurer que les données sont correctement inscrites dans le compte de stockage.

>[AZURE.NOTE] Pour les machines virtuelles Linux, seule les sauvegardes fichiers compatibles sont possibles, car l’équivalent de la plateforme VSS n’existe pas sous Linux.

Azure Backup effectue la sauvegarde complète VSS sur les machines virtuelles Windows (en savoir plus sur la [sauvegarde complète VSS](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Pour activer les sauvegardes de copie VSS, la clé de registre ci-dessous doit être définie sur la machine virtuelle.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


Ce tableau décrit les types de compatibilité et les conditions dans lesquelles elles se produisent pendant les procédures de sauvegarde et de restauration de machine virtuelle Azure.

| Cohérence | En fonction du service VSS | Explication et détails |
|-------------|-----------|---------|
| Cohérence des applications | Oui | Il s’agit du type de cohérence idéale pour les charges de travail Microsoft, car il apporte les garanties suivantes :<ol><li> la machine virtuelle *démarre*. <li>Les données *ne sont pas endommagées*. <li>Il n’y a *aucune perte de données*.<li> Les données sont cohérentes vis-à-vis de l’application qui les utilise grâce à la sollicitation de l’application au moment de la sauvegarde (à l’aide de VSS).</ol> La plupart des charges de travail de Microsoft ont des enregistreurs VSS qui effectuent des actions de charges de travail spécifiques relatives à la cohérence des données. Par exemple, Microsoft SQL Server dispose d’un enregistreur VSS qui garantit que les écritures dans le journal des transactions et de la base de données sont effectuées correctement.<br><br> Pour les sauvegardes d’une machine virtuelle Azure, l’obtention d’un point de récupération cohérent signifie que l’extension de sauvegarde a pu appeler le flux de travail VSS et se terminer *correctement* avant la prise de l’instantané de la machine virtuelle. Bien entendu, cela signifie que les enregistreurs VSS de toutes les applications dans la machine virtuelle Azure ont été également appelés.<br><br>(Découvrez les[principes de base du service VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), puis approfondissez vos connaissances sur [son fonctionnement](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). |
| Cohérence du système de fichiers | Oui : pour les ordinateurs Windows | Il existe deux scénarios où le point de récupération peut être *cohérent avec le système de fichiers* :<ul><li>En cas de sauvegardes de machines virtuelles Linux dans Azure, Linux n’ayant pas une plateforme équivalente à VSS.<li>En cas d’échec du service VSS lors de la sauvegarde de machines virtuelles Windows dans Azure.</li></ul> Dans les deux cas, la meilleure solution consiste à s’assurer que les conditions suivantes sont satisfaites : <ol><li> la machine virtuelle *démarre*. <li>Les données * ne sont pas endommagées*.<li>Il n’y a pas de *perte de données*.</ol> Les applications doivent implémenter leur propre mécanisme de « correctif » sur les données restaurées.|
| Cohérence en cas d’incident | Non | Cette situation est la même que lorsqu’une machine virtuelle rencontre un « incident » (via une réinitialisation matérielle ou logicielle). Cela se produit généralement lorsque la machine virtuelle Azure est arrêtée au moment de la sauvegarde. Pour les sauvegardes d’une machine virtuelle Azure, l’obtention d’un point de récupération cohérent suite à un incident signifie qu’Azure Backup ne fournit aucune garantie de cohérence des données sur le support de stockage, que ce soit au niveau du système d’exploitation ou de l’application. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées. <br/> <br/> Même s’il n’existe aucune garantie, dans la plupart des cas, le système d’exploitation démarre. Ce démarrage est généralement suivi d’une procédure de vérification du disque comme chkdsk permettant de résoudre les erreurs d’endommagement. Les données ou les écritures en mémoire qui n’ont pas été complètement transférées sur le disque seront perdues. Si une restauration de données est nécessaire, l’application suit généralement son propre mécanisme de vérification. <br><br>Par exemple, cette situation peut survenir si le journal des transactions comporte des entrées qui n’existent pas dans la base de données. Le logiciel de base de données effectue alors une restauration jusqu’à ce que les données soient cohérentes. Lorsque des données sont réparties sur plusieurs disques virtuels (comme des volumes fractionnés), un point de récupération cohérent après incident ne fournit aucune garantie quant à l’exactitude des données.|


## Performance et utilisation des ressources
Au même titre qu’un logiciel de sauvegarde déployé sur site, l’utilisation de capacité et des ressources des machines virtuelles dans Azure doit être planifiée. Les [limites d’Azure Storage](azure-subscription-service-limits.md#storage-limits) définissent la structuration des déploiements de machine virtuelle pour obtenir des performances maximales avec un impact minimal sur l’exécution des charges de travail.

Il existe deux limites de stockage Azure principales sauvegarde ayant un impact sur les performances de sauvegarde :

- Nombre maximal de sorties par compte de stockage
- Taux de requête total par compte de stockage

### Limites de compte de stockage
Lorsque les données de sauvegarde sont copiées hors du compte de stockage du client, il est comptabilisé dans les mesures de l’IOPS (entrées/sorties par seconde) et des sorties (débit de stockage) du compte de stockage. Dans le même temps, les machines virtuelles exécutent et consomment des IOPS et débit. L’objectif est de vous assurer que le trafic général (sauvegarde et machine virtuelle) ne dépasse pas les limites du compte de stockage.

### Nombre de disques
La procédure de sauvegarde est gourmande en temps et consomme le maximum de ressources, car son objectif est d’achever sa sauvegarde dans les meilleurs délais. Cependant, toutes les opérations d’E/S sont limitées par le *débit cible d’un seul objet Blob*, qui est limité à 60 Mo par seconde. Pour accélérer le processus de sauvegarde, la sauvegarde de chaque disque de la machine virtuelle est tentée *en parallèle*. Donc, si une machine virtuelle est équipée de quatre disques, la sauvegarde Azure tente de sauvegarder les quatre disques simultanément. Ainsi, le facteur le plus important pour déterminer le trafic de sauvegarde sortant d’un compte de stockage client est le **nombre de disques** sauvegardés à partir du compte de stockage.

### Planification de sauvegarde
Autre facteur ayant un impact sur les performances : la **planification de sauvegarde**. Si vous configurez une sauvegarde simultanée de toutes les machines virtuelles, le nombre de disques sauvegardés *en parallèle* augmente, car Azure Backup tente de sauvegarder autant de disques que possible. Par conséquent, il est possible de réduire le trafic de sauvegarde depuis un compte de stockage en s’assurant que les différentes machines virtuelles sont sauvegardées à différents moments de la journée, sans chevauchement.

## Planification de la capacité
Le rassemblement de tous ces facteurs signifie que l’utilisation du compte de stockage doit être planifiée correctement. Téléchargez la [feuille de calcul Excel de planification des capacités des machines virtuelles](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) pour voir l’impact de vos choix en matière de planification de disques et de sauvegardes.

### Débit de sauvegarde
Pour chaque disque en cours de sauvegarde, Azure Backup lit les blocs sur le disque et stocke uniquement les données modifiées (sauvegarde incrémentielle). Ce tableau indique les valeurs de débit moyen que vous pouvez attendre de la part d’Azure Backup. Vous pouvez ainsi estimer le temps nécessaire à la sauvegarde d’un disque d’une taille donnée.

| Opération de sauvegarde | Meilleur débit |
| ---------------- | ---------- |
| Sauvegarde initiale | 160 Mbits/s |
| Sauvegarde incrémentielle (DR) | 640 Mbits/s <br><br> Ce débit peut baisser considérablement si les données du disque à sauvegarder sont très dispersées. |

### Durée de sauvegarde de machine virtuelle totale
La majeure partie du temps de sauvegarde est consacrée à la lecture et la copie des données, mais il existe d’autres opérations qui contribuent à la durée totale nécessaire à la sauvegarde d’une machine virtuelle :

- Délai nécessaire à l’[installation ou à la mise à jour de l’extension de sauvegarde](backup-azure-vms.md#offline-vms).
- Heure de l'instantané, qui est la durée nécessaire au déclenchement d’un instantané. Les instantanés sont déclenchés peu avant l’heure de sauvegarde planifiée.
- Délai d’attente de file d’attente. Comme le service de sauvegarde traite les sauvegardes de plusieurs clients, il se peut que la copie de données de sauvegarde de l’instantané vers l’archivage de sauvegarde Azure ne démarre pas immédiatement. Lors de pic de charge, les temps d'attente peuvent durer jusqu'à 8 heures en raison du nombre de sauvegardes à traiter. Toutefois, la durée de sauvegarde totale d’un ordinateur virtuel est inférieure à 24 heures pour des stratégies de sauvegarde quotidiennes.

## Meilleures pratiques
Nous vous suggérons de suivre les Meilleures pratiques lors de la configuration de sauvegardes de machines virtuelles

- Veuillez ne pas planifier la sauvegarde de plus de 4 machines virtuelles classiques à partir du même service cloud en même temps. Nous vous suggérons l’échelonnement des planifications de sauvegarde d’une heure si vous souhaitez configurer davantage de machines virtuelles à partir du même service cloud pour la sauvegarde. 
- Veuillez ne pas planifier la sauvegarde de plus de 40 machines virtuelles Resource Manager en même temps.
- Planifiez les sauvegardes pendant les heures creuses pour les machines virtuelles afin que le service de sauvegarde obtienne le nombre maximal d’opérations d’E/S par seconde pour transférer des données à partir du compte de stockage client vers le coffre de sauvegarde. 
- Veuillez vous assurer que, dans une stratégie, les machines virtuelles sont réparties entre différents comptes de stockage. Si le nombre total de disques stockés dans un seul compte de stockage à partir de machines virtuelles est supérieur à 20, nous vous suggérons de répartir les machines virtuelles entre des planifications de sauvegarde différentes pour obtenir le nombre d’E/S par seconde requis au cours de la phase de transfert de la sauvegarde.
- Il n’est pas recommandé de restaurer la machine virtuelle exécutée sur le stockage Premium vers le même compte de stockage car cette opération peut interférer avec l’opération de sauvegarde et réduire le nombre d’E/S par seconde disponibles pour la sauvegarde. 
- Nous vous recommandons de garder chaque machine virtuelle Premium sur un compte de stockage Premium différent pour obtenir de bonnes performances en matière de durée de sauvegarde. 

## Chiffrement des données

Azure Backup ne chiffre pas les données dans le cadre du processus de sauvegarde. Toutefois, vous pouvez chiffrer les données dans la machine virtuelle et sauvegarder les données protégées en toute transparence (pour en savoir plus, lire [sauvegarde des données chiffrées](backup-azure-vms-encryption.md)).


## Mode de calcul des instances protégées
Les machines virtuelles Azure sauvegardées par le biais d’Azure Backup sont soumises à la [Tarification d’Azure Backup](https://azure.microsoft.com/pricing/details/backup/). Le calcul des instances protégées est basé sur la taille *réelle* de la machine virtuelle, qui est la somme de toutes les données de la machine virtuelle, à l’exception du « disque de ressources ».

Vous n’êtes *pas* facturé en fonction de la taille maximale prise en charge pour chaque disque de données attaché à la machine virtuelle, mais en fonction des données réelles stockées sur le disque de données. De même, la facture du stockage de sauvegarde est basée sur la quantité de données stockées avec Azure Backup, qui est la somme des données réelles de chaque point de récupération.

Prenons l’exemple d’une machine virtuelle de taille standard A2 avec deux disques de données supplémentaires d’une taille maximale de 1 To. Le tableau ci-dessous indique les données réelles stockées sur chacun de ces disques :

|Type de disque|Taille maximale|Données réelles présentes|
|---------|--------|------|
| Disque de système d’exploitation | 1 023 Go | 17 Go |
| Disque local/Disque de ressources | 135 Go | 5 Go (non inclus pour la sauvegarde) |
| Disque de données 1 |	1 023 Go | 30 Go |
| Disque de données 2 | 1 023 Go | 0 Go |

La taille *réelle* de la machine virtuelle est dans ce cas 17 Go + 30 Go + 0 Go= 47 Go. Il s’agit de la taille d’instance protégée sur laquelle est basée la facture mensuelle. La taille de l’instance protégée utilisée pour la facturation augmente proportionnellement à la quantité de données issues de la machine virtuelle.

La facturation ne commence pas avant la fin de la première sauvegarde réussie. À partir de ce moment, la facturation du stockage et des instances protégées commence. La facturation continue tant que des *données de sauvegarde sont stockées avec Azure Backup* pour la machine virtuelle. L’opération Arrêter la protection n’arrête pas la facturation si les données de sauvegarde sont conservées.

La facturation pour une machine virtuelle spécifiée est interrompue uniquement si la protection est arrêtée *et* que les données de sauvegarde sont supprimées. Si aucun travail de sauvegarde n’est actif (et que la protection a été arrêtée), la taille de la machine virtuelle au moment de la dernière sauvegarde réussie devient la taille d’instance protégée sur laquelle est basée la facture mensuelle.

## Des questions ?
Si vous avez des questions ou si vous souhaitez que certaines fonctionnalités soient incluses, [envoyez-nous vos commentaires](http://aka.ms/azurebackup_feedback).

## Étapes suivantes

- [Sauvegarde de machines virtuelles](backup-azure-vms.md)
- [Gestion de la sauvegarde de machine virtuelle](backup-azure-manage-vms.md)
- [Restauration des machines virtuelles](backup-azure-restore-vms.md)
- [Résoudre les problèmes de sauvegarde de machines virtuelles](backup-azure-vms-troubleshoot.md)

<!---HONumber=AcomDC_0518_2016-->