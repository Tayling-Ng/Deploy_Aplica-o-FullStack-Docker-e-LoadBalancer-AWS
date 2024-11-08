FROM nginx:1.23.2
RUN rm /etc/nginx/conf.d/default.conf
COPY ./frontend2/nginx.conf /etc/nginx/conf.d
COPY ./frontend2/. /usr/share/nginx/html
