<properties linkid="manage-services-import-export" urlDisplayName="Azure Import/Export Service" pageTitle="Using import/export to transfer data to Blob Storage | Microsoft Azure" metaKeywords="" description="Learn how to create import and export jobs in the Azure Management Portal to transfer data to blob storage." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Using the Azure Import/Export Service to Transfer Data to Blob Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Transfert de données vers le stockage d'objets blob à l'aide du service Microsoft Azure Import/Export

Vous pouvez utiliser le service Microsoft Azure Import/Export pour transférer de grandes quantités de données de fichiers vers le stockage d'objets blob Azure lorsque le coût de l'opération de téléchargement via le réseau est prohibitif, voire irréalisable. Vous pouvez également utiliser ce service pour transférer de manière rapide et économique des volumes importants de données résidant dans le stockage d'objets Blob vers vos installations locales.

Pour transférer un jeu de données de fichiers volumineux vers le stockage d'objets blob, vous pouvez envoyer le ou les disques durs contenant ces données à un centre de données Azure, qui téléchargera vos données vers votre compte de stockage. De la même manière, pour exporter des données à partir d'un stockage d'objets blob, vous pouvez envoyer des disques durs vides à un centre de données Azure, qui se chargera de copier les données BLOB de votre compte de stockage sur vos disques durs avant de vous les renvoyer. Avant d'envoyer un lecteur, vous devez chiffrer les données qu'il contient ; le service Import/Export en fait de même avant de vous renvoyer vos données exportées.

Vous pouvez créer et gérer des tâches d'importation et d'exportation de l'une des deux façons suivantes :

-   en utilisant le portail de gestion Azure ;
-   en utilisant une interface REST communiquant avec le service.

Cet article offre une vue d'ensemble du service Import/Export et explique comment l'utiliser via le portail de gestion. Pour plus d'informations sur l'API REST, consultez la page [Référence de l'API REST du service d'importation/exportation Azure][].

## Vue d'ensemble du service Import/Export

Pour lancer le processus d'importation ou d'exportation vers ou à partir d'un stockage d'objets blob, commencez par créer une *tâche*. Il peut s'agir d'une *tâche d'importation* ou d'une *tâche d'exportation* :

-   Une tâche d'importation vise à transférer des données locales vers des objets blob de votre compte de stockage Azure.
-   Une tâche d'exportation vise à transférer des données stockées sous forme d'objets blob dans votre compte de stockage sur des disques durs qui vous sont ensuite expédiés.

Lorsque vous créez une tâche, vous avertissez le service Import/Export que vous allez expédier un ou plusieurs disques durs vers un centre de données Azure. Dans le cas d'une tâche d'importation, vous expédiez des disques durs contenant des données de fichiers. Dans le cas d'une tâche d'exportation, vous expédiez des disques durs vides.

Pour préparer l'envoi de votre lecteur pour une tâche d'importation, vous devez exécuter l'outil **Microsoft Azure Import/Export Tool**. Celui-ci facilite la copie de vos données sur le lecteur, chiffre ces données avec BitLocker, puis génère les fichiers journaux du lecteur. Ces opérations vous seront décrites ultérieurement.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Les donn&eacute;es contenues dans le disque dur doivent &ecirc;tre chiffr&eacute;es &agrave; l'aide du chiffrement de lecteur BitLocker. Cette op&eacute;ration permet de prot&eacute;ger vos donn&eacute;es pendant qu'elles sont en transit. Dans le cas d'une t&acirc;che d'exportation, le service Import/Export chiffre les donn&eacute;es avant de vous renvoyer le disque dur.</p>
</div>

Lorsque vous créez une tâche d'importation ou d'exportation, vous avez également besoin d'un *ID de lecteur*, qui est le numéro de série attribué au disque dur par le fabricant de lecteurs. L'ID de lecteur figure sur la façade extérieure du lecteur.

### Exigences et portée

