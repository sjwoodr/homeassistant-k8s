
# homeassistant

Note: to work with istio and cert-manager/lets-encrypt you need to change a setting in configuration.yaml
https://techoverflow.net/2021/11/25/how-to-fix-home-assistant-a-request-from-a-reverse-proxy-was-received-from-127-0-0-1-but-your-http-integration-is-not-set-up-for-reverse-proxies/
```
http:
  use_x_forwarded_for: true
  trusted_proxies:
  - 127.0.0.1
  ip_ban_enabled: true
  login_attempts_threshold: 5
```

# setup
This repo is meant to be used along with https://github.com/sjwoodr/argocd-config
