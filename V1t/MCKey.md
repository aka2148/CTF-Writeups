MCKey — Short Writeup

Target: https://tommytheduck.github.io/mckey/
Flag: V1T{MCK-pap-cool-ooh-yeah}

Summary

Flag was encoded by wrapping characters in <mark> tags across the page. Extracting and concatenating these marked segments yields the flag.

Steps

Fetch page source or inspect DOM.

Extract <mark> contents in DOM order.

Concatenate segments to produce the flag.