1.  **Abonnement et comptes de stockage :** vous devez être titulaire d'un abonnement Azure et posséder un ou plusieurs comptes de stockage pour pouvoir utiliser le service Import/Export. Chaque tâche peut servir à transférer des données vers ou à partir d'un seul compte de stockage. Autrement dit, une tâche ne peut pas englober plusieurs comptes de stockage. Pour plus d'informations sur la création d'un compte de stockage, consultez la page [Création d'un compte de stockage][].
2.  **Disques durs :** seuls les disques durs SATA II/III de 3,5 pouces sont pris en charge par le service Import/Export. Les disques durs de plus de 4 To ne sont pas pris en charge. Dans le cas des tâches d'importation, seul le premier volume de données du lecteur est traité. Il doit être formaté avec NTFS. Vous pouvez raccorder un disque dur SATA II/III par voie externe à la plupart des ordinateurs à l'aide d'un adaptateur USB SATA II//III.
3.  **Chiffrement BitLocker :** toutes les données stockées sur les disques durs doivent être chiffrées à l'aide de BitLocker avec des clés de chiffrement protégées par des mots de passe numériques.
4.  **Cibles de stockage d'objets blob :** les données peuvent être téléchargées vers ou à partir d'objets blob de blocs ou de pages.
5.  **Nombre de tâches :** un client peut avoir au maximum 20 tâches actives par compte de stockage.
6.  **Taille maximale d'une tâche :** la taille d'une tâche dépend de la capacité des disques durs utilisés et de la quantité maximale de données qu'un compte de stockage peut accueillir. Chaque tâche peut contenir au maximum 10 disques durs.

## Création d'une tâche d'importation dans le portail de gestion

Créez une tâche d'importation pour avertir le service Import/Export que vous allez expédier au centre de données un ou plusieurs lecteurs contenant des données à importer dans votre compte de stockage.

### Préparation des lecteurs

Avant de créer une tâche d'importation, préparez vos lecteurs à l'aide de l'outil Microsoft Azure Import/Export Tool. Pour plus d'informations sur l'utilisation de l'outil Microsoft Azure Import/Export Tool, consultez la page [Référence de l'outil Microsoft Azure Import/Export Tool][]. Vous pouvez télécharger l'outil [Microsoft Azure Import/Export Tool][] en tant que package autonome.

Pour préparer vos lecteurs, effectuez les trois étapes suivantes :

1.  Déterminez les données à importer et le nombre de lecteurs dont vous avez besoin.
2.  Identifiez les objets blob de destination de vos données dans le service d'objets blob Azure.
3.  Utilisez l'outil Microsoft Azure Import/Export Tool pour copier vos données sur un ou plusieurs disques durs.

L'outil Microsoft Azure Import/Export Tool génère un fichier *journal de lecteur* lors de la préparation de chaque lecteur. Le fichier journal de lecteur est stocké sur votre ordinateur local, et non sur le lecteur lui-même. Vous serez amené à télécharger le fichier journal lors de la phase de création de la tâche d'importation. Le fichier journal d'un lecteur contient son ID, la clé BitLocker, ainsi que d'autres informations sur le lecteur.

### Création de la tâche d'importation

1.  Après avoir préparé votre lecteur, accédez à votre compte de stockage sur le portail de gestion, puis affichez le tableau de bord. Sous **Quick Glance**, cliquez sur **Create an Import Job**.

2.  À l'étape 1 de l'Assistant, précisez que vous avez préparé votre lecteur et que le fichier journal correspondant est disponible.

3.  À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'importation. Si vous souhaitez enregistrer des données de journal détaillées pour la tâche d'importation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.

4.  À l'étape 3, téléchargez les fichiers journaux de lecteur que vous avez obtenus à l'étape de préparation de lecteur. Vous devrez télécharger un fichier pour chaque lecteur préparé.

    ![Créer une tâche d'importation - Étape 3][]

5.  À l'étape 4, attribuez un nom descriptif à la tâche d'importation. Notez que le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, qu'il doit commencer par une lettre et qu'il ne peut pas contenir d'espaces. Le nom que choisissez vous servira à suivre la tâche pendant et après son exécution.

    Sélectionnez ensuite la région de votre centre de données dans la liste. La région du centre de données indique à quel centre de données et à quelle adresse vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

6.  À l'étape 5, sélectionnez votre transporteur de retour dans la liste, puis entrez le numéro de votre compte transporteur. Microsoft utilisera ce compte pour vous expédier vos lecteurs une fois votre tâche d'importation terminée.

    Si vous connaissez votre numéro de suivi, entrez-le après avoir sélectionné votre transporteur dans la liste.

    Si vous n'avez pas encore de numéro de suivi, choisissez **I will provide my shipping information for this import job once I have shipped my package**, puis terminez le processus d'importation.

7.  Pour entrer votre numéro de suivi après l'expédition de votre colis, revenez à la page **Import/Export** de votre compte de stockage dans le portail de gestion, sélectionnez votre tâche dans la liste, puis choisissez **Informations d'expédition**. Parcourez l'Assistant et entrez votre numéro de suivi à l'étape 2.

    Si la tâche est en cours de création, d'expédition ou de transfert, vous pouvez également mettre à jour le numéro de votre compte transporteur à l'étape 2 de l'Assistant. Une fois la tâche en cours de packaging, vous ne pouvez plus mettre ce numéro à jour pour cette tâche.

## Création d'une tâche d'exportation dans le portail de gestion

Créez une tâche d'exportation pour avertir le service Import/Export que vous allez expédier un ou plusieurs lecteurs vides au centre de données, de sorte que les données puissent être exportées de votre compte de stockage vers les lecteurs, qui vous seront ensuite renvoyés.

1.  Pour créer une tâche d'exportation, accédez à votre compte de stockage dans le portail de gestion, puis affichez le tableau de bord. Sous **Aperçu rapide**, cliquez sur **Créer un travail d'exportation**, puis parcourez les étapes de l'Assistant.

2.  À l'étape 2, fournissez les coordonnées de la personne responsable de cette tâche d'exportation. Si vous souhaitez enregistrer des données de journal détaillées pour la tâche d'exportation, activez l'option **Enregistrer le journal détaillé dans le conteneur d'objets blob 'waimportexport'**.

3.  À l'étape 3, indiquez les données d'objet blob que vous souhaitez exporter de votre compte de stockage vers le ou les lecteurs vides. Vous pouvez choisir d'exporter toutes les données d'objet blob contenues dans le compte de stockage ou indiquer les objets blob ou les ensembles d'objets blob que vous souhaitez exporter.

    ![Créer une tâche d'exportation - Étape 3][]

    -   Pour spécifier un objet blob à exporter, utilisez le sélecteur **Equal To**, puis indiquez le chemin d'accès relatif de l'objet blob en le faisant précéder du nom du conteneur. Utilisez *$root* pour spécifier le conteneur racine.
    -   Pour spécifier tous les objets blob commençant par un préfixe, utilisez le sélecteur **Starts With**, puis spécifiez le préfixe en le faisant précéder d'une barre oblique (« / »). Il peut s'agir du préfixe du nom de conteneur, du nom de conteneur complet, ou du nom de conteneur complet suivi du préfixe du nom d'objet blob.

    Le tableau suivant présente des exemples de chemins d'accès d'objet blob valides :

	<table border="1">
    <tr><th>Sélecteur</th><th>Chemin d'accès d'objet blob</th><th>Description</th></tr>
    <tr><td>Starts With   </td><td>/                               </td><td>Exporte tous les objets blob présents dans le compte de stockage.                                       </td></tr>
    <tr><td>Starts With   </td><td>/$root/                         </td><td>Exporte tous les objets blob présents dans le conteneur racine.                                         </td></tr>
    <tr><td>Starts With   </td><td>/book                           </td><td>Exporte tous les objets blob présents dans un conteneur commençant par le préfixe <strong>book</strong>.             </td></tr>
    <tr><td>Starts With   </td><td>/music/                         </td><td>Exporte tous les objets blob présents dans le conteneur <strong>music</strong>.                                      </td></tr>
    <tr><td>Starts With   </td><td>/music/love                     </td><td>Exporte tous les objets blob présents dans le conteneur <strong>music</strong> qui commence par le préfixe <strong>love</strong>. </td></tr>
    <tr><td>Equal To      </td><td>$root/logo.bmp                  </td><td>Exporte l'objet blob <strong>logo.bmp</strong> présent dans le conteneur racine.                                     </td></tr>
    <tr><td>Equal To      </td><td>videos/story.mp4                </td><td>Exporte l'objet blob <strong>story.mp4</strong> présent dans le conteneur <strong>videos</strong>.                                </td></tr>
	</table>
	
4.  À l'étape 4, attribuez un nom descriptif à la tâche d'exportation. Le nom que vous entrez ne peut contenir que des minuscules, des chiffres, des tirets et des traits de soulignement, il doit commencer par une lettre et il ne peut pas contenir d'espaces.

    La région du centre de données indique à quel centre de données vous devez expédier votre colis. Consultez le Forum Aux Questions ci-après pour plus d'informations.

5.  À l'étape 5, sélectionnez votre transporteur de retour dans la liste, puis entrez le numéro de votre compte transporteur. Microsoft utilisera ce compte pour vous expédier vos lecteurs une fois votre tâche d'exportation terminée.

    Si vous connaissez votre numéro de suivi, entrez-le après avoir sélectionné votre transporteur dans la liste.

    Si vous n'avez pas encore de numéro de suivi, choisissez **Je fournirai mes informations d'expédition pour ce travail d'exportation après envoi de mon colis.**, puis terminez le processus d'exportation.

6.  Pour entrer votre numéro de suivi après l'expédition de votre colis, revenez à la page **Import/Export** de votre compte de stockage dans le portail de gestion, sélectionnez votre tâche dans la liste, puis choisissez **Informations d'expédition**. Parcourez l'Assistant et entrez votre numéro de suivi à l'étape 2.

    Si la tâche est en cours de création, d'expédition ou de transfert, vous pouvez également mettre à jour le numéro de votre compte transporteur à l'étape 2 de l'Assistant. Une fois la tâche en cours de packaging, vous ne pouvez plus mettre ce numéro à jour pour cette tâche.

## Suivi du statut des tâches dans le portail de gestion

Vous pouvez suivre le statut de vos tâches d'importation ou d'exportation dans le portail de gestion. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Import/Export**. La liste de vos tâches s'affiche sur la page. Vous pouvez filtrer la liste en fonction du statut des tâches, de leur nom, de leur type ou du numéro de suivi.

Le tableau ci-dessous indique la signification de chaque désignation de statut de tâche :

<table border="1">
<tr><th> Statut de tâche </th><th> Description                                                                                                                              </th></tr>
<tr><td> Creating            </td><td> Votre tâche a été créée, mais vous n'avez pas encore fourni vos détails d'expédition.                                                        </td></tr>
<tr><td> Expédition          </td><td> Votre tâche a été créée et vous avez fourni vos détails d'expédition.                                                                        </td></tr>
<tr><td> Transferring        </td><td> Vos données sont en cours de transfert de votre disque dur (pour une tâche d'importation) ou vers ce dernier (pour une tâche d'exportation). </td></tr>
<tr><td> Packaging           </td><td> Le transfert de vos données est terminé et votre disque dur est en cours de préparation pour vous être renvoyé.                              </td></tr>
<tr><td> Complete            </td><td> Votre disque dur vous a été renvoyé.                                                                                                         </td></tr>
</table>

## Affichage des clés BitLocker pour une tâche d'exportation

Dans le cas des tâches d'exportation, vous pouvez afficher et copier les clés BitLocker que le service a générées pour vos lecteurs. Celles-ci vous permettent de déchiffrer vos données exportées une fois que vous avez reçu les lecteurs en provenance du centre de données Azure. Accédez à votre compte de stockage dans le portail de gestion, puis cliquez sur l'onglet **Import/Export**. Sélectionnez votre tâche d'exportation dans la liste, puis cliquez sur le bouton **View Keys**. Les clés BitLocker s'affichent comme illustré ci-dessous :

![Afficher les clés BitLocker pour une tâche d'exportation][]

## Forum Aux Questions (FAQ)

### Généralités

**Combien coûte le service Import/Export ?**

-   Consultez cette [page][] pour obtenir des informations sur la tarification.

**Combien de temps faut-il compter pour que mes données soient importées ou exportées ?**

-   Il faut compter le temps d'expédition des disques, plus le temps de copie des données, soit plusieurs heures par To de données.

**Quels sont les types d'interface pris en charge ?**

-   Le service Import/Export prend en charge les disques durs SATA II/III de 3,5 pouces. Avant l'expédition, vous pouvez utiliser les convertisseurs suivants pour transférer les données des périphériques via les interfaces USB/SATA :

    -   Anker 68UPSATAA-02BU
    -   Anker 68UPSHHDS-BU
    -   Startech SATADOCK22UE

> [WACOM.NOTE] Si vous disposez d'un convertisseur non répertorié ci-dessus, vous pouvez tenter d'exécuter l'outil Microsoft Azure Import/Export Tool avec ce convertisseur lors de la préparation du lecteur avant d'acheter un convertisseur pris en charge.

**Que faire si je souhaite importer ou exporter plus de 10 lecteurs ?**

-   Le service Import/Export limite chaque tâche d'importation ou d'exportation au référencement de 10 lecteurs. Si vous souhaitez en expédier plus, vous pouvez créer plusieurs tâches.

**Que se passe-t-il si j'envoie par accident un disque dur non conforme au type pris en charge ?**

-   Le centre de données Azure vous renvoie le lecteur non conforme au type pris en charge. Si seule une partie des lecteurs contenus dans le colis respecte la configuration requise, ceux-ci sont traités ; ceux qui ne la respectent vous sont renvoyés.

### Gestion des tâches d'importation/exportation

**Qu'advient-il de mes tâches d'importation et d'exportation si je supprime mon compte de stockage Azure ?**

-   Lorsque vous supprimez votre compte de stockage, toutes les tâches d'importation/exportation Azure sont supprimées en même temps que votre compte.

**Puis-je annuler une tâche ?**

-   Vous pouvez annuler une tâche dont le statut est Creating ou Shipping.

**Pendant combien de temps le statut des tâches terminées est-il consultable dans le portail de gestion ?**

-   Le statut des tâches terminées est visible pendant 90 jours. Passé ce délai, toutes les tâches terminées sont supprimées.

**Le chiffrement Bitlocker est-il obligatoire ?**

-   Oui. Tous les lecteurs doivent être chiffrés avec une clé BitLocker.

**Formatez-vous les lecteurs avant de les renvoyer ?**

-   Non. Tous les lecteurs doivent être préparés avec BitLocker.

### Expédition

**À quels services d'expédition peut-on recourir ?**

-   Aux États-Unis et en Europe, seul [Federal Express][] (FedEx) est pris en charge. Tous les colis sont renvoyés via FedEx Ground ou FedEx International Economy.

-   Pour l'Asie, seul [DHL][] est pris en charge. Tous les colis sont renvoyés via DHL Express Worldwide.

    <div class="dev-callout">
<strong>Important</strong>
<p>Vous devez fournir votre num&eacute;ro de suivi au service Azure Import/Export&nbsp;; &agrave; d&eacute;faut, votre t&acirc;che n'est pas trait&eacute;e.</p>
</div>

**Les retours d'expédition sont-ils facturés ?**

-   Microsoft utilise le numéro du compte transporteur que vous lui avez fourni lors de la création de la tâche pour expédier les lecteurs du centre de données à votre adresse de retour. N'oubliez pas de fournir le numéro de compte du transporteur pris en charge dans la région de votre centre de données. Si vous n'en possédez pas, vous pouvez créer un compte transporteur [FedEx][Federal Express] (pour les États-Unis et l'Europe) ou [DHL][] (pour l'Asie).

-   Les frais d'expédition de retour sont prélevés sur votre compte transporteur et dépendent du transporteur.

**D'où et vers quelles destinations puis-je expédier mes données ?**

-   Le service Import/Export prend en charge l'importation et l'exportation de données vers et depuis les comptes de stockage dans les régions suivantes :

    -   Est des États-Unis
    -   Ouest des États-Unis
    -   États-Unis - partie centrale septentrionale
    -   États-Unis - partie centrale méridionale
    -   Europe du Nord
    -   Europe de l'Ouest
    -   Est de l'Asie
    -   Asie du Sud-Est
-   Une adresse d'expédition vous sera fournie en fonction de la région de votre compte de stockage. Par exemple, si vous vivez aux États-Unis et que votre compte de stockage est hébergé dans le centre de données de la région Europe de l'Ouest, vous recevrez une adresse d'expédition en Europe pour l'envoi des lecteurs.

    <div class="dev-callout">
<strong>Important</strong>
<p>Veuillez noter que le support physique que vous exp&eacute;diez peut &ecirc;tre amen&eacute; &agrave; franchir des fronti&egrave;res internationales. L'importation et/ou l'exportation de votre support physique et de vos donn&eacute;es doivent &ecirc;tre conformes aux lois en vigueur. Avant d'exp&eacute;dier le support physique, consultez vos conseillers pour v&eacute;rifier que vous &ecirc;tes l&eacute;galement autoris&eacute; &agrave; exp&eacute;dier votre support et vos donn&eacute;es vers le centre de donn&eacute;es identifi&eacute;. Il pourra ainsi &ecirc;tre remis &agrave; Microsoft dans les meilleurs d&eacute;lais.</p>
</div>

-   Dans le cadre de l'expédition de vos colis, vous devez vous conformer aux [Conditions du service Microsoft Azure][].

**Est-il possible d'acheter des lecteurs auprès de Microsoft pour des tâches d'importation/exportation ?**

-   Non. Vous devez expédier vos propres lecteurs, aussi bien pour les tâches d'importation que pour les tâches d'exportation.

**Que dois-je inclure dans mon colis ?**

-   Expédiez uniquement vos disques durs. N'incluez rien d'autre dans votre colis (pas de câbles d'alimentation ou de câbles USB, par exemple).

  [Référence de l'API REST du service d'importation/exportation Azure]: http://go.microsoft.com/fwlink/?LinkID=329099
  [Création d'un compte de stockage]: ../storage-create-storage-account/
  [Référence de l'outil Microsoft Azure Import/Export Tool]: http://go.microsoft.com/fwlink/?LinkId=329032
  [Microsoft Azure Import/Export Tool]: http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409
  [Créer une tâche d'importation - Étape 3]: ./media/storage-import-export-service/import-job-03.png
  [Créer une tâche d'exportation - Étape 3]: ./media/storage-import-export-service/export-job-03.png
  [Afficher les clés BitLocker pour une tâche d'exportation]: ./media/storage-import-export-service/export-job-bitlocker-keys.png
  [page]: http://go.microsoft.com/fwlink/?LinkId=329033
  [Federal Express]: http://www.fedex.com/us/oadr/
  [DHL]: http://www.dhl-welcome.com/Tutorial/
  [Conditions du service Microsoft Azure]: http://azure.microsoft.com/en-us/support/legal/services-terms/
