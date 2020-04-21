<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie das [Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) , um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

In diesem Abschnitt erweitern Sie die `GraphManager` Klasse so, dass eine Funktion hinzugefügt wird, um die Ereignisse `CalendarViewController` des Benutzers abzurufen und diese neuen Funktionen zu aktualisieren.

1. Öffnen Sie **graphmanager. h** , und fügen Sie den folgenden `@interface` Code oberhalb der Deklaration hinzu.

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSData* _Nullable data, NSError* _Nullable error);
    ```

1. Fügen Sie der `@interface` Deklaration den folgenden Code hinzu.

    ```objc
    - (void) getEventsWithCompletionBlock: (GetEventsCompletionBlock)completionBlock;
    ```

1. Öffnen Sie **graphmanager. m** , und fügen Sie der `GraphManager` Klasse die folgende Funktion hinzu.

    ```objc
    - (void) getEventsWithCompletionBlock:(GetEventsCompletionBlock)completionBlock {
        // GET /me/events?$select='subject,organizer,start,end'$orderby=createdDateTime DESC
        NSString* eventsUrlString =
        [NSString stringWithFormat:@"%@/me/events?%@&%@",
         MSGraphBaseURL,
         // Only return these fields in results
         @"$select=subject,organizer,start,end",
         // Sort results by when they were created, newest first
         @"$orderby=createdDateTime+DESC"];

        NSURL* eventsUrl = [[NSURL alloc] initWithString:eventsUrlString];
        NSMutableURLRequest* eventsRequest = [[NSMutableURLRequest alloc] initWithURL:eventsUrl];

        MSURLSessionDataTask* eventsDataTask =
        [[MSURLSessionDataTask alloc]
         initWithRequest:eventsRequest
         client:self.graphClient
         completion:^(NSData *data, NSURLResponse *response, NSError *error) {
             if (error) {
                 completionBlock(nil, error);
                 return;
             }

             // TEMPORARY
             completionBlock(data, nil);
         }];

        // Execute the request
        [eventsDataTask execute];
    }
    ```

    > [!NOTE]
    > Überprüfen Sie, was `getEventsWithCompletionBlock` der Code in tut.
    >
    > - Die URL, die aufgerufen wird, lautet `/v1.0/me/events`.
    > - Der `select` Abfrageparameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.
    > - Der `orderby` Abfrageparameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.

1. Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

    ```objc
    #import "CalendarViewController.h"
    #import "SpinnerViewController.h"
    #import "GraphManager.h"
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

        [GraphManager.instance
         getEventsWithCompletionBlock:^(NSData * _Nullable data, NSError * _Nullable error) {
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

1. Führen Sie die APP aus, melden Sie sich an, und tippen Sie im Menü auf das Navigationselement **Kalender** . Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen. In diesem Abschnitt ändern Sie die `getEventsWithCompletionBlock` Funktion so, dass stark typisierte Objekte zurückgegeben werden, `CalendarViewController` und ändern, um die Ereignisse mithilfe einer Tabellenansicht zu rendern.

### <a name="update-getevents"></a>Aktualisieren von GetEvents

1. Öffnen Sie **graphmanager. h**. Ändern Sie `GetEventsCompletionBlock` die Typdefinition in Folgendes.

    ```objc
    typedef void (^GetEventsCompletionBlock)(NSArray<MSGraphEvent*>* _Nullable events, NSError* _Nullable error);
    ```

1. Öffnen Sie **graphmanager. m**. Ersetzen Sie `completionBlock(data, nil);` die- `getEventsWithCompletionBlock` Funktion durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/GraphManager.m" id="GetEventsSnippet" highlight="24-43":::

### <a name="update-calendarviewcontroller"></a>CalendarViewController aktualisieren

1. Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarTableViewCell`Projekt mit dem Namen. Wählen Sie **UITableViewCell** in der unter **Klasse von Field aus** .
1. Öffnen Sie **CalendarTableViewCell. h** , und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.h" id="CalendarTableCellSnippet":::

1. Öffnen Sie **CalendarTableViewCell. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.m" id="CalendarTableCellSnippet":::

1. Öffnen Sie **Main. Storyboard** , und suchen Sie die **Kalender Szene**. Wählen Sie die **Ansicht** in der **Kalender Szene** aus, und löschen Sie Sie.

    ![Screenshot der Ansicht in der Kalender Szene](./images/view-in-calendar-scene.png)

1. Hinzufügen einer **Tabellenansicht** aus der **Bibliothek** zur **Kalender Szene**.
1. Wählen Sie die Tabellenansicht aus, und wählen Sie dann den **Attributes Inspector**aus. Legen Sie **Prototype Cells** auf **1**fest.
1. Verwenden Sie die **Bibliothek** , um der Zelle Prototyp drei **Beschriftungen** hinzuzufügen.
1. Wählen Sie die Zelle Prototyp aus, und wählen Sie dann den **Identitäts Inspektor**aus. Ändern Sie die **Klasse** in **CalendarTableViewCell**.
1. Wählen Sie den **Attributes Inspector** aus, `EventCell`und legen Sie **Identifier** auf fest.
1. Wählen Sie mit ausgewähltem **EventCell** den **Verbindungs Inspektor** aus, `durationLabel`und `organizerLabel`verbinden `subjectLabel` Sie die Beschriftungen, die Sie der Zelle im Storyboard hinzugefügt haben.
1. Legen Sie die Eigenschaften und Einschränkungen für die drei Beschriftungen wie folgt fest.

    - **Betreff-Bezeichnung**
        - Add Constraint: Leading Space to Content View Leading Margin, Value: 0
        - Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0
        - Hinzufügen von Einschränkungen: oberer Bereich für oberer Rand der Inhaltsansicht, Wert: 0
    - **Organizer-Bezeichnung**
        - Schriftart: System 12,0
        - Add Constraint: Leading Space to Content View Leading Margin, Value: 0
        - Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0
        - Add Constraint: Top Space to subject Label Bottom, Value: Standard
    - **Dauer Bezeichnung**
        - Schriftart: System 12,0
        - Farbe: dunkle graue Farbe
        - Add Constraint: Leading Space to Content View Leading Margin, Value: 0
        - Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0
        - Add Constraint: Top Space to Organizer Label Bottom, Value: Standard
        - Add Constraint: Bottom Space to Content View Bottom Margin, Value: 8

    ![Screenshot des Zellenlayouts für Prototypen](./images/prototype-cell-layout.png)

1. Öffnen Sie **CalendarViewController. h** , und `calendarJSON` entfernen Sie die Eigenschaft.
1. Ändern Sie `@interface` die Deklaration in Folgendes.

    ```objc
    @interface CalendarViewController : UITableViewController
    ```

1. Öffnen Sie **CalendarViewController. m** , und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="objc" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.m" id="CalendarViewSnippet":::

1. Führen Sie die APP aus, melden Sie sich an, und tippen Sie auf die Registerkarte **Kalender** . Die Liste der Ereignisse sollte angezeigt werden.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-list.png)
