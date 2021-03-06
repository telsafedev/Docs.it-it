---
title: Registrazione a prestazioni elevate con LoggerMessage in ASP.NET Core
author: guardrex
description: "Informazioni su come usare le funzionalità di LoggerMessage per creare delegati inseribili nella cache che richiedono un numero minore di allocazioni di oggetti rispetto ai metodi di estensione del logger per scenari di registrazione a prestazioni elevate."
manager: wpickett
ms.author: riande
ms.date: 11/03/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: b155826b5047e88a79d9e339d7bca8885a79006d
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2018
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>Registrazione a prestazioni elevate con LoggerMessage in ASP.NET Core

Di [Luke Latham](https://github.com/guardrex)

Le funzionalità di [LoggerMessage](/dotnet/api/microsoft.extensions.logging.loggermessage) creano delegati inseribili nella cache che richiedono un numero minore di allocazioni di oggetti e riducono il sovraccarico di calcolo rispetto ai [metodi di estensione del logger](/dotnet/api/Microsoft.Extensions.Logging.LoggerExtensions), ad esempio `LogInformation`, `LogDebug` e `LogError`. Per gli scenari di registrazione a prestazioni elevate, usare `LoggerMessage`.

`LoggerMessage` offre i seguenti vantaggi in termini di prestazioni rispetto ai metodi di estensione del logger:

* I metodi di estensione del logger richiedono una "conversione boxing" dei tipi di valori, ad esempio `int`, in `object`. `LoggerMessage` evita la conversione boxing usando campi `Action` statici e metodi di estensione con parametri fortemente tipizzati.
* I metodi di estensione del logger devono analizzare il modello di messaggio (stringa di formato denominata) ogni volta che viene scritto un messaggio del log. Solo `LoggerMessage` richiede una sola analisi del modello durante la definizione del messaggio.

[Visualizzare o scaricare il codice di esempio](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/sample/) ([procedura per il download](xref:tutorials/index#how-to-download-a-sample))

L'app di esempio illustra le funzionalità di `LoggerMessage` con un sistema di verifica delle offerte di base. L'app aggiunge ed elimina le offerte usando un database in memoria. Durante l'esecuzione di queste operazioni, i messaggi del log vengono generati usando `LoggerMessage`.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](/dotnet/api/microsoft.extensions.logging.loggermessage.define) crea un delegato `Action` per la registrazione di un messaggio. Gli overload `Define` permettono il passaggio di un massimo di sei parametri di tipo in una stringa di formato denominata (modello).

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) crea un delegato `Func` per definire un [ambito del log](xref:fundamentals/logging/index#log-scopes). Gli overload `DefineScope` permettono il passaggio di un massimo di tre parametri di tipo in una stringa di formato denominata (modello).

## <a name="message-template-named-format-string"></a>Modello di messaggio (stringa di formato denominata)

La stringa specificata nei metodi `Define` e `DefineScope` è un modello e non una stringa interpolata. I segnaposto vengono inseriti nell'ordine in cui sono specificati i tipi. I nomi dei segnaposto nel modello devono essere descrittivi e coerenti tra i modelli. Vengono usati come nomi di proprietà all'interno dei dati di log strutturati. Per i nomi dei segnaposto è consigliabile usare la [convenzione Pascal](/dotnet/standard/design-guidelines/capitalization-conventions). Ad esempio, `{Count}`, `{FirstName}`.

## <a name="implementing-loggermessagedefine"></a>Implementazione di LoggerMessage.Define

Ogni messaggio di log è un elemento `Action` contenuto in un campo statico creato da `LoggerMessage.Define`. Ad esempio, l'app di esempio crea un campo per descrivere un messaggio di log per una richiesta GET per la pagina di indice (*Internal/LoggerExtensions.cs*):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet1)]

Per `Action`, specificare:

* Il livello del log.
* Un identificatore di evento univoco ([EventId](/dotnet/api/microsoft.extensions.logging.eventid)) con il nome del metodo di estensione statico.
* Il modello di messaggio (stringa di formato denominata). 

Una richiesta per la pagina di indice dell'app di esempio imposta:

* Il livello del log su `Information`.
* L'ID evento su `1` con il nome del metodo `IndexPageRequested`.
* Il modello di messaggio (stringa di formato denominata) su una stringa.

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet5)]

Negli archivi di log strutturati è possibile che venga usato il nome evento quando viene specificato con l'ID evento per arricchire la registrazione. Ad esempio, [Serilog](https://github.com/serilog/serilog-extensions-logging) usa il nome evento.

`Action` viene richiamato attraverso un metodo di estensione fortemente tipizzato. Il metodo `IndexPageRequested` registra un messaggio per la richiesta GET di una pagina di indice nell'app di esempio:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested` viene chiamato nel logger nel metodo `OnGetAsync` in *Pages/Index.cshtml.cs*:

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Esaminare l'output della console dell'app:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Per passare i parametri in un messaggio di log, definire un massimo di sei tipi durante la creazione del campo statico. L'app di esempio registra una stringa quando viene aggiunta un'offerta definendo un tipo `string` per il campo `Action`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet2)]

