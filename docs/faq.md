# Orion - FAQ
## Security and Browser polycies
For protecting your server against script injection
and your clients against XSS attacks, we recommend
the following packages modifications:
```bash
meteor remove insecure autopublish
meteor add audit-arguments-check browser-policy matteodem:easy-security
```
* `audit-arguments-check` checks the correctness of your development ensuring you that you've carefully checked user's inputs.
* `browser-policy` constraints modern users in not using anything else except what is precisely specified.
* `matteodem:easy-security` is a rate limiting user's entry to avoid attacks 'à la` DDOS.

The following browser policy rules have to be set server side:
**server/browserPolicy.coffee**
```coffee
# Article sources:
# * https://dweldon.silvrback.com/browser-policy
# * http://paris.meteor.com/presentations/uByDe8qDLrNGJLzMC
# Black list everything
BrowserPolicy.framing.disallow()
BrowserPolicy.content.disallowEval()
# BrowserPolicy.content.disallowInlineScripts()
BrowserPolicy.content.disallowConnect()
# Only allow necessary protocols
rootUrl = __meteor_runtime_config__.ROOT_URL
BrowserPolicy.content.allowConnectOrigin rootUrl
BrowserPolicy.content.allowConnectOrigin (rootUrl.replace 'http', 'ws')
# Allow origin for Meteor hosting
for protocol in ['http', 'https', 'ws', 'wss']
  BrowserPolicy.content.allowConnectOrigin "#{protocol}://*.meteor.com"
# Allow external CSS
for origin in ['fonts.googleapis']
  for protocol in ['http', 'https']
    BrowserPolicy.content.allowStyleOrigin "#{protocol}://#{origin}"
# Allow external fonts
for origin in ['fonts.gstatic.com']
  for protocol in ['http', 'https']
    BrowserPolicy.content.allowFontOrigin "#{protocol}://#{origin}"
# Allow Fonts and CSS
for protocol in ['http', 'https']
  BrowserPolicy.content.allowStyleOrigin "#{protocol}://fonts.googleapis.com"
  BrowserPolicy.content.allowFontOrigin "#{protocol}://fonts.gstatic.com"
# Trusted sites
for origin in ['*.google-analytics.com', 'browser-update.org']
  for protocol in ['http', 'https']
    origin = "#{protocol}://#{origin}"
    BrowserPolicy.content.allowOriginForAll origin
```
