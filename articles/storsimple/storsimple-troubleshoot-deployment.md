<properties 
   pageTitle="Résolution des problèmes de déploiement de StorSimple | Microsoft Azure"
   description="Décrit comment diagnostiquer et corriger les erreurs qui se produisent lorsque vous déployez StorSimple pour la première fois."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/29/2015"
   ms.author="alkohli" />

# Résolution des problèmes de déploiement d’un appareil StorSimple

## Vue d'ensemble

Cet article fournit des conseils de dépannage utiles pour votre déploiement de Microsoft Azure StorSimple. Il décrit les problèmes courants, les causes possibles et les étapes recommandées pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous configurez StorSimple. Ces informations s’appliquent à l’appareil physique local StorSimple et à l’appareil virtuel StorSimple.

> [AZURE.NOTE]Les problèmes liés à la configuration de l’appareil que vous êtes susceptible de rencontrer peuvent se produire lorsque vous déployez l’appareil pour la première fois ou ultérieurement, lorsque l’appareil est opérationnel. Cet article se concentre sur la résolution des problèmes qui surviennent lors du premier déploiement. Pour résoudre les problèmes d’un appareil opérationnel, consultez la page [Résolution des problèmes d’un appareil opérationnel](storsimple-troubleshoot-operational-device.md).

Cet article décrit également les outils de résolution des problèmes de déploiements StorSimple et fournit un exemple pas à pas de dépannage.

## Problèmes lors du premier déploiement

Si vous rencontrez un problème lors du premier déploiement de votre appareil, procédez comme suit :

