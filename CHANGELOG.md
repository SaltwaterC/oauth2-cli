0.2.1
-----

 * Fix separator options parser - OptionParser defaults to boolean if no string precedes the argument.
 * Clarify that the TOKEN_URL host is used as filename for write, not the URL as a whole.
 * Add `audience` optional argument authorisation request.
 * Cleaner error output when parsing arguments.

0.2.0
-----

 * Added `host` argument to customise the callback hostname to enable running this on a remote machine.
 * Added `write` flag to write the token contents into a JSON file in the current working directory where this tool is invoked.

0.1.0
-----

 * Initial release.
