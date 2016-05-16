<properties 
   pageTitle="Appareil virtuel StorSimple Update 1 | Microsoft Azure"
   description="Apprenez à créer, déployer et gérer un appareil virtuel StorSimple dans un réseau virtuel Microsoft Azure. (S'applique à StorSimple Update 1)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/29/2016"
   ms.author="alkohli" />

# Déployer et gérer un appareil virtuel StorSimple dans Azure

##Vue d’ensemble
L’appareil virtuel StorSimple est une fonctionnalité supplémentaire fournie avec votre solution Microsoft Azure StorSimple. L’appareil virtuel StorSimple s’exécute sur une machine virtuelle dans un réseau virtuel Microsoft Azure. Vous pouvez l’utiliser pour sauvegarder et cloner les données à partir de vos ordinateurs hôtes. Les rubriques suivantes vous aideront à découvrir, à configurer et à utiliser l’appareil virtuel StorSimple.

- Différences entre l’appareil virtuel et l’appareil physique

- Considérations de sécurité relatives à l’utilisation d’un appareil virtuel

- Configuration requise pour l’appareil virtuel

- Création et configuration de l’appareil virtuel

- Utilisation de l’appareil virtuel

- Basculement vers l’appareil virtuel

- Arrêt ou suppression de l’appareil virtuel

Ce didacticiel s’applique à tous les appareils virtuels StorSimple exécutant Update 1.

## Différences entre l’appareil virtuel et l’appareil physique

L’appareil virtuel StorSimple est une version logicielle de StorSimple qui s’exécute sur un seul nœud dans une machine virtuelle Microsoft Azure. L’appareil virtuel prend en charge les scénarios de récupération d’urgence dans lesquels votre appareil physique n’est pas disponible. Il est approprié pour une utilisation dans les scénarios de récupération au niveau des éléments à partir de sauvegardes, de récupération d’urgence en local et de développement et de test dans le cloud.

### Différences par rapport à l’appareil physique

Voici quelques différences clés entre l’appareil virtuel StorSimple et l’appareil physique StorSimple :

- L’appareil virtuel a une seule interface réseau : DATA 0. L’appareil physique comporte six interfaces réseau : de DATA 0 à DATA 5.
- L’appareil virtuel est inscrit au cours de l’étape de configuration et non comme une tâche distincte.
- Vous ne pouvez pas régénérer la clé de chiffrement des données de service à partir de l’appareil virtuel. Au cours de la substitution de la clé, vous régénérez la clé sur l’appareil physique, puis mettez à jour l’appareil virtuel avec la nouvelle clé.
- Si des mises à jour doivent être appliquées à l’appareil virtuel, celui-ci sera confronté à un temps d’arrêt, ce qui n’est pas le cas avec l’appareil physique.

## Considérations de sécurité relatives à l’utilisation d’un appareil virtuel

Gardez à l’esprit les considérations de sécurité suivantes lorsque vous utilisez l’appareil virtuel StorSimple :

- L’appareil virtuel est sécurisé via votre abonnement Microsoft Azure. Cela signifie que si vous utilisez l’appareil virtuel et que votre abonnement Azure est compromis, les données stockées sur votre appareil virtuel sont également vulnérables.

- La clé publique du certificat permettant de chiffrer les données stockées dans Azure StorSimple est accessible en toute sécurité dans le Portail Azure Classic, et la clé privée est conservée avec l’appareil StorSimple. Sur l’appareil virtuel StorSimple, les clés publiques et privées sont stockées dans Azure.

- L’appareil virtuel est hébergé dans le centre de données Microsoft Azure.


## Configuration requise pour l’appareil virtuel

Les sections suivantes vous aideront à vous préparer à utiliser l’appareil virtuel StorSimple.

### Conditions requises pour Azure

Avant d’approvisionner l’appareil virtuel, vous devez effectuer les préparatifs suivants dans votre environnement Azure :