- Si vous dépannez un appareil physique, assurez-vous que le matériel a été installé et configuré comme décrit à la page [Installation de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md) ou [Installation de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).
- Vérifiez les conditions préalables pour le déploiement. Assurez-vous de disposer de toutes les informations décrites dans la [liste de vérification de la configuration de déploiement](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
- Passez en revue les notes de publication de StorSimple pour voir si le problème est décrit. Les notes de publication incluent des solutions de contournement concernant les problèmes d’installation connus. 

Lors du déploiement d’un appareil, les problèmes les plus courants auxquels les utilisateurs sont confrontés se produisent lorsqu’ils exécutent l’Assistant Installation et lorsqu’ils inscrivent l’appareil via Windows PowerShell pour StorSimple. (Vous utilisez Windows PowerShell pour StorSimple pour inscrire et configurer votre appareil StorSimple. Pour plus d'informations sur l’inscription des appareils, consultez l’[Étape 3 : configuration et inscription de votre appareil via Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).)

Les sections suivantes peuvent vous aider à résoudre les problèmes que vous rencontrez lorsque vous configurez l’appareil StorSimple pour la première fois.

## Processus de l’Assistant Première installation

Les étapes suivantes résument le processus de l’Assistant Installation. Pour obtenir des informations de configuration détaillées, consultez [Déploiement de votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

1. Exécutez l’applet de commande [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) pour démarrer l’Assistant Installation qui vous guidera tout au long des étapes restantes. 
2. Configurez le réseau : l’Assistant Installation vous permet de configurer les paramètres réseau de l’interface réseau DATA 0 sur votre appareil StorSimple. Les paramètres suivants sont inclus :
  - Adresse IP virtuelle, masque de sous-réseau et passerelle : l’applet de commande [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) est exécutée en arrière-plan. Elle configure l’adresse IP, le masque de sous-réseau et la passerelle pour l’interface réseau DATA 0 sur votre appareil StorSimple.
  - Serveur DNS principal : l’applet de commande [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) est exécutée en arrière-plan. Elle configure les paramètres DNS pour votre solution StorSimple.
  - Serveur NTP : l’applet de commande [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) est exécutée en arrière-plan. Elle configure les paramètres NTP pour votre solution StorSimple.
  - Proxy web facultatif : l’applet de commande [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) est exécutée en arrière-plan. Elle définit et permet la configuration du proxy web pour votre solution StorSimple.
3. Configurez les mots de passe : l’étape suivante consiste à définir les mots de passe de l’administrateur de l’appareil et du Gestionnaire d’instantanés StorSimple. Si vous utilisez Update 1, vous n’êtes pas tenu de configurer le mot de passe du gestionnaire d’instantanés StorSimple.
  - Le mot de passe Administrateur est utilisé pour ouvrir une session sur votre appareil. Le mot de passe par défaut de l’appareil est **Password1**.
  - Lorsque vous configurez un appareil pour utiliser le Gestionnaire d’instantanés StorSimple, vous devez fournir le mot de passe du gestionnaire. Vous devez tout d’abord définir le mot de passe dans l’Assistant Installation, puis vous pouvez le définir et le modifier à partir du service StorSimple Manager. Ce mot de passe authentifie l’appareil auprès du gestionnaire d’instantanés StorSimple.
 
    > [AZURE.IMPORTANT]Les mots de passe sont collectés avant l’inscription, mais appliqués seulement après que vous avez inscrit correctement l’appareil. En cas de non-application d’un mot de passe, vous devez fournir le mot de passe à nouveau jusqu’à ce que les mots de passe requis (qui répondent aux exigences de complexité) soient collectés.

4. Inscrivez l’appareil : la dernière étape consiste à inscrire l’appareil auprès du service StorSimple Manager en cours d’exécution dans Microsoft Azure. L’inscription vous oblige à [obtenir la clé d’inscription](storsimple-manage-service.md#get-the-service-registration-key) à partir du portail de gestion Azure et à le fournir dans l’Assistant Installation. Une fois que l’appareil est inscrit correctement, une clé de chiffrement des données du service vous est fournie. Veillez à conserver cette clé de chiffrement dans un emplacement sûr, car elle sera nécessaire pour inscrire tous les autres appareils auprès du service.

## Erreurs courantes lors du déploiement de l’appareil

Les tableaux suivants répertorient les erreurs courantes que vous pouvez rencontrer lorsque vous :

- configurez les paramètres réseau requis ;
- configurez les paramètres de proxy web facultatifs ;
- configurez les mots de passe de l’administrateur de l’appareil et du Gestionnaire d’instantanés StorSimple ; 
- inscrivez l’appareil. 

## Erreurs pendant la configuration des paramètres réseau requis

| Non.| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard : cette commande ne peut être exécutée que sur le contrôleur actif. | La configuration a été effectuée sur le contrôleur passif.| Exécutez cette commande depuis le contrôleur actif. Pour plus d’informations, consultez la page [Identification d’un contrôleur actif sur votre appareil](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).|
| 2 | Invoke-HcsSetupWizard : l’appareil n’est pas prêt. | Il existe des problèmes liés à la connectivité réseau sur DATA 0.| Vérifiez la connectivité réseau physique sur DATA 0.|
| 3 | Invoke-HcsSetupWizard : il existe un conflit d’adresses IP avec un autre système sur le réseau (exception de HRESULT : 0x80070263). | L’adresse IP fournie pour DATA 0 était déjà utilisée par un autre système. | Fournissez une nouvelle adresse IP qui n’est pas en cours d’utilisation.|
| 4 | Invoke-HcsSetupWizard : échec de la ressource de cluster (exception de HRESULT : 0x800713AE). | Adresse IP virtuelle en double. L’adresse IP fournie est déjà en cours d’utilisation.| Fournissez une nouvelle adresse IP qui n’est pas en cours d’utilisation.|
| 5\. | Invoke-HcsSetupWizard : adresse IPv4 non valide. | L’adresse IP est fournie dans un format incorrect.| Vérifiez le format et fournissez à nouveau votre adresse IP. Pour plus d’informations, consultez la page [Adressage IPv4][1]. |
| 6\. | Invoke-HcsSetupWizard : adresse IPv6 non valide. | L’adresse IP est fournie dans un format incorrect.| Vérifiez le format et fournissez à nouveau votre adresse IP. Pour plus d’informations, consultez la page [Adressage IPv6][2].|
| 7 | Invoke-HcsSetupWizard : il n’y a plus de points de terminaison disponibles auprès du mappeur de point de terminaison. (Exception de HRESULT : 0x800706D9) | La fonctionnalité de cluster ne fonctionne pas. | Pour les étapes suivantes, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).

## Erreurs pendant la configuration des paramètres de proxy web facultatifs

| Non.| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1 | Invoke-HcsSetupWizard : paramètre non valide (exception de HRESULT: 0 x 80070057). | L’un des paramètres fournis pour les paramètres de proxy n’est pas valide.| L’URI n’est pas fourni dans le format correct. Utilisez le format suivant : http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | Invoke-HcsSetupWizard : serveur RPC non disponible (exception de HRESULT : 0x800706ba). | La cause première est l’une des suivantes :<ol><li>Le cluster n’est pas disponible.</li><li>Le contrôleur passif ne peut pas communiquer avec le contrôleur actif, et la commande est exécutée à partir du contrôleur passif.</li></ol> | Suivant la cause première :<ol><li>[Contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous assurer que le cluster est disponible.</li><li>Exécutez la commande à partir du contrôleur actif. Si vous souhaitez exécuter la commande à partir du contrôleur passif, vous devez vous assurer que le contrôleur passif peut communiquer avec le contrôleur actif. Vous devez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) si cette connectivité est interrompue.</li></ol> |
| 3 | Invoke-HcsSetupWizard : l’appel RPC a échoué (exception de HRESULT : 0x800706be). | Le cluster est arrêté. | [Contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous assurer que le cluster est disponible.|
| 4 | Invoke-HcsSetupWizard : ressource de cluster introuvable (exception de HRESULT : 0x8007138f). | Impossible de trouver la ressource de cluster. Cela peut se produire lorsque l’installation ne s’est pas déroulée correctement. | Vous devrez peut-être rétablir les paramètres par défaut d’origine de l’appareil. [Contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour créer une ressource de cluster.|
| 5\. | Invoke-HcsSetupWizard : ressource de cluster pas en ligne (exception de HRESULT : 0x8007138c).| Les ressources de cluster ne sont pas en ligne. | Pour les étapes suivantes, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).|

## Erreurs liées aux mots de passe d’administrateur de l’appareil et du Gestionnaire d’instantanés StorSimple

Le mot de passe par défaut de l’appareil est **Password1**. Ce mot de passe expire après la première session ; par conséquent, vous devez utiliser l’Assistant Installation pour le modifier. Vous devez fournir un nouveau mot de passe Administrateur de l’appareil lorsque vous inscrivez l’appareil pour la première fois.

Si vous utilisez le logiciel Gestionnaire d’instantanés StorSimple en cours d’exécution sur l’ordinateur hôte Windows Server pour gérer l’appareil, vous devez également fournir un mot de passe du Gestionnaire d’instantanés StorSimple pendant l’inscription initiale.

Assurez-vous que vos mots de passe répondent aux exigences suivantes :

- Le mot de passe Administrateur de votre appareil doit comprendre entre 8 et 15 caractères.
- Le mot de passe du Gestionnaire d’instantanés StorSimple doit comporter 14 ou 15 caractères.
- Les mots de passe doivent contenir 3 des 4 types de caractères suivants : minuscules, majuscules, numériques et spéciaux. 
- Votre mot de passe ne peut pas être identique aux 24 derniers mots de passe.

En outre, n’oubliez pas que les mots de passe expirent chaque année et qu’ils peuvent être modifiés uniquement après l’inscription correcte de l’appareil. Si l’inscription échoue pour une raison quelconque, les mots de passe ne sont pas modifiés. Pour plus d’informations relatives aux mots de passe du Gestionnaire d'instantanés StorSimple et d’administrateur de l’appareil, consultez [Utilisation du service StorSimple Manager pour modifier vos mots de passe StorSimple](storsimple-change-passwords.md).

Vous pouvez rencontrer une ou plusieurs des erreurs suivantes lorsque vous configurez les mots de passe de l’administrateur de l’appareil et du Gestionnaire d’instantanés StorSimple.

| Non.| Message d’erreur | Action recommandée |
| ---| ------------- | ------------------ | 
| 1 | Le mot de passe dépasse la longueur maximale. |Utilisez un mot de passe qui répond à ces exigences :<ul><li>Le mot de passe Administrateur de votre appareil doit comporter entre 8 et 15 caractères.</li><li>Le mot de passe du Gestionnaire d’instantanés StorSimple doit comporter 14 ou 15 caractères.</li></ul> | 
| 2 | Le mot de passe ne respecte pas la longueur requise. | Utilisez un mot de passe qui répond à ces exigences :<ul><li>Le mot de passe Administrateur de votre appareil doit comporter entre 8 et 15 caractères.</li><li>Le mot de passe du Gestionnaire d’instantanés StorSimple doit comporter 14 ou 15 caractères.</lu></ul> |
| 3 | Le mot de passe doit contenir des caractères en minuscules. | Les mots de passe doivent contenir 3 des 4 types de caractères suivants : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 4 | Le mot de passe doit contenir des caractères numériques. | Les mots de passe doivent contenir 3 des 4 types de caractères suivants : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 5\. | Le mot de passe doit contenir des caractères spéciaux. | Les mots de passe doivent contenir 3 des 4 types de caractères suivants : minuscules, majuscules, numériques et spéciaux. Assurez-vous que votre mot de passe répond à ces exigences. |
| 6\. | Les mots de passe doivent contenir 3 des 4 types de caractères suivants : minuscules, majuscules, numériques et spéciaux. | Votre mot de passe ne contient pas les types de caractères requis. Assurez-vous que votre mot de passe répond à ces exigences. |
| 7\. | Le paramètre ne correspond pas à la confirmation. | Assurez-vous que votre mot de passe répond à toutes les exigences et que vous l’avez entré correctement. |
| 8\. | Votre mot de passe ne peut pas correspondre au mot de passe par défaut. | Le mot de passe par défaut est *Password1*. Vous devez modifier ce mot de passe après votre première connexion. |
| 9\. | Le mot de passe que vous avez entré ne correspond pas au mot de passe de l’appareil. Retapez le mot de passe. | Vérifiez le mot de passe et retapez-le. |

Les mots de passe sont collectés avant l’inscription de l’appareil, mais appliqués seulement une fois que l’inscription a réussi. Le flux de travail de récupération de mot de passe nécessite que l’appareil soit inscrit.

> [AZURE.IMPORTANT]En général, si une tentative d’application d’un mot de passe échoue, le logiciel essaie à plusieurs reprises de collecter le mot de passe jusqu’à ce que son application réussisse. Dans de rares circonstances, le mot de passe ne peut pas être appliqué. Dans ce cas, vous pouvez inscrire l’appareil et continuer ; cependant, les mots de passe ne sont pas modifiés. Vous ne recevez aucune indication spécifiant quel mot de passe n’a pas été modifié : le mot de passe Administrateur ou le mot de passe du Gestionnaire d’instantanés StorSimple. Si cette situation se produit, nous vous recommandons de modifier les deux mots de passe.

Vous pouvez réinitialiser les mots de passe à partir du portail de gestion de votre service StorSimple Manager. Pour plus d’informations, consultez :

- [Modification du mot de passe d’administrateur de l’appareil](storsimple-change-passwords.md#change-the-device-administrator-password).
- [Modification du mot de passe du Gestionnaire d’instantanés StorSimple](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## Erreurs pendant l'inscription d’un appareil

Vous utilisez le service StorSimple Manager en cours d’exécution dans Microsoft Azure pour inscrire l’appareil. Vous pouvez rencontrer un ou plusieurs des problèmes suivants lors de l’inscription de l’appareil.

| Non.| Message d’erreur | Causes possibles | Action recommandée |
| ---| ------------- | --------------- | ------------------ |
| 1 | Erreur 350027 : Impossible d’inscrire l’appareil auprès de StorSimple Manager. | | Patientez quelques minutes et recommencez l’opération. Si le problème persiste, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).|
| 2 | Erreur 350013 : Une erreur s’est produite lors de l’inscription de l’appareil. Cela peut résulter d’une clé d’inscription du service incorrecte. | | Inscrivez à nouveau l’appareil avec la clé d’inscription de service appropriée. Pour plus d’informations, consultez la section [
Obtenir la clé d’inscription de service.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | Erreur 350063 : l’authentification auprès du service StorSimple Manager a réussi, mais l’inscription a échoué. Veuillez réessayer l’opération après un certain temps. | Cette erreur indique que l’authentification avec ACS a réussi, mais que l’appel pour l’inscription au service a échoué. Cela peut résulter d’un problème réseau sporadique. | Si le problème persiste, contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md). |
| 4 | Erreur 350049 : Le service n’a pas pu être atteint lors de l’inscription. | Lorsque l’appel au service est effectué, une exception web est reçue. Dans certains cas, ce problème peut être résolu avec une nouvelle tentative ultérieure de l’opération. | Vérifiez votre adresse IP et le nom DNS, puis réessayez l’opération. Si le problème persiste, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md). | 
| 5\. | Erreur 350031 : L’appareil a déjà été inscrit. | | Aucune action requise. |
| 6\. | Erreur 350016 : Échec de l’inscription de l’appareil. | |Vérifiez que la clé d’inscription est correcte. |
| 7\. | Invoke-HcsSetupWizard : Une erreur s’est produite lors de l’inscription de votre appareil ; cela peut être dû à une adresse IP ou un nom DNS incorrect. Vérifiez vos paramètres réseau et réessayez. Si le problème persiste, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md). (Erreur 350050) | Assurez-vous que votre appareil peut tester le réseau externe (avec une commande ping). Si vous n’avez pas de connectivité vers le réseau externe, l’inscription peut échouer avec cette erreur. Cette erreur peut être une combinaison d’un ou plusieurs des éléments suivants :<ul><li>Adresse IP incorrecte</li><li>Sous-réseau incorrect</li><li>Passerelle incorrecte</li><li>Paramètres DNS incorrects</li></ul> | Pour les étapes, consultez la page [Exemple de résolution de problème pas à pas](#step-by-step-storsimple-troubleshooting-example). |
| 8 | Invoke-HcsSetupWizard : échec de l’opération en cours en raison d’une erreur de service interne [0x1FBE2]. Veuillez réessayer l’opération après un certain temps. Si le problème persiste, contactez le support technique Microsoft. | Il s’agit d’une erreur générique levée pour toutes les erreurs du service ou de l’agent invisibles pour l’utilisateur. La raison la plus courante est un échec de l’authentification ACS. Une cause possible de l’échec est l’existence de problèmes avec la configuration du serveur NTP et un paramétrage incorrect de l’heure sur l’appareil. | Corrigez l’heure (s’il existe des problèmes), puis réessayez l’opération d’inscription. Si le problème persiste, contactez le [support technique Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes. |
| 9\. | Avertissement : impossible d’activer l’appareil. Les mots de passe de l’administrateur de l’appareil et du Gestionnaire d’instantanés StorSimple n’ont pas été modifiés. | Si l’inscription échoue, les mots de passe de l’administrateur de l’appareil et du Gestionnaire d’instantanés StorSimple ne sont pas modifiés. |

## Outils de résolution des problèmes de déploiement de StorSimple

StorSimple comprend plusieurs outils que vous pouvez utiliser pour résoudre les problèmes de votre solution StorSimple. Ces outils sont les suivants :

- Packages de prise en charge et journaux d’appareil 
- Applets de commande conçues spécialement pour la résolution des problèmes 

## Packages de prise en charge et journaux d’appareil disponibles pour la résolution des problèmes

Un package de prise en charge contient tous les journaux pertinents qui peuvent aider l’équipe de support technique de Microsoft à résoudre les problèmes des appareils. Vous pouvez utiliser Windows PowerShell pour StorSimple pour générer un package de prise en charge chiffré que vous pouvez ensuite partager avec le personnel du support technique.

### Pour afficher les journaux ou le contenu du package de prise en charge

1. Utilisez Windows PowerShell pour StorSimple pour générer un package de support, comme décrit à la page [Création et gestion d'un package de support](storsimple-create-manage-support-package.md).

2. Téléchargez le [script de déchiffrement](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localement sur votre ordinateur client.

3. Utilisez cette [procédure pas à pas](storsimple-create-manage-support-package.md#edit-a-support-package) pour ouvrir et déchiffrer le package de prise en charge.

4. Les journaux du package de prise en charge déchiffré sont au format etw/etvx. Vous pouvez afficher ces fichiers dans l’Observateur d’événements Windows en procédant comme suit :
  1. Exécutez la commande **eventvwr** sur votre client Windows. L’Observateur d’événements démarre.
  2. Dans le volet **Actions**, cliquez sur **Ouvrir le journal enregistré** et pointez sur les fichiers journaux au format etvx/etw (le package de prise en charge). Vous pouvez maintenant consulter le fichier. Après avoir ouvert le fichier, vous pouvez cliquer avec le bouton droit et enregistrer le fichier au format texte.
   
    > [AZURE.IMPORTANT]Vous pouvez également utiliser l’applet de commande **Get-WinEvent** pour ouvrir ces fichiers dans Windows PowerShell. Pour plus d’informations, consultez la page [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) dans la documentation de référence des applets de commande Windows PowerShell.

5. Une fois les journaux ouverts dans l’Observateur d’événements, recherchez les journaux ci-dessous ; ceux-ci contiennent les problèmes liés à la configuration de l’appareil :

  - hcs\_pfconfig/Operational Log
  - hcs\_pfconfig/Config

6. Dans les fichiers journaux, recherchez les chaînes relatives aux applets de commande appelées par l’Assistant Installation. Pour obtenir la liste de ces applets de commande, consultez la section [Processus de l’Assistant Première installation](#first-time-setup-wizard-process). 

7. Si vous n’êtes pas en mesure de déterminer la cause du problème, vous pouvez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour connaître les étapes suivantes. Suivez la procédure décrite à la page [Création d’une demande de support](storsimple-contact-microsoft-support.md#create-a-support-request) pour demander de l’aide au support technique Microsoft.

## Applets de commande disponibles pour la résolution des problèmes

Utilisez les applets de commande Windows PowerShell suivantes pour détecter les erreurs de connectivité.

- `Get-NetAdapter` : utilisez cette applet de commande pour détecter l’intégrité des interfaces réseau. 

- `Test-Connection` : utilisez cette applet de commande pour vérifier la connectivité réseau à l’intérieur et à l’extérieur du réseau.

- `Test-HcsmConnection` : utilisez cette applet de commande pour vérifier la connectivité d’un appareil correctement inscrit.

Si vous utilisez Update 1 sur votre appareil StorSimple, les applets de commande de diagnostic suivantes sont également disponibles.

- `Sync-HcsTime` : utilisez cette applet de commande pour afficher l’heure de l’appareil et forcer une synchronisation avec le serveur NTP.

- `Enable-HcsPing` et `Disable-HcsPing` : utilisez ces applets de commande pour autoriser les hôtes à effectuer un test ping sur les interfaces réseau de votre appareil StorSimple. Par défaut, les interfaces réseau de StorSimple ne répondent pas aux requêtes ping.

- `Trace-HcsRoute` : utilisez cette applet de commande comme un outil de suivi d’itinéraire. Elle envoie des paquets à chaque routeur jusqu’à une destination finale pendant une période donnée, puis calcule les résultats en fonction des paquets renvoyés par chaque tronçon. Comme l’applet de commande `Trace-HcsRoute` indique le degré de perte de paquets au niveau d’un routeur ou d’un lien donné, vous pouvez identifier les routeurs ou liens susceptibles de provoquer des problèmes de réseau.

- `Get-HcsRoutingTable` : utilisez cette applet de commande pour afficher la table de routage IP locale.

## Résolution des problèmes avec l’applet de commande Get-NetAdapter

Au moment de configurer des interfaces réseau pour un premier déploiement d’appareil, l’état du matériel n’est pas disponible dans l’interface utilisateur du service StorSimple Manager, car l’appareil n’est pas encore inscrit auprès du service. De plus, la page Statut du matériel ne reflète pas toujours correctement l’état de l’appareil, surtout si des problèmes empêchent la synchronisation du service. En pareil cas, vous pouvez utiliser l’applet de commande `Get-NetAdapter` pour déterminer l’intégrité et l’état des interfaces réseau.

### Pour afficher la liste de toutes les cartes réseau de votre appareil

1. Démarrez Windows PowerShell pour StorSimple, puis tapez `Get-NetAdapter`. 

2. Utilisez la sortie de l’applet de commande `Get-NetAdapter` et les instructions suivantes pour comprendre l’état de votre interface réseau.
  - Si l’interface est intègre et activée, l’état d’**ifIndex** indique **Activé**.
  - Si l’interface est intègre, mais qu’elle n’est pas physiquement connectée (par un câble réseau), l’état d’**ifIndex** indique **Désactivé**.
  - Si l’interface est intègre mais pas activée, l’état d’**ifIndex** indique **Absent**.
  - Si l’interface n’existe pas, il n’apparaît pas dans cette liste. L’interface utilisateur du service StorSimple Manager continue d’afficher cette interface avec l’état Échec.

Pour plus d’informations sur l’utilisation de cette applet de commande, accédez à la page [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) dans le document de référence des applets de commande Windows PowerShell.  


Les sections suivantes présentent des exemples de sortie de l’applet de commande `Get-NetAdapter`.

 Dans ces exemples, le contrôleur 0 était le contrôleur passif et a été configuré comme suit :

- Les interfaces réseau DATA 0, DATA 1, DATA 2 et DATA 3 existaient sur l’appareil.
- Les cartes d’interface réseau DATA 4 et DATA 5 étaient absentes ; elles ne figurent donc pas dans la sortie.
- DATA 0 était activée.

Le contrôleur 1 était le contrôleur actif et a été configuré comme suit :

- Les interfaces réseau DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 et DATA 5 existaient sur l’appareil.
- DATA 0 était activée.

**Exemple de sortie – contrôleur 0**

Voici la sortie du contrôleur 0 (le contrôleur passif). DATA 1, DATA 2 et DATA 3 ne sont pas connectées. DATA 4 et DATA 5 ne sont pas répertoriées, car elles sont absentes de l’appareil.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Exemple de sortie – contrôleur 1**

Voici la sortie du contrôleur 1 (le contrôleur actif). Seule l’interface réseau DATA 0 est configurée et fonctionne sur l’appareil.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## Résolution des problèmes avec l’applet de commande Test-Connection  


Vous pouvez utiliser l’applet de commande `Test-Connection` pour déterminer si votre appareil StorSimple peut se connecter au réseau externe. Si tous les paramètres réseau, y compris le DNS, sont correctement configurés dans l’Assistant Installation, vous pouvez utiliser l’applet de commande `Test-Connection` pour effectuer un test ping sur une adresse connue à l’extérieur du réseau, comme outlook.com.

Vous devez activer la commande ping pour résoudre les problèmes de connectivité avec cette applet de commande si la commande ping est désactivée.

Consultez ci-dessous les exemples de sortie de l’applet de commande `Test-Connection`.

> [AZURE.NOTE]Dans le premier exemple, l’appareil est configuré avec un paramètre DNS incorrect. Dans le deuxième exemple, le paramètre DNS est correct.
 
**Exemple de sortie – DNS incorrect**

Dans l’exemple suivant, il n’y a pas de sortie pour les adresses IPV4 et IPV6, ce qui indique que le DNS n’est pas résolu. Cela signifie qu’il n’y a pas de connectivité vers le réseau externe et qu’un DNS correct doit être fourni.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Exemple de sortie : DNS correct**

Dans l’exemple suivant, le DNS renvoie l’adresse IPV4, ce qui indique que le DNS est correctement configuré. Cela confirme qu’il existe une connectivité vers le réseau externe.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## Résolution des problèmes avec l’applet de commande Test-HcsmConnection

Utilisez l’applet de commande `Test-HcsmConnection` pour un appareil qui est déjà connecté et inscrit auprès du service StorSimple Manager. Cette applet de commande permet de vérifier la connectivité entre un appareil inscrit et le service StorSimple Manager correspondant. Vous pouvez exécuter cette commande sur Windows PowerShell pour StorSimple.

### Pour exécuter l’applet de commande Test-HcsmConnection

1. Assurez-vous que l’appareil est inscrit.

2. Vérifiez l’état de l’appareil. Si l’appareil est désactivé, en mode de maintenance ou hors connexion, les erreurs suivantes peuvent s’afficher :

   - ErrorCode.CiSDeviceDecommissioned : indique que l’appareil est désactivé.
   - ErrorCode.DeviceNotReady : indique que l’appareil est en mode maintenance.
   - ErrorCode.DeviceNotReady : indique que l’appareil n’est pas en ligne.

3. Vérifiez que le service StorSimple Manager est en cours d’exécution (utilisez l’applet de commande [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx)). Si le service n’est pas en cours d’exécution, les erreurs suivantes peuvent s’afficher :

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError : indique qu’une exception est survenue lors de l’exécution de Get-ClusterResource.

4. Vérifiez le jeton du service de contrôle d’accès (ACS) S’il lève une exception web, cela peut résulter d’un problème de passerelle, d’une authentification proxy manquante, d’un DNS incorrect ou d’un échec d’authentification. Les erreurs suivantes peuvent s’afficher :

   - ErrorCode.CiSApplianceGateway : indique une exception HttpStatusCode.BadGateway : le service de résolution de noms n’a pas pu résoudre le nom de l’hôte. 
   - ErrorCode.CiSApplianceProxy : indique une exception HttpStatusCode.ProxyAuthenticationRequired (code d’état HTTP 407) : le client n’a pas pu s’authentifier auprès du serveur proxy. 
   - ErrorCode.CiSApplianceDNSError : indique une exception WebExceptionStatus.NameResolutionFailure : le service de résolution de noms n’a pas pu résoudre le nom d’hôte.
   - ErrorCode.CiSApplianceACSError : indique que le service a renvoyé une erreur d’authentification, mais qu’il existe une connectivité.
   
    Si l’erreur ne lève pas d’exception web, recherchez ErrorCode.CiSApplianceFailure. Cette erreur indique un échec de l’appliance.

5. Vérifiez la connectivité du service cloud. Si le service lève une exception web, vous pouvez voir les erreurs suivantes :

  - ErrorCode.CiSApplianceGateway – indique une exception HttpStatusCode.BadGateway : un serveur proxy intermédiaire a reçu une demande incorrecte à partir d’un autre proxy ou du serveur d’origine.
  - ErrorCode.CiSApplianceProxy : indique une exception HttpStatusCode.ProxyAuthenticationRequired (code d’état HTTP 407) : le client n’a pas pu s’authentifier auprès du serveur proxy. 
  - ErrorCode.CiSApplianceDNSError : indique une exception WebExceptionStatus.NameResolutionFailure : le service de résolution de noms n’a pas pu résoudre le nom d’hôte.
  - ErrorCode.CiSApplianceACSError : indique que le service a renvoyé une erreur d’authentification, mais qu’il existe une connectivité.
  
    Si l’erreur ne lève pas d’exception web, recherchez ErrorCode.CiSApplianceSaasServiceError. Cette erreur indique un problème lié au service StorSimple Manager.
 
6. Vérifiez la connectivité d’Azure Service Bus. ErrorCode.CiSApplianceServiceBusError indique que l’appareil ne peut pas se connecter à Service Bus.
 
Les fichiers journaux CiSCommandletLog0Curr.errlog et CiSAgentsvc0Curr.errlog comporteront plus d’informations, notamment les détails de l’exception.

Pour plus d’informations sur l’utilisation de l’applet de commande, accédez à la page [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) dans la documentation de référence de Windows PowerShell.

> [AZURE.IMPORTANT]Vous pouvez exécuter cette applet de commande pour le contrôleur passif et actif.
 
Consultez ci-dessous les exemples de sortie de l’applet de commande `Test-HcsmConnection`.

**Exemple de sortie : appareil inscrit exécutant StorSimple Release (juillet 2014)**

Le premier exemple concerne un appareil qui est inscrit auprès du service StorSimple Manager et qui n’est confronté à aucun problème de connectivité.

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Exemple de sortie : appareil inscrit exécutant StorSimple Update 1**

Si vous utilisez Update 1 sur votre appareil StorSimple, vous n’avez pas besoin de l’utiliser avec le commutateur verbose.

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Exemple de sortie : appareil hors connexion exécutant StorSimple Release (juillet 2014)**

Cet exemple concerne un appareil qui a le statut **hors connexion** dans le portail de gestion.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

L’appareil n’a pas pu se connecter à l’aide de la configuration actuelle du proxy web. Il peut s’agir d’un problème avec la configuration du proxy web ou d’un problème de connectivité réseau. Dans ce cas, vous devez vous assurer que vos paramètres de proxy web sont corrects et que vos serveurs de proxy web sont en ligne et accessibles.

## Résoudre les problèmes avec l’applet de commande Sync-HcsTime

Utilisez cette applet de commande pour afficher l’heure de l’appareil. Si l’heure de l’appareil est décalée par rapport à celle du serveur NTP, vous pouvez utiliser cette applet de commande pour forcer la synchronisation avec le serveur NTP. Si le décalage entre l’appareil et le serveur NTP est supérieur à 5 minutes, un avertissement s’affiche. S’il excède 15 minutes, l’appareil est mis hors connexion. Vous pouvez toujours utiliser cette applet de commande pour forcer une synchronisation. Toutefois, si le décalage est supérieur à 15 heures, vous ne pouvez pas forcer la synchronisation, et un message d’erreur s’affiche.

**Exemple de sortie : synchronisation forcée à l’aide de l’applet de commande Sync-HcsTime**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## Résoudre les problèmes avec les applets de commande Enable-HcsPing et Disable-HcsPing

Utilisez ces applets de commande pour vous assurer que les interfaces réseau de votre appareil répondent aux requêtes ping ICMP. Par défaut, les interfaces réseau de StorSimple ne répondent pas aux requêtes ping. Utiliser cette applet de commande est le moyen le plus simple de savoir si votre appareil est en ligne et accessible.

**Exemple de sortie : Enable-HcsPing et Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## Résoudre les problèmes avec l’applet de commande Trace-HcsRoute

Utilisez cette applet de commande comme un outil de suivi d’itinéraire. Elle envoie des paquets à chaque routeur jusqu’à une destination finale pendant une période donnée, puis calcule les résultats en fonction des paquets renvoyés par chaque tronçon. Comme cette applet de commande indique le degré de perte de paquets au niveau d’un routeur ou d’un lien donné, vous pouvez identifier les routeurs ou liens susceptibles de provoquer des problèmes de réseau.

**Exemple de sortie indiquant comment suivre l’itinéraire d’un paquet avec Trace-HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## Résoudre les problèmes avec l’applet de commande Get-HcsRoutingTable

Utilisez cette applet de commande pour afficher la table de routage de votre appareil StorSimple. Une table de routage est un ensemble de règles qui peuvent aider à déterminer où les paquets de données circulant sur un réseau IP (Internet Protocol) sont dirigés.

La table de routage présente les interfaces et la passerelle qui achemine les données vers les réseaux spécifiés. Elle indique également la métrique de routage, qui décide du chemin d’accès emprunté pour atteindre une destination particulière. La métrique de routage est inversement proportionnelle à la préférence.

Exemple : vous avez 2 interfaces réseau, DATA 2 et DATA 3, connectées à Internet. Si les métriques de routage de DATA 2 et 3 sont respectivement 15 et 261, l’interface DATA 2 pourvue de la métrique de routage inférieure est l’interface préférée utilisée pour accéder à Internet.

Si vous utilisez Update 1 sur votre appareil StorSimple, votre interface réseau DATA 0 a la préférence la plus élevée pour le trafic de cloud. Cela signifie que même s’il existe d’autres interfaces compatibles avec le cloud, le trafic de cloud est acheminé via l’interface DATA 0.

Si vous exécutez l’applet de commande `Get-HcsRoutingTable` sans spécifier de paramètres (comme dans l’exemple suivant), l’applet de commande génère en sortie des tables de routage IPv4 et IPv6. Vous pouvez également spécifier `Get-HcsRoutingTable -IPv4` ou `Get-HcsRoutingTable -IPv6` pour obtenir une table de routage pertinente.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## Exemple de résolution pas à pas des problèmes de StorSimple

L’exemple suivant montre étape par étape comment résoudre les problèmes d’un déploiement StorSimple. Dans ce scénario, l’inscription de l’appareil échoue avec un message d’erreur indiquant que les paramètres réseau ou le nom DNS sont incorrects.

Le message d’erreur retourné est :

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

L’erreur peut provenir des éléments suivants :

- Installation matérielle incorrecte
- Interface(s) réseau défectueuse(s)
- Adresse IP, masque de sous-réseau, passerelle, serveur DNS principal ou proxy web incorrect
- Clé d’inscription incorrecte
- Paramètres de pare-feu incorrects

### Pour localiser et résoudre le problème d’inscription de l’appareil

1. Vérifiez la configuration de l’appareil : sur le contrôleur actif, exécutez `Invoke-HcsSetupWizard`.

     >[AZURE.NOTE]L’Assistant Installation doit s’exécuter sur le contrôleur actif. Pour vérifier que vous êtes connecté au contrôleur actif, examinez la bannière présentée dans la console série. La bannière indique si vous êtes connecté au contrôleur 0 ou 1, et si le contrôleur est actif ou passif. Pour plus d’informations, consultez la page [Identification d’un contrôleur actif sur votre appareil](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
 
2. Assurez-vous que l’appareil est câblé correctement : vérifiez le câblage sur le panneau arrière de l’appareil. Le câblage est spécifique au modèle d’appareil. Pour plus d’informations, consultez [Installation de votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md) ou [Installation de votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).

     >[AZURE.NOTE]Si vous utilisez des ports réseau 10 GbE, vous devez utiliser les adaptateurs QSFP-SFP et les câbles SFP fournis. Pour plus d’informations, consultez la [liste des câbles, commutateurs et transmetteurs recommandés par le fournisseur OEM pour les ports Mellanox](http://www.mellanox.com/page/cables?mtag=cable_overview).
 
3. Vérifiez l’intégrité de l’interface réseau :

   - Utilisez l’applet de commande Get-NetAdapter pour détecter l’intégrité des interfaces réseau pour DATA 0. 
   - Si le lien ne fonctionne pas, l’état **ifindex** indique que l’interface est arrêtée. Vous devez ensuite vérifier la connexion réseau du port au matériel et au commutateur. Vous devez également écarter les câbles défectueux. 
   - Si vous pensez que le port DATA 0 sur le contrôleur actif est défectueux, vous pouvez le vérifier en vous connectant au port DATA 0 sur le contrôleur 1. Pour vérifier cela, débranchez le câble réseau du contrôleur 0 au dos de l’appareil, raccordez le câble au contrôleur 1, puis réexécutez l’applet de commande Get-NetAdapter. Si le port DATA 0 sur un contrôleur échoue, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour les étapes suivantes. Vous devrez peut-être remplacer le contrôleur sur votre système.
 
4. Vérifiez la connectivité au commutateur :
   - Assurez-vous que les interfaces réseau DATA 0 sur le contrôleur 0 et le contrôleur 1 dans votre boîtier principal sont sur le même sous-réseau. 
   - Vérifiez le concentrateur ou le routeur. En règle générale, vous devez connecter les deux contrôleurs au même concentrateur ou routeur. 
   - Assurez-vous que les commutateurs que vous utilisez pour la connexion ont des interfaces réseau DATA 0 pour les deux contrôleurs dans le même réseau local virtuel.
   
5. Éliminez les erreurs utilisateur :

  - Réexécutez l’Assistant Installation (exécutez **Invoke-HcsSetupWizard**), puis entrez de nouveau les valeurs pour vous assurer qu’il n’y a pas d’erreur. 
  - Vérifiez la clé d’inscription utilisée. La même clé d’inscription peut servir à connecter plusieurs appareils à un service StorSimple Manager. Utilisez la procédure décrite dans la section [Obtenir la clé d’inscription](storsimple-manage-service.md#get-the-service-registration-key) pour vous assurer que vous utilisez la clé d’inscription correcte.

    > [AZURE.IMPORTANT]Si vous avez plusieurs services en cours d’exécution, vous devez vous assurer que la clé d’inscription du service approprié est utilisée pour inscrire l’appareil. Si vous avez inscrit un appareil avec le service StorSimple Manager incorrect, vous devez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour connaître les étapes suivantes. Vous devrez peut-être rétablir les paramètres d’usine de l’appareil (ce qui peut entraîner une perte de données) pour le connecter ensuite au service prévu.

6. Utilisez l’applet de commande Test-Connection pour vérifier que vous disposez d’une connectivité vers le réseau externe. Pour plus d’informations, consultez la section [Dépannage avec l’applet de commande Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).

7. Vérifiez les interférences avec le pare-feu. Si vous avez vérifié que les paramètres d’adresse IP virtuelle, de sous-réseau, de passerelle et DNS sont tous corrects et que vous rencontrez encore des problèmes de connectivité, il est possible que votre pare-feu bloque les communications entre votre appareil et le réseau externe. Vérifiez que les ports 80 et 443 sont disponibles pour la communication sortante sur l’appareil StorSimple. Pour plus d’informations, consultez la page [Configuration réseau requise pour votre appareil StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

8. Examinez les journaux. Consultez la page [Packages de support et journaux des appareils disponibles pour la résolution des problèmes](#support-packages-and-device-logs-available-for-troubleshooting).

9. Si les étapes précédentes ne permettent pas de résoudre le problème, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance.

## Étapes suivantes
[Apprenez à résoudre les problèmes d’un appareil opérationnel](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx

<!---HONumber=Oct15_HO4-->