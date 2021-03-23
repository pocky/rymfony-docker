# Rymfony docker

Use rymfony in a container

## Why?

I don't have any php binaries on my computer because my projects are always dockerized
with different PHP versions, on different computers etc.

This Dockerfile allow me to test my Symfony projects against PHP.

This dockerfile use Debian (because I don't like Alpine and always use Debian in production)
and [Rymfony](https://github.com/Orbitale/Rymfony) instead of standard [Symfony server](https://symfony.com/doc/current/setup/symfony_server.html).
Rymfony has fewer functionalities than Symfony one but Rymfony is truly open source (for the win).

## Installation

- Clone this project
- Go to `8.0` (or 7.4) and run `docker build -t rymfony:<version> .`
- Profit!

## Usage

For the moment, there are few problems with Rymfony through Docker:

- [sudo is required](https://github.com/Orbitale/Rymfony/issues/79)
- [User and group configuration in fpm-conf.ini](https://github.com/Orbitale/Rymfony/issues/79) (related to root user)
- can't share uid/gid (thread 'main' panicked with PermissionDenied on Result::unwrap in src/main.rs:65:55)
- rymfony is killed with ctrl+c but can't restart (Error: server is already running)

I also need to check some points:

- ca-certificate
- ctrl+c stop server but *.pid files are not removed (because of volume?)

```bash
docker run -it --rm \
	--network="host" \
	-v $(pwd):/usr/src/app \
	-v $(pwd)/.rymfony:/root/.rymfony \
	-w /usr/src/app rymfony:<version> \
	rymfony <command>
```

`rymfony server:start` will fail during the first run because of `fpm-conf.ini`.
After this first (failed) run, go to `.rymfony` folder and edit `fpm-conf.ini`:

Replace lines 17/18 (remove comment and change user/group)

```bash
user = www-data
group = www-data
```

And profit!

## Contributing

See the [CONTRIBUTING](docs/CONTRIBUTING.md) file.

## Code of conduct

Be nice and take a look on our [CODE OF CONDUCT](docs/CODE_OF_CONDUCT.md).

## Support

This project is open source and this is our [support rules](docs/SUPPORT.md).

## License

This project is licensed under AGPLv3.

## Credits

Created by [Alexandre Balmes](https://alexandre.balmes.co).
See also the [thank you](/docs/thank-you.md).
