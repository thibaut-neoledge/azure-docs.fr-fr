<properties 
   pageTitle="Installation d’Update 0.1 sur StorSimple Virtual Array | Microsoft Azure"
   description="Décrit comment utiliser l’interface utilisateur web de StorSimple Virtual Array pour installer Update 0.1 à l’aide du portail et de la méthode des correctifs logiciels"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/26/2016"
   ms.author="alkohli" />

# Installation d’Update 0.1 sur StorSimple Virtual Array

## Vue d'ensemble

Cet article décrit les étapes requises pour installer Update 0.1 sur votre instance StorSimple Virtual Array. Il décrit les procédures de mise à jour effectuées à l’aide du portail Azure Classic et via l’interface utilisateur web locale. Chaque procédure de mise à jour prend moins de 2 minutes.

Pour maintenir StorSimple Virtual Array à jour, vous devrez généralement appliquer des mises à jour et des correctifs logiciels. En règle générale, nous vous recommandons d'installer les mises à jour par le biais du portail Azure Classic. Toutefois, dans les cas où ce dernier n'est pas disponible, vous pouvez utiliser l'interface utilisateur web locale pour appliquer les correctifs ou les mises à jour.

N’oubliez pas que l’installation d’une mise à jour ou d’un correctif logiciel nécessite le redémarrage de votre appareil. Étant donné que StorSimple Virtual Array est un appareil à nœud unique, les E/S en cours seront interrompues et votre appareil subira des temps d’arrêt.

Avant d’appliquer une mise à jour, nous vous recommandons de mettre les volumes ou les partages hors connexion sur l’ordinateur hôte puis sur l’appareil. Vous réduisez ainsi toute possibilité d’altération des données.

## Utilisation du portail Azure Classic

Nous vous recommandons d’installer les mises à jour par le biais du portail Azure Classic. La procédure du portail oblige l’utilisateur à rechercher et télécharger les mises à jour avant de les installer. Effectuez les opérations suivantes pour installer la mise à jour ou le correctif logiciel.

#### Pour installer des mises à jour par le biais du portail Azure Classic

1. À la page **Appareils**, sélectionnez l’appareil sur lequel vous souhaitez installer les mises à jour.

2. Accédez à **Appareils** > **Maintenance** > **Mises à jour logicielles**.

3. Un message s’affiche si des mises à jour logicielles sont disponibles. Pour vérifier si des mises à jour sont disponibles, vous pouvez également cliquer sur l’option **Vérifier les mises à jour** en bas de la page.

	![Rechercher des mises à jour](./media/storsimple-ova-install-update-01/aupdate1m.png)

4. En bas de la page, cliquez sur **Télécharger les mises à jour**. Une boîte de dialogue informe l’utilisateur que la mise à jour entraînera des interruptions. StorSimple Virtual Array est un appareil à nœud unique, ce qui signifie qu’il redémarrera après la mise à jour. Cette opération interrompra toutes les E/S en cours. Cliquez sur l’icône en forme de coche pour lancer une tâche permettant de télécharger les mises à jour disponibles.

	![Confirmer le téléchargement des mises à jour](./media/storsimple-ova-install-update-01/aupdate3m.png)

	Vous serez averti une fois les mises à jour téléchargées.

	![Télécharger les mises à jour](./media/storsimple-ova-install-update-01/aupdate5m.png)

5. En bas de la page, cliquez sur **Installer les mises à jour** pour lancer la mise à jour de l’appareil. La boîte de dialogue s’affiche à nouveau. Cliquez sur l’icône en forme de coche pour démarrer une tâche d’installation des mises à jour.
 
 	![Confirmer l’installation des mises à jour](./media/storsimple-ova-install-update-01/aupdate6m.png)
 
 
	Un message vous informe que la tâche a bien été créée.

	![Installer les mises à jour](./media/storsimple-ova-install-update-01/aupdate8m.png)
	
6. Cliquez sur le lien **Afficher la tâche** pour accéder à la page **Tâches** et surveiller l’état de l’installation. Vous pouvez cliquer à tout moment sur **Détails** pour obtenir des informations détaillées sur la tâche de mise à jour.

	![Surveiller les mises à jour](./media/storsimple-ova-install-update-01/aupdate9m.png)