Il modello di messaggio di log del delegato riceve i valori dei segnaposto dai tipi specificati. L'app di esempio definisce un delegato per l'aggiunta di un'offerta dove il parametro dell'offerta è un elemento `string`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet6)]

Il metodo di estensione statico per l'aggiunta di un'offerta, `QuoteAdded`, riceve il valore dell'argomento dell'offerta e lo passa al delegato `Action`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet10)]

Nel modello della pagina di indice (*Pages/Index.cshtml.cs*) viene chiamato `QuoteAdded` per registrare il messaggio:

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Esaminare l'output della console dell'app:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand')
```

L'app di esempio implementa un modello `try`&ndash;`catch` per l'eliminazione dell'offerta. Per un'operazione di eliminazione con esito positivo, viene registrato un messaggio informativo. Per un'operazione di eliminazione con la generazione di un'eccezione, viene registrato un messaggio di errore. Il messaggio di log per un'operazione di eliminazione con esito negativo include l'analisi dello stack dell'eccezione (*Internal/LoggerExtensions.cs*):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet7)]

Si noti come l'eccezione viene passata al delegato in `QuoteDeleteFailed`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet11)]

Nel modello di pagina per la pagina di indice l'eliminazione di un'offerta con esito positivo chiama il metodo `QuoteDeleted` nel logger. Quando non viene trovata un'offerta per l'eliminazione, viene generata un'eccezione `ArgumentNullException`. L'eccezione viene intercettata dall'istruzione `try`&ndash;`catch` e registrata chiamando il metodo `QuoteDeleteFailed` nel logger nel blocco `catch` (*Pages/Index.cshtml.cs*):

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Quando un'offerta viene eliminata, esaminare l'output della console dell'app:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Quando l'eliminazione di un'offerta ha esito negativo, esaminare l'output della console dell'app. Si noti che l'eccezione è inclusa nel messaggio di log:

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T](T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() in 
      <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="implementing-loggermessagedefinescope"></a>Implementazione di LoggerMessage.DefineScope

Definire un [ambito del log](xref:fundamentals/logging/index#log-scopes) da applicare a una serie di messaggi di log usando il metodo [DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope).

L'app di esempio include un pulsante **Clear All** (Cancella tutto) per eliminare tutte le offerte nel database. Le offerte vengono eliminate rimuovendole una alla volta. Ogni volta che viene eliminata un'offerta, viene chiamato il metodo `QuoteDeleted` nel logger. Viene aggiunto un ambito di log a questi messaggi di log.

Abilitare `IncludeScopes` nelle opzioni del logger della console:

[!code-csharp[Main](loggermessage/sample/Program.cs?name=snippet1&highlight=22)]

Nelle app ASP.NET Core 2.0 è necessario impostare `IncludeScopes` per abilitare gli ambiti di log. L'impostazione di `IncludeScopes` tramite i file di configurazione *appsettings* è una funzionalità prevista per ASP.NET Core 2.1.

L'app di esempio cancella gli altri provider e aggiunge filtri per ridurre l'output di registrazione. Ciò facilita la visualizzazione dei messaggi di log dell'esempio che descrive le funzionalità di `LoggerMessage`.

Per creare un ambito di log, aggiungere un campo per inserire un delegato `Func` per l'ambito. L'app di esempio crea un campo denominato `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet4)]

Usare `DefineScope` per creare il delegato. È possibile specificare un massimo di tre tipi da usare come argomenti di modello quando viene richiamato il delegato. L'app di esempio usa un modello di messaggio che include il numero di offerte eliminate (un tipo `int`):

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet8)]

Specificare un metodo di estensione statico per il messaggio di log. Includere i parametri di tipo per le proprietà denominate visualizzate nel modello di messaggio. L'app di esempio accetta un numero `count` di offerte da eliminare e restituisce `_allQuotesDeletedScope`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet12)]

L'ambito esegue il wrapping delle chiamate di estensione di registrazione in un blocco `using`:

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Esaminare i messaggi di log nell'output della console dell'app. Il risultato seguente mostra tre offerte eliminate con il messaggio di ambito di log incluso:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

## <a name="see-also"></a>Vedere anche

* [Registrazione](xref:fundamentals/logging/index)