- Pour l’appareil virtuel, [configurez un réseau virtuel sur Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). 
- Il est recommandé d’utiliser le serveur DNS par défaut fourni par Azure au lieu de spécifier le nom de votre propre serveur DNS. Si le nom de votre serveur DNS n'est pas valide, la création de l’appareil virtuel échoue.
- Les options de point à site et de site à site sont facultatives (non obligatoires). Si vous le souhaitez, vous pouvez configurer ces options pour des scénarios plus avancés. 

>[AZURE.IMPORTANT] **Assurez-vous que le réseau virtuel est situé dans la même région que les comptes de stockage cloud que vous allez utiliser avec l’appareil virtuel.**

- Vous pouvez créer des [Machines virtuelles Azure](../virtual-machines/virtual-machines-linux-about.md) (serveurs hôtes) dans le réseau virtuel qui peut utiliser les volumes exposés par l’appareil virtuel. Ces serveurs doivent répondre aux exigences suivantes : 							
	- Il doit s’agir de machines virtuelles Windows ou Linux sur lesquelles l’initiateur iSCSI est installé
	- Ils doivent être en cours d’exécution dans le même réseau virtuel que l’appareil virtuel
	- Ils doivent être en mesure de se connecter à la cible iSCSI de l’appareil virtuel via l’adresse IP interne de ce dernier

- Vérifiez que vous avez configuré la prise en charge de iSCSI et du trafic cloud sur le même réseau virtuel.

### Conditions requises pour StorSimple

Apportez les mises à jour suivantes à votre service Azure StorSimple avant de créer un appareil virtuel :


- Ajoutez des [enregistrements de contrôle d’accès](storsimple-manage-acrs.md) pour les machines virtuelles qui vont être les serveurs hôtes de votre appareil virtuel.

