<properties 
    pageTitle="Notes de publication de StorSimple série 8000 Update 1"
    description="Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement associés à StorSimple série 8000 Update 1."
    services="storsimple"
    documentationCenter="NA"
    authors="SharS"
    manager="adinah"
    editor="tysonn" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="06/05/2015"
    ms.author="v-sharos" />

# Notes de publication de StorSimple série 8000 Update 1  

## Vue d'ensemble

Les notes de publication suivantes décrivent les nouvelles fonctionnalités et identifient les problèmes critiques non résolus relatifs à StorSimple série 8000 Update 1. Elles contiennent également la liste des mises à jour logicielles et de microprogramme incluses dans cette version. Il s’agit de la première version importante postérieure à la mise à disposition générale de la version StorSimple série 8000, en juillet 2014.

Cette mise à jour implique un changement de nom du logiciel de l’appareil, qui devient StorSimple série 8000 Update 1. Lisez les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple. Pour en savoir plus, consultez la section relative à l’[installation d’Update 1 sur votre appareil StorSimple](storsimple-install-update-1.md).

Lisez les informations contenues dans les notes de publication avant de déployer les mises à jour dans votre solution StorSimple.

>[AZURE.IMPORTANT]
> 
- Pour installer Update 1, utilisez le service StorSimple Manager, et non Windows PowerShell pour StorSimple.
- Cette version comporte également des mises à jour de microprogramme du disque, qui peuvent être appliquées uniquement lorsque l’appareil est en mode Maintenance. Il s’agit des mises à jour entraînant des temps d’arrêt de votre appareil. Vous pouvez appliquer ces mises à jour durant les activités de maintenance planifiée.
- L’installation de cette mise à jour prend entre 5 et 10 heures (en comptant les mises à jour Windows). 
- Pour les nouvelles versions, vous ne voyez pas immédiatement les mises à jour, car nous effectuons un déploiement échelonné des mises à jour. Revérifiez les mises à jour dans quelques jours, car elles seront bientôt disponibles.

## Nouveautés de Update 1

Cette mise à jour contient les nouvelles fonctionnalités et améliorations suivantes :

