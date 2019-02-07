## oauth2-cli

Command line utility to get an OAuth access token for three-legged flows where you authorise an application to access your account. Inspired by [oauth2-cli](https://github.com/dcarley/oauth2-cli) written in Go.

The reason for rewriting this in Ruby was the difficulty to debug the OAuth2 flow when things don't go as expected. Go's OAuth2 library is pretty opaque and trying to figure out why for some services the client_id is not passed when sending the the token request after obtaining the authorisation code has been fruitless - even though sending the request via curl using the produced authorisation code proved successful.

The purpose for this tool is to obtain access and refresh tokens for applications which run as services (e.g daemons) where doing the OAuth2 flow is a bit difficult. However, the scope is not limited to just this. For this use case, the `client_credentials` grant type would be more appropriate, but unfortunately not all API providers support this because they make the rather wrong assumption that all apps are used interactively. They only support `authorization_code` grant type or worse, `password` grant type.

## Install

```bash
gem install oauth2-cli
```

## Usage

For services which validate the callback URL, you must use `http://127.0.0.1:8000/oauth/callback` in your OAuth2 application. Bear in mind that `8000` is the default port which may be changed via CLI argument. `127.0.0.1` is the default host which may be changed via CLI argument. Adapt as necessary.

The `oauth2-cli` script has a built in help:

```bash
oauth2-cli --help
Usage: oauth2-cli --auth AUTHORISATION_URL --token TOKEN_URL --id CLIENT_ID --secret CLIENT_SECRET

    -a, --auth AUTHORISATION_URL     Authorisation URL (required)
    -t, --token TOKEN_URL            Token URL (required)
    -i, --id CLIENT_ID               Client ID (required)
    -s, --secret CLIENT_SECRET       Client secret (required)
    -o, --scope SCOPE1,SCOPE2,etc    OAuth2 scope to authorise (not used if not specified)
    -e, --separator SEPARATOR        OAuth2 scope separator character (defaults to space) n.b the scope arg is always passed as array and joined with the separator char for the request
    -h, --host 127.0.0.1             Callback host (defaults to 127.0.0.1) n.b this allows you to run this tool on a remote machine and have the authorisation code go there; the callback HTTP server always binds to all available network interfaces irrespective of this value
    -p, --port 8000                  Callback port (defaults to 8000)
    -w, --write                      Write the returned token as JSON using TOKEN_URL host as filename with the current working directory being the destination
    -u, --audience AUDIENCE          The token audience, not used if unspecified (optional)
    -d, --debug                      Turn on OAuth2 library debug and WEBrick log
```

The scope separator character has been implemented for services which interpret the OAuth2 spec a tad different - for example Facebook is using comma separated values for scope.

## Examples

```bash
# request tokens for accessing Netatmo Weather station
oauth2-cli -a https://api.netatmo.com/oauth2/authorize -t https://api.netatmo.com/oauth2/token -i CLIENT_ID -s CLIENT_SECRET -o read_station

Go to URL: https://api.netatmo.com/oauth2/authorize?access_type=offline&client_id=CLIENT_ID&redirect_uri=http%3A%2F%2F127.0.0.1%3A8000%2Foauth%2Fcallback&response_type=code&scope=read_station&state=ewcxkqpsfrinhgvyamzbouljtd

Starting server - use Ctrl+C to stop

{"scope"=>["read_station"],
 "expire_in"=>10800,
 :access_token=>"ACCESS_TOKEN",
 :refresh_token=>"REFRESH_TOKEN",
 :expires_at=>TIMESTAMP}

^C

# request tokens for accessing Awair air-quality monitor
oauth2-cli -a https://oauth-login.awair.is -t https://oauth2.awair.is/v2/token -i CLIENT_ID -s CLIENT_SECRET

Go to URL: https://oauth-login.awair.is?access_type=offline&client_id=CLIENT_ID&redirect_uri=http%3A%2F%2F127.0.0.1%3A8000%2Foauth%2Fcallback&response_type=code&state=jtiznuypwqfhbvmradlgkeoxcs

Starting server - use Ctrl+C to stop

{"token_type"=>"Bearer",
 :access_token=>"ACCESS_TOKEN",
 :refresh_token=>"REFRESH_TOKEN",
 :expires_at=>nil}

^C
```

The output hash from the token request may be used by Ruby's [OAuth2](https://github.com/oauth-xx/oauth2) library to recreate an `AccessToken` object using the `from_hash` class method. You have to implement your own token refresh capabilities when the access token expires for the OAuth2 library doesn't provide such functionality.
