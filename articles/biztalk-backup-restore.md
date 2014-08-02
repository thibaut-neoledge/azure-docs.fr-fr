<properties linkid="biztalk-backup-restore" urlDisplayName="BizTalk Services: Backup and Restore" pageTitle="BizTalk Services: Backup and Restore | Azure" metaKeywords="" description="BizTalk Services includes Backup and Restore capabilities. When creating a Backup, a snapshot of the BizTalk Services configuration is taken." metaCanonical="" services="" documentationCenter="" title="BizTalk Services: Backup and Restore" authors="" solutions="" writer="mandia" manager="paulettm" editor="cgronlun" />

Sauvegarde et restauration de BizTalk Services
==============================================

Azure BizTalk Services offre des fonctionnalités de sauvegarde et de restauration. Lors de la création d'une sauvegarde, une capture instantanée de la configuration Azure BizTalk Services est prise.

Examinez les scénarios suivants :

-   Vous pouvez sauvegarder la configuration de votre service BizTalk de deux manières à l'aide du portail de gestion Azure : en créant des sauvegardes (ad hoc), si nécessaire, et en exécutant des sauvegardes planifiées.

-   Le contenu des sauvegardes peut être restauré vers le même service BizTalk ou vers un nouveau service BizTalk. Pour restaurer le service BizTalk à l'aide du même nom, le service BizTalk existant doit être supprimé. Sinon, la restauration échoue.

-   BizTalk Services peut être restauré vers la même édition ou une édition supérieure. La restauration de BizTalk vers une édition inférieure, à partir de la sauvegarde créée, n'est pas prise en charge.

    Par exemple, une sauvegarde à l'aide de l'édition De base peut être restaurée vers l'édition Premium alors qu'une sauvegarde dans l'autre sens est impossible.

-   Les numéros de contrôle EDI sont sauvegardés pour assurer la continuité des numéros de contrôle. Si des messages sont traités après la dernière sauvegarde, la restauration du contenu de cette sauvegarde peut entraîner des numéros de contrôle en double.

-   La sauvegarde et la restauration ne sont pas disponibles avec l'édition Développeur de BizTalk Services.

