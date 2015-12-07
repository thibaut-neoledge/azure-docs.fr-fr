<properties 
	pageTitle="Vue d'ensemble du Kit de développement logiciel de Windows Universal" 
	description="Vue d'ensemble du Kit de développement logiciel de Windows Universal pour Azure Mobile Engagement" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Vue d'ensemble du Kit de développement logiciel de Windows Universal pour Azure Engagement Mobile

Cliquez ici pour obtenir tous les détails de l'intégration d'Azure Mobile Engagement dans une application Windows Universal. Si vous souhaitez tout d'abord l'essayer, pensez à suivre notre [didacticiel de 15 minutes](mobile-engagement-windows-store-dotnet-get-started.md).

Cliquez pour voir le [contenu du Kit de développement logiciel (SDK)](mobile-engagement-windows-store-sdk-content.md)

##Procédures d'intégration

1. Commencez ici : [intégration de Mobile Engagement à votre application Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)

2. Pour les notifications : [intégration du module Couverture (Notifications) à votre application Windows Universal](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. Implémentation du plan de balise : [utilisation de l'API avancée de balisage de Mobile Engagement dans votre application Windows Universal](mobile-engagement-windows-store-use-engagement-api.md)

##Notes de publication

###3\.2.0 (20/11/2015)

-   Prise en charge supplémentaire pour les applications de la plateforme Windows universelle Windows 10.
-   Ajout de la fonctionnalité de partage de canal Push pour résoudre les conflits de canal (désormais compatibles avec Azure Notification Hubs).
-   Incident fixe lors de la demande de l'ID de l'appareil juste après l'initialisation.
-   Améliorations des journaux de la console.
-   Incident fixe lors de l'analyse de certaines exceptions non gérées.

Pour les versions antérieures, consultez les [notes de publication complètes](mobile-engagement-windows-store-release-notes.md)

##Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d'Engagement dans votre application, vous devez prendre en compte les points suivants lors de la mise à niveau du Kit de développement logiciel.

Si vous avez manqué plusieurs versions du Kit de développement logiciel (SDK), vous devrez peut-être effectuer plusieurs procédures. Consultez la version complète des [Procédures mises à jour](mobile-engagement-windows-store-upgrade-procedure.md) pour les découvrir. Par exemple, si vous migrez de la version 0.10.1 vers 0.11.0, vous devez tout d'abord suivre la procédure « Migration de 0.9.0 vers 0.10.1 », puis la procédure « Migration de 0.10.1 vers 0.11.0 ».

###De 3.1.0 à 3.2.0

#### Ressources
Cette étape concerne uniquement les ressources personnalisées. Si vous avez personnalisé les ressources fournies par le Kit de développement logiciel (HTML, images, superposition), vous devez ensuite les sauvegarder avant de les mettre à niveau et réappliquer votre personnalisation sur les ressources mises à niveau.

#### l'intégration de vue web
Certaines améliorations pour la correspondance des facteurs de forme d'appareil ont été introduites dans cette version. Assurez-vous que votre intégration de la vue Web correspond à ce qui suit :

Dans votre page XAML () :

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

Et dans votre fichier .cs associé :

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

### Mise à niveau à partir de versions antérieures

Consultez la rubrique [Procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure/)

<!---HONumber=AcomDC_1125_2015-->