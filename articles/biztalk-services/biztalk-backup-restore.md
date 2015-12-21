<properties 
	pageTitle="Créer et restaurer une sauvegarde dans BizTalk Services | Microsoft Azure" 
	description="BizTalk Services offre des fonctionnalités de sauvegarde et de restauration. Apprenez à créer et à restaurer une sauvegarde et à déterminer les éléments sauvegardés. MABS, WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="mandia"/>


# Sauvegarde et restauration de BizTalk Services

Azure BizTalk Services offre des fonctionnalités de sauvegarde et de restauration. Cette rubrique montre comment effectuer une sauvegarde et une restauration BizTalk Services à l’aide du portail Azure Classic.

Vous pouvez également utiliser l'[API REST BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=325584) pour sauvegarder BizTalk Services.

> [AZURE.NOTE]Les connexions hybrides NE sont PAS sauvegardées, quelle que soit l’édition. Vous devez recréer vos connexions hybrides.

## Avant de commencer

- Il se peut que les fonctionnalités de sauvegarde et de restauration ne soient pas disponibles dans toutes les éditions. Consultez [BizTalk Services : tableau comparatif des éditions](biztalk-editions-feature-chart.md).

- À l’aide du portail Azure Classic, vous pouvez créer une sauvegarde à la demande ou planifiée.

- Le contenu des sauvegardes peut être restauré vers le même service BizTalk ou vers un nouveau service BizTalk. Pour restaurer le service BizTalk à l'aide du même nom, le service BizTalk existant doit être supprimé et le nom doit être disponible. Après avoir supprimé un service BizTalk, la disponibilité du même nom peut prendre du temps. Si vous ne pouvez pas attendre que le même nom soit disponible, restaurez vers un nouveau service BizTalk.

- BizTalk Services peut être restauré vers la même édition ou une édition supérieure. La restauration de BizTalk Services vers une édition inférieure, à partir de la sauvegarde créée, n'est pas prise en charge.

	Par exemple, une sauvegarde à l'aide de l'édition De base peut être restaurée vers l'édition Premium alors qu'une sauvegarde dans l'autre sens est impossible.

- Les numéros de contrôle EDI sont sauvegardés pour assurer la continuité des numéros de contrôle. Si des messages sont traités après la dernière sauvegarde, la restauration du contenu de cette sauvegarde peut entraîner des numéros de contrôle en double.

- Si un lot contient des messages actifs, traitez-le **avant** d'effectuer une sauvegarde. Lors de la création d'une sauvegarde (à la demande ou planifiée), les messages contenus dans des lots ne sont jamais stockés.

	**En cas de sauvegarde présentant des messages actifs dans un lot, ceux-ci ne sont pas sauvegardés et sont donc perdus.**

- Facultatif : dans le portail BizTalk Services, arrêtez toutes les opérations de gestion.


## Création d'une sauvegarde

Une sauvegarde peut être effectuée à tout moment et vous la contrôlez complètement. Cette section répertorie les étapes à suivre pour créer des sauvegardes à l’aide du portail Azure Classic :

