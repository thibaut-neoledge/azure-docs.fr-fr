<properties 
   pageTitle="Afficher et gérer les alertes StorSimple Virtual Array | Microsoft Azure"
   description="Décrit les conditions et la gravité des alertes StorSimple Virtual Array et comment utiliser le service StorSimple Manager pour gérer les alertes."
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
   ms.workload="NA"
   ms.date="03/02/2016"
   ms.author="alkohli" />

# Utiliser le service StorSimple Manager pour afficher et gérer les alertes liées au StorSimple Virtual Array

## Vue d'ensemble

L’onglet **Alertes** dans le service StorSimple Manager vous permet d’examiner et d’effacer en temps réel les alertes relatives au StorSimple Virtual Array. Sous cet onglet, vous pouvez surveiller de manière centralisée les problèmes d’intégrité de vos StorSimple Virtual Arrays (également appelés appareils virtuels locaux StorSimple) et l’ensemble de la solution Microsoft Azure StorSimple.

Ce didacticiel décrit comment configurer les notifications d’alerte, les conditions d’alerte courantes, les niveaux de gravité des alertes et comment afficher les alertes et en effectuer le suivi. En outre, il inclut les tables de référence rapide des alertes, ce qui vous permet de localiser rapidement une alerte spécifique et d’agir en conséquence.

![Page des alertes](./media/storsimple-ova-manage-alerts/alerts1.png)

## Configuration des paramètres d'alerte

Vous pouvez choisir d’être averti par e-mail de la condition des alertes pour chacun de vos appareils virtuels StorSimple. En outre, vous pouvez identifier d’autres destinataires de notification des alertes en entrant leurs adresses e-mail dans la zone **Autres destinataires du message**, séparées par des points-virgules.

>[AZURE.NOTE] Vous pouvez entrer un maximum de 20 adresses e-mail par appareil virtuel.

Après avoir activé la notification par e-mail pour un appareil virtuel, les membres de la liste de notification reçoivent un e-mail chaque fois qu’une alerte critique survient. Les messages seront envoyés depuis **storsimple-alerts-noreply@mail.windowsazure.com* et décriront la condition d’alerte. Les destinataires peuvent cliquer sur **Se désabonner** pour ne plus faire partie de la liste des notifications par e-mail.

#### Activation de la notification par e-mail des alertes pour un appareil virtuel

1. Accédez à **Appareils** > **Configuration** pour l’appareil virtuel. Accédez à la section **Paramètres d’alerte**.

    ![paramètres d’alerte](./media/storsimple-ova-manage-alerts/alerts2.png)

2. Sous **Paramètres d’alerte**, définissez les éléments suivants :

    1. Dans le champ **Activer la notification par e-mail**, sélectionnez **OUI**.

    2. Dans le champ **Envoyer un e-mail aux administrateurs du service**, sélectionnez **OUI** si vous voulez que l’administrateur et tous les coadministrateurs de service reçoivent les notifications d’alerte.

    3. Dans le champ **Autres destinataires du message**, entrez les adresses e-mail de tous les autres destinataires qui doivent recevoir les notifications d’alerte. Entrez les noms au format **someone@somewhere.com*. Utilisez des points-virgules pour séparer les adresses e-mail. Vous pouvez configurer un maximum de 20 adresses e-mail par appareil virtuel.

        ![configuration des notifications des alertes](./media/storsimple-ova-manage-alerts/alerts3.png)

3. En bas de la page, cliquez sur **Enregistrer** pour enregistrer votre configuration.

4. Pour envoyer une notification de test par e-mail, cliquez sur l’icône en forme de flèche à côté de **Envoyer un e-mail de test**. Le service StorSimple Manager affiche des messages d'état lorsqu’il transfère la notification de test.

