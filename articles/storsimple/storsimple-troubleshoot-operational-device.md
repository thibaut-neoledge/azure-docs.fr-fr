---
title: Résolution des problèmes d'un appareil StorSimple déployé | Microsoft Docs
description: Décrit comment diagnostiquer et corriger les erreurs qui se produisent sur un appareil StorSimple actuellement déployé et opérationnel.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/16/2016
ms.author: v-sharos

---
# Résolution des problèmes d'un appareil StorSimple opérationnel
## Vue d'ensemble
Cet article fournit des conseils de dépannage pour résoudre les problèmes de configuration que vous pouvez rencontrer une fois votre appareil StorSimple déployé et opérationnel. Il décrit les problèmes courants, les causes possibles et les étapes recommandées pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous démarrez Microsoft Azure StorSimple. Ces informations s’appliquent à l’appareil physique local StorSimple et à l’appareil virtuel StorSimple.

À la fin de cet article, vous trouverez une liste des codes d'erreur que vous pouvez rencontrer lors de l’utilisation de Microsoft Azure StorSimple, ainsi que les actions à effectuer pour résoudre les erreurs.

## Processus de l'Assistant Installation pour les appareils opérationnels
Vous utilisez l'Assistant Installation ([Invoke-HcsSetupWizard][1]) pour vérifier la configuration de l’appareil et prendre des mesures correctives si nécessaire.

Lorsque vous exécutez l'Assistant Installation sur un appareil précédemment configuré et opérationnel, le déroulement du processus est différent. Vous pouvez modifier uniquement les entrées suivantes :

* Adresse IP, Masque de sous-réseau et Passerelle
* Serveur DNS principal
* Serveur NTP principal
* Configuration du proxy web (facultatif)

L'Assistant Installation n'effectue pas les opérations liées à la collecte de mot de passe et l’inscription de l’appareil.

## Erreurs survenant lors des exécutions suivantes de l’Assistant Installation
Le tableau suivant décrit les erreurs que vous pouvez rencontrer lorsque vous exécutez l'Assistant Installation sur un appareil opérationnel, leurs causes probables et les mesures recommandées pour les résoudre.

| Non. | Message d'erreur ou condition | Causes possibles | Action recommandée |
|:--- |:--- |:--- |:--- |
| 1 |Erreur 350032 : cet appareil a déjà été désactivé. |Cette erreur survient si vous exécutez l’Assistant Installation sur un appareil désactivé. |Pour les étapes suivantes, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md). Un appareil désactivé ne peut pas être mis en service. Une réinitialisation des paramètres peut être nécessaire avant la réactivation de l’appareil. |
| 2 |Invoke-HcsSetupWizard : ERROR\_INVALID\_FUNCTION(exception de HRESULT: 0x80070001) |La mise à jour du serveur DNS a échoué. Les paramètres DNS sont des paramètres globaux et sont appliqués à toutes les interfaces réseau activées. |Activez l'interface et appliquez de nouveau les paramètres DNS. Cela peut perturber le réseau pour les autres interfaces activées, car ces paramètres sont globaux. |
| 3 |L’appareil semble être en ligne dans le portail du service StorSimple Manager, mais lorsque vous essayez d'exécuter le programme d'installation minimale et d’enregistrer la configuration, l'opération échoue. |Pendant l'installation initiale, le proxy web n'a pas été configuré, même si un serveur proxy était en place. |Utilisez l’applet de commande [Test-HcsmConnection][2] pour localiser l’erreur. Si vous ne parvenez pas à résoudre le problème, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md). |
| 4 |Invoke-HcsSetupWizard : la valeur ne figure pas dans la plage attendue. |Cette erreur est générée par un masque de sous-réseau incorrect. Les causes possibles sont : <ul><li> Le masque de sous-réseau est manquant ou vide.</li><li>Le format de préfixe Ipv6 est incorrect.</li><li>L'interface est activée pour le cloud, mais la passerelle est manquante ou incorrecte.</li></ul>Notez que l’interface réseau DATA 0 est automatiquement activée pour le cloud si elle est configurée via l'Assistant Installation. |Pour déterminer le problème, utilisez le sous-réseau 0.0.0.0 ou 256.256.256.256, puis examinez la sortie. Entrez les valeurs correctes pour le masque de sous-réseau, la passerelle et le préfixe Ipv6, le cas échéant. |

## Codes d’erreur
Les erreurs sont répertoriées dans l'ordre numérique.

| Numéro d'erreur | Description ou texte d’erreur | Action de l'utilisateur recommandée |
|:--- |:--- |:--- |
| 10502 |Une erreur s'est produite lors de l'accès à votre compte de stockage. |Patientez quelques minutes et réessayez. Si l’erreur persiste, contactez le support technique Microsoft. |
| 40017 |Impossible de résoudre un disque dans un jeu de sauvegarde. |Si l’erreur persiste, contactez le support technique Microsoft. |
| 40018 |Impossible de résoudre le moindre disque dans un jeu de sauvegarde. |Si l’erreur persiste, contactez le support technique Microsoft. |
| 390061 |Le système est occupé ou non disponible. |Patientez quelques minutes et réessayez. Si l’erreur persiste, contactez le support technique Microsoft. |
| 390143 |Une erreur s'est produite avec le code d'erreur 390143. (Erreur inconnue.) |Si l’erreur persiste, contactez le support technique Microsoft. |

## Étapes suivantes
Si vous ne pouvez pas résoudre le problème, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir de l’aide.

[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx

<!---HONumber=AcomDC_0518_2016-->