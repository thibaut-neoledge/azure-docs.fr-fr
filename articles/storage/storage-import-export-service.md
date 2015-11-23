<properties 
	pageTitle="Utilisation du service Import/Export pour transférer des données vers le stockage d’objets blob | Microsoft Azure" 
	description="Découvrez comment créer des tâches d’importation et d’exportation dans le portail de gestion Azure pour transférer des données vers le stockage d’objets blob." 
	authors="tamram" 
	manager="adinah" 
	editor="" 
	services="storage" 
	documentationCenter=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2015" 
	ms.author="tamram"/>


# Transfert de données vers le stockage d’objets blob à l’aide du service Microsoft Azure Import/Export

## Vue d'ensemble

Vous pouvez utiliser le service Microsoft Azure Import/Export pour transférer de grandes quantités de données de fichiers vers le stockage d’objets blob Azure lorsque le coût de l’opération de téléchargement via le réseau est prohibitif, voire irréalisable. Vous pouvez également utiliser ce service pour transférer de manière rapide et économique des volumes importants de données résidant dans le stockage d'objets Blob vers vos installations locales.

Pour transférer un jeu de données de fichiers volumineux vers le stockage d'objets blob, vous pouvez envoyer le ou les disques durs contenant ces données à un centre de données Azure, qui téléchargera vos données vers votre compte de stockage. De la même manière, pour exporter des données à partir d'un stockage d'objets blob, vous pouvez envoyer des disques durs vides à un centre de données Azure, qui se chargera de copier les données BLOB de votre compte de stockage sur vos disques durs avant de vous les renvoyer. Avant d'envoyer un lecteur, vous devez chiffrer les données qu'il contient ; le service Import/Export en fait de même avant de vous renvoyer vos données exportées.

Vous pouvez créer et gérer des tâches d'importation et d'exportation de l'une des deux façons suivantes :

- en utilisant le portail de gestion Azure ;
- en utilisant une interface REST communiquant avec le service.

Cet article offre une vue d'ensemble du service Import/Export et explique comment l'utiliser via le portail de gestion. Pour plus d'informations sur l'API REST, consultez la page [Référence de l'API REST du service d'importation/exportation Azure](http://go.microsoft.com/fwlink/?LinkID=329099).

## Présentation du service Import/Export ##

Pour lancer le processus d'importation ou d'exportation vers ou à partir d'un stockage d'objets blob, commencez par créer une *tâche*. Il peut s'agir d'une *tâche d'importation* ou d'une *tâche d'exportation* :

- Une tâche d’importation vise à transférer des données locales vers des objets blob de votre compte de stockage Azure.
- Une tâche d'exportation vise à transférer des données stockées sous forme d'objets blob dans votre compte de stockage sur des disques durs qui vous sont ensuite expédiés.

Lorsque vous créez une tâche, vous avertissez le service Import/Export que vous allez expédier un ou plusieurs disques durs vers un centre de données Azure. Dans le cas d'une tâche d'importation, vous expédiez des disques durs contenant des données de fichiers. Dans le cas d'une tâche d'exportation, vous expédiez des disques durs vides.

Pour préparer l'envoi de votre lecteur pour une tâche d'importation, vous devez exécuter l'outil **Microsoft Azure Import/Export**. Celui-ci facilite la copie de vos données sur le disque, chiffre ces données avec BitLocker, puis génère les fichiers journaux du lecteur. Ces opérations vous seront décrites ultérieurement.

> [AZURE.NOTE]Les données contenues dans le disque dur doivent être chiffrées à l’aide du chiffrement de lecteur BitLocker. Cette opération permet de protéger vos données pendant qu'elles sont en transit. Dans le cas d'une tâche d'exportation, le service Import/Export chiffre les données avant de vous renvoyer le disque dur.

Lorsque vous créez une tâche d'importation ou d'exportation, vous avez également besoin d'un *ID de lecteur*, qui est le numéro de série attribué au disque dur par le fabricant de lecteurs. L'ID de lecteur figure sur la façade extérieure du lecteur.

### Exigences et portée

1.	**Abonnement et comptes de stockage :** vous devez être titulaire d'un abonnement Azure et posséder un ou plusieurs comptes de stockage pour pouvoir utiliser le service Import/Export. Chaque tâche peut servir à transférer des données vers ou à partir d'un seul compte de stockage. Autrement dit, une tâche ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage](storage-create-storage-account.md).
2.	**Disques durs :** seuls les disques durs internes SATA II/III de 3,5 pouces sont pris en charge par le service Import/Export. Les disques durs jusqu'à 6 To sont pris en charge. Dans le cas des tâches d'importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS. Vous pouvez raccorder un disque SATA II/III par voie externe à la plupart des ordinateurs à l’aide d’un adaptateur USB SATA II/III externe.
3.	**Chiffrement BitLocker :** toutes les données stockées sur les disques durs doivent être chiffrées à l’aide de BitLocker avec des clés de chiffrement protégées par des mots de passe numériques.
4.	**Cibles de stockage d’objets blob** : les données peuvent être téléchargées vers ou à partir d’objets blob de blocs ou de pages. 
5.	**Nombre de tâches :** un client peut avoir au maximum 20 tâches actives par compte de stockage.
6.	**Taille maximale des tâches :** la taille d'une tâche dépend de la capacité des disques durs utilisés et de la quantité maximale de données qu'un compte de stockage peut accueillir. Chaque tâche peut contenir au maximum 10 disques durs.

  >[AZURE.IMPORTANT]Les disques durs externes équipés d’un adaptateur USB ne sont pas pris en charge par ce service. Ne préparez pas de lecteur de disque dur. Le disque dans le boîtier externe ne peut pas non plus être utilisé pour l’importation de données. Utilisez un lecteur de disque dur **interne** SATA II/III de 3,5 pouces. Si vous ne pouvez pas connecter le disque SATA directement à votre ordinateur, utilisez un adaptateur SATA/USB externe. Consultez la liste des adaptateurs recommandés dans la section Forum aux questions.

