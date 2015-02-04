<properties urlDisplayName="Azure Import/Export Service" pageTitle="Utilisation du service Import/Export pour transférer des données vers le stockage d'objets blob | Microsoft Azure" metaKeywords="" description="Découvrez comment créer des tâches d'importation et d'exportation dans le portail de gestion Azure pour transférer des données vers le stockage d'objets blob." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Using the Azure Import/Export Service to Transfer Data to Blob Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/25/2014" ms.author="tamram" />


# Transfert de données vers le stockage d'objets blob à l'aide du service Microsoft Azure Import/Export

Vous pouvez utiliser le service Microsoft Azure Import/Export pour transférer de grandes quantités de données de fichiers vers le stockage d'objets blob Azure lorsque le coût de l'opération de téléchargement via le réseau est prohibitif, voire irréalisable. Vous pouvez également utiliser ce service pour transférer de manière rapide et économique des volumes importants de données résidant dans le stockage d'objets Blob vers vos installations locales.

Pour transférer un jeu de données de fichiers volumineux vers le stockage d'objets blob, vous pouvez envoyer le ou les disques durs contenant ces données à un centre de données Azure, qui téléchargera vos données vers votre compte de stockage. De la même manière, pour exporter des données à partir d'un stockage d'objets blob, vous pouvez envoyer des disques durs vides à un centre de données Azure, qui se chargera de copier les données BLOB de votre compte de stockage sur vos disques durs avant de vous les renvoyer. Avant d'envoyer un lecteur, vous devez chiffrer les données qu'il contient ; le service Import/Export en fait de même avant de vous renvoyer vos données exportées.

Vous pouvez créer et gérer des tâches d'importation et d'exportation de l'une des deux façons suivantes :

- en utilisant le portail de gestion Azure ;
- en utilisant une interface REST communiquant avec le service.

Cet article offre une vue d'ensemble du service Import/Export et explique comment l'utiliser via le portail de gestion. Pour plus d'informations sur l'API REST, consultez la page [Référence de l'API REST du service Import/Export d'Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

## Vue d'ensemble du service Import/Export ##

Pour lancer le processus d'importation ou d'exportation vers ou à partir d'un stockage d'objets blob, commencez par créer une *tâche*. Il peut s'agir d'une *tâche d'importation* ou d'une *tâche d'exportation* :

- Une tâche d'importation vise à transférer des données locales vers des objets blob de votre compte de stockage Azure.
- Une tâche d'exportation vise à transférer des données stockées sous forme d'objets blob dans votre compte de stockage sur des disques durs qui vous sont ensuite expédiés.

Lorsque vous créez une tâche, vous avertissez le service Import/Export que vous allez expédier un ou plusieurs disques durs vers un centre de données Azure. Dans le cas d'une tâche d'importation, vous expédiez des disques durs contenant des données de fichiers. Dans le cas d'une tâche d'exportation, vous expédiez des disques durs vides.

Pour préparer l'envoi de votre lecteur pour une tâche d'importation, vous devez exécuter l'outil **Microsoft Azure Import/Export**. Celui-ci facilite la copie de vos données sur le disque, chiffre ces données avec BitLocker, puis génère les fichiers journaux du lecteur. Ces opérations vous seront décrites ultérieurement.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Les données contenues dans le disque dur doivent être chiffrées à l'aide du chiffrement de lecteur BitLocker. Cette opération permet de protéger vos données pendant qu'elles sont en transit. Dans le cas d'une tâche d'exportation, le service Import/Export chiffre les données avant de vous renvoyer le disque dur.</p>
</div>

Lorsque vous créez une tâche d'importation ou d'exportation, vous avez également besoin d'un *ID de lecteur*, qui est le numéro de série attribué au disque dur par le fabricant de lecteurs. L'ID de lecteur figure sur la façade extérieure du lecteur. 

<h3>Exigences et portée</h3>

1.	**Abonnement et comptes de stockage :** vous devez être titulaire d'un abonnement Azure et posséder un ou plusieurs comptes de stockage pour pouvoir utiliser le service Import/Export. Chaque tâche peut servir à transférer des données vers ou à partir d'un seul compte de stockage. Autrement dit, une tâche ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](../storage-create-storage-account/).
2.	**Disques durs :** seuls les disques durs SATA II/III de 3,5 pouces sont pris en charge par le service Import/Export. Les disques durs de plus de 4 To ne sont pas pris en charge. Dans le cas des tâches d'importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS. Vous pouvez raccorder un disque SATA II/III par voie externe à la plupart des ordinateurs à l'aide d'un adaptateur USB SATA II/III.
3.	**Chiffrement BitLocker :** toutes les données stockées sur les disques durs doivent être chiffrées à l'aide de BitLocker avec des clés de chiffrement protégées par des mots de passe numériques.
4.	**Cibles de stockage d'objets blob :** les données peuvent être téléchargées vers ou à partir d'objets blob de blocs ou de pages. 
5.	**Nombre de tâches :** un client peut avoir au maximum 20 tâches actives par compte de stockage.
6.	**Taille maximale d'une tâche :** la taille d'une tâche dépend de la capacité des disques durs utilisés et de la quantité maximale de données qu'un compte de stockage peut accueillir. Chaque tâche peut contenir au maximum 10 disques durs.

