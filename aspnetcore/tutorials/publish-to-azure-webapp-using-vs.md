---
title: Pubblicare un'app ASP.NET Core in Azure con Visual Studio
author: rick-anderson
description: Informazioni su come pubblicare un'app ASP.NET Core in Servizio app di Azure con Visual Studio.
manager: wpickett
ms.author: riande
ms.date: 12/16/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 14d8dd0a5e6a99bacce3bf50b0468b20e0dddb96
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2018
---
# <a name="publish-an-aspnet-core-web-app-to-azure-app-service-using-visual-studio"></a>Pubblicare un'app Web ASP.NET Core in Servizio app di Azure con Visual Studio

Di [Rick Anderson](https://twitter.com/RickAndMSFT), [Cesar Blum Silveira](https://github.com/cesarbs) e [Rachel Appel](https://twitter.com/rachelappel)

Vedere [Publish to Azure from Visual Studio for Mac](https://blog.xamarin.com/publish-azure-visual-studio-mac/) (Pubblicare in Azure da Visual Studio per Mac) se si usa un Mac.

## <a name="set-up"></a>Impostare

* Aprire un [account Azure gratuito](https://aka.ms/K5y5yh) se non è già disponibile un account. 

## <a name="create-a-web-app"></a>Creare un'app Web

Nella Pagina iniziale di Visual Studio selezionare **File > Nuovo > Progetto**

![File (menu)](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Completare la finestra di dialogo **Nuovo progetto**:

* Nel riquadro a sinistra selezionare **.NET Core**.
* Nel riquadro al centro selezionare **Applicazione Web ASP.NET Core**.
* Scegliere **OK**.

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Nella finestra di dialogo **Nuova applicazione Web ASP.NET Core**:

* Selezionare **Applicazione Web**.
* Selezionare **Modifica autenticazione**.

![Finestra di dialogo Nuovo progetto](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Viene visualizzata la finestra di dialogo **Modifica autenticazione**. 

* Selezionare **Account utente individuali**.
* Selezionare **OK** per tornare a **Nuova applicazione Web ASP.NET Core** e selezionare nuovamente **OK**.

![Finestra per autenticazione Nuova applicazione Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio crea la soluzione.

## <a name="run-the-app"></a>Eseguire l'app

* Premere CTRL+F5 per eseguire il progetto.
* Eseguire il test dei collegamenti **About** (Informazioni su) e **Contact** (Contatto).

![Applicazione Web aperta in Microsoft Edge su localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrare un utente

* Selezionare **Registra** e registrare un nuovo utente. È possibile usare un indirizzo di posta elettronica fittizio. Quando si esegue l'invio, nella pagina viene visualizzato l'errore seguente:

    *"Internal Server Error: A database operation failed while processing the request. Per un'eccezione SQL, non è possibile aprire il database. Applying existing migrations for Application DB context may resolve this issue."* (Errore interno del server: Operazione sul database non riuscita durante l'elaborazione della richiesta. Eccezione SQL: Impossibile aprire il file di database. Per risolvere il problema, applicare le migrazioni esistenti per il contesto di database dell'applicazione).
* Selezionare **Apply Migrations** (Applica migrazioni) e, quando la pagina è stata caricata, eseguire l'aggiornamento.

![Un errore interno del server indicante che un'operazione di database non è riuscita durante l'elaborazione della richiesta. Per un'eccezione SQL, non è possibile aprire il database. Per risolvere il problema, provare ad applicare le migrazioni esistenti per il contesto di database dell'applicazione.](publish-to-azure-webapp-using-vs/_static/mig.png)

L'app visualizza l'indirizzo di posta elettronica usato per registrare il nuovo utente e un collegamento **Disconnessione**.

![Applicazione Web aperta in Microsoft Edge Il collegamento Register (Registra) viene sostituito dal testo Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Distribuire l'app in Azure

In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica...**.

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

Nella finestra di dialogo **Pubblica**:

* Selezionare **Servizio app di Microsoft Azure**.
* Selezionare l'icona a forma di ingranaggio e quindi selezionare **Crea profilo**.
* Selezionare **Crea profilo**.

![Finestra di dialogo Pubblica](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Creare risorse di Azure

Viene visualizzata la finestra di dialogo **Crea servizio app**:

* Immettere la sottoscrizione.
* I campi di immissione **Nome dell'app**, **Gruppo di risorse** e **Piano di servizio app** vengono popolati automaticamente. È possibile mantenere questi nomi o modificarli.

![Finestra di dialogo Servizio app](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Selezionare la scheda **Servizi** per creare un nuovo database.

* Selezionare l'icona verde **+** per creare un nuovo database SQL

![Nuovo database SQL](publish-to-azure-webapp-using-vs/_static/sql.png)

* Selezionare **Nuovo** nella finestra di dialogo **Configura database SQL** per creare un nuovo database.

![Nuovo database SQL e server](publish-to-azure-webapp-using-vs/_static/conf.png)

Viene visualizzata la finestra di dialogo **Configura SQL Server**.

* Immettere nome utente e password di amministratore e selezionare **OK**. È possibile mantenere il **Nome server** predefinito. 

> [!NOTE]
> La stringa "admin" non è consentita come nome utente di amministratore.

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Scegliere **OK**.

Visual Studio torna alla finestra di dialogo **Crea servizio app**.

* Selezionare **Crea** nella finestra di dialogo **Crea servizio app**.

![Finestra di dialogo Configura database SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio crea l'app Web e SQL Server in Azure. Questo passaggio potrebbe richiedere alcuni minuti. Per informazioni sulle risorse create, vedere [Risorse aggiuntive](#additonal-resources).

Al termine della distribuzione, selezionare **impostazioni**:

![Finestra di dialogo Configura SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Nella pagina **Impostazioni** della finestra di dialogo **Pubblica**:

  * Espandere **Database** e selezionare **Usa questa stringa di connessione in fase di esecuzione**.
  * Espandere **Migrazioni Entity Framework** e selezionare **Applica questa migrazione in fase di pubblicazione**.

* Selezionare **Salva**. Visual Studio torna alla finestra di dialogo **Pubblica**. 

![Finestra di dialogo Pubblica: pannello Impostazioni](publish-to-azure-webapp-using-vs/_static/pubs.png)

Fare clic su **Pubblica**. Visual Studio pubblica l'app in Azure. Al termine della distribuzione, l'app viene aperta in un browser.

### <a name="test-your-app-in-azure"></a>Testare l'app in Azure

* Eseguire il test dei collegamenti **About** (Informazioni su) e **Contact** (Contatto).

* Registrare un nuovo utente

![Applicazione Web aperta in Microsoft Edge in Servizio app di Azure](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aggiornare l'app

* Modificare la pagina Razor *Pages/About.cshtml* e modificarne il contenuto. Ad esempio, è possibile modificare il paragrafo specificando "Hello ASP.NET Core!": [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Fare clic con il pulsante destro del mouse sul progetto e selezionare **Pubblica**.

![Menu di scelta rapida con il collegamento per la pubblicazione evidenziato](publish-to-azure-webapp-using-vs/_static/pub.png)

* Dopo la pubblicazione dell'app, verificare che le modifiche apportate siano disponibili in Azure.

![Verificare che l'attività sia stata completata](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Eseguire la pulizia

Al termine del test dell'app accedere al [portale di Azure](https://portal.azure.com/) ed eliminare l'app.

* Selezionare **Gruppi di risorse** e in seguito il gruppo di risorse che è stato creato.

![Portale di Azure: Gruppi di risorse nel menu laterale](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Nella pagina **Gruppi di risorse** selezionare **Elimina**.

![Portale di Azure: pagina Gruppi di risorse](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Immettere il nome del gruppo di risorse e selezionare **Elimina**. A questo punto l'app e tutte le altre risorse create in questa esercitazione vengono eliminate da Azure.

### <a name="next-steps"></a>Passaggi successivi

* [Distribuzione continua in Azure con Visual Studio e Git](xref:host-and-deploy/azure-apps/azure-continuous-deployment)

## <a name="additonal-resources"></a>Risorse aggiuntive

* [Servizio app di Azure](https://docs.microsoft.com/azure/app-service/app-service-web-overview)
* [Gruppi di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/)
