# Troubleshooting Burp Suite’s Browser on Ubuntu

## Problem

Burp Suite’s built-in browser failed to start because Ubuntu restricted
the unprivileged user namespaces its Chromium sandbox needs.

## Fix

I created an AppArmor profile at `/etc/apparmor.d/burpbrowser` for Burp’s
bundled Chromium executable. The profile granted that executable access
to user namespaces so the browser could run with its sandbox enabled.

## Verification

I opened Burp’s built-in browser, visited PortSwigger with interception
off, and confirmed that the request appeared in Proxy → HTTP history.
