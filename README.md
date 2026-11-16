# Interacousta

Two single-file HTML apps. Open them in a browser; nothing needs installing.

- **`interacousta.html`**: an acoustic web browser that carries its traffic over sound, plus mail over sound.
- **`free-mail.html`**: a standalone Nostr mail client (free.mail). It uses the internet directly, not sound.

Interacousta works with two devices. The **Router** has an internet connection. The **Remote** has none, and reaches the web through the Router by talking to it over speakers and microphones.

> **Status: experimental.** The sound protocol and the mail layer have been tested on a simulated sound link and simulated relays, including 15–25% frame loss. They have not yet been tested on real speakers and microphones, and the mail encryption has not been audited. Don't rely on either for sensitive messages yet.

## Quick start

1. **Serve the files over HTTPS or localhost.** Browsers only allow microphone access and the crypto APIs in a secure context. GitHub Pages works: in the repository's Settings, open Pages and deploy from the `main` branch.
2. **Open `interacousta.html` on two devices.** Place each device's speaker close to the other's microphone.
3. **On the Router**, open Settings, set **Role** to *Router*, choose a **Modulation**, and press **Enable microphone**.
4. **On the Remote**, set **Role** to *Remote*, choose the same **Modulation**, and press **Enable microphone**.
5. **On the Remote**, type an address in the bar and press **Go**. The page comes back over sound.

Modulation options: **Bell 202** runs at 1200 baud. **4-FSK · 10** and **4-FSK · 20** are slower, at 10 and 20 symbols per second. Both devices must use the same setting. These are rough estimates, not measurements: about 30 bytes per second on Bell 202, and about 2 bytes per second on 4-FSK at 20 symbols per second.

## Mail over sound

The ✉ button beside **Go** opens the mail panel. On a Router, the panel talks directly to Nostr relays and Blossom servers. On a Remote, every mail action goes over sound through the Router:

- **Create identity.** The Router makes the key and sends it back encrypted over sound.
- **Log in.** Enter your nsec on the Remote. Only your public key travels over sound.
- **Send and fetch.** The Router publishes events and retrieves them. The Remote seals, signs, and decrypts.

Limits over sound are 1,200 characters per message and 2 KiB per attachment. The inbox fetches 3 messages by default. Relays and Blossom servers are configured on the Router, in the Account tab.

## Security and privacy

Read this before using it.

- **The Router sees the identity it creates for a Remote.** The Router generates that key, so it can read mail sent to that identity. Use Remote identities only with a Router you control.
- **The identity exchange is not authenticated.** Someone listening to the sound can't read the key. But someone able to transmit on the same link could impersonate the Router.
- **Sound is public.** Anyone within earshot can hear the traffic, and can use a Router as a relay while it runs. Run a Router only where that's acceptable.
- **The mail encryption is custom.** Bodies and attachments are sealed with ECDH, HKDF, and AES-GCM, following free.mail's scheme. It is not NIP-04 or NIP-44. Have it reviewed before trusting it with anything sensitive.
- **Keys stay in memory.** Nothing writes your nsec to storage. Closing the page loses an identity unless you saved its nsec.
- **Third-party services.** Pages a Router fetches go through `r.jina.ai`. Images for the SSTV image feature are fetched through `images.weserv.nl`, falling back to the direct URL. Mail goes to whichever relays and Blossom servers the Router is configured with. The defaults are `wss://relay.damus.io`, `wss://nos.lol`, and `https://blossom.band`. Relays and servers see metadata, such as who you message and when. The operators of those services see what you request.
- **Remote text is escaped** before it is displayed, so a fetched page can't inject markup into the app.

## free-mail.html

A standalone Nostr mail client. It connects to relays and Blossom servers over the internet, and generates and holds its keys in the page. Its encryption is the same scheme described above, with the same caveats.

## License

MIT. See [LICENSE](LICENSE). The apps bundle `@noble/curves`, also MIT. Its license notices are included in both HTML files.
