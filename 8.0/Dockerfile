ARG PHP_VERSION=8.0

FROM php:${PHP_VERSION}-fpm-buster

RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		acl \
		apt-transport-https \
		git \
		unzip \
		sudo \
		zip \
	; \
	rm -rf /var/lib/apt/lists/*

ARG APCU_VERSION=5.1.19
RUN set -eux; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		$PHPIZE_DEPS \
		libicu-dev \
		libmariadb-dev \
		libxslt-dev \
		libzip-dev \
		postgresql-server-dev-all \
	; \
	docker-php-ext-configure zip; \
	docker-php-ext-install -j$(nproc) \
		intl \
		pdo_mysql \
		pdo_pgsql \
		xsl \
		zip \
	; \
	pecl install \
		apcu-${APCU_VERSION} \
	; \
	pecl clear-cache; \
	docker-php-ext-enable \
		apcu \
		opcache \
	; \
	rm -rf /var/lib/apt/lists/*

RUN set -eux; \
	curl \
		--silent \
		--fail \
		--location \
		--retry 3 \
		--output rymfony.zip \
		--url "https://nightly.link/Orbitale/Rymfony/workflows/CI.yaml/main/rymfony.ubuntu.zip" \
	; \
	unzip rymfony.zip \
	&& mv rymfony /usr/bin/rymfony \
	&& chmod a+x /usr/bin/rymfony

RUN set -eux; \
	php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
	&& php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
	&& php composer-setup.php \
	&& php -r "unlink('composer-setup.php');" \
	&& mv composer.phar /usr/bin/composer

WORKDIR /usr/src/app

EXPOSE 8000

CMD ["rymfony"]
