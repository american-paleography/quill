Needs MySQL + data. Some of the data is VERY LARGE (5MB+ line size), so increase `max_allowed_packet` if needed ("MySQL error 2006: mysql server has gone away" indicates that it's needed).

If you want to use subdomains instead of different root paths, you'll need to modify the cookie path as well. An nginx reverse proxy with `proxy_cookie_path` set works well for this.
