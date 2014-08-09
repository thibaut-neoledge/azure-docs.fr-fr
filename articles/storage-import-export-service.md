<properties linkid="manage-services-import-export" urlDisplayName="Azure Import/Export Service" pageTitle="Using import/export to transfer data to Blob Storage | Microsoft Azure" metaKeywords="" description="Learn how to create import and export jobs in the Azure Management Portal to transfer data to blob storage." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Using the Azure Import/Export Service to Transfer Data to Blob Storage" authors="tamram" />

Transfert de données vers le stockage d'objets blob à l'aide du service Azure Import/Export
===========================================================================================

Vous pouvez utiliser le service Azure Import/Export pour transférer de grandes quantités de données de fichiers vers le stockage d'objets blob Azure lorsque le coût de l'opération de téléchargement via le réseau est prohibitif, voire irréalisable. Vous pouvez également utiliser ce service pour transférer de manière rapide et économique des volumes importants de données résidant dans le stockage d'objets Blob vers vos installations locales.

Pour transférer un jeu de données de fichiers volumineux vers le stockage d'objets blob, vous pouvez envoyer le ou les disques durs contenant ces données à un centre de données Azure, qui téléchargera vos données vers votre compte de stockage. De la même manière, pour exporter des données à partir d'un stockage d'objets blob, vous pouvez envoyer des disques durs vides à un centre de données Azure, qui se chargera de copier les données BLOB de votre compte de stockage sur vos disques durs avant de vous les renvoyer. Avant d'envoyer un lecteur, vous devez chiffrer les données qu'il contient ; le service Import/Export en fait de même avant de vous renvoyer vos données exportées.

Vous pouvez créer et gérer des tâches d'importation et d'exportation de l'une des deux façons suivantes :

-   en utilisant le portail de gestion Azure ;
-   en utilisant une interface REST communiquant avec le service.

