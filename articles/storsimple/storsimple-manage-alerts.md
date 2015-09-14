<properties 
   pageTitle="Affichage et gestion des alertes StorSimple | Microsoft Azure"
	description="Décrit les conditions et la gravité des alertes StorSimple, comment configurer les notifications d'alerte et comment utiliser le service StorSimple Manager pour gérer les alertes."
	services="storsimple"
	documentationCenter="NA"
	authors="SharS"
	manager="carolz"
	editor=""/>
<tags 
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="09/01/2015"
	ms.author="v-sharos"/>

# Utiliser le service StorSimple Manager pour afficher et gérer vos alertes StorSimple

## Vue d’ensemble

L’onglet **Alertes** dans le service StorSimple Manager vous offre la possibilité d’examiner et d’effacer en temps réel les alertes relatives aux appareils StorSimple. Dans cet onglet, vous pouvez surveiller de manière centralisée l'intégrité de vos appareils StorSimple et l’ensemble de la solution Microsoft Azure StorSimple.

Ce didacticiel décrit les conditions d’alerte courantes, les niveaux de gravité des alertes et comment configurer les notifications d'alerte. En outre, il inclut les tables de référence rapide des alertes, ce qui vous permet de localiser rapidement une alerte spécifique et d’agir en conséquence.

