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
	 ms.date="04/04/2016"
	 ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Service Azure Backup – Forum aux questions

> [AZURE.SELECTOR]
- [FAQ Azure Backup pour le mode classique](backup-azure-backup-faq.md)
- [FAQ Azure Backup pour le mode ARM](backup-azure-backup-ibiza-faq.md)

Cet article est constitué d’une liste de questions fréquemment posées (et des réponses respectives) sur le service Azure Backup. Notre communauté répond rapidement, et si une question est souvent posée, nous l’ajouterons à cet article. Généralement, les réponses aux questions fournissent des informations de référence ou de prise en charge. Vous pouvez poser des questions sur Azure Backup dans la section Disques de cet article ou d’un article associé. Vous pouvez également publier des questions sur le service Azure Backup dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Installation et configuration
**Q1. Quelle est la liste des systèmes d’exploitation pris en charge sur lesquels je peux sauvegarder des fichiers dans Azure à l’aide d’Azure Backup ?** <br/> R1. Azure Backup prend en charge les systèmes d’exploitation suivants :


| Système d'exploitation | Plateforme | SKU |
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

**Q2. Où puis-je télécharger le dernier agent Azure Backup ?** <br/> R2. Vous pouvez télécharger le dernier agent de sauvegarde de Windows Server, de System Center DPM ou du client Windows, en cliquant [ici](http://aka.ms/azurebackup_agent). Si vous souhaitez sauvegarder une machine virtuelle, utilisez l’Agent de machine virtuelle (qui installe automatiquement l’extension appropriée). L’agent de machine virtuelle est déjà présent dans les machines virtuelles créées à partir de la galerie Azure.

**Q3. Quelle version du serveur SCDPM est prise en charge ?** <br/> R3. Nous vous recommandons d’installer le [dernier](http://aka.ms/azurebackup_agent) agent Azure Backup sur le dernier correctif cumulatif de SCDPM (UR6 depuis juillet 2015)

****Q4. Pendant la configuration de l’agent Azure Backup, je suis invité à entrer les **informations d’identification de coffre**. Les informations d’identification de coffre expirent-elles ? R4. Oui, les informations d’identification de coffre expirent au bout de 48 heures. Si le fichier expire, connectez-vous au portail Azure et téléchargez les fichiers d’informations d’identification de coffre à partir de votre archivage de sauvegarde.

**Q5. Le nombre d’archivages de sauvegarde pouvant être créés dans chaque abonnement Azure est-il limité ?** <br/> R5. Oui. Depuis juillet 2015, vous pouvez créer 25 archivages par abonnement. Si vous avez besoin de plus d’archivages, créez un autre abonnement.

**Q6. Les archivages sont-ils des entités de facturation ?** <br/> R6. Même s’il est possible d’obtenir une facture détaillée pour chaque archivage, nous vous recommandons vivement de considérer l’abonnement Azure comme entité de facturation. Il est cohérent sur tous les services et est plus facile à gérer.

**Q7. Le nombre de serveurs/ordinateurs pouvant être inscrits pour chaque archivage est-il limité ?** <br/> R7. Oui, vous pouvez inscrire un maximum de 50 ordinateurs par archivage. Pour les machines virtuelles Azure IaaS, la limite est de 200 machines virtuelles par coffre. Si vous avez besoin d’inscrire davantage d’ordinateurs, créez un autre archivage.

**Q8. La quantité de données pouvant être sauvegardées à partir d’un serveur/client Windows ou d’un serveur SCDPM est-elle limitée ?** <br/> R8. Non.

**Q9. Comment inscrire mon serveur dans un autre centre de données ?**<br/> R9. Les données de sauvegarde sont envoyées au centre de données du service Azure Backup dans lequel elles sont inscrites. Le moyen le plus simple de modifier le centre de données est de désinstaller/réinstaller l’agent et de l’inscrire dans un nouveau centre de données.

**Q10. Que se passe-t-il si je renomme un serveur Windows qui sauvegarde des données dans Azure ?**<br/> R10. Lorsque vous renommez un serveur, toutes les sauvegardes actuellement configurées sont arrêtées. Vous devez enregistrer le nouveau nom du serveur avec le coffre de sauvegarde. Lorsque vous créez un nouvel enregistrement, la première opération de sauvegarde est une sauvegarde complète et non une sauvegarde incrémentielle. Si vous devez récupérer des données précédemment sauvegardées dans le coffre avec le nom de l’ancien serveur, vous pouvez utiliser l’option [**Un autre serveur**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) de l’assistant **Récupérer des données**.


**Q11. À partir de quels types de lecteurs puis-je sauvegarder des fichiers et des dossiers ?** <br/> R11. L’ensemble suivant de lecteurs/volumes ne peut pas être sauvegardé :

- Support amovible : le lecteur doit être déclaré fixe pour être utilisé comme source d’éléments de sauvegarde.
- Volumes en lecture seule : le volume doit être accessible en écriture pour que le service VSS puisse fonctionner.
- Volumes déconnectés : le volume doit être en ligne pour que le service VSS puisse fonctionner.
- Partage réseau : le volume doit être local sur le serveur à sauvegarder à l’aide de la sauvegarde en ligne.
- Volumes protégés par BitLocker : le volume doit être déverrouillé pour pouvoir effectuer la sauvegarde.
- Identification du système de fichiers : NTFS est le seul système de fichiers pris en charge pour cette version du service de sauvegarde en ligne.

**Q12. Quels types de fichier et dossier puis-je sauvegarder à partir de mon serveur ?**<br/> R12. Les types suivants sont pris en charge :

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

**Q13. Quelle est la taille minimale requise du dossier du cache ?** <br/> R13. La taille du dossier du cache détermine la quantité de données que vous sauvegardez. Le dossier cache doit représenter 5 % de l’espace requis pour le stockage de données.

**Q14. Si mon organisation possède un archivage de sauvegarde, comment isoler les données d’un serveur d’un autre serveur lors de la restauration des données ?**<br/> R14. Tous les serveurs inscrits dans le même coffre sont en mesure de récupérer les données sauvegardées par d’autres serveurs *qui utilisent la même phrase secrète*. Si vous avez des serveurs dont vous souhaitez isoler les données de sauvegarde des autres serveurs de votre organisation, utilisez une phrase secrète désignée pour ces serveurs. Par exemple, les serveurs des ressources humaines peuvent utiliser une phrase secrète de chiffrement, les serveurs de comptabilité peuvent en utiliser une autre et les serveurs de stockage une troisième.

**Q15. Puis-je « migrer » mes données de sauvegarde entre les abonnements ?** <br/> R15 : non

**Question 16. Puis-je « migrer » mon archivage de sauvegarde entre les abonnements ?** <br/> R16 : non. L’archivage est créé au niveau de l’abonnement et ne peut pas être réaffecté à un autre abonnement une fois créé.

**Question 17. L’agent Azure Backup fonctionne-t-il sur un serveur qui utilise la déduplication Windows Server 2012 ?** <br/> R17 : oui. Le service de l’agent convertit les données dédupliquées en données normales lorsqu'il prépare l'opération de sauvegarde. Il optimise ensuite les données pour la sauvegarde, chiffre les données, puis envoie les données chiffrées au service de sauvegarde en ligne.

**Question 18. Si j’annule une opération de sauvegarde après le démarrage, les données de sauvegarde sont-elles supprimées ?** <br/> R18 : non. L’archivage de sauvegarde stocke les données sauvegardées qui ont été transférées jusqu’au moment de l’annulation. Azure Backup utilise un mécanisme de point de contrôle pour ajouter occasionnellement des points de contrôle aux données de sauvegarde pendant la sauvegarde. En présence de points de contrôle dans les données de sauvegarde le processus de sauvegarde suivant est en mesure de valider l’intégrité des fichiers. La sauvegarde suivante est déclenchée de manière incrémentielle sur les données qui avaient été sauvegardées précédemment. Une sauvegarde incrémentielle optimise l’utilisation de la bande passante, ce qui vous évite d’avoir à transférer les mêmes données plusieurs fois.

**Question 19. Pourquoi l’avertissement « Les sauvegardes Azure n’ont pas été configurées pour ce serveur » apparaît-il alors que j’avais planifié des sauvegardes standard ?** <br/> R19 : cet avertissement est généré lorsque les paramètres de planification de la sauvegarde stockés sur le serveur local diffèrent des paramètres stockés dans l’archivage de sauvegarde. Lorsque le serveur ou les paramètres ont été restaurés à un état correct connu, les planifications de sauvegarde peuvent se désynchroniser. Si vous recevez cet avertissement, [reconfigurez la stratégie de sauvegarde](backup-azure-backup-windows-server.md), puis **Exécuter la sauvegarde maintenant** pour resynchroniser le serveur local avec Azure.

**Question 20. Quelles sont les règles de pare-feu devant être configurées pour Azure Backup ?** <br/> R20. Pour avoir une protection transparente des données sur site vers Azure et la charge de travail vers Azure, il est recommandé que vous autorisiez votre pare-feu à communiquer avec les URL suivantes :

- www.msftncsi.com
- *.Microsoft.com
- *.MicrosoftAzure.com
- *.microsoftonline.com
- *.windows.net

**Q21. Puis-je installer l’agent Azure Backup sur une machine virtuelle Azure déjà sauvegardée par le service Azure Backup en utilisant l’extension de machine virtuelle ?** <br/> A21. Absolument. Azure Backup fournit une sauvegarde au niveau de la machine virtuelle pour les machines virtuelles Azure à l’aide de l’extension de machine virtuelle. Vous pouvez installer l’agent Azure Backup sur le système d’exploitation Windows invité et protéger des fichiers et des dossiers sur ce système invité.

**Q22. Puis-je installer l’agent Azure Backup sur une machine virtuelle Azure pour sauvegarder des fichiers et des dossiers présents sur le stockage temporaire fourni par la machine virtuelle Azure ?** <br/> A22. Vous pouvez installer l’agent Azure Backup sur le système d’exploitation Windows invité et sauvegarder des fichiers et des dossiers sur le stockage temporaire. Toutefois, notez que les sauvegardes échoueront une fois que les données de stockage temporaire seront effacées. En outre, si les données de stockage temporaire ont été supprimées, vous pouvez uniquement restaurer les stockages non volatiles.

**Q23. Quelle est la longueur du chemin d’accès pouvant être spécifiée dans le cadre de la stratégie d’agent Azure Backup avec l’agent Azure Backup ?** <br/> A23. L’agent Azure Backup utilise le format NTFS. La [spécification de longueur de chemin d’accès est limitée par l’API Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Si votre chemin d’accès au fichier dépasse la longueur autorisée par l’API Windows, vous pouvez sauvegarder le dossier parent ou le lecteur de disque des fichiers de votre choix.

**Q24. Quels sont les caractères autorisés dans le chemin d’accès du fichier de stratégie de sauvegarde Azure à l’aide de l’agent Azure Backup ?** <br/> A24. L’agent Azure Backup utilise le format NTFS. Elle active les [caractères NTFS pris en charge](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) dans le cadre de la spécification de fichier.

**Q25. Puis-je utiliser Azure Backup Server pour créer une sauvegarde de la récupération complète (BMR) pour un serveur physique ?** <br/> A25. Oui.

**Q26. Puis-je configurer le service Backup pour envoyer un courrier électronique si une opération de sauvegarde échoue ?** <br/> A26. Oui, le service Backup dispose de plusieurs alertes basées sur l’événement qui peuvent être utilisés avec un script PowerShell. Pour obtenir une description complète, consultez [Notifications d’alerte](backup-azure-manage-vms.md#alert-notifications)



## Sauvegarde et rétention
**Q1. La taille de chaque source de données sauvegardée est-elle limitée ?** <br/> R1. Depuis août 2015, la taille maximale de la source de données du système d’exploitation pris en charge est :

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

**Q2. Existe-t-il des limites au nombre de fois qu’une tâche de sauvegarde peut être planifiée chaque jour ?**<br/> R2. Oui, vous pouvez exécuter les tâches de sauvegarde sur Windows Server ou un client Windows jusqu’à trois fois/jour. Vous pouvez exécuter des tâches de sauvegarde sur System Center DPM jusqu’à deux fois par jour. Vous pouvez exécuter une tâche de sauvegarde pour les machines virtuelles IaaS une fois par jour.

**Q3. Existe-t-il une différence entre la stratégie de planification de DPM et Windows Server (par exemple, sous Windows Server sans DPM) ?** <br/> R3. Oui. Avec DPM, vous pouvez spécifier des planifications quotidiennes, hebdomadaires, mensuelles et annuelles. Windows Server (sans DPM) vous permet de spécifier uniquement les planifications quotidiennes et hebdomadaires.

**Q4. Existe-t-il une différence entre les stratégies de rétention de DPM et d’un serveur/client Windows (par exemple, sur Windows Server sans DPM) ?**<br/> R4. Non, DPM et le serveur/client Windows Server répondent tous les deux à des stratégies quotidienne, hebdomadaire, mensuelle et annuelle de rétention.

**Q5. Puis-je configurer mes stratégies de rétention de manière sélective (par exemple, configurer des stratégies hebdomadaires et quotidiennes, mais pas annuelles et mensuelles) ?**<br/> R5. Oui, la structure de rétention Azure Backup vous permet une flexibilité complète dans la définition de la stratégie de rétention selon vos besoins.

**Q6. Puis-je « planifier une sauvegarde » à 18 h 00 et spécifier des « stratégies de rétention » à une autre heure ?**<br/> R6. Non. Les stratégies de rétention ne peuvent être appliquées que sur les points de sauvegarde. Dans l’image qui suit, la stratégie de rétention est spécifiée sur les sauvegardes effectuées à minuit et à 18 h 00. <br/>

![Planification de sauvegarde et rétention](./media/backup-azure-backup-faq/Schedule.png) <br/>

**Q7. Une copie incrémentielle est-elle transférée pour les stratégies de rétention planifiées ?** <br/> R7. Non, la copie incrémentielle est envoyée en fonction de l’heure mentionnée dans la page de planification de sauvegarde. Les points qui peuvent être conservés sont déterminés par la stratégie de rétention.

**Q8. Si une sauvegarde est conservée pendant une longue période, faut-il plus de temps pour récupérer un point de données plus ancien ?** <br/> R8. Non, le délai de récupération reste identique pour les points de données récents ou anciens. Chaque point de récupération se comporte comme un point complet.

**Q9. Si chaque point de récupération est un point complet, a-t-il un impact sur la quantité totale de stockage de sauvegarde facturable ?**<br/> R9. Les produits avec points de rétention à long terme stockent les données de sauvegarde en tant que points complets. Toutefois, même si ces points *occupent* de l’espace de stockage, ils sont plus faciles et plus rapides à récupérer. Les copies incrémentielles *occupent moins d’espace de stockage*, mais vous devez restaurer une chaîne de données, ce qui rallonge le temps de récupération. L’architecture de stockage d’Azure Backup vous offre le meilleur des deux en stockant les données de manière optimale pour des restaurations rapides et des coûts de stockage faibles. Cette approche de stockage de données garantit que votre bande passante entrante et sortante est utilisée de façon efficace. La quantité de stockage de données et le temps nécessaire pour récupérer les données sont tous les deux réduits au minimum.

**Q10. Le nombre de points de récupération pouvant être créés est-il limité ?**<br/> R10. Non. Nous avons éliminé les limites sur les points de récupération. Vous pouvez créer autant de points de récupération que vous le souhaitez.

**Q11. Pourquoi la quantité de données transférée dans la sauvegarde est-elle différente de la quantité de données que j’ai sauvegardée ?**<br/> R11. Toutes les données sauvegardées sont compressées et chiffrées avant d’être transférées. Une fois la compression et le chiffrement appliqués, les données dans le coffre de sauvegarde sont inférieures de 30 à 40 %.

**Q12. Existe-t-il un moyen d’adapter la quantité de bande passante utilisée par le service Backup ?**<br/> R12. Oui, utilisez l’option **Modifier les propriétés** de l’agent Backup pour régler la bande passante. Ajuste la quantité de bande passante et les heures d’utilisation de cette bande passante. Consultez la page [Limitation de bande passante](../backup-configure-vault.md#enable-network-throttling) pour en savoir plus.

**Q13. Ma bande passante Internet est limitée par rapport à la quantité de données que j’ai besoin de sauvegarder. Est-il possible de déplacer des données vers un emplacement spécifique avec un grand canal réseau et de transmettre ces données dans Azure ?** <br/> Q13. Vous pouvez sauvegarder des données dans Azure via le processus de sauvegarde en ligne standard, ou vous pouvez utiliser le service Azure Import/Export pour transférer des données vers le stockage d’objets blob Azure. Il n’existe aucune autre façon de transférer les données de sauvegarde dans le stockage Azure. Pour plus d’informations sur l’utilisation du service Azure Import/Export avec Azure Backup, consultez l’article [Flux de travail de la sauvegarde hors connexion](backup-azure-backup-import-export).


## Récupérer
**Q1. Combien de récupérations puis-je effectuer sur les données sauvegardées dans Azure ?**<br/> R1. Il n’existe aucune limite concernant le nombre de récupérations dans Azure Backup.

**Q2. Le trafic sortant du centre de données Azure m’est-il facturé pendant les récupérations ?**<br/> R2. Non. Vos récupérations sont gratuites et le trafic sortant ne vous est pas facturé.

## Sécurité
**Q1. Les données envoyées à Azure sont-elles chiffrées ?** <br/> R1. Oui. Les données sont chiffrées sur l’ordinateur client/serveur/SCDPM local avec AES256 et sont envoyées via une connexion HTTPS sécurisée.

**Q2. Les données de sauvegarde sont-elles également chiffrées dans Azure ?**<br/> R2. Oui. Les données envoyées à Azure restent chiffrées (au repos). Microsoft ne déchiffre les données de sauvegarde à aucun moment.

**Q3. Quelle est la longueur minimale de la clé de chiffrement utilisée pour chiffrer les données de sauvegarde ?** <br/> R3. La clé de chiffrement doit contenir au moins 16 caractères.

**Q4. Que se passe-t-il si j’ai égaré la clé de chiffrement ? Puis-je récupérer des données (ou) Microsoft peut-il récupérer les données ?** <br/> R4. La clé utilisée pour chiffrer les données de sauvegarde est présente uniquement localement chez le client. Microsoft ne conserve pas de copie dans Azure et n’a pas accès à la clé. Si le client égare la clé, Microsoft ne peut pas récupérer les données de sauvegarde.

## Cache de sauvegarde

**Q1. Comment puis-je modifier l’emplacement du cache spécifié pour l’agent Azure Backup ?**<br/> R1. Parcourez dans l’ordre la liste ci-dessous pour modifier l’emplacement du cache.
- Arrêtez le moteur Backup en exécutant la commande qui suit dans une invite de commandes avec élévation de privilèges :

  ```PS C:\> Net stop obengine```

- Ne déplacez pas les fichiers, mais copiez le dossier d’espace de cache dans un autre lecteur disposant d’un espace suffisant. L’espace de cache d’origine peut être supprimé après avoir confirmé que les sauvegardes fonctionnent avec le nouvel espace de cache.

- Mettez à jour les entrées de Registre suivantes en utilisant le chemin d’accès au nouveau dossier d’espace de cache.<br/>

|Chemin d’accès au Registre | Clé de Registre | Valeur |
| ------ | ------- | ------|
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | *Emplacement du nouveau dossier de cache* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | *Emplacement du nouveau dossier de cache* |

- Démarrez le moteur Backup en exécutant la commande qui suit dans une invite de commandes avec élévation de privilèges :

  ```PS C:\> Net start obengine```

  Une fois les sauvegardes correctement effectuées avec le nouvel emplacement de cache, vous pouvez supprimer le dossier de cache d’origine.

<!---HONumber=AcomDC_0413_2016-->