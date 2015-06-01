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
	ms.topic="article"
	ms.date="04/30/2015"
	ms.author="aashishr"/>


# Sauvegarde des machines virtuelles Azure

Cet article est un guide essentiel pour la sauvegarde des machines virtuelles. Avant de continuer, vérifiez que toutes les [conditions préalables][prereq] ont été remplies.

Les trois principales étapes de la sauvegarde des machines virtuelles sont les suivantes :

![Trois étapes pour sauvegarder une machine virtuelle Azure][three-steps]

## Découverte des machines virtuelles Azure
Le processus de découverte interroge Azure pour obtenir la liste des machines virtuelles de l'abonnement et des informations supplémentaires, comme le nom du service cloud et la région.

> [AZURE.NOTE]Ce processus doit toujours être exécuté en premier. En effet, il permet de vérifier que les nouvelles machines virtuelles ajoutées à l’abonnement sont identifiées.

Pour déclencher le processus de découverte, procédez comme suit :

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.

2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure**, puis cliquez sur le bouton **Sélectionner**. ![sélectionner la charge de travail][select-workload]

3. Cliquez sur le bouton **Découvrir** en bas de la page. ![bouton découverte][discover-button]

4. Le processus de découverte peut s’exécuter pendant quelques minutes tandis que les machines virtuelles sont affichées sous forme de tableau. Une notification toast s’affiche en bas de l’écran pendant l’exécution du processus de découverte. ![découvrir des machines virtuelles][discover-vms]

5. Une fois le processus de découverte terminé, une autre notification toast s’affiche. ![découverte terminée][discover-done]

<br><br>
## Inscription des machines virtuelles Azure
Pour protéger une machine virtuelle, cette dernière doit être inscrite auprès du service Azure Backup. Le processus d’inscription a deux objectifs principaux :

1. Connecter l’extension de sauvegarde à l’agent de machine virtuelle dans la machine virtuelle

2. Associer la machine virtuelle au service Azure Backup.

L’inscription est généralement une activité unique. Le service Azure Backup gère de façon transparente la mise à niveau et la correction de l’extension de sauvegarde sans aucune intervention fastidieuse pour l’utilisateur. Cela évite à l’utilisateur la « surcharge de gestion des agents » généralement associée aux produits de sauvegarde.

### Inscription des machines virtuelles

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.

2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure**, puis cliquez sur le bouton Sélectionner. ![sélectionner la charge de travail][select-workload]

3. Cliquez sur le bouton **Inscrire** en bas de la page. ![bouton inscription][register-button]

4. Dans la fenêtre contextuelle **Inscrire les éléments** sélectionnez les machines virtuelles que vous souhaitez inscrire. Si au moins deux machines virtuelles portent le même nom, utilisez le service cloud pour les distinguer.

  	L’opération **Inscrire** peut être effectuée à grande échelle, ce qui signifie que plusieurs machines virtuelles peuvent être sélectionnées en même temps pour l’inscription. Cela réduit considérablement l’opération unique de préparation de la machine virtuelle pour la sauvegarde.

  	> [AZURE.NOTE]Seules les machines virtuelles qui ne sont pas inscrites et se trouvent dans la même région que l’archivage de sauvegarde sont affichées ici.

  	![Liste de machines virtuelles à inscrire][register-vms]

5. Un travail est créé pour chaque machine virtuelle à inscrire. La notification toast indique l’état de cette activité. Cliquez sur **Afficher le travail** pour accéder à la page **Travaux**. ![inscrire le travail][register-job]

6. La machine virtuelle est également affichée dans la liste des éléments inscrits et l’état de l’opération d’inscription s’affiche ![État de l’inscription 1][register-status1]

7. Une fois l’opération terminée, l’état affiché dans le portail change pour refléter l’état inscrit. ![État de l’inscription 2][register-status2]

<br><br>
## Sauvegarde des machines virtuelles Azure
Cette étape implique la configuration d’une stratégie de sauvegarde et de rétention pour la machine virtuelle. Pour protéger une machine virtuelle, procédez comme suit :

