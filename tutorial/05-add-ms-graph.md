<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft-Graph-Client-](https://github.com/microsoftgraph/msgraph-sdk-javascript) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

1. Öffnen `./src/GraphService.ts` Sie und fügen Sie die folgende Funktion hinzu.

    :::code language="typescript" source="../demo/graph-tutorial/src/GraphService.ts" id="getEventsSnippet":::

    Überlegen Sie sich, was dieser Code macht.

    - Die URL, die aufgerufen wird, lautet `/me/events`.
    - Die `select` -Methode schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf genau diejenigen ein, die die Ansicht tatsächlich verwendet wird.
    - Die `orderby` Methode sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.

1. Erstellen Sie eine reaktionskomponente, um die Ergebnisse des Anrufs anzuzeigen. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Calendar.tsx` dem Namen, und fügen Sie den folgenden Code hinzu.

    ```typescript
    import React from 'react';
    import { Table } from 'reactstrap';
    import moment from 'moment';
    import { Event } from 'microsoft-graph';
    import { config } from './Config';
    import { getEvents } from './GraphService';
    import withAuthProvider, { AuthComponentProps } from './AuthProvider';

    interface CalendarState {
      events: Event[];
    }

    // Helper function to format Graph date/time
    function formatDateTime(dateTime: string | undefined) {
      if (dateTime !== undefined) {
        return moment.utc(dateTime).local().format('M/D/YY h:mm A');
      }
    }

    class Calendar extends React.Component<AuthComponentProps, CalendarState> {
      constructor(props: any) {
        super(props);

        this.state = {
          events: []
        };
      }

      async componentDidMount() {
        try {
          // Get the user's access token
          var accessToken = await this.props.getAccessToken(config.scopes);
          // Get the user's events
          var events = await getEvents(accessToken);
          // Update the array of events in state
          this.setState({events: events.value});
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

1. Fügen Sie diese neue Komponente zur APP hinzu. Öffnen `./src/App.tsx` Sie und fügen Sie `import` die folgende Anweisung am Anfang der Datei hinzu.

    ```typescript
    import Calendar from './Calendar';
    ```

1. Fügen Sie die folgende Komponente unmittelbar nach dem `<Route>`vorhandenen hinzu.

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

1. Ersetzen Sie die `render` vorhandene Funktion `./src/Calendar.js` in durch die folgende Funktion.

    :::code language="typescript" source="../demo/graph-tutorial/src/Calendar.tsx" id="renderSnippet":::

    Dadurch wird die Auflistung von Ereignissen durchlaufen, und für jede einzelne Tabelle wird eine Tabellenzeile hinzugefügt.

1. Speichern Sie die Änderungen, und starten Sie die APP neu. Klicken Sie auf den Link **Kalender** , und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/add-msgraph-01.png)
