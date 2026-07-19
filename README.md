# ⚽ Fan Park — a serverless live crowd page for the World Cup Final

One HTML file. No backend. No signup. Open the link, pick your flag, and you're
cheering with everyone else in real time.

Built live from a phone in Central Park during the **2026 World Cup Final
(🇦🇷 Argentina vs Spain 🇪🇸, July 19, 2026)** so the crowd on the lawn — one of
the most international crowds on earth — could see itself.

## What it does

- **Cheer battle** — two giant tap buttons, 🇦🇷 vs 🇪🇸. Every tap from every
  phone moves a shared tug-of-war bar and fires an emoji burst on everyone's
  screen.
- **Flag wall** — pick where you're from and your flag joins a live wall:
  *"214 fans · 31 countries"*.
- **Chant buttons** — GOOOOL! ⚽, VAMOS, ¡OLÉ!… presets that float up across
  every connected phone. (Deliberately no free-text chat: anonymous + zero
  moderation = no thanks.)
- **QR share** — one tap shows a QR code so the person next to you joins in
  five seconds.

## The trick: realtime with no server

The page connects straight to a **public MQTT broker over WebSockets**
(EMQX, with HiveMQ and Mosquitto as fallbacks). That gives us pub/sub fan-out
for free, from a static page on GitHub Pages:

- **Cheers & chants** are plain ephemeral publishes — everyone subscribed sees
  them instantly.
- **Presence & totals** use MQTT **retained messages**: each client publishes
  its own flag to `…/pins/<id>` and its own tap count to `…/taps/<id>` with the
  retained flag set. The broker keeps the last message per topic, so a newcomer
  subscribing to `…/pins/#` immediately receives everyone who came before.
  Summing per-client retained counters gives global totals — no database, no
  server, no accounts.

Trade-offs, honestly: a public broker is best-effort, unauthenticated, and
shared with the world — anyone can publish to the topics. Perfect for an
ephemeral one-day party page; wrong for anything that matters. Client-side we
clamp values, validate payloads, and rate-limit sends.

## Run it

It's a static file. Open `index.html`, or serve it:

```
python3 -m http.server 8000
```

Change `NS` in the config block at the top of the script to get your own
isolated room — that's also all it takes to reuse this for a different match,
a conference, a wedding…

## License

MIT. Fork it, gut it, point it at your own event.