5. Lorsque le message suivant s’affiche, cliquez sur **OK**.

    ![E-mail de notification de test des alertes envoyé](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Si le message de notification de test ne peut pas être envoyé, le service StorSimple Manager affiche un message approprié. Cliquez sur**OK**, attendez quelques minutes, puis réessayez d'envoyer votre message de notification de test.

    Le message de notification de test sera similaire à ce qui suit.

    ![Exemple d’e-mail de test des alertes](./media/storsimple-ova-manage-alerts/alerts5.png)

## Conditions d’alerte courantes

Votre StorSimple Virtual Array génère des alertes en réponse à différentes conditions. Les éléments suivants constituent les types de conditions d’alerte les plus courants :

- **Problèmes de connectivité** : ces alertes se produisent lorsque des difficultés surviennent dans le transfert de données. Des problèmes de communication peuvent survenir pendant le transfert des données vers et depuis le compte de stockage Azure ou en raison d’une absence de connectivité entre les appareils virtuels et le service StorSimple Manager. Les problèmes de communication sont les plus difficiles à résoudre, car il existe de nombreux points de défaillance. Avant toute chose, vous devez toujours vérifier que la connectivité au réseau et l'accès à Internet sont disponibles avant d’aller plus loin dans la résolution des problèmes. Pour plus d’informations sur les ports et les paramètres du pare-feu, consultez la page [Configuration système requise pour StorSimple Virtual Array](storsimple-ova-system-requirements.md). Pour obtenir de l’aide sur la résolution des problèmes, consultez la section [Dépannage avec l’applet de commande Test-Connection](storsimple-troubleshoot-deployment.md).

- **Problèmes de performances** : ces alertes sont dues aux performances de votre système qui ne sont pas optimales, notamment lorsqu’il est soumis à des conditions de surcharge.

De plus, vous pouvez afficher les alertes liées à la sécurité, aux mises à jour ou aux tâches qui ont échoué.

## Niveaux de gravité des alertes

Les alertes présentent différents niveaux de gravité, en fonction de l'impact de la situation d'alerte et de la nécessité d'une réponse à cette alerte. Les niveaux de gravité des alertes sont :

- **Critique** : cette alerte répond à une condition qui affecte les performances optimales de votre système. Il est nécessaire d’agir pour vous assurer que le service StorSimple n'est pas interrompu.

- **Avertissement** : cette condition peut devenir critique si elle n’est pas résolue. Vous devez examiner la situation et prendre toutes les dispositions nécessaires pour résoudre le problème.

- **Information** : cette alerte contient des informations qui peuvent être utiles pour le suivi et la gestion de votre système.

## Afficher et effectuer le suivi des alertes

Le tableau de bord du service StorSimple Manager vous offre un aperçu rapide du nombre d’alertes sur vos appareils virtuels, classées par niveau de gravité.

![Tableau de bord des alertes](./media/storsimple-ova-manage-alerts/alerts6.png)

Cliquez sur le niveau de gravité pour ouvrir l’onglet **Alertes**. Les résultats incluent uniquement les alertes qui correspondent à ce niveau de gravité.

![Le rapport des alertes est limité au type d'alerte](./media/storsimple-ova-manage-alerts/alerts7.png)

En cliquant sur une alerte dans la liste, vous obtenez des détails supplémentaires sur l'alerte, notamment la dernière fois que l'alerte a été signalée, le nombre d'occurrences de cette alerte sur l'appareil et l'action recommandée pour résoudre cette alerte.

Si vous devez envoyer les informations au Support Microsoft, vous pouvez copier les détails de l’alerte dans un fichier texte. Après avoir suivi la recommandation et résolu la condition d’alerte en local, vous devez effacer l’alerte en la sélectionnant sous l’onglet **Alertes** et en cliquant sur **Effacer**. Pour effacer plusieurs alertes, sélectionnez chaque alerte, cliquez sur n’importe quelle colonne, sauf la colonne **Alerte**, puis cliquez sur **Effacer** après avoir sélectionné toutes les alertes à effacer. Notez que certaines alertes sont effacées automatiquement lorsque le problème est résolu ou lorsque le système met à jour l'alerte avec de nouvelles informations.

Lorsque vous cliquez sur **Effacer**, vous avez la possibilité d’insérer des commentaires sur l'alerte et les étapes que vous avez suivies pour résoudre le problème.

![commentaires d’alerte](./media/storsimple-ova-manage-alerts/clear-alert.png)

Cliquez sur l’icône en forme de coche ![icône-coche](./media/storsimple-ova-manage-alerts/check-icon.png) pour enregistrer vos commentaires.

Certains événements seront effacés par le système si un autre événement est déclenché avec de nouvelles informations. Dans ce cas, le message suivant s'affiche :

![Message de suppression de l'alerte](./media/storsimple-ova-manage-alerts/alerts8.png)

## Tri et vérification des alertes

Cela peut s'avérer plus efficace d’exécuter des rapports sur les alertes afin que vous puissiez les examiner et les effacer en groupes. Par ailleurs, l’onglet **Alertes** peut afficher jusqu'à 250 alertes. Si vous avez dépassé ce nombre d'alertes, toutes les alertes ne seront pas affichées dans l’affichage par défaut. Vous pouvez combiner les champs suivants pour personnaliser les alertes qui s’affichent :

- **État** : vous pouvez afficher des alertes **Actives** ou **Effacées**. Les alertes actives sont toujours déclenchées dans votre système, tandis que les alertes effacées ont été effacées manuellement par un administrateur, ou elles ont été effacées par un programme, car le système a mis à jour la condition d'alerte avec de nouvelles informations.

- **Gravité** : vous pouvez afficher des alertes de tous les niveaux de gravité (critique, avertissement, information), ou seulement celles d’une certaine gravité, par exemple les alertes critiques uniquement.

- **Source** : vous pouvez afficher les alertes de toutes les sources, ou vous limiter aux alertes qui proviennent d’un service ou d’un ou de tous les appareils virtuels.

- **Intervalle de temps** : en spécifiant l’horodatage **De** et **À**, vous pouvez regarder les alertes pour la période qui vous intéresse.

## Référence rapide des alertes

Les tableaux suivants répertorient certaines des alertes Microsoft Azure StorSimple que vous pouvez rencontrer, ainsi que des informations supplémentaires et des recommandations lorsqu'elles sont disponibles. Les alertes de l’appareil virtuel StorSimple appartiennent à l’une des catégories suivantes :

- [Alertes de connectivité au cloud](#cloud-connectivity-alerts)

- [Alertes de configuration](#configuration-alerts)

- [Alertes d'échec de tâche](#job-failure-alerts)

- [Alertes de performances](#performance-alerts)

- [Alertes de sécurité](#security-alerts)

- [Alertes de mise à jour](#update-alerts)

### Alertes de connectivité au cloud

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|L’appareil *<device name>* n’est pas connecté au cloud.|L’appareil nommé ne peut pas se connecter au cloud. |Impossible de se connecter au cloud. La raison peut être l’une des suivantes :<ul><li>Les paramètres réseau de votre appareil présentent peut-être un problème.</li><li>Les informations d’identification du compte de stockage présentent peut-être un problème.</li></ul>Pour plus d’informations sur la résolution des problèmes de connectivité, accédez à [l’interface utilisateur web locale](storsimple-ova-web-ui-admin.md) de l’appareil.|


### Alertes de configuration

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|La configuration d’un appareil virtuel local n’est pas prise en charge.|Performances lentes.|La configuration actuelle peut aboutir à une dégradation des performances. Vérifiez que votre serveur répond à la configuration minimale requise. Pour plus d’informations, accédez à [Configuration requise pour StorSimple Virtual Array](storsimple-ova-system-requirements.md). 
|Vous n’avez presque plus d’espace disque approvisionné sur <*nom de l’appareil*>.|Avertissement d’espace disque.|Vous n’avez presque plus d’espace disque approvisionné. Pour libérer de l’espace, envisagez de déplacer des charges de travail vers un autre volume ou partage ou de supprimer des données.

### Alertes d'échec de tâche

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Impossible d’effectuer la sauvegarde de <*nom de l’appareil*>.|Échec du travail de sauvegarde.|Impossible de créer une sauvegarde. Envisagez l’une des possibilités suivantes :<ul><li>Des problèmes de connectivité peuvent empêcher la bonne exécution de l’opération de sauvegarde. Vérifiez qu’il n’existe aucun problème de connectivité. Pour plus d’informations sur la résolution des problèmes de connectivité, accédez à l’[interface utilisateur web locale](storsimple-ova-web-ui-admin.md) de votre appareil virtuel.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, envisagez de supprimer les sauvegardes devenues inutiles.</li></ul> Résolvez les problèmes, effacez l’alerte et recommencez l’opération.|
|Impossible d’effectuer la restauration de <*nom de l’appareil*>.|Échec du travail de restauration.|La restauration à partir de la sauvegarde a échoué. Envisagez l’une des possibilités suivantes :<ul><li>Votre liste de sauvegarde n’est peut-être pas valide. Actualisez-la pour vérifier qu’elle est toujours valide.</li><li>Des problèmes de connectivité peuvent empêcher la bonne exécution de l’opération de restauration. Vérifiez qu’il n’existe aucun problème de connectivité.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, envisagez de supprimer les sauvegardes devenues inutiles.</li></ul>Résolvez les problèmes, effacez l’alerte et recommencez l’opération de restauration.|
|Impossible d’effectuer le clonage de <*nom de l’appareil*>.|Échec du travail de clonage.|La création d’un clone a échoué. Envisagez l’une des possibilités suivantes :<ul><li>Votre liste de sauvegarde n’est peut-être pas valide. Actualisez-la pour vérifier qu’elle est toujours valide.</li><li>Des problèmes de connectivité peuvent empêcher la bonne exécution de l’opération de clonage. Vérifiez qu’il n’existe aucun problème de connectivité.</li><li>Vous avez atteint la limite de stockage disponible. Pour libérer de l’espace, envisagez de supprimer les sauvegardes devenues inutiles .</li></ul>Résolvez les problèmes, effacez l’alerte et recommencez l’opération.|

### Alertes de performances

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Vous rencontrez des retards inattendus dans le transfert de données.|Transfert de données lent.|Les erreurs de limitation se produisent lorsque vous dépassez les valeurs cibles d’évolutivité d’un service de stockage. Le service de stockage effectue cette opération afin de s’assurer qu’aucun client ne peut utiliser le service au détriment des autres utilisateurs. Pour plus d’informations sur la résolution des problèmes de votre compte de stockage Azure, accédez à [Surveiller, diagnostiquer et résoudre les problèmes liés à Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
|Vous n’avez presque plus d’espace disque de réservation locale sur <*nom de l’appareil*>.|Temps de réponse lent.|10 % de la taille approvisionnée totale pour <*nom de l’appareil*> sont réservés sur l’appareil local alors qu’il n’y a presque plus d’espace réservé. La charge de travail sur <*nom de l’appareil*> génère un taux d’évolution élevé ou bien vous avez migré récemment une grande quantité de données. Les performances peuvent s’en trouver réduites. Envisagez l’une des actions suivantes pour résoudre ce problème :<ul><li>Augmentez la bande passante du cloud vers cet appareil.</li><li>Réduisez ou déplacez des charges de travail vers un autre volume ou un autre partage.</li></ul>

### Alertes de sécurité

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Le mot de passe pour <*nom de l’appareil*> expirera dans <*nombre*> jours.|Avertissement de mot de passe.| Votre mot de passe expire dans <nombre> jours. Pensez à le changer. Pour plus d’informations, accédez à [Modifier le mot de passe administrateur de l’appareil StorSimple Virtual Array](storsimple-ova-change-device-admin-password.md).

### Alertes de mise à jour

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|De nouvelles mises à jour sont disponibles pour votre appareil.|Des mises à jour sont disponibles pour le StorSimple Virtual Array.|Vous pouvez installer les nouvelles mises à jour depuis la page **Maintenance**.|
|Impossible de rechercher de nouvelles mises à jour sur <*nom de l’appareil*>.|Échec de la mise à jour. |Une erreur s’est produite lors de l’installation de nouvelles mises à jour. Vous pouvez installer les mises à jour manuellement. Si le problème persiste, contactez le [support technique de Microsoft](storsimple-contact-microsoft-support.md).|


## Étapes suivantes

- [Découvrez le StorSimple Virtual Array](storsimple-ova-overview.md).

<!---HONumber=AcomDC_0309_2016-->