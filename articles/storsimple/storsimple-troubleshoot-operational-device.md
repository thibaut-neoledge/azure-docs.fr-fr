<properties 
   pageTitle="Résolution des problèmes d'un appareil StorSimple opérationnel"
   description="Décrit comment diagnostiquer et corriger les erreurs qui se produisent sur un appareil StorSimple opérationnel."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/09/2015"
   ms.author="v-sharos" />

# Résolution des problèmes d'un appareil StorSimple opérationnel

## Vue d'ensemble

Cet article fournit des conseils de dépannage pour résoudre les problèmes de configuration que vous pouvez rencontrer une fois votre appareil StorSimple déployé et opérationnel. Il décrit les problèmes courants, les causes possibles et les étapes recommandées pour vous aider à résoudre les problèmes que vous pouvez rencontrer lorsque vous démarrez StorSimple. Ces informations s'appliquent à l’appareil physique local StorSimple et à l’appareil virtuel StorSimple.

## Processus de l'Assistant Installation pour les appareils opérationnels

Vous utilisez l'Assistant Installation (Invoke-HcsSetupWizard) pour vérifier la configuration de l’appareil et prendre des mesures correctives si nécessaire.

Lorsque vous exécutez l'Assistant Installation sur un appareil précédemment configuré et opérationnel, le déroulement du processus est différent. Vous pouvez modifier uniquement les entrées suivantes :

- Adresse IP, Masque de sous-réseau et Passerelle
- Serveur DNS principal
- Serveur NTP principal
- Configuration du proxy web (facultatif)

L'Assistant Installation n'effectue pas les opérations liées à la collecte de mot de passe et l’inscription de l’appareil.

## Erreurs survenant lors des exécutions suivantes de l’Assistant Installation

Le tableau suivant décrit les erreurs que vous pouvez rencontrer lorsque vous exécutez l'Assistant Installation sur un appareil opérationnel, leurs causes probables et les mesures recommandées pour les résoudre.

| Non. | Message d'erreur ou condition | Causes possibles | Action recommandée |
| --- | -------------------------- | --------------- | ------------------ |
| 1 | Erreur 350032 : cet appareil a déjà été désactivé. | Cette erreur survient si vous exécutez l’Assistant Installation sur un appareil désactivé. | Pour les étapes suivantes, [contactez le support technique Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx). Un appareil désactivé ne peut pas être mis en service. Une réinitialisation des paramètres peut être nécessaire avant la réactivation de l’appareil. |
| 2 | Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(exception de HRESULT: 0x80070001) | La mise à jour du serveur DNS a échoué. Les paramètres DNS sont des paramètres globaux et sont appliqués à toutes les interfaces réseau activées. | Activez l'interface et appliquez de nouveau les paramètres DNS. Cela peut perturber le réseau pour les autres interfaces activées, car ces paramètres sont globaux. |
| 3 | L’appareil semble être en ligne dans le portail du service StorSimple Manager, mais lorsque vous essayez d'exécuter le programme d'installation minimale et d’enregistrer la configuration, l'opération échoue. | Pendant l'installation initiale, le proxy web n'a pas été configuré, même si un serveur proxy était en place. | Utilisez l’applet de commande [Test-HcsmConnection](https://msdn.microsoft.com/library/azure/eedae62d-0957-4005-b346-9248724f90e0#sec05) pour localiser l’erreur. Si vous ne parvenez pas à résoudre le problème, [contactez le support technique Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx). |
| 4 | Invoke-HcsSetupWizard : la valeur ne figure pas dans la plage attendue. | Cette erreur est générée par un masque de sous-réseau incorrect. Les causes possibles sont : <ul><li> Le masque de sous-réseau est manquant ou vide.</li><li>Le format de préfixe Ipv6 est incorrect.</li><li>L'interface est activée pour le cloud, mais la passerelle est manquante ou incorrecte.</li></ul>Notez que l’interface réseau DATA 0 est automatiquement activée pour le cloud si elle est configurée via l'Assistant Installation. | Pour déterminer le problème, utilisez le sous-réseau 0.0.0.0 ou 256.256.256.256, puis examinez la sortie. Entrez les valeurs correctes pour le masque de sous-réseau, la passerelle et le préfixe Ipv6, le cas échéant. |
 
## Étapes suivantes
Si vous ne pouvez pas résoudre le problème, [contactez le support technique Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx) pour obtenir une assistance. <!--HONumber=52-->
 