[Sauvegarde à la demande](#backupnow)

[Planification d'une sauvegarde](#backupschedule)

#### <a name="backupnow"></a>Sauvegarde à la demande
1. Dans le portail Azure Classic, sélectionnez **BizTalk Services**, puis le service BizTalk que vous souhaitez sauvegarder.
2. Sous l'onglet **Tableau de bord**, sélectionnez **Sauvegarder** en bas de la page.
3. Entrez un nom de sauvegarde. Par exemple, entrez *myBizTalkService*BU*Date*.
4. Choisissez un compte de stockage d'objets blob et sélectionnez la coche pour démarrer la sauvegarde.

Une fois la sauvegarde terminée, un conteneur portant le nom de sauvegarde indiqué est créé dans le compte de stockage. Ce conteneur comprend la configuration de sauvegarde de votre service BizTalk.

#### <a name="backupschedule"></a>Planification d'une sauvegarde

1. Dans le portail Azure Classic, sélectionnez **BizTalk Services**, puis le nom du service BizTalk pour lequel vous souhaitez planifier la sauvegarde et enfin l’onglet **Configurer**.
2. Définissez **État de la sauvegarde** sur **Automatique**. 
3. Sélectionnez le **Compte de stockage** dans lequel stocker la sauvegarde, puis entrez la **Fréquence** de création des sauvegardes et la durée pendant laquelle vous souhaitez les conserver (**Jours de rétention**) :

	![][AutomaticBU]

	**Remarques** : concernant les **jours de rétention**, la période de rétention doit être supérieure à la fréquence des sauvegardes. Sélectionnez **Conserver toujours au moins une sauvegarde**, même si elle est au-delà de la période de rétention.
	

4. Sélectionnez **Enregistrer**.


Lors de l'exécution d'une tâche de sauvegarde planifiée, un conteneur est créé (pour stocker les données de sauvegarde) dans le compte de stockage que vous avez indiqué. Le nom du conteneur se présente comme suit : *Service BizTalk nom-date-heure*.

Si le tableau de bord du service BizTalk indique l'état **Échec** :

![Statut de la dernière sauvegarde planifiée][BackupStatus]

Le lien ouvre les journaux des opérations des services de gestion pour vous aider à résoudre le problème. Consultez [BizTalk Services : résolution des problèmes à l'aide des journaux des opérations](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## Restauration

Vous pouvez restaurer des sauvegardes depuis le portail Azure Classic ou l’[API REST Restaurer le service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582). Cette section répertorie les étapes à suivre pour restaurer une sauvegarde à l’aide du portail Azure Classic.

#### Avant de restaurer une sauvegarde

- De nouveaux magasins de suivi, d'archivage et de surveillance peuvent être spécifiés lors de la restauration d'un service BizTalk.

- Les mêmes données de runtime EDI sont restaurées. La sauvegarde du runtime EDI stocke les numéros de contrôle. Les numéros de contrôle restaurés apparaissent en séquence depuis le moment de la sauvegarde. Si des messages sont traités après la dernière sauvegarde, la restauration du contenu de cette sauvegarde peut entraîner des numéros de contrôle en double.

#### Restauration d'une sauvegarde

1. Dans le portail Azure Classic, sélectionnez **Nouveau** > **Services d’application** > **Service BizTalk** > **Restaurer** :

	![Restauration d'une sauvegarde][Restore]

2. Dans **URL de sauvegarde**, sélectionnez l'icône de dossier et développez le compte de stockage Azure qui stocke la sauvegarde de la configuration du service BizTalk. Développez le conteneur, puis dans le volet droit, sélectionnez le fichier .txt de sauvegarde correspondant.. <br/><br/>Sélectionnez **Ouvrir**.

3. Dans la page **Restaurer le service BizTalk**, entrez le **Nom du service BizTalk**, puis vérifiez l'**URL de domaine**, l'**Édition** et la **Région** du service BizTalk restauré. Vous devez ensuite **Créer une instance de base de données SQL** pour la base de données de suivi :

	![][RestoreBizTalkService]

	Sélectionnez la flèche Suivant.

4. 	Vérifiez le nom de la base de données SQL, entrez le serveur physique sur lequel la base de données SQL sera créée, ainsi qu'un nom d'utilisateur/mot de passe pour ce serveur.


	Si vous souhaitez configurer l'édition, la taille et d'autres propriétés de la base de données SQL, sélectionnez **Configurer les paramètres avancés de la base de données**.

	Sélectionnez la flèche Suivant.

5. Créez un compte de stockage ou spécifiez un compte de stockage existant pour le service BizTalk.

7. Cliquez sur la coche pour démarrer la restauration.

Une fois la restauration terminée, un nouveau service BizTalk est répertorié comme étant dans un état interrompu sur la page BizTalk Services du portail Azure Classic.



### <a name="postrestore"></a>Après avoir restauré une sauvegarde

Le service BizTalk est systématiquement restauré dans un état **Suspendu**. Dans cet état, vous pouvez apporter des modifications à la configuration avant que le nouvel environnement ne soit fonctionnel :

- Si vous avez créé des applications de service BizTalk à l'aide du Kit de développement logiciel (SDK) Azure BizTalk Services, vous devrez peut-être mettre à jour les informations d'identification Access Control (ACS) qui s'y rapportent pour qu'elles fonctionnent dans l'environnement restauré.

- Vous restaurez un service BizTalk pour répliquer un environnement de service BizTalk existant. Dans ce cas de figure, s'il existe des contrats configurés dans le portail BizTalk Services d'origine utilisant un dossier FTP source, vous devrez peut-être mettre à jour les contrats de l'environnement récemment restauré de manière à utiliser un autre dossier FTP source. Sinon, vous risquez de vous retrouver avec deux contrats différents tentant d'extraire le même message.

- Si vous avez procédé à la restauration pour plusieurs environnements de service BizTalk, veillez à cibler l'environnement approprié dans les applications Visual Studio, applets de commande PowerShell, API REST ou API OM du portail de gestion du partenaire commercial.

- Il est recommandé de configurer des sauvegardes automatisées dans l'environnement du service BizTalk récemment restauré.

Pour démarrer le service BizTalk dans le portail Azure Classic, sélectionnez le service BizTalk restauré, puis **Reprendre** dans la barre des tâches.



## Éléments sauvegardés

Dans le cadre d'une sauvegarde, les éléments suivants sont sauvegardés :

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Éléments sauvegardés</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portail Azure BizTalk Services</strong></td>
</tr> 
<tr>
<td>Configuration et exécution</td> 
<td>
<ul>
<li>Détails du partenaire et du profil</li>
<li>Contrats partenaires</li>
<li>Assemblys personnalisés déployés</li>
<li>Ponts déployés</li>
<li>Certificats</li>
<li>Transformations déployées</li>
<li>Pipelines</li>
<li>Modèles créés et enregistrés dans le portail BizTalk Services</li>
<li>Mappages X12 ST01 et GS01</li>
<li>Numéros de contrôle (EDI)</li>
<li>Valeurs MIC des messages AS2</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Service Azure BizTalk</strong></td>
</tr> 
<tr>
<td>Certificat SSL</td> 
<td>
<ul>
<li>Données du certificat SSL</li>
<li>Mot de passe du certificat SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Paramètres du service BizTalk</td> 
<td>
<ul>
<li>Nombre d'unités mises à l'échelle</li>
<li>Édition</li>
<li>Version du produit</li>
<li>Région/centre de données</li>
<li>Espace de noms et clé Access Control Service (ACS)</li>
<li>Chaîne de connexion à la base de données de suivi</li>
<li>Chaîne de connexion au compte de stockage d'archive</li>
<li>Chaîne de connexion au compte de stockage de surveillance</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Autres éléments</strong></td>
</tr> 
<tr>
<td>Base de données des suivis</td> 
<td>Lors de la création du service BizTalk, les détails relatifs à la base de données de suivi sont entrés, y compris le serveur de la base de données SQL Azure et le nom de la base de données de suivi. La base de données de suivi n'est pas automatiquement sauvegardée.
<br/><br/>
<strong>Important</strong><br/>
Si la base de données de suivi est supprimée et qu'elle doit être récupérée, une sauvegarde précédente est nécessaire. En l'absence de sauvegarde précédente, la base de données de suivi et les données associées ne pourront pas être récupérées. Dans ce cas, créez une base de données de suivi portant le même nom. La géo-réplication est recommandée.</td>
</tr> 
</table>

## Suivant

Pour créer des services Azure BizTalk Services dans le portail Azure Classic, accédez à [BizTalk Services : approvisionnement à l’aide du portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=302280). Pour commencer à créer des applications, consultez la page [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## Voir aussi
- [Sauvegarde d'un service BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restauration d'un service BizTalk depuis une sauvegarde](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Tableau comparatif des éditions Développeur, De base, Standard et Premium de BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk Services : approvisionnement à l’aide du portail Azure Classic](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Tableau comparatif des états d'approvisionnement BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Onglets Tableau de bord, Surveiller et Mettre à l'échelle dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Limitation dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Nom et clé de l'émetteur dans BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Utilisation du Kit de développement logiciel (SDK) Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 

<!---HONumber=AcomDC_1210_2015-->