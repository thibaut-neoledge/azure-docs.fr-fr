<properties
	pageTitle="Sauvegarde Azure - FAQ"
	description="Forum aux questions sur le service de sauvegarde Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="03/26/2015"
	 ms.author="prvijay"/>

# Sauvegarde Azure - FAQ
Voici une liste de questions fréquemment posées sur la sauvegarde Azure. Si vous avez d'autres questions concernant sauvegarde Azure, accédez à la la [forum de discussion](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazureonlinebackup) et publiez vos questions. Une personne de notre communauté vous aideront à vos réponses. Si une question est fréquemment posée, nous allons l'ajouter à cet article afin qu'il peut trouver rapidement et facilement.

## Installation et Configuration

**Q1. Qu'est la liste des systèmes d'exploitation à partir de laquelle puis-je sauvegarder vers Azure à l'aide de la sauvegarde Azure ?** <br/> A1. La liste suivante de OS'es est pris en charge par la sauvegarde Azure


| Système d'exploitation | Plateforme | SKU |
| :------------- |-------------| :-----|
| Windows 8 et dernières SPs | 64 bits | Entreprise, Pro |
| Windows 7 et dernières SPs | 64 bits | Édition intégrale, entreprise, Professionnel, Édition familiale Premium, Édition Familiale Basique, Starter |
| Windows 8.1 et dernières SPs | 64 bits | Entreprise, Pro |
|Windows Server 2012 R2 et dernières SPs|	64 bits|	Foundation standard, Datacenter,|
|Windows Server 2012 et dernières SPs|	64 bits|	Centre de données, Foundation, Standard|
|Windows Storage Server 2012 R2 et dernières SPs |64 bits|	Standard, groupe de travail|
|Windows Storage Server 2012 et dernières SPs |64 bits |Standard, groupe de travail
|Windows Server 2012 R2 et dernières SPs |64 bits|	Essentielles|
|Windows Server 2008 R2 SP1 |64 bits|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bits|	Standard, Enterprise, Datacenter, Foundation|