- **Migration des appareils de la série 5000-7000 vers les appareils de la série 8000** - Cette version présente une nouvelle fonction de migration qui permet aux utilisateurs des appliances de la série 5000 7000 de migrer leurs données sur une appliance physique StorSimple série 8000 ou sur une appliance virtuelle 1100. La fonctionnalité de migration présente deux propositions de valeur principales :                                                                  

    - La **continuité d’activité**, via la prise en charge de la migration des données existantes des appliances de la série 5000-7000 sur les appliances de la série 8000.
    - **Offres améliorées des fonctionnalités des appliances de la série 8000**, telles que la gestion centralisée et efficace de plusieurs appliances via le service StorSimple Manager, une classe optimisée de matériel et un microprogramme mis à jour, des appliances virtuelles, la mobilité des données ou encore les fonctions de la feuille de route à venir.

    Consultez le [guide de migration](http://www.microsoft.com/download/details.aspx?id=47322) pour en savoir plus sur la migration des données des appareils StorSimple série 5000-7000 vers les appareils de la série 8000.

- **Disponibilité sur le portail d’administration de Microsoft Azure** - StorSimple est désormais disponible sur le portail d’administration de Microsoft Azure. Consultez la section relative au [déploiement d’un appareil StorSimple sur le portail d’administration de Microsoft Azure](storsimple-deployment-walkthrough-gov.md).

- **Prise en charge d’autres fournisseurs de services cloud** - Les autres fournisseurs de services cloud pris en charge sont Amazon S3, Amazon S3 with RRS, HP et OpenStack (bêta).

- **Mise à jour vers les API de stockage les plus récentes** - Dans cette version, StorSimple a été mis à jour vers les API de service Microsoft Azure Storage les plus récentes. Les appareils StorSimple série 8000 exécutant le logiciel de disponibilité générale utilisent des versions des API de service de Microsoft Azure Storage antérieures au 12 février 2012. Comme indiqué dans l’[annonce du retrait des versions de service de Microsoft Azure Storage](http://azure.microsoft.com/blog/2014/08/04/microsoft-azure-storage-service-version-removal/), ces API seront rendues obsolètes le 10 décembre 2015. Il est impératif que vous installiez la version StorSimple série 8000 Update 1 avant le 9 décembre 2015. Si vous ne procédez pas à cette mise à jour, vos appareils StorSimple ne fonctionneront plus correctement.

- **Prise en charge du stockage redondant dans une zone** - Avec la mise à niveau vers la dernière version des API Microsoft Azure Storage, les appareils StorSimple série 8000 prendront en charge le stockage redondant dans une zone, en plus du stockage localement redondant et du stockage géo-redondant. Pour en savoir plus sur le stockage redondant dans une zone, consultez cet [article sur les options de redondance dans Microsoft Azure Storage](../storage/storage-redundancy.md).

- **Expérience améliorée de déploiement initial et de mise à jour** - Dans cette version, les processus d’installation et de mise à jour ont été améliorés. L’installation via l’assistant de configuration permet désormais de signaler à l’utilisateur les paramétrages incorrects du pare-feu et les configuration réseau inappropriées. Des applets de commande de diagnostic supplémentaires ont été ajoutées, ceci pour vous permettre de résoudre les problèmes de mise en réseau de l’appareil. Pour en savoir plus sur ces applets de commande de diagnostic, consultez l’[article sur le déploiement de la résolution des problèmes](storsimple-troubleshoot-deployment.md).

## Problèmes résolus dans Update 1


Le tableau suivant récapitule les problèmes qui ont été résolus dans cette mise à jour.

| N° | Fonctionnalité | Problème | S’applique à un appareil physique | S’applique à un appareil virtuel |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Windows PowerShell pour StorSimple | Lorsqu’un utilisateur accédait à distance à l’appareil StorSimple à l’aide de Windows PowerShell pour StorSimple, puis lançait l’assistant de configuration, un incident se produisait dès la saisie de l’adresse IP de Data 0. Ce bogue a été corrigé dans Update 1. | Oui | Oui |
| 2 | Réinitialisation aux paramètres d’usine | Dans certains cas, lorsque vous effectuiez une réinitialisation aux paramètres d’usine, l’appareil StorSimple se bloquait et affichait le message suivant : **Réinitialisation aux paramètres d’usine en cours (phase 8)**. Cela se produisait si vous appuyiez sur les touches CTRL + C lors de l’exécution de l’applet de commande. Ce bogue a été corrigé.| Oui | Non |
| 3 | Réinitialisation aux paramètres d’usine | Après l’échec de la réinitialisation aux paramètres d’usine d’un contrôleur double, vous pouviez procéder à l’inscription de l’appareil. Cela entraînait la mise en place d’une configuration système non prise en charge. Dans Update 1, un message d’erreur s’affiche et l’inscription de l’appareil présentant un échec de réinitialisation aux paramètres d’usine est bloquée. | Oui | Non |
| 4 | Réinitialisation aux paramètres d’usine | Dans certains cas, des alertes d’incompatibilité illégitimes étaient émises. Les alertes illégitimes d’incompatibilité ne seront plus générées sur les appareils exécutant Update 1. | Oui | Non |
| 5 | Réinitialisation aux paramètres d’usine | Si une réinitialisation aux paramètres d’usine a été interrompue avant la fin de l’opération, l’appareil passait en mode de récupération ; vous ne pouviez plus accéder à Windows PowerShell pour StorSimple. Ce bogue a été corrigé. | Oui | Non |
| 6 | Récupération d'urgence | Un bogue de récupération d’urgence a été corrigé. Auparavant, la récupération était mise en échec durant la découverte des sauvegardes sur l’appareil cible. | Oui | Oui |
| 7 | LED de contrôle | Dans certains cas, les LED de contrôle situées à l’arrière de l’appliance n’indiquaient pas le statut approprié. La LED bleue était désactivée. Les LED de DATA 0 et DATA 1 clignotaient, même lorsque ces interfaces n’étaient pas configurées. Ce problème a été corrigé ; les LED de contrôle indiquent désormais le statut correct. | Oui | Non |
| 8 | Interfaces réseau | Dans les versions précédentes, un appareil StorSimple configuré avec une passerelle non routable pouvait se déconnecter. Dans cette version, le métrique de routage de l’interface Data 0 a été définie sur la valeur la plus faible. En conséquence, même si d’autres interfaces réseau sont déployées sur le cloud, l’ensemble du trafic cloud de l’appareil sera routé via Data 0. | Oui | Oui | 

## Problèmes connus dans Update 1

Le tableau suivant récapitule les problèmes connus de cette version.

| N° | Fonctionnalité | Problème | Commentaires/solution de contournement | S’applique à un appareil physique | S’applique à un appareil virtuel |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Disque quorum | Dans de rares cas, si la majorité des disques du boîtier EBOD d’un appareil 8600 sont déconnectés, ce qui signifie qu’il n’y a pas de disque quorum, le pool de stockage est hors connexion. Il reste hors connexion même si les disques sont reconnectés. | Vous devez redémarrer l’appareil. Si le problème persiste, contactez le support technique Microsoft. | Oui | Non |
| 2 | ID de contrôleur incorrect | Lorsqu’un contrôleur est remplacé, le contrôleur 0 peut apparaître comme contrôleur 1. Pendant le remplacement du contrôleur, lorsque l’image est chargée à partir du nœud homologue, l’ID du contrôleur peut s’afficher initialement comme l’ID du contrôleur homologue. Dans de rares cas, ce comportement peut également se produire après un redémarrage du système. | Aucune action utilisateur n’est requise. Cette situation se résout automatiquement une fois le contrôleur remplacé. | Oui | Non |
| 3 | Comptes de stockage | La suppression du compte de stockage à l’aide du service de stockage n’est pas prise en charge. En effet, cette opération donnerait lieu à une situation dans laquelle il serait impossible de récupérer les données utilisateur. | Oui | Oui |
| 4 | Basculement de l’appareil | Le basculement multiple d’un conteneur de volumes d’un même appareil source vers différents appareils cibles n’est pas pris en charge. Si un appareil inactif est basculé vers plusieurs appareils, les conteneurs de volumes du premier appareil basculé perdent la propriété des données. Après un basculement de ce type, les conteneurs de volumes concernés apparaissent ou se comportent différemment lorsque vous les affichez dans le portail de gestion. | | Oui | Non |
| 5 | Installation | Lors de l’installation de l’adaptateur StorSimple pour SharePoint, vous devez fournir une adresse IP d’appareil pour que l’installation s’effectue correctement. | | Oui | Non |
| 6 | Proxy web | Si HTTPS est défini comme protocole dans la configuration du proxy web, la communication appareil-service est altérée et l’appareil se met hors connexion. Des packages de prise en charge sont également générés, ce qui consomme de nombreuses ressources de l’appareil. | Vérifiez que le protocole défini pour l’URL du proxy web est bien HTTP. Pour plus d’informations, consultez la section [Configuration du proxy web pour votre appareil](https://msdn.microsoft.com/library/azure/dn764937.aspx). | Oui | Non |
| 7 | Proxy web | Si vous configurez et activez le proxy web sur un appareil inscrit, vous devez redémarrer le contrôleur actif sur votre appareil. | | Oui | Non |
| 8 | Latence de cloud élevée et charge de travail d’E/S élevée | Lorsque l’appareil StorSimple doit gérer à la fois des latences de cloud très élevées (de l’ordre de quelques secondes) et une charge de travail d’E/S élevée, ses volumes se détériorent et une défaillance peut se produire au niveau des E/S, avec l’erreur « appareil non prêt ». | Vous devez redémarrer les contrôleurs de l’appareil manuellement ou effectuer un basculement d’appareil pour résoudre ce problème. | Oui | Non |
| 9 | Azure PowerShell | Lorsque vous utilisez l’applet de commande StorSimple **Get-AzureStorSimpleStorageAccountCredential | Select-Object -First 1 -Wait** pour sélectionner le premier objet afin de créer un objet **contrôleur de volumes**, l’applet de commande renvoie l’ensemble des objets. | Encapsulez l’applet de commande entre parenthèses comme suit : **(Get-Azure-StorSimpleStorageAccountCredential) | Select-Object -First 1 -Wait** | Oui | Oui |
| 10| Migration | Lorsque plusieurs conteneurs de volumes sont transmis pour migration, l’heure prévue de la dernière sauvegarde est exacte uniquement pour le premier conteneur de volumes. Par ailleurs, la migration parallèle démarre après la migration des quatre premières sauvegardes du premier conteneur de volumes. | Nous vous recommandons de migrer un seul conteneur de volumes à la fois. | Oui | Non |
| 11| Migration | Après la restauration, les volumes ne sont pas ajoutés à la stratégie de sauvegarde ni au groupe de disques virtuels. | Pour créer les sauvegardes, il vous faudra ajouter ces volumes à une stratégie de sauvegarde. | Oui | Oui |
| 12| Migration | Une fois la migration terminée, l’appareil de série 5000/7000 ne doit pas accéder aux conteneurs de données migrées. | Nous vous recommandons de supprimer les conteneurs de données migrées une fois que la migration est terminée et validée. | Oui | Non |
| 13.| Clonage et récupération d’urgence | Un appareil StorSimple exécutant Update 1 ne peut pas cloner un appareil exécutant une version logicielle antérieure, ni exécuter de récupération d’urgence sur ce dernier. | Pour être en mesure d’effectuer ces opérations, vous devrez mettre à jour l’appareil cible vers Update 1. | Oui | Oui |
| 14 | Migration | La sauvegarde de la configuration pour la migration peut être mise en échec sur un appareil de série 5000-7000 lorsqu’aucun volume n’est associé à certains groupes de volumes. | Supprimez l’ensemble des groupes de volumes vides ne présentant aucun volume associé, puis réessayez la sauvegarde de configuration.| Oui | Non |

## Mises à jour des appareils physiques dans Update 1

Lorsque ces mises à jour sont appliquées à un appareil physique, la version logicielle 6.3.9600.17491 est activée.

## Mises à jour du microprogramme et du contrôleur Serial-Attached SCSI (SAS) dans Update 1

Cette version met à jour le pilote et le microprogramme du contrôleur SAS de votre appareil physique. Elle met également à jour le microprogramme du disque sur votre appareil.
 
- Pour en savoir plus sur la mise à jour du contrôleur SAS, consultez la section [Update 1 pour les contrôleurs SAS LSI dans Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3043005). 

- Pour en savoir plus sur la mise à jour du microprogramme, consultez la section [Mise à jour Update 1 du microprogramme pour Microsoft Azure StorSimple Appliance](https://support.microsoft.com/kb/3063414).

- Pour en savoir plus sur la mise à jour du microprogramme de disque, consultez la section [Mise à jour Update 1 du microprogramme de disque pour Microsoft Azure StorSimple Appliance](https://support.microsoft.com/fr-fr/kb/3063416).
 
## Mises à jour des appareils virtuels dans Update 1

Cette version ne contient aucune mise à jour pour l’appareil virtuel. L’installation de cette mise à jour ne modifie pas la version logicielle de l’appareil virtuel.

## Étapes suivantes

- [Installer Update 1 sur votre appareil](storsimple-install-update-1.md)
 

<!---HONumber=58_postMigration-->