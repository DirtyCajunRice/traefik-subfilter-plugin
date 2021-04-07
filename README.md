# Subfilter

Subfilter is a middleware plugin for [Traefik][traefik] which rewrites the HTTP response body
by replacing a search regex by a replacement string. Subfilter was directly cloned from
[plugin-rewritebody][rewritebody] and modified (keeping all git history) to address issues not resolved in the 
upstream repository

## Configuration

### Static

```toml
[pilot]
  token = "xxxx"

[experimental.plugins.subfilter]
  modulename = "github.com/DirtyCajunRice/traefik-subfilter-plugin"
  version = "v0.0.1"
```

### Dynamic

To configure the `Subfilter` plugin, create a middleware in your configuration as explained [here][middleware-docs].
The following examples create and use `subfilter` to replace all foo occurrences by bar in the HTTP response body.

If you want to apply some limits on the response body, you can chain this middleware plugin with
the [Buffering middleware][buffering-middleware] from Traefik.

```toml
[http.routers]
  [http.routers.my-router]
    rule = "Host(`localhost`)"
    middlewares = ["subfilter-foo"]
    service = "my-service"

[http.middlewares]
  [http.middlewares.subfilter-foo.plugin.subfilter]
    # Keep Last-Modified header returned by the HTTP service.
    # By default, the Last-Modified header is removed.
    lastModified = true

    # Rewrites all "foo" occurences by "bar"
    [[http.middlewares.subfilter-foo.plugin.subfilter.rewrites]]
      regex = "foo"
      replacement = "bar"

[http.services]
  [http.services.my-service]
    [http.services.my-service.loadBalancer]
      [[http.services.my-service.loadBalancer.servers]]
        url = "http://127.0.0.1"
```

[traefik]: https://github.com/traefik/traefik
[middleware-docs]: https://docs.traefik.io/middlewares/overview/
[buffering-middleware]: https://docs.traefik.io/middlewares/buffering/
[rewritebody]: https://github.com/traefik/plugin-rewritebody