6. Une fois l’installation terminée (lorsque l’état de la tâche atteint 100 %), accédez à **Appareils** > **Maintenance** > **Mises à jour logicielles**. La version du logiciel doit être **10.0.10279.0**.

	![Vérifier les mises à jour](./media/storsimple-ova-install-update-01/aupdate13m.png)

## Utilisation de l’interface utilisateur web locale 

Utilisez l’interface utilisateur web locale pour appliquer les mises à jour lorsque le portail Azure Classic n’est pas disponible. Le procédure de mise à jour à l’aide de l’interface utilisateur web locale se déroule en deux étapes :

- Téléchargement de la mise à jour ou du correctif
- Installation de la mise à jour ou du correctif

### Téléchargement de la mise à jour ou du correctif

Procédez comme suit pour télécharger la mise à jour logicielle à partir du Catalogue Microsoft Update.

#### Pour téléchargement la mise à jour ou le correctif

1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si vous utilisez le catalogue Microsoft Update pour la première fois sur cet ordinateur, cliquez sur **Installer** lorsque vous êtes invité à installer le module complémentaire Catalogue Microsoft Update.
   
	![Installer le catalogue](./media/storsimple-ova-install-update-01/install-catalog.png)

3. Dans la zone de recherche du Catalogue Microsoft Update, entrez le numéro KB (Base de connaissances) du correctif que vous souhaitez télécharger, par exemple **3160441**, puis cliquez sur **Rechercher**.

    La liste des correctifs s’affiche, par exemple, **StorSimple Virtual Array Update 0.1**.

    ![Rechercher dans le catalogue](./media/storsimple-ova-install-update-01/download1.png)

4. Cliquez sur **Add**. La mise à jour est ajoutée au panier.

5. Cliquez sur **Afficher le panier**.

6. Cliquez sur **Télécharger**. Spécifiez ou **recherchez** l’emplacement local où vous voulez effectuer les téléchargements. Les mises à jour sont téléchargées à l’emplacement spécifié et placées dans un sous-dossier portant le même nom que la mise à jour. Ce dossier peut également être copié sur un partage réseau accessible à partir de l’appareil.


### Installation de la mise à jour ou du correctif

Avant l'installation de la mise à jour ou du correctif, vérifiez que la mise à jour ou le correctif est téléchargé(e) localement sur votre ordinateur hôte ou accessible via un partage réseau. Effectuez les opérations suivantes pour installer la mise à jour ou le correctif logiciel.

#### Pour installer la mise à jour ou le correctif

1. Dans l’interface utilisateur web locale, accédez à **Maintenance** > **Mise à jour logicielle**.

2. Dans le **chemin d’accès au fichier de mise à jour**, entrez le nom de fichier pour la mise à jour ou le correctif. Vous pouvez également accéder au fichier d'installation de la mise à jour ou du correctif si celui-ci est placé sur un partage réseau. Cliquez sur **Apply**.

	![mettre à jour l'appareil](./media/storsimple-ova-install-update-01/update1m.png)

3.  Un message d’avertissement s’affiche. Puisqu’il s’agit d’un appareil à nœud unique, l’application de la mise à jour entraînera le redémarrage de l’appareil et provoquera un temps d’arrêt. Cliquez sur l’icône en forme de coche.

	![mettre à jour l'appareil](./media/storsimple-ova-install-update-01/update4m.png)

4. La mise à jour démarre. Une fois l’appareil correctement mis à jour, il sera redémarré. L’interface utilisateur locale ne sera pas accessible pendant cet intervalle.

    ![mettre à jour l'appareil](./media/storsimple-ova-install-update-01/update6m.png)

4. Une fois le redémarrage effectué, vous êtes redirigé vers la page de connexion. Vous pouvez alors vérifier la version du logiciel. Accédez à **Maintenance** > **Mise à jour logicielle**. Le numéro de version doit être **10.0.0.0.10279**.

	> [AZURE.NOTE] L’affichage des versions logicielles diffère légèrement entre l’interface utilisateur web locale et le portail Azure Classic. La même version est signalée par **10.0.0.0.10279** dans l’interface utilisateur web locale et par **10.0.10279.0** sur le portail Azure Classic.

	![mettre à jour l'appareil](./media/storsimple-ova-install-update-01/update9m.png)

## Étapes suivantes

En savoir plus sur la [gestion de votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0601_2016-->