Cette rubrique décrit la procédure à suivre pour effectuer une sauvegarde et une restauration BizTalk Services à l'aide du portail de gestion Azure. Vous pouvez également utiliser des API REST pour sauvegarder BizTalk Services. Pour plus d'informations, consultez la rubrique [API REST BizTalk Services](http://msdn.microsoft.com/library/windowsazure/dn232347.aspx).

[Éléments sauvegardés](#budata)

[Création d'une sauvegarde](#createbu)

[Restauration](#restore)

Éléments sauvegardés
--------------------

Dans le cadre d'une sauvegarde, les éléments suivants sont sauvegardés :

<table border="1"> 
<TR bgcolor="FAF9F9">
<th> </th>
<TH>Éléments sauvegardés</TH> 
</TR> 
<TR>
<td colspan="2">
 <strong>Portail Azure BizTalk Services</strong></td>
</TR> 
<TR>
<TD>Configuration et exécution</TD> 
<TD><bl>
<li>Détails du partenaire et du profil </li>
<li>Contrats partenaires </li>
<li>Assemblys personnalisés déployés</li>
<li>Ponts déployés</li>
<li>Certificats</li>
<li>Transformations déployées</li>
<li>Pipelines</li>
<li>Modèles créés et enregistrés dans le portail BizTalk Services</li>
<li>Mappages X12 ST01 et GS01 </li>
<li>Numéros de contrôle (EDI)</li>
<li>Valeurs MIC des messages AS2</li>
</bl></TD>
</TR> 
 
<TR>
<td colspan="2">
 <strong>Service Azure BizTalk</strong></td>
</TR> 
<TR>
<TD>Certificat SSL</TD> 
<TD>
<bl>
<li>Données du certificat SSL</li>
<li>Mot de passe du certificat SSL</li>
</bl>
</TD>
</TR> 
<TR>
<TD>Paramètres du service BizTalk</TD> 
<TD>
<bl>
<li>Nombre d'unités mises à l'échelle</li>
<li>Édition</li>
<li>Version du produit</li>
<li>Région/centre de données</li>
<li>Espace de noms et clé Access Control Service (ACS)</li>
<li>Chaîne de connexion à la base de données de suivi</li>
<li>Chaîne de connexion au compte de stockage d'archive</li>
<li>Chaîne de connexion au compte de stockage de surveillance</li>
</bl></TD>
</TR> 
<TR>
<td colspan="2">
 <strong>Autres éléments</strong></td>
</TR> 
<TR>
<TD>Base de données de suivi</TD> 
<TD>Lors de l'approvisionnement du service BizTalk, les détails relatifs à la base de données de suivi sont entrés, y compris le serveur de la base de données SQL Azure et le nom de la base de données de suivi. La base de données de suivi n'est pas automatiquement sauvegardée.<br/><br/>
<strong>Important</strong><br/>
Si la base de données de suivi est supprimée par inadvertance et qu'elle doit être récupérée, la sauvegarde précédente est nécessaire. En l'absence de sauvegarde précédente, la base de données de suivi et les données associées ne pourront pas être récupérées. Dans ce cas, créez une base de données de suivi portant le même nom. La géo-réplication est recommandée.</TD>
</TR> 
</table>

Création d'une sauvegarde
-------------------------

Une sauvegarde peut être effectuée à tout moment et vous la contrôlez complètement. Vous pouvez effectuer des sauvegardes depuis le portail de gestion Azure ou l'API REST du service BizTalk. Pour créer une sauvegarde à l'aide de l'API REST du service BizTalk, consultez la rubrique [Sauvegarde du service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)

Cette section contient des instructions sur la manière d'effectuer des sauvegardes à l'aide du portail de gestion. Vous pouvez utiliser le portail de gestion pour créer des sauvegardes ad hoc ou planifier des sauvegardes aux intervalles souhaités.

Avant de créer une sauvegarde
-----------------------------

Avant de créer une sauvegarde, prenez en compte ce qui suit :

1.  Avant d'exécuter une sauvegarde ad hoc, pour les messages actifs d'un lot, traitez le lot de messages. Cela permet d'éviter la perte de messages *si* cette sauvegarde est restaurée. Les messages par lots ne sont jamais stockés lors d'une sauvegarde. Les sauvegardes planifiées ne vous permettent pas de savoir s'il existe des messages dans le lot lorsque la sauvegarde commence.
    **Remarque**

    En cas de sauvegarde présentant des messages actifs dans un lot, ceux-ci ne sont pas sauvegardés et sont donc perdus.

2.  Facultatif : dans le portail BizTalk Services, arrêtez toutes les opérations de gestion.
3.  Créez la sauvegarde sur le compte de stockage à l'aide des commandes de [Sauvegarde des services BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584) disponibles avec l'API REST.

[Sauvegardes ad hoc](#backupnow)

[Planification d'une sauvegarde](#backupschedule)

### Sauvegardes ad hoc

1.  Depuis le portail de gestion Azure, cliquez sur BizTalk Services, puis sur le nom du service BizTalk que vous souhaitez sauvegarder.
2.  Depuis l'onglet **Tableau de bord** du service BizTalk, cliquez sur **Sauvegarder** en bas de la page.
3.  Dans la boîte de dialogue **Sauvegarder un service BizTalk**, entrez le nom de la sauvegarde.
4.  Sélectionnez un compte de stockage d'objets blob et cliquez sur la coche pour démarrer la sauvegarde.

Une fois la sauvegarde terminée, un conteneur portant le nom de sauvegarde que vous avez indiqué est créé sous le compte de stockage. Ce conteneur comprend la configuration de sauvegarde de votre service BizTalk.

### Planification d'une sauvegarde

1.  Depuis le portail de gestion Azure, cliquez sur BizTalk Services, puis sur le nom du service BizTalk pour lequel vous souhaitez planifier des sauvegardes automatisées et enfin sur l'onglet **Configurer**.
2.  Pour **État de la sauvegarde**, sélectionnez **None** si vous ne souhaitez pas planifier de sauvegardes automatisées. Pour planifier des sauvegardes automatisées, cliquez sur **Automatic**.
3.  Pour **Compte de stockage**, sélectionnez un compte de stockage Azure dans lequel les sauvegardes seront créées.
4.  Pour **Fréquence**, indiquez la date et l'heure de début de la première sauvegarde ainsi que l'intervalle (en jours) des sauvegardes.
5.  Pour **Jours de rétention**, indiquez un délai (en jours) durant lequel les sauvegardes seront conservées. La période de rétention doit être supérieure à la fréquence des sauvegardes.
6.  Activez la case à cocher **Conserver toujours au moins une sauvegarde** pour veiller à ce qu'au moins une sauvegarde soit disponible même si son délai de rétention (en jours) est dépassé.
7.  Cliquez sur **Enregistrer**.

Lors de l'exécution d'une tâche de sauvegarde planifiée, un conteneur est créé (pour stocker les données de sauvegarde) dans le compte de stockage que vous avez indiqué. Le nom du conteneur se présente au format suivant : *BizTalk Service nom-date-heure*.

Si une sauvegarde échoue, la page du tableau de bord du service BizTalk affiche le statut **Failed**.

![Statut de la dernière sauvegarde planifiée](./media/biztalk-backup-restore/status-last-backup.png)

Vous pouvez cliquer sur le lien pour accéder à la page Management Services Operation Logs afin d'en savoir plus sur le problème. Pour plus d'informations sur les journaux d'opérations relatifs à BizTalk Services, consultez la rubrique [BizTalk Services : résolution de problèmes à l'aide des journaux d'opérations](http://go.microsoft.com/fwlink/?LinkId=391211).

Restauration
------------

Vous pouvez restaurer les sauvegardes depuis le portail de gestion Azure ou l'API REST de BizTalk Services. Cette section contient des instructions sur la restauration à l'aide du portail de gestion. Pour effectuer une restauration à l'aide de l'API REST, consultez la rubrique [Restauration d'un service BizTalk depuis une sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582).

### Avant de restaurer une sauvegarde

Lors de la restauration d'une sauvegarde, tenez compte des informations suivantes :

-   De nouveaux magasins de suivi, d'archivage et de surveillance peuvent être spécifiés lors de la restauration d'un service BizTalk.

-   Pour restaurer le service BizTalk en conservant son nom, supprimez le service BizTalk existant avant de lancer la restauration. Sinon, la restauration échoue.

-   Les mêmes données de runtime EDI sont restaurées. La sauvegarde du runtime EDI stocke les numéros de contrôle. Les numéros de contrôle restaurés apparaissent en séquence depuis le moment de la sauvegarde. Si des messages sont traités après la dernière sauvegarde, la restauration du contenu de cette sauvegarde peut entraîner des numéros de contrôle en double.

#### Pour restaurer une sauvegarde

1.  Depuis le portail de gestion Azure, cliquez sur **Nouveau**, pointez sur **Services App**, **Service BizTalk**, puis cliquez sur **Restaurer**.

    ![Restauration d'une sauvegarde](./media/biztalk-backup-restore/restore-ui.png)

2.  Dans l'Assistant Restauration - Nouveau service BizTalk, sur la page **Restaurer le service BizTalk**, cliquez sur l'icône du dossier depuis la zone de texte **URL de sauvegarde** pour ouvrir la boîte de dialogue **Parcourir le stockage cloud**. Cette boîte de dialogue répertorie vos comptes de stockage Azure.

    Développez le compte de stockage que vous avez spécifié lors de la création ou de la planification de la sauvegarde, puis sélectionnez le nom du conteneur à partir duquel vous devez restaurer la configuration du service BizTalk.

    Depuis le volet de droite, sélectionnez le fichier .txt correspondant à la sauvegarde à partir de laquelle vous effectuez la restauration, puis cliquez sur **Ouvrir**.

3.  Sur la page **Restaurer le service BizTalk**, entrez les informations suivantes :
    -   Entrez le nom du service BizTalk. Par défaut, le nom du service BizTalk sauvegardé est utilisé.
    -   Vérifiez l'URL du domaine, l'édition et la région du service BizTalk restauré.
    -   Choisissez de créer une instance de base de données SQL pour la base de données de suivi, puis cliquez sur la flèche droite.

4.  Sur la page **Spécifier les paramètres de la base de données**, vérifiez le nom de la base de données SQL, spécifiez le serveur physique sur lequel la base de données SQL sera créée ainsi qu'un nom d'utilisateur/mot de passe pour ce serveur.

    Si vous souhaitez configurer les paramètres avancés de la base de données SQL, activez la case à cocher **Configurer les paramètres avancés de la base de données**, puis cliquez sur la flèche droite.

    Si vous ne souhaitez pas configurer les paramètres avancés de la base de données, cliquez sur la flèche droite, puis passez à l'étape 6 ci-dessous.

5.  Sur la page **Paramètres avancés de la base de données**, sélectionnez l'édition de la base de données que vous souhaitez utiliser (**Web** ou **Business**), spécifiez la taille maximale de la base de données ainsi que les règles d'assemblage. Cliquez sur la flèche droite.

6.  Sur la page **Spécifiez les paramètres de surveillance/archivage**, créez un compte de stockage ou spécifiez un compte de stockage existant où les informations de surveillance du service BizTalk seront stockées.

7.  Cliquez sur la coche pour lancer le processus de restauration&lt;/b\>.

8.  Une fois la restauration terminée, un nouveau service BizTalk est répertorié comme étant dans un état interrompu sur la page BizTalk Services du portail de gestion Azure.

### Après avoir restauré une sauvegarde

Le service BizTalk est systématiquement restauré dans un état **interrompu**. Cela vous permet d'apporter les modifications de configuration de votre choix à vos applications et, si besoin, à vos applications du service BizTalk avant que le nouvel environnement ne soit fonctionnel. Vous trouverez ci-dessous différents aspects à prendre en compte avant de démarrer le service BizTalk que vous venez de restaurer :

-   Si vous avez créé des applications de service BizTalk à l'aide du Kit de développement logiciel (SDK) Azure BizTalk Services, vous devrez peut-être mettre à jour les informations d'identification ACS qui s'y rapportent à des fins de fonctionnement dans l'environnement restauré.

-   Vous pouvez restaurer un service BizTalk pour répliquer un environnement de service BizTalk déjà fonctionnel. Dans ce cas, si vous disposez de contrats configurés dans le portail BizTalk Services d'origine utilisant les partages FTP comme sources, vous pouvez mettre à jour ces contrats dans l'environnement récemment restauré afin d'utiliser d'autres sources ou partages FTP. À défaut de procéder ainsi, vous risquez de vous retrouver avec deux contrats différents tentant d'extraire le même message.

-   Si vous avez utilisé l'opération de restauration pour plusieurs environnements de service BizTalk, veillez à cibler l'environnement qui convient à l'aide des applications Visual Studio, cmdlets PowerShell, API REST ou API OM du portail de gestion de partenaire commercial.

-   Il est également recommandé de configurer des sauvegardes automatisées de l'environnement du service BizTalk récemment restauré.

Après avoir pris en compte ces différents points, accédez à la page du service BizTalk sur le portail de gestion Azure, sélectionnez le service BizTalk récemment restauré dans l'état suspendu, puis cliquez sur **Reprendre** en bas de la page pour démarrer le service.

Suivant
-------

Pour approvisionner Azure BizTalk Services dans le portail de gestion Azure, accédez à [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280). Pour commencer à créer des applications, consultez la page [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

Voir aussi
----------

-   [Sauvegarde d'un service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
-   [Restauration d'un service BizTalk depuis une sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582)
-   [BizTalk Services : Tableau comparatif des éditions Développeur, De base, Standard et Premium](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk Services : Approvisionnement à l'aide du portail de gestion Azure](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk Services : Tableau comparatif des états d'approvisionnement](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [BizTalk Services : Limitation](http://go.microsoft.com/fwlink/p/?LinkID=302282)
-   [BizTalk Services : Nom et clé de l'émetteur](http://go.microsoft.com/fwlink/p/?LinkID=303941)
-   [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

