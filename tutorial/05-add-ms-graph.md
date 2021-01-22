<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung integrieren Sie Microsoft Graph in die Anwendung. Für diese Anwendung verwenden Sie das [Microsoft Graph SDK für Objective C,](https://github.com/microsoftgraph/msgraph-sdk-objc) um Aufrufe an Microsoft Graph zu senden.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

In diesem Abschnitt erweitern Sie die Klasse, um eine Funktion hinzuzufügen, um die Ereignisse des Benutzers für die aktuelle Woche zu erhalten, und aktualisieren, um diese `GraphManager` `CalendarViewController` neue Funktion zu verwenden.

1. Öffnen **Sie GraphManager.h,** und fügen Sie den folgenden Code über der `@interface` Deklaration hinzu.

    ```objc
    typedef void (^GetCalendarViewCompletionBlock)(NSData* _Nullable data,
                                                   NSError* _Nullable error);
    ```

1. Fügen Sie der Deklaration den folgenden Code `@interface` hinzu.

    ```objc
    - (void) getCalendarViewStartingAt: (NSString*) viewStart
                              endingAt: (NSString*) viewEnd
                   withCompletionBlock: (GetCalendarViewCompletionBlock) completion;
    ```

1. Öffnen **Sie GraphManager.m,** und fügen Sie der Klasse die folgende Funktion `GraphManager` hinzu.

    ```objc
    - (void) getCalendarViewStartingAt: (NSString *) viewStart
                              endingAt: (NSString *) viewEnd
                   withCompletionBlock: (GetCalendarViewCompletionBlock) completion {
        // Set calendar view start and end parameters
        NSString* viewStartEndString =
        [NSString stringWithFormat:@"startDateTime=%@&endDateTime=%@",
         viewStart,
         viewEnd];

        // GET /me/calendarview
        NSString* eventsUrlString =
        [NSString stringWithFormat:@"%@/me/calendarview?%@&%@&%@&%@",
         MSGraphBaseURL,
         viewStartEndString,
         // Only return these fields in results
         @"$select=subject,organizer,start,end",
         // Sort results by start time
         @"$orderby=start/dateTime",
         // Request at most 25 results
         @"$top=25"];

        NSURL* eventsUrl = [[NSURL alloc] initWithString:eventsUrlString];
        NSMutableURLRequest* eventsRequest = [[NSMutableURLRequest alloc] initWithURL:eventsUrl];

        // Add the Prefer: outlook.timezone header to get start and end times
        // in user's time zone
        NSString* preferHeader =
        [NSString stringWithFormat:@"outlook.timezone=\"%@\"",
         self.graphTimeZone];
        [eventsRequest addValue:preferHeader forHTTPHeaderField:@"Prefer"];

        MSURLSessionDataTask* eventsDataTask =
        [[MSURLSessionDataTask alloc]
         initWithRequest:eventsRequest
         client:self.graphClient
         completion:^(NSData *data, NSURLResponse *response, NSError *error) {
             if (error) {
                 completion(nil, error);
                 return;
             }

             // TEMPORARY
             completion(data, nil);
         }];

        // Execute the request
        [eventsDataTask execute];
    }
    ```

    > [!NOTE]
    > Überlegen Sie, was der Code `getCalendarViewStartingAt` gerade macht.
    >
    > - Die URL, die aufgerufen wird, lautet `/v1.0/me/calendarview`.
    >   - Die `startDateTime` Parameter `endDateTime` und Abfrageparameter definieren den Anfang und das Ende der Kalenderansicht.
    >   - Der Abfrageparameter beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `select` tatsächlich von der Ansicht verwendet werden.
    >   - Der `orderby` Abfrageparameter sortiert die Ergebnisse nach Startzeit.
    >   - Der `top` Abfrageparameter fordert 25 Ergebnisse pro Seite an.
    >   - Der Header bewirkt, dass Microsoft Graph die Start- und Endzeiten der einzelnen Ereignisse in der Zeitzone des `Prefer: outlook.timezone` Benutzers zurück gibt.

1. Erstellen Sie eine neue **Cocoa Touch-Klasse** im **GraphTutorial-Projekt** mit dem Namen **GraphToIana**. Wählen Sie in der Unterklasse des Felds **"NSObject"** aus. 
1. Öffnen **Sie GraphToIana.h,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.h" id="GraphToIanaSnippet":::

1. Öffnen **Sie GraphToIana.m,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphToIana.m" id="GraphToIanaSnippet":::

    Dies führt eine einfache Suche durch, um einen IANA-Zeitzonenbezeichner basierend auf dem von Microsoft Graph zurückgegebenen Zeitzonennamen zu finden.

1. Öffnen **Sie CalendarViewController.m,** und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

    ```objc
    #import "CalendarViewController.h"
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
    #import "GraphToIana.h"
    #import <MSGraphClientModels/MSGraphClientModels.h>

    @interface CalendarViewController ()

    @property SpinnerViewController* spinner;

    @end

    @implementation CalendarViewController

    - (void)viewDidLoad {
        [super viewDidLoad];
        // Do any additional setup after loading the view.

        self.spinner = [SpinnerViewController alloc];
        [self.spinner startWithContainer:self];

        // Calculate the start and end of the current week
        NSString* timeZoneId = [GraphToIana
                                getIanaIdentifierFromGraphIdentifier:
                                [GraphManager.instance graphTimeZone]];

        NSDate* now = [NSDate date];
        NSCalendar* calendar = [NSCalendar calendarWithIdentifier:NSCalendarIdentifierGregorian];
        NSTimeZone* timeZone = [NSTimeZone timeZoneWithName:timeZoneId];
        [calendar setTimeZone:timeZone];

        NSDateComponents* startOfWeekComponents = [calendar
                                                   components:NSCalendarUnitCalendar |
                                                   NSCalendarUnitYearForWeekOfYear |
                                                   NSCalendarUnitWeekOfYear
                                                   fromDate:now];
        NSDate* startOfWeek = [startOfWeekComponents date];
        NSDate* endOfWeek = [calendar dateByAddingUnit:NSCalendarUnitDay
                                                 value:7
                                                toDate:startOfWeek
                                               options:0];

        // Convert start and end to ISO 8601 strings
        NSISO8601DateFormatter* isoFormatter = [[NSISO8601DateFormatter alloc] init];
        NSString* viewStart = [isoFormatter stringFromDate:startOfWeek];
        NSString* viewEnd = [isoFormatter stringFromDate:endOfWeek];

        [GraphManager.instance
         getCalendarViewStartingAt:viewStart
         endingAt:viewEnd
         withCompletionBlock:^(NSData * _Nullable data, NSError * _Nullable error) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [self.spinner stop];

                 if (error) {
                     // Show the error
                     UIAlertController* alert = [UIAlertController
                                                 alertControllerWithTitle:@"Error getting events"
                                                 message:error.debugDescription
                                                 preferredStyle:UIAlertControllerStyleAlert];

                     UIAlertAction* okButton = [UIAlertAction
                                                actionWithTitle:@"OK"
                                                style:UIAlertActionStyleDefault
                                                handler:nil];

                     [alert addAction:okButton];
                     [self presentViewController:alert animated:true completion:nil];
                     return;
                 }

                 // TEMPORARY
                 self.calendarJSON.text = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                 [self.calendarJSON sizeToFit];
             });
         }];
    }

    @end
    ```

1. Führen Sie die App aus, melden Sie sich an, und tippen Sie im Menü auf das **Navigationselement** "Kalender". Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie das JSON-Dump durch eine benutzerfreundliche Anzeige der Ergebnisse ersetzen. In diesem Abschnitt ändern Sie die Funktion so, dass stark typierte Objekte zurückgeben werden, und ändern sie so, dass zum Rendern der Ereignisse eine `getCalendarViewStartingAt` `CalendarViewController` Tabellenansicht verwendet wird.

### <a name="update-getcalendarviewstartingat"></a>GetCalendarViewStartingAt aktualisieren

1. Öffnen **Sie GraphManager.h**. Ändern Sie `GetCalendarViewCompletionBlock` die Typdefinition wie folgt.

    ```objc
    typedef void (^GetCalendarViewCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. Öffnen **Sie GraphManager.m**. Ersetzen Sie die vorhandene `getCalendarViewStartingAt`-Funktion durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="GetCalendarViewSnippet" highlight="42-61":::

### <a name="update-calendarviewcontroller"></a>Aktualisieren von CalendarViewController

1. Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **GraphTutorial-Projekt** mit dem Namen `CalendarTableViewCell` . Wählen **Sie "UITableViewCell"** in der **Unterklasse des Felds** aus.
1. Öffnen **Sie CalendarTableViewCell.h,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.h" id="CalendarTableCellSnippet":::

1. Öffnen **Sie CalendarTableViewCell.m,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.m" id="CalendarTableCellSnippet":::

1. Erstellen Sie eine neue **Cocoa Touch Class-Datei** im **GraphTutorial-Projekt** mit dem Namen `CalendarTableViewController` . Wählen **Sie "UITableViewController"** in der **Unterklasse des Felds** aus.
1. Öffnen **Sie CalendarTableViewController.h,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.h" id="CalendarTableViewControllerSnippet":::

1. Öffnen **Sie CalendarTableViewController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewController.m" id="CalendarTableViewControllerSnippet":::

1. Öffnen **Sie Main.storyboard,** und suchen Sie **die Kalender-Szene.** Löschen Sie die Bildlaufansicht aus der Stammansicht.
1. Fügen Sie **in der Bibliothek** oben in der **Ansicht** eine Navigationsleiste hinzu.
1. Doppelklicken Sie auf **den Titel** in der Navigationsleiste, und aktualisieren Sie ihn in `Calendar` .
1. Fügen Sie **in der Bibliothek** rechts neben der Navigationsleiste ein Balkenschaltflächeelement hinzu. 
1. Wählen Sie die Schaltfläche für die neue Leiste und dann den **Attributprüfungsinspektor aus.** Bild **in** Plus **ändern.**
1. Fügen Sie der  **Ansicht unter der Navigationsleiste** eine Containeransicht aus der Bibliothek hinzu. Ändern Sie die Größe der Containeransicht, um den verbleibenden Platz in der Ansicht zu übernehmen.
1. Legen Sie Einschränkungen für die Navigationsleiste und die Containeransicht wie folgt ein.

    - **Navigationsleiste**
        - Einschränkung hinzufügen: Höhe, Wert: 44
        - Einschränkung hinzufügen: Führendes Leerzeichen zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz zum sicheren Bereich, Wert: 0
    - **Containeransicht**
        - Einschränkung hinzufügen: Führendes Leerzeichen zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum sicheren Bereich, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz auf der Navigationsleiste unten, Wert: 0
        - Einschränkung hinzufügen: Unterer Platz zum sicheren Bereich, Wert: 0

1. Suchen Sie den zweiten Ansichtscontroller, der dem Storyboard hinzugefügt wurde, wenn Sie die Containeransicht hinzugefügt haben. Es wird durch eine **Einbettungs-Segue** mit der Kalenderszene verbunden. Wählen Sie diesen Controller aus, und verwenden Sie den **Identitätsinspektor,** um **die Klasse in** **CalendarTableViewController zu ändern.**
1. Löschen Sie **die Ansicht** aus dem **Kalender tabellenansichtscontroller**.
1. Fügen Sie dem  **Tabellenansichtscontroller** des Kalenders eine Tabellenansicht aus der **Bibliothek hinzu.**
1. Wählen Sie die Tabellenansicht und dann den **Attributes Inspector aus.** Set **Prototype Cells** to **1**.
1. Ziehen Sie den unteren Rand der Prototypzelle, um Ihnen einen größeren Bereich für die Arbeit zu bieten.
1. Verwenden Sie die **Bibliothek,** um **der** Prototypzelle drei Beschriftungen hinzuzufügen.
1. Wählen Sie die Prototypzelle und dann den **Identitätsinspektor aus.** Ändern **Sie die Klasse** in **CalendarTableViewCell**.
1. Wählen Sie den **Attributes Inspector aus,** und legen **Sie den Bezeichner auf** . `EventCell`
1. Wenn Die **EventCell ausgewählt ist,** wählen Sie den **Connections Inspector** und verbinden , und mit den Beschriftungen, die Sie der Zelle `durationLabel` im `organizerLabel` `subjectLabel` Storyboard hinzugefügt haben.
1. Legen Sie die Eigenschaften und Einschränkungen für die drei Beschriftungen wie folgt dar.

    - **Betreffbezeichnung**
        - Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz am oberen Rand der Inhaltsansicht, Wert: 0
    - **Bezeichnung des Organisators**
        - Schriftart: System 12.0
        - Einschränkung hinzufügen: Höhe, Wert: 15
        - Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz im unteren Bereich der Betreffbezeichnung, Wert: Standard
    - **Bezeichnung "Dauer"**
        - Schriftart: System 12.0
        - Farbe: Dunkelgrau
        - Einschränkung hinzufügen: Höhe, Wert: 15
        - Einschränkung hinzufügen: Führendes Leerzeichen zum führenden Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Nachgestellter Speicherplatz zum nachgestellten Rand der Inhaltsansicht, Wert: 0
        - Einschränkung hinzufügen: Oberster Platz am unteren Rand der Bezeichnung "Organizer", Wert: Standard
        - Einschränkung hinzufügen: Unterer Bereich zum unteren Rand der Inhaltsansicht, Wert: 0

1. Select the **EventCell,** then select the **Size Inspector**. Aktivieren **Sie "Automatisch** für **Zeilenhöhe".**

    ![Screenshot der Ansichtscontroller für Kalender- und Kalendertabelle](images/calendar-table-storyboard.png)

1. Öffnen **Sie CalendarViewController.h,** und entfernen Sie die `calendarJSON` Eigenschaft.

1. Öffnen **Sie CalendarViewController.m,** und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.m" id="CalendarViewControllerSnippet":::

1. Führen Sie die App aus, melden Sie sich an, und tippen Sie auf die **Registerkarte "Kalender".** Die Liste der Ereignisse sollte angezeigt werden.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-list.png)
