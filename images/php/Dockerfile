# Для начала указываем исходный образ, он будет использован как основа
FROM php:7.1-apache
# Необязательная строка с указанием автора образа
MAINTAINER eshaft <eshaft@gmail.com>

# RUN выполняет идущую за ней команду в контексте нашего образа.
# В данном случае мы установим некоторые зависимости и модули PHP.
# Для установки модулей используем команду docker-php-ext-install.
# На каждый RUN создается новый слой в образе, поэтому рекомендуется объединять команды.
RUN apt-get update && apt-get install -y \
        curl \
        wget \
        git \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libpng12-dev \     
        libmemcached-dev \
        zlib1g-dev \    
    && docker-php-ext-install -j$(nproc) iconv mcrypt mbstring mysqli pdo_mysql zip \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd
    
RUN git clone https://github.com/php-memcached-dev/php-memcached.git /usr/src/php/ext/memcached \
    && cd /usr/src/php/ext/memcached \
    && git checkout php7 \
    && docker-php-ext-install memcached

RUN pecl install xdebug-2.5.3 \
    && docker-php-ext-enable xdebug

# Куда же без composer'а.
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
	&& composer global require "fxp/composer-asset-plugin:^1.2.0" \
	&& composer global require "codeception/codeception:*" \
	&& composer global require "codeception/specify:*" \
	&& composer global require "codeception/verify:*" \
	&& ln -s ~/.composer/vendor/codeception/codeception/codecept /usr/local/bin/codecept \
	&& a2enmod rewrite \
    && a2enmod ssl \
	&& /etc/init.d/apache2 restart

# Добавим свой php.ini, можем в нем определять свои значения конфига
ADD php.ini /usr/local/etc/php/conf.d/40-custom.ini

#SSL
ADD ./ssl /usr/home/ssl

# Запускаем контейнер
# Из документации: The main purpose of a CMD is to provide defaults for an executing container. These defaults can include an executable, 
# or they can omit the executable, in which case you must specify an ENTRYPOINT instruction as well.
CMD ["apache2-foreground"]