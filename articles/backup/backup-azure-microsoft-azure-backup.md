<properties
  pageTitle="Préparation de la sauvegarde des charges de travail sur Microsoft Azure | Microsoft Azure"
  description="Cet article fournit une présentation des capacités de charge de travail d’application dans Microsoft Azure Backup"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"
  ms.workload="storage-backup-recovery"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/07/2015"
  ms.author="sammehta; jimpark"/>

# Préparation de la sauvegarde des charges vers Microsoft Azure

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure Backup](backup-azure-microsoft-azure-backup.md)

Cet article concerne la préparation de votre environnement à la sauvegarde des charges de travail, donc, vous pouvez sauvegarder vos données sur Azure. Avec le serveur Microsoft Azure Backup, vous pouvez protéger des charges de travail d’applications telles que les machines virtuelles Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange et les clients Windows vers :

- **Disque** (D2D), offrant les RTO pour les charges de travail de niveau 1
- **Azure** (D2D2C) pour une conservation à long terme

Vous pouvez gérer la protection de différentes entités protégées (serveurs et clients) à partir d’une interface utilisateur sur site.

>[AZURE.NOTE]Le serveur Microsoft Azure Backup hérite des fonctionnalités de Data Protection Manager (DPM) pour la sauvegarde de charge de travail. Vous trouverez des liens vers la documentation DPM pour certaines de ces fonctionnalités. Cependant, Microsoft Azure Backup ne fournit pas de protection sur bande et ne s’intègre pas à System Center.

Vous pouvez déployer le serveur Microsoft Azure Backup en tant que :