1. Accédez à l’archivage de sauvegarde qui se trouve sous **Recovery Services** dans le portail Azure, puis cliquez sur l’onglet **Éléments inscrits**.

2. Choisissez le type de charge de travail dans le menu déroulant en tant que **Machine virtuelle Azure**, puis cliquez sur le bouton Sélectionner. ![Sélectionner la charge de travail dans le portail][select-workload]

3. Cliquez sur le bouton **Protéger** en bas de la page.

4. Un Assistant **Protection des éléments** s’affiche, où les machines virtuelles à protéger peuvent être sélectionnées. Si au moins deux machines virtuelles portent le même nom, utilisez le service cloud pour les distinguer. L’opération **Protéger** peut être effectuée à grande échelle, ce qui signifie que plusieurs machines virtuelles peuvent être sélectionnées en même temps pour l’inscription. Cela réduit considérablement les opérations nécessaires pour la protection de la machine virtuelle.

	> [AZURE.NOTE]Seules les machines virtuelles correctement inscrites avec le service Azure Backup et situées dans la même région que l’archivage de sauvegarde sont affichées ici.

	![Sélectionner les éléments à protéger][protect-wizard1]

5. Dans le deuxième écran de l’Assistant **Protection des éléments**, choisissez une stratégie de sauvegarde et de rétention pour sauvegarder les machines virtuelles sélectionnées. Sélectionnez un ensemble de stratégies existant ou définissez-en un nouveau.

	> [AZURE.NOTE]Pour la version préliminaire, la durée maximale de rétention va jusqu’à 30 jours et le nombre de sauvegardes maximum par jour est de 1.

	![Sélectionner la stratégie de protection][protect-wizard2]

	Dans chaque archivage de sauvegarde, vous pouvez avoir plusieurs stratégies de sauvegarde. Ces stratégies reflètent les informations détaillées sur la planification et la conservation de la sauvegarde. Par exemple, une stratégie de sauvegarde peut être définie pour une sauvegarde quotidienne à 22h00, alors qu’une autre stratégie de sauvegarde peut être définie pour une sauvegarde hebdomadaire à 06h00. Plusieurs stratégies de sauvegarde améliorent la flexibilité de planification de sauvegardes pour l’infrastructure de votre machine virtuelle. Vous pouvez associer plusieurs machines virtuelles à chaque stratégie de sauvegarde. Vous ne pouvez associer votre machine virtuelle qu’à une seule stratégie à un moment donné.

6. Un travail est créé pour chaque machine virtuelle afin de configurer la stratégie de protection et d’associer les machines virtuelles à la stratégie. Cliquez sur l’onglet **Travaux** et sélectionnez le filtre approprié pour afficher la liste des travaux **Configurer la protection**. ![Configurer le travail de protection][protect-configure]

7. Lorsque vous avez terminé, les machines virtuelles sont protégées par une stratégie et doivent attendre jusqu’à ce que le délai de sauvegarde prévu pour la sauvegarde initiale se termine. La machine virtuelle apparaît désormais sous l’onglet **Éléments protégés**, tandis que leur état est défini sur *Protégée* (en attente de la sauvegarde initiale).

	> [AZURE.NOTE]Le démarrage de la sauvegarde initiale immédiatement après la configuration de la protection n’est pas disponible pour le moment.

8. À l’heure planifiée, le service Azure Backup crée un travail de sauvegarde pour chaque machine virtuelle à sauvegarder. Cliquez sur l’onglet **Travaux** pour afficher la liste des travaux **Backup**. Dans le cadre de l’opération de sauvegarde, le service Azure Backup émet une commande vers l’extension de sauvegarde de chaque machine virtuelle pour vider toutes les écritures et prendre un instantané cohérent. ![Sauvegarde en cours][protect-inprogress]

