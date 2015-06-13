<properties
	pageTitle="Sauvegarde des machines virtuelles Azure : Backup"
	description="En savoir plus sur la sauvegarde d’une machine virtuelle Azure après inscription"
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/26/2015"
	ms.author="aashishr"/>


# Sauvegarder des machines virtuelles

Cet article est un guide essentiel pour la sauvegarde des machines virtuelles. Avant de continuer, vérifiez que toutes les [conditions préalables](backup-azure-vms-introduction.md#prerequisites) ont été remplies.

Les trois principales étapes de la sauvegarde des machines virtuelles sont les suivantes :

![Trois étapes pour sauvegarder une machine virtuelle Azure](./media/backup-azure-vms/3-steps-for-backup.png)

## Découvrir les machines virtuelles
Le processus de découverte interroge Azure pour obtenir la liste des ordinateurs virtuels dans l'abonnement, ainsi que des informations supplémentaires telles que le nom du Service de cloud computing et la région.

> [AZURE.NOTE]Ce processus doit toujours être exécuté en premier. En effet, il permet de vérifier que les nouvelles machines virtuelles ajoutées à l’abonnement sont identifiées.

Pour déclencher le processus de découverte, procédez comme suit :

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.

2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure**, puis cliquez sur le bouton **Sélectionner**. ![sélectionner la charge de travail](./media/backup-azure-vms/discovery-select-workload.png)

3. Cliquez sur le **DISCOVER** bouton au bas de la page. ![bouton découverte](./media/backup-azure-vms/discover-button.png)

4. Le processus de découverte peut s’exécuter pendant quelques minutes tandis que les machines virtuelles sont affichées sous forme de tableau. Une notification toast s’affiche en bas de l’écran pendant l’exécution du processus de découverte. ![découvrir des machines virtuelles](./media/backup-azure-vms/discovering-vms.png)

5. Une fois le processus de découverte terminé, une autre notification toast s’affiche. ![découverte terminée](./media/backup-azure-vms/discovery-complete.png)


## Inscrire des machines virtuelles
Avant un ordinateur virtuel peut être protégé, il doit être inscrit auprès du service de sauvegarde Azure. Le processus d’inscription a deux objectifs principaux :

1. Connecter l’extension de sauvegarde à l’agent de machine virtuelle dans la machine virtuelle

2. Pour associer l'ordinateur virtuel avec le service de sauvegarde Azure

L’inscription est généralement une activité unique. Le service Azure Backup gère de façon transparente la mise à niveau et la correction de l’extension de sauvegarde sans aucune intervention fastidieuse pour l’utilisateur. Cela évite à l’utilisateur la « surcharge de gestion des agents » généralement associée aux produits de sauvegarde.

### Pour inscrire des machines virtuelles

1. Naviguer dans le coffre de sauvegarde qui se trouve sous **Services de récupération** dans le portail Azure, puis cliquez sur le **éléments enregistrés** onglet

2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure** puis cliquez sur le bouton de sélection. ![sélectionner la charge de travail](./media/backup-azure-vms/discovery-select-workload.png)

3. Cliquez sur le **inscrire** bouton au bas de la page. ![bouton inscription](./media/backup-azure-vms/register-button.png)

4. Dans la fenêtre contextuelle **Inscrire les éléments** sélectionnez les machines virtuelles que vous souhaitez inscrire. S'il y a deux ou plusieurs ordinateurs virtuels portant le même nom utiliser le service de cloud pour faire la distinction entre les machines virtuelles.

    L’opération **Inscrire** peut être effectuée à grande échelle, ce qui signifie que plusieurs machines virtuelles peuvent être sélectionnées en même temps pour l’inscription. Cela réduit considérablement l’opération unique de préparation de la machine virtuelle pour la sauvegarde.

    >[AZURE.NOTE]Seuls les ordinateurs virtuels qui ne sont pas enregistrés et se trouvent dans la même région que le coffre de sauvegarde s'affichent.

5. Un travail est créé pour chaque machine virtuelle à inscrire. La notification toast indique l’état de cette activité. Cliquez sur **Afficher le travail** pour accéder à la page **Travaux**. ![inscrire le travail](./media/backup-azure-vms/register-create-job.png)

6. La machine virtuelle est également affichée dans la liste des éléments inscrits et l’état de l’opération d’inscription s’affiche ![État de l’inscription 1](./media/backup-azure-vms/register-status01.png)

7. Une fois l’opération terminée, l’état affiché dans le portail change pour refléter l’état inscrit. ![État de l’inscription 2](./media/backup-azure-vms/register-status02.png)

## Sauvegarder des machines virtuelles
Cette étape implique la configuration d’une stratégie de sauvegarde et de rétention pour la machine virtuelle. Pour protéger une machine virtuelle, procédez comme suit :

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.
2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure**, puis cliquez sur le bouton **Sélectionner**. ![Sélectionner la charge de travail dans le portail](./media/backup-azure-vms/select-workload.png)

3. Cliquez sur le **protéger** bouton au bas de la page.

4. Un Assistant **Protection des éléments** s’affiche, où les machines virtuelles à protéger peuvent être sélectionnées. S'il y a deux ou plusieurs ordinateurs virtuels portant le même nom utiliser le service de cloud pour faire la distinction entre les machines virtuelles.

    L’opération **Protéger** peut être effectuée à grande échelle, ce qui signifie que plusieurs machines virtuelles peuvent être sélectionnées en même temps pour l’inscription. Cela réduit considérablement les opérations nécessaires pour la protection de la machine virtuelle.

    >[AZURE.NOTE]Seules les machines virtuelles correctement inscrites avec le service Azure Backup et situées dans la même région que l’archivage de sauvegarde sont affichées ici.

5. Dans le deuxième écran de l’Assistant **Protection des éléments**, choisissez une stratégie de sauvegarde et de rétention pour sauvegarder les machines virtuelles sélectionnées. Sélectionnez un ensemble de stratégies existant ou définissez-en un nouveau.

    >[AZURE.NOTE]Pour la version préliminaire, la durée maximale de rétention va jusqu’à 30 jours et le nombre de sauvegardes maximum par jour est de 1.

    Dans chaque archivage de sauvegarde, vous pouvez avoir plusieurs stratégies de sauvegarde. Ces stratégies reflètent les informations détaillées sur la planification et la conservation de la sauvegarde. Par exemple, une stratégie de sauvegarde peut être définie pour une sauvegarde quotidienne à 22h00, alors qu’une autre stratégie de sauvegarde peut être définie pour une sauvegarde hebdomadaire à 06h00. Plusieurs stratégies de sauvegarde améliorent la flexibilité de planification de sauvegardes pour l’infrastructure de votre machine virtuelle.

    Vous pouvez associer plusieurs machines virtuelles à chaque stratégie de sauvegarde. Vous ne pouvez associer votre machine virtuelle qu’à une seule stratégie à un moment donné.

6. Un travail est créé pour chaque machine virtuelle afin de configurer la stratégie de protection et d’associer les machines virtuelles à la stratégie. Cliquez sur l’onglet **Travaux** et sélectionnez le filtre approprié pour afficher la liste des travaux **Configurer la protection**. ![Configurer le travail de protection](./media/backup-azure-vms/protect-configureprotection.png)

7. Lorsque vous avez terminé, les machines virtuelles sont protégées par une stratégie et doivent attendre jusqu’à ce que le délai de sauvegarde prévu pour la sauvegarde initiale se termine. La machine virtuelle apparaît désormais sous l’onglet **Éléments protégés**, tandis que leur état est défini sur *Protégée* (en attente de la sauvegarde initiale).
    >[AZURE.NOTE]Le démarrage de la sauvegarde initiale immédiatement après la configuration de la protection n’est pas disponible pour le moment.

8. À l’heure planifiée, le service Azure Backup crée un travail de sauvegarde pour chaque machine virtuelle à sauvegarder. Cliquez sur l’onglet **Travaux** pour afficher la liste des travaux **Backup**. Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent. ![Sauvegarde en cours](./media/backup-azure-vms/protect-inprogress.png)

9. Une fois la commande exécutée, l’état de protection de la machine virtuelle sous l’onglet **Éléments protégés** s’affiche en tant que *protégé*. ![La machine virtuelle est sauvegardée avec un point de récupération](./media/backup-azure-vms/protect-backedupvm.png)

## Affichage des détails et l'état de sauvegarde
Une fois la protection appliquée, le nombre de machines virtuelles augmente également sur la page de synthèse nommée **Tableau de bord**. Cette page affiche également le nombre de travaux à partir des dernières 24 heures ayant réussi, échoué et toujours en cours. Cliquez sur n’importe quelle catégorie pour l’examiner plus en détail dans la page **Travaux**. ![État de la sauvegarde sur la page Tableau de bord](./media/backup-azure-vms/dashboard-protectedvms.png)

## Résolution des erreurs
Vous pouvez résoudre les erreurs rencontrées pendant que l'utilisation d'Azure Backup avec des informations figurant dans le tableau ci-dessous.

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Découverte | Échec de la découverte de nouveaux éléments : Microsoft Azure Backup a rencontré une erreur interne. Patientez quelques minutes et recommencez l’opération. | Recommencez le processus de découverte après 15 minutes.
| Découverte | Échec de la découverte de nouveaux éléments : une autre opération de découverte est déjà en cours. Veuillez patienter jusqu’à la fin de l’opération de découverte en cours. | Aucun |
| S’inscrire | Le rôle de machine virtuelle Azure n’est pas en état pour installer l’extension : veuillez vérifier si la machine virtuelle est en cours d’exécution. L’extension Azure Recovery Services requiert l’exécution de la machine virtuelle. | Démarrez la machine virtuelle et, lorsqu’elle est en cours d’exécution, recommencez l’opération d’inscription.|
| S’inscrire | Nombre de disques de données attaché à la machine virtuelle a dépassé la limite prise en charge : détacher des disques de données sur cet ordinateur virtuel et recommencez l'opération. Sauvegarde Azure prend en charge jusqu'à 5 disques de données attachés à une machine virtuelle Azure pour la sauvegarde | Aucun |
| S’inscrire | Microsoft Azure Backup a rencontré une erreur interne - attendre quelques minutes et réessayez l'opération. Si le problème persiste, contactez le Support Microsoft. | Vous pouvez obtenir cette erreur en raison d'une des configurations non prises en charge suivantes : <ol><li>LRS Premium <li>multiples NIC <li>équilibrage de charge </ol> |
| S’inscrire | Certificat de l'Agent invité ordinateur virtuel introuvable | Suivez ces instructions pour résoudre l'erreur : <ol><li>Télécharger la dernière version de l'Agent de machine virtuelle à partir de [ici](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vérifiez que la version de l'agent téléchargé est 2.6.1198.718 ou une version ultérieure. <li>Installez l'Agent de machine virtuelle de la machine virtuelle.</ol> [Apprendre](#validating-vm-agent-installation) comment vérifier la version de l'Agent de machine virtuelle. |
| S’inscrire | Échec de l'inscription avec le délai d'expiration de l'installation d'Agent opération | Vérifiez si la version du système d'exploitation de l'ordinateur virtuel est prise en charge. |
| S’inscrire | L'exécution de commande a échoué - une autre opération est en cours sur cet élément. Veuillez patienter jusqu'à la fin de l'opération précédente | Aucun |
| Sauvegarde | Copie de disques durs virtuels coffre de sauvegarde a expiré - Veuillez recommencer l'opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft. | Cela se produit lorsqu'il y a trop de données à copier. Vérifiez si vous disposez de six disques de données. |
| Sauvegarde | Instantané VM sub tâche a expiré, veuillez recommencer l'opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft | Cette erreur est générée si un problème avec l'Agent de machine virtuelle ou d'accès réseau à l'infrastructure Azure est bloquée d'une certaine façon. <ul><li>En savoir plus sur [débogage des problèmes d'Agent de machine virtuelle](#Troubleshooting-vm-agent-related-issues) <li>en savoir plus sur [débogage des problèmes de réseau](#troubleshooting-networking-issues) </ul> |
| Sauvegarde | La sauvegarde a échoué avec une erreur interne - Veuillez recommencer l'opération dans quelques minutes. Si le problème persiste, contactez le Support Microsoft | Vous pouvez obtenir cette erreur pour des raisons de 2: <ol><li> est trop de données à copier. Vérifiez si vous disposez de six disques. <li>La machine virtuelle d'origine a été supprimée, et par conséquent la sauvegarde ne peut pas être effectuée. Afin de conserver les données de sauvegarde pour un ordinateur virtuel supprimé, mais arrêter les erreurs de sauvegarde, ôter la protection de la machine virtuelle, puis choisissez l'option Conserver les données. Cela empêchera la planification de sauvegarde, ainsi que les messages d'erreur récurrents. |
| Sauvegarde | Impossible d'installer les Services de récupération Azure extension sur l'élément sélectionné - Agent de machine virtuelle est requis pour l'Extension des Services de récupération Azure. Installez l'agent de machine virtuelle Azure et recommencez l'opération d'enregistrement | <ol> <li>Vérifier si l'agent de machine virtuelle a été installé correctement. <li>Vérifiez que l'indicateur sur la configuration de l'ordinateur virtuel est défini correctement.</ol> [Plus](#validating-vm-agent-installation) sur l'installation de l'agent de machine virtuelle et comment valider l'installation de l'agent de machine virtuelle. |
| Sauvegarde | L'exécution de commande a échoué - une autre opération est actuellement en cours sur cet élément. Veuillez patienter jusqu'à la fin de l'opération précédente, puis réessayez | Une sauvegarde existante ou un travail de restauration de l'ordinateur virtuel est en cours d'exécution, et un nouveau travail ne peut être démarré pendant l'exécution du travail existant. <br><br>Si vous souhaitez que la possibilité d'annuler une tâche en cours, ajoutez votre vote pour le [Azure évaluations](http://feedback.azure.com/forums/258995-azure-backup-and-scdpm/suggestions/7941501-add-feature-to-allow-cancellation-of-backup-restor). |

### Problèmes de résolution des problèmes d'Agent de machine virtuelle

#### Configuration de l'Agent de machine virtuelle
En règle générale, l'Agent de machine virtuelle est déjà présent dans les ordinateurs virtuels qui sont créés à partir de la galerie Azure. Cependant, les ordinateurs virtuels qui sont migrés à partir de centres de données sur site n'aurait pas installé l'Agent de machine virtuelle. Pour ces machines virtuelles, l'Agent de machine virtuelle doit être installé de manière explicite. En savoir plus sur [l'installation de l'agent de machine virtuelle sur un ordinateur virtuel existant](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Pour les machines virtuelles Windows :

- Téléchargez et installez le [agent MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Vous aurez besoin des privilèges d'administrateur pour terminer l'installation.
- [Mettre à jour la propriété de la machine virtuelle](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) pour indiquer que l'agent est installé.


#### Mise à jour de l'Agent de machine virtuelle
Mise à jour de l'Agent de machine virtuelle est aussi simple que la réinstallation du [binaires de l'Agent de machine virtuelle](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Toutefois, vous devez vous assurer qu'aucune opération de sauvegarde ne s'exécute alors que la mise à jour de l'Agent de machine virtuelle.


#### Validation de l'installation de l'Agent de machine virtuelle
Comment vérifier la version de l'Agent de machine virtuelle sur les machines virtuelles Windows :

- Connexion à la machine virtuelle Azure et accédez au dossier *C:\WindowsAzure\\Packages*.
- Vous devriez trouver le fichier WaAppAgent.exe présent.
- Avec le bouton droit sur le fichier, accédez à **propriétés**, puis sélectionnez le **détails** onglet.
- Le champ de Version du produit doit être 2.6.1198.718 ou une version ultérieure

### Résolution des problèmes de mise en réseau
Comme toutes les extensions, extension de sauvegarde ont besoin d'accéder à l'internet public à utiliser. Ne pas avoir accès à l'internet public peut se manifester de différentes façons :

- L'installation de l'extension peut échouer.
- Les opérations de sauvegarde (comme disque snapshot) peuvent échouer.
- Affichage de l'état de l'opération de sauvegarde peut échouer.

La nécessité pour la résolution des adresses internet publique a été articulée [ici](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Vous devez vérifier la configuration DNS pour le réseau virtuel et vérifiez que les URI Azure peuvent être résolu.

Une fois que la résolution de noms a été effectuée correctement, l'accès aux adresses IP Azure doit également être fourni. Pour débloquer l'accès à l'infrastructure Azure, procédez comme suit :

1. Obtenir la liste des [Centre de données Azure IPs]()https://msdn.microsoft.com/
2. / library/azure/dn175718.aspx) à la liste d'autorisation.
2. Débloquer les adresses IP à l'aide du [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) applet de commande. Exécutez cette applet de commande dans la machine virtuelle Azure, dans une fenêtre PowerShell avec élévation de privilèges (exécuter en tant qu'administrateur).


## Cohérence des points de récupération
Lorsque vous traitez des données de sauvegarde, clients vous soucier du comportement de l'ordinateur virtuel après que qu'il a été restaurée. Les questions habituelles des clients sont :

- La machine virtuelle va-t-elle démarrer ?
- Les données seront-elles disponibles sur le disque (ou) des données seront-elles perdues ?
- L’application sera-t-elle en mesure de lire les données (ou) les données seront-elles endommagées ?
- L’application pourra-t-elle lire les données (ou) les données seront-elles cohérentes lorsqu’elles seront lues par l’application ?

Le tableau ci-dessous décrit les types de cohérence rencontrés pendant la restauration et la sauvegarde d’une machine virtuelle Azure :

| Cohérence | En fonction du service VSS | Explication et détails |
|-------------|-----------|---------|
| Cohérence des applications | Oui | C’est l’endroit idéal pour les charges de travail Microsoft, car il garantit :<ol><li> que la machine virtuelle *démarre* <li>que les données ne sont *pas endommagées*, <li>qu’il n’y a pas de *perte de données* et<li> que les données sont cohérentes vis-à-vis de l’application qui les utilise, en impliquant l’application au moment de la sauvegarde, à l’aide de VSS.</ol> Le service VSS (Volume Shadow Copy Service) garantit que les données sont correctement écrites dans le stockage. La plupart des charges de travail de Microsoft ont des enregistreurs VSS qui effectuent des actions de charges de travail spécifiques relatives à la cohérence des données. Par exemple, Microsoft SQL Server dispose d’un enregistreur VSS qui garantit que les écritures dans le journal des transactions et de la base de données sont effectuées correctement.<br><br> Pour la sauvegarde d’une machine virtuelle Azure, l’obtention d’un point de récupération cohérent signifie que l’extension de sauvegarde a pu appeler le flux de travail VSS et se terminer *correctement* avant la prise de l’instantané de la machine virtuelle. Bien entendu, cela signifie que les enregistreurs VSS de toutes les applications dans la machine virtuelle Azure ont été également appelés.<br><br>Découvrez les[principes de base du service VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), puis approfondissez vos connaissances sur [son fonctionnement](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Cohérence du système de fichiers | Oui : pour les machines Windows | Il existe deux scénarios où le point de récupération peut être cohérent avec le système de fichiers :<ul><li>En cas de sauvegarde de machines virtuelles Linux dans Azure, Linux n’ayant pas une plateforme équivalente à VSS.<li>En cas d’échec du service VSS lors de la sauvegarde de machines virtuelles Windows dans Azure.</li></ul> Dans ces deux cas, la meilleure solution consiste à s’assurer : <ol><li> que la machine virtuelle *démarre* <li>que les données *ne sont pas endommagées* et <li>qu’il n’y a pas *de perte de données*.</ol> Les applications doivent implémenter leur propre mécanisme de « correctif » sur les données restaurées.|
| Cohérence en cas d’incident | Non | Cette situation est la même que lorsqu’une machine rencontre un « incident » (via une réinitialisation matérielle ou logicielle). Il n’existe aucune garantie de cohérence des données sur le support de stockage. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées. <ol><li>Même s’il n’existe aucune garantie, dans la plupart des cas, le système d’exploitation démarre.<li>Ce démarrage est généralement suivi d’une procédure de vérification du disque comme chkdsk permettant de résoudre les erreurs d’endommagement.<li> Les données ou les écritures en mémoire qui n’ont pas été complètement transférées sur le disque seront perdues.<li> Si une restauration de données est nécessaire, l’application suit généralement son propre mécanisme de vérification. </ol>Pour la sauvegarde de machine virtuelle Azure, l’obtention d’un point de récupération cohérent suite à un incident signifie que Microsoft Azure Backup ne fournit aucune garantie de cohérence des données sur le stockage, au niveau du système d’exploitation ou de l’application. Cela se produit généralement lorsque la machine virtuelle Azure est arrêtée au moment de la sauvegarde.<br><br>Par exemple, cette situation peut survenir si le journal des transactions comporte des entrées qui n’existent pas dans la base de données. Le logiciel de base de données effectue alors une restauration jusqu’à ce que les données soient cohérentes. Lorsque vous traitez des données réparties sur plusieurs disques virtuels (comme des volumes fractionnés), un point de récupération cohérent après incident ne fournit aucune garantie quant à l’exactitude des données.|

## Étapes suivantes
Pour plus d’informations sur la prise en main de Microsoft Azure Backup, consultez :

- [Restauration des machines virtuelles](backup-azure-restore-vms.md)
- [Gérer des ordinateurs virtuels](backup-azure-manage-vms)

<!---HONumber=GIT-SubDir--> 