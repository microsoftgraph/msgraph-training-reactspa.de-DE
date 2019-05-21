<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft-Graph-Client-](https://github.com/microsoftgraph/msgraph-sdk-javascript) Bibliothek, um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen aus Outlook

Beginnen Sie mit dem Hinzufügen einer neuen `./src/GraphService.js` Methode zur Datei, um die Ereignisse aus dem Kalender abzurufen. Fügen Sie die folgende Funktion hinzu.

```js
export async function getEvents(accessToken) {
  const client = getAuthenticatedClient(accessToken);

  const events = await client
    .api('/me/events')
    .select('subject,organizer,start,end')
    .orderby('createdDateTime DESC')
    .get();

  return events;
}
```

Überprüfen Sie, was dieser Code tut.

- Die URL, die aufgerufen wird `/me/events`.
- Die `select` -Methode schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf genau diejenigen ein, die die Ansicht tatsächlich verwendet wird.
- Die `orderby` Methode sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.

Erstellen Sie nun eine reaktionskomponente, um die Ergebnisse des Anrufs anzuzeigen. Erstellen Sie eine neue Datei im `./src` Verzeichnis mit `Calendar.js` dem Namen, und fügen Sie den folgenden Code hinzu.

```JSX
import React from 'react';
import { Table } from 'reactstrap';
import moment from 'moment';
import config from './Config';
import { getEvents } from './GraphService';

// Helper function to format Graph date/time
function formatDateTime(dateTime) {
  return moment.utc(dateTime).local().format('M/D/YY h:mm A');
}

export default class Calendar extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      events: []
    };
  }

  async componentDidMount() {
    try {
      // Get the user's access token
      var accessToken = await window.msal.acquireTokenSilent(config.scopes);
      // Get the user's events
      var events = await getEvents(accessToken);
      // Update the array of events in state
      this.setState({events: events.value});
    }
    catch(err) {
      this.props.showError('ERROR', JSON.stringify(err));
    }
  }

  render() {
    return (
      <pre><code>{JSON.stringify(this.state.events, null, 2)}</code></pre>
    );
  }
}
```

Im Moment wird dadurch nur das Array von Ereignissen in JSON auf der Seite gerendert. Fügen Sie diese neue Komponente zur APP hinzu. Öffnen `./src/App.js` Sie und fügen Sie `import` die folgende Anweisung am Anfang der Datei hinzu.

```js
import Calendar from './Calendar';
```

Fügen Sie dann die folgende Komponente unmittelbar nach dem `<Route>`vorhandenen hinzu.

```JSX
<Route exact path="/calendar"
  render={(props) =>
    <Calendar {...props}
      showError={this.setErrorMessage.bind(this)} />
  } />
```

Speichern Sie die Änderungen, und starten Sie die App neu. Melden Sie sich an, und klicken Sie in der Navigationsleiste auf den Link **Kalender** . Wenn alles funktioniert, sollte ein JSON-Abbild der Ereignisse im Kalender des Benutzers angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Jetzt können Sie die `Calendar` Komponente aktualisieren, um die Ereignisse auf benutzerfreundlichere Weise anzuzeigen. Ersetzen Sie die `render` vorhandene Funktion `./src/Calendar.js` in durch die folgende Funktion.

```JSX
render() {
  return (
    <div>
      <h1>Calendar</h1>
      <Table>
        <thead>
          <tr>
            <th scope="col">Organizer</th>
            <th scope="col">Subject</th>
            <th scope="col">Start</th>
            <th scope="col">End</th>
          </tr>
        </thead>
        <tbody>
          {this.state.events.map(
            function(event){
              return(
                <tr key={event.id}>
                  <td>{event.organizer.emailAddress.name}</td>
                  <td>{event.subject}</td>
                  <td>{formatDateTime(event.start.dateTime)}</td>
                  <td>{formatDateTime(event.end.dateTime)}</td>
                </tr>
              );
            })}
        </tbody>
      </Table>
    </div>
  );
}
```

Dadurch wird die Auflistung von Ereignissen durchlaufen, und für jede einzelne Tabelle wird eine Tabellenzeile hinzugefügt. Speichern Sie die Änderungen, und starten Sie die APP neu. Klicken Sie auf den Link **Kalender** , und die APP sollte jetzt eine Tabelle mit Ereignissen rendern.

![Ein Screenshot der Ereignistabelle](./images/add-msgraph-01.png)