9. Une fois la commande exécutée, l’état de protection de la machine virtuelle sous l’onglet **Éléments protégés** s’affiche en tant que *protégé*. ![La machine virtuelle est sauvegardée avec un point de récupération][protect-backedup]


## Affichage de l’état et des détails de la sauvegarde
Une fois la protection appliquée, le nombre de machines virtuelles augmente également sur la page de synthèse nommée **Tableau de bord**. Cette page affiche également le nombre de travaux à partir des dernières 24 heures ayant réussi, échoué et toujours en cours. Cliquez sur n’importe quelle catégorie pour l’examiner plus en détail dans la page **Travaux**. ![État de la sauvegarde sur la page Tableau de bord][dashboard]


## Résolution des erreurs
Les informations suivantes vous aideront à résoudre les problèmes de découverte et d’inscription.

| Opération de sauvegarde | Détails de l’erreur | Solution de contournement |
| -------- | -------- | -------|
| Découverte | Échec de la découverte de nouveaux éléments : Microsoft Azure Backup a rencontré une erreur interne. Patientez quelques minutes et recommencez l’opération. | Recommencez le processus de découverte après 15 minutes.
| Découverte | Échec de la découverte de nouveaux éléments : une autre opération de découverte est déjà en cours. Veuillez patienter jusqu’à la fin de l’opération de découverte en cours. | Veuillez attendre la fin de l’opération de découverte en cours. |
| S’inscrire | Le rôle de machine virtuelle Azure n’est pas en état pour installer l’extension : veuillez vérifier si la machine virtuelle est en cours d’exécution. L’extension Azure Recovery Services requiert l’exécution de la machine virtuelle. | Démarrez la machine virtuelle et, lorsqu’elle est en cours d’exécution, recommencez l’opération d’inscription.|


## Cohérence des points de récupération
Lorsque vous traitez des données de sauvegarde, l’une des principales préoccupations des clients concerne le comportement de la machine virtuelle après sa restauration. Les questions habituelles des clients sont :

+ La machine virtuelle va-t-elle démarrer ?

+ Les données seront-elles disponibles sur le disque (ou) des données seront-elles perdues ?

+ L’application sera-t-elle en mesure de lire les données (ou) les données seront-elles endommagées ?

+ L’application pourra-t-elle lire les données (ou) les données seront-elles cohérentes lorsqu’elles seront lues par l’application ?

Le tableau ci-dessous décrit les types de cohérence rencontrés pendant la restauration et la sauvegarde d’une machine virtuelle Azure :