Cet article offre une vue d'ensemble du service Import/Export et explique comment l'utiliser via le portail de gestion. Pour plus d'informations sur l'API REST, consultez la page [Référence de l'API REST du service d'importation/exportation Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

Vue d'ensemble du service Import/Export
---------------------------------------

Pour lancer le processus d'importation ou d'exportation vers ou à partir d'un stockage d'objets blob, commencez par créer une *tâche*. Il peut s'agir d'une *tâche d'importation* ou d'une *tâche d'exportation* :

-   Une tâche d'importation vise à transférer des données locales vers des objets blob de votre compte de stockage Azure.
-   Une tâche d'exportation vise à transférer des données stockées sous forme d'objets blob dans votre compte de stockage sur des disques durs qui vous sont ensuite expédiés.

Lorsque vous créez une tâche, vous avertissez le service Import/Export que vous allez expédier un ou plusieurs disques durs vers un centre de données Azure. Dans le cas d'une tâche d'importation, vous expédiez des disques durs contenant des données de fichiers. Dans le cas d'une tâche d'exportation, vous expédiez des disques durs vides.

Pour préparer l'envoi de votre lecteur pour une tâche d'importation, vous devez exécuter l'outil **WAImportExport**. Celui-ci facilite la copie de vos données sur le disque, chiffre ces données avec BitLocker, puis génère les fichiers journaux du lecteur. Ces opérations vous seront décrites ultérieurement.
**Remarque**

Les données contenues dans le disque dur doivent être chiffrées à l'aide du chiffrement de lecteur BitLocker. Cette opération permet de protéger vos données pendant qu'elles sont en transit. Dans le cas d'une tâche d'exportation, le service Import/Export chiffre les données avant de vous renvoyer le disque dur.

Lorsque vous créez une tâche d'importation ou d'exportation, vous avez également besoin d'un *ID de lecteur*, qui est le numéro de série attribué au disque dur par le fabricant de lecteurs. L'ID de lecteur figure sur la façade extérieure du lecteur.

### Exigences et portée

1.  **Abonnement et comptes de stockage :** vous devez être titulaire d'un abonnement Azure et posséder un ou plusieurs comptes de stockage pour pouvoir utiliser le service Import/Export. Chaque tâche peut servir à transférer des données vers ou à partir d'un seul compte de stockage. Autrement dit, une tâche ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](http://www.windowsazure.com/fr-fr/manage/services/storage/how-to-create-a-storage-account/).
2.  **Disques durs :** seuls les disques durs SATA II de 3,5 pouces sont pris en charge par le service Import/Export. Les disques durs de plus de 4 To ne sont pas pris en charge avec la version préliminaire. Dans le cas des tâches d'importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS. Vous pouvez raccorder un disque SATA II par voie externe à la plupart des ordinateurs à l'aide d'un adaptateur USB SATA II.
3.  **Chiffrement BitLocker :** toutes les données stockées sur les disques durs doivent être chiffrées à l'aide de BitLocker avec des clés de chiffrement protégées par des mots de passe numériques.
4.  **Cibles de stockage d'objets blob :** les données peuvent être téléchargées vers ou à partir d'objets blob de blocs ou de pages.
5.  **Nombre de tâches :** un client peut avoir au maximum 20 tâches actives par abonnement.
6.  **Taille maximale d'une tâche :** la taille d'une tâche dépend de la capacité des disques durs utilisés et de la quantité maximale de données qu'un compte de stockage peut accueillir. Chaque tâche peut contenir au maximum 10 disques durs.

Création d'une tâche d'importation dans le portail de gestion
-------------------------------------------------------------

Créez une tâche d'importation pour avertir le service Import/Export que vous allez expédier au centre de données un ou plusieurs lecteurs contenant des données à importer dans votre compte de stockage.

### Préparation des lecteurs

Avant de créer une tâche d'importation, préparez vos lecteurs à l'aide de l'[outil WAImportExport](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409). Pour plus d'informations sur l'utilisation de l'outil WAImportExport, consultez la page [Référence de l'outil WAImportExport](http://go.microsoft.com/fwlink/?LinkId=329032).

Pour préparer vos lecteurs, effectuez les trois étapes suivantes :

1.  Déterminez les données à importer et le nombre de lecteurs dont vous avez besoin.
2.  Identifiez les objets blob de destination de vos données dans le service d'objets blob Azure.
3.  Utilisez l'outil WAImportExport pour copier vos données sur un ou plusieurs disques durs.

L'outil WAImportExport génère un fichier *journal de lecteur* lors de la préparation de chaque lecteur. Le fichier journal de lecteur est stocké sur votre ordinateur local, et non sur le lecteur lui-même. Vous serez amené à télécharger le fichier journal lors de la phase de création de la tâche d'importation. Le fichier journal d'un lecteur contient son ID, la clé BitLocker, ainsi que d'autres informations sur le lecteur.

### Création de la tâche d'importation

1.  Après avoir préparé votre lecteur, accédez à votre compte de stockage dans le portail de gestion, puis affichez le tableau de bord. Sous **Quick Glance**, cliquez sur **Create an Import Job**.

2.  À l'étape 1 de l'Assistant, précisez que vous avez préparé votre lecteur et que avez le fichier journal du lecteur à disposition.

3.  À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'importation. Si vous souhaitez enregistrer des données de journal détaillées pour la tâche d'importation, activez l'option **Save the verbose log in my 'waimportexport' blob container**.

4.  À l'étape 3, téléchargez les fichiers journaux de lecteur que vous avez obtenus à l'étape de préparation de lecteur. Vous devez télécharger un fichier pour chaque lecteur préparé.

    ![Créer une tâche d'importation - Étape 3](./media/storage-import-export-service/import-job-03.png)

5.  À l'étape 4, attribuez un nom descriptif à la tâche d'importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que choisissez vous servira à suivre la tâche pendant et après son exécution.

    La région du centre de données indique à quel centre de données vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

    Si vous détenez déjà votre numéro de suivi FedEx, sélectionnez **I have my tracking number and want to enter it now**, puis passez à l'étape suivante. Si vous n'avez pas encore de numéro de suivi, choisissez **I will provide my shipping information for this import job once I have shipped my package**, puis terminez le processus d'importation.

6.  Si vous disposez déjà de votre numéro de suivi, entrez ce numéro et confirmez-le à l'étape 5.

Création d'une tâche d'exportation dans le portail de gestion
-------------------------------------------------------------

Créez une tâche d'exportation pour avertir le service Import/Export que vous allez expédier un ou plusieurs lecteurs vides au centre de données, de sorte que les données puissent être exportées de votre compte de stockage vers les lecteurs, qui vous seront ensuite renvoyés.

1.  Pour créer une tâche d'exportation, accédez à votre compte de stockage dans le portail de gestion, puis affichez le tableau de bord. Sous **Quick Glance**, cliquez sur **Create an Export Job**, puis parcourez les étapes de l'Assistant.

2.  À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'exportation. Si vous souhaitez enregistrer des données de journal détaillées pour la tâche d'exportation, activez l'option **Save the verbose log in my 'waimportexport' blob container**.

3.  À l'étape 3, indiquez les données BLOB que vous souhaitez exporter de votre compte de stockage vers le ou les lecteurs vides. Vous pouvez choisir d'exporter toutes les données BLOB contenues dans le compte de stockage ou indiquer les objets blob ou les ensembles d'objets blob que vous souhaitez exporter.

    ![Créer une tâche d'exportation - Étape 3](./media/storage-import-export-service/export-job-03.png)

    -   Pour spécifier un objet blob à exporter, utilisez le sélecteur **Equal To**, puis indiquez le chemin d'accès relatif de l'objet blob en le faisant précéder du nom du conteneur. Utilisez *\$root* pour spécifier le conteneur racine.
    -   Pour spécifier tous les objets blob commençant par un préfixe, utilisez le sélecteur **Starts With**, puis spécifiez le préfixe en le faisant précéder d'une barre oblique (« / »). Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob.

    Le tableau suivant présente des exemples de chemins d'accès d'objet blob valides :

    <table data-morhtml="true" border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tbody data-morhtml="true">
        <tr data-morhtml="true">
            <td data-morhtml="true"><strong data-morhtml="true">Sélecteur</strong></td>
            <td data-morhtml="true"><strong data-morhtml="true">Chemin d'accès d'objet blob</strong></td>
            <td data-morhtml="true"><strong data-morhtml="true">Description</strong></td>
        </tr>
        <tr data-morhtml="true">
            <td data-morhtml="true">Starts With</td>
            <td data-morhtml="true">/</td>
            <td data-morhtml="true">Exporte tous les objets blob présents dans le compte de stockage.</td>
        </tr>
        <tr data-morhtml="true">
            <td data-morhtml="true">Starts With</td>
            <td data-morhtml="true">/$root/</td>
            <td data-morhtml="true">Exporte tous les objets blob présents dans le conteneur racine.</td>
        </tr>
        <tr data-morhtml="true">
            <td data-morhtml="true">Starts With</td>
            <td data-morhtml="true">/book</td>
            <td data-morhtml="true">Exporte tous les objets blob présents dans un conteneur commençant par le préfixe <strong data-morhtml="true">book</strong>.</td>
        </tr>
        <tr data-morhtml="true">
            <td data-morhtml="true">Starts With</td>
            <td data-morhtml="true">/music/</td>
            <td data-morhtml="true">Exporte tous les objets blob présents dans le conteneur <strong data-morhtml="true">music</strong>.</td>
        </tr>
        <tr data-morhtml="true">
            <td data-morhtml="true">Starts With</td>
            <td data-morhtml="true">/music/love</td>
            <td data-morhtml="true">Exporte tous les objets blob présents dans le conteneur <strong data-morhtml="true">music</strong> qui commence par le préfixe <strong data-morhtml="true">love</strong>.</td>
        </tr>
        <tr data-morhtml="true">
            <td data-morhtml="true">Equal To</td>
            <td data-morhtml="true">$root/logo.bmp</td>
            <td data-morhtml="true">Exporte l'objet blob <strong data-morhtml="true">logo.bmp</strong> présent dans le conteneur racine.</td>
        </tr>
        <tr data-morhtml="true">
            <td data-morhtml="true">Equal To</td>
            <td data-morhtml="true">videos/story.mp4</td>
            <td data-morhtml="true">Exporte l'objet blob <strong data-morhtml="true">story.mp4</strong> présent dans le conteneur <strong data-morhtml="true">videos</strong>.</td>
        </tr>
    </tbody>
</table>
  </li>
  <li>

  À l'étape 4, attribuez un nom descriptif à la tâche d'exportation. Le nom que vous entrez ne peut contenir 	que des minuscules, des chiffres, des tirets et des traits de soulignement ; il doit commencer par une lettre et ne peut pas contenir d'espaces.</p>

    <p>La région du centre de données indique à quel centre de données vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.</p>

    <p>Si vous détenez déjà votre numéro de suivi FedEx, sélectionnez <strong data-morhtml="true">I have my tracking number and want to enter it now</strong>, puis passez à l'étape suivante. Si vous n'avez pas encore de numéro de suivi, choisissez <strong data-morhtml="true">I will provide my shipping information for this export job once I have shipped my package</strong>, puis terminez le processus d'exportation.</p><li>

  À l'étape 5, entrez votre numéro de suivi, puis confirmez-le. </p></li>

Suivi du statut des tâches dans le portail de gestion
-----------------------------------------------------

Vous pouvez suivre le statut de vos tâches d'importation ou d'exportation dans le portail de gestion. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Import/Export**. La liste de vos tâches s'affiche sur la page. Vous pouvez filtrer la liste en fonction du statut des tâches, de leur nom, de leur type ou du numéro de suivi.

Le tableau ci-dessous indique la signification de chaque désignation de statut de tâche :

<table data-morhtml="true" border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody data-morhtml="true">
		<tr data-morhtml="true">
			<td data-morhtml="true"><strong data-morhtml="true">Statut de tâche</strong></td>
			<td data-morhtml="true"><strong data-morhtml="true">Description</strong></td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Creating</td>
			<td data-morhtml="true">Votre tâche a été créée, mais vous n'avez pas encore fourni vos détails d'expédition.</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Shipping</td>
			<td data-morhtml="true">Votre tâche a été créée et vous avez fourni vos détails d'expédition.</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Transferring</td>
			<td data-morhtml="true">Vos données sont en cours de transfert de votre disque dur (pour une tâche d'importation) ou vers ce dernier (pour une tâche d'exportation).</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Packaging</td>
			<td data-morhtml="true">Le transfert de vos données est terminé et votre disque dur est en cours de préparation pour vous être renvoyé.</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Complete</td>
			<td data-morhtml="true">Votre disque dur vous a été renvoyé.</td>
		</tr>
	</tbody>
</table>

Affichage des clés BitLocker pour une tâche d'exportation
---------------------------------------------------------

Dans le cas des tâches d'exportation, vous pouvez afficher et copier les clés BitLocker que le service a générées pour vos lecteurs. Celles-ci vous permettent de déchiffrer vos données exportées une fois que vous avez reçu les lecteurs en provenance du centre de données Azure. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Import/Export**. Sélectionnez votre tâche d'exportation dans la liste, puis cliquez sur le bouton **View Keys**. Les clés BitLocker s'affichent comme illustré ci-dessous :

![Afficher les clés BitLocker pour une tâche d'exportation](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Forum Aux Questions (FAQ)
-------------------------

### Généralités

**Combien coûte le service Import/Export ?**

-   Consultez cette [page](http://go.microsoft.com/fwlink/?LinkId=329033) pour obtenir des informations sur la tarification.

**Combien de temps faut-il compter pour que mes données soient importées ou exportées ?**

-   Il faut compter le temps d'expédition des disques, plus le temps de copie des données, soit plusieurs heures par To de données.

**Quels sont les types d'interface pris en charge ?**

-   Le service Import/Export prend en charge les disques durs SATA II de 3,5 pouces. Avant l'expédition, vous pouvez utiliser les convertisseurs suivants pour transférer les données des périphériques via les interfaces USB/SATA :
    -   Anker 68UPSATAA-02BU
    -   Anker 68UPSHHDS-BU
    -   Startech SATADOCK22UE

**Que faire si je souhaite importer ou exporter plus de 10 lecteurs ?**

-   La version préliminaire du service Import/Export limite chaque tâche d'importation ou d'exportation au référencement de 10 lecteurs. Si vous souhaitez en expédier plus, vous pouvez créer plusieurs tâches.

**Que se passe-t-il si j'envoie par accident un disque dur non conforme au type pris en charge ?**

-   Le centre de données Azure vous renvoie le lecteur non conforme au type pris en charge. Si seule une partie des lecteurs contenus dans le colis respecte la configuration requise, ceux-ci sont traités ; ceux qui ne la respectent vous sont renvoyés.

### Gestion des tâches d'importation/exportation

**Qu'advient-il de mes tâches d'importation et d'exportation si je supprime mon compte de stockage Azure ?**

-   Lorsque vous supprimez votre compte de stockage, toutes les tâches d'importation/exportation Azure sont supprimées en même temps que votre compte.

**Puis-je annuler une tâche ?**

-   Vous pouvez annuler une tâche dont le statut est Creating ou Shipping.

**Pendant combien de temps le statut des tâches terminées est-il consultable dans le portail de gestion ?**

-   Le statut des tâches terminées est visible pendant 90 jours. Au-delà de ce délai, toutes les tâches terminées sont archivées. Si vous avez besoin de récupérer le statut de vos tâches terminées après 90 jours, vous pouvez contacter le service client.

### Expédition

**À quels services d'expédition peut-on recourir ?**

-   Avec la version préliminaire, seul le service Federal Express (FedEx) est pris en charge.
-   Dans le cas d'une tâche d'importation, les colis peuvent être acheminés via FedEx Express ou FedEx Ground.
-   Tous les colis sont renvoyés via FedEx Ground.

    **Important**

    Vous devez fournir votre numéro de suivi au service Azure Import/Export ; à défaut, votre tâche n'est pas traitée.

**Les retours d'expédition sont-ils facturés ?**

-   Les retours sont gratuits dans le cadre de la version préliminaire.

**D'où et vers quelles destinations puis-je expédier mes données ?**

-   Le service Import/Export accepte les expéditions **en provenance** des États-Unis uniquement, et ne peut renvoyer les colis qu'à des adresses situées aux États-Unis. Le service prend en charge l'importation et l'exportation de données vers et depuis des comptes de stockage dans les régions suivantes :
    -   Est des États-Unis
    -   Ouest des États-Unis
    -   États-Unis - partie centrale septentrionale
    -   États-Unis - partie centrale méridionale
    -   Europe du Nord
    -   Europe de l'Ouest
    -   Est de l'Asie
    -   Asie du Sud-Est
-   Si votre compte de stockage réside dans un centre de données situé aux États-Unis, vous serez peut-être amené à expédier vos lecteurs vers un autre centre de données d'une autre région, car certains centres de données n'assurent pas encore le service Import/Export. Si votre tâche est traitée dans une région différente de celle où est situé votre compte de stockage, des frais de sortie peuvent vous être facturés.

-   Si votre compte de stockage se trouve dans un centre de données situé en Europe ou en Asie, vous devez expédier votre lecteur dans l'une des régions des États-Unis prise en charge, et l'expédition doit provenir des États-Unis. Le service Import/Export se chargera ensuite de copier les données vers ou à partir de votre compte de stockage situé en Europe ou en Asie.

    -   Dans le cas d'une tâche d'importation, aucun frais d'entrée n'est appliqué pour l'opération de copie.
    -   Dans le cas d'une tâche d'exportation, des frais de transfert de données sont appliqués pour la copie de données entre différents centres de données Azure. Par exemple, si votre compte de stockage est situé en Europe de l'Ouest et que vous expédiez votre lecteur vers le centre de données Est des États-Unis, des frais de sortie vous seront facturés pour l'acheminement des données d'Europe de l'Ouest vers l'Est des États-Unis en vue de leur exportation.

    **Important**

    Les centres de données Azure ne réceptionnent pas les expéditions de colis en provenance de l'extérieur des États-Unis et refusent leur livraison.

**Est-il possible d'acheter des lecteurs auprès de Microsoft pour des tâches d'importation/exportation ?**

-   Non. Vous devez expédier vos propres lecteurs, aussi bien pour les tâches d'importation que pour les tâches d'exportation.

### Sécurité

**Le chiffrement Bitlocker est-il obligatoire ?**

-   Oui. Tous les lecteurs doivent être chiffrés avec une clé BitLocker.

**Formatez-vous les lecteurs avant de les renvoyer ?**

-   Non. Tous les lecteurs doivent être préparés avec BitLocker.