- Assurez-vous que vous disposez d’un [compte de stockage](storsimple-manage-storage-accounts.md#add-a-storage-account) dans la même région que l’appareil virtuel. Des comptes de stockage dans différentes régions peuvent entraîner une dégradation des performances.

- Assurez-vous que vous utilisez un compte de stockage différent pour la création de l’appareil virtuel de celui utilisé pour vos données. L’utilisation du même compte de stockage peut entraîner une dégradation des performances.

Assurez-vous de disposer des informations suivantes avant de commencer :


- Vous disposez d’un compte avec les informations d’identification d’accès au Portail Azure Classic.

- Vous disposez des informations d’identification d’accès au compte de stockage Azure.

- Vous avez une copie de la clé de chiffrement de données de service de votre appareil physique.

- Vous avez une copie de la clé de chiffrement du stockage cloud pour chaque conteneur de volume.


## Création et configuration de l’appareil virtuel

Avant d’effectuer ces procédures, assurez-vous que vous avez rempli les [conditions requises pour l’appareil virtuel](#prerequisites-for-the-virtual-device).

Après avoir effectué ces procédures, vous êtes prêt à [utiliser l’appareil virtuel](#work-with-the-StorSimple-virtual-device).

### Création de l’appareil virtuel

Après avoir créé un réseau virtuel, configuré un service StorSimple Manager et inscrit votre appareil physique StorSimple auprès du service, vous pouvez suivre les étapes suivantes pour créer un appareil virtuel StorSimple.

Procédez comme suit pour créer l’appareil virtuel StorSimple.

1.  Dans le Portail Azure Classic, accédez au service **StorSimple Manager**.

2. Accédez à la page **Appareils**. Cliquez sur **Créer un appareil virtuel** en bas de la page **Appareils**.

3. Dans la boîte de dialogue **Créer un appareil virtuel**, spécifiez les éléments suivants.

     ![Création d’un appareil virtuel StorSimple](./media/storsimple-virtual-device-u1/StorSimple_CreateVirtualDevice1.png)

	1. **Nom** : nom unique de votre appareil virtuel.

	2. **Version** : sélectionnez la version de l’appareil virtuel. Cette option est absente si vous n’avez inscrit que les appareils physiques de version Update 1 (ou ultérieure) auprès de ce service. Ce champ n’apparaît que si vous avez une combinaison d’appareils physiques de la mise à jour préliminaire 1 et de mise à jour postérieure 1 inscrits auprès du service. Comme la version de l’appareil virtuel détermine l’appareil physique à partir duquel vous pouvez basculer ou cloner, il est important de créer une version appropriée de l’appareil virtuel. Sélectionnez :

	   - la version Update 0.3 en cas de basculement ou de clonage à partir d’un appareil physique avec version GA ou version Update 0.1 à 0.3. 
	   - la version Update 1 en cas de basculement ou de clonage à partir d’un appareil physique avec Update 1 (ou version ultérieure). La sélection d’Update 1 dans la liste déroulante entraîne l’approvisionnement d’un appareil virtuel Update 1.1.
 
	3. **Réseau virtuel** – Nom du réseau virtuel que vous souhaitez utiliser avec cet appareil virtuel.

	4. **Sous-réseau** – Sous-réseau du réseau virtuel pour une utilisation avec l’appareil virtuel.

	5. **Compte de stockage pour la création d’un appareil virtuel** – Compte de stockage qui contient l'image de l'appareil virtuel lors de l'approvisionnement et héberge les disques de l'appareil virtuel après l'approvisionnement. Ce compte de stockage doit être situé dans la même région que l’appareil virtuel et le réseau virtuel. Il ne doit pas être utilisé pour le stockage des données par l’appareil physique ou virtuel. Par défaut, un compte de stockage est créé à cet effet. Toutefois, si vous avez déjà un compte de stockage qui convient pour cette utilisation, vous pouvez le sélectionner dans la liste.

    >[AZURE.NOTE] L’appareil virtuel ne peut fonctionner qu’avec les comptes de stockage Azure. Les autres fournisseurs de services cloud tels qu’Amazon, HP et OpenStack (pris en charge pour l’appareil physique) ne sont pas pris en charge pour l’appareil virtuel StorSimple.
	
4. Cliquez sur la coche pour indiquer que vous savez que les données stockées sur l’appareil virtuel sont hébergées dans un centre de données Microsoft. Si vous utilisez uniquement un appareil physique, votre clé de chiffrement est conservée avec celui-ci ; par conséquent, Microsoft ne peut pas le déchiffrer. ![Étape de création de l’appareil virtuel StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceCreating1M.png)

    Lorsque vous utilisez un appareil virtuel, la clé de chiffrement et la clé de déchiffrement sont stockées dans Microsoft Azure. Pour plus d’informations, consultez la page [Considérations de sécurité relatives à l’utilisation d’un appareil virtuel](#security-considerations-for-using-a-virtual-device).

### Configuration et inscription de l’appareil virtuel

Avant de commencer cette procédure, assurez-vous que vous disposez d’une copie de la clé de chiffrement des données de service. Cette clé de chiffrement a été créée lorsque vous avez configuré votre premier appareil StorSimple et que vous avez été invité à l’enregistrer dans un emplacement sécurisé. Si vous n’avez pas de copie de la clé de chiffrement des données de service, vous devez contacter le support technique de Microsoft pour obtenir de l’aide.

Procédez comme suit pour configurer et inscrire l’appareil virtuel StorSimple.

1. Sélectionnez l’**appareil virtuel StorSimple** que vous venez de créer dans la page Appareils. 

2. Cliquez sur **Terminer la configuration de l’appareil**. L’Assistant Configurer l’appareil démarre.

    ![Installation complète de l’appareil StorSimple à la page Appareils](./media/storsimple-virtual-device-u1/StorSimple_CompleteDeviceSetupSVA1M.png)

3. Entrez la **clé de chiffrement des données de service** dans l’espace fourni.

4. Entrez les mots de passe d’administrateur de l’appareil et du Gestionnaire d'instantanés conformes à la longueur et aux paramètres spécifiés.

5. Cliquez sur la coche pour terminer la configuration initiale et l’inscription de l’appareil virtuel.

    ![Paramètres de l’appareil virtuel StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceSettings1.png)

Une fois la configuration et l'inscription terminées, l’appareil est mis en ligne. (La mise en ligne de l’appareil peut prendre plusieurs minutes.)

![Étape en ligne de l’appareil virtuel StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceOnline1M.png)


### Modification des paramètres de configuration de l’appareil

La section suivante décrit les paramètres de configuration d’appareil que vous pouvez configurer pour l’appareil virtuel StorSimple si vous envisagez d’utiliser CHAP, le Gestionnaire d'instantanés StorSimple ou de modifier le mot de passe d’administrateur de l’appareil.

#### Configuration de l’initiateur CHAP (facultatif)

Ce paramètre contient les informations d’identification que votre appareil virtuel (cible) attend des initiateurs (serveurs) qui tentent d’accéder aux volumes. Les initiateurs fournissent un nom d’utilisateur et un mot de passe CHAP pour s’identifier auprès de votre appareil au cours de cette authentification.

#### Configuration de la cible CHAP (facultatif)

Ce paramètre contient les informations d’identification que votre appareil virtuel utilise lorsqu’un initiateur CHAP demande une authentification mutuelle ou bidirectionnelle. Votre appareil virtuel utilise un nom d’utilisateur et un mot de passe CHAP inversés pour s’identifier auprès de l’initiateur pendant le processus d’authentification. Notez que les paramètres CHAP cibles sont des paramètres globaux. Lorsqu’ils sont appliqués, tous les volumes connectés à l’appareil virtuel de stockage utilisent l’authentification CHAP. Sélectionnez votre appareil dans la page Appareils. Accédez à la page Configurer à l’intérieur de la page Appareils et faites défiler la page vers le bas jusqu’à la section CHAP.

#### Configuration du Gestionnaire d’instantanés StorSimple (facultatif)

Le Gestionnaire d’instantanés StorSimple réside sur l’ordinateur hôte Windows et permet aux administrateurs de gérer les sauvegardes de votre appareil StorSimple sous la forme d’instantanés cloud ou locaux.

>[AZURE.NOTE] Pour l’appareil virtuel, l’ordinateur hôte Windows est une machine virtuelle Azure.

Lorsque vous configurez un appareil dans le Gestionnaire d’instantanés StorSimple, vous devez fournir l’adresse IP et le mot de passe de l’appareil StorSimple pour authentifier votre appareil de stockage.

Pour modifier le mot de passe du Gestionnaire d’instantanés StorSimple, procédez comme suit.

1. Sur votre appareil virtuel, accédez à **Appareils > Configurer**.

2. Accédez à la section **Gestionnaire d’instantanés**. Entrez un mot de passe 14 ou 15 caractères. Assurez-vous que le mot de passe contient 3 caractères sur 4 en majuscules, minuscules, chiffres et caractères spéciaux.

3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer** au bas de la page.

Le mot de passe du Gestionnaire d’instantanés StorSimple est maintenant mis à jour et peut être utilisé lorsque vous authentifiez vos ordinateurs hôtes Windows.

#### Modification du mot de passe d’administrateur de l’appareil

Lorsque vous utilisez l’interface Windows PowerShell pour accéder à l’appareil virtuel, vous devez entrer un mot de passe Administrateur d’appareil. Pour la sécurité de vos données, vous êtes obligé de changer ce mot de passe avant de pouvoir utiliser l’appareil virtuel.

Pour modifier le mot de passe d’administrateur de votre appareil virtuel StorSimple, procédez comme suit.

1. Sur votre appareil virtuel, accédez à **Appareils > Configurer**.
 
2. Accédez à la section **Mot de passe Administrateur d’appareil**. Indiquez un mot de passe Administrateur contenant entre 8 et 15 caractères. Le mot de passe doit contenir 3 caractères sur 4 en majuscules, minuscules, chiffres et caractères spéciaux.

3. Confirmez le mot de passe.
 
4. Cliquez sur **Enregistrer** au bas de la page.

Le mot de passe Administrateur d’appareil doit maintenant être à jour. Vous allez utiliser le mot de passe modifié pour accéder à l’interface Windows PowerShell sur votre appareil virtuel.

#### Configuration de l’administration à distance (facultatif)

L’accès à distance à votre appareil virtuel via l’interface Windows PowerShell n’est pas activé par défaut. Vous devez tout d’abord activer la gestion à distance sur l’appareil virtuel, puis sur le client qui sera utilisé pour accéder à votre appareil virtuel.

Vous pouvez choisir de vous connecter via HTTP ou HTTPS. Pour des raisons de sécurité, nous vous recommandons d’utiliser HTTPS avec un certificat auto-signé pour vous connecter à votre appareil virtuel.

Procédez comme suit pour configurer la gestion à distance de l’appareil virtuel StorSimple.


1. Sur votre appareil virtuel, accédez à **Appareils > Configurer**.

2. Accédez à la section **Gestion à distance**.

3. Définissez **Activer la gestion à distance** sur **Oui**.

4. Vous pouvez maintenant choisir de vous connecter à l’aide de HTTP. La valeur par défaut consiste à se connecter via HTTPS. Une connexion via HTTP est acceptable uniquement sur des réseaux approuvés.

5. Cliquez sur **Télécharger le certificat de gestion à distance** pour télécharger un certificat de gestion à distance. Spécifiez un emplacement dans lequel enregistrer le fichier. Ce certificat doit ensuite être installé sur l’ordinateur client ou hôte que vous utiliserez pour vous connecter à l’appareil virtuel.

6. Cliquez sur **Enregistrer** au bas de la page.


## Utilisation de l’appareil virtuel StorSimple

Maintenant que vous avez créé et configuré l’appareil virtuel StorSimple, vous êtes prêt à commencer à l’utiliser. Vous pouvez utiliser des conteneurs de volumes, des volumes et des stratégies de sauvegarde sur un appareil virtuel comme vous le feriez sur un appareil physique StorSimple ; la seule différence est que vous devez vous assurer que vous sélectionnez l’appareil virtuel à partir de votre liste d’appareils. Reportez-vous aux sections suivantes pour obtenir des instructions sur les tâches associées :


- [Conteneurs de volume](storsimple-manage-volume-containers.md)

- [Volumes](storsimple-manage-volumes.md)

- [Stratégies de sauvegarde](storsimple-manage-backup-policies.md)

Les sections suivantes présentent les différences que vous rencontrez lorsque vous utilisez l’appareil virtuel.

### Maintenance d’un appareil virtuel StorSimple

Comme il s’agit d’un appareil logiciel uniquement, la maintenance de l’appareil virtuel est minime par rapport à la maintenance de l’appareil physique. Vous disposez des options suivantes :

- **Mises à jour logicielles** – Vous pouvez afficher la date de dernière mise à jour du logiciel, ainsi que les messages d’état de mise à jour. Vous pouvez utiliser le bouton **Rechercher les mises à jour** en bas de la page pour effectuer une recherche manuelle des nouvelles mises à jour. Si les mises à jour sont disponibles, cliquez sur **Installer les mises à jour**. Étant donné qu’il n’y a qu’une interface sur l’appareil virtuel, cela signifie qu’il y aura une brève interruption de service lors de l’application des mises à jour. L’appareil virtuel s’arrête et redémarre (si nécessaire) pour appliquer les mises à jour publiées.
- **Package de prise en charge** – Vous pouvez créer et télécharger un package de prise en charge pour aider le support Microsoft à résoudre les problèmes que vous rencontrez avec votre appareil virtuel.

### Comptes de stockage d’un appareil virtuel

Les comptes de stockage sont créés pour une utilisation par l’appareil physique, par le service StorSimple Manager et par l’appareil virtuel. Lorsque vous créez vos comptes de stockage, nous vous recommandons d’utiliser un identificateur de région avec un nom convivial pour garantir la cohérence entre les différents composants système. Pour un appareil virtuel, il est important que tous les composants soient situés dans la même région afin d’éviter les problèmes de performances.

### Désactivation d’un appareil virtuel StorSimple

La désactivation d’un appareil virtuel supprime la machine virtuelle et les ressources créées lors de son approvisionnement. Une fois l’appareil virtuel désactivé, il ne peut pas être restauré vers son état précédent. Avant de désactiver l’appareil virtuel, arrêtez ou supprimez les clients et ordinateurs hôtes qui en dépendent.

La désactivation d’un appareil virtuel entraîne les actions suivantes :

- L’appareil virtuel est supprimé.

- Les disques de données et de système d’exploitation de l’appareil virtuel sont supprimés.

- Le service hébergé et le réseau virtuel créés lors de l’approvisionnement sont conservés. Si vous ne les utilisez pas, vous devez les supprimer manuellement.

- Les instantanés cloud créés pour l’appareil virtuel sont conservés.

Dès que l’appareil est indiqué comme désactivé dans la page du service StorSimple Manager, vous pouvez le supprimer de la liste des appareils du service StorSimple Manager.

### Accès à distance à un appareil virtuel StorSimple

Après l’avoir activé dans la page de configuration de l’appareil StorSimple, vous pouvez utiliser l’accès distant Windows PowerShell pour vous connecter à l’appareil virtuel à partir d’une autre machine virtuelle située dans le même réseau virtuel. Par exemple, vous pouvez vous connecter à partir de la machine virtuelle que vous avez configurée et utilisée pour vous connecter à iSCSI. Dans la plupart des déploiements, vous avez déjà ouvert un point de terminaison public pour accéder à votre machine virtuelle hôte que vous pouvez utiliser pour accéder à l’appareil virtuel.

>[AZURE.WARNING] **Pour renforcer la sécurité, nous vous recommandons d’utiliser le protocole HTTPS lors de la connexion aux points de terminaison et de supprimer ces derniers à la fin de la session à distance PowerShell.**

Vous devez respecter les procédures de la rubrique [Connexion à distance à votre appareil StorSimple](storsimple-remote-connect.md) afin de configurer la communication à distance pour votre appareil virtuel.

Toutefois, si vous souhaitez vous connecter directement à l’appareil virtuel à partir d’un autre ordinateur en dehors du réseau virtuel ou de l’environnement Microsoft Azure, vous devez créer des points de terminaison supplémentaires comme décrit dans la procédure suivante.

Procédez comme suit pour créer un point de terminaison public sur l’appareil virtuel :

1. Connectez-vous à la version classique du portail Azure.

- Cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle utilisée comme appareil virtuel.

- Cliquez sur **Endpoints**. La page Points de terminaison répertorie tous les points de terminaison de la machine virtuelle.

- Cliquez sur **Ajouter**. La boîte de dialogue Ajouter un point de terminaison s’affiche. Cliquez sur la flèche pour continuer.

- Pour le **Nom**, tapez le nom de point de terminaison suivant : **WinRMHttps**.

- Pour le **Protocole**, indiquez **TCP**.

- Pour le **Port Public**, tapez les numéros de port que vous souhaitez utiliser pour la connexion.

- Pour le **Port privé**, indiquez **5986**.

- Cliquez sur la coche pour créer le point de terminaison.

Une fois le point de terminaison créé, vous pouvez afficher les détails correspondants pour déterminer l’adresse IP virtuelle publique. Enregistrez cette adresse.

Nous vous recommandons de vous connecter à partir d’une autre machine virtuelle à l’intérieur du même réseau virtuel, car cela réduit le nombre de points de terminaison publics sur votre réseau virtuel. Lorsque vous utilisez cette méthode, vous vous connectez simplement à la machine virtuelle via une session Bureau à distance, puis vous configurez cette machine virtuelle pour utilisation, comme vous le feriez avec tout autre client Windows sur un réseau local. Il est inutile d’ajouter le numéro de port public, car le port est déjà connu.

### Arrêt et redémarrage

Contrairement à l’appareil physique StorSimple, aucun bouton d’alimentation ou de mise hors tension n’est présent sur un appareil virtuel StorSimple. Toutefois, il se peut que vous deviez arrêter et redémarrer l’appareil virtuel. Par exemple, certaines mises à jour peuvent nécessiter le redémarrage de la machine virtuelle pour terminer le processus de mise à jour. Le moyen le plus simple pour vous permettre de démarrer, d’arrêter et de redémarrer un appareil virtuel est d’utiliser la console de gestion des machines virtuelles.

Lorsque vous examinez la console de gestion, l’état de l’appareil virtuel est **En cours d’exécution**, car il est démarré par défaut après sa création. Vous pouvez arrêter et redémarrer une machine virtuelle à tout moment.

Pour arrêter un appareil virtuel, cliquez sur son nom, puis sur **Arrêter**. Lorsque l’appareil virtuel est en train de s’arrêter, son état est **Arrêt en cours**. Une fois l’appareil virtuel arrêté, son état est **Arrêté**.

Lorsqu’un appareil virtuel est en cours d’exécution et que vous souhaitez le redémarrer, cliquez sur son nom, puis sur **Redémarrer**. Lorsque l’appareil virtuel est en cours de redémarrage, son état est **Redémarrage en cours**. Lorsque l’appareil virtuel est prêt à être utilisé, son état est **En cours d’exécution**.

Vous pouvez également utiliser les applets de commande Windows PowerShell suivantes pour démarrer, arrêter et redémarrer l’appareil virtuel. Un exemple suit chaque applet de commande.

`Start-AzureVMC:\PS>Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    

`Stop-AzureVMC:\PS>Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Restart-AzureVMC:\PS>Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

### Rétablir les paramètres d’usine par défaut

Si vous décidez de repartir de zéro avec votre appareil virtuel, désactivez-le, supprimez-le, puis créez-en un nouveau. Comme c’est le cas également lorsque votre appareil physique est réinitialisé, aucune mise à jour n’est installée sur votre nouvel appareil virtuel. Par conséquent, recherchez les éventuelles mises à jour avant de l’utiliser.


## Basculement vers l’appareil virtuel

La récupération d’urgence est un des scénarios clés pour lequel l’appareil virtuel StorSimple a été conçu. Dans ce scénario, l’appareil physique StorSimple ou un centre de données entier peut ne pas être disponible. Heureusement, vous pouvez utiliser un appareil virtuel pour restaurer les opérations dans un autre emplacement. Pendant la récupération d’urgence, la propriété des conteneurs de volumes de l’appareil source change et ces derniers sont transférés vers l’appareil virtuel. Les conditions requises pour la récupération d’urgence sont les suivantes : l’appareil virtuel a été créé et configuré, tous les volumes du conteneur de volumes ont été mis hors connexion et le conteneur de volumes est associé à un instantané cloud.

>[AZURE.NOTE] Vous ne pouvez pas effectuer un basculement ou un clonage à partir d’un appareil exécutant Update 1 sur un appareil exécutant un logiciel de la mise à jour préliminaire 1. Si vous sélectionnez un appareil cible exécutant la mise à jour préliminaire 1, vous êtes averti que vous devez mettre à jour l’appareil cible avant d’effectuer le basculement.

### Pour restaurer votre appareil physique en fonction de l’appareil virtuel StorSimple

1. Vérifiez que le conteneur de volumes que vous souhaitez basculer est associé à des instantanés cloud.

2. Dans la page **Appareils**, cliquez sur l’onglet **Conteneurs de volumes**.

3. Sélectionnez un conteneur de volumes que vous souhaitez basculer vers l’appareil virtuel. Cliquez sur le conteneur de volumes pour afficher la liste des volumes qui y sont inclus. Sélectionnez un volume et cliquez sur **Déconnecter** pour mettre le volume hors connexion. Répétez ce processus pour tous les volumes dans le conteneur de volume.

4. Répétez l’étape précédente pour tous les conteneurs de volumes que vous souhaitez basculer vers l’appareil virtuel.

5. Dans la page **Appareils**, sélectionnez l’appareil que vous souhaitez basculer, puis cliquez sur **Basculement** pour ouvrir l’Assistant **Basculement d’appareil**.

6. Dans **Sélectionner le conteneur de volumes à basculer**, sélectionnez les conteneurs de volumes que vous souhaitez basculer. Pour figurer dans cette liste, le conteneur de volumes doit contenir un instantané cloud et être hors connexion. Si un conteneur de volumes que vous souhaitez voir n’est pas présent, annulez l’Assistant et vérifiez s’il est hors connexion.

7. Dans la page suivante, sous **Choisir un appareil cible pour les volumes** dans les conteneurs de volumes, sélectionnez l’appareil virtuel dans la liste déroulante des appareils disponibles. Seuls les appareils possédant la capacité disponible sont affichés dans la liste.

8. Passez en revue tous les paramètres de basculement dans la page **Confirmer le basculement**. S’ils sont corrects, cliquez sur l’icône en forme de coche.

Le processus de basculement commence. Lorsque le basculement est terminé, accédez à la page Appareils et sélectionnez l’appareil virtuel qui a été utilisé comme cible pour le processus de basculement. Accédez à la page Conteneurs de volumes. Tous les conteneurs de volumes, ainsi que les volumes de l’ancien appareil, doivent être répertoriés.

>[AZURE.NOTE] La quantité de stockage prise en charge sur l’appareil virtuel est de 30 To.

## Arrêt ou suppression de l’appareil virtuel

Si vous avez précédemment configuré et utilisé un appareil virtuel StorSimple, mais que vous ne voulez plus cumuler de frais de calcul pour son utilisation, vous pouvez l’arrêter. L’arrêt de l’appareil virtuel ne supprime pas son système d’exploitation ni les disques de données du stockage. Cette opération arrête le cumul de frais dans votre abonnement, mais les frais de stockage pour les disques de système d’exploitation et de données continuent à être facturés.

Si vous supprimez ou arrêtez l’appareil virtuel, il apparaît comme **Hors connexion** dans la page Appareils du service StorSimple Manager. Vous pouvez choisir de le désactiver ou de le supprimer comme appareil si vous souhaitez également supprimer les sauvegardes créées par l’appareil virtuel. Pour plus d’informations, consultez [Désactiver et supprimer un appareil StorSimple](storsimple-deactivate-and-delete-device.md).

### Arrêt de l’appareil virtuel StorSimple

1. Connectez-vous à la version classique du portail Azure.

2. Cliquez sur **Virtual Machines**, puis sélectionnez l’appareil virtuel.

3. Cliquez sur **Arrêter**.

### Suppression de l’appareil virtuel StorSimple

1. Connectez-vous à la version classique du portail Azure.

2. Cliquez sur **Virtual Machines**, puis sélectionnez l’appareil virtuel.

3. Cliquez sur **Supprimer**, puis choisissez de supprimer tous les disques de la machine virtuelle.

## Étapes suivantes

Découvrez comment [restaurer un volume StorSimple à partir d’un jeu de sauvegarde](storsimple-restore-from-backup-set.md).

<!---HONumber=AcomDC_0504_2016-->