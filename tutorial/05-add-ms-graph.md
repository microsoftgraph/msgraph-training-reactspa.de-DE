<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft-Graph-Client-](https://github.com/microsoftgraph/msgraph-sdk-javascript) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

1. Öffnen `./src/GraphService.ts` Sie und fügen Sie die folgende Funktion hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    Überlegen Sie sich, was dieser Code macht.

    - Die URL, die aufgerufen wird, lautet `/me/calendarview`.
    - Die `header` -Methode fügt die `Prefer: outlook.timezone=""` Kopfzeile der Anforderung hinzu, wodurch sich die Zeiten in der Antwort in der bevorzugten Zeitzone des Benutzers befinden.
    - Die `query` -Methode fügt die `startDateTime` Parameter and hinzu und `endDateTime` definiert das Zeitfenster für die Kalenderansicht.
    - Die `select` -Methode schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf genau diejenigen ein, die die Ansicht tatsächlich verwendet wird.
    - Die `orderby` Methode sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.
    - Die `top` Methode schränkt die Ergebnisse auf die ersten 50-Ereignisse ein.
    - Wenn die Antwort einen `@odata.nextLink` Wert enthält, der angibt, dass weitere Ergebnisse zur Verfügung stehen, wird ein `PageIterator` Objekt verwendet, um [durch die Auflistung zu blättern](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript) , um alle Ergebnisse abzurufen.

1. Erstellen Sie eine reaktionskomponente, um die Ergebnisse des Anrufs anzuzeigen. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `Calendar.tsx` , und fügen Sie den folgenden Code hinzu.

    ```typescript
    import React from 'react';
    import { NavLink as RouterNavLink } from 'react-router-dom';
    import { Table } from 'reactstrap';
    import moment from 'moment-timezone';
    import { findOneIana } from "windows-iana";
    import { Event } from 'microsoft-graph';
    import { config } from './Config';
    import { getUserWeekCalendar } from './GraphService';
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';

    interface CalendarState {
      eventsLoaded: boolean;
      events: Event[];
      startOfWeek: Moment | undefined;
    }

    class Calendar extends React.Component<AuthComponentProps, CalendarState> {
      constructor(props: any) {
        super(props);

        this.state = {
          eventsLoaded: false,
          events: [],
          startOfWeek: undefined
        };
      }

      async componentDidUpdate() {
        try {
          // Get the user's access token
          var accessToken = await this.props.getAccessToken(config.scopes);
          // Convert user's Windows time zone ("Pacific Standard Time")
          // to IANA format ("America/Los_Angeles")
          // Moment needs IANA format
          var ianaTimeZone = findOneIana(this.props.user.timeZone);

          // Get midnight on the start of the current week in the user's timezone,
          // but in UTC. For example, for Pacific Standard Time, the time value would be
          // 07:00:00Z
          var startOfWeek = moment.tz(ianaTimeZone!.valueOf()).startOf('week').utc();

          // Get the user's events
          var events = await getUserWeekCalendar(accessToken, this.props.user.timeZone, startOfWeek);

          // Update the array of events in state
          this.setState({
            eventsLoaded: true,
            events: events,
            startOfWeek: startOfWeek
          });
        }
        catch(err) {
          this.props.setError('ERROR', JSON.stringify(err));
        }
      }

      render() {
        return (
          <pre><code>{JSON.stringify(this.state.events, null, 2)}</code></pre>
        );
      }
    }

    export default withAuthProvider(Calendar);
    ```

    Im Moment wird dadurch nur das Array von Ereignissen in JSON auf der Seite gerendert.

1. Fügen Sie diese neue Komponente zur APP hinzu. Öffnen `./src/App.tsx` Sie und fügen Sie die folgende `import` Anweisung am Anfang der Datei hinzu.

    ```typescript
    import Calendar from './Calendar';
    ```

1. Fügen Sie die folgende Komponente unmittelbar nach dem vorhandenen hinzu `<Route>` .

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. Speichern Sie die Änderungen, und starten Sie die App neu. Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** . Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie die `Calendar` Komponente aktualisieren, um die Ereignisse auf benutzerfreundlichere Weise anzuzeigen.

1. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `Calendar.css` , und fügen Sie den folgenden Code hinzu.

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. Erstellen Sie eine reaktionskomponente zum Rendern von Ereignissen an einem einzelnen Tag als Tabellenzeilen. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit dem Namen `CalendarDayRow.tsx` , und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. Fügen Sie die folgenden `import` Anweisungen am Anfang von **Calendar. TSX**hinzu.

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. Ersetzen Sie die vorhandene `render` Funktion in `./src/Calendar.tsx` durch die folgende Funktion.

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    Dadurch werden die Ereignisse in ihre jeweiligen Tage aufgeteilt und ein Tabellenabschnitt für jeden Tag gerendert.

1. Speichern Sie die Änderungen, und starten Sie die APP neu. Klicken Sie auf den Link **Kalender** , und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
