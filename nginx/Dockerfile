FROM nginx:1.24.0

RUN rm /etc/localtime
RUN ln -s /usr/share/zoneinfo/Asia/Taipei /etc/localtime
RUN "date"

WORKDIR /var/www/