![Page des alertes](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## Conditions d’alerte courantes

Votre appareil StorSimple génère des alertes en réponse à différentes conditions. Les éléments suivants constituent les types de conditions d’alerte les plus courants :

- **Problèmes matériels** : ces alertes vous informent sur l'intégrité de votre matériel. Elles vous permettent de savoir si les mises à niveau des microprogrammes sont nécessaires, si une interface réseau rencontre des problèmes ou s'il existe un problème avec l’un de vos lecteurs de données.

- **Problèmes de connectivité** : ces alertes se produisent lorsque des difficultés surviennent dans le transfert de données entre des sources de données. Des problèmes de communication peuvent survenir pendant le transfert des données vers et depuis le compte de stockage Azure ou en raison d'une absence de connectivité entre les appareils et le service StorSimple Manager. Ils peuvent se produire lors des processus de configuration, de sauvegarde ou de restauration. Les problèmes de communication sont les plus difficiles à résoudre, car il existe de nombreux points de défaillance. Avant toute chose, vous devez toujours vérifier que la connectivité au réseau et l'accès à Internet sont disponibles avant d’aller plus loin dans la résolution des problèmes.

- **Problèmes de performances** : ces alertes sont dues aux performances de votre système qui ne sont pas optimales, notamment lorsqu’il est soumis à des conditions de surcharge.

De plus, vous pouvez afficher les alertes liées à la sécurité, aux mises à jour ou aux tâches qui ont échoué.

## Niveaux de gravité des alertes

Les alertes présentent différents niveaux de gravité, en fonction de l'impact de la situation d'alerte et de la nécessité d'une réponse à cette alerte. Les niveaux de gravité des alertes sont :

- **Critique** : cette alerte répond à une condition qui affecte les performances optimales de votre système. Il est nécessaire d’agir pour vous assurer que le service StorSimple n'est pas interrompu.

- **Avertissement** : cette condition peut devenir critique si elle n’est pas résolue. Vous devez examiner la situation et prendre toutes les dispositions nécessaires pour résoudre le problème.

- **Information** : cette alerte contient des informations qui peuvent être utiles pour le suivi et la gestion de votre système.

## Configuration des paramètres d'alerte

Vous pouvez choisir d’être averti par e-mail de la condition des alertes pour chacun de vos appareils StorSimple. En outre, vous pouvez identifier d’autres destinataires de notification des alertes en entrant leurs adresses e-mail dans la zone **AUTRES DESTINATAIRES D’E-MAIL**, séparées par des points-virgules.

>[AZURE.NOTE]Vous pouvez entrer un maximum de 20 adresses e-mail par appareil.

Après avoir activé la notification par e-mail pour un appareil, les membres de la liste de notification recevront un e-mail chaque fois qu’une alerte critique survient. Les messages seront envoyés depuis **storsimple-alerts-noreply@mail.windowsazure.com* et décriront la condition d’alerte. Les destinataires peuvent cliquer sur **Se désabonner** pour ne plus faire partie de la liste des notifications par e-mail.

#### Activation de la notification par e-mail des alertes pour un appareil

1. Cliquez sur **Appareils** > **Configurer** pour l’appareil.

2. Sous **Paramètres d’alerte**, définissez les éléments suivants :

    1. Dans le champ **ENVOYER UNE NOTIFICATION PAR E-MAIL**, sélectionnez **OUI**.

    2. Dans le champ **ADMINISTRATEURS DU SERVICE DE MESSAGERIE**, sélectionnez **OUI** si vous voulez que l’administrateur et tous les coadministrateurs de service reçoivent les notifications d’alerte.

    3. Dans le champ **AUTRES DESTINATAIRES DES E-MAILS**, entrez les adresses e-mail de tous les autres destinataires qui doivent recevoir les notifications d’alerte. Entrez les noms au format **someone@somewhere.com*. Utilisez des points-virgules pour séparer les adresses e-mail. Vous pouvez configurer un maximum de 20 adresses e-mail par appareil.

    ![Page de configuration des notifications des alertes](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig.png)

3. Pour envoyer une notification de test par e-mail, cliquez sur l’icône de flèche à côté de **ENVOYER UN E-MAIL DE TEST**. Le service StorSimple Manager affiche des messages d'état lorsqu’il transfère la notification de test.

4. Quand le message suivant s’affiche, cliquez sur **OK**.

    ![E-mail de notification de test des alertes envoyé](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE]Si le message de notification de test ne peut pas être envoyé, le service StorSimple Manager affiche un message approprié. Cela peut se produire en raison d’un problème de trafic ou de réseau. Cliquez sur **OK**, attendez quelques minutes, puis essayez à nouveau d’envoyer votre message de notification de test.

## Afficher et effectuer le suivi des alertes

Le tableau de bord du service StorSimple Manager vous offre un aperçu du nombre d'alertes sur vos appareils, classées par niveau de gravité.

![Tableau de bord des alertes](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Le fait de cliquer sur le niveau de gravité ouvre l’onglet **Alertes**. Les résultats incluent uniquement les alertes qui correspondent à ce niveau de gravité.

![Le rapport des alertes est limité au type d'alerte](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

En cliquant sur une alerte dans la liste, vous obtenez des détails supplémentaires sur l'alerte, notamment la dernière fois que l'alerte a été signalée, le nombre d'occurrences de cette alerte sur l'appareil et l'action recommandée pour résoudre cette alerte. S'il s'agit d'une alerte matérielle, elle identifiera également le composant matériel.

![Exemple d'alerte de matériel](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Si vous souhaitez effectuer le suivi de la résolution de l'alerte dans un fichier distinct, ou si vous avez besoin d'envoyer les informations au Support Microsoft, vous pouvez copier les détails de l'alerte dans un fichier texte. Une fois que la condition d’alerte est résolue, vous devez effacer l’alerte de l’appareil en sélectionnant l’alerte sous l’onglet **Alertes** et en cliquant sur **Effacer**. Pour effacer plusieurs alertes, appuyez sur la touche Ctrl tout en sélectionnant les alertes, puis cliquez sur **Effacer**. Notez que certaines alertes sont effacées automatiquement lorsque le problème est résolu ou lorsque le système met à jour l'alerte avec de nouvelles informations.

Quand vous cliquez sur **Effacer**, vous avez la possibilité d’insérer des commentaires sur l’alerte et sur les étapes que vous avez suivies pour résoudre le problème. Certains événements seront effacés par le système si un autre événement est déclenché avec de nouvelles informations. Dans ce cas, le message suivant s'affiche :

![Message de suppression de l'alerte](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## Tri et vérification des alertes

Cela peut s'avérer plus efficace d’exécuter des rapports sur les alertes afin que vous puissiez les examiner et les effacer en groupes. Par ailleurs, l’onglet **Alertes** peut afficher jusqu’à 250 alertes. Si vous avez dépassé ce nombre d'alertes, toutes les alertes ne seront pas affichées dans l’affichage par défaut. Vous pouvez combiner les champs suivants pour personnaliser les alertes qui s’affichent :

- **État** : vous pouvez afficher des alertes **Actives** ou **Effacées**. Les alertes actives sont toujours déclenchées dans votre système, tandis que les alertes effacées ont été effacées manuellement par un administrateur, ou elles ont été effacées par un programme, car le système a mis à jour la condition d'alerte avec de nouvelles informations.

- **Gravité** : vous pouvez afficher des alertes de tous les niveaux de gravité (critique, avertissement, information), ou seulement celles d’une certaine gravité, par exemple uniquement les alertes critiques.

- **Source** : vous pouvez afficher les alertes provenant de toutes les sources, ou vous limiter aux alertes qui proviennent d’un service, ou de tout ou partie des appareils.

- **Intervalle de temps** : en spécifiant l’horodatage **De** et **À**, vous pouvez voir les alertes pour la période qui vous intéresse.

## Référence rapide des alertes

Les tableaux suivants répertorient certaines des alertes Microsoft Azure StorSimple que vous pouvez rencontrer, ainsi que des informations supplémentaires et des recommandations lorsqu'elles sont disponibles. Les alertes de l'appareil StorSimple appartiennent à l’une des catégories suivantes :

- [Alertes de connectivité au cloud](#cloud-connectivity-alerts)

- [Alertes de cluster](#cluster-alerts)

- [Alertes de récupération d'urgence](#disaster-recovery-alerts)

- [Alertes de matériel](#hardware-alerts)

- [Alertes d'échec de tâche](#job-failure-alerts)

- [Alertes de performances](#performance-alerts)

- [Alertes de sécurité](#security-alerts)

- [Alertes du package de prise en charge](#support-package-alerts)

- [Alertes de test](#test-alerts)

- [Alertes de mise à jour](#update-alerts)

### Alertes de connectivité au cloud

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|La connectivité à <*nom des informations d’identification cloud*> ne peut pas être établie.|Impossible de se connecter au compte de stockage.|Il se peut qu'il y ait un problème de connectivité avec votre appareil. Exécutez l’applet de commande `Test-HcsmConnection` à partir de l’interface Windows PowerShell pour StorSimple sur votre appareil pour identifier et résoudre le problème. Si les paramètres sont corrects, le problème peut provenir des informations d'identification du compte de stockage pour lequel l'alerte a été déclenchée. Dans ce cas, utilisez l’applet de commande `Test-HcsStorageAccountCredential` pour déterminer s’il existe des problèmes que vous pouvez résoudre.<ul><li>Vérifiez vos paramètres réseau.</li><li>Vérifiez les informations d’identification de votre compte de stockage.</li></ul>|
|Nous n’avons pas reçu de pulsation de votre appareil au cours des <*nombre*> dernières minutes.|Impossible de se connecter à l’appareil.|Il semble qu'il y ait un problème de connectivité avec votre appareil. Veuillez utiliser l’applet de commande `Test-HcsmConnection` à partir de l'interface Windows PowerShell pour StorSimple sur votre appareil pour identifier et résoudre le problème, ou contactez votre administrateur réseau.|

### Comportement de StorSimple en cas d’échec de connexion au cloud

Que se passe-t-il en cas d'échec de connexion au cloud pour mon appareil StorSimple en cours d'exécution en production ?

En cas d'échec de la connexion au cloud sur votre appareil de production StorSimple, en fonction de l'état de votre périphérique, les événements suivants peuvent se produire :

- **Pour les données locales sur votre appareil** : il n’y a pas d’interruption de service et les lectures sont servies. Toutefois, lorsque les E/S en attente augmentent et dépassent la limite, les lectures peuvent commencer à échouer. 

	Selon la quantité de données sur les couches locales de votre appareil, les écritures continuent également à se produire pendant les premières heures suivant l'interruption de la connexion au cloud. Puis, les écritures ralentissent et finissent par échouer si l'interruption de la connexion au cloud dure plusieurs heures.

 
- **Pour les données dans le cloud** : pour la plupart des erreurs de connectivité au cloud, une erreur est renvoyée. Une fois que la connectivité est rétablie, les E/S reprennent sans que l'utilisateur doive mettre le volume en ligne. Dans de rares cas, l'intervention de l'utilisateur peut être nécessaire pour ramener le volume en ligne à partir du portail Azure.
 
- **Pour les instantanés cloud en cours** : l'opération est retentée plusieurs fois pendant 4 à 5 heures et si la connectivité n'est pas restaurée, les instantanés cloud échouent.


### Alertes de cluster

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Basculement de l’appareil vers <*nom de l’appareil*>.|L’appareil est en mode Maintenance.|Basculement de l’appareil en raison de l’activation ou de la désactivation du mode Maintenance. Ceci est normal et aucune action n'est nécessaire. Une fois que vous avez reconnu cette alerte, effacez-la de la page des alertes.|
|Basculement de l’appareil vers <*nom de l’appareil*>.|Le microprogramme ou le logiciel de l’appareil vient d’être mis à jour.|Un basculement de cluster est survenu en raison d’une mise à jour. Ceci est normal et aucune action n'est nécessaire. Une fois que vous avez reconnu cette alerte, effacez-la de la page des alertes.|
|Basculement de l’appareil vers <*nom de l’appareil*>.|Le contrôleur a été arrêté ou redémarré.|L’appareil a été basculé, car le contrôleur actif a été arrêté ou redémarré par un administrateur. Aucune action n'est nécessaire. Une fois que vous avez reconnu cette alerte, effacez-la de la page des alertes.|
|Basculement de l’appareil vers <*nom de l’appareil*>.|Basculement planifié.|Vérifiez qu’il s’agissait d’un basculement planifié. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes.|
|Basculement de l’appareil vers <*nom de l’appareil*>.|Basculement non planifié.|StorSimple est conçu pour effectuer une récupération automatique à partir des basculements non planifiés. Si vous voyez un grand nombre de ces alertes, veuillez contacter le Support Microsoft.|
|Basculement de l’appareil vers <*nom de l’appareil*>.|Autre cause/cause inconnue.|Si vous voyez un grand nombre de ces alertes, veuillez contacter le Support Microsoft. Une fois que le problème est résolu, effacez cette alerte de la page des alertes.|

### Alertes de récupération d'urgence

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Les opérations de récupération n'ont pas pu restaurer tous les paramètres pour ce service. Les données de configuration d’appareil sont dans un état incohérent pour certains appareils.|Incohérence de données détectée après la récupération d'urgence.|Les données chiffrées sur le service ne sont pas synchronisées avec celles sur l’appareil. Dans StorSimple Manager, autorisez l’appareil <*nom de l’appareil*> à démarrer le processus de synchronisation. Utilisez l’interface Windows PowerShell pour StorSimple pour exécuter l’applet de commande `Restore-HcsmEncryptedServiceData` sur <*nom de l’appareil*>, en fournissant l’ancien mot de passe comme entrée à cette applet de commande pour restaurer le profil de sécurité. Puis exécutez l’applet de commande `Invoke-HcsmServiceDataEncryptionKeyChange` pour mettre à jour la clé de chiffrement des données du service. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes.|
|Le service a basculé vers un centre de données secondaire en raison d'une défaillance inattendue.|Autre cause/cause inconnue.|Vous devez vérifier vos paramètres de configuration dans StorSimple Manager avant de continuer. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes. Pour plus d'informations relatives à StorSimple Manager, consultez [Utilisation du service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).|

### Alertes de matériel

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Le composant matériel <*ID du composant*> indique l’état <*état*>.||Les conditions temporaires peuvent parfois provoquer ces alertes. Dans ce cas, l’alerte est automatiquement effacée après un certain temps. Si le problème persiste, contactez le support technique Microsoft.|
|Dysfonctionnement du contrôleur passif.|Le contrôleur passif (secondaire) ne fonctionne pas.|Votre appareil est opérationnel, mais l’un de vos contrôleurs est défectueux. Essayez de redémarrer ce contrôleur. Si le problème persiste, contactez le Support Microsoft.|

### Alertes d'échec de tâche

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Échec de la sauvegarde de <*ID du groupe de volumes source*>.|Échec de la sauvegarde de la tâche.|Des problèmes de connectivité peuvent empêcher la réalisation de l'opération de sauvegarde. S'il n'y a aucun problème de connectivité, vous avez peut-être atteint le nombre maximal de sauvegardes. Supprimez toutes les sauvegardes qui ne sont plus nécessaires et recommencez l'opération. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes.|
|Le clonage de <*ID des éléments de sauvegarde source*> vers <*numéros de série des volumes de destination*> a échoué.|Échec du clonage de la tâche.|Actualisez la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que les problèmes de connectivité au cloud empêchent l'opération de clonage de se terminer correctement. S'il n'y a aucun problème de connectivité, vous avez peut-être atteint la limite de stockage. Supprimez toutes les sauvegardes qui ne sont plus nécessaires et recommencez l'opération. Après avoir pris les mesures appropriées pour résoudre le problème, effacez cette alerte de la page des alertes.|
|La restauration de <*ID des éléments de sauvegarde source*> a échoué.|Échec de la restauration de la tâche.|Actualisez la liste de sauvegarde pour vérifier que la sauvegarde est toujours valide. Si la sauvegarde est valide, il est possible que des problèmes de connectivité au cloud empêchent l'opération de restauration de se terminer correctement. S'il n'y a aucun problème de connectivité, vous avez peut-être atteint la limite de stockage. Supprimez toutes les sauvegardes qui ne sont plus nécessaires et recommencez l'opération. Après avoir pris les mesures appropriées pour résoudre le problème, effacez cette alerte de la page des alertes.|

### Alertes de performances

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|La charge de l’appareil a dépassé <*seuil*>.|Plus lent que le temps de réponse attendu.|Votre appareil indique que l'utilisation d'entrée/sortie est surchargée. Cela peut entraîner du mauvais fonctionnement de votre appareil. Veuillez consulter les charges de travail que vous avez attachées à l’appareil et déterminer si certaines peuvent être déplacées vers un autre appareil ou ne sont plus nécessaires.|

### Alertes de sécurité

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|La session du Support Microsoft a commencé.|Session du support accédée par des tiers.|Veuillez confirmer que cet accès est autorisé. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes.|
|Le mot de passe pour <*élément*> expirera dans <*période de temps*>.||Modifiez votre mot de passe avant son expiration.|
|Les informations de configuration de sécurité sont manquantes pour <*ID de l’élément*>.||Les volumes associés à ce conteneur de volume ne peuvent pas être utilisés pour répliquer votre configuration StorSimple. Pour vous assurer que vos données sont stockées en toute sécurité, nous vous invitons à supprimer le conteneur de volumes et les volumes associés à ce conteneur de volume. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes.|
|<*nombre*> tentatives de connexion ont échoué pour <*ID de l’élément*>.|Échec de plusieurs tentatives de connexion.|Il se peut que votre appareil soit attaqué ou qu’un utilisateur autorisé tente de se connecter avec un mot de passe incorrect.<ul><li>Contactez vos utilisateurs autorisés et vérifiez que ces tentatives proviennent d’une source légitime. Si vous continuez à voir un grand nombre de tentatives de connexion échouées, envisagez de désactiver la gestion à distance et contactez votre administrateur réseau. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes.</li><li>Vérifiez que vos instances du gestionnaire d’instantanés sont configurées avec le bon mot de passe. Après avoir pris les mesures appropriées, effacez cette alerte de la page des alertes.</li></ul>|
|Une ou plusieurs défaillances se sont produites lors de la modification de la clé de chiffrement de données du service.||Des erreurs sont survenues lors de la modification de la clé de chiffrement de données du service. Une fois que vous avez résolu les conditions d’erreur, exécutez l’applet de commande `Invoke-HcsmServiceDataEncryptionKeyChange` à partir de l’interface Windows PowerShell pour StorSimple sur votre appareil pour mettre à jour le service. Si le problème persiste, veuillez contacter le support Microsoft. Une fois que le problème est résolu, effacez cette alerte de la page des alertes.|

### Alertes du package de prise en charge

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Échec de la création du package de prise en charge.|StorSimple n'a pas pu générer le package.|Retentez l'opération. Si le problème persiste, contactez le support technique Microsoft. Une fois que le problème est résolu, effacez cette alerte de la page des alertes.|

### Alertes de test

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Il s'agit d'un message de test envoyé à partir de votre appareil StorSimple. Votre administrateur StorSimple vous a ajouté comme destinataire de notifications d’alertes pour l’appareil : <*nom de l’appareil*>.|Tester l’e-mail de notification d'alerte.|Si vous pensez que vous avez reçu ce message par erreur, veuillez contacter votre administrateur StorSimple.|

### Alertes de mise à jour

|Texte d'alerte|Événement|Plus d'informations/actions recommandées|
|:---|:---|:---|
|Correctif logiciel installé.|Mise à jour du logiciel ou du microprogramme terminée.|Le correctif logiciel est installé correctement sur votre appareil.|
|Mises à jour manuelles disponibles.|Notification des mises à jour disponibles.|Utilisez l'interface Windows PowerShell pour StorSimple sur votre appareil pour installer ces mises à jour.|
|Nouvelles mises à jour disponibles.|Notification des mises à jour disponibles.|Vous pouvez installer ces mises à jour à partir de la page **Maintenance** ou en utilisant l’interface de Windows PowerShell pour StorSimple sur votre appareil.|
|Échec de l’installation des mises à jour.|Les mises à jour n'ont pas été correctement installées.|Votre système n'a pas pu installer les mises à jour. Vous pouvez installer ces mises à jour à partir de la page **Maintenance** ou en utilisant l’interface de Windows PowerShell pour StorSimple sur votre appareil. Si le problème persiste, contactez le support technique Microsoft.|
|Impossible de vérifier automatiquement les nouvelles mises à jour.|Échec de la vérification automatique.|Vous pouvez rechercher manuellement les nouvelles mises à jour à partir de la page **Maintenance**.|
|Nouvel agent WUA disponible.|Notification de la mise à jour disponible.|Téléchargez l’agent Windows Update le plus récent et installez-le à partir de l'interface Windows PowerShell.|
|La version du composant de microprogramme <*ID du composant*> ne correspond pas au matériel.|Les mises à jour de microprogramme n'ont pas été correctement installées.|Veuillez contacter le Support Microsoft.|

## Étapes suivantes

[En savoir plus sur les erreurs StorSimple](storsimple-troubleshoot-operational-device.md).

<!---HONumber=September15_HO1-->