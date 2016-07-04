<properties 
   pageTitle="Installation de mises à jour sur une instance StorSimple Virtual Array | Microsoft Azure"
   description="Décrit comment utiliser l’interface utilisateur web de StorSimple Virtual Array pour installer des mises à jour à l’aide du portail et de la méthode des correctifs logiciels"
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
   ms.date="06/16/2016"
   ms.author="alkohli" />

# Installation de mises à jour sur votre instance StorSimple Virtual Array

## Vue d'ensemble

Cet article décrit les étapes requises pour installer des mises à jour sur votre instance StorSimple Virtual Array à l’aide de l’interface utilisateur web locale. Cette procédure prend moins de 2 minutes. Pour maintenir votre instance StorSimple Virtual Array à jour, vous devez appliquer des mises à jour ou des correctifs logiciels.

N’oubliez pas que l’installation d’une mise à jour ou d’un correctif logiciel nécessite le redémarrage de votre appareil. Étant donné que StorSimple Virtual Array est un appareil à nœud unique, les E/S en cours seront interrompues et votre appareil subira des temps d’arrêt.

Avant d’appliquer une mise à jour, nous vous recommandons de mettre les volumes ou les partages hors connexion sur l’ordinateur hôte puis sur l’appareil. Vous réduisez ainsi toute possibilité d’altération des données.

## Utilisation de l’interface utilisateur web locale 
Pour installer la dernière mise à jour, vous devez utiliser l’interface utilisateur web locale pour appliquer des correctifs ou des mises à jour (pour l’instant, vous ne pouvez pas utiliser le portail Azure Classic pour installer la mise à jour).

Le procédure de mise à jour à l’aide de l’interface utilisateur web locale se déroule en deux étapes :

- Téléchargement de la mise à jour ou du correctif
- Installation de la mise à jour ou du correctif

### Téléchargement de la mise à jour ou du correctif

Procédez comme suit pour télécharger la mise à jour logicielle à partir du Catalogue Microsoft Update.

#### Pour téléchargement la mise à jour ou le correctif

1. Démarrez Internet Explorer et accédez à [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Si vous utilisez le catalogue Microsoft Update pour la première fois sur cet ordinateur, cliquez sur **Installer** lorsque vous êtes invité à installer le module complémentaire Catalogue Microsoft Update.
   
	![Installer le catalogue](./media/storsimple-ova-install-update-01/install-catalog.png)

3. Dans la zone de recherche du Catalogue Microsoft Update, entrez le numéro KB (Base de connaissances) du correctif que vous souhaitez télécharger. Entrez **3160441** pour Update 0.2 ou **3160441** pour Update 0.1, puis cliquez sur **Rechercher**.

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

4. Une fois le redémarrage effectué, vous êtes redirigé vers la page de connexion. Vous pouvez alors vérifier la version du logiciel. Accédez à **Maintenance** > **Mise à jour logicielle**. La version du logiciel doit être **10.0.10280.0** pour Update 0.2 ou **10.0.10279.0** pour Update 0.1.

	> [AZURE.NOTE] L’affichage des versions logicielles diffère légèrement entre l’interface utilisateur web locale et le portail Azure Classic. Par exemple, la même version est signalée par **10.0.0.0.10279** dans l’interface utilisateur web locale et par **10.0.10279.0** sur le portail Azure Classic.

	![mettre à jour l'appareil](./media/storsimple-ova-install-update-01/update9m.png)

## Étapes suivantes

En savoir plus sur la [gestion de votre StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0622_2016-->