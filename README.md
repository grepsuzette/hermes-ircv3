# hermes-ircv3

[Hermes Agent](https://github.com/NousResearch/hermes-agent) now provides a IRC platform, but it's fairly limited.

This is an alternative IRCv3 adapter for Hermes Agent.:

* draft/multiline (BATCH) 
    - preserves formatting
    - enables multiline markdown
* CAP negotiation
* Custom CA certs
* Supports NickServ auth
* Nick collision recovery
* Multi-channel

## Warning

IRC security is close to non-existant.

Even if you use it with TLS, admins can still see everything 
you send, and potentially impersonate you or other nicknames.

Only use IRC if you know what you're doing.

If you do: 

* Consider using a modern IRC server like [Ergo](https://ergo.chat/)
* Have the code for this plugin double-checked
* Enable TLS for all client connections
* Restrict server access via:
  - Ideally port forwarding (so it's all private)
  - Or IRC server password and firewalls
  - Client certificate authentication

Despite these strong concerns with IRC, we still provided this adapter as a PR because:

- Other platforms are often proprietary (feishu, telegram, signal)
- Or heavy/complex (e.g. matrix)
- The simplicity of IRC can sometimes be seen as an advantage in certain situations, e.g. when you can secure it with tunnels
- The irc adapter currently shipped with hermes wasn't shipped back then

## Installation

Clone this repo to `<hermes-dir>/plugins/platforms/irc`

Add to config.yaml:
```yaml
plugins:
  enabled:
    - platforms/irc
```

This will override the default irc plugin shipped with hermes.

## Configuration

IRC bots get configured by environment variables.

The location is in `~/.hermes/profiles/<name>/.env` (or in `~/.hermes/.env` for the main profile).

```bash
IRC_SERVER=irc.example.com
IRC_PORT=6697            # or 6667 for clear(!) traffic
IRC_USE_TLS=true         # true for port 6697
IRC_PASSWORD=            # optional, server password

IRC_NICK=MyBotNick
IRC_NICKSERV_PASSWORD=   # optional, NickServ identify on connect
IRC_NICKSERV_SERVICE=NickServ
IRC_USERNAME=mybot             # optional, defaults to IRC_NICK
IRC_REALNAME="My AI Bot"        # optional, defaults to "Hermes Agent"
IRC_CHANNELS=#channel1,#channel2  # comma-separated channels to join
IRC_ALLOWED_USERS=alice,bob    # case insensitive nicks, * to allow all

IRC_MESSAGE_CHUNK_LIMIT=16384  # per-line limit when BATCH unavailable (default 350)
IRC_REQUIRE_MULTILINE=true     # fail if server lacks draft/multiline

IRC_TLS_CA_CERT=/path/to/cert.pem  # custom CA certificate for TLS

IRC_HOME_CHANNEL=#home    # default channel for cron delivery
```

## Channel mentions

In IRC channels, the bot only responds to messages that mention it in the list of nicknames at the very beginning,
provided the sender nick is in `IRC_ALLOWED_USERS`.

- Example: `Foo: hello` -> only agent 'Foo' would answer
- Example: `Foo: Bar: Baz: hello` -> the three agents 'Foo', 'Bar', 'Baz' would all answer
- **DMs are not affected** — they work normally

## Why this exists (AI rant)

Hermes IRC does not support IRCv3 multiline, can't load
a custom CA cert, strips markdown aggressively (because IRC users hate
formatting?), and handles nick collision by appending _1, _2, _3 like a
WiFi reconnect. But thankfully they now allow plugins, so here is one
more implementation to choose from.

## TODO / MAYBE

- Cron delivery through the live adapter (send via connected bot, no ephemeral connection)

## License

GPL-3.0
