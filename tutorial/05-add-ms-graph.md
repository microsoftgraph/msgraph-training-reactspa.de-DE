<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie die Microsoft-Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [microsoft-graph-client-Bibliothek,](https://github.com/microsoftgraph/msgraph-sdk-javascript) um Anrufe an Microsoft Graph.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

1. Öffnen `./src/GraphService.ts` Sie die folgende Funktion, und fügen Sie sie hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getUserWeekCalendarSnippet":::

    Überlegen Sie sich, was dieser Code macht.

    - Die URL, die aufgerufen wird, lautet `/me/calendarview`.
    - Die Methode fügt der Anforderung den Header hinzu, sodass sich die Zeiten in der Antwort in der bevorzugten Zeitzone des Benutzers `header` `Prefer: outlook.timezone=""` befinden.
    - Die -Methode fügt die Parameter and hinzu und definiert das `query` `startDateTime` `endDateTime` Zeitfenster für die Kalenderansicht.
    - Die Methode beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `select` von der Ansicht tatsächlich verwendet werden.
    - Die Methode sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der sie erstellt wurden, und das letzte `orderby` Element ist zuerst.
    - Die `top` Methode beschränkt die Ergebnisse einer einzelnen Seite auf 25 Ereignisse.
    - Wenn die Antwort einen Wert enthält, der angibt, dass mehr Ergebnisse verfügbar sind, wird ein Objekt verwendet, um die Auflistung zu pagen, um `@odata.nextLink` `PageIterator` alle Ergebnisse zu erhalten. [](https://docs.microsoft.com/graph/sdks/paging?tabs=typeScript)

1. Erstellen Sie React Komponente zum Anzeigen der Ergebnisse des Anrufs. Erstellen Sie eine neue Datei im `./src` Verzeichnis `Calendar.tsx` namens, und fügen Sie den folgenden Code hinzu.

    ```typescript
    import React from 'react';
    import { NavLink as RouterNavLink } from 'react-router-dom';
    import { Table } from 'reactstrap';
    import moment, { Moment } from 'moment-timezone';
    import { findIana } from "windows-iana";
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
        if (this.props.user && !this.state.eventsLoaded)
        {
          try {
            // Get the user's access token
            var accessToken = await this.props.getAccessToken(config.scopes);

            // Convert user's Windows time zone ("Pacific Standard Time")
            // to IANA format ("America/Los_Angeles")
            // Moment needs IANA format
            var ianaTimeZones = findIana(this.props.user.timeZone);

            // Get midnight on the start of the current week in the user's timezone,
            // but in UTC. For example, for Pacific Standard Time, the time value would be
            // 07:00:00Z
            var startOfWeek = moment.tz(ianaTimeZones![0].valueOf()).startOf('week').utc();

            // Get the user's events
            var events = await getUserWeekCalendar(accessToken, this.props.user.timeZone, startOfWeek);

            // Update the array of events in state
            this.setState({
              eventsLoaded: true,
              events: events,
              startOfWeek: startOfWeek
            });
          }
          catch (err) {
            this.props.setError('ERROR', JSON.stringify(err));
          }
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

    Damit wird derzeit nur das Array von Ereignissen in JSON auf der Seite gerendert.

1. Fügen Sie diese neue Komponente der App hinzu. Öffnen `./src/App.tsx` Sie die folgende `import` Anweisung, und fügen Sie sie am oberen Rand der Datei hinzu.

    ```typescript
    import Calendar from './Calendar';
    ```

1. Fügen Sie die folgende Komponente direkt hinter der vorhandenen `<Route>` hinzu.

    ```typescript
    <Route exact path="/calendar"
      render={(props) =>
        this.props.isAuthenticated ?
          <Calendar {...props} /> :
          <Redirect to="/" />
      } />
    ```

1. Speichern Sie die Änderungen, und starten Sie die App neu. Melden Sie sich an, **und** klicken Sie in der Navigationsleiste auf den Link Kalender. Wenn alles funktioniert, sollte ein JSON-Abbild von Ereignissen im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie die Komponente so aktualisieren, dass die Ereignisse `Calendar` benutzerfreundlicher angezeigt werden.

1. Erstellen Sie eine neue Datei im `./src` Verzeichnis `Calendar.css` namens, und fügen Sie den folgenden Code hinzu.

    :::code language="css" source="../demo/graph-tutorial/src/Calendar.css":::

1. Erstellen Sie React Komponente, um Ereignisse an einem einzelnen Tag als Tabellenzeilen zu rendern. Erstellen Sie eine neue Datei im `./src` Verzeichnis `CalendarDayRow.tsx` namens, und fügen Sie den folgenden Code hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/CalendarDayRow.tsx" id="CalendarDayRowSnippet":::

1. Fügen Sie die folgenden `import` Anweisungen oben in **Calendar.tsx hinzu.**

    ```typescript
    import CalendarDayRow from './CalendarDayRow';
    import './Calendar.css';
    ```

1. Ersetzen Sie die `render` vorhandene Funktion in durch die folgende `./src/Calendar.tsx` Funktion.

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    Dadurch werden die Ereignisse in ihre jeweiligen Tage aufgeteilt und ein Tabellenabschnitt für jeden Tag gerendert.

1. Speichern Sie die Änderungen, und starten Sie die App neu. Klicken Sie auf den **Link Kalender,** und die App sollte nun eine Tabelle mit Ereignissen rendern.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
