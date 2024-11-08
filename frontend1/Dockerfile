FROM nginx:1.23.2
RUN rm /etc/nginx/conf.d/default.conf
COPY ./frontend1/nginx.conf /etc/nginx/conf.d
COPY ./frontend1/. /usr/share/nginx/html
