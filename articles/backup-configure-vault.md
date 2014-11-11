<properties linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="Configure a Backup Vault" pageTitle="Configure Azure Recovery Services to quickly and easily back-up Windows Server" metaKeywords="disaster recovery" description="Use this tutorial to learn how to use the Backup service in Microsoft's Azure cloud offering to back up Windows Server to the cloud." metaCanonical="" services="recovery-services" documentationCenter="" title="Configure Azure Backup to quickly and easily back-up Windows Server" authors="raynew" solutions="" manager="johndaw" editor="tysonn" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# <span id="configure-a-backup-vault-tutorial"></span></a>Configuration d'Azure Backup pour sauvegarder rapidement et facilement Windows Server

<div class="dev-callout"> 
<strong>Remarque</strong>
 
<p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Ce didacticiel vous guidera dans l'activation de la fonctionnalit&eacute; Azure Backup. Auparavant, vous aviez besoin de cr&eacute;er ou de vous procurer un certificat X.509 v3 afin d'enregistrer votre serveur de sauvegarde. Les certificats sont toujours pris en charge, mais d&eacute;sormais, pour faciliter l'enregistrement d'un coffre Azure avec un serveur, vous pouvez g&eacute;n&eacute;rer un droit d'information d'identification de coffre &agrave; partir de la page de d&eacute;marrage rapide. </p>
<ul> 
<li>Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="/fr-fr/pricing/free-trial/">Version d'&eacute;valuation gratuite d'Azure</a>.</li> 
 

</ul>
 

</div>

Pour sauvegarder des fichiers et données de votre serveur Windows Server vers Azure, vous devez créer un coffre de sauvegarde dans la région géographique où vous souhaitez stocker les données. Ce didacticiel vous guidera lors de la création du coffre de stockage des sauvegardes, du téléchargement d'une information d'identification de coffre et de l'installation de l'agent de sauvegarde. Il vous présentera également les tâches de gestion de sauvegarde disponibles dans le portail de gestion.

## <span id="create"></span></a>Création d'un coffre de sauvegarde

1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  Cliquez sur **New**, puis placez le curseur sur **Data Services**, puis sur **Recovery Services**, puis cliquez sur **Backup Vault**, puis sur **Quick Create**.

3.  Dans **Nom**, entrez un nom convivial permettant d'identifier le coffre de sauvegarde.

4.  Dans **Region**, sélectionnez la région géographique du coffre de sauvegarde.
     ![Nouveau coffre de sauvegarde][Nouveau coffre de sauvegarde]

5.  Cliquez sur **Create vault**.

    La création du coffre de sauvegarde peut prendre du temps. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Lorsque le coffre de sauvegarde est créé, un message vous en informe et le coffre est répertorié dans les ressources de Recovery Services sous le statut **Active**.
    ![création d'un coffre de sauvegarde][création d'un coffre de sauvegarde]

6.  Si vous disposez de plusieurs abonnements associés à votre compte professionnel, choisissez le compte correct à associer avec le coffre de sauvegarde.

## <span id="upload"></span></a>Téléchargement d'une information d'identification de coffre

Les informations d'identification de coffre remplacent les certificats pour l'inscription de votre service Azure auprès de votre serveur. Vous pouvez toujours utiliser des certificats. Toutefois, les informations d'identification de coffre sont plus faciles à utiliser, car vous utilisez le portail Azure pour les générer et les télécharger.

1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  Cliquez sur **Recovery Services**, puis sélectionnez le coffre de sauvegarde que vous souhaitez enregistrer avec un serveur. La page de démarrage rapide pour ce coffre de sauvegarde apparaît.

3.  Sur la page de démarrage rapide, cliquez sur **Download vault credentials** pour afficher le portail afin de générer et de télécharger les informations d'identification de coffre que vous utiliserez pour enregistrer votre serveur avec le coffre de sauvegarde.

4.  Le portail générera une information d'identification de coffre en combinant le nom du coffre et la date actuelle. Cliquez sur **Save** pour télécharger les informations d'identification de coffre vers les dossier de téléchargements du compte local, ou sélectionnez **Save As** à partir du menu **Save** pour spécifier un emplacement pour les informations d'identification de coffre. Vous ne pouvez pas modifier les informations d'identification. Il n'y a donc aucune raison de cliquer sur Ouvrir. Une fois que les informations d'identification auront été téléchargées, vous serez invité à Ouvrir le dossier. Cliquez sur **x** pour fermer ce menu.

## <span id="download"></span></a>Téléchargement et installation d'un agent de sauvegarde

1.  Dans le [portail de gestion][portail de gestion].

