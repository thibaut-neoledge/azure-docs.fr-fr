<properties
   pageTitle="Azure Backup - Forum aux questions | Microsoft Azure"
   description="Réponses aux questions fréquemment posées sur le service de sauvegarde, l’agent de sauvegarde, la sauvegarde et la rétention, la récupération, la sécurité et d’autres questions courantes sur la récupération d’urgence et la sauvegarde."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="sauvegarde et récupération d’urgence ; service de sauvegarde"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="08/29/2016"
	 ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Service Azure Backup – Forum aux questions

> [AZURE.SELECTOR]
- [FAQ Azure Backup pour le mode classique](backup-azure-backup-faq.md)
- [FAQ Azure Backup pour le mode Resource Manager](backup-azure-backup-ibiza-faq.md)

Cet article est constitué d’une liste de questions fréquemment posées (et des réponses respectives) sur le service Azure Backup. Notre communauté répond rapidement, et si une question est souvent posée, nous l’ajouterons à cet article. Généralement, les réponses aux questions fournissent des informations de référence ou de prise en charge. Vous pouvez poser des questions sur Azure Backup dans la section Disques de cet article ou d’un article associé. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).


## Quelle est la liste des systèmes d’exploitation pris en charge, sur lesquels je peux sauvegarder des fichiers dans Azure à l’aide d’Azure Backup ? <br/>
Azure Backup prend en charge les systèmes d’exploitation suivants pour la sauvegarde du dossier de fichiers et la sauvegarde de l’application à l’aide d’Azure Backup Server et de SCDPM.

| Système d’exploitation | Plateforme | SKU |
| :------------- |-------------| :-----|
| Windows 8 et derniers Service Packs | 64 bits | Entreprise, Professionnel |
| Windows 7 et derniers Service Packs | 64 bits | Édition Intégrale, Entreprise, Professionnel, Édition Familiale Premium, Édition Familiale Basique, Édition Starter |
| Windows 8.1 et derniers Service Packs | 64 bits | Entreprise, Professionnel |
| Windows 10 | 64 bits | Entreprise, Professionnel, Familiale |
|Windows Server 2012 R2 et derniers Service Packs|	64 bits|	Standard, Datacenter, Foundation|
|Windows Server 2012 et derniers Service Packs|	64 bits|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 et derniers Service Packs |64 bits|	Standard, Workgroup|
|Windows Storage Server 2012 et derniers Service Packs |64 bits |Standard, Workgroup
|Windows Server 2012 R2 et derniers Service Packs |64 bits|	Essential|
|Windows Server 2008 R2 SP1 |64 bits|	Standard, Entreprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bits|	Standard, Entreprise, Datacenter, Foundation|

Pour la sauvegarde de machines virtuelles Azure,

- **Linux** : Azure Backup prend en charge [une liste de distributions approuvées par Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md), à l’exception de CoreOS Linux. D’autres distributions « Bring-Your-Own-Linux » fonctionnent également tant que l’agent de machine virtuelle est disponible sur la machine virtuelle et que Python est pris en charge.
- **Windows Server** : les versions antérieures à Windows Server 2008 R2 ne sont pas prises en charge.

