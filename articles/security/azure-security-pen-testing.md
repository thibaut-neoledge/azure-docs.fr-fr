<properties
   pageTitle="Test de pénétration | Microsoft Azure"
   description="Cet article fournit une vue d’ensemble du processus de test de pénétration (pentest) et explique comment effectuer ce test sur vos applications exécutées dans l’infrastructure Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="terrylan"/>

# Test de pénétration

Un des grands avantages de l’utilisation de Microsoft Azure pour le test et le déploiement d’applications est que vous n’êtes pas obligé d’assembler une infrastructure locale pour développer, tester et déployer vos applications. L’ensemble de l’infrastructure est prise en charge par les services de la plateforme Microsoft Azure. Vous n’avez pas à vous soucier des aspects liés à la demande, à l’acquisition, à la mise en rack et à l’empilage de votre propre matériel en local.

Tout cela est très bien, certes, mais il vous reste toujours à effectuer les contrôles de sécurité standard. Vous devez notamment soumettre les applications que vous déployez dans Azure à un test de pénétration.

Vous savez sans doute déjà que Microsoft effectue le [test de pénétration de l’environnement Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Ce processus nous aide à améliorer notre plateforme et oriente nos actions en faveur de l’amélioration des contrôles de sécurité, de l’introduction de nouveaux contrôles de sécurité et de l’amélioration de nos processus de sécurité.
 
Nous n’effectuons pas les tests de pénétration à votre place, mais nous savons parfaitement qu’il est important pour vous d’exécuter ces tests sur vos propres applications. Et nous ne pouvons que vous en remercier car, lorsque vous améliorez la sécurité de vos applications, vous renforcez la sécurité de l’ensemble de l’écosystème Azure.

Or, lorsque vous soumettez vos applications à un test de pénétration, nous pouvons le percevoir comme une attaque. Nous [surveillons en permanence](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) les schémas d’attaque et initions un processus de réponse aux incidents si les circonstances nous y obligent. Mais lancer ce processus dans le cadre de vos propres tests de pénétration ne présente aucun intérêt, pour vous comme pour nous.
 
Que faire, alors ?

Lorsque vous êtes prêt à effectuer des tests de pénétration sur vos applications hébergées sur Azure, vous devez nous en informer. Si nous savons que vous allez effectuer des tests spécifiques, nous ne prendrons aucune mesure à votre encontre (par exemple, en bloquant l’adresse IP à partir de laquelle vous effectuez vos tests), à condition que vos tests restent conformes aux conditions de test de pénétration Azure. Vous avez la possibilité d’effectuer plusieurs tests :

- Tests sur vos points de terminaison visant à détecter les [10 principales vulnérabilités de l’OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Fuzzing](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de vos points de terminaison
- [Balayage des ports](https://en.wikipedia.org/wiki/Port_scanner) de vos points de terminaison

En revanche, vous ne pouvez effectuer aucune forme d’[attaque par déni de service (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack), c’est-à-dire que nous ne pouvez ni initier une attaque par déni de service proprement dite, ni effectuer les tests associés permettant de déterminer, démontrer ou simuler n’importe quel type d’attaque de déni de service.

Prêt à vous lancer dans les tests de pénétration de vos applications hébergées dans Microsoft Azure ? Si tel est le cas, rendez-vous sur la page [Penetration Test Overview](https://security-forms.azure.com/penetration-testing/terms) (Présentation du test de pénétration) et cliquez sur le bouton Create a Testing Request (Créer une demande de test) au bas de la page. Vous trouverez également d’autres informations sur les conditions de test de pénétration ainsi que des liens utiles sur la manière de signaler des failles de sécurité liées à Azure ou à tout autre service Microsoft.

<!---HONumber=AcomDC_0525_2016-->