2.  Cliquez sur **Recovery Services**, puis sélectionnez un coffre de sauvegarde pour afficher sa page de démarrage rapide.

3.  Dans la page de démarrage rapide, sélectionnez le type d'agent que vous souhaitez télécharger. Vous pouvez choisir **Download Azure Backup Agent**, **Windows Server and System Center Data Protection Manager**, ou **Windows Server Essentials**. Pour plus d'informations, consultez les pages suivantes :

    -   [Installer l'agent Azure Backup pour Windows Server 2012 et System Center 2012 SP1 - Data Protection Manager][Installer l'agent Azure Backup pour Windows Server 2012 et System Center 2012 SP1 - Data Protection Manager]
    -   [Installer l'agent Azure Backup pour Windows Server 2012 Essentials][Installer l'agent Azure Backup pour Windows Server 2012 Essentials]

Une fois l'agent installé, vous pouvez utiliser l'interface de gestion locale appropriée (telle que le composant logiciel enfichable Microsoft Management Console, la console System Center Data Protection Manager ou le tableau de bord Windows Server Essentials) pour configurer la stratégie de sauvegarde pour le serveur.

## <span id="manage"></span></a>Gestion des coffres et serveurs de sauvegarde

1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  Cliquez sur **Recovery Services**, puis sur le nom du coffre de sauvegarde pour afficher la page de démarrage rapide.

3.  Cliquez sur **Dashboard** pour afficher l'aperçu de l'utilisation du serveur. En bas du tableau de bord, vous pouvez réaliser les tâches suivantes :

    -   **Manage certificate**. Si un certificat a été utilisé pour enregistrer le serveur, utilisez cette tâche pour mettre à jour le certificat. Si vous utilisez des informations d'identification de coffre, n'utilisiez pas **Manage certificate**.
    -   **Supprimer**. Permet de supprimer le coffre de sauvegarde actuel. Si un coffre de sauvegarde n'est plus utilisé, vous pouvez le supprimer pour libérer de l'espace de stockage. L'option **Supprimer** est disponible uniquement une fois que tous les serveurs inscrits ont été supprimés du coffre.
    -   **Vault credentials**. Utilisez cet élément de menu d'aperçu rapide pour configurer vos informations d'identification de coffre.

4.  Cliquez sur **Éléments protégés** pour afficher les éléments sauvegardés à partir des serveurs. Cette liste est fournie à titre d'information uniquement.
    ![éléments protégés][éléments protégés]

5.  Cliquez sur **Serveurs** pour afficher le nom des serveurs inscrits auprès de ce coffre. Exécutez ensuite les tâches suivantes :

    -   **Allow Re-registration**. Lorsque cette option est sélectionnée pour un serveur, vous pouvez utiliser l'Assistant Inscription dans l'agent pour inscrire à nouveau le serveur auprès du coffre de sauvegarde. Vous devrez peut-être effectuer cette nouvelle inscription en raison d'une erreur dans le certificat ou de la nécessité de régénérer un serveur. La réinscription est autorisée une seule fois par nom de serveur.
    -   **Supprimer**. Permet de supprimer un serveur du coffre de sauvegarde. Toutes les données stockées associées au serveur sont immédiatement supprimées.

        ![Serveur supprimé][Serveur supprimé]

## <span id="next"></span></a>Étapes suivantes

-   Pour en savoir plus sur Azure Backup, consultez la page [Présentation de la Sauvegarde Windows Azure][Présentation de la Sauvegarde Windows Azure].

-   Consultez le [forum Azure Backup][forum Azure Backup].

  [portail de gestion]: https://manage.windowsazure.com
  [Nouveau coffre de sauvegarde]: http://i.imgur.com/506c7ch.png
  [création d'un coffre de sauvegarde]: http://i.imgur.com/grtLcKM.png
  [Installer l'agent Azure Backup pour Windows Server 2012 et System Center 2012 SP1 - Data Protection Manager]: http://technet.microsoft.com/fr-fr/library/hh831761.aspx#BKMK_installagent
  [Installer l'agent Azure Backup pour Windows Server 2012 Essentials]: http://technet.microsoft.com/fr-fr/library/jj884318.aspx
  [éléments protégés]: ./media/backup-configure-vault/RS_protecteditems.png
  [Serveur supprimé]: ./media/backup-configure-vault/RS_deletedserver.png
  [Présentation de la Sauvegarde Windows Azure]: http://go.microsoft.com/fwlink/p/?LinkId=222425
  [forum Azure Backup]: http://go.microsoft.com/fwlink/p/?LinkId=290933