| Cohérence | En fonction du service VSS | Explication et détails |
|-------------|-----------|---------|
| Cohérence des applications | Oui | C’est l’endroit idéal pour les charges de travail Microsoft, car il garantit :<ol><li> que la machine virtuelle *démarre* <li>que les données ne sont *pas endommagées*, <li>qu’il n’y a pas de *perte de données* et<li> que les données sont cohérentes vis-à-vis de l’application qui les utilise, en impliquant l’application au moment de la sauvegarde, à l’aide de VSS.</ol> Le service VSS (Volume Shadow Copy Service) garantit que les données sont correctement écrites dans le stockage. La plupart des charges de travail de Microsoft ont des enregistreurs VSS qui effectuent des actions de charges de travail spécifiques relatives à la cohérence des données. Par exemple, Microsoft SQL Server dispose d’un enregistreur VSS qui garantit que les écritures dans le journal des transactions et de la base de données sont effectuées correctement.<br><br> Pour la sauvegarde d’une machine virtuelle Azure, l’obtention d’un point de récupération cohérent signifie que l’extension de sauvegarde a pu appeler le flux de travail VSS et se terminer *correctement* avant la prise de l’instantané de la machine virtuelle. Bien entendu, cela signifie que les enregistreurs VSS de toutes les applications dans la machine virtuelle Azure ont été également appelés.<br><br>Découvrez les[principes de base du service VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx), puis approfondissez vos connaissances sur [son fonctionnement](https://technet.microsoft.com/fr-fr/library/cc785914%28v=ws.10%29.aspx). |
| Cohérence du système de fichiers | Oui : pour les machines Windows | Il existe deux scénarios où le point de récupération peut être cohérent avec le système de fichiers :<ul><li>En cas de sauvegarde de machines virtuelles Linux dans Azure, Linux n’ayant pas une plateforme équivalente à VSS.<li>En cas d’échec du service VSS lors de la sauvegarde de machines virtuelles Windows dans Azure.</li></ul> Dans ces deux cas, la meilleure solution consiste à s’assurer : <ol><li> que la machine virtuelle *démarre* <li>que les données *ne sont pas endommagées* et <li>qu’il n’y a pas *de perte de données*.</ol> Les applications doivent implémenter leur propre mécanisme de « correctif » sur les données restaurées.|
| Cohérence en cas d’incident | Non | Cette situation est la même que lorsqu’une machine rencontre un « incident » (via une réinitialisation matérielle ou logicielle). Il n’existe aucune garantie de cohérence des données sur le support de stockage. Seules les données déjà présentes sur le disque au moment de la sauvegarde sont capturées et sauvegardées. <ol><li>Même s’il n’existe aucune garantie, dans la plupart des cas, le système d’exploitation démarre.<li>Ce démarrage est généralement suivi d’une procédure de vérification du disque comme chkdsk permettant de résoudre les erreurs d’endommagement.<li> Les données ou les écritures en mémoire qui n’ont pas été complètement transférées sur le disque seront perdues.<li> Si une restauration de données est nécessaire, l’application suit généralement son propre mécanisme de vérification. </ol>Pour la sauvegarde de machine virtuelle Azure, l’obtention d’un point de récupération cohérent suite à un incident signifie que Microsoft Azure Backup ne fournit aucune garantie de cohérence des données sur le stockage, au niveau du système d’exploitation ou de l’application. Cela se produit généralement lorsque la machine virtuelle Azure est arrêtée au moment de la sauvegarde.<br><br>Par exemple, cette situation peut survenir si le journal des transactions comporte des entrées qui n’existent pas dans la base de données. Le logiciel de base de données effectue alors une restauration jusqu’à ce que les données soient cohérentes. Lorsque vous traitez des données réparties sur plusieurs disques virtuels (comme des volumes fractionnés), un point de récupération cohérent après incident ne fournit aucune garantie quant à l’exactitude des données.|



## Étapes suivantes
Pour plus d’informations sur la prise en main de Microsoft Azure Backup, consultez :

- [Restauration des machines virtuelles](backup-azure-restore-vms.md)

[three-steps]: ./media/backup-azure-vms/3-steps-for-backup.png
[select-workload]: ./media/backup-azure-vms/discovery-select-workload.png
[discover-button]: ./media/backup-azure-vms/discover-button.png
[discover-vms]: ./media/backup-azure-vms/discovering-vms.png
[discover-done]: ./media/backup-azure-vms/discovery-complete.png
[register-button]: ./media/backup-azure-vms/register-button.png
[register-job]: ./media/backup-azure-vms/register-create-job.png
[register-vms]: ./media/backup-azure-vms/register-popup.png
[register-status1]: ./media/backup-azure-vms/register-status01.png
[register-status2]: ./media/backup-azure-vms/register-status02.png
[select-workload]: ./media/backup-azure-vms/select-workload.png
[protect-wizard1]: ./media/backup-azure-vms/protect-wizard1.png
[protect-wizard2]: ./media/backup-azure-vms/protect-wizard2.png
[protect-configure]: ./media/backup-azure-vms/protect-configureprotection.png
[protect-inprogress]: ./media/backup-azure-vms/protect-inprogress.png
[protect-backedup]: ./media/backup-azure-vms/protect-backedupvm.png
[dashboard]: ./media/backup-azure-vms/dashboard-protectedvms.png
[prereq]: http://azure.microsoft.com/documentation/articles/backup-azure-vms-introduction/#prerequisites

<!--HONumber=54-->