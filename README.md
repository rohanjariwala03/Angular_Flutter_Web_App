# Angular + Flutter Support App

A take-home assignment with two parts: a Flutter app where a user chats with a support agent, and an Angular "internal tools" dashboard for support staff. The Flutter app embeds the Angular dashboard in a WebView on its second tab.

There is no backend. Agent replies, tickets and logs are all generated on the client.

## Flutter app (`flutter_app/`)

Two tabs in a bottom navigation bar.

Messages
- Chat screen with message bubbles and timestamps.
- After you send a message, a canned "agent" reply arrives 1 to 3 seconds later.
- Messages are saved to SharedPreferences, so the conversation survives a restart.
- The Messages tab shows a badge with the number of unread agent replies. Opening the tab marks them read.
- A delete button clears the conversation after a confirmation dialog.

Dashboard
- Loads the Angular dashboard from `http://localhost:4200` in a `webview_flutter` WebView. The code also lists `127.0.0.1:4200` and `10.0.2.2:4200` (the Android emulator's address for the host) as fallbacks.
- Shows a loading spinner, a refresh button, and an error screen with a Retry button and setup steps if the dashboard can't be reached.

State is handled with `provider` (a single `MessageService` ChangeNotifier).

## Angular dashboard (`webpage/`)

Angular 17 standalone components styled with Tailwind CSS, with a sidebar and three routes.

- `/tickets`: a table of 10 randomly generated support tickets (ID, subject, status, priority, assignee, created date) with All / Open / In Progress / Closed filters.
- `/knowledgebase`: an article editor with a title field and a Markdown textarea, an Edit/Preview toggle, and Save and Clear buttons. The preview uses a small regex-based Markdown converter (headings, bold, italic, links, inline code, lists). Save only shows a confirmation message; nothing is persisted.
- `/logs`: a terminal-style log panel that starts with 20 entries and adds a random one every few seconds. You can toggle Info / Warning / Error / Debug filters (each shows a count) and clear the log. It keeps the last 100 entries.

## Running it

Start the dashboard first, since the Flutter app's Dashboard tab expects it on port 4200.

```bash
cd webpage
npm install
npm start          # ng serve --host 0.0.0.0 --port 4200
```

Then in another terminal:

```bash
cd flutter_app
flutter pub get
flutter run
```

The dashboard also works on its own in a browser at `http://localhost:4200`. `npm run build` produces a build in `webpage/dist/internal-tools-dashboard`.

`webview_flutter` only supports Android, iOS and macOS, so the Dashboard tab won't render on web, Windows or Linux. The Messages tab works everywhere.

Built with Flutter 3.32 / Dart 3.8 (per `pubspec.lock`), Angular 17.3 and Tailwind CSS 3.

## Structure

```
flutter_app/lib/
  main.dart                    App entry, bottom navigation, unread badge
  models/message.dart          Message model with JSON (de)serialization
  services/message_service.dart  Send/load/save messages, simulated agent replies
  screens/messages_screen.dart Chat UI
  screens/dashboard_screen.dart WebView wrapper for the Angular dashboard

webpage/src/app/
  app.component.ts             Header and sidebar layout
  app.routes.ts                Routes for the three tools
  components/ticket-viewer/
  components/knowledgebase-editor/
  components/live-logs/
```

## License

MIT, see [LICENSE](LICENSE).
