# apache-samesite
This repo contains snippets useful for the SameSite cookie migration happening in early 2020.

## Config

Read the two *.conf files in this repo. It assumes you just want to temporarily slap SameSite=none
on every Set-Cookie without a SameSite parameter, but you can obviously match certain cookie names.

## Testing

You can simulate some headers easily by setting globally:

    Header always add Set-Cookie FOO1=BAR
    Header always add Set-Cookie "FOO2=BAZ; Domain=example.com"
    Header always add Set-Cookie "FOO3=BAZ; Domain=example.com ; SameSite=Strict"
    Header always add Set-Cookie "FOO4=BAZ; Domain=example.com ; SAMESITE=Strict"

Then send some test requests varying the user-agent:

    wget -qS http://localhost -O-
    wget -qS -U "Mozilla/5.0 (Windows NT 6.0; WOW64) AppleWebKit/534.24 (KHTML, like Gecko) Chrome/11.0.696.34 Safari/534.24." http://localhost
    # No change to FOO1 and FOO2 since this is an interolerant one!
    wget -qS -U "Mozilla/5.0 (Windows NT 6.0; WOW64) AppleWebKit/534.24 (KHTML, like Gecko) Chrome/60.0.696.34 Safari/534.24." http://localhost
    wget -qS -U "Mozilla/5.0 (Windows NT 6.0; WOW64) AppleWebKit/534.24 (KHTML, like Gecko) Chrome/69.0.696.34 Safari/534.24." http://localhost


## Why mod_rewrite?
It would be nice to remove the per-vhost stuff by using the modern features, but 
my users need this for Apache 2.2 which means no mod_lua, `<if>` or `Header ... expr=...`


## Why not whitelist browsers?

Chrome UA strings are complicated when considering e.g. mobile: https://developer.chrome.com/multidevice/user-agent