**2E TRIMESTRE. Où puis-je télécharger le dernier Azure Backup agent ?** <br/> A2. Vous pouvez télécharger le dernier agent à partir de [ici](http://aka.ms/azurebackup_agent). Cela peut être installé sur Windows Server, serveur SCDPM ou client Windows

**Q3. Version de SCDPM server est pris en charge ?** <br/> A3. Il est recommandé d'installer le [dernière](http://aka.ms/azurebackup_agent) Azure Backup agent sur le dernier correctif cumulatif de SCDPM (UR5 depuis février 2015)

**Q4. Lorsque vous configurez l'agent de sauvegarde Azure, je suis invité à saisir les références de coffre « ». Il n'importe quelle date d'expiration associée aux informations d'identification de coffre ?** <br/> A4. Oui, les informations d'identification de coffre expirent après 48hours. Si le fichier expire, connectez-vous au portail Azure et téléchargez les fichiers d'informations d'identification de coffre depuis votre coffre de sauvegarde.

**Q5. Existe-t-il une limite du nombre de coffres de sauvegarde peuvent être créés dans chaque abonnement Azure ?** <br/> A5. Oui. Depuis mars 2015, vous pouvez créer des 25 coffres par abonnement. Si vous avez besoin de plus de coffres, puis créer un nouvel abonnement.

**Q6. Faut-il examiner les coffre comme une entité de facturation ?** <br/> A6. S'il est possible d'obtenir une facture détaillée pour chaque coffre, il est vivement recommandé que vous envisagez un abonnement Azure comme une entité de facturation. Il est cohérente entre tous les services et plus facile à gérer.

**Q7. Existe-t-il des limites sur le nombre de serveurs/machines peuvent être enregistrés pour chaque coffre ?** <br/> A7. Oui, vous pouvez enregistrer jusqu'à 50 ordinateurs par coffre-fort. Si vous avez besoin inscrire des ordinateurs, créez un coffre de.

**Q8. Existe-t-il des limites sur la quantité de données qui peuvent être sauvegardées à partir d'un client Windows server ou le serveur SCDPM ?** <br/> A8. Non.

**Q9. Comment inscrire mon serveur vers un autre centre de données ?**<br/> A9. En général, les données de sauvegarde sont envoyées au centre de données du Service de sauvegarde dans lequel elle est inscrite. Pour modifier le centre de données le plus simple consiste à désinstaller l'agent et réinstallez l'agent et vous inscrire à un nouveau centre de données.

**Q10. Que se passe-t-il si vous renommez un serveur Windows qui sauvegarde de données vers Azure ?** <br/> A10. Toutes les sauvegardes configurées seront interrompues. Vous devrez réinscrire le serveur avec le coffre de sauvegarde et il est considéré un nouveau serveur par les Services de récupération, la première opération de sauvegarde qui se produit après l'inscription est donc une sauvegarde complète de toutes les données incluses dans la sauvegarde, au lieu des modifications depuis la dernière sauvegarde. Toutefois, si vous avez besoin effectuer une opération de récupération, vous pouvez récupérer les données qui ont été sauvegardées à l'aide de la restauration à partir d'une autre option de récupération de serveur. Pour plus d'informations, voir Renommage d'un serveur.

**Q 11. Quels types de lecteurs pouvez-vous sauvegarder des fichiers et dossiers de ?** <br/> A11. L'ensemble suivant de lecteurs/volumes peut être sauvegarde :

+ Support amovible : Le lecteur doit déclarer fixe pour être utilisé comme source d'éléments de sauvegarde.

+ Les Volumes en lecture seule : le volume doit être accessible en écriture pour le volume shadow copy service (VSS) à fonction.

+ Volumes hors connexion : Le volume doit être en ligne pour VSS pour la fonction.

+ Partage réseau : le volume doit être local sur le serveur à sauvegarder à l'aide de la sauvegarde en ligne.

**Q12. Quels types de fichier et dossier puis-je sauvegarder à partir de mon serveur ?**<br/> A12. Les types suivants sont pris en charge :

+ Chiffré

+ Compressé

+ Sparse

+ Compressé + partiellement alloué

+ Liens physiques : Non pris en charge, ignoré

+ Point d'analyse : Non pris en charge, ignoré

+ Chiffré + compressé : Non pris en charge, ignoré

+ Chiffré + partiellement alloué : Non pris en charge, ignoré

+ Flux compressé : Non pris en charge, ignoré

+ Flux de données Sparse : Pas pris en charge, ignoré

**Q 13. Quelle est la taille minimale requise pour le dossier du cache ?** <br/> A13. La taille du dossier du cache est déterminée par la quantité de données que vous sauvegardez. En général, vous devez prévoir que 10 à 15 % de l'espace requis pour le stockage de données devrait être alloué pour le dossier du cache.

**Q14. Comment isoler les données de serveur spécifique d'être récupérée par d'autres serveurs de mon organisation ? **<br/> A14. Tous les serveurs qui sont inscrits en utilisant le même coffre sera en mesure de récupérer les données sauvegardées par d'autres serveurs qui utilisent le même mot de passe. Si vous avez des serveurs que vous souhaitez garantir la récupération se produit uniquement à des serveurs spécifiques dans votre organisation, vous devez utiliser un mot de passe distinct indiqué pour ces serveurs. Par exemple, les serveurs de ressources humaines peuvent utiliser une phrase secrète de chiffrement, serveurs de comptabilité un autre et les serveurs de stockage tiers.

**Q15. Puis-je « migrer » mes données de sauvegarde entre les abonnements ?** <br/> A15 : non

**Q16 : Puis-je « migrer » mon coffre de sauvegarde entre les abonnements ?** <br/> A16 : non. Le coffre est créé à un niveau d'abonnement et ne peut pas être réaffecté à un autre abonnement qui a été créé.

## Sauvegarde et rétention
**Q1. Y a-t-il une limite sur la taille de chaque source de données à sauvegarder ?** <br/> A1. Depuis mars 2015, chaque source de données doit être inférieure ou égale à 1,7 To. Une source de données est

+ Volume de fichier/dossier

+ BASE DE DONNÉES SQL

+ Batterie de serveurs SharePoint

+ Serveur Exchange

+ Ordinateur virtuel Hyper-V

**2E TRIMESTRE. Y a-t-il limite du nombre d'applications de sauvegarde peut être planifié par jour ?**<br/> A2. Oui, Azure Backup permet des copies de sauvegarde 3 par jour via Windows Client/serveur et des copies de sauvegarde 2 par jour via SCDPM.

**Q3. Existe-t-il une différence entre de DPM et la stratégie de planification de sauvegarde Azure Backup (ex : sur Windows Server sans DPM) ?** <br/> A3. Oui. À l'aide de DPM, vous pouvez spécifier de planification quotidienne, hebdomadaire, mensuelle, annuelle tandis que d'un serveur de Windows (sans DPM), vous pouvez spécifier seulement tous les jours, les planifications hebdomadaires.

**Q4. Existe-t-il une différence entre de DPM et Azure Backup (ex : sur Windows Server sans DPM) stratégie de rétention ?**<br/> A4. Non, vous avez les mêmes fonctions. Vous pouvez spécifier quotidienne, hebdomadaire, mensuelle et annuelle de rétention.

**Q5. Puis-je configurer mon rétention stratégies – par exemple, configurent de manière sélective hebdomadaire et quotidien mais pas annuel et chaque mois ?**<br/> A5. Vous avez le jeu complet de boutons pour définir des stratégies qui définissent les mieux vos exigences de conformité et de rétention.

**Q6. Puis-je « planifier une sauvegarde » à 6 h et spécifiez « stratégies de rétention » à un autre moment ?**<br/> A6. Non. Stratégies de rétention peuvent être appliqués uniquement sur les points de sauvegarde. Dans le sous image, la stratégie de rétention est spécifiée sur les sauvegardes effectuées à 12: 00 et 18 h 00. <br/>

![Rétention et planifier la sauvegarde][1] <br/>

**Q7. Une copie incrémentielle est transférée pour les stratégies de rétention planifiées ?** <br/> A7. Non, la copie incrémentielle est envoyée en fonction de l'heure mentionné dans la page Planification de sauvegarde. Les points qui peuvent être conservés sont déterminées selon la stratégie de rétention.

**Q8. Si la sauvegarde est conservée sur une longue durée, faut-il un temps considérable pour récupérer les données (par exemple, le point le plus ancien) ?** <br/> A8. Non – le temps de récupération le plus ancien ou le dernier point est le même. Chaque point de récupération se comporte comme un point plein.

**Q9. Si chaque point de récupération est comme un point complète, est son impact sur le stockage de sauvegarde total facturable ?**<br/> A9. Produits classiques à long terme rétention stockent les données de sauvegarde en tant que points complètes. Toutefois, celles-ci sont inefficaces de stockage mais facilitent et accélèrent la restauration. Copies incrémentielles sont efficace du stockage, mais vous devez restaurer une chaîne de données qui a un impact sur le temps de récupération. Architecture de stockage unique de la sauvegarde Azure vous offre le meilleur des deux mondes en stockant les données des restaurations rapides optimale encourir les coûts de stockage faible. Cette approche garantit que votre bande passante (entrant et sortant) est utilisé efficacement, stockage est conservé au minimum et le temps de récupération est conservé au minimum.

**Q10. Y a-t-il une limite sur le nombre de points de récupération qui peut être créé ?**<br/> A10. Depuis avril 2015, vous pouvez avoir jusqu'à 366 points de récupération. Vous pouvez utiliser n'importe quel ordre pour arriver à un nombre inférieur à 366. Ex: points de la rétention de la sous image ajouter à 354. <br/>

![Écran de rétention][2]

**Q 11. Une fois que Microsoft améliore la limite de 366, dois-je mettre à niveau de l'agent ou réattribuer la sauvegarde initiale ?** <br/> A11. Non. Une fois que nous apporter la modification dans notre service, vous serez averti via notre médias sociaux (blogs, annonces Azure, etc. portail). Selon vos besoins, vous est ensuite demandé pour modifier uniquement la stratégie de rétention.

**Q12. Pourquoi la quantité de données est transférée dans la sauvegarde différent de la quantité de données sauvegardées ?**<br/> A12. Toutes les données sauvegardées sont compressées et chiffrées avant d'être transférées. Vous pouvez vous attendre 30 à 40 % des avantages de la compression en fonction du type de données à sauvegarder.

## Récupérations
**Q1. Nombre de récupérations puis-je effectuer sur les données qui sont sauvegardées dans Azure ?**<br/> A1. Il n'existe aucune limite sur le nombre de récupérations de sauvegarde Azure.

**2E TRIMESTRE. Dois-je payer pour le trafic sortant à partir du centre de données Azure au cours des restaurations ?**<br/> A2. Non. Votre récupérations sont gratuites et vous n'êtes pas facturé pour le trafic sortant.

## Sécurité
**Q1. Les données sont envoyées vers Azure chiffré ?** <br/> A1. Oui. Les données sont chiffrées sur l'ordinateur du client/serveur/SCDPM local à l'aide de AES256 et les données sont envoyées via une connexion HTTPS sécurisée.

**2E TRIMESTRE. Les données de sauvegarde n'est sur Azure également crypté ?**<br/> A2. Oui. Les données qui sont envoyées vers Azure restent chiffrées (au repos). Microsoft ne déchiffre pas les données de sauvegarde à tout moment.

**Q3. Quelle est la longueur minimale de clé de chiffrement utilisée pour chiffrer les données de sauvegarde ?** <br/> A3. La clé de chiffrement doit être au moins 16 caractères.

**Q4. Que se passe-t-il si j'ai égaré la clé de chiffrement ? Puis-je récupérer des données (ou) Microsoft, récupérez les données ?** <br/> A4. La clé utilisée pour chiffrer les données de sauvegarde est présente uniquement sur le site du client. Microsoft ne conserve pas de copie dans Azure et n'a pas accès à la clé. Si le client égare la clé, Microsoft ne peut pas récupérer les données de sauvegarde.


<!--Image references-->
[1]: ./media/backup-azure-backup-faq/Schedule.png
[2]: ./media/backup-azure-backup-faq/RetentionScreen1.png

<!---HONumber=GIT-SubDir--> 