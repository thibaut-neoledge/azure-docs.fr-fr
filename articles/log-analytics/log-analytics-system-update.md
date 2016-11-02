<properties
    pageTitle="Solution d’évaluation des mises à jour système dans Log Analytics | Microsoft Azure"
    description="Vous pouvez utiliser la solution d’évaluation des mises à jour système dans Log Analytics pour vous aider à appliquer les mises à jour manquantes aux serveurs de votre infrastructure."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>


# <a name="system-update-assessment-solution-in-log-analytics"></a>Solution d’évaluation des mises à jour système dans Log Analytics

Vous pouvez utiliser la solution d’évaluation des mises à jour système dans Log Analytics pour vous aider à appliquer les mises à jour manquantes aux serveurs de votre infrastructure. Une fois la solution installée, vous pouvez afficher les mises à jour manquantes de vos serveurs analysés à l’aide de la vignette **Évaluation des mises à jour système** dans la page **Vue d’ensemble** d’OMS.

Si des mises à jour manquantes sont trouvées, des informations détaillées sont affichées sur le tableau de bord **Mises à jour** . Vous pouvez utiliser le tableau de bord des **Mises à jour** pour trouver les mises à jour manquantes et définir un programme pour les appliquer aux serveurs qui en ont besoin.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Ajoutez la solution d’évaluation des mises à jour système à votre espace de travail OMS en suivant la procédure décrite dans la rubrique [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).  Aucune configuration supplémentaire n’est requise.

## <a name="system-update-data-collection-details"></a>Détails de la collecte de données des mises à jour système

L’évaluation des mises à jour système collecte les données d’état et les métadonnées à l’aide des agents que vous avez activés.

Le tableau suivant présente les méthodes de collecte des données et d’autres informations sur le mode de collecte en vue de l’évaluation des mises à jour système.

| plateforme | Agent direct | Agent SCOM | Azure Storage | SCOM requis ? | Données de l’agent SCOM envoyées via un groupe d’administration | fréquence de collecte |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)|![Non](./media/log-analytics-system-update/oms-bullet-red.png)|            ![Non](./media/log-analytics-system-update/oms-bullet-red.png)|![Oui](./media/log-analytics-system-update/oms-bullet-green.png)| Au moins 2 fois par jour et 15 minutes après l’installation d’une mise à jour|

Le tableau suivant répertorie des exemples de types de données collectées par l’évaluation des mises à jour système :

|**Type de données**|**Champs**|
|---|---|
|Metadata|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|State|StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified|


### <a name="to-work-with-updates"></a>Utilisation des mises à jour

1. Dans la page **Vue d’ensemble**, cliquez sur la vignette **Évaluation des mises à jour du système**.  
    ![Évaluation des mises à jour du système](./media/log-analytics-system-update/sys-update-tile.png)
2. Sur le tableau de bord des **Mises à jour** , affichez les catégories de mise à jour.  
    ![Tableau de bord des mises à jour](./media/log-analytics-system-update/sys-updates02.png)
3. Faites défiler vers la droite de la page pour afficher le panneau **Mises à jour Windows critiques ou de sécurité**, puis, sous **Classification**, cliquez sur **Mises à jour de sécurité**.  
    ![Mises à jour de sécurité](./media/log-analytics-system-update/sys-updates03.png)
4. La page de recherche de journal affiche une série d’informations sur les mises à jour de sécurité absentes de serveurs de votre infrastructure. Cliquez sur **Liste** pour afficher des informations détaillées sur les mises à jour.  
    ![Résultats de recherche - Liste](./media/log-analytics-system-update/sys-updates04.png)
5. La page Recherche de journal affiche des informations détaillées sur chaque mise à jour. En regard du numéro KBID, cliquez sur **Afficher** pour consulter l’article correspondant sur le site web du Support Microsoft.  
    ![Résultats de Recherche de journal - Affichage de la base de connaissance](./media/log-analytics-system-update/sys-updates05.png)
6. Votre navigateur web ouvre la page web du Support Microsoft correspondant à la mise à jour dans un nouvel onglet. Consultez les informations sur la mise à jour manquante.  
    ![Page web du Support Microsoft](./media/log-analytics-system-update/sys-updates06.png)
7. Les informations trouvées vous permettent d’élaborer un plan pour appliquer manuellement la mise à jour manquante. Autrement, vous pouvez suivre les étapes restantes pour appliquer automatiquement la mise à jour.
8. Si vous souhaitez appliquer automatiquement la mise à jour manquante, revenez au tableau de bord **Mises à jour**, puis, sous **Exécutions de mise à jour**, cliquez sur **Cliquez pour planifier une exécution de mise à jour**.  
    ![Exécutions de mise à jour - Onglet Planifiées](./media/log-analytics-system-update/sys-updates07.png)
9. Dans la page **Exécutions de mise à jour**, sous l’onglet **Planifiées**, cliquez sur **Ajouter** pour créer une exécution de mise à jour.  
    ![Onglet Planifiée - Ajouter](./media/log-analytics-system-update/sys-updates08.png)
10. Dans la page **Nouvelle exécution de mise à jour**, tapez un nom pour l’exécution de la mise à jour, ajoutez des ordinateurs ou des groupes d’ordinateurs, définissez une planification, puis cliquez sur **Enregistrer**.  
    ![Nouvelle exécution de mise à jour](./media/log-analytics-system-update/sys-updates09.png)
11. L’onglet **Planifiées** de la page **Exécutions de mise à jour** affiche la nouvelle mise à jour planifiée.  
    ![Onglet Planifiées](./media/log-analytics-system-update/sys-updates10.png)
12. Lorsque l’exécution de la mise à jour démarre, des informations la concernant s’affichent sous l’onglet **En cours d’exécution**.  
    ![Onglet En cours d’exécution](./media/log-analytics-system-update/sys-updates11.png)
13. Une fois la mise à jour exécution terminée, l’onglet **Terminé** affiche l’état.
14. Si des mises à jour ont été appliquées à partir de l’exécution de la mise à jour, dans le panneau **Mises à jour Windows critiques ou de sécurité**, vous pouvez voir que le nombre de mises à jour est réduit.  
    ![Panneau Mises à jour Windows critiques ou de sécurité - Nombre de mises à jour réduit](./media/log-analytics-system-update/sys-updates12.png)



## <a name="next-steps"></a>Étapes suivantes

- [Lancez une recherche dans les journaux](log-analytics-log-searches.md) pour afficher les données de mise à jour système détaillées.



<!--HONumber=Oct16_HO2-->