## Création d'une tâche d'importation dans le portail de gestion##

Créez une tâche d'importation pour avertir le service Import/Export que vous allez expédier au centre de données un ou plusieurs lecteurs contenant des données à importer dans votre compte de stockage.

<h3>Préparation des lecteurs</h3>

Avant de créer une tâche d'importation, préparez vos lecteurs à l'aide de l'outil Microsoft Azure Import/Export. Pour plus d'informations sur l'utilisation de l'outil Microsoft Azure Import/Export, consultez la [page de référence sur l'outil Microsoft Azure Import/Export](http://go.microsoft.com/fwlink/?LinkId=329032). Vous pouvez télécharger l'[outil Microsoft Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) sous forme de package autonome.
  
Pour préparer vos lecteurs, effectuez les trois étapes suivantes : 

1.	Déterminez les données à importer et le nombre de lecteurs dont vous avez besoin.
2.	Identifiez les objets blob de destination de vos données dans le service d'objets blob Azure.
3.	Utilisez l'outil Microsoft Azure Import/Export pour copier vos données sur un ou plusieurs disques durs.

L'outil Microsoft Azure Import/Export génère un fichier *journal de lecteur* lors de la préparation de chaque lecteur. Le fichier journal de lecteur est stocké sur votre ordinateur local, et non sur le lecteur lui-même. Vous serez amené à télécharger le fichier journal lors de la phase de création de la tâche d'importation. Le fichier journal d'un lecteur contient son ID, la clé BitLocker, ainsi que d'autres informations sur le lecteur.  

<h3>Création de la tâche d'importation</h3>

1.	Après avoir préparé votre lecteur, accédez à votre compte de stockage dans le portail de gestion, puis affichez le tableau de bord. Sous <strong>Aperçu rapide</strong>, cliquez sur <strong>Créer une tâche d'importation</strong>. 
 
2.	À l'étape 1 de l'Assistant, précisez que vous avez préparé votre lecteur et que le fichier journal du lecteur est disponible.
 
3.	À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'importation. Pour enregistrer les données de journal détaillées pour la tâche d'importation, activez l'option <strong>Enregistrer le journal détaillé dans mon conteneur d'objets blob " waimportexport ".</strong>.

4.	À l'étape 3, téléchargez les fichiers journaux de lecteur que vous avez obtenus à l'étape de préparation de lecteur. Vous devez télécharger un fichier pour chaque lecteur préparé.

	![Create import job - Step 3][import-job-03]

5.	À l'étape 4, attribuez un nom descriptif à la tâche d'importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. 	Le nom que choisissez vous servira à suivre la tâche pendant et après son exécution.

	Sélectionnez ensuite la région du centre de données dans la liste. Cette dernière indique à quel centre de données et à quelle adresse vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

6. 	À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d'importation terminée.

	Si vous avez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste. 

	Si vous n'avez pas encore de numéro de suivi, choisissez **Je fournirai mes informations d'expédition pour ce travail d'importation après envoi de mon colis**, puis terminez le processus d'importation.

7. Pour entrer le numéro de suivi après avoir expédié votre colis, revenez à la page **Importer/Exporter** de votre compte de stockage dans le portail de gestion, sélectionnez votre tâche dans la liste, puis choisissez **Informations d'expédition**. Parcourez l'Assistant, puis entrez votre numéro de suivi à l'étape 2.

	Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l'étape 2 de l'Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant. 

## Création d'une tâche d'exportation dans le portail de gestion##

Créez une tâche d'exportation pour avertir le service Import/Export que vous allez expédier un ou plusieurs lecteurs vides au centre de données, de sorte que les données puissent être exportées de votre compte de stockage vers les lecteurs, qui vous seront ensuite renvoyés.

1. 	Pour créer une tâche d'exportation, accédez à votre compte de stockage dans le portail de gestion, puis affichez le tableau de bord. 	Sous <strong>Aperçu rapide</strong>, cliquez sur <strong>Créer un travail d'exportation</strong>et suivez les instructions de l'Assistant.

2. 	À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'exportation. Pour enregistrer les données de journal détaillées pour la tâche d'exportation, activez l'option <strong>Enregistrer le journal détaillé dans le conteneur d'objets blob " waimportexport ".</strong>.

3.	À l'étape 3, indiquez les données d'objets blob que vous souhaitez exporter de votre compte de stockage vers le ou les lecteurs vides. Vous pouvez choisir d'exporter toutes les données d'objets blob contenues dans le compte de stockage ou indiquer les objets blob ou ensembles d'objets blob à exporter.

	![Create export job - Step 3][export-job-03]

	- Pour spécifier un objet blob à exporter, utilisez le sélecteur **Égal à**, puis indiquez le chemin d'accès relatif de l'objet blob en le faisant précéder du nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.
	- Pour spécifier tous les objets blob commençant par un préfixe, utilisez le sélecteur **Commence par**, puis spécifiez le préfixe en le faisant précéder d'une barre oblique (" / "). Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob.

	Le tableau suivant présente des exemples de chemins d'accès d'objet blob valides :

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
		<tbody>
			<tr>
				<td><strong>Sélecteur</strong></td>
				<td><strong>Chemin d'accès d'objet blob</strong></td>
				<td><strong>Description</strong></td>
			</tr>
			<tr>
				<td>Commence par</td>
				<td>/</td>
				<td>Exporte tous les objets blob présents dans le compte de stockage.</td>
			</tr>
			<tr>
				<td>Commence par</td>
				<td>/$root/</td>
				<td>Exporte tous les objets blob présents dans le conteneur racine.</td>
			</tr>
			<tr>
				<td>Commence par</td>
				<td>/book</td>
				<td>Exporte tous les objets blob dans un conteneur commençant par le préfixe <strong>book.</strong></td>
			</tr>
			<tr>
				<td>Commence par</td>
				<td>/music/</td>
				<td>Exporte tous les objets blob présents dans le conteneur <strong>music.</strong></td>
			</tr>
			<tr>
				<td>Commence par</td>
				<td>/music/love</td>
				<td>Exporte tous les objets blob qui commencent par le préfixe <strong>love</strong> dans le conteneur <strong>music</strong></td>
			</tr>
			<tr>
				<td>Égal à</td>
				<td>$root/logo.bmp</td>
				<td>Exporte l'objet blob <strong>logo.bmp</strong> dans le conteneur racine</td>
			</tr>
			<tr>
				<td>Égal à</td>
				<td>videos/story.mp4</td>
				<td>Exporte l'objet blob <strong>story.mp4</strong> dans le conteneur <strong>vidéos.</strong></td>
			</tr>
		</tbody>
	</table>


4.	À l'étape 4, attribuez un nom descriptif à la tâche d'exportation. Le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, il doit commencer par une lettre et ne peut pas contenir d'espaces.

	La région du centre de données indique à quel centre de données vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

5. 	À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d'exportation terminée.

	Si vous avez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste. 

	Si vous n'avez pas encore de numéro de suivi, sélectionnez <strong>Je fournirai mes informations d'expédition pour ce travail d'exportation après envoi de mon colis</strong>, puis terminez le processus d'exportation.

6. Pour entrer le numéro de suivi après avoir expédié votre colis, revenez à la page **Importer/Exporter** de votre compte de stockage dans le portail de gestion, sélectionnez votre tâche dans la liste, puis choisissez **Informations d'expédition**. Parcourez l'Assistant, puis entrez votre numéro de suivi à l'étape 2.

	Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l'étape 2 de l'Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant. 


## Suivi du statut des tâches dans le portail de gestion##

Vous pouvez suivre le statut de vos tâches d'importation ou d'exportation dans le portail de gestion. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Importer/Exporter**. La liste de vos tâches s'affiche sur la page. Vous pouvez filtrer la liste en fonction du statut des tâches, de leur nom, de leur type ou du numéro de suivi.

Le tableau ci-dessous indique la signification de chaque désignation de statut de tâche :

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td><strong>Statut de tâche</strong></td>
			<td><strong>Description</strong></td>
		</tr>
		<tr>
			<td>Creating</td>
			<td>Votre tâche a été créée, mais vous n'avez pas encore fourni vos détails d'expédition.</td>
		</tr>
		<tr>
			<td>Expédition</td>
			<td>Votre tâche a été créée et vous avez fourni vos détails d'expédition.</td>
		</tr>
		<tr>
			<td>Transferring</td>
			<td>Vos données sont en cours de transfert de votre disque dur (pour une tâche d'importation) ou vers ce dernier (pour une tâche d'exportation).</td>
		</tr>
		<tr>
			<td>Packaging</td>
			<td>Le transfert de vos données est terminé et votre disque dur est en cours de préparation pour vous être renvoyé.</td>
		</tr>
		<tr>
			<td>Complete</td>
			<td>Votre disque dur vous a été renvoyé.</td>
		</tr>
	</tbody>
</table>

## Affichage des clés BitLocker pour une tâche d'exportation ##

Dans le cas des tâches d'exportation, vous pouvez afficher et copier les clés BitLocker que le service a générées pour vos lecteurs. Celles-ci vous permettent de déchiffrer vos données exportées une fois que vous avez reçu les lecteurs en provenance du centre de données Azure. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Importer/Exporter**. Sélectionnez votre tâche d'exportation dans la liste, puis cliquez sur le bouton **Afficher les clés**. Les clés BitLocker s'affichent comme illustré ci-dessous :

![View BitLocker keys for export job][export-job-bitlocker-keys]

## Forum Aux Questions (FAQ) ##

<h3>Généralités</h3>

**Combien coûte le service Import/Export ?**

- Consultez la [page de tarification](http://go.microsoft.com/fwlink/?LinkId=329033) pour en savoir plus.

**Combien de temps faut-il compter pour que mes données soient importées ou exportées ?**

- Il faut compter le temps d'expédition des disques, plus le temps de copie des données, soit plusieurs heures par To de données.
 
**Quels sont les types d'interface pris en charge ?**

- Le service Import/Export prend en charge les disques durs SATA II/III internes de 3,5 pouces. Avant l'expédition, vous pouvez utiliser les convertisseurs suivants pour transférer les données des périphériques via les interfaces USB/SATA :
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE 

> [WACOM.NOTE] Si le convertisseur dont vous disposez n'est pas répertorié ci-dessus, vous pouvez tenter de l'utiliser pour exécuter l'outil Microsoft Azure Import/Export pour voir s'il fonctionne avec avant d'en acheter un qui soit pris en charge.

**Que faire si je souhaite importer ou exporter plus de 10 lecteurs ?**

- Le service Import/Export limite chaque tâche d'importation ou d'exportation au référencement de 10 lecteurs. Si vous souhaitez en expédier plus, vous pouvez créer plusieurs tâches.

**Que se passe-t-il si j'envoie par accident un disque dur non conforme au type pris en charge ?**

- Le centre de données Azure vous renvoie le lecteur non conforme au type pris en charge. Si seule une partie des lecteurs contenus dans le colis respecte la configuration requise, ceux-ci sont traités ; ceux qui ne la respectent vous sont renvoyés.

<h3>Gestion des tâches d'importation/exportation</h3>

**Qu'advient-il de mes tâches d'importation et d'exportation si je supprime mon compte de stockage Azure ?**

- Lorsque vous supprimez votre compte de stockage, toutes les tâches d'importation/exportation Azure sont supprimées en même temps que votre compte.  

**Puis-je annuler une tâche ?**

- Vous pouvez annuler une tâche dont le statut est Creating ou Shipping.

**Pendant combien de temps le statut des tâches terminées est-il consultable dans le portail de gestion ?**

- Le statut des tâches terminées est visible pendant 90 jours. Au-delà de ce délai, toutes les tâches terminées sont supprimées.

**Le chiffrement Bitlocker est-il obligatoire ?**

- Oui. Tous les lecteurs doivent être chiffrés avec une clé BitLocker.

**Formatez-vous les lecteurs avant de les renvoyer ?**

- Non. Tous les lecteurs doivent être préparés avec BitLocker.

<h3>Expédition</h3>

**À quels services d'expédition peut-on recourir ?**

- Pour les régions situées aux États-Unis et en Europe, seul [Federal Express](http://www.fedex.com/us/oadr/) (FedEx) est pris en charge. Tous les colis sont renvoyés via FedEx Ground ou FedEx International Economy.

- Pour les régions d'Asie, seul [DHL](http://www.dhl-welcome.com/Tutorial/) est pris en charge. Tous les colis sont renvoyés via DHL Express Worldwide.

	<div class="dev-callout">
	<strong>Important</strong>
	<p>Vous devez fournir votre numéro de suivi au service Azure Import/Export ; à défaut, votre tâche n'est pas traitée.</p>
	</div>

**Les retours d'expédition sont-ils facturés ?**

- Microsoft utilise le numéro de compte de transporteur que vous avez fourni lors de la création de la tâche pour expédier les lecteurs du centre de données vers votre adresse de retour. Veillez à fournir le numéro de compte du transporteur pris en charge dans la région du centre de données. Vous pouvez créer un compte de transporteur [FedEx](http://www.fedex.com/us/oadr/) (pour les États-Unis et l'Europe) ou [DHL](http://www.dhl-welcome.com/Tutorial/) (pour l'Asie) si vous n'en avez pas.

- Les frais de retour sont facturés sur le compte de votre transporteur et dépendent de ce dernier.

**Depuis et vers quelles destinations puis-je expédier mes données ?**

- Le service Import/Export prend en charge l'importation et l'exportation de données vers et à partir des comptes de stockage situés dans les régions suivantes :
	- Est des États-Unis 
	- Ouest des États-Unis 
	- Nord du centre des États-Unis 
	- Sud du centre des États-Unis 
	- Europe du Nord
	- Europe de l'Ouest
	- Est de l'Asie
	- Asie du Sud-Est

- L'adresse d'expédition qui vous sera communiquée sera située dans la région de votre compte de stockage. Par exemple, si vous habitez aux États-Unis et que votre compte de stockage est situé dans le centre de données Europe de l'Ouest, vous recevrez une adresse d'expédition située en Europe pour l'envoi de vos lecteurs.

	<div class="dev-callout">
	<strong>Important</strong>
	<p>Notez que le support physique que vous expédiez devra peut-être franchir des frontières. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d'expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d'atteindre Microsoft dans les délais.</p>
	</div>

- Lorsque vous expédiez vos colis, vous devez respecter les [conditions d'utilisation des services Microsoft Azure](http://azure.microsoft.com/fr-fr/support/legal/services-terms/). 

**Est-il possible d'acheter des lecteurs auprès de Microsoft pour des tâches d'importation/exportation ?**

- 	Non. Vous devez expédier vos propres lecteurs, aussi bien pour les tâches d'importation que pour les tâches d'exportation.

**Que doit inclure mon colis ?**

- Incluez uniquement vos disques durs. N'incluez pas d'accessoires tels que des câbles d'alimentation ou USB.



[import-job-03]: ./media/storage-import-export-service/import-job-03.png
[export-job-03]: ./media/storage-import-export-service/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service/export-job-bitlocker-keys.png

<!--HONumber=35.1-->
