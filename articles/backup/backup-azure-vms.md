<properties
	pageTitle="Sauvegarde des ordinateurs virtuels Azure : Backup | Microsoft Azure"
	description="En savoir plus sur la sauvegarde d’une machine virtuelle Azure après inscription"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="hero-article" ms.date="09/09/2015" ms.author="aashishr"; "jimpark"/>


# Sauvegarde des machines virtuelles Azure
Cet article est un guide essentiel pour la sauvegarde des machines virtuelles. Avant de continuer, vérifiez que toutes les [conditions préalables](backup-azure-vms-introduction.md#prerequisites) ont été remplies.

Les trois principales étapes de la sauvegarde des machines virtuelles sont les suivantes :

![Trois étapes pour sauvegarder une machine virtuelle Azure](./media/backup-azure-vms/3-steps-for-backup.png)

>[AZURE.NOTE]La sauvegarde de machine virtuelle est locale. Le coffre de sauvegarde situé dans une région Azure spécifiée ne vous autorise pas à sauvegarder des machines virtuelles issues d’une autre région Azure. Ainsi, au moins 1 coffre de sauvegarde doit être créé dans cette région pour chaque région Azure équipée de machines virtuelles nécessitant une sauvegarde.

## 1\. Découvrir les machines virtuelles Azure
Le processus de découverte interroge Azure pour obtenir la liste des machines virtuelles de l’abonnement et des informations supplémentaires, comme le nom du service cloud et la région. Ce processus doit toujours être exécuté en premier. En effet, il permet de vérifier que les nouvelles machines virtuelles ajoutées à l’abonnement sont identifiées.

### Pour lancer le processus de découverte

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.

2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure**, puis cliquez sur le bouton **Sélectionner**.

    ![sélectionner la charge de travail](./media/backup-azure-vms/discovery-select-workload.png)

3. Cliquez sur le bouton **DÉCOUVRIR** en bas de la page. ![bouton découverte](./media/backup-azure-vms/discover-button-only.png)

4. Le processus de découverte peut s’exécuter pendant quelques minutes tandis que les machines virtuelles sont affichées sous forme de tableau. Une notification toast s’affiche en bas de l’écran pendant l’exécution du processus de découverte.

    ![découvrir des machines virtuelles](./media/backup-azure-vms/discovering-vms.png)

5. Une fois le processus de découverte terminé, une autre notification toast s’affiche.

    ![découverte terminée](./media/backup-azure-vms/discovery-complete.png)

##  2\. Inscription des machines virtuelles Azure
Pour protéger une machine virtuelle, cette dernière doit être inscrite auprès du service Azure Backup. L’objectif principal du processus d’inscription consiste à associer la machine virtuelle au service de sauvegarde Azure Backup. L’inscription est généralement une activité unique.

>[AZURE.NOTE]L’extension de sauvegarde n’est pas installée pendant l’étape d’inscription. L’installation et la mise à jour de l’agent de sauvegarde font désormais partie de la tâche de sauvegarde planifiée.

### Pour inscrire des machines virtuelles

1. Accédez au coffre de sauvegarde se trouvant sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.

2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Azure Virtual Machine**, puis cliquez sur le bouton de sélection.

    ![sélectionner la charge de travail](./media/backup-azure-vms/discovery-select-workload.png)

3. Cliquez sur le bouton **INSCRIRE** en bas de la page.

    ![bouton inscription](./media/backup-azure-vms/register-button-only.png)

4. Dans le menu contextuel **Inscrire les éléments**, sélectionnez les machines virtuelles que vous souhaitez inscrire. Si au moins deux machines virtuelles portent le même nom, utilisez le service cloud pour les distinguer.

    L’opération **Inscrire** peut être effectuée à grande échelle, ce qui signifie que plusieurs machines virtuelles peuvent être sélectionnées en même temps pour l’inscription. Cela réduit considérablement l’opération unique de préparation de la machine virtuelle pour la sauvegarde.

5. Un travail est créé pour chaque machine virtuelle à inscrire. La notification toast indique l’état de cette activité. Cliquez sur **Afficher le travail** pour accéder à la page **Travaux**.

    ![inscrire le travail](./media/backup-azure-vms/register-create-job.png)

6. La machine virtuelle est également affichée dans la liste des éléments inscrits et l’état de l’opération d’inscription s’affiche.

    ![État de l’inscription 1](./media/backup-azure-vms/register-status01.png)

7. Une fois l’opération terminée, l’état affiché dans le portail change pour refléter l’état inscrit.

    ![État de l’inscription 2](./media/backup-azure-vms/register-status02.png)

## 3\. Protection : sauvegarde des machines virtuelles Azure
Cette étape implique la configuration d’une stratégie de sauvegarde et de rétention pour la machine virtuelle. Plusieurs machines virtuelles peuvent être protégées lors de l’évolution à l’aide d’une action de protection unique.

>[AZURE.NOTE]Les coffres de sauvegarde Azure créés après mai 2015 sont livré avec une stratégie par défaut intégrée. Cette stratégie par défaut est fournie avec une durée de rétention par défaut de 30 jours et une fréquence quotidienne de sauvegarde d’une fois par jour.

Pour protéger une machine virtuelle, procédez comme suit :

1. Accédez au coffre de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.
2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure**, puis cliquez sur le bouton **Sélectionner**.

    ![Sélectionner la charge de travail dans le portail](./media/backup-azure-vms/select-workload.png)

3. En bas de la page, cliquez sur **CONNECT**. L’Assistant **Protection des éléments** s’affiche. Cet Assistant ne répertorie que les machines virtuelles qui sont enregistrées et non protégées.

4. L’Assistant **Protection des éléments** se trouve à l’endroit où vous pouvez sélectionner les machines virtuelles à protéger. Si au moins deux machines virtuelles portent le même nom, utilisez le service cloud pour les distinguer.

    L’opération **Protéger** peut être effectuée à grande échelle, ce qui signifie que plusieurs machines virtuelles peuvent être sélectionnées en même temps pour l’inscription. Cela réduit considérablement les opérations nécessaires pour la protection de la machine virtuelle.

    ![Configurer les paramètres de protection pendant la mise à jour](./media/backup-azure-vms/protect-at-scale.png)

5. Dans le deuxième écran de l’Assistant **Protection des éléments**, choisissez une stratégie de sauvegarde pour sauvegarder les machines virtuelles sélectionnées. Sélectionnez un ensemble de stratégies existant ou définissez-en un nouveau.

    Dans chaque archivage de sauvegarde, vous pouvez avoir plusieurs stratégies de sauvegarde. Ces stratégies reflètent les informations détaillées sur la planification et la conservation de la sauvegarde. Par exemple, une stratégie de sauvegarde peut être définie sur une sauvegarde quotidienne à 22 h 00, alors qu’une autre est définie pour effectuer une sauvegarde hebdomadaire à 6 h 00. Plusieurs stratégies de sauvegarde améliorent la flexibilité de planification de sauvegardes pour l’infrastructure de votre machine virtuelle.

    Vous pouvez associer plusieurs machines virtuelles à chaque stratégie de sauvegarde. Vous ne pouvez associer votre machine virtuelle qu’à une seule stratégie à un moment donné.

    ![Protéger grâce à la nouvelle stratégie](./media/backup-azure-vms/policy-schedule.png)

6. Dans le troisième écran de l’Assistant **Protéger les éléments**, choisissez une durée de rétention à associer à des sauvegardes effectuées. Ce filtre prend en charge le schéma de rétention standard de l’industrie GFS (Grand père-Père-Fils). En savoir plus sur [Rétention à Long terme] (backup-azure-vms-introduction.md#Long term retention).

    Une stratégie de sauvegarde implique également le schéma de rétention des sauvegardes planifiées. La sélection d’une stratégie de sauvegarde existante dans l’écran précédent désactive la modification du schéma de rétention. Les sauvegardes respectent la politique de rétention, comme indiqué dans la stratégie.

    ![Protéger avec la rétention flexible](./media/backup-azure-vms/policy-retention.png)

7. Un travail est créé pour chaque machine virtuelle afin de configurer la stratégie de protection et d’associer les machines virtuelles à la stratégie. Cliquez sur l’onglet **Travaux** et sélectionnez le filtre approprié pour afficher la liste des travaux de **Configuration de la protection**.

    ![Configurer le travail de protection](./media/backup-azure-vms/protect-configureprotection.png)


## Activités post-protection

### Installation de l’extension de sauvegarde
Le service Azure Backup gère de façon transparente la mise à niveau et la correction de l’extension de sauvegarde sans aucune intervention fastidieuse pour l’utilisateur. Cela évite à l’utilisateur la « surcharge de gestion des agents » généralement associée aux produits de sauvegarde.

#### Machines virtuelles hors connexion
L’extension de sauvegarde est installée si la machine virtuelle est en cours de fonctionnement. Une machine virtuelle en cours d’exécution présente également le plus grand risque d’obtenir un point d’application cohérent. Toutefois, le service Azure Backup poursuit la sauvegarde de machine virtuelle, même si elle est éteinte et si l’extension n’a pas été installée (c’est-à-dire, si la machine virtuelle est hors ligne). L’impact se voit à la cohérence, auquel cas le point de récupération est * cohérent avec le crash*.

### Sauvegarde initiale
Une fois la machine virtuelle protégée par une politique, elle apparaît sous l’onglet **Éléments protégés**, à l’état *Protégé* (sauvegarde initiale en attente). Par défaut, la première sauvegarde planifiée est la sauvegarde initiale. Pour déclencher la sauvegarde initiale immédiatement après la configuration de protection, utilisez le bouton **Sauvegarder maintenant** au bas de la page **Éléments protégés**.

Le service Azure Backup crée un travail de sauvegarde pour l’opération de sauvegarde initiale. Cliquez sur l’onglet **Travaux** pour afficher la liste des travaux. Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent.

![Sauvegarde en cours](./media/backup-azure-vms/protect-inprogress.png)

Une fois la sauvegarde initiale terminée, l’*état de protection* de la machine virtuelle présente dans l’onglet **Éléments protégés** s’affiche en tant que *protégé*.

![La machine virtuelle est sauvegardée avec un point de récupération](./media/backup-azure-vms/protect-backedupvm.png)

### Affichage des détails et de l’état de sauvegarde
Une fois la protection appliquée, le nombre de machines virtuelles augmente également sur la page de synthèse nommée **Tableau de bord**. La page **Tableau de bord** affiche également le nombre de travaux des dernières 24 heures ayant réussi, ayant échoué ou toujours en cours. Cliquez sur n’importe quelle catégorie pour l’examiner plus en détail dans la page **Travaux**.

![État de la sauvegarde sur la page Tableau de bord](./media/backup-azure-vms/dashboard-protectedvms.png)

### Rétention à long terme
La stratégie de rétention spécifie la durée de stockage de la sauvegarde. Au lieu de simplement spécifier une stratégie e rétention uniforme pour tous les points de sauvegarde, les clients peuvent spécifier différentes stratégies de rétention en fonction du moment où est effectuée la sauvegarde. Par exemple, le point de sauvegarde effectué à la fin de chaque trimestre peut devoir être conservé plus longtemps à des fins d’audit, alors que le point de sauvegarde effectué quotidiennement (qui fait office de point de récupération opérationnel) doit être conservé pendant 90 jours.

1. **Stratégie de rétention quotidienne** : les sauvegardes effectuées quotidiennement sont stockées pendant 30 jours.
2. **Stratégie de rétention hebdomadaire** : sauvegardes effectuées tous les samedis à minuit et 18 h 00 sont conservées pendant 104 semaines.
3. **Stratégie de rétention mensuelle** : les sauvegardes effectuées le dernier samedi de chaque mois à minuit et à 18 h 00 sont conservées pendant 120 mois.
4. **Stratégie de rétention annuelle** : les sauvegardes effectuées le dernier samedi de janvier de chaque année sont conservées pendant 99 ans.

## Cohérence des points de récupération
Lorsque vous traitez des données de sauvegarde, l’une des principales préoccupations des clients concerne le comportement de la machine virtuelle après sa restauration. Les questions habituelles des clients sont :

- La machine virtuelle va-t-elle démarrer ?
- Les données seront-elles disponibles sur le disque (ou) des données seront-elles perdues ?
- L’application sera-t-elle en mesure de lire les données (ou) les données seront-elles endommagées ?
- L’application pourra-t-elle lire les données (ou) les données seront-elles cohérentes lorsqu’elles seront lues par l’application ?

Le tableau suivant décrit les types de cohérence rencontrés pendant la restauration et la sauvegarde d’une machine virtuelle Azure.

| Cohérence | En fonction du service VSS | Explication et détails |
|-------------|-----------|---------|
| Cohérence des applications | Oui | C’est l’endroit idéal pour les charges de travail Microsoft, car il garantit les points suivants :<ol><li> La machine virtuelle *démarre*. <li>Les données *ne sont pas endommagées*. <li>Il n’y a *aucune perte de données*.<li> Les données sont cohérentes vis-à-vis de l’application qui les utilise, et appelle l’application au moment de la sauvegarde (à l’aide de VSS).</ol> Le service VSS (Volume Shadow Copy Service) garantit que les données sont correctement écrites dans le stockage. La plupart des charges de travail de Microsoft ont des enregistreurs VSS qui effectuent des actions de charges de travail spécifiques relatives à la cohérence des données. Par exemple, Microsoft SQL Server dispose d’un enregistreur VSS qui garantit que les écritures dans le journal des transactions et de la base de données sont effectuées correctement.<br><br> Pour la sauvegarde d’une machine virtuelle Azure, l’obtention d’un point de récupération cohérent signifie que l’extension de sauvegarde a pu appeler le flux de travail VSS et se terminer *correctement* avant la prise de l’instantané de la machine virtuelle. Bien entendu, cela signifie que les enregistreurs VSS de toutes les applications dans la machine virtuelle Azure ont été également appelés.<br><br>Découvrez les[principes de base du service VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), puis approfondissez vos connaissances sur [son fonctionnement](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Cohérence du système de fichiers | Oui : pour les machines Windows | Il existe deux scénarios où le point de récupération peut être cohérent avec le système de fichiers :<ul><li>En cas de sauvegarde de machines virtuelles Linux dans Azure, Linux n’ayant pas une plateforme équivalente à VSS.<li>En cas d’échec du service VSS lors de la sauvegarde de machines virtuelles Windows dans Azure.</li></ul> Dans ces deux cas, la meilleure solution consiste à garantir les points suivants : <ol><li> La machine virtuelle *démarre*. <li>Les données *ne sont pas endommagées*.<li>Il n’y a pas de *perte de données*.</ol> Les applications doivent implémenter leur propre mécanisme de « correctif » sur les données restaurées.|
| Cohérence en cas d’incident | Non | Cette situation est la même que lorsqu’une machine rencontre un « incident » (via une réinitialisation matérielle ou logicielle). Cela se produit généralement lorsque la machine virtuelle Azure est arrêtée au moment de la sauvegarde. Pour la sauvegarde d’une machine virtuelle Azure, l’obtention d’un point de récupération cohérent suite à un incident signifie qu’Azure Backup ne fournit aucune garantie de cohérence des données sur le support de stockage, que ce soit au niveau du système d’exploitation ou de l’application. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées. <br/> <br/> Même s’il n’existe aucune garantie, dans la plupart des cas, le système d’exploitation démarre. Ce démarrage est généralement suivi d’une procédure de vérification du disque comme chkdsk permettant de résoudre les erreurs d’endommagement. Les données ou les écritures en mémoire qui n’ont pas été complètement transférées sur le disque seront perdues. Si une restauration de données est nécessaire, l’application suit généralement son propre mécanisme de vérification. Pour la sauvegarde de machine virtuelle Azure, l’obtention d’un point de récupération cohérent suite à un incident signifie que Microsoft Azure Backup ne fournit aucune garantie de cohérence des données sur le stockage, au niveau du système d’exploitation ou de l’application. Cela se produit généralement quand la machine virtuelle Azure est arrêtée au moment de la sauvegarde.<br><br>Par exemple, cette situation peut survenir si le journal des transactions comporte des entrées qui n’existent pas dans la base de données. Le logiciel de base de données effectue alors une restauration jusqu’à ce que les données soient cohérentes. Lorsque vous traitez des données réparties sur plusieurs disques virtuels (comme des volumes fractionnés), un point de récupération cohérent après incident ne fournit aucune garantie quant à l’exactitude des données.|


## Performance et utilisation des ressources
Au même titre qu’un logiciel de sauvegarde déployé sur site, l’utilisation de capacité et des ressources des machines virtuelles dans Azure doivent être planifiées. Les [limites de stockage Azure](azure-subscription-service-limits.md#storage-limits) définiront la structuration des déploiements de machine virtuelle pour obtenir des performances maximales avec un impact minimum sur l’exécution des charges de travail. Il existe deux limites de stockage Azure principales sauvegarde ayant un impact sur les performances de sauvegarde :

- Nombre maximal de sorties par compte de stockage
- Taux de requête total par compte de stockage

Lorsque les données de sauvegarde sont copiées hors du compte de stockage du client, il est comptabilisé dans les mesures de l’IOPS et des sorties (débit de stockage) du compte de stockage. Dans le même temps, les machines virtuelles exécutent et consomment des IOPS et débit. L’objectif est de vous assurer que le trafic général (sauvegarde et machine virtuelle) ne dépasse pas les limites du compte de stockage.

La sauvegarde est gourmande en temps et consomme autant de ressources possibles, car son objectif consiste à terminer sa sauvegarde dans les meilleurs délais. Toutefois, toutes les opérations d’E/S sont limitées par le *débit cible d’un seul objet Blob*, qui est limité à *60 Mo par seconde*. Pour accélérer le processus de sauvegarde, la sauvegarde de chaque disque de la machine virtuelle est tentée *en parallèle*. Donc, si une machine virtuelle est équipée de 4 disques, la sauvegarde Azure va tenter de sauvegarder l’ensemble des 4 disques simultanément. Par conséquent, le facteur le plus important pour déterminer le trafic de sauvegarde sortant d’un compte de stockage est le **nombre de disques** sauvegardés sur le compte de stockage.

Autre facteur ayant un impact sur les performances : la **planification de sauvegarde**. Si vous configurez toutes les machines virtuelles à sauvegarder simultanément, le nombre de disques à sauvegarder *en parallèle* augmente. Azure Backup va tenter de sauvegarder autant de disques que possible. Par conséquent, il est possible de réduire le trafic de sauvegarde depuis un compte de stockage en s’assurant que les différentes machines virtuelles sont sauvegardées à différents moments de la journée, sans chevauchement.

### Planification de la capacité
Le rassemblement de tous ces facteurs signifie que l’utilisation du compte de stockage doit être planifiée correctement. Téléchargez la [feuille de calcul Excel de planification des capacités de machine virtuelle](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) pour voir l’impact de vos choix en matière de disque et de planification de sauvegarde.

### Débit de sauvegarde
Pour chaque disque en cours de sauvegarde, Azure Backup lit les blocs sur le disque et stocke uniquement les données modifiées (sauvegarde incrémentielle). Le tableau ci-dessous indique les valeurs de débit moyen attendues de la sauvegarde Azure :

| Opération de sauvegarde | Meilleur débit |
| ---------------- | ---------- |
| Sauvegarde initiale | 160 Mbits/s |
| Sauvegarde incrémentielle (DR) | 640 Mbits/s <br><br> Ce débit baisse considérablement si une quantité importante de code est dispersée sur le disque et doit être sauvegardée |

Ce type de sauvegarde permet d’estimer la quantité de temps nécessaire à la sauvegarde d’un disque de taille donnée.

### Durée de sauvegarde de machine virtuelle totale
La majeure partie du temps est consacrée à la lecture et la copie des données, mais il existe d’autres opérations qui contribuent à la durée totale nécessaire à la sauvegarde d’une machine virtuelle :

1. Durée nécessaire à l’[installation de la mise à jour de l’extension de sauvegarde](backup-azure-vms.md#offline-vms)
2. Temps d’attente de file d’attente : le service traitant les sauvegardes de plusieurs clients, il se peut que votre opération de sauvegarde ne démarre pas immédiatement. Le temps d’attente moyen d’une machine virtuelle est de 15 à 30 minutes.

## Résolution des erreurs
Obtenez une liste exhaustive des solutions de contournement des erreurs rencontrées au cours de la sauvegarde d’une machine virtuelle :

- [Dépannage de la sauvegarde de machine virtuelle](backup-azure-vms-troubleshoot.md)

## Étapes suivantes
Pour plus d’informations sur la prise en main de Microsoft Azure Backup, consultez :

- [Restauration des machines virtuelles](backup-azure-restore-vms.md)
- [Gestion des machines virtuelles](backup-azure-manage-vms.md)

<!---HONumber=Sept15_HO2-->