- serveur physique autonome.
- machine virtuelle Hyper-V. Vous pouvez exécuter Microsoft Azure Backup Server en tant que machine virtuelle hébergée sur un serveur hôte Hyper-V local afin de sauvegarder des données locales. Pour obtenir la liste des éléments à prendre en compte dans cet environnement, consultez la section [Installation de DPM en tant que machine virtuelle sur un serveur Hyper-V local](https://technet.microsoft.com/library/dn440941.aspx).
- machine virtuelle Windows dans un environnement VMware : vous pouvez déployer Microsoft Azure Backup Server afin de fournir une protection aux charges de travail Microsoft s’exécutant sur des machines virtuelles Windows dans VMware. Dans ce scénario, Microsoft Azure Backup Server peut être déployé en tant que serveur physique autonome, en tant que machine virtuelle Hyper-V ou en tant que machine virtuelle Windows dans VMware.
- machine virtuelle Azure : vous pouvez exécuter Microsoft Azure Backup Server en tant que machine virtuelle dans Azure pour enregistrer des charges de travail cloud en cours d’exécution en tant que machines virtuelles Azure. Pour plus d’informations sur ce déploiement, consultez [Installation de DPM en tant que machine virtuelle Azure](https://technet.microsoft.com/library/hh758176.aspx).

De plus,

- Microsoft Azure Backup peut être installé sur Windows Server 2008 R2 SP1, 2012 et 2012 R2.
- Le serveur Microsoft Azure Backup ne peut pas être installé sur une machine sur laquelle l’agent SCDPM ou SCDPM RA est installé.
- Le serveur Microsoft Azure Backup ne peut pas être installé sur un ordinateur équipé de l’agent Microsoft Azure Backup installé et enregistré avec un coffre de sauvegarde Azure.



Une fois que vous avez sélectionné le serveur sur lequel vous souhaitez installer Microsoft Azure Backup, vous devez :

- vous assurer que les conditions préalables du serveur Microsoft Azure Backup sont remplies ,
- créer un coffre de sauvegarde ,
- télécharger les informations d’identification de coffre et
- télécharger les fichiers d’installation du serveur Microsoft Azure Backup.

Chacune de ces opérations est décrite en détail ci-dessous.

## Avant de commencer

Aujourd’hui, le serveur Microsoft Azure Backup server est disponible pour les paramètres régionaux anglais uniquement. Actuellement, Microsoft Azure Recovery Services est disponible dans toutes les zones géographiques où Azure est disponible, excepté le centre de données de Microsoft Azure gouvernemental et Microsoft Azure en Chine.

Pour une installation sans heurts, vérifiez que les conditions préalables suivantes sont remplies avant d’installer le serveur de sauvegarde Microsoft Azure.

- Le serveur doit disposer d’une connectivité à Internet. Microsoft Azure doit être accessible par le biais du serveur.
- La configuration serveur requise pour l’installation de Microsoft Azure Backup est la même que DPM. Consultez la section [configuration matérielle requise](https://technet.microsoft.com/library/dn726764.aspx) pour plus d’informations.
- Le serveur Microsoft Azure Backup doit être joint au domaine.
- Les fonctions .Net 3.5, .Net 4.0 et Net 3.5 SP1 doivent être installées sur le serveur Microsoft Azure Backup. Pour plus d’informations, voir [Autres informations sur l’activation de .Net Framework](https://technet.microsoft.com/library/dn482071.aspx).
- Windows Management Framework 4.0 doit être installé sur le serveur Microsoft Azure Backup. Vous pouvez le télécharger [ici](http://www.microsoft.com/download/details.aspx?id=40855).
- Pour le disque utilisé comme espace dédié pour le stockage de données Microsoft Azure Backup Server, la taille du pool de stockage recommandée est de 1,5 fois le volume des données protégées. Pour plus d’informations, consultez la section disques et stockage de [cette rubrique](https://technet.microsoft.com/library/hh758176.aspx#DPM serveur).

Préparer le serveur Microsoft Azure Backup pour sauvegarder les données en :

1. **Créant un coffre de sauvegarde** : créez un coffre dans la console Azure Backup.
2. **Téléchargeant les informations d’identification de l’archivage** : dans Azure Backup, téléchargez le certificat de gestion que vous avez créé dans le coffre.
3. **Téléchargeant le serveur Microsoft Azure Backup** : vous pouvez télécharger le serveur Microsoft Azure Backup pour les charges de travail d’application de la page de démarrage rapide d’un coffre de sauvegarde.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

## Télécharger le serveur Microsoft Azure Backup.
Vous pouvez télécharger Microsoft Azure Backup pour les charges de travail d’application depuis la **page de démarrage rapide** d’un coffre de sauvegarde au même titre que les informations d’identification.

1. Cliquez sur **Pour les charges de travail d’application (Disque à Disque vers Cloud)**.

    ![Écran d’accueil Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. Cliquez sur **Télécharger**.

    ![Centre de téléchargement 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. Sélectionnez tous les fichiers, puis cliquez sur **Suivant**. ![Centre de téléchargement 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE]Puisque la taille de téléchargement de l’ensemble des fichiers est > 3G, sur un lien de téléchargement de 10 Mbits/s, le téléchargement peut prendre jusqu’à 60 minutes.

4. Placez tous les fichiers dans le même dossier.

5. Téléchargez tous les fichiers en provenance de la page de téléchargement Microsoft Azure Backup.

## Installer le serveur Microsoft Azure Backup
Assurez-vous que les conditions préalables mentionnées dans la section qui précède sont remplies avant de lancer le programme d’installation.

1. Une fois que vous avez téléchargé tous les fichiers, cliquez sur **MicrosoftAzureBackupInstaller.exe**. L’**Assistant Installation de Microsoft Azure Backup** s’affiche.

    ![Logiciel d’installation Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. Indiquez l’emplacement sur le serveur sur lequel les fichiers seront extraits, puis cliquez sur **Suivant**.

    ![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. Cliquez sur **Extraire** pour commencer l’extraction des fichiers d’installation.

    ![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. Cochez la case pour lancer setup.exe et lancer l’installation du serveur Microsoft Azure Backup et cliquez sur **Terminer**.

    ![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. Cliquez sur **Microsoft Azure Backup** pour lancer l’Assistant d’installation.

    ![Assistant Installation de Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. Dans l’écran d’accueil, cliquez sur **Suivant**.

    ![Écran d’accueil Microsoft Azure Backup](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. Cliquez sur **Vérifier** pour déterminer si les configurations matérielle et logicielle requises pour le serveur Microsoft Azure Backup sont respectées.

    ![Microsoft Azure Backup PreReq1](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. Si toutes les conditions préalables ont été satisfaites, un message indiquant que l’ordinateur répond à la configuration requise s’affichera. Cliquez sur **Next**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. Le serveur Microsoft Azure Backup exige SQL Server Standard. Sélectionnez une instance locale de SQL Server Standard 2014 existante ou laissez l’assistant installer SQL Server Standard. Cliquez sur **Vérifier et installer** pour vous assurer que les éléments prérequis SQL sont installés.

    ![Microsoft Azure Backup SQL1](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    Si une défaillance se produit et qu’il est conseillé de redémarrer la machine, faites-le, puis cliquez sur **Vérifier à nouveau**.

    ![Défaillance du filtre SiS](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. Une fois les composants requis installés, cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. Fournissez un emplacement pour l’installation des fichiers de serveur Microsoft Azure Backup, puis cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE]Un emplacement temporaire est requis pour la sauvegarde sur Azure. Vérifiez que l’emplacement temporaire correspond à au moins 5 % du volume qu’il est prévu de sauvegarder dans le cloud. Pour la protection de disque, des disques séparés doivent être séparés une fois l’installation terminée. Pour plus d’informations sur les pools de stockage, consultez [Configurer des pools de stockage et de stockage sur disque](https://technet.microsoft.com/library/hh758075.aspx).

12. Fournissez un mot de passe fort pour les comptes utilisateur locaux restreints et cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE]Des mots de passe forts sont essentiels pour la sécurité de votre système. Un mot de passe fort est un mot de passe comportant au moins six caractères, ne comportant tout ou partie du nom du compte utilisateur et contenant au moins trois des quatre catégories de caractères suivantes : caractéristiques en majuscules, caractères minuscules, chiffres en base 10 et symboles (tels que !, @, #).

13. Indiquez si vous souhaitez utiliser *Microsoft Update* pour vérifier les mises à jour et cliquez sur **Suivant**.

    >[AZURE.NOTE]Nous conseillons de faire en sorte que Windows Update soit redirigé vers Microsoft Update, qui offre une sécurité et des mises à jour importantes pour Windows et autres produits, tels que le serveur Microsoft Azure Backup.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. Examinez le *Résumé des paramètres* et cliquez sur **Installer**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    L’installation se déroule en plusieurs phases. Lors de la première phase, un agent est installé sur le serveur et vous devez inscrire le serveur avec les informations d’identification du coffre de sauvegarde Azure valide que vous avez téléchargé avec ce produit. L’Assistant vérifie ensuite la connectivité directe à Internet. Si la connectivité à Internet est disponible, vous pouvez poursuivre l’installation, sinon, vous devez fournir les informations de proxy détaillées pour se connecter à Internet.

15. Cliquez sur **Suivant** pour lancer la phase d’installation de l’Agent Microsoft Azure Recovery Services.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/proxy.png)

    L’assistant vérifie les conditions préalables et installe des éléments s’il en manque certains.

16. Cliquez sur **Installer**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. Une fois l’installation de l’agent terminée, cliquez sur **Suivant** pour enregistrer ce serveur avec le coffre de sauvegarde Azure.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. Fournissez les informations d’identification du coffre de sauvegarde Azure pour inscrire le serveur, puis cliquez sur **Suivant**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. Fournir une phrase secrète utilisée pour chiffrer/déchiffrer les données envoyées à Azure et un emplacement pour stocker cette phrase secrète. Vous pouvez automatiquement générer une phrase secrète ou fournir votre propre phrase secrète d’au minimum 16 caractères. Cliquez sur **Next**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE]Pour conserver la confidentialité, le serveur Microsoft Azure Backup n’envoie pas cette phrase secrète avec les données vers Azure. Vous devez stocker cette phrase secrète, qui vous servira lorsque vous restaurerez des données d’Azure, dans un emplacement sécurisé.

    Une fois l’inscription du serveur Microsoft Azure Backup terminée avec succès, le programme procède à l’installation et à la configuration de SQL Server 2014 Standard (s’il est sélectionné).

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    Une fois l’installation de SQL terminée, les composants de serveur Microsoft Azure Backup sont installés.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. Lorsque le produit est installé et les icônes du bureau ont été créées, double-cliquez sur l’icône pour lancer le produit.

    ![Icônes](./media/backup-azure-microsoft-azure-backup/icons.png)

## Ajouter un disque au pool de stockage

Pour sauvegarder des charges de travail sur le disque et Azure, vous devez d’abord ajouter un disque au pool de stockage. Pour plus d’informations sur l’ajout de disques, consultez la section [Configurer des pools de stockage et un disque de stockage](https://technet.microsoft.com/library/hh758075.aspx).

Une fois les pools de stockage configurés, vous pouvez sauvegarder des charges de travail d’application sur les deux disques et Azure.

## Résolution de problèmes

Si le serveur Microsoft Azure Backup échoue avec des erreurs pendant la phase d’installation (ou de sauvegarde ou restauration), reportez-vous à ce [document de codes d’erreur](https://support.microsoft.com/kb/3041338) pour plus d’informations. Vous pouvez également vous reporter au [FAQ relatives à la sauvegarde Azure](backup-azure-backup-faq.md)

## FAQ

### Installation et déploiement

Q : La compression NTFS est-elle prise en charge sur les disques/volumes de serveur Microsoft Azure Backup utilisés pour les sauvegardes sur disque ? <br>R : NTFS n’est **pas** pris en charge sur des volumes/disques attachés au serveur Microsoft Azure Backup.

Q : Puis-je transférer le serveur Microsoft Azure Backup vers un nouveau domaine post-déploiement ? <br>Q : Non, le transfert d’un serveur Microsoft Azure Backup vers un nouveau domaine après déploiement **n’est pas** pris en charge.

Q : Quelles ressources le serveur Microsoft Azure Backup peut-il protéger s’il est installé sur un contrôleur de domaine ? <br>R : Le serveur Microsoft Azure Backup ne peut protéger que les sources de données locales installées sur le même serveur en tant que contrôleur de domaine. Pour que le serveur Microsoft Azure Backup protège les charges de travail sur d’autres serveurs, ne l’installez pas sur le même ordinateur en tant que contrôleur de domaine.

Q : Le serveur de Microsoft Azure Backup peut-il utiliser une instance de serveur SQL Server distant ? <br>R : Le serveur Microsoft Azure Backup ne peut utiliser qu’une instance de serveur SQL Server locale ?

### Pool de stockage de serveur Microsoft Azure Backup

Q : Puis-je fournir un disque virtuel (VHD/HDx) dans le pool de stockage de serveur Microsoft Azure Backup ? <br>R : Oui.

Q : Puis-je effectuer une déduplication sur un pool de stockage de serveur Microsoft Azure Backup ? <br>R : Oui, la déduplication sur un pool de stockage de serveur Microsoft Azure Backup est disponible. L’expérience utilisateur du serveur Microsoft Azure Backup est parfaitement conforme aux détails fournis dans ce [billet de blog DPM](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx).

Q : Est-il possible d’utiliser des périphériques USB ou amovibles comme pool de stockage de serveur Microsoft Azure Backup ? <br>R : Non.

### Connectivité serveur Microsoft Azure Backup au service Azure

Q : En quoi la connectivité de serveur Microsoft Azure Backup au service Azure et l’état de l’abonnement Azure affectent-ils les sauvegardes et la restauration ? <br>R : le serveur Microsoft Azure Backup doit être connecté au service Azure pour que le produit fonctionne correctement. Dans le même temps, l’abonnement Azure doit être dans un état correct.

Le tableau ci-dessous explique les fonctionnalités du serveur Microsoft Azure Backup par rapport à la connectivité et aux états d’un abonnement Azure.

| État de la connectivité | Abonnement Azure | Sauvegarde sur Azure| Sauvegarde sur disque | Restauration à partir d’Azure | Restauration à partir d’un disque |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| Connecté | Actif | Autorisé | Autorisé | Autorisé | Autorisé |
| Connecté | Expiré | Arrêté | Arrêté | Autorisé | Autorisé |
| Normal | Approvisionnement annulé | Arrêté | Arrêté | Arrêté et points de restauration Azure supprimés | Arrêté |
| Connectivité perdue depuis > 15 jours | Actif | Arrêté | Arrêté | Autorisé | Autorisé |
| Connectivité perdue depuis > 15 jours | Expiré | Arrêté | Arrêté | Autorisé | Autorisé |
| Connectivité perdue depuis > 15 jours | Approvisionnement annulé | Arrêté | Arrêté | Arrêté et points de restauration Azure supprimés | Arrêté |

Q : En supposant que l’état de l’abonnement continue d’être *Actif*, que se passe-t-il si la connectivité du serveur Microsoft Azure Backup est restaurée ? <br>R : Une fois que la connectivité du serveur Microsoft Azure Backup vers le service Azure est rétablie comme normale et si l’état de l’abonnement est *Actif*, toutes les opérations du serveur Microsoft Azure Backup sont autorisées. Les sauvegardes sur les deux disques et sur Azure s’exécuteront normalement.

Q : Que se passe-t-il si l’état de l’abonnement est restauré de l’état *Expiré* à l’état *Actif* ? <br>R : Si l’on suppose que la connectivité du serveur Microsoft Azure Backup vers le service Azure est normale, une fois que l’état de l’abonnement du serveur Microsoft Azure Backup est restauré sur *Actif*, toutes les opérations du serveur Microsoft Azure Backup sont autorisées. Les sauvegardes sur les deux disques et sur Azure s’exécuteront désormais normalement.

Q : Que se passe-t-il si l’état de l’abonnement est restauré de l’état *Approvisionnement annulé* à l’état *Actif* ? <br>R : Si l’on suppose que la connectivité du serveur Microsoft Azure Backup vers le service Azure est normale, une fois que l’état de l’abonnement du serveur Microsoft Azure Backup est restauré sur *Actif*, toutes les opérations du serveur Microsoft Azure Backup sont autorisées. Toutefois, les points de récupération Azure sont supprimés et il est impossible de les restaurer. Les points de récupération de disque peuvent être restaurés si les sauvegardes sur disque se situent dans une plage de conservation valide.

Q : De quelles exceptions ai-je besoin pour autoriser la connectivité du serveur Microsoft Azure Backup à un service public Azure ? <br>R : Vous devez autoriser les adresses de domaine suivantes dans le profil de pare-feu - www.msftncsi.com, *. Microsoft.com, *. WindowsAzure.com, *. microsoftonline.com, *. windows.net.

Q : Comment puis-je confirmer que le serveur Microsoft Azure Backup dispose d’une connectivité au service Azure ? <br>R : Exécutez l’applet de commande PowerShell suivante dans la console du serveur Microsoft Azure Backup :

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

Q : Pourquoi l’état de mon abonnement n’est-il pas *Actif* ? Comment puis-je le remettre sur *Actif* ? <br>R : Connectez-vous au [portail d’abonnement](https://account.windowsazure.com/Subscriptions) et gérez votre abonnement.

### Facturation

Q : Quel est le modèle de facturation pour le serveur Microsoft Azure Backup ? <br>R : Les utilisateurs seront facturés via un modèle d’instance protégée. Pour plus d’informations, consultez le Forum aux questions sur la page [Tarification](http://azure.microsoft.com/pricing/details/backup/).

Q : Quel est le modèle de facturation si seul le disque contient des données protégées ? <br>R : Le modèle de facturation est le même que le modèle d’instance protégé. Dans la mesure où ces données sont protégées sur l’espace de stockage local, aucun frais de stockage Azure n’est facturé pour les sauvegardes sur disque uniquement. Dans ce cas, seuls les frais d’instance protégée doivent être facturés au client. Pour plus d’informations sur ce qui définit une instance et le montant facturé par instance, consultez le Forum aux questions sur la [Tarification ](http://azure.microsoft.com/pricing/details/backup/).

Q : Quel est le coût pour chaque instance protégée ? <br>R : Reportez-vous à la page [Tarification](http://azure.microsoft.com/pricing/details/backup/).

Q : Où puis-je trouver des exemples mettant en évidence les différentes charges de travail d’application et les frais d’instance protégée associés ? <br>R : Reportez-vous à la « section Exemples » sur la page [tarification](http://azure.microsoft.com/pricing/details/backup/).

Q : Comment le serveur Microsoft Azure Backup facture-t-il une source de données protégée sur disque ainsi que sur le cloud ? <br>R : Que les données soient sauvegardées sur disque ou sur le cloud, le serveur de sauvegarde Microsoft Azure établira sa facturation en fonction des instances protégées. La taille de l’instance protégée est calculée en fonction de la taille du serveur frontal de la source de données. Pour les données de sauvegarde dans le stockage Azure, les coûts de stockage Azure s’appliquent également.

## Étapes suivantes

Vous pouvez utiliser ces articles pour apprendre à mieux connaître la notion de protection de charge de travail à l’aide du serveur Microsoft Azure Backup.

- [Sauvegarde SQL Server](backup-azure-backup-sql.md)
- [Sauvegarde de serveur SharePoint](backup-azure-backup-sharepoint.md)
- [Sauvegarde sur un autre serveur](backup-azure-alternate-dpm-server.md)

<!---HONumber=Oct15_HO3-->