## Création d'une tâche d'importation dans le portail de gestion##

Créez une tâche d'importation pour avertir le service Import/Export que vous allez expédier au centre de données un ou plusieurs lecteurs contenant des données à importer dans votre compte de stockage.

### Préparation des lecteurs

Avant de créer une tâche d'importation, préparez vos lecteurs à l'aide de l'outil Microsoft Azure Import/Export. Pour plus d'informations sur l'utilisation de l'outil Microsoft Azure Import/Export, consultez la [page de référence sur l'outil Microsoft Azure Import/Export](http://go.microsoft.com/fwlink/?LinkId=329032). Vous pouvez télécharger l'[outil Microsoft Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) comme package autonome.
  
Pour préparer vos lecteurs, effectuez les trois étapes suivantes :

1.	Déterminez les données à importer et le nombre de lecteurs dont vous avez besoin.
2.	Identifiez les objets blob de destination de vos données dans le service d'objets blob Azure.
3.	Utilisez l'outil Microsoft Azure Import/Export pour copier vos données sur un ou plusieurs disques durs.

L'outil Microsoft Azure Import/Export génère un fichier *journal de lecteur* lors de la préparation de chaque lecteur. Le fichier journal de lecteur est stocké sur votre ordinateur local, et non sur le lecteur lui-même. Vous serez amené à télécharger le fichier journal lors de la phase de création de la tâche d'importation. Le fichier journal d'un lecteur contient son ID, la clé BitLocker, ainsi que d'autres informations sur le lecteur.

### Création de la tâche d'importation

1.	Après avoir préparé votre lecteur, accédez à votre compte de stockage dans le portail de gestion, puis affichez le tableau de bord. Sous **Quick Glance**, cliquez sur **Create an Import Job**. 
 
2.	À l'étape 1 de l'Assistant, précisez que vous avez préparé votre lecteur et que le fichier journal du lecteur est disponible.
 
3.	À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'importation. Pour enregistrer des données de journal détaillées pour la tâche d'importation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.

4.	À l'étape 3, téléchargez les fichiers journaux de lecteur que vous avez obtenus à l'étape de préparation de lecteur. Vous devez télécharger un fichier pour chaque lecteur préparé.

	![Créer une tâche d'importation - Étape 3][import-job-03]

5.	À l’étape 4, attribuez un nom descriptif à la tâche d’importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que choisissez vous servira à suivre la tâche pendant et après son exécution.

	Sélectionnez ensuite la région du centre de données dans la liste. Cette dernière indique à quel centre de données et à quelle adresse vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

6. 	À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d'importation terminée.

	Si vous avez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste.

	Si vous n'avez pas encore de numéro de suivi, choisissez **I will provide my shipping information for this import job once I have shipped my package**, puis terminez le processus d'importation.

7. Pour entrer le numéro de suivi après avoir expédié votre colis, revenez à la page **Importer/Exporter** de votre compte de stockage dans le portail de gestion, sélectionnez votre tâche dans la liste, puis choisissez **Informations d'expédition**. Parcourez l’Assistant, puis entrez votre numéro de suivi à l’étape 2.
	
	Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera.

	Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l’étape 2 de l’Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant.

## Création d'une tâche d'exportation dans le portail de gestion##

Créez une tâche d'exportation pour avertir le service Import/Export que vous allez expédier un ou plusieurs lecteurs vides au centre de données, de sorte que les données puissent être exportées de votre compte de stockage vers les lecteurs, qui vous seront ensuite renvoyés.

1. 	Pour créer une tâche d'exportation, accédez à votre compte de stockage dans le portail de gestion, puis affichez le tableau de bord. Sous **Aperçu rapide**, cliquez sur **Créer un travail d'exportation**, puis parcourez les étapes de l'Assistant.

2. 	À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'exportation. Pour enregistrer des données de journal détaillées pour la tâche d'exportation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.

3.	À l'étape 3, indiquez les données d'objets blob que vous souhaitez exporter de votre compte de stockage vers le ou les lecteurs vides. Vous pouvez choisir d’exporter toutes les données d’objets blob contenues dans le compte de stockage ou indiquer les objets blob ou ensembles d’objets blob à exporter.

	![Créer une tâche d'exportation - Étape 3][export-job-03]

	- Pour spécifier un objet blob à exporter, utilisez le sélecteur **Equal To**, puis indiquez le chemin d'accès relatif de l'objet blob en le faisant précéder du nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.
	- Pour spécifier tous les objets blob commençant par un préfixe, utilisez le sélecteur **Starts With**, puis spécifiez le préfixe en le faisant précéder d'une barre oblique (« / »). Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob.

	Le tableau suivant présente des exemples de chemins d'accès d'objet blob valides :

	Sélecteur|Chemin d'accès d'objet blob|Description
	---|---|---
	Starts With|/|Exporte tous les objets blob présents dans le compte de stockage.
	Starts With|/$root/|Exporte tous les objets blob présents dans le conteneur racine.
	Starts With|/book|Exporte tous les objets blob présents dans un conteneur commençant par le préfixe **book**.
	Starts With|/music/|Exporte tous les objets blob présents dans le conteneur **music**.
	Starts With|/music/love|Exporte tous les objets blob présents dans le conteneur **music** qui commence par le préfixe **love**.
	Equal To|$root/logo.bmp|Exporte l'objet blob **logo.bmp** présent dans le conteneur racine.
	Equal To|videos/story.mp4|Exporte l'objet blob **story.mp4** présent dans le conteneur **videos**.


4.	À l'étape 4, attribuez un nom descriptif à la tâche d'exportation. Le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, il doit commencer par une lettre et ne peut pas contenir d'espaces.

	La région du centre de données indique à quel centre de données vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

5. 	À l'étape 5, sélectionnez votre transporteur dans la liste, puis entrez son numéro de compte. Microsoft utilise ce compte pour réexpédier vos lecteurs une fois la tâche d'exportation terminée.

	Si vous avez un numéro de suivi, entrez-le après avoir sélectionné le transporteur dans la liste.

	Si vous n'avez pas encore de numéro de suivi, choisissez **Je fournirai mes informations d'expédition pour ce travail d'exportation après envoi de mon colis**, puis terminez le processus d'exportation.

6. Pour entrer le numéro de suivi après avoir expédié votre colis, revenez à la page **Importer/Exporter** de votre compte de stockage dans le portail de gestion, sélectionnez votre tâche dans la liste, puis choisissez **Informations d'expédition**. Parcourez l’Assistant, puis entrez votre numéro de suivi à l’étape 2.
	
	Si le numéro de suivi n'est pas mis à jour dans les 2 semaines de création de la tâche, cette dernière expirera.

	Si la tâche a le statut Création, Expédition ou Transfert, vous pouvez également mettre à jour le numéro de compte du transporteur à l’étape 2 de l’Assistant. Une fois que la tâche a le statut Emballage, vous ne pouvez plus mettre à jour le numéro de compte de transporteur correspondant.

> [AZURE.NOTE]Si l'objet blob à exporter est en cours d'utilisation au moment de la copie sur le disque dur, le service d'importation/exportation Azure prend un instantané de l'objet blob et copie la capture instantanée.

## Suivi du statut des tâches dans le portail de gestion##

Vous pouvez suivre le statut de vos tâches d'importation ou d'exportation dans le portail de gestion. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Import/Export**. La liste de vos tâches s'affiche sur la page. Vous pouvez filtrer la liste en fonction du statut des tâches, de leur nom, de leur type ou du numéro de suivi.

Le tableau ci-dessous indique la signification de chaque désignation de statut de tâche :

Statut de tâche|Description
---|---
Creating|Votre tâche a été créée, mais vous n'avez pas encore fourni vos détails d'expédition.
Shipping|Votre tâche a été créée et vous avez fourni vos détails d'expédition.
Transferring|Vos données sont en cours de transfert de votre disque dur (pour une tâche d'importation) ou vers ce dernier (pour une tâche d'exportation).
Packaging|Le transfert de vos données est terminé et votre disque dur est en cours de préparation pour vous être renvoyé.
Complete|Votre disque dur vous a été renvoyé.


## Affichage des clés BitLocker pour une tâche d'exportation ##

Dans le cas des tâches d'exportation, vous pouvez afficher et copier les clés BitLocker que le service a générées pour vos lecteurs. Celles-ci vous permettent de déchiffrer vos données exportées une fois que vous avez reçu les lecteurs en provenance du centre de données Azure. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Import/Export**. Sélectionnez votre tâche d'exportation dans la liste, puis cliquez sur le bouton **View Keys**. Les clés BitLocker s’affichent comme illustré ci-dessous :

![Afficher les clés BitLocker pour une tâche d'exportation][export-job-bitlocker-keys]

## Forum Aux Questions (FAQ) ##

### Généralités

**Combien coûte le service Import/Export ?**

- Consultez cette [page](http://go.microsoft.com/fwlink/?LinkId=329033) pour obtenir des informations sur la tarification.

**Combien de temps faut-il compter pour que mes données soient importées ou exportées ?**

- Il faut compter le temps d’expédition des disques, plus le temps de copie des données, soit plusieurs heures par To de données.
 
**Quels sont les types d’interface pris en charge ?**

- Le service Import/Export prend en charge les disques durs SATA II/III internes de 3,5 pouces. Avant l'expédition, vous pouvez utiliser les convertisseurs suivants pour transférer les données des périphériques via les interfaces USB/SATA :
	- Anker 68UPSATAA-02BU
	- Anker 68UPSHHDS-BU
	- Startech SATADOCK22UE 

> [AZURE.NOTE]Si le convertisseur dont vous disposez n’est pas répertorié ci-dessus, vous pouvez tenter de l’utiliser pour exécuter l’outil Microsoft Azure Import/Export pour voir s’il fonctionne avec avant d’en acheter un qui soit pris en charge.

- Les lecteurs de disque dur externes équipés d’un adaptateur USB ne sont pas pris en charge.

**Que faire si je souhaite importer ou exporter plus de 10 lecteurs ?**

- Le service Import/Export limite chaque tâche d’importation ou d’exportation au référencement de 10 lecteurs. Si vous souhaitez en expédier plus, vous pouvez créer plusieurs tâches.

**Que se passe-t-il si j’envoie par accident un disque dur non conforme au type pris en charge ?**

- Le centre de données Azure vous renvoie le lecteur non conforme au type pris en charge. Si seule une partie des lecteurs contenus dans le colis respecte la configuration requise, ceux-ci sont traités ; ceux qui ne la respectent vous sont renvoyés.

### Gestion des tâches d’importation/exportation

**Qu’advient-il de mes tâches d’importation et d’exportation si je supprime mon compte Azure Storage ?**

- Lorsque vous supprimez votre compte de stockage, toutes les tâches d’importation/exportation Azure sont supprimées en même temps que votre compte.  

**Puis-je annuler une tâche ?**

- Vous pouvez annuler une tâche dont le statut est Creating ou Shipping.

**Pendant combien de temps le statut des tâches terminées est-il consultable dans le portail de gestion ?**

- Le statut des tâches terminées est visible pendant 90 jours. Au-delà de ce délai, toutes les tâches terminées sont supprimées.

**Le chiffrement Bitlocker est-il obligatoire ?**

- Oui. Tous les lecteurs doivent être chiffrés avec une clé BitLocker.

**Formatez-vous les lecteurs avant de les renvoyer ?**

- Non. Tous les lecteurs doivent être préparés avec BitLocker.
 
**Ai-je besoin d’effectuer la préparation du disque lors de la création d'une tâche d'exportation ?**
- Non, mais certaines vérifications préalables sont recommandées. Vérifiez le nombre de disques requis à l'aide de la commande [Aperçu de l’exportation](https://msdn.microsoft.com/library/azure/dn722414.aspx) de l’outil Azure Import/Export. Celle-ci vous permet d'afficher un aperçu de l'utilisation du disque pour les objets BLOB que vous avez sélectionnés, en fonction de la taille des disques que vous voulez utiliser. Vérifiez également que vous pouvez lire/modifier le contenu du disque dur qui sera utilisé pour la tâche d'exportation.

### Expédition

**À quels services d’expédition peut-on recourir ?**

- Pour les régions situées aux États-Unis et en Europe, seul [Federal Express](http://www.fedex.com/us/oadr/) (FedEx) est pris en charge. Tous les colis sont renvoyés via FedEx Ground ou FedEx International Economy.

- Pour les régions situées en Asie, seul [DHL](http://www.dhl-welcome.com/Tutorial/) est pris en charge. Tous les colis sont renvoyés via DHL Express Worldwide.

	> [AZURE.IMPORTANT]Vous devez fournir votre numéro de suivi au service Azure Import/Export ; à défaut, votre tâche n’est pas traitée.

**Les retours d’expédition sont-ils facturés ?**

- Microsoft utilise le numéro de compte de transporteur que vous avez fourni lors de la création de la tâche pour expédier les lecteurs du centre de données vers votre adresse de retour. Veillez à fournir le numéro de compte du transporteur pris en charge dans la région du centre de données. Vous pouvez créer un compte de transporteur [FedEx](http://www.fedex.com/us/oadr/) (pour les États-Unis et l'Europe) ou [DHL](http://www.dhl-welcome.com/Tutorial/) (Asie) si vous n'en avez pas.

- Les frais de retour sont facturés sur le compte de votre transporteur et dépendent de ce dernier.

**Depuis et vers quelles destinations puis-je expédier mes données ?**

- Le service Import/Export prend en charge l’importation et l’exportation de données vers et à partir des comptes de stockage situés dans les régions suivantes :
	- Est des États-Unis 
	- Ouest des États-Unis 
	- États-Unis - partie centrale septentrionale 
	- États-Unis - partie centrale méridionale 
	- Europe du Nord
	- Europe de l'Ouest
	- Est de l'Asie
	- Asie du Sud-Est

- L'adresse d'expédition qui vous sera communiquée sera située dans la région de votre compte de stockage. Par exemple, si vous habitez aux États-Unis et que votre compte de stockage est situé dans le centre de données Europe de l’Ouest, vous recevrez une adresse d’expédition située en Europe pour l’envoi de vos lecteurs.

	> [AZURE.IMPORTANT]Notez que le support physique que vous expédiez devra peut-être franchir des frontières. Vous êtes responsable de l'application des lois applicables lorsque vous importez et/ou exportez vos données et supports physiques. Avant d'expédier le support physique, demandez à vos conseillers juridiques de vérifier que vos supports multimédias et données peuvent être envoyés légalement vers le centre de données identifié. Cela vous assurera d'atteindre Microsoft dans les délais.

- Lorsque vous expédiez vos colis, vous devez respecter les [conditions d'utilisation des services Microsoft Azure](http://azure.microsoft.com/support/legal/services-terms/).

**Est-il possible d’acheter des lecteurs auprès de Microsoft pour des tâches d’importation/exportation ?**

- 	Non. Vous devez expédier vos propres lecteurs, aussi bien pour les tâches d’importation que pour les tâches d’exportation.

**Que doit inclure mon colis ?**

- Incluez uniquement vos disques durs. N’incluez pas d’accessoires tels que des câbles d’alimentation ou USB.



[import-job-03]: ./media/storage-import-export-service/import-job-03.png
[export-job-03]: ./media/storage-import-export-service/export-job-03.png
[export-job-bitlocker-keys]: ./media/storage-import-export-service/export-job-bitlocker-keys.png
 

<!---HONumber=Nov15_HO3-->