## Où puis-je télécharger le dernier agent Azure Backup ? <br/>
Vous pouvez télécharger le dernier agent de sauvegarde de Windows Server, de System Center DPM ou du client Windows, en cliquant [ici](http://aka.ms/azurebackup_agent). Si vous souhaitez sauvegarder une machine virtuelle, utilisez l’Agent de machine virtuelle (qui installe automatiquement l’extension appropriée). L’agent de machine virtuelle est déjà présent dans les machines virtuelles créées à partir de la galerie Azure.

## Quelle version du serveur SCDPM est prise en charge ? <br/>
Nous vous recommandons d’installer le [dernier](http://aka.ms/azurebackup_agent) agent Azure Backup sur le dernier correctif cumulatif de SCDPM (UR11 d’août 2016).

## Pendant la configuration de l’agent Azure Backup, je suis invité à entrer des informations d’identification de coffre. Les informations d’identification de coffre expirent-elles ?
Oui, les informations d’identification de coffre expirent au bout de 48 heures. Si le fichier expire, connectez-vous au portail Azure et téléchargez les fichiers d’informations d’identification de votre coffre.

## Le nombre de coffres pouvant être créés dans chaque abonnement Azure est-il limité ? <br/>
Oui. Depuis août 2016, vous pouvez créer 25 coffres (archivage de sauvegarde et archivage Recovery Service) par abonnement. Au-delà, vous devez créer un autre abonnement.

## Le nombre de serveurs/ordinateurs pouvant être inscrits dans chaque coffre est-il limité ? <br/>
Oui, vous pouvez inscrire un maximum de 50 ordinateurs par archivage. Pour les machines virtuelles Azure IaaS, la limite est de 200 machines virtuelles par coffre. Si vous avez besoin d’inscrire davantage d’ordinateurs, créez un autre archivage.

## Comment inscrire mon serveur dans un autre centre de données ?<br/>
Les données de sauvegarde sont envoyées au centre de données du coffre dans lequel il est inscrit. Le moyen le plus simple pour changer de centre de données consiste à désinstaller/réinstaller l’agent et à demander un nouveau coffre appartenant au centre de données choisi.

## Que se passe-t-il si je renomme un serveur Windows qui sauvegarde des données dans Azure ?<br/>
Lorsque vous renommez un serveur, toutes les sauvegardes actuellement configurées sont arrêtées. Vous devez enregistrer le nouveau nom du serveur avec le coffre de sauvegarde. Lorsque vous créez un nouvel enregistrement, la première opération de sauvegarde est une sauvegarde complète et non une sauvegarde incrémentielle. Si vous devez récupérer des données précédemment sauvegardées dans le coffre avec le nom de l’ancien serveur, vous pouvez utiliser l’option [**Un autre serveur**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) de l’assistant **Récupérer des données**.

## À partir de quels types de lecteurs puis-je sauvegarder des fichiers et des dossiers ? <br/>
La sauvegarde des lecteurs/volumes n’est pas prise en charge :

- Support amovible : le lecteur doit être déclaré fixe pour être utilisé comme source d’éléments de sauvegarde.
- Volumes en lecture seule : le volume doit être accessible en écriture pour que le service VSS puisse fonctionner.
- Volumes déconnectés : le volume doit être en ligne pour que le service VSS puisse fonctionner.
- Partage réseau : le volume doit être local sur le serveur à sauvegarder à l’aide de la sauvegarde en ligne.
- Volumes protégés par BitLocker : le volume doit être déverrouillé pour pouvoir effectuer la sauvegarde.
- Identification du système de fichiers : NTFS est le seul système de fichiers pris en charge pour cette version du service de sauvegarde en ligne.

## Quels types de fichier et dossier puis-je sauvegarder à partir de mon serveur ?<br/>
Les types suivants sont pris en charge :

- Chiffré
- Compressé
- Partiellement alloué
- Compressé + partiellement alloué
- Liens physiques : non pris en charge, ignorés
- Point d’analyse : non pris en charge, ignoré
- Chiffré + compressé : non pris en charge, ignoré
- Chiffré + partiellement alloué : non pris en charge, ignoré
- Flux compressé : non pris en charge, ignoré
- Flux partiellement alloué : non pris en charge, ignoré

## Quelle est la taille minimale requise du dossier du cache ? <br/>
La taille du dossier du cache détermine la quantité de données que vous sauvegardez. Le dossier cache doit représenter 5 % de l’espace requis pour le stockage de données.

## Si mon organisation possède un coffre, comment isoler les données d’un serveur de celles d’un autre serveur lors de la restauration des données ?<br/>
Tous les serveurs inscrits dans le même coffre sont en mesure de récupérer les données sauvegardées par d’autres serveurs *qui utilisent la même phrase secrète*. Si vous avez des serveurs dont vous souhaitez isoler les données de sauvegarde des autres serveurs de votre organisation, utilisez une phrase secrète désignée pour ces serveurs. Par exemple, les serveurs des ressources humaines peuvent utiliser une phrase secrète de chiffrement, les serveurs de comptabilité peuvent en utiliser une autre et les serveurs de stockage une troisième.

## Puis-je « migrer » mon coffre ou mes données de sauvegarde entre des abonnements ?<br/>
Non. Le coffre est créé au niveau de l’abonnement et ne peut pas être ensuite réaffecté à un autre abonnement.

## L’agent Azure Backup fonctionne-t-il sur un serveur qui utilise la déduplication Windows Server 2012 ?<br/>
Oui. Le service de l’agent convertit les données dédupliquées en données normales lorsqu'il prépare l'opération de sauvegarde. Il optimise ensuite les données pour la sauvegarde, chiffre les données, puis envoie les données chiffrées au service de sauvegarde en ligne.

## Si j’annule une opération de sauvegarde en cours, les données de sauvegarde transférées sont-elles supprimées ?<br/>
Non. Le coffre de sauvegarde stocke les données sauvegardées qui ont été transférées jusqu’au moment de l’annulation. Azure Backup utilise un mécanisme de point de contrôle pour ajouter occasionnellement des points de contrôle aux données de sauvegarde pendant la sauvegarde. En présence de points de contrôle dans les données de sauvegarde le processus de sauvegarde suivant est en mesure de valider l’intégrité des fichiers. La sauvegarde suivante est déclenchée de manière incrémentielle sur les données qui avaient été sauvegardées précédemment. Une sauvegarde incrémentielle optimise l’utilisation de la bande passante, ce qui vous évite d’avoir à transférer les mêmes données plusieurs fois.

Lorsqu’une sauvegarde de machine virtuelle Azure est annulée, les données transférées sont ignorées et une nouvelle sauvegarde transfère les données incrémentielles de la précédente tâche de sauvegarde ayant abouti.

## Pourquoi l’avertissement « Les sauvegardes Azure n’ont pas été configurées pour ce serveur » apparaît-il alors que j’avais planifié des sauvegardes standard ? <br/>
Cet avertissement est généré lorsque les paramètres de planification de la sauvegarde stockés sur le serveur local diffèrent des paramètres stockés dans le coffre de sauvegarde. Lorsque le serveur ou les paramètres ont été restaurés à un état correct connu, les planifications de sauvegarde peuvent se désynchroniser. Si vous recevez cet avertissement, [reconfigurez la stratégie de sauvegarde](backup-azure-manage-windows-server.md), puis sélectionnez **Exécuter la sauvegarde maintenant** pour resynchroniser le serveur local avec Azure.

## Quelles sont les règles de pare-feu à configurer pour Azure Backup ?<br/>
Pour avoir une protection transparente des données sur site vers Azure et la charge de travail vers Azure, il est recommandé que vous autorisiez votre pare-feu à communiquer avec les URL suivantes :

- www.msftncsi.com
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.microsoftonline.com
- *.windows.net

##Puis-je installer l’agent Azure Backup sur une machine virtuelle Azure déjà sauvegardée par le service Azure Backup en utilisant l’extension de machine virtuelle ?<br/>
Absolument. Azure Backup fournit une sauvegarde au niveau de la machine virtuelle pour les machines virtuelles Azure à l’aide de l’extension de machine virtuelle. Vous pouvez installer l’agent Azure Backup sur le système d’exploitation Windows invité et protéger des fichiers et des dossiers sur ce système invité.

## Puis-je installer l’agent Azure Backup sur une machine virtuelle Azure pour sauvegarder des fichiers et des dossiers situés sur le stockage temporaire fourni par la machine virtuelle Azure ?<br/>
Vous pouvez installer l’agent Azure Backup sur le système d’exploitation Windows invité et sauvegarder des fichiers et des dossiers sur le stockage temporaire. Toutefois, notez que les sauvegardes échoueront une fois que les données de stockage temporaire seront effacées. En outre, si les données de stockage temporaire ont été supprimées, vous pouvez uniquement restaurer les stockages non volatiles.

## J’ai installé l’agent Azure Backup pour protéger mes fichiers et dossiers. Puis-je à présent installer SCDPM pour utiliser l’agent Azure Backup afin de protéger les charges de travail d’application/de machine virtuelle locales dans Azure ?<br/>
Pour utiliser Azure Backup avec SCDPM, il est recommandé d’installer d’abord SCDPM, puis l’agent Azure Backup. Cela garantit une intégration transparente de l’agent Azure Backup avec SCDPM et permet de protéger des fichiers, des dossiers, des charges de travail d’application et des machines virtuelles dans Azure, directement à partir de la console de gestion de SCDPM. Pour les raisons invoquées ci-avant, l’installation de SCDPM après l’agent Azure Backup n’est ni recommandée, ni prise en charge.

## Quelle est la longueur du chemin d’accès pouvant être spécifiée dans le cadre de la stratégie Azure Backup avec l’agent Azure Backup ? <br/>  
L’agent Azure Backup utilise le format NTFS. La [spécification de longueur de chemin d’accès est limitée par les API Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). En cas de sauvegarde de fichiers dont la longueur de chemin d’accès du fichier est supérieure à celui qui est spécifié par l’API Windows, les clients peuvent choisir de sauvegarder le dossier parent ou le lecteur de disque des fichiers de sauvegarde.

## Quels sont les caractères autorisés dans le chemin d’accès du fichier de stratégie Azure Backup à l’aide de l’agent Azure Backup ? <br>  
 L’agent Azure Backup utilise le format NTFS. Elle active les [caractères NTFS pris en charge](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) dans le cadre de la spécification de fichier.

## Puis-je utiliser Azure Backup Server pour créer une sauvegarde de la récupération complète (BMR) pour un serveur physique ? <br/>
Oui.

## Puis-je configurer le service Backup pour qu’il envoie un courrier électronique si une opération de sauvegarde échoue ? <br/>
Oui, le service Backup dispose de plusieurs alertes basées sur l’événement qui peuvent être utilisés avec un script PowerShell. Pour obtenir une description complète, consultez [Notifications d’alerte](backup-azure-manage-vms.md#alert-notifications)

## La taille de chaque source de données sauvegardée est-elle limitée ? <br/>
Au niveau du coffre, il n’existe aucune limite sur la quantité de données que vous pouvez sauvegarder. Azure Backup impose une restriction (pour des raisons pratiques, ces limites sont très élevées) sur la taille maximale de la source de données. Depuis août 2015, la taille maximale de la source de données du système d’exploitation pris en charge est :

|N° |	Système d’exploitation |	Taille maximale de la source de données |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 ou version ultérieure| 54 400 Go|
|2| Windows 8 ou version ultérieure| 54 400 Go|
|3| Windows Server 2008, Windows Server 2008 R2 | 1 700 Go|
|4| Windows 7 | 1 700 Go|

Le tableau suivant explique comment la taille de chaque source de données est déterminée.

|	Source de données |	Détails |
| :-------------: |:-------------|
|Volume |Quantité de données sauvegardées à partir d’un volume unique ou d’une machine client|
|Machine virtuelle Hyper-V | Somme des données de tous les disques durs virtuels de la machine virtuelle en cours de sauvegarde|
|Base de données Microsoft SQL Server | Taille d’une base de données SQL unique en cours de sauvegarde |
|Microsoft SharePoint |Somme des bases de données de contenu et de configuration dans une batterie de serveurs SharePoint en cours de sauvegarde|
|Microsoft Exchange |Somme de toutes les bases de données Exchange sur un serveur Exchange en cours de sauvegarde|
|État système/récupération complète |Chaque copie individuelle de l’état système/récupération complète de l’ordinateur en cours de sauvegarde|

## Existe-t-il des limites au nombre de fois qu’une tâche de sauvegarde peut être planifiée chaque jour ?<br/>
Oui, vous pouvez exécuter les tâches de sauvegarde sur Windows Server ou un client Windows jusqu’à trois fois/jour. Vous pouvez exécuter des tâches de sauvegarde sur System Center DPM jusqu’à deux fois par jour. Vous pouvez exécuter une tâche de sauvegarde pour les machines virtuelles IaaS une fois par jour.

## Existe-t-il une différence entre la stratégie de planification de DPM et celle de Windows Server (par exemple, sous Windows Server sans DPM) ? <br/>
Oui. Avec DPM, vous pouvez spécifier des planifications quotidiennes, hebdomadaires, mensuelles et annuelles. Windows Server (sans DPM) vous permet de spécifier uniquement les planifications quotidiennes et hebdomadaires.

## Existe-t-il une différence entre les stratégies de rétention de DPM et celle d’un serveur/client Windows (par exemple, sur Windows Server sans DPM) ?<br/>
Non, DPM et le serveur/client Windows Server répondent tous les deux à des stratégies quotidienne, hebdomadaire, mensuelle et annuelle de rétention.

## Puis-je configurer mes stratégies de rétention de manière sélective (par exemple, configurer des stratégies hebdomadaires et quotidiennes, mais pas annuelles et mensuelles) ?<br/>
Oui, la structure de rétention Azure Backup vous permet une flexibilité complète dans la définition de la stratégie de rétention selon vos besoins.

## Puis-je « planifier une sauvegarde » à 18 h 00 et spécifier des « stratégies de rétention » à une autre heure ?<br/>
Non. Les stratégies de rétention ne peuvent être appliquées que sur les points de sauvegarde. Dans l’image qui suit, la stratégie de rétention est spécifiée sur les sauvegardes effectuées à minuit et à 18 h 00. <br/>

![Planification de sauvegarde et rétention](./media/backup-azure-backup-faq/Schedule.png) <br/>

## Une copie incrémentielle est-elle transférée pour les stratégies de rétention planifiées ? <br/>
Non, la copie incrémentielle est envoyée en fonction de l’heure mentionnée dans la page de planification de sauvegarde. Les points qui peuvent être conservés sont déterminés par la stratégie de rétention.

## Si une sauvegarde est conservée pendant longtemps, faut-il plus de temps pour récupérer un point de données plus ancien ? <br/>
 Non, le délai de récupération reste identique pour les points de données récents ou anciens. Chaque point de récupération se comporte comme un point complet.

## Si chaque point de récupération est un point complet, cela a-t-il un impact sur la quantité totale de stockage de sauvegarde facturable ?<br/>
Les produits avec points de rétention à long terme stockent les données de sauvegarde en tant que points complets. Toutefois, même si ces points *occupent* de l’espace de stockage, ils sont plus faciles et plus rapides à récupérer. Les copies incrémentielles *occupent moins d’espace de stockage*, mais vous devez restaurer une chaîne de données, ce qui rallonge le temps de récupération. L’architecture de stockage d’Azure Backup vous offre le meilleur des deux en stockant les données de manière optimale pour des restaurations rapides et des coûts de stockage faibles. Cette approche de stockage de données garantit que votre bande passante entrante et sortante est utilisée de façon efficace. La quantité de stockage de données et le temps nécessaire pour récupérer les données sont tous les deux réduits au minimum. En savoir plus sur l’efficacité des [sauvegardes incrémentielles](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

## Le nombre de points de récupération pouvant être créés est-il limité ?<br/>
Non. Nous avons éliminé les limites sur les points de récupération. Vous pouvez créer autant de points de récupération que vous le souhaitez.

## Pourquoi la quantité de données transférées dans la sauvegarde est-elle différente de la quantité de données que j’ai sauvegardée ?<br/>
 Toutes les données sauvegardées à partir de l’agent Azure Backup, de SCDPM ou d’Azure Backup Server sont compressées et chiffrées avant d’être transférées. Une fois la compression et le chiffrement appliqués, les données dans le coffre de sauvegarde sont inférieures de 30 à 40 %.

## Existe-t-il un moyen d’adapter la quantité de bande passante utilisée par le service Backup ?<br/>
 Oui, utilisez l’option **Modifier les propriétés** de l’agent Backup pour régler la bande passante. Ajuste la quantité de bande passante et les heures d’utilisation de cette bande passante. Consultez la page [Limitation de bande passante](../backup-configure-vault.md#enable-network-throttling) pour en savoir plus.

## Ma bande passante Internet est limitée par rapport à la quantité de données que j’ai besoin de sauvegarder. Est-il possible de déplacer des données vers un emplacement spécifique avec un grand canal réseau et de transmettre ces données à Azure ?<br/>
Vous pouvez sauvegarder des données dans Azure via le processus de sauvegarde en ligne standard, ou vous pouvez utiliser le service Azure Import/Export pour transférer des données vers le stockage d’objets blob Azure. Il n’existe aucune autre façon de transférer les données de sauvegarde dans le stockage Azure. Pour plus d’informations sur l’utilisation du service Azure Import/Export avec Azure Backup, consultez l’article [Flux de travail de la sauvegarde hors connexion](backup-azure-backup-import-export.md).

## Combien de récupérations puis-je effectuer sur les données sauvegardées dans Azure ?<br/>
Il n’existe aucune limite concernant le nombre de récupérations dans Azure Backup.

## Le trafic sortant du centre de données Azure m’est-il facturé pendant les récupérations ?<br/>
 Non. Vos récupérations sont gratuites et le trafic sortant ne vous est pas facturé.

## Les données envoyées à Azure sont-elles chiffrées ? <br/>
Oui. Les données sont chiffrées sur l’ordinateur client/serveur/SCDPM local avec AES256 et sont envoyées via une connexion HTTPS sécurisée.

## Les données de sauvegarde sont-elles également chiffrées dans Azure ?<br/>
 Oui. Les données envoyées à Azure restent chiffrées (au repos). Microsoft ne déchiffre les données de sauvegarde à aucun moment. Lors de la sauvegarde, Azure Backup s’appuie sur le chiffrement de la machine virtuelle. Par exemple, si votre machine virtuelle est chiffrée à l’aide d’Azure Disk Encryption ou d’une autre technologie de chiffrement, Azure Backup utilise ce chiffrement pour sécuriser vos données.

## Quelle est la longueur minimale de la clé de chiffrement utilisée pour chiffrer les données de sauvegarde ? <br/>
 La clé de chiffrement doit contenir au moins 16 caractères.

## Que se passe-t-il si j’ai égaré la clé de chiffrement ? Puis-je récupérer des données (ou) Microsoft peut-il récupérer les données ? <br/>
La clé utilisée pour chiffrer les données de sauvegarde est présente uniquement localement chez le client. Microsoft ne conserve pas de copie dans Azure et n’a pas accès à la clé. Si le client égare la clé, Microsoft ne peut pas récupérer les données de sauvegarde.

## Comment puis-je modifier l’emplacement du cache spécifié pour l’agent Azure Backup ?<br/>
 Parcourez dans l’ordre la liste ci-dessous pour modifier l’emplacement du cache.
- Arrêtez le moteur Backup en exécutant la commande qui suit dans une invite de commandes avec élévation de privilèges :

  ```PS C:\> Net stop obengine```

- Ne déplacez pas les fichiers, mais copiez le dossier d’espace de cache dans un autre lecteur disposant d’un espace suffisant. L’espace de cache d’origine peut être supprimé après avoir confirmé que les sauvegardes fonctionnent avec le nouvel espace de cache.

- Mettez à jour les entrées de registre suivantes en utilisant le chemin d’accès au nouveau dossier d’espace de cache.<br/>

|Chemin d’accès au Registre | Clé de Registre | Valeur |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Emplacement du nouveau dossier de cache* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Emplacement du nouveau dossier de cache* |

- Démarrez le moteur Backup en exécutant la commande suivante dans une invite de commandes avec élévation de privilèges :

  ```PS C:\> Net start obengine```

  Une fois les sauvegardes correctement effectuées avec le nouvel emplacement de cache, vous pouvez supprimer le dossier de cache d’origine.

## Où dois-je placer le dossier du cache de l’agent Azure Backup pour que ce dernier fonctionne comme prévu ?<br/>
Les emplacements suivants pour le dossier du cache ne sont pas recommandés :

- Partage réseau ou un média amovible : le dossier du cache doit être local sur le serveur nécessitant une sauvegarde avec la sauvegarde en ligne. Les emplacements réseau ou les médias amovibles (tels que les lecteurs USB) ne sont pas pris en charge.
- Volumes hors connexion : le dossier du cache doit être en ligne pour la sauvegarde attendue avec l’agent Azure Backup.

## Existe-t-il des attributs du dossier du cache qui ne sont pas pris en charge ?<br/>
 Les attributs suivants ou leurs combinaisons ne sont pas pris en charge pour le dossier du cache :

- Chiffré
- Dédupliqué
- Compressé
- Partiellement alloué
- Point d’analyse

Le dossier du cache et les métadonnées de disque dur virtuel ne doivent pas avoir les attributs ci-dessus pour que l’agent Azure Backup fonctionne correctement.

<!---HONumber=AcomDC_0831_2016-->