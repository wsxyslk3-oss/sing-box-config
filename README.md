This is a sing-box config for a DNS-aware proxy setup with split routing and rule-based filtering. In plain terms:

    It sends DNS requests through DoH servers:
        Cloudflare
        Google
        AliDNS
        Tencent/doh.pub
    It blocks or rejects:
        hijacking
        ads/privacy-related domains
        rule sets like block, Ads_AWAvenue, Advertising, Privacy
    It routes:
        China/ChinaMax domains and local-direct rules to direct
        Google/OpenAI/Telegram/Global/Proxy_List to Proxy
        everything else to Proxy by final fallback

What each major section does:

    dns
        defines upstream DNS servers and host overrides
        sets rules for when to reject, route to direct, or route to proxy
        final fallback is dns-proxy
        optimistic and ipv4_only are enabled

    route
        loads local rule sets from ./rules/local/{tag}.json
        downloads remote rule sets from GitHub
        applies routing logic:
            sniff traffic
            hijack DNS on port 53 / protocol dns
            reject malicious/hijacking rules
            route direct/proxy based on rule sets
            resolve-only traffic gets special handling
        final fallback is Proxy

    experimental
        enables cache database
        exposes Clash-style API on 127.0.0.1:9999 with secret singbox

    services
        exposes sing-box API on 127.0.0.1:9090
        enables dashboard at the specified local path

Important caveat:

    This snippet references outbound names like "Proxy" and "direct" in many places, but the actual outbounds section is not shown.
    If that section is missing elsewhere, sing-box will not work because those detours/outbounds must exist.

Also:

    The config is using remote rule sets from GitHub and includes update_interval: "24h", so it refreshes automatically.
    The duplicate route rules near the end are not necessarily wrong, but they are redundant and